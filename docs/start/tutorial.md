# Tutorial — twenty minutes, one repository, four real findings

Every command and every output on this page was executed to produce it. If you follow along you
will get the same results, because nothing here is illustrative.

You will need Python 3.9+ and about twenty minutes.

```bash
pip install "evidence[all]"
```

---

## 1. A repository with a defect in it

Make a small service with a genuine lock-order inversion — two locks taken in opposite orders in
two functions. This deadlocks under concurrency and passes every unit test.

```bash
mkdir -p tut/svc && cd tut && git init -q
```

```python title="svc/pool.py"
import threading

conn_lock = threading.Lock()
stats_lock = threading.Lock()


def checkout():
    with conn_lock:
        with stats_lock:
            pass


def report():
    with stats_lock:
        with conn_lock:
            pass
```

## 2. Ask what is true

```console
$ evidence audit .
  gridlock       WEDGES: conn_lock -> stats_lock -> conn_lock   [FAIL]
  honestbench    no evidence manifest found (honestbench n...   [n/a]
  proof-drift    no Lean sources, so there is no proof to ...   [n/a]
  sf-verify      no hash-chained decision log (.jsonl with...   [n/a]
  signoff-cert   no signoff-cert/v1 certificates found          [n/a]
  --------------------------------------------------------------------------
  AGGREGATE: FAILED — 1 fail, 4 n/a
  gridlock is FAILED, and the aggregate is the weakest leg — 0 other constituent(s) passing does not lift it
$ echo $?
1
```

!!! note "Read the four `n/a` lines carefully"
    They are **not** four passes. Four tools looked for something to check and did not find it.
    If they had been counted as passes, this repository would score "4 of 5 green" — and the one
    real defect would be a rounding error. This is the entire design.

## 3. Look at the finding on its own

```console
$ gridlock import python ./svc | gridlock check -
WEDGES — a cycle exists
  nodes ............... 2
  wait-for edges ...... 2
  cycle ............... conn_lock -> stats_lock -> conn_lock
  every holder in that cycle waits on the next; none can ever proceed
```

The cycle is exhibited, not asserted. A verdict you cannot check is not a verdict, so every
negative result in this portfolio carries the witness that makes it checkable.

!!! warning "What `gridlock`'s Python importer cannot see"
    It does not track locks across function boundaries. If `f()` holds A and calls `g()`, which
    takes B, that A→B edge is invisible. **A cycle it finds is real; a `SAFE` verdict from this
    importer is weak evidence.** Every run says so in its own output.

## 4. Now make the evidence itself checkable

Record what the repository contains, then verify it:

```console
$ honestbench manifest . --out MANIFEST.sha256
$ evidence audit .
  gridlock       WEDGES: conn_lock -> stats_lock -> conn_lock   [FAIL]
  honestbench    honestbench verify                             [PASS]
  AGGREGATE: FAILED — 1 fail, 3 n/a, 1 pass
  gridlock is FAILED, and the aggregate is the weakest leg — 1 other constituent(s) passing does not lift it
```

Note the last line. One constituent now passes and **the aggregate did not move**. That is the
weakest-leg rule doing its job: a pass cannot dilute a failure.

## 5. The honestbench moment

Delete the file the manifest lists — the thing your CI supposedly checks:

```console
$ rm svc/pool.py
$ evidence audit .
  honestbench    honestbench verify                             [FAIL]
  gridlock       no Python sources for the lock-order impo...   [n/a]
  AGGREGATE: FAILED — 1 fail, 4 n/a
$ honestbench verify MANIFEST.sha256 .
honestbench verify
  committed root ... 239b8d51de38efbdfd9589f236ca0a7bc8388e7a48e16057ba7af5d05e5f20e0
  recomputed root .. None
  missing .......... 1
  changed .......... 0
     MISSING  svc/pool.py
```

Two things happened, and the second matters more.

`honestbench` went red — correctly. But look at `gridlock`: it flipped from **FAILED** to
**n/a**. Deleting the source made the deadlock finding *disappear*. In a system without an
absence audit, removing the evidence is indistinguishable from fixing the bug, **and it looks
better**.

That is what "our CI was green; we deleted the evidence it checks; it was still green" means, and
it is why `honestbench` exists.

## 6. Before you run an experiment, check it can fail

Different tool, same discipline. Suppose you plan to test whether batching perturbs greedy
decoding:

```json title="plan.json"
{
  "name": "batch-invariance",
  "hypothesis": "batch composition changes greedy decoding output",
  "decision_rule": "argmax_flips > 0",
  "metrics": { "argmax_flips": { "type": "integer", "lo": 0, "hi": 0 } }
}
```

```console
$ pip install preregister
$ preregister check plan.json
UNFALSIFIABLE — batch-invariance
  rule            argmax_flips > 0
  analysis        exhaustive
  finding fires   NEVER
  null fires      yes
    witness       argmax_flips=0
  CONSTANT        argmax_flips (declared support has exactly one value)

THE FINDING CAN NEVER BE REPORTED. ... The run is guaranteed to return the null before any data
is collected.
$ echo $?
1
```

That configuration (`prompt_logprobs=0`) pins the metric to zero. **We shipped exactly this**,
sealed, publicly, before data. It could only ever report the null, and the seal made that look
like rigour. `preregister` will not seal it.

## 7. Put it in CI

```yaml title=".github/workflows/verify.yml"
- uses: nickharris808/proof-carrying-ci@main
  id: audit

- uses: github/codeql-action/upload-sarif@v3
  if: always()
  with: { sarif_file: proof-carrying-ci.sarif }
```

By default the job reddens only on a check that **ran and failed**. An `UNVERIFIED` aggregate is
reported everywhere — summary, SARIF, step outputs — without breaking the build, because a check
that goes red when an optional tool is missing is a check that gets deleted. Opt into
`fail-on: unverified` when you want the stricter posture.

---

## What you just proved, and what you did not

| you proved | you did not prove |
|---|---|
| this lock pair *can* deadlock — here is the cycle | that the code has no other deadlock; the importer cannot see cross-function edges |
| every file the manifest lists is present and unchanged | that the manifest lists everything that matters |
| the aggregate is `FAILED` | that a `PASSED` would mean "correct" — it means every applicable check ran and held |
| the pre-registration cannot fire | that a falsifiable rule has the *power* to detect an effect; that is a separate calculation |

Keeping that column honest is the point. See [What this proves](../concepts/what-this-proves.md).

## Next

- [The abstention discipline](../concepts/abstention.md) — why "not checked" is a third value
- [Verdicts and exit codes](../concepts/verdicts.md) — the algebra, precisely
- [Troubleshooting](../guides/troubleshooting.md) — when a tool says something surprising
