---
name: ml-engineering
version: 5.1.1-moderate
description: "ML engineering: MLOps, pipelines, serving, monitoring, LLMOps."
author: Dinoudon
tags: [mlops, machine-learning, model-serving, llm]
related_skills: [architecture-design, sdlc-observability]
---

# ML Engineering

Balanced guide to MLOps, model lifecycle, and production ML.

## Steps

### 1. MLOps Maturity (Google)

| Level | Training | Deploy | Monitoring |
|-------|----------|--------|------------|
| L0 | Manual | Manual | None |
| L1 | Pipelines | Auto | Basic |
| L2 | CI/CD+CT | Canary/A/B | Auto-retrain |

### 2. Pipeline Tools

| Tool | Strengths |
|------|-----------|
| MLflow | Simple tracking, registry |
| Airflow | Mature orchestrator |
| Prefect | Modern DX |
| Dagster | Data-aware |

### 3. Feature Stores

Feast (open-source): online (Redis) + offline (S3) stores. Point-in-time joins prevent leakage.

### 4. Model Serving

| Tool | Best For |
|------|----------|
| BentoML | Best DX, fast prototyping |
| Triton | Multi-framework, GPU |
| Seldon | K8s-native, explainability |

### 5. Drift Monitoring

| Type | Detection |
|------|-----------|
| Data drift | KS test, PSI |
| Concept drift | ADWIN, DDM |
| Model drift | Output distribution |

Tools: Evidently AI, NannyML, whylogs

### 6. Experiment Tracking

MLflow (self-hosted) vs W&B (cloud, best UX)

### 7. LLMOps

RAG: embed docs → vector DB → retrieve → inject into prompt
Vector DBs: Chroma (local), Pinecone (managed), Qdrant (perf)
Fine-tuning: LoRA/QLoRA with HuggingFace TRL

---

## Checklist

- [ ] Pipeline reproducible
- [ ] Experiment tracking configured
- [ ] Model registry with stages
- [ ] Drift monitoring + alerts
- [ ] Prompt versioning (LLM)
- [ ] Safety guardrails (LLM)

## Pitfalls

| Pitfall | Fix |
|---------|-----|
| No data versioning | Use DVC |
| Training-serving skew | Feature store |
| No drift monitoring | Evidently AI |
| Notebook-only | Scripts/pipelines |
