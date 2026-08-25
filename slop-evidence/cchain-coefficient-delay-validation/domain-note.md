# Domain note: pre-delay coefficient identity

The registered C-CHAIN mechanism starts its coefficient at exactly `1.0`.
After the two-update snapshot warmup, each active diagnostic adds one entry to
the coefficient window. The implementation updates the coefficient only when
that window count reaches the frozen delay of ten.

Therefore, when

`max(observations - snapshot_warmup_updates, 0) < coefficient_delay`,

the final coefficient must still equal `1.0` for every adaptive C-CHAIN arm.
The receipt already records all inputs needed to derive this invariant. The
fix checks it at validation time, preventing a self-consistent-looking record
from claiming coefficient adaptation before the mechanism can perform one.

This strengthens development-record integrity only. It does not authenticate
execution, change an arm or metric, or establish a performance result.
