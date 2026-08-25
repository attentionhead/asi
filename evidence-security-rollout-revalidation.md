# Security rollout validator revalidation evidence

## Scope

This is a Fix-lane measurement-integrity change. It changes no benchmark arm,
seed, threshold, output artifact, or promoted result.

## Reproduced defect

`SecurityRolloutStep` validates action, reward, state, termination flags, and
metadata during construction. Python's low-level `object.__setattr__` can still
replace fields on a frozen dataclass after construction. At trusted baseline
`c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`,
`validate_security_rollout` checked only the lengths of `state` and
`next_state`. It accepted a record whose reward had been replaced with NaN.
The same gap accepted a built-in integer in place of `SecurityAction`, an
integer termination flag, and a list in place of the canonical state tuple.

That makes the public validator weaker than the record it claims to validate.
A caller can receive a successful validation for a transition that cannot be
published as RFC-compliant JSON and does not satisfy the active-defense record
contract.

## Repair

At candidate `84359ed6deae2de787096b96581885dad533a51d`, the validator requires an
exact list or tuple of exact `SecurityRolloutStep` records and an exact
`SecurityFeatureSchema`. It reconstructs every step through the record's
constructor before checking feature widths. Any corrupted field now fails
with the indexed `invalid rollout step` error.

## Result

The four failing-test-first cases failed on baseline and pass on the candidate.
All 63 security tests, repository-wide Ruff, changed-source mypy, and
`git diff --check` pass. Live searches found no open issue or pull request for
`SecurityRolloutStep`, `validate_security_rollout`, or the security rollout
validator defect.
