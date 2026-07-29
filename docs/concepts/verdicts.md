# Verdicts, exit codes, and the weakest leg

## The exit-code contract

Portfolio-wide, without exception:

| code | meaning |
|---|---|
| `0` | checked, and the property holds |
| `1` | checked, and the property does **not** hold |
| `2` | **NOT CHECKED** — no conclusion was reached |

Some tools use domain words for these (`SAFE`/`WEDGES`/`ABSTAIN`, `VERIFIED`/`FAILED`/`UNVERIFIED`,
`FLOOR`/`NO_FLOOR`), but the numeric contract is the same everywhere, so `$?` means one thing
across all twenty-five.

!!! danger "Read `$?` before you pipe"
    `tool | head` gives you `head`'s exit status, not the tool's. This has caught us twice.
    Redirect to a file, then read `$?`.

## The aggregation rule

When more than one check runs, the answer is **the weakest leg, never the mean.**

Weakest to strongest:

```
FAILED   <   UNVERIFIED   <   PASSED
```

`min` over that ordering *is* the rule. So:

- four passes and one `UNVERIFIED` → **`UNVERIFIED`**. Not "80% verified."
- fifty passes and one `FAILED` → **`FAILED`**. No number of passes lifts it.

`FAILED` is weakest because it is the only value reporting a *known* defect. `UNVERIFIED` sits
above it — "we don't know" beats "we know it's broken" — and strictly below `PASSED`.

## The two values that do not vote

**`n/a`** — the tool found nothing here to speak about. It looked for a Lean file; there were
none. Folding this into the verdict would make every repository permanently `UNVERIFIED`, and a
warning nobody can ever clear is a warning everybody learns to ignore.

**`MISSING` / not installed** — reported loudly rather than skipped, because an aggregate
assembled from whichever tools happened to be importable is an aggregate whose *scope depends on
the machine that ran it*.

## The rule that catches the rule

**An aggregate over zero voting legs is `UNVERIFIED`, always.**

`all([])` is `True`. A runner that reports success because nothing objected is precisely the bug
this portfolio exists to prevent, reappearing one level up. There is a test for it in both
`evidence` and `proof-carrying-ci`.

## Verdict-preserving formats

A verdict must survive every format it is rendered into:

- **SARIF** — `FAILED` → `error`, `UNVERIFIED` → `warning`, `PASSED` → `note`. Passes are
  **emitted**, not omitted: an empty SARIF file is indistinguishable from a run that checked
  nothing.
- **JUnit** — an `UNVERIFIED` leg is an `<error>`, **never** a `<skipped>`. A skipped test is one
  nobody wanted to run; an unverified check is one that was wanted and could not be completed,
  and dashboards colour those very differently.
- **JSON** — a refused score is `null`, never `0.0`. Anything that treats a missing score as zero
  should crash rather than average it in.

There is a regression test asserting that every export format of every tool preserves the verdict
it was built from.
