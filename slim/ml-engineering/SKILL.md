---
name: ml-engineering
version: 5.1.1-slim
description: "ML engineering: MLOps, serving, monitoring, LLMOps."
author: Dinoudon
tags: [mlops, machine-learning, llm]
related_skills: [architecture-design]
---

# ML Engineering

Quick reference for production ML systems.

## MLOps Maturity

L0: manual → L1: pipelines → L2: CI/CD+CT

## Key Tools

| Category | Tools |
|----------|-------|
| Tracking | MLflow, W&B |
| Pipelines | Airflow, Prefect |
| Serving | BentoML, Triton |
| Drift | Evidently AI, whylogs |
| Feature store | Feast |
| Vector DB | Chroma, Qdrant |

## Drift Types

Data drift: inputs changed → KS test
Concept drift: relationship changed → ADWIN
Model drift: predictions changed → monitor output

## LLMOps

RAG: embed → vector DB → retrieve → prompt
Fine-tuning: LoRA/QLoRA with HF TRL
Vector DBs: Chroma (local), Pinecone (managed)

## Checklist

- [ ] Pipeline reproducible
- [ ] Tracking + registry
- [ ] Drift monitoring
- [ ] Prompt versioning (LLM)
- [ ] Safety guardrails (LLM)

## Pitfalls

| Pitfall | Fix |
|---------|-----|
| No data versioning | DVC |
| Training-serving skew | Feature store |
| Notebook-only | Scripts |
