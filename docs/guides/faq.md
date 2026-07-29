# FAQ

Written from objections actually raised — several by hostile reviews of this work, several by us
against ourselves. Where the objection landed, it says so.

## "Isn't this just linting with extra steps?"

A linter answers *did I find a problem*. These answer *was I able to look*. The difference only
shows up on degenerate input, which is exactly where a linter quietly returns success. Hand a
linter an empty file and it is happy. Hand `gridlock` an empty graph and it refuses to certify it.

## "Exit code 2 is non-standard. Why not just fail?"

Because they mean different things and conflating them destroys both. `1` says *I checked and
found a defect* — actionable, fix it. `2` says *I could not check* — usually a configuration
problem, and fixing the code will not help.

If they were the same value, every "could not check" would be triaged as a bug in the code, and
after the third false alarm the check would be disabled.

## "You made the CI Action default to not failing on UNVERIFIED. Isn't that fail-open — the exact thing you criticise?"

**This is the best objection on the page and it is half right.**

It would be fail-open if anything were *reported as verified*. Nothing is: the verdict is
`UNVERIFIED` in the summary, in SARIF, in the step outputs, and in the JSON. The only question is
whether it turns the tick red.

We resolved it toward *not* reddening because a check that goes red when an optional tool is
missing is a check that gets deleted within a fortnight — and then nothing is reported at all.
`fail-on: unverified` is one line away if you disagree, and all nine cells of that matrix are
pinned by a test. The reasoning is in the module docstring, not just here.

## "The empty-graph bug is trivial. Who ships that?"

We did. `gridlock` certified `{}` as **SAFE** and printed *"rank strictly decreases on every edge:
yes"*. There was a test asserting that behaviour was correct — `test_empty_graph_is_safe`. It was
inverted rather than deleted, and kept as a marker.

`all([])` being `True` is not obscure. It is trivial *and* it shipped, which is the point.

## "Your benchmark accused a correct tool. Why should I trust it?"

You should trust it more for that being written down. On its first live run `abstain-bench`
reported two unearned passes against a **correct** verifier: `{"a": []}` — one node waiting on
nothing — genuinely cannot deadlock. Both cases moved to a `sound_pass` category scored in
neither direction.

A benchmark that manufactures findings is exactly as untrustworthy as the unearned passes it
exists to measure. If you find another false accusation, it is a bug and we will move the case.

## "Is any of this novel?"

Mostly **no**, and the packages say so. `floorgen` is pigeonhole lifted pointwise — its own
reports and its CLAIMS-MAP state that it makes no novelty claim. `gridlock`'s theorem is
textbook. Clopper–Pearson is from 1934.

What is contributed is the *refusal discipline* applied consistently, the exact mechanised counts,
and the honest scope sections. If you want novelty claims, this is the wrong portfolio.

## "Why is there no speedup number anywhere?"

Because we measured one and it did not survive. A live comparison came out at **≈0.997×**. There
is no performance claim in this portfolio, and there will not be one that is not reproducible from
published code.

## "Why won't you publish results against real products?"

Responsible disclosure. The checker and the methodology ship; a league table of other people's
software does not. The only subjects named anywhere in this portfolio are a synthetic example
written for the purpose and our own tools. Point them at whatever you like and publish under your
own name.

## "What's the catch? What are you selling?"

The measuring is free and Apache-2.0 forever. The **enforcement** side — binding a partition key
at the admission decision, the compiled gate, the certificate-issuing faucet — is covered by
filed patents and licensed separately. Every repository carries a `CLAIMS-MAP.md` naming the
nearest claim family and the specific step the open tool does not take.

**Reading is free. Enforcing is licensed.** See [Measure vs gate](../concepts/measure-gate.md).

## "Can I use these in a compliance audit?"

They produce technical evidence, not legal conformity. A `PASSED` says every applicable check ran
and held — nothing about any regulation. Anyone telling you a tool proves compliance is selling
something.

## "One of your tools disagreed with another."

Please open an issue with both outputs. There are differential tests between implementations
precisely because a reimplementation is a second chance to be wrong — the browser version of
`gridlock` in the visualiser Space is checked against the Python one over 400 random graphs, with
0 disagreements. A disagreement is a real bug in at least one of them.

## "Do you use these on yourselves?"

Yes, and it keeps finding things. While writing the tutorial for this site, running the documented
commands revealed that `evidence`'s `honestbench` leg had **never once run** — it was invoked with
the wrong argument order, argparse rejected it, and the usage message was being reported as the
leg's detail. It reported `UNVERIFIED`, so nothing was ever falsely claimed, but the check had
never happened. Both that and a broken remedy string in an error message were found by *using* the
tools rather than reading them, and both are now covered by regression tests.
