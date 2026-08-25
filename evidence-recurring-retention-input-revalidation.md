# Recurring-IPMNIST report input revalidation

This evidence records a development-only measurement-integrity fix. It does
not establish a retention result, promote an artifact, or change a benchmark
arm, seed, threshold, or stored output.

At exact baseline `c9aba7b54dedd647f8bd5f5c7bf6780b1413b676`,
`build_recurring_ipmnist_retention_report` accepted frozen input records
without rerunning their constructor invariants. After a valid
`RecurringIPMNISTTrace` was mutated through `object.__setattr__`, a binary
predict-before-update outcome could be replaced by `0.5`; the builder emitted
a plausible report instead of rejecting the non-binary measurement.

The same boundary trusted nested protocol phases and sentinel snapshots. The
candidate at `fad98ecdc320dcd74b168b73eb985e0978b0182e` revalidates every
phase, sentinel binding, protocol, trace, and snapshot immediately before it
derives or hashes report metrics. Regressions cover fractional accuracy,
boolean-as-index corruption, and integer-as-correctness corruption.

The baseline regression failed because no `ValueError` was raised. The
candidate passed 21 focused tests, repository-wide Ruff, changed-source strict
mypy, and `git diff --check`. Repository-wide mypy retains the unchanged
current-main macOS `os.O_TMPFILE` stub error and is not claimed green.

Exact semantic searches found no open issue or pull request for this report
boundary defect. Open PRs #2272 and #2275 touch the module only as part of the
separate repository-wide NumPy integer-alias gate.
