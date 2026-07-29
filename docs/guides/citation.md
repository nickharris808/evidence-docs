# Citing this work

Every repository carries a `CITATION.cff`, so GitHub's "Cite this repository" button works and
tools like Zenodo and `cffconvert` can read it directly.

## One tool

Use the repository's own `CITATION.cff`. On GitHub: **Cite this repository** → BibTeX.

```bibtex
@software{harris_gridlock,
  author  = {Harris, Nick},
  title   = {gridlock: certify a wait-for relation cannot wedge},
  url     = {https://github.com/nickharris808/gridlock},
  license = {Apache-2.0},
  year    = {2026}
}
```

## The portfolio

```bibtex
@software{harris_verification_portfolio_2026,
  author  = {Harris, Nick},
  title   = {A measurement you cannot check is a press release:
             twenty-five verification tools that refuse to guess},
  url     = {https://github.com/nickharris808},
  license = {Apache-2.0},
  year    = {2026}
}
```

## The datasets

The Hugging Face datasets carry their own cards with licence and provenance:

- [`abstain-corpus`](https://huggingface.co/datasets/nickh007/abstain-corpus) — 32 inputs a
  verifier must not pass
- [`kv-tenant-isolation-bench`](https://huggingface.co/datasets/nickh007/kv-tenant-isolation-bench)
  — including four rows marked uninterpretable
- [`kv-reuse-econ-traces`](https://huggingface.co/datasets/nickh007/kv-reuse-econ-traces)
- [`llm-precision-fingerprints`](https://huggingface.co/datasets/nickh007/llm-precision-fingerprints)

## If you are citing a negative result

Several results here are **honest negatives** and are more useful cited as such:

- a pre-registered null: default vLLM 0.24.0 was **exactly** batch-invariant under static
  co-batching on the configuration tested (dynamic batching untested);
- a headline of ours that turned out to be an **arithmetic identity** rather than a measurement;
- **no speedup claim**: a live comparison came out at ≈0.997×.

These are documented in the repositories that produced them, not summarised away.
