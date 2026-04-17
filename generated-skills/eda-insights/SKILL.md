---
name: eda-insights
description: Recommend feature engineering steps for each feature based on provided statistics from exploratory data analysis.
mode: llm_driven
---
## Feature Recommender Skill

## Purpose
You receive a JSON object from an upstream Python step:
- per-column stats (including views if present)
- high_views label distribution
- correlations with high_views
- warnings.

The target variable is high_views. You are given a classification machine learning problem.

Your task is to recommend feature engineering steps for each feature, as well as class balancing if needed for the dataset.

## Feature Engineering Techniques

Here is a list of common feature engineering techniques and what they do:
- Null_handling
- Imputation: Fills in missing data values using statistical methods (like mean or median) or predictive algorithms. (imputation)
- One-Hot Encoding: Converts non-ordinal categorical variables into multiple binary (0 or 1) columns. (one_hot_encode)
- Label Encoding: Converts categorical variables into numerical integers to preserve a specific order or hierarchy. (label_encoding)
- Target Encoding: Replaces categorical values with the mean of the target variable for that specific category. (target_encoding)
- Standardization: Scales numerical features so they have a mean of 0 and a standard deviation of 1. (standardization)
- Normalization (Min-Max scaling): Scales numerical features to fit within a specific, restricted range, typically 0 to 1. (normalisation)
- Binning: Converts continuous numerical variables into discrete intervals, groups, or "bins". (binning)
- Log Transformation: Applies a logarithmic function to data to reduce extreme skewness and minimize the impact of outliers. (log_transform)
- Polynomial Features: Creates new features by squaring or multiplying existing features together to capture non-linear relationships. (polynomial)
- Date/Time Extraction: Splits single timestamp columns into separate, analyzable components like day, month, year, or day of the week. (datetime_extraction)
- Text Vectorization: Converts raw text strings into numerical representations based on word frequency or importance so algorithms can process them. (text_vectorisation)

## Correlation Analysis
Sort each feature under correlations in descending order of magnitude.
Label the top 10 features as "strong_correlation". Label remaining features as "weak_correlation".

## Class Imbalance Analysis
Examine the class distribution under label_distribution.
Compute: undersample_ratio = N_minority / N_majority

## Analysis Steps
1. Create a list of feature engineering steps for each feature in columns.
2. Identify features with strong and weak correlation with high_views under correlations.
3. Compute undersample_ratio using the class distribution under label_distribution.

## Output Format
You MUST return ONLY a valid JSON object — no markdown prose, no explanation, no headings.
Return exactly this structure:

```json
{
  "<column_name>": ["<action1>", "<action2>"],
  "<column_name>": ["<action1>"],
  "correlation": {
    "strong_correlation": [["<col1>", "<col2>", ...], <threshold>],
    "weak_correlation":   [["<col1>", "<col2>", ...], <threshold>]
  },
  "undersample_ratio": [<float>, "<implication string>", ["<technique1>", "<technique2>"]]
}