# World-model smoke result nested-arm integrity

Exact trusted `main` accepted a `WorldModelSmokeResult` whose frozen nested arm had been
mutated after its own constructor validation. In the minimal reproduction, replacing the first
arm's nonnegative prequential losses with negative values and its persistent-byte count with
zero still allowed the outer result to be reconstructed successfully. The boundary also
accepted a metric space outside the frozen arm roster and arm step counts that disagreed with
the outer result.

The result object is the public development-smoke measurement record. If it trusts nested
frozen values merely because their Python types and arm identifiers look right, corrupted or
mismatched measurements can be published as structurally valid records.

The candidate re-runs the nested arm constructor validation, binds each frozen arm identifier
to its required metric space, and requires every arm to use the outer result's step count.
Failing-test-first regressions cover corrupted nested values, metric substitution, and outer-arm
step disagreement.

This repair is intentionally narrow. It is development-only and does not change model
behavior, benchmark arms, seeds, thresholds, artifacts, or evidence-promotion status. Passing
the regression panel is implementation evidence, not a scientific or performance claim.

Base: `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`

Candidate: `161b9e2d3cc4bfc76a12435bfc6b0526823e711c`
