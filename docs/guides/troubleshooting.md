# Troubleshooting

## "It exited 2 and I don't know why"

`2` means **nothing was checked**. It is never a soft failure. Every tool prints the reason on the
line above; the common causes:

| you saw | it means | do this |
|---|---|---|
| `ABSTAIN — nothing to certify` | the importer found no subjects | check the path; an empty graph would certify as vacuously SAFE, so it refuses to emit one |
| `UNVERIFIED` from `sf-verify` | the chain is intact but there is **no anchor** | supply a signed tree head; a prefix of a hash chain always verifies |
| `NO_FLOOR` from `floorgen` | every situation demands the same answer | re-read your answer function — a floor of 1 is true and vacuous |
| `UNDETERMINED` from `preregister` | the analysis could not settle reachability | narrow the declared supports, or simplify the rule |
| `no exact count was established` | the domain exceeds the enumeration cap | shrink the domain, or pass `--sample N` for an explicitly approximate answer |

## `$?` gives the wrong number

```bash
mytool check thing | head        # <-- $? is head's status, not mytool's
```

Redirect first:

```bash
mytool check thing > out.txt; echo $?
```

This has caught us twice. It is the single most common way to misread these tools.

## "0 tests passed" when running pytest

Several packages set `addopts = "-q"` in `pyproject.toml`. Adding another `-q` on the command line
makes `-qq`, which suppresses the summary line entirely — so it looks like nothing ran. Use plain
`pytest`, or `pytest -v`.

## `evidence` says a tool is `n/a` and I expected it to run

`n/a` means the tool looked for its subject and did not find one. Run `evidence tools` to see what
each constituent needs. The most common cases:

- **honestbench** needs a manifest: `honestbench manifest . --out MANIFEST.sha256`
- **gridlock** needs Python sources it can import a lock graph from
- **sf-verify** needs a `.jsonl` whose records carry a `prev` field
- **signoff-cert** needs a file containing `signoff-cert/v1`

## `pip install .[dev]` succeeded but pytest is missing

pip treats an **unknown extra as a warning, not an error**. If a package has no `dev` extra, the
install silently succeeds and installs nothing extra. Check the extra exists:

```bash
python -c "import tomllib;print(list(tomllib.load(open('pyproject.toml','rb'))['project']['optional-dependencies']))"
```

## A GitHub Action fails on Python 3.9

Check `requires-python` in the package. Some packages need 3.10+; a hardcoded 3.9 in a workflow
matrix will fail at install rather than at test time.

## `gridlock` says SAFE and I don't believe it

You are right to be suspicious. The Python importer **cannot see locks across function
boundaries** — if `f()` holds A and calls `g()`, which takes B, that edge is invisible. A cycle it
finds is real; a `SAFE` from that importer is weak evidence, and the tool says so on every run.

## A number in a README doesn't reproduce

That is a bug and we want to know. Every number in this portfolio should be reproducible by
running the published code — there are tests asserting several of them against live runs. Open an
issue on the repository in question with the command you ran.
