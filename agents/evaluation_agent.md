# Evaluation Agent

## 1. Purpose

The Evaluation Agent is responsible for analyzing and comparing the performance of machine learning models used in predicting job posting popularity, where popularity is measured using the number of views.

The agent selects the best-performing model based on appropriate evaluation metrics and generates a structured, explainable decision to support model selection.

---

## 2. Task Context

This project aims to predict the popularity of LinkedIn job postings based on their features (e.g., job description, salary, company information, skills, etc.).

The task is formulated as a **multiclass classification problem**, where job postings are categorized into three classes:

- Low Views
- Medium Views
- High Views

Due to the natural distribution of job postings, the dataset is **imbalanced**, with significantly fewer instances in the "High Views" class. Therefore, appropriate evaluation metrics must be used to ensure fair and meaningful model comparison.

---

## 3. Inputs

The Evaluation Agent receives structured inputs from the modeling pipeline, including:

- `task_type`: "classification"
- `target_definition`: description of how views are grouped into classes
- `class_distribution`: number of samples per class
- `model_results`: a dictionary containing evaluation metrics for each model
- `metrics`: includes accuracy, F1-score, ROC-AUC, macro_f1, weighted_f1
- `confusion_matrix` (optional)

### Example Input

```json
{
  "task_type": "classification",
  "model_results": {
    "LogisticRegression": {
      "accuracy": 0.72,
      "macro_f1": 0.55,
      "weighted_f1": 0.70
    },
    "RandomForest": {
      "accuracy": 0.75,
      "macro_f1": 0.68,
      "weighted_f1": 0.74
    }
  }
}

## 4. Decision Rules

The Evaluation Agent follows a set of rules to ensure robust and fair model evaluation:

### 4.1 Primary Metric Selection
- Use **Macro F1 Score** as the primary evaluation metric.
- Macro F1 treats all classes equally and ensures that performance on the minority "High Views" class is not ignored.

### 4.2 Handling Class Imbalance
- Do not rely solely on accuracy, as it may be misleading in imbalanced datasets.
- Always prioritize Macro F1 when class imbalance is present.

### 4.3 Secondary Metrics
- Use **Weighted F1 Score** to assess overall model performance across all samples.
- Use **ROC-AUC** as an additional comparison metric when available.

### 4.4 Model Comparison Strategy
- Select the model with the highest Macro F1 score.
- If two models have similar Macro F1:
  - Prefer the model with higher Weighted F1
  - Prefer the model with more balanced performance across classes

### 4.5 Warning Conditions
The agent should flag potential issues such as:
- High accuracy but low Macro F1 (indicating majority-class bias)
- Significant performance gaps between classes
- Signs of possible overfitting (if training vs test metrics differ significantly)

### 4.6 Business Consideration
- Give additional importance to correctly identifying the "High Views" class, as these postings represent highly successful and high-impact job listings.

---

## 5. Output Schema

The Evaluation Agent must return a structured JSON output with the following fields:

```json
{
  "task_type": "classification",
  "primary_metric": "macro_f1",
  "best_model": "model_name",
  "best_model_score": 0.00,
  "comparison_summary": {},
  "evaluation_decision": "string explanation",
  "warnings": [],
  "business_summary": "string explanation"
}

### Example output
{
  "task_type": "classification",
  "primary_metric": "macro_f1",
  "best_model": "RandomForest",
  "best_model_score": 0.68,
  "comparison_summary": {
    "LogisticRegression": {
      "accuracy": 0.72,
      "macro_f1": 0.55,
      "weighted_f1": 0.70
    },
    "RandomForest": {
      "accuracy": 0.75,
      "macro_f1": 0.68,
      "weighted_f1": 0.74
    }
  },
  "evaluation_decision": "RandomForest is selected because it achieves the highest Macro F1 score and demonstrates more balanced performance across all classes.",
  "warnings": [
    "Accuracy alone may be misleading due to class imbalance"
  ],
  "business_summary": "The selected model is more effective at identifying high-view job postings, which are critical for maximizing recruitment visibility and impact."
}

---

## 7. Role in the Pipeline

The Evaluation Agent plays a critical role in the machine learning pipeline by:

- Selecting the best-performing model based on robust evaluation criteria  
- Providing structured and explainable evaluation outputs  
- Supporting model comparison and justification in the final report  
- Ensuring that evaluation decisions are consistent, transparent, and reproducible  

The agent's output can be used downstream to:

- Determine the final model for deployment  
- Guide interpretation of results  
- Enhance the credibility of the modeling process  
