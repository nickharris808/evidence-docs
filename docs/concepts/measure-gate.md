# Measure vs gate

Every tool in this portfolio **measures**. None of them **gates**.

## The distinction

A tool that *measures* observes something and reports a verdict. A tool that *gates* takes that
verdict and acts on it — admits a request, provisions a resource, binds a key, withholds a
payment, blocks a release.

The line matters because it is where the open-source portfolio ends and a patented,
separately-licensed implementation begins. Every method independent in the associated family
terminates in a physical actuation step, and **no package here performs one**.

## What that means concretely

| these tools do | these tools do not |
|---|---|
| compute a verdict and print it | admit, refuse, provision, install or actuate anything |
| return an exit code | require anything to honour that exit code |
| write SARIF, JUnit, JSON, Markdown | block a merge, a deploy, or a payment |
| exhibit a counterexample | repair the defect |

Each repository carries a `CLAIMS-MAP.md` naming the nearest claim family and **the specific step
not taken**. They are worth reading if you are evaluating the boundary rather than taking it on
trust.

## The one that sits closest to the line

[`proof-carrying-ci`](https://github.com/nickharris808/proof-carrying-ci) *looks* like a gate — it
is a CI check with a `fail-on` setting. It stays on the measuring side because:

> `fail-on` is a **reporting** control. Setting `fail-on: unverified` makes the *step* exit
> non-zero. Turning that into a blocked merge requires branch-protection rules that live in your
> repository settings and are not shipped, generated, or documented by the package.

Adding a mode that bound the refusal into a release record would cross the line. It is not there.

## Enforced by a rail, not by intention

`check_measure_only.py` runs over every artifact tagged CLEAN and fails the build if it finds an
actuation path — a deployment verb, a gate-shaped entry point, a provisioning call. It currently
passes at **zero exemptions**.

It also produces false positives, and how those are handled is the point. It flags any function
whose name *begins* with a gate verb. Two legitimate functions tripped it — `admitted_by` (which
counts states admitted *by* a policy) and `gate_count`. Both were **renamed** rather than
exempted:

> An exemption marker is a hole in a guard forever. A rename costs one line.

## If you want the enforcement side

The mechanisms that act on these verdicts — binding a partition key at the admission decision, the
compiled gate, the certificate-*issuing* faucet — are covered by filed patents and licensed
separately.

**Reading is free. Enforcing is licensed.**
