---
name: evaluate-model
description: Train a given list of machine learning models from PySpark
  MLlib based on the optimised hyperparameters provided. Evaluate each model based on the
  prediction outputs provided for each model.
mode: organisational
---

# Evaluation Skill

## Purpose
You are provided with a dictionary containing pipelines for the following machine learning models:
- logistic_regression: ML pipeline for logistic regression
- naive_bayes: ML pipeline for naive bayes
- random_forest: ML pipeline for random forest
- decision_tree: ML pipeline for DecisionTreeClassifier

The target variable is `high_views`. You are given a classification machine learning problem and 2 dataframes, the former containing the training data and the latter containing the unseen test data.

From the dataset provided, split the data into training and test sets using the 80-20 ratio.

For each model provided, train the ML pipeline using the training data, before making predictions on the test data. Evaluate each model using the model's predictions.

## Evaluation Metrics
Compute for each model the following metrics:
1. Accuracy
2. Precision
3. Recall
4. F1-score
5. AUC

## Feature Importance
For each model, identify the top 5 features that contribute to the prediction of engagement level.
Identify the top 5 features that most frequently frequently appear among the top-ranked feature importances across all models.

## Output format
Return a JSON containing these keys:
- logistic_regression: Dictionary of evaluation metrics for logistic regression
- naive_bayes: Dictionary of evaluation metrics for naive bayes
- random_forest: Dictionary of evaluation metrics for random forest
- decision_tree: Dictionary of evaluation metrics for decision tree
- top_features: List of top 5 features across all models

CRITICAL OUTPUT CONSTRAINTS:
1. Return ONLY a JSON object.
2. DO NOT wrap the dictionary in markdown formatting (e.g., absolutely no ```python or ``` backticks).
3. DO NOT include any conversational filler, explanations, greetings, or preamble.