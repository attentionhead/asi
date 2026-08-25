# Low-cost result trust-boundary note

`LowCostResult` is the host-side development measurement record for the issue-1566 low-cost
activation comparator. On trusted `main`, it checked only schema, profile, arm count/order, the
nonpromotion flag, and the catalog. It trusted already-constructed nested arm objects and did not
bind the seed or dataset digest.

That allowed reconstructed records to carry non-finite or out-of-range metrics, mismatched curve
lengths, an enabled mechanism flag on the frozen control arm, a built-in boolean seed, an
out-of-schedule seed, or a malformed dataset identity. Those values can change or misattribute a
development comparison without changing its declared roster.

The candidate makes construction the validation boundary. It requires the exact frozen seed and
lowercase SHA-256 dataset identity, exact catalog and arm types, re-runs every nested arm
validator, binds mechanism flags and preactivation widths to the frozen roster, binds every curve
to the profile task count, and checks the frozen update schedule. It also bounds probability,
loss, rank, and curve-shape values.

This remains a permanently nonpromoting development comparator. The change does not alter an
arm, seed schedule, threshold, result, query-accounting formula, or evidence-promotion policy.
