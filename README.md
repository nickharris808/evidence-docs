# evidence-docs

Documentation for a portfolio of twenty-five verification tools with one rule: report what is
true, refuse what cannot be checked, and never round "not checked" up to "fine".

**Read it: <https://nickharris808.github.io/evidence-docs/>**

Built with MkDocs Material. The reference section is **generated** — `reference/cli.md` by running
`--help` on every published console script, `reference/api.md` from each package's real `__all__`,
`reference/tools.md` from what actually shipped. A CI rail (`gen_site.py --check`) fails the build
if a generated page drifts from the code it describes.

```bash
pip install mkdocs-material
mkdocs serve
```

Apache-2.0.
