---
name: ml-engineering
version: 5.1.1
description: "ML engineering: MLOps maturity, pipelines, feature stores, model serving, monitoring, LLMOps."
author: Dinoudon
tags: [mlops, machine-learning, model-serving, feature-store, llm, rag, experiment-tracking]
related_skills: [architecture-design, sdlc-deployment, sdlc-observability, sdlc-testing-qa]
---

# ML Engineering

Comprehensive guide to MLOps, model lifecycle, and production ML systems.

## When to Use

- Building ML pipelines for training and serving
- Setting up experiment tracking and model registry
- Monitoring models in production (drift detection)
- Implementing RAG or LLM-based applications
- Scaling ML infrastructure

## Steps

### 1. MLOps Maturity Model (Google)

| Level | Training | Deployment | Monitoring |
|-------|----------|------------|------------|
| L0: Manual | Notebook → handoff | Manual deploy | None |
| L1: Pipeline | Automated pipelines (CT) | Automated deploy | Basic metrics |
| L2: CI/CD+CT | Full CI/CD/CT | Canary/A/B testing | Automated retraining |

Most orgs at L0-L1. Jump to L2 requires org change, not just tooling.

Source: https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning

---

### 2. ML Pipeline Tools

| Tool | Type | Strengths | Weaknesses |
|------|------|-----------|------------|
| Kubeflow | Full platform | K8s-native, scalable | Complex setup |
| MLflow | Tracking+registry | Simple, Databricks-native | Not orchestration |
| Airflow | Orchestrator | Mature, 1000+ operators | Verbose, ops-heavy |
| Prefect | Orchestrator | Modern DX, dynamic DAGs | Less mature |
| Dagster | Orchestrator | Data-aware, typed | Steeper learning |

**MLflow Tracking:**
```python
import mlflow

mlflow.set_experiment("my_experiment")

with mlflow.start_run():
    mlflow.log_param("learning_rate", 0.01)
    mlflow.log_param("n_estimators", 100)
    
    model = train_model(X_train, y_train)
    accuracy = evaluate(model, X_test, y_test)
    
    mlflow.log_metric("accuracy", accuracy)
    mlflow.sklearn.log_model(model, "model")
```

**Airflow DAG:**
```python
from airflow import DAG
from airflow.operators.python import PythonOperator

with DAG('ml_pipeline', schedule='@daily') as dag:
    extract = PythonOperator(task_id='extract', python_callable=extract_data)
    train = PythonOperator(task_id='train', python_callable=train_model)
    evaluate = PythonOperator(task_id='evaluate', python_callable=evaluate_model)
    deploy = PythonOperator(task_id='deploy', python_callable=deploy_model)
    
    extract >> train >> evaluate >> deploy
```

---

### 3. Feature Stores

| Tool | Type | Online Store | Offline Store |
|------|------|--------------|---------------|
| Feast | Open-source | Redis, DynamoDB | S3, BigQuery, Parquet |
| Tecton | Managed | Low-latency | S3, Snowflake |
| Hopsworks | Open-source | MySQL | HopsFS |

**Feast:**
```python
from feast import FeatureStore

fs = FeatureStore(repo_path=".")

# Get online features for serving
features = fs.get_online_features(
    features=[
        "user_features:avg_order_value",
        "user_features:order_count",
    ],
    entity_rows=[{"user_id": "u123"}],
).to_dict()

# Get historical features for training
training_df = fs.get_historical_features(
    entity_df=entity_df,
    features=[
        "user_features:avg_order_value",
        "user_features:order_count",
    ],
).to_df()
```

Key concept: Point-in-time joins prevent data leakage.

---

### 4. Model Serving

| Tool | Framework | Strengths | Best For |
|------|-----------|-----------|----------|
| TorchServe | PyTorch | Official, REST/gRPC | PyTorch shops |
| TF Serving | TensorFlow | Optimized, canary | TF shops |
| Triton | Multi-framework | GPU-optimized, ensembles | Multi-framework |
| BentoML | Any | Best DX, easy packaging | Fast prototyping |
| Seldon | Any | K8s-native, explainability | Production K8s |

**BentoML:**
```python
import bentoml

# Save model
bentoml.sklearn.save_model("iris_classifier", model)

# Create service
# service.py
import bentoml

@bentoml.service
class IrisClassifier:
    model = bentoml.sklearn.load_model("iris_classifier:latest")
    
    @bentoml.api
    def predict(self, input_data: list) -> list:
        return self.model.predict([input_data]).tolist()
```

---

### 5. ML Monitoring (Drift Detection)

| Drift Type | What Changes | Detection Methods |
|------------|--------------|-------------------|
| Data drift | Input distributions | KS test, PSI, Wasserstein |
| Concept drift | Feature→target relationship | ADWIN, DDM |
| Model drift | Prediction distribution | Monitor output dist |

**Evidently AI:**
```python
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=train_df, current_data=production_df)
report.save_html("drift_report.html")
```

**Key tools:**
| Tool | Type | Strengths |
|------|------|-----------|
| Evidently AI | Open-source | Drift reports, dashboards |
| NannyML | Open-source | Estimate perf without labels |
| whylogs | Open-source | Lightweight profiling |
| Arize AI | SaaS | Production ML observability |

---

### 6. Experiment Tracking

| Tool | Hosting | Strengths |
|------|---------|-----------|
| MLflow | Self-hosted | Open-source, Databricks-native |
| W&B | Cloud | Best UX, visualization |
| Neptune | Cloud | Enterprise, large teams |

**W&B:**
```python
import wandb

wandb.init(project="my-project", config={"lr": 0.01, "epochs": 10})

for epoch in range(10):
    loss = train_epoch()
    wandb.log({"loss": loss, "epoch": epoch})

wandb.finish()
```

---

### 7. Model Registry

**MLflow Model Registry:**
```python
# Register model
mlflow.register_model("runs:/abc123/model", "my_model")

# Transition stages
client.transition_model_version_stage(
    name="my_model",
    version=1,
    stage="Production"
)
```

Stages: None → Staging → Production → Archived

**DVC (Data Version Control):**
```bash
dvc add data/training_data.parquet
git add data/training_data.parquet.dvc
git commit -m "Update training data"
dvc push
```

---

### 8. LLMOps

**RAG Pattern:**
```python
from langchain.vectorstores import Chroma
from langchain.embeddings import OpenAIEmbeddings
from langchain.chains import RetrievalQA

# Build vector store
vectorstore = Chroma.from_documents(docs, OpenAIEmbeddings())

# Create RAG chain
qa_chain = RetrievalQA.from_chain_type(
    llm=ChatOpenAI(model="gpt-4"),
    retriever=vectorstore.as_retriever(search_kwargs={"k": 5}),
)

answer = qa_chain.run("What is the refund policy?")
```

**Vector DB Comparison:**
| DB | Type | Strengths |
|----|------|-----------|
| Chroma | Embedded | Simple, local dev |
| Pinecone | Managed | Scalable, serverless |
| Weaviate | Open-source | Hybrid search |
| Qdrant | Open-source | High performance |
| pgvector | Extension | PostgreSQL integration |

**Fine-tuning (LoRA/QLoRA):**
```python
from peft import LoraConfig, get_peft_model

config = LoraConfig(
    r=16,
    lora_alpha=32,
    target_modules=["q_proj", "v_proj"],
    lora_dropout=0.05,
)

model = get_peft_model(base_model, config)
# Train with HuggingFace TRL, Axolotl, or Unsloth
```

---

### 9. ML Testing

| Test Type | What to Test | Tools |
|-----------|--------------|-------|
| Data validation | Schema, nulls, ranges | Great Expectations, Pandera |
| Model testing | Accuracy, fairness, bias | pytest, custom |
| Integration | End-to-end pipeline | pytest, Testcontainers |
| Performance | Latency, throughput | Locust, k6 |

---

### 10. ML Deployment Patterns

| Pattern | Risk | Speed | Use Case |
|---------|------|-------|----------|
| Shadow | None | Fast | Validate new model |
| Canary | Low | Medium | Gradual rollout |
| A/B | Medium | Slow | Measure business impact |
| Blue-green | None | Instant | Full swap |

---

## Checklist

### Pipeline
- [ ] Pipeline reproducible (pinned deps, versioned data)
- [ ] Experiment tracking configured
- [ ] Model registry with stage transitions
- [ ] CI/CD for model training and deployment

### Serving
- [ ] Model versioning in production
- [ ] Health checks and readiness probes
- [ ] Latency and throughput SLAs defined
- [ ] Fallback model for failures

### Monitoring
- [ ] Data drift detection configured
- [ ] Model performance tracked
- [ ] Alerts on drift thresholds
- [ ] Retraining triggers automated

### LLMOps
- [ ] Prompt versioning
- [ ] Token usage monitoring
- [ ] Safety guardrails (toxicity, PII)
- [ ] Evaluation metrics defined

---

## Pitfalls

| Pitfall | Impact | Fix |
|---------|--------|-----|
| No data versioning | Can't reproduce results | Use DVC or lakeFS |
| Training-serving skew | Model fails in prod | Feature store point-in-time joins |
| No drift monitoring | Silent model degradation | Evidently AI / whylogs |
| Notebook-only workflow | Not reproducible | Convert to scripts/pipelines |
| No model registry | Version chaos | MLflow or W&B registry |
| Ignoring data quality | Garbage in, garbage out | Great Expectations validation |

---

## Quick Reference

**Key URLs:**
- MLOps maturity: https://cloud.google.com/architecture/mlops-continuous-delivery-and-automation-pipelines-in-machine-learning
- MLflow: https://mlflow.org/docs/
- Kubeflow: https://www.kubeflow.org/docs/
- Feast: https://docs.feast.dev/
- BentoML: https://docs.bentoml.com/
- Evidently AI: https://docs.evidentlyai.com/
- LangChain: https://docs.langchain.com/
- HuggingFace: https://huggingface.co/docs/
