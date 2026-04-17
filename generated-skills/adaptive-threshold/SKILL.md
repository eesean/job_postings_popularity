---
name: adaptive-threshold-median
description: Finds the best single integer threshold for binarising the
  views column into high_views, using the median as a starting point.
mode: llm_driven
---

# Adaptive Threshold Agent

## Purpose
You are given summary statistics for the views column in a LinkedIn job postings
dataset. Views is a low-cardinality integer. Your task is to find the best
single integer threshold for binarising views into:
- high_views = 1 if views > threshold
- high_views = 0 if views <= threshold

## Input
JSON with keys:
- median: float
- mean: float
- skewness: float
- class_dist_at_median: {low: int, high: int, low_pct: float, high_pct: float}
- value_counts: list of {views: int, count: int, pct: float} for top 20 values

## Task
1. Start from the median as a baseline candidate.
2. Evaluate nearby integers (median - 2 to median + 2) by reasoning about
   the class distribution each would produce based on the value_counts provided.
3. Avoid thresholds that fall on a spike value — if a single views value
   accounts for >15% of all rows, the threshold should not sit at that value
   as it creates an arbitrary boundary within a dense cluster.
4. Prioritise class balance — the closer to 50/50 the better.
5. Recommend exactly one integer threshold and justify your choice.

## Output format (JSON only, no extra text)
{
  "recommended_threshold": <int>,
  "high_views_rule": "views > <int>",
  "class_dist": {"low": <int>, "high": <int>, "low_pct": <float>, "high_pct": <float>},
  "median_is_spike": <bool>,
  "justification": "<string>"
}