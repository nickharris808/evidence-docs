# Install

## Everything at once

```bash
pip install "evidence[all]"
```

That pulls the runner plus every constituent it knows how to drive. `evidence` works with **no**
constituent installed too — each missing tool reports `MISSING` and does not vote. It cannot drag
the verdict down, and it cannot hold it up either.

## One tool at a time

```bash
pip install gridlock preregister floorgen gatecount abstain-bench
```

!!! info "PyPI publication is pending an account action"
    Trusted Publishing (OIDC) workflows are committed to every package — no token is stored
    anywhere — but enabling them needs a one-time browser step by the account owner. Until then,
    install from source:

    ```bash
    pip install "git+https://github.com/nickharris808/<name>"
    ```

    Every package installs cleanly this way; it is how CI installs them.

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
pip install "formal-proof-mcp[all]"
```

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
