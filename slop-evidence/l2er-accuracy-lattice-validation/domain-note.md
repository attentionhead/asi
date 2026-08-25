# Domain note: bind reported L2-ER accuracy to observed counts

The L2-ER development payload records a fixed number of observations,
`n_tasks * task_length`. Its builder derives mean online accuracy from per-task
accuracies, and each task accuracy is a correct-count divided by
`task_length`. Therefore the aggregate mean must represent an integer number
of correct predictions across the recorded observations.

Before this fix, the validator checked only that the mean was finite and in
`[0, 1]`. A self-consistent reconstructed receipt could therefore report an
impossible fractional correct count while keeping all resource totals and
configuration fields valid.

The fix multiplies the reported mean by the recorded observation count and
requires the result to be an integer within a small floating-point rounding
tolerance. A positive regression covers a builder-produced three-task mean
representing exactly 199 correct predictions out of 300 observations.

This is a development-record integrity repair. It changes no benchmark arm,
seed, threshold, output artifact, promotion state, or scientific claim.
