# What this proves — and what it does not

Stated as matched pairs, because a scope section that only lists strengths is marketing.

## Portfolio-wide

| a `PASSED` means | a `PASSED` does **not** mean |
|---|---|
| every check that had something to examine, examined it, and the property held | that the checks cover everything worth checking. An audit is exactly as broad as the tools that ran |
| the specific property each tool names | "this code is correct". No tool here claims that |
| the property held **on the input you supplied** | that the input is representative of production |

An aggregate inherits every limitation of every leg it summarises and adds **no confidence of its
own**.

## Per tool

### `gridlock` — deadlock

**Proves:** the wait-for relation you supplied is acyclic, and a rank strictly decreases along
every edge, so no set of those holders can be stuck waiting on each other. Cycle detection is
exact, not sampled.

**Does not prove:** that your *system* cannot deadlock. The Python importer cannot see locks
across function boundaries — if `f()` holds A and calls `g()`, which takes B, that edge is
invisible. **A cycle it finds is real; a `SAFE` from that importer is weak evidence**, and every
run says so. The k8s importer reads exported manifests and never contacts a cluster, so its graph
is exactly as complete as the file you exported.

### `floorgen` — state floors

**Proves:** no implementation meeting your spec can distinguish fewer than N situations. If your
budget is below N, the spec is impossible — and that impossibility is *proven*, by pigeonhole.

**Does not prove:** that N is achievable. Meeting the bound excludes the counting obstruction and
nothing else, which is why `floorgen` says `NOT_EXCLUDED` and never "feasible". The floor is also
exact only for *the space your spec declares*: a spec that omits a distinction the real system
must make yields a floor that is too low, and no tool can detect that.

**Makes no novelty claim.** Pigeonhole lifted pointwise is textbook. What is mechanised is the
exact count.

### `gatecount` — over-acceptance

**Proves:** over the domain you declared, exactly this many assignments are admitted by one
predicate and refused by the other. A count, not an estimate.

**Does not prove:** that the domain is your real input space (declare 0–255 for a 64-bit field and
the number is right about a different system); that the states are equally likely or equally
dangerous — this is a cardinality, not a risk estimate; or that your transcription of the policy
into an expression is faithful.

### `preregister` — falsifiability

**Proves:** both outcomes are reachable over the supports your spec declares, so the data is
*capable* of deciding.

**Does not prove:** that the experiment has the **power** to decide. Reachability is not
sensitivity; `auc > 0.5` is falsifiable at n=3 and useless at n=3. Nor that your declared supports
are correct — declare a range your config actually pins to a constant and the analysis runs over
the wrong domain. The seal proves the plan did not change; **it is not a signature** and proves
nothing about authorship.

### `honestbench` — evidence honesty

**Proves:** every file the manifest lists is present and hashes to what it claimed.

**Does not prove:** that the manifest lists everything that matters. It cannot know what you chose
not to record.

### `sf-verify` — decision logs

**Proves:** the log's hash chain is intact.

**Does not prove:** that the log is **complete**. A prefix of a hash chain verifies perfectly;
only an external anchor detects a removed tail. This is why `VERIFIED` requires an anchor and
anything else is `UNVERIFIED`, exit 2.

### `abstain-bench` — verifier quality

**Proves:** on these inputs, in the families the subject demonstrated it handles, it never claimed
success on something it could not check.

**Does not prove:** that the verifier is *correct*. This measures one failure mode. The corpus is
not exhaustive — six ways of having nothing to check, and there are certainly others. With 24
scored cases, report the exact binomial interval; 0 of 24 is **not** "0% ± 0%", it is 0% with an
upper bound of 14.2%.

### `kvleak` / `kvprobe` — live systems

**Prove:** what they measured, under the residency precondition they check first.

**Do not prove:** anything at all when that precondition fails. A null from a cache that could not
have retained the victim's state is uninterpretable, and `kvleak` refuses to report it as an
all-clear.

## Claims this portfolio does not make

**No speedup claim, anywhere.** We measured one and it did not survive: a live comparison came out
at ≈0.997×. It is not in any README.

**No claim about any named third-party product.** The checkers and the methodology ship; verdicts
about other people's software do not. Point the tools at whatever you like and publish your own
results under your own name.

**No security guarantee.** These are measurement instruments. Finding nothing is not the same as
there being nothing, and every tool here is built to make that distinction impossible to miss.
