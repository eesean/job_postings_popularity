---
name: execute-features
description: Apply all validated agent-generated feature functions to the full dataset and update df_model in Spark.
mode: organisational
---

Wraps each validated Python function as a Spark UDF (DoubleType) and applies it via withColumn(),
then drops the `description` column. The full dataset is never collected to the driver.

This is the zero-LLM-cost execution stage — all functions are deterministic Python/regex.
The make_udf() factory pattern correctly captures each function in its own closure,
preventing the classic Python closure-in-loop bug.

After execution, df_model contains all original tabular columns plus the agent-generated feature columns,
with `description` removed (raw text is no longer needed in the model feature matrix).