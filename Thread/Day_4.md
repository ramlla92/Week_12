# Day 4


URL: https://x.com/akmel3784/status/2052833560688480499?s=20


1/ In benchmark evaluation, a confidence interval is only useful if the resampling method matches the experiment design.

If the same tasks appear in both baseline and trained conditions, the comparison is paired.

---

2/ Paired bootstrap resamples tasks while keeping each task’s baseline and trained scores together.

This preserves task difficulty and estimates the uncertainty of the actual model improvement.

---

3/ Unpaired bootstrap breaks that structure.

It can compare easy trained samples against hard baseline samples, adding noise and distorting the confidence interval.

---

4/ Key lesson:

Bootstrap is not just “run resampling 1,000 times.”

You must resample the correct statistical unit.

For shared-task model comparisons, that unit is the task pair.