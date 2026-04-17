---
name: validate-features
description: Safety-check, syntax-test, and correlation-filter agent-generated Python functions before applying to the full dataset.
mode: organisational
---

Validates each LLM-generated Python function through four sequential gates:

1. Safety check   — rejects functions containing dangerous patterns
                    (os., subprocess, open(, __import__, eval(, exec(, requests, urllib, socket)
2. Syntax check   — ast.parse() to catch malformed or incomplete code before any execution
3. Execution test — exec() the function, run on 10 sample rows, assert numeric (int/float) output
4. Correlation    — Pearson r with high_views target on full sample; keeps only |r| >= 0.03

Functions that pass all four gates are returned as Python callables ready for dataset-wide application.
Functions that fail any gate are logged and discarded.