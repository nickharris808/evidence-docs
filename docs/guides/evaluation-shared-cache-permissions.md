# Reviewing cache permission and residency outcomes

This is a bounded review and test proposal, not a report of a new executed experiment.

## Technical excerpt

For a cache test, separate permission eligibility from residency. A permitted request can miss because the block was evicted; a denied request must not hit merely because the block remains resident. Record tenant identity, rights revision, computation identity, residency, and observed reuse. Exercise revocation and restart separately. This is an evaluation design, not a completed serving-engine benchmark. Public formal scope: https://verifycorelabs.com/theorems/tn-bits/

## Further reading

Read the full [VerifyCore Labs article](https://verifycorelabs.com/blog/shared-cache-permissions/) for the proposed method and its evidence limits. These notes do not expand what any tool in this documentation proves.

[Public evidence and scope](https://verifycorelabs.com/theorems/tn-bits/)

Attribution: VerifyCore Labs.
