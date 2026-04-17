---
name: explain-results
description: Write a plain-language business report from ML evaluation metrics.
  Avoids jargon and explains results in terms of candidate engagement for job postings.
mode: llm_driven
---

# Business Results Explanation Skill

## When to use
- After model training, when you need to communicate results to a non-technical audience
- User wants a summary that avoids statistical jargon

## How to respond
You will receive a JSON object containing evaluation metrics for each model, as well as top 5 features that influence the predictions across all models.
Write a clear, concise report for a business audience.
The report should:
1. Name the best-performing model and explain in plain terms why it was selected.
2. Explain the underlying reasons why these top 5 features are the strongest drivers of candidate engagement, and suggest how to leverage them to improve job postings.
3. Add one limitation of the best-performing model.

## Formatting rules
- Write in prose paragraphs, no bullet points
- Avoid all statistical abbreviations (write "precision" not "prec", and so on)
- Keep the report under 350 words
- Do not use em dashes