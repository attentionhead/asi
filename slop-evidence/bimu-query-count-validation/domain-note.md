# Domain note: zero threshold determines the BiMU query schedule

BiMU queries a label when `variation_ratio >= query_threshold`. The variation ratio is a
nonnegative fraction, so a configured threshold of exactly zero deterministically queries every
presented observation. The compact receipt already binds the threshold, observation horizon,
label-query count, optimizer-update count, and model-query count.

Before this fix, the validator checked only that label queries were within the observation
horizon and that model-query accounting agreed with the reported query count. A producer could
therefore lower the label-query count, update count, and derived model-query count together and
still pass validation, yielding an impossible record for the bound zero-threshold protocol.

The fix adds the missing cross-field invariant: when `query_threshold == 0.0`, the label-query
count must equal the full observation horizon. It does not require optimizer updates to equal
queries because the runner deliberately counts only nonzero-gradient updates. Nonzero-threshold
configurations retain their existing bounded-count contract.

This is a receipt-integrity fix, not a performance result, paper-comparability claim, evidence
promotion, or execution attestation.
