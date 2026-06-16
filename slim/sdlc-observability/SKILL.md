---
name: sdlc-observability
description: "Observability: OpenTelemetry 2024, GenAI semantic conventions, eBPF (Cilium/Hubble/Tetragon), sidecar-less mesh, profiling signal, structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM stack, distributed tracing, cost optimization, serverless observability, LLM/AI observability, edge observability, OTel Collector deployment patterns, microservices golden signals, log aggregation, metrics aggregation, alert design patterns, observability maturity model, ML model monitoring, AI agent observability, MLOps observability."
version: 4.8.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, observability, opentelemetry, prometheus, grafana, loki, jaeger, sli, slo, error-budget, tracing, logging, sre, ebpf, cilium, genai, profiling, serverless, lambda, edge, llm, ai, collector, golden-signals, elk, clickhouse, alerting, maturity-model, ml-monitoring, drift-detection, agent-observability, mlops]
    related_skills: [sdlc-deployment, sdlc-cicd-pipeline, sdlc-testing-qa]
---

---
name: sdlc-observability
description: "Observability: OpenTelemetry 2024, GenAI semantic conventions, eBPF (Cilium/Hubble/Tetragon), sidecar-less mesh, profiling signal, structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM stack, distributed tracing, cost optimization, serverless observability, LLM/AI observability, edge observability, OTel Collector deployment patterns, microservices golden signals, log aggregation, metrics aggregation, alert design patterns, 
## When to Use
Trigger when user:
- Instruments services with OpenTelemetry or LLM/AI services
- Sets up dashboards, alerts, or SLOs
- Configures structured logging or distributed tracing
- Configures eBPF-based observability (Cilium, Tetragon, Pixie)
- Migrates from Envoy sidecars to sidecar-less mesh
- Deploys OTel Collector (sidecar, daemonset, gateway)
- Designs observability for microservices
- Chooses log aggregation stack (ELK, Loki, ClickHouse)
- Assesses observability maturity
- Compares LLM observability platforms
- Monitors ML model drift and degradation
- Instruments AI agents for cost/latency/reasoning visibility
- Tracks ML experiments and pipelines
## Step 1: OpenTelemetry (OTEL)
Source: https://opentelemetry.io/
CNCF observability framework. Standardizes telemetry collection. Vendor-neutral.
**Auto-instrumentation (zero-code):**
```bash
# Python
opentelemetry-instrument python app.py

# Java
java -javaagent:opentelemetry-javaagent.jar -jar app.jar

# Node.js
node --require @opentelemetry/auto-instrumentations-node app.js
```
## Step 2: Three Pillars — Logs, Metrics, Traces
| Pillar | What | Why | Example |
|--------|------|-----|---------|
| **Metrics** | Numerical measurements over time | WHAT is happening | requests_total, duration_seconds |
| **Traces** | End-to-end request path | WHERE the problem is | trace_id → [span1 → span2 → span3] |
| **Logs** | Timestamped text records | WHY it happened | "Connection refused to db-prod:5432" |
**Metric Types:** Counter (monotonically increasing), Gauge (point-in-time), Histogram (distribution)
**Trace concepts:** Trace = tree of spans. Each span has trace_id, span_id, parent_span_id, name, start_time, duration, attributes, status. Context propagation via W3C TraceContext headers.
## Step 3: Structured Logging
**Bad (unstructured):**
```
2024-01-15 ERROR: Connection refused to db-prod:5432
```
## Step 4: SLIs / SLOs / SLAs
**Prometheus SLI queries:**
```promql
# Availability SLI
sum(rate(http_requests_total{code!~"5.."}[30d]))
/
sum(rate(http_requests_total[30d]))

# Latency SLI (p99 < 250ms)
sum(rate(http_request_duration_seconds_bucket{le="0.25"}[30d]))
/
sum(rate(http_request_duration_seconds_count[30d]))
```
## Step 5: Error Budgets
**Error budget = 1 - SLO target**
| SLO | Error Budget | Monthly Downtime |
|-----|-------------|------------------|
| 99.9% | 0.1% | 43.2 minutes |
| 99.95% | 0.05% | 21.6 minutes |
| 99.99% | 0.01% | 4.32 minutes |
**Budget-based decisions:**
- Budget > 50%: push features faster
- Budget 20-50%: normal pace
- Budget < 20%: slow down, focus on reliability
- Budget exhausted: freeze deployments, fix reliability
```yaml
# Fast burn: 14.4x over 1h (2% budget in 1h) → page
- alert: FastBurnBudget
  expr: |
    (
      sum(rate(http_requests_total{code=~"5.."}[1h])) /
      sum(rate(http_requests_total[1h]))
    ) > (14.4 * (1 - 0.999))
  for: 5m
  labels:
    severity: page

# Slow burn: 6x over 6d (5% budget in 6d) → ticket
- alert: SlowBurnBudget
  expr: |
    (
```
## Step 6: Grafana LGTM Stack
```
Loki (logs) ← Promtail/Alloy (agent)
Grafana (visualization) ← all datasources
Tempo (traces) ← OTel Collector
Mimir (metrics) ← Prometheus remote_write
```
## Step 7: Continuous Profiling
Fourth OTel signal. Standardizes profiling data format (pprof → OTLP Profiling). Providers: Grafana Pyroscope, Elastic Profiler.
```go
import "github.com/grafana/pyroscope-go"

pyroscope.Start(pyroscope.Config{
  ApplicationName: "myapp",
  ServerAddress:   "http://pyroscope:4040",
  ProfileTypes: []pyroscope.ProfileType{
    pyroscope.ProfileCPU,
    pyroscope.ProfileAllocObjects,
    pyroscope.ProfileAllocSpace,
  },
})
```
## Step 8: Anti-Patterns
| Anti-Pattern | Why It's Bad | Fix |
|--------------|-------------|-----|
| Unstructured logs | Can't filter, aggregate, correlate | Structured JSON with trace_id |
| No trace correlation | Can't follow request across services | OTel auto-instrumentation |
| Alert on everything | Alert fatigue | SLO-based burn-rate alerts |
| Dashboards without SLOs | No business-relevant health metric | Define SLIs → SLOs → dashboards |
| No log sampling | High-throughput paths generate TB of logs | Sample at 1-10% for debug logs |
| Vendor lock-in | Can't switch observability tools | OTel Collector as abstraction layer |
## Step 9: eBPF Observability
eBPF runs sandboxed programs in kernel space. No code changes, no sidecars.
```bash
# Install Cilium with Hubble
cilium install --enable-hubble --enable-hubble-ui
# Observe network flows
hubble observe --namespace production --verdict DROPPED
hubble observe --follow --protocol tcp --to-pod api/deployment
```
## Step 10: Sidecar-less Service Mesh
| Aspect | Envoy Sidecar (Istio) | Cilium (sidecar-less) |
|--------|----------------------|----------------------|
| Data plane | L7 proxy per pod (Envoy) | eBPF in kernel + shared Envoy |
| Overhead | ~50-100MB RAM per sidecar | ~0 additional RAM per pod |
| Latency added | +2-5ms per hop | +0.1-0.5ms |
| mTLS | Envoy terminates | eBPF + WireGuard or IPsec |
| Observability | Envoy access logs, Istio metrics | Hubble L7 flows, Prometheus metrics |
**When to use which:**
- Cilium mesh: new clusters, latency-sensitive, resource-constrained
- Istio/Envoy: existing Envoy investment, complex L7 policies, multi-cluster
## Step 11: Distributed Tracing Advanced
- `traceparent` header: version-traceId-spanId-traceFlags
- `tracestate`: vendor-specific key-value pairs
- **Baggage:** key-value pairs propagated across service boundaries (tenant-id, user-tier)
- **Span links:** relate spans from different traces (batch processor → producer spans)
- **Baggage-driven sampling:** use baggage to force-include high-value requests
- **Cross-process propagation:** through message queues (Kafka, RabbitMQ) — explicit inject/extract
## Step 12: Observability-Driven Development (ODD)
"If I can't observe it, I can't ship it."
- Design: define RED metrics (Rate, Errors, Duration) per endpoint
- Implementation: add semantic spans with business attributes before unit tests
- Review: PR checklist includes instrumentation
- Deploy: define SLI → SLO → error budget before deploy
## Step 13: Incident Management
| Tool | Focus |
|------|-------|
| PagerDuty | Event-driven, auto-escalation |
| Opsgenie | Atlassian-integrated, on-call scheduling |
| incident.io | Slack-native, incident roles |
**Best practices:** Severity classification (SEV1-SEV5). Blameless postmortems. Runbooks attached to alerts. Incident retrospectives feed back into SLO definitions.
## Step 14: Serverless Observability
| Challenge | Why | Impact |
|-----------|-----|--------|
| Cold starts | New execution environment on demand | Latency spike 100ms-10s |
| No persistent host | Container recycled after idle | Can't tail logs on disk |
| Ephemeral spans | Context dies when function returns | Must flush before return |
| Short-lived execution | Sub-second to few seconds max | Batching windows must fit timeout |
**Pattern:**
```
[Lambda] → OTLP → [OTel Collector Lambda Extension] → [Backend]
```
## Step 15: LLM/AI Observability
| Attribute | Description | Example |
|-----------|-------------|---------|
| `gen_ai.system` | Provider | openai, anthropic, cohere |
| `gen_ai.request.model` | Requested model | gpt-4-turbo, claude-3-opus |
| `gen_ai.request.max_tokens` | Max output tokens | 4096 |
| `gen_ai.request.temperature` | Sampling temperature | 0.7 |
| `gen_ai.usage.input_tokens` | Prompt tokens | 350 |
| `gen_ai.usage.output_tokens` | Completion tokens | 120 |
| `gen_ai.response.finish_reason` | Stop condition | stop, length, content_filter |
| `gen_ai.response.model` | Actual model used | gpt-4-0613 |
| `gen_ai.operation.name` | Operation type | chat, text_completion, embeddings |
```python
from opentelemetry import trace
import time

tracer = trace.get_tracer("genai.tracer")

def call_llm(prompt: str, model: str = "gpt-4-turbo"):
    with tracer.start_as_current_span("chat_completion") as span:
        span.set_attribute("gen_ai.system", "openai")
        span.set_attribute("gen_ai.operation.name", "chat")
        span.set_attribute("gen_ai.request.model", model)
        span.set_attribute("gen_ai.request.temperature", 0.7)

        start = time.time()
        response = openai.chat.completions.create(
            model=model,
```
## Step 16: Edge Observability
```javascript
export default {
  async fetch(request, env) {
    const start = Date.now();
    const response = await handleRequest(request);

    env.WAE.writeDataPoint({
      blobs: [request.url, request.method, request.cf.colo, request.cf.country],
      doubles: [Date.now() - start, response.status],
    });

    return response;
  }
};
```
## Step 17: OTel Collector Deployment Patterns
| Pattern | Resource Cost | Config Flexibility | Tail Sampling | Recommended Scale |
|---------|--------------|-------------------|---------------|-------------------|
| Sidecar | High (N× pods) | Per-service | No (unless gateway) | < 50 services |
| DaemonSet | Medium (N× nodes) | Per-node | No (unless gateway) | 50-500 services |
| Gateway | Low overhead + gateway cost | Centralized | Yes | 100+ services |
**Hybrid (recommended for production):**
```
[App Pods] → [Agent Collector (daemonset)] → [Gateway Collector Cluster] → [Backends]
```
## Step 18: Microservices Golden Signals
| Signal | What It Measures | Metric Type | Example |
|--------|-----------------|-------------|---------|
| **Latency** | Time to serve a request | Histogram | `http_request_duration_seconds` |
| **Traffic** | Demand on the system | Counter | `http_requests_total` |
| **Errors** | Rate of failed requests | Counter | `http_requests_total{code=~"5.."}` |
| **Saturation** | How "full" the system is | Gauge | `container_cpu_usage` |
```promql
# Latency: p99
histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, service))
# Traffic: rps
sum(rate(http_requests_total[5m])) by (service)
# Errors: error rate
sum(rate(http_requests_total{code=~"5.."}[5m])) by (service) / sum(rate(http_requests_total[5m])) by (service)
# Saturation: CPU
sum(rate(container_cpu_usage_seconds_total[5m])) by (pod) / sum(container_cpu_cores) by (pod)
```
## Step 19: Log Aggregation
| Aspect | Elasticsearch | Loki | ClickHouse |
|--------|--------------|------|------------|
| Indexing | Full-text (Lucene) | Labels only | Column-oriented |
| Storage cost | High | Low | Medium |
| Query power | Full Lucene queries | LogQL (label filter) | Full SQL |
| Ingestion speed | Fast | Fast | Very fast |
| Resource usage | Heavy (JVM) | Light | Medium |
| Best for | Full-text search | Prometheus-native teams | Analytics at scale |
**ClickHouse logs table:**
```sql
CREATE TABLE logs (
    timestamp DateTime64(3),
    service LowCardinality(String),
    level LowCardinality(String),
    message String,
    trace_id String,
    attributes Map(String, String)
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(timestamp)
ORDER BY (service, timestamp, level)
TTL timestamp + INTERVAL 90 DAY;
```
## Step 20: Metrics Aggregation
```yaml
groups:
  - name: slo_recordings
    interval: 1m
    rules:
      - record: service:error_rate:rate5m
        expr: |
          sum(rate(http_requests_total{code=~"5.."}[5m])) by (service)
          /
          sum(rate(http_requests_total[5m])) by (service)

      - record: service:latency_p99:histogram5m
        expr: |
          histogram_quantile(0.99,
            sum(rate(http_request_duration_seconds_bucket[5m])) by (le, service)
          )
```
## Step 21: Alert Design Patterns
| Type | What | Example | When to Page |
|------|------|---------|-------------|
| **Symptom-based** | User-visible impact | Error rate > 1% | Yes — always page |
| **Cause-based** | Internal condition | CPU > 80% | Maybe — supplementary |
**Principle:** Alert on symptoms (what users see), not causes (what broke).
```yaml
route:
  receiver: default-slack
  group_by: ['service', 'namespace']
  routes:
    - match:
        severity: page
        slo_breach: true
      receiver: pagerduty-oncall
      repeat_interval: 15m
    - match:
        team: payments
      receiver: payments-slack
    - match:
        severity: warn
      receiver: jira-webhook
```
## Step 22: Observability Maturity Model
| Capability | Status |
|-----------|--------|
| Metrics | Basic Prometheus/Grafana dashboards |
| Logs | Structured logging, centralized |
| Traces | Manual instrumentation on critical paths |
| Alerts | CPU/memory/disk thresholds |
| SLOs | None |
| Coverage | Core services only (5-10) |
| Capability | Status |
|-----------|--------|
| Metrics | Recording rules, standard dashboards per service |
| Logs | Trace correlation, log sampling |
| Traces | Auto-instrumented via OTel, tail-based sampling |
| Alerts | SLO-based burn-rate alerts, alert routing by team |
| SLOs | Defined per service (availability + latency) |
| Coverage | All production services (50+) |
| Capability | Status |
|-----------|--------|
| Metrics | Long-term storage (Mimir/Thanos), cross-cluster |
| Logs | Multi-surface (Loki + ClickHouse) |
| Traces | 100% tail-sampled, continuous profiling correlated |
## Step 23: ML Model Monitoring
**KS Test (continuous features):**
```python
from scipy.stats import ks_2samp

stat, p_value = ks_2samp(reference_data["feature_1"], production_data["feature_1"])
drifted = p_value < 0.05
```
## Step 24: AI Agent Observability
```
agent_run (trace)
├── llm_call: plan next action (span)
│   ├── gen_ai.usage.input_tokens = 450
│   └── gen_ai.usage.output_tokens = 80
├── tool_call: search_database (span)
│   ├── tool.name = "search_database"
│   └── tool.input = {"query": "..."}
├── llm_call: synthesize results (span)
│   ├── gen_ai.usage.input_tokens = 1200
│   └── gen_ai.usage.output_tokens = 300
└── llm_call: final answer (span)
    ├── gen_ai.usage.input_tokens = 800
    └── gen_ai.usage.output_tokens = 150
```
## Step 25: MLOps Observability
```python
import mlflow

mlflow.set_tracking_uri("http://mlflow-server:5000")
mlflow.set_experiment("fraud-detection-v2")

with mlflow.start_run(run_name="xgboost-hyperopt"):
    mlflow.log_params({
        "model": "xgboost",
        "n_estimators": 500,
        "max_depth": 6,
        "learning_rate": 0.1,
    })
    model = train_model(X_train, y_train, params)
    mlflow.log_metrics({
        "roc_auc": 0.95,
```
## Step 26: Chaos Engineering
```yaml
apiVersion: litmuschaos.io/v1alpha1
kind: ChaosEngine
metadata:
  name: api-chaos
spec:
  appinfo:
    appns: production
    applabel: app=api
    appkind: deployment
  chaosServiceAccount: litmus-admin
  experiments:
    - name: pod-delete
      spec:
        components:
          env:
```
## Step 27: Toil Reduction & Capacity Planning
Manual, repetitive, automatable, reactive, no lasting value. Target: < 50% of engineering time (< 30% mature orgs).
**Automation priority:**
```
High frequency × High duration = automate first (deployments, scaling)
High frequency × Low duration = automate second (ticket routing)
Low frequency  × High duration = automate third (disaster recovery)
Low frequency  × Low duration = document, skip automation
```
## Step 28: Honeycomb Patterns
Emit rich events instead of narrow metrics. Single event contains all context.
```json
{
  "timestamp": "2024-06-15T10:30:00.123Z",
  "service": "api",
  "trace.trace_id": "abc123",
  "http.method": "GET",
  "http.path": "/api/users",
  "http.status_code": 200,
  "http.request_duration_ms": 45,
  "user.id": "u-1001",
  "user.tier": "enterprise",
  "db.system": "postgresql",
  "db.duration_ms": 12,
  "cache.hit": true,
  "deploy.version": "v2.3.1",
  "k8s.pod": "api-7b8d9-x2k4j",
```
## Step 29: Grafana LGTM Deep Dive
| Component | Signal | Storage | Query Language |
|-----------|--------|---------|----------------|
| Mimir | Metrics | S3/GCS | PromQL |
| Loki | Logs | S3/GCS | LogQL |
| Tempo | Traces | S3/GCS | TraceQL |
| Pyroscope | Profiles | S3/GCS | ProfileQL |
```
# Slow traces for a service
{ resource.service.name = "api" && duration > 2s }

# Error traces
{ status = error }

# Structural: service A calls service B
{ resource.service.name = "api" } >> { resource.service.name = "payment" }

# Specific attributes
{ span.db.system = "postgresql" && span.duration > 500ms }

# Traces hitting both Redis AND Postgres
{ span.db.system = "redis" } && { span.db.system = "postgresql" }
```
## Step 30: Monitoring as Code
```hcl
resource "grafana_dashboard" "api_overview" {
  folder      = grafana_folder.production.id
  config_json = file("${path.module}/dashboards/api-overview.json")
  overwrite   = true
}

resource "grafana_alert_rule" "high_error_rate" {
  folder_uid = grafana_folder.production.uid
  name       = "High Error Rate"
  condition  = "C"

  data {
    ref_id         = "A"
    datasource_uid = grafana_data_source.prometheus.uid
    relative_time_range { from = 300; to = 0 }
```
## Step 31: Incident Response Automation
```
Prometheus Alert
    → Alertmanager
        ├──→ PagerDuty / incident.io
        │         ├── Create incident
        │         ├── Open Slack channel
        │         ├── Page on-call engineer
        │         ├── Update status page
        │         └── Execute runbook
        │                  ├── Pull recent deploys
        │                  ├── Check service health
        │                  └── Auto-remediate (restart, rollback, scale)
        └──→ Slack notification (lower severity)
```
## Step 32: eBPF Deep Dive
```bash
helm install cilium cilium/cilium \
  --namespace kube-system \
  --set hubble.enabled=true \
  --set hubble.relay.enabled=true \
  --set hubble.ui.enabled=true \
  --set hubble.metrics.enabled="{dns,drop,tcp,flow,icmp,http}"

# Observe flows
hubble observe --namespace production --verdict dropped
hubble observe --to-pod production/api-server --protocol http
hubble observe --from-pod production/payment-svc --to-fqdn api.stripe.com
```
## Pitfalls
1. **Don't skip OpenTelemetry** — it's the standard, use auto-instrumentation
2. **Don't log without trace_id** — logs and traces must be correlated
3. **Don't alert on raw metrics** — use SLO-based burn-rate alerting
4. **Don't skip structured logging** — unstructured logs are useless at scale
5. **Don't vendor-lock** — OTel Collector abstracts away backend choice
6. **Don't create too many dashboards** — one per service with SLIs
7. **Don't skip error budgets** — they balance reliability vs velocity
8. **Don't ignore log sampling** — high-throughput paths need sampling
9. **Don't log PII/secrets** — sanitize before logging
10. **Don't use single thresholds for all services** — different baselines
## Quick Reference
```bash
# OTel Collector health check
curl http://localhost:8888/metrics

# Prometheus query from CLI
curl -G 'http://localhost:9090/api/v1/query' --data-urlencode 'query=up'

# Loki log query from CLI
curl -G 'http://localhost:3100/loki/api/v1/query_range' \
  --data-urlencode 'query={service="api"} |= "error"' \
  --data-urlencode 'start=2024-01-15T00:00:00Z' \
  --data-urlencode 'end=2024-01-15T01:00:00Z'

# Tempo trace query
curl http://localhost:3200/api/traces/<traceID>

```