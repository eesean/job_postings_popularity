---
name: exploratory-data-analysis
description: Profile a PySpark DataFrame and flag data quality problems including
  missing values and class imbalance. Use this as the first step in any ML workflow.
mode: organisational
---

# Exploratory Data Analysis Skill

## Purpose
Examine the dataset before modelling. Surface problems early so that downstream
steps are not surprised.

## What to compute
- Row and column counts
- For each numeric column: mean, std, min, max, count of nulls
- For each categorical column: count of distinct values, count of nulls
- Label distribution: count and percentage for each class value
- Correlation analysis of each column with the target variable (high_views)

## Output format
Return a markdown file containing the following metadata:
- columns: per-column statistics
- label_distribution: count per class
- positive_rate: fraction of positive examples
- warnings: list of any problems found
- correlations: correlation results for each column with the target variable