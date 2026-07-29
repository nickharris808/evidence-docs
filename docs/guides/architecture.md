# Architecture

## Shape

Every package is the same shape, deliberately:

```
src/<pkg>/
  core.py        the decision procedure — no I/O, no argparse, importable and testable
  cli.py         argument parsing, rendering, exit codes
  __init__.py    a curated __all__; this IS the public API
tests/
examples/        inputs the README's worked example actually uses
CLAIMS-MAP.md    the measure/gate boundary for this package
```

**No package depends on another at runtime.** Where two need the same code — the expression parser,
the Clopper–Pearson interval — it is **vendored with the provenance stated in the file header**,
not shared through a dependency edge. Twenty-five packages coupled by a dependency graph would
have twenty-five coupled release cycles.

The one exception is `evidence`, which *drives* the others as subprocesses. It never imports them.

## The three layers

```
proof-carrying-ci        ← CI integration: SARIF, job summary, step outputs
   └── evidence          ← detection + aggregation; the constituent registry lives HERE
         └── the tools   ← each answers one question, exactly
```

`proof-carrying-ci` delegates detection to `evidence` rather than duplicating it, so the two
cannot drift about what a constituent is. `formal-proof-mcp` sits beside `evidence`, exposing the
same tools to an agent over MCP.

## Why subprocesses and not imports

`evidence` invokes each constituent as a **subprocess** and reads its exit code. That is slower and
it is the right call:

- the exit-code contract is the *actual* interface, so driving it exercises what users get;
- a constituent that crashes cannot take the runner with it;
- a version mismatch shows up as a usage error, not as a silent behaviour change.

It also has a cost that bit us: an invocation with the wrong argument order is only detectable by
*running* it. `evidence`'s `honestbench` leg was wrong for its entire life — reported `UNVERIFIED`
so nothing was falsely claimed, but the check never ran. There is now a test that executes every
constituent's invocation against a real fixture and fails on a usage error.

## Generated, not written

Anything that would go stale is generated from the thing it describes:

| artefact | derived from | rail |
|---|---|---|
| `reference/cli.md` | **running** `--help` on every console script | `gen_site.py --check` |
| `reference/api.md` | each package's real `__all__` and signatures | `gen_site.py --check` |
| `reference/tools.md` | `PUBLISHED.json` + the canonical table | `gen_site.py --check` |
| portfolio footers | the directories that exist | `crosslink.py --check` |
| per-package `docs/CLI.md` | `--help` | `gen_docs.py --verify` |

This is not tidiness. Four separate hand-written lists in this repository went stale **while still
rendering**: a docs generator that knew 12 of 14 tools, a cross-linker with four literal group
lists and the words "Seventeen artifacts", and an MCP self-test asserting `len(TOOLS) == 6` while
the server advertised ten. All four kept working and kept being wrong.

## The rails

Run over the whole tree, in CI and before every publish:

| rail | refuses |
|---|---|
| `check_measure_only` | an actuation path in anything tagged CLEAN. **Zero exemptions** |
| `check_license_tags` | a package whose licence and CLAIMS-MAP tag disagree |
| `check_readme_reality` | a README claiming something the code does not do |
| `preflight_scan` | credentials, local paths, internal module names, business figures |
| `check_provenance` | a number with no recorded source |
| `check_no_publish` | anything on the hold list reaching a publish table |
| `gen_site --check` | a generated page that has drifted from the code |

`check_measure_only` is name-based and does produce false positives. Both that have occurred were
fixed by **renaming the function**, never by adding an exemption marker: an exemption is a hole in
a guard forever; a rename costs one line.

## Testing posture

Each package's suite is weighted toward the ways a *correct-looking* answer can be wrong:

- **degenerate input** — empty, single-element, all-identical, zero-size;
- **the false-accusation direction** — ordinary valid inputs asserted *not* to be flagged, because
  a tool that cries wolf gets its refusals bypassed;
- **an independent oracle** — brute force written separately from the implementation;
- **differential** — where a procedure is reimplemented (the browser cycle detector), the two are
  checked against each other on random inputs.
