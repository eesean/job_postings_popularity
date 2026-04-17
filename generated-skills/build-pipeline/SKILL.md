---
name: build-pipeline
description: Build pipelines for a given list of machine learning models from PySpark
  MLlib. Returns machine learning pipelines for each model.
mode: llm_driven
---

# Model Pipeline Skill

## Purpose
You are given the following machine learning models:
- Logistic Regression
- Random Forest
- DecisionTreeClassifier
- Naive Bayes

The target variable is `high_views`. You are given a classification machine learning problem.

Using the dataset schema and categorical feature cardinality provided, convert the features into a vector assembler for training of machine learning models. The dataset includes a pre-computed BERT embedding feature.

## CRITICAL API RULES
1. **DATA TYPE SEPARATION:** NEVER apply `StringIndexer`, `OneHotEncoder`, `FeatureHasher`, `RegexTokenizer`, or `CountVectorizer` to numeric columns (e.g., Integer, Double, Float) OR vector/array columns. These text transformations MUST ONLY be applied to **string** columns.
2. Whenever you use `VectorAssembler`, the `inputCols` parameter MUST be a hardcoded list of **strings** representing the exact column names. NEVER pass pipeline stage objects or list slices.
3. The final `VectorAssembler` for **EVERY SINGLE MODEL** must include four things: the SCALED numeric features, the converted BERT embedding vector, the output from the OneHotEncoder, AND the output from the high-cardinality handler. Do not leave any out.
4. **UNSEEN LABELS:** You MUST explicitly set `handleInvalid="keep"` for every single `StringIndexer` instance you create.
5. **NAIVE BAYES NEGATIVE VALUES:** BERT embeddings contain negative values. PySpark's default Multinomial Naive Bayes will crash. You MUST set `modelType="gaussian"` when instantiating the NaiveBayes estimator.
6. **FINAL FEATURE COLUMN NAME:** The final feature vector column produced by the final `VectorAssembler` MUST have its `outputCol` set exactly to `"features"`. The downstream models must use `"features"` as their input.
7. **TARGET VARIABLE MAPPING:** PySpark models default to looking for a column named `"label"`. Because our target variable is `"high_views"`, you MUST explicitly set `labelCol="high_views"` when instantiating EVERY single machine learning estimator (Logistic Regression, Random Forest, Decision Tree, and Naive Bayes).

## Pipeline steps
1. Apply StringIndexer to each categorical (string) feature.
2. Apply OneHotEncoder to indexed categoricals with low cardinality.
3. Handle high-cardinality **STRING** features (defined as > 100 unique values):
   - For Logistic Regression, Random Forest, and DecisionTreeClassifier: Apply `FeatureHasher`. Specify `numFeatures` to a reasonable power of 2 (e.g., 2^12).
   - For Naive Bayes: Do NOT use `FeatureHasher`. MUST DO THIS IN TWO STEPS: First, apply `RegexTokenizer` (pattern=r"\W+"). **Use a raw Python string (the 'r' prefix) to avoid syntax warnings.** Second, apply `CountVectorizer` (with a limited `vocabSize`).
4. Numeric Vectorization & Scaling (FOR ALL MODELS):
   - First, assemble ONLY the raw continuous numeric columns into an intermediate vector named `"raw_numeric_features"` using `VectorAssembler`.
   - Second, apply `StandardScaler` (with `withMean=False`) to `"raw_numeric_features"`. The `outputCol` MUST be `"scaled_numeric_features"`.
5. Final Feature Assembly (FOR ALL MODELS):
   - Assemble all engineered features into a single, final vector using a new `VectorAssembler`.
   - `inputCols` MUST contain: `"scaled_numeric_features"` + the converted BERT vector column (`description_embedding_vec`) + OneHotEncoder output columns + Hasher/Vectorizer output columns. **(DO NOT include the raw numeric columns or the original `description_embedding` column).**
   - **CRITICAL NAMING:** The `outputCol` for this final assembler MUST be exactly `"features"`.
6. Construct separate MLlib Pipelines for each model.
   - **CRITICAL:** Do **NOT** put `array_to_vector` or any column expressions in the pipeline stages. Your `stages=[...]` list must ONLY contain valid PySpark Transformers and Estimators (`StringIndexer`, `OneHotEncoder`, Hashers/Vectorizers, Assemblers, Scalers, and the Model).
   - **CRITICAL MODEL INSTANTIATION:** When instantiating the NaiveBayes model, you MUST explicitly pass `modelType="gaussian"`. Furthermore, for ALL models, you MUST explicitly pass `labelCol="high_views"`.

## Output format
Write pure, executable Python code that instantiates the actual PySpark MLlib objects.
The final step in your script MUST be a dictionary assigned to a variable named `pipelines_dict` containing these keys:
- logistic_regression: PySpark Pipeline object for logistic regression
- naive_bayes: PySpark Pipeline object for naive bayes
- random_forest: PySpark Pipeline object for random forest
- decision_tree: PySpark Pipeline object for DecisionTreeClassifier

CRITICAL OUTPUT CONSTRAINTS:
1. Return ONLY pure Python code. Do NOT return JSON.
2. You MUST include all necessary PySpark imports at the top of the code.
3. DO NOT wrap the code in markdown formatting (e.g., absolutely no ```python or ``` backticks).
4. DO NOT include any conversational filler, explanations, greetings, or preamble. Just the raw Python script.