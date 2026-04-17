---
name: sample-dataset
description: Sample 20% of df_model for LLM agent discovery using Spark collect(). Only training labels are exposed — test labels are never seen by the agent, preventing supervised selection bias.
mode: organisational
---

Samples 20% of the PySpark df_model (seed=42), selects only `description` and `high_views`,
filters nulls, then calls .collect() to return a plain Python list of Spark Row objects.
No pandas conversion — data is accessed as row.description and row.high_views throughout.

This sample is the ONLY data the discovery agent ever sees.
Test labels are never passed to any LLM at any point in the pipeline.