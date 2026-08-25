# JEPA transfer receipt integrity note

On trusted `main` at `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`, the native JEPA transfer
runner produced a valid tiny result. After the first frozen arm's `return_sum` was changed to
NaN through `object.__setattr__`, `dataclasses.replace(result)` reconstructed and accepted the
outer `JEPATransferResult`.

The same boundary accepted three other invalid nested records: nonzero imported-pretraining
bytes, a timing receipt whose clock claimed benchmark scope, and a malformed development
identity digest. Each nested frozen dataclass validates these values in its own constructor, but
the outer constructor previously trusted instances after their original construction.

Candidate `f11a94308fb281620f46f59c1945da970a79a5fd` reconstructs the protocol, development
identity, every arm receipt, every resource receipt, and every timing receipt before checking the
matched roster and resource schedule. This makes the outer measurement boundary fail closed on
post-construction corruption and stores canonical rebuilt nested records.

This is a development-only measurement-integrity fix. It changes no JEPA arm, seed, threshold,
reward, benchmark result, external qualification gate, or scientific-promotion policy.

The complete open-PR file inventory covered 306 heads. Open PR #2273 touches the same source
file only to add an external-catalog CLI path; it does not change the native result classes,
nested receipt validation, or the existing result tests. Exact open issue and pull-request
searches found no semantic owner for this defect.
