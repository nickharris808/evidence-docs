# Contributing

Each repository has its own `CONTRIBUTING.md`; this is what applies across all of them.

## The one rule

**No change may make a tool produce a confident-looking answer it has not earned.**

A patch that turns an `UNVERIFIED` into a `PASSED` needs to explain what is now being checked that
was not before. A patch that removes a refusal needs to explain why the refused input is now
checkable. Both are welcome — that is how the tools get better — but the burden is on the change.

## What a good pull request looks like

1. **A failing test first.** Preferably one that produces a *wrong-looking-right* answer, not a
   crash. Crashes are easy; confident wrong answers are the risk.
2. **The fix.**
3. **The scope section updated** if the change alters what the tool proves.

## What will be sent back

- **Weakening a test to make CI green.** Fix the cause. If the test was wrong, say so explicitly
  and invert it rather than deleting it — there is precedent: `test_empty_graph_is_safe` asserted
  a defect was correct behaviour and was inverted, with a comment, as a marker.
- **A hand-written list or count** of anything that could be derived. This has gone stale four
  times in this repository, silently, while still rendering.
- **An exemption marker to satisfy a rail.** Rename the function. An exemption is a hole forever.
- **A number in a README that the published code does not produce.** Every number must be
  reproducible by running the shipped code.
- **A verdict about a named third-party product.** Ship the checker; not a league table.

## Adding a new tool

If it fits the portfolio it will have these:

- a **three-valued verdict** and the 0/1/2 exit-code contract;
- a **refusal** for its own degenerate input, with a test;
- a **worked example in the README that actually runs**, with real output;
- an **honest scope** section written as matched pairs — what a pass means, and what it does not;
- a `CLAIMS-MAP.md` naming the nearest claim family and the step not taken;
- **no runtime dependency on a sibling package**. Vendor with the provenance in the header.

## Running the tests

```bash
pip install -e ".[dev]"
python -m pytest -q
```

Do not add `-q` — several packages set `addopts = "-q"` already, and `-qq` suppresses the summary
so it looks like nothing ran.

## Reporting a false accusation

If a tool flags something correct, that is a **defect of equal severity** to a missed detection.
Over-refusal trains people to bypass refusals, which destroys the tool. Open an issue with the
input and the expected verdict.

## Security

These are measurement instruments; they take untrusted input by design. If you find a way to make
one execute code from a data file, or report a confident pass on input it cannot check, please
open a private security advisory on the affected repository rather than a public issue.

Spec files deliberately carry **tables, not expressions**, for this reason: a file format that
evaluated code from disk would make `floorgen floor untrusted.json` a code-execution primitive.
