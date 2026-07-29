# The abstention discipline

Every verifier has the same tempting shape:

```python
def check(thing):
    for rule in rules:
        if rule.violated(thing):
            return FAIL
    return PASS          # <-- the bug
```

Nothing is wrong with that code until you hand it something it cannot examine. An empty file. A
truncated log. An artifact with no trust anchor. A schema it has never seen. It runs every rule,
finds no violation, and returns `PASS`.

It has verified nothing, and it has said so in green.

## Three values, not two

```
0   checked, and the property holds
1   checked, and the property does NOT hold
2   NOT CHECKED
```

`2` is not a soft failure. It means *no conclusion was reached*, and it must never be reachable by
rounding either of the other two.

## Six ways to have nothing to check

The [`abstain-corpus`](https://huggingface.co/datasets/nickh007/abstain-corpus) enumerates them,
because you cannot defend against a class you have not named:

| category | why a pass would be unearned |
|---|---|
| **empty** | zero records, nodes or bindings. `all([])` is `True` — a fact about logic, not evidence about software |
| **truncated** | a hash chain's prefix verifies *perfectly*. Only an anchor reveals the missing tail |
| **no anchor** | internally consistent, tied to nothing. Self-consistency is not authenticity |
| **degenerate** | structurally valid, semantically vacuous: a bound of 1.0, a sample of size zero |
| **malformed** | not the thing it claims to be. The right answer is a diagnosis, not a verdict |
| **out of distribution** | a schema, version or shape the verifier does not know |

## The failure is not hypothetical

Each of these was a real defect in this portfolio, found and fixed:

- `gridlock` certified `{}` as **SAFE**, and printed *"rank strictly decreases on every edge:
  yes"* about a graph with no edges.
- `sf-verify` printed **VERIFIED** and exited 0 on a **truncated** decision log. The prefix
  verified, because a prefix of a hash chain always does.
- `proof-to-code-drift` exited **0** when it found **zero** bindings to check.
- `evidence`'s `gridlock` leg reported **PASS** because `gridlock import` succeeded — an import
  says nothing whatever about deadlock.
- `evidence`'s `honestbench` leg was invoked with the wrong argument order and **never ran once**.
  It reported UNVERIFIED, so nothing was ever falsely claimed — but the check had never happened.

The last two are the interesting ones. Both were found by *using* the tools, not by reading them.

## Over-refusal is a defect too

A verifier that refuses everything scores a perfect zero unearned passes and is completely
useless. This is not a rhetorical concession — it bit us:

> The first time [`abstain-bench`](https://github.com/nickharris808/abstain-bench) was pointed at
> a real tool, **it accused a correct verifier of two unearned passes.** `{"a": []}` — one node
> waiting on nothing — genuinely cannot deadlock. An acyclic graph whose identifiers contain a
> right-to-left override is still acyclic; the deception is in how a *terminal renders* the name,
> not in the verdict.

Both moved to a `sound_pass` category that is scored in neither direction. **A benchmark that
manufactures findings is exactly as untrustworthy as the unearned passes it exists to measure.**

That is why every scoring tool here refuses to report a number until the subject has demonstrated
it can *discriminate* — accept a positive control, reject a negative one.

## What follows from it

- an aggregate over **zero** checks is `UNVERIFIED`, always ([`evidence`](https://github.com/nickharris808/evidence))
- a **sampled** zero is not a proof of emptiness ([`gatecount`](https://github.com/nickharris808/gatecount))
- a floor of 1 establishes nothing, so it exits 2 ([`floorgen`](https://github.com/nickharris808/floorgen))
- an analysis that could not settle a question returns `UNDETERMINED` and **refuses to seal**
  ([`preregister`](https://github.com/nickharris808/preregister))
- a SARIF file with no results is indistinguishable from a run that checked nothing, so passes
  are emitted too ([`proof-carrying-ci`](https://github.com/nickharris808/proof-carrying-ci))
