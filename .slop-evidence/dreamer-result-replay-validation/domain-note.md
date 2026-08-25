# Dreamer result replay note

`DevelopmentResult` is the host-side measurement record for the permanently nonpromoting
Dreamer-family development lane. Its validator checked frozen configuration, source identity,
nested types, and resource formulas, but it did not bind reported metrics to the deterministic
execution that produced them.

On trusted `main`, the validator accepted an existing result after the guarded-imagination
arm's task returns were replaced with `(1000000.0, 1000000.0, 1000000.0)`. It also accepted
final action values replaced with `(1000000.0, -1000000.0)`. The environment emits only
`-1.0` or `1.0` rewards, so these records cannot come from the declared one-step task
schedule.

The candidate replays each frozen arm from the recorded seed and configuration. It compares the
complete arm record after zeroing only `elapsed_ns`, which is explicitly telemetry-only. Any
change to task returns, final action values, deterministic counters, persistent bytes, or replay
accounting now fails validation.

This change does not alter an arm, task schedule, seed, threshold, metric, result, or promotion
policy. It only makes the existing development measurement record fail closed.
