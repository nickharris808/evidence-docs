# A measurement you cannot check is a press release

Twenty-five open-source artifacts. **One idea.**

> Every verifier has the same tempting shape: *return success unless you find a problem.* Hand one
> an empty file, a truncated log, or an artifact with no trust anchor, and it finds no problem — so
> it reports success. It has verified nothing, and it has said so in green.

Every tool here is built to refuse that. When a tool cannot check something, it says **so**, in a
way you cannot mistake for a pass.

---

## The rule, in three lines

```
exit 0    checked, and the property holds
exit 1    checked, and the property does NOT hold
exit 2    NOT CHECKED — and this is never a pass
```

That third value is the whole portfolio. It is why `gridlock` refuses to call an empty graph
deadlock-free, why `kvleak` refuses to report a null it cannot interpret, why `preregister` refuses
to seal an experiment whose conclusion is already fixed, and why `evidence` aggregates to **the
weakest leg, never the mean**.

---

## Why these are one body of work and not twenty-five unrelated repos

They came out of one project — a proof-carrying inference stack — and each one exists because
*that* project got something wrong first. The tools are the scar tissue, extracted and made
general.

| the tool | the mistake that produced it |
|---|---|
| [`preregister`](https://github.com/nickharris808/preregister) | we sealed a pre-registration whose positive branch was **structurally unreachable**. `prompt_logprobs=0` pins `argmax_flips` to zero; the sealed rule was `argmax_flips > 0`. Public, honest, and guaranteed to report the null before a GPU started |
| [`gridlock`](https://github.com/nickharris808/gridlock) | our own checker certified `{}` — the empty graph — as **SAFE**, and printed "rank strictly decreases on every edge: yes". `all([])` is `True` |
| [`abstain-bench`](https://github.com/nickharris808/abstain-bench) | when we first pointed it at a real tool, **it accused a correct verifier**. Two of its findings were wrong |
| [`kvleak`](https://github.com/nickharris808/kvleak) | a clean cross-tenant result whose **positive control had failed** — the probe could not have seen a leak if one existed |
| [`gatecount`](https://github.com/nickharris808/gatecount) | "we fuzzed it and found zero escapes" is a statement about a search, not about the system |
| [`evidence`](https://github.com/nickharris808/evidence) | four checks passing and one that could not run is **not** "80% verified" |
| [`signoff-cert`](https://github.com/nickharris808/signoff-cert) | a certificate that does not carry its own false-pass bound is a sticker |
| [`honestbench`](https://github.com/nickharris808/honestbench) | our CI was green. We deleted the evidence it checked. It was **still green** |

That last one is the shortest description of the whole portfolio. If deleting your evidence does
not turn your build red, your build was never checking your evidence.

---

## Where to start

<div class="grid cards" markdown>

- **:material-play: I want to try it**

    ---

    One command over your own repository, in about thirty seconds.

    [→ Install](start/install.md) · [→ The tutorial](start/tutorial.md)

- **:material-lightbulb: I want to understand the idea**

    ---

    Why "not checked" is a third value, and what that buys you.

    [→ The abstention discipline](concepts/abstention.md) · [→ Verdicts](concepts/verdicts.md)

- **:material-scale-balance: I want to know what it actually proves**

    ---

    The honest scope, stated as matched pairs: what a pass means, and what it does not.

    [→ What this proves](concepts/what-this-proves.md)

- **:material-book-open: I want the reference**

    ---

    Every tool, every command, every API.

    [→ Tools](reference/tools.md) · [→ CLI](reference/cli.md) · [→ API](reference/api.md)

</div>

---

## Sixty seconds

```bash
pip install "evidence[all]"
evidence audit .
```

Against a repository with a genuine lock-order inversion:

```console
  gridlock       WEDGES: conn_lock -> stats_lock -> conn_lock   [FAIL]
  honestbench    no evidence manifest found (honestbench n...   [n/a]
  proof-drift    no Lean sources, so there is no proof to ...   [n/a]
  sf-verify      no hash-chained decision log (.jsonl with...   [n/a]
  signoff-cert   no signoff-cert/v1 certificates found          [n/a]
  --------------------------------------------------------------------------
  AGGREGATE: FAILED — 1 fail, 4 n/a
  gridlock is FAILED, and the aggregate is the weakest leg — 0 other constituent(s) passing does not lift it
```

Note what it did **not** say. It did not say "1 of 5 checks failed, 80% healthy". Four tools found
nothing to look at; that is not four passes.

---

## The map

**Run everything** — [`evidence`](https://github.com/nickharris808/evidence) ·
[`proof-carrying-ci`](https://github.com/nickharris808/proof-carrying-ci) (the GitHub Action) ·
[`formal-proof-mcp`](https://github.com/nickharris808/formal-proof-mcp) (for coding agents)

**Check a property** — [`gridlock`](https://github.com/nickharris808/gridlock) (deadlock) ·
[`floorgen`](https://github.com/nickharris808/floorgen) (state floors) ·
[`gatecount`](https://github.com/nickharris808/gatecount) (over-acceptance) ·
[`proof-to-code-drift`](https://github.com/nickharris808/proof-to-code-drift) ·
[`tokencount`](https://github.com/nickharris808/tokencount)

**Check the evidence itself** — [`signoff-cert`](https://github.com/nickharris808/signoff-cert) ·
[`honestbench`](https://github.com/nickharris808/honestbench) ·
[`sf-verify`](https://github.com/nickharris808/sf-verify) ·
[`preregister`](https://github.com/nickharris808/preregister)

**Check a live system** — [`kvleak`](https://github.com/nickharris808/kvleak) ·
[`kvprobe`](https://github.com/nickharris808/kvprobe)

**Check the checkers** — [`abstain-bench`](https://github.com/nickharris808/abstain-bench) ·
[`illusion-bench`](https://github.com/nickharris808/illusion-bench)

**Reproduce our own numbers** —
[`kv-reuse-econ-bench`](https://github.com/nickharris808/kv-reuse-econ-bench) ·
[`llm-tenant-isolation-bench`](https://github.com/nickharris808/llm-tenant-isolation-bench)

Full table with one-liners and honest-scope notes: [**Tools reference**](reference/tools.md).

---

## What none of this does

**Nothing here gates anything.** Every tool computes a verdict and prints it; the exit code is
advisory, and no package ships a mechanism that would withhold, block, or refuse an operation on
the strength of its own result. That line is deliberate and it is documented per-package in each
repository's `CLAIMS-MAP.md`. See [Measure vs gate](concepts/measure-gate.md).

**Nothing here claims a speedup.** There is no performance claim anywhere in this portfolio.
Where we measured one and it did not survive, we said so.

---

*Apache-2.0. Built by [@nickharris808](https://github.com/nickharris808).*
