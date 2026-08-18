# Install

!!! danger "Two commands on this page were wrong until 2026-07-30 — read this if you copied them"
    This page previously opened with `pip install "evidence[all]"` and
    `pip install gridlock ...`. **Neither name is ours on PyPI.** `evidence` is an unrelated DFXML
    forensics tool and `gridlock` is an unrelated coupled-gridding library — the second installs
    *silently*, pulling `numpy` and `float-raster`. If you ran either, uninstall with
    `pip uninstall evidence gridlock` and use the commands below.

    Our distributions are named `evidence-runner`, `gridlock-certify` and `tokencount-verify`
    precisely because the bare names were taken. The corrected commands follow.

## Install from source — this works for all 20 packages, today

```bash
pip install "git+https://github.com/nickharris808/<name>"
```

Every package installs cleanly this way, and it is how CI installs them. Substitute the repository
name, e.g. `gridlock`, `preregister`, `floorgen`, `gatecount`, `abstain-bench`.

## From PyPI — 4 of 20 are published

```bash
pip install abstain-bench certhead evidence-runner tokencount-verify
```

Those four are live. **The other 16 are not on PyPI yet**, so a bare `pip install <name>` for them
will either 404 or — for `evidence` and `gridlock` — resolve to a different project entirely. Use the
git+https form above until this page says otherwise.

!!! info "Why publication is pending"
    Trusted Publishing (OIDC) workflows are committed — no token is stored anywhere — but enabling
    each project needs a one-time browser step by the account owner.

## The aggregate runner

```bash
pip install "git+https://github.com/nickharris808/evidence"
```

`evidence-runner` is on PyPI, but **its `[all]` extra cannot resolve today**: it depends on
`gridlock-certify`, `signoff-cert`, `honestbench`, `sf-verify` and `proof-to-code-drift`, none of which
is published yet. Install the runner alone — it works with **no** constituent installed. Each missing
tool reports `MISSING` and does not vote: it cannot drag the verdict down, and it cannot hold it up.

To drive real constituents, install them from source alongside it.

## In CI

```yaml
- uses: nickharris808/proof-carrying-ci@main
  id: audit
- uses: github/codeql-action/upload-sarif@v3
  if: always()
  with: { sarif_file: proof-carrying-ci.sarif }
```

## For a coding agent (MCP)

```bash
pip install "git+https://github.com/nickharris808/formal-proof-mcp"
```

`formal-proof-mcp` is **not on PyPI yet**, so `pip install formal-proof-mcp` will 404. The `[all]`
extra additionally pulls constituents that are also unpublished; install from source and add the
constituents you want the same way.

```json
{ "mcpServers": { "formal-proof": { "command": "formal-proof-mcp" } } }
```

Ten tools, including a Lean proof kernel with an axiom audit that catches `sorry`. A tool whose
package is not installed returns `unavailable` — **never** `ok`.

## Requirements

Python **3.9+**. Every package has **zero required runtime dependencies**; optional extras are
declared per package (`[yaml]`, `[k8s]`, `[plot]`).

## Verify the install

```bash
evidence tools          # what can run here, and what deliberately cannot
gridlock demo           # seven domains, seven firing foils
preregister selftest
floorgen selftest
gatecount selftest
```
