---
name: sdlc-observability
description: "Observability: OpenTelemetry 2024, GenAI semantic conventions, eBPF (Cilium/Hubble/Tetragon), structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM stack, distributed tracing, serverless/edge/LLM/AI observability, ML model monitoring, agent observability."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, observability, opentelemetry, prometheus, grafana, loki, jaeger, sli, slo, error-budget, tracing, logging, sre, ebpf, cilium, genai, profiling, serverless, llm, ai, golden-signals, alerting, ml-monitoring, agent-observability]
    related_skills: [sdlc-deployment, sdlc-cicd-pipeline, sdlc-testing-qa]
---

# Observability

OpenTelemetry, eBPF, structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM stack, distributed tracing, serverless/edge/LLM/AI observability, ML monitoring.

## When to Use

Trigger when user:
- Instruments services with OpenTelemetry or LLM/AI services
- Sets up dashboards, alerts, or SLOs
- Configures structured logging or distributed tracing
- Deploys eBPF-based observability (Cilium, Tetragon, Pixie)
- Deploys OTel Collector (sidecar, daemonset, gateway)
- Designs observability for microservices
- Monitors ML model drift and degradation
- Instruments AI agents for cost/latency/reasoning visibility

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

**Collector config:**
```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
processors:
  batch:
    timeout: 5s
  memory_limiter:
    check_interval: 1s
    limit_mib: 512
exporters:
  otlp:
    endpoint: "jaeger:4317"
  prometheus:
    endpoint: "0.0.0.0:8889"
service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [batch, memory_limiter]
      exporters: [otlp]
    metrics:
      receivers: [otlp]
      processors: [batch]
      exporters: [prometheus]
```

**Semantic Conventions (2024 stable):**

| Category | Attribute | Description |
|----------|-----------|-------------|
| HTTP | `http.request.method` | GET, POST (replaces `http.method`) |
| HTTP | `http.response.status_code` | 200, 404, 500 |
| DB | `db.system` | postgresql, mysql, redis, mongodb |
| DB | `db.namespace` | Database name (replaces `db.name`) |
| DB | `db.operation.name` | SELECT, INSERT (replaces `db.operation`) |
| Messaging | `messaging.system` | kafka, rabbitmq, sqs |
| GenAI | `gen_ai.system` | openai, anthropic, cohere, vertex_ai |
| GenAI | `gen_ai.request.model` | gpt-4, claude-3, etc. |
| GenAI | `gen_ai.usage.input_tokens` | Prompt tokens consumed |
| GenAI | `gen_ai.usage.output_tokens` | Completion tokens generated |

**Logs Bridge API:** Connect existing logging libs to OTLP via `LoggingHandler`. **Collector Connectors:** Join pipelines within same Collector (e.g., count connector: traces→count→metrics).

## Step 2: Three Pillars — Logs, Metrics, Traces

| Pillar | What | Why | Example |
|--------|------|-----|---------|
| **Metrics** | Numerical measurements over time | WHAT is happening | requests_total, duration_seconds |
| **Traces** | End-to-end request path | WHERE the problem is | trace_id → [span1 → span2 → span3] |
| **Logs** | Timestamped text records | WHY it happened | "Connection refused to db-prod:5432" |

**Metric Types:** Counter (monotonically increasing), Gauge (point-in-time), Histogram (distribution)

**Trace concepts:** Trace = tree of spans. Each span has trace_id, span_id, parent_span_id, name, start_time, duration, attributes, status. Context propagation via W3C TraceContext headers.

## Step 3: Structured Logging

**Structured JSON (good):**
```json
{
  "timestamp": "2024-01-15T10:30:00Z",
  "level": "ERROR",
  "message": "Connection refused",
  "service": "api-gateway",
  "host": "db-prod",
  "port": 5432,
  "trace_id": "abc123def456",
  "span_id": "789ghi",
  "request_id": "req-42",
  "duration_ms": 5023
}
```

**Libraries:** Go: zerolog/zap | Python: structlog | Java: Logback+logstash-encoder | Node.js: pino/winston | .NET: Serilog

**Best practices:** Always include trace_id/span_id. Consistent field names across services. Avoid logging PII/secrets. Use log sampling in high-throughput paths.

## Step 4: SLIs / SLOs / SLAs

**Prometheus SLI queries:** Availability: `sum(rate(http_requests_total{code!~"5.."}[30d])) / sum(rate(http_requests_total[30d]))`. Latency: `sum(rate(http_request_duration_seconds_bucket{le="0.25"}[30d])) / sum(rate(http_request_duration_seconds_count[30d]))`.

| Concept | Definition | Target |
|---------|-----------|--------|
| SLI | Quantitative measure | Measured from telemetry |
| SLO | Target for SLI | Internal (99.9%) |
| SLA | Business contract | External (99.5%), with penalties |

SLO should be stricter than SLA (buffer for error).

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

**Multi-Window Burn-Rate Alerting:**
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
      sum(rate(http_requests_total{code=~"5.."}[6d])) /
      sum(rate(http_requests_total[6d]))
    ) > (6 * (1 - 0.999))
  for: 30m
  labels:
    severity: ticket
```

| Burn Rate | Time to Exhaust 30d Budget | Budget Consumed |
|-----------|---------------------------|-----------------|
| 1x | 30 days | 100% (normal) |
| 6x | 5 days | 5% in 6d |
| 14.4x | 50 hours | 2% in 1h |
| 1000x | 43 minutes | total failure |

## Step 6: Grafana LGTM Stack

```
Loki (logs) ← Promtail/Alloy (agent)
Grafana (visualization) ← all datasources
Tempo (traces) ← OTel Collector
Mimir (metrics) ← Prometheus remote_write
```

**Prometheus:**
```yaml
scrape_configs:
  - job_name: 'api'
    static_configs:
      - targets: ['api:8080']
    metrics_path: /metrics
    scrape_interval: 15s
```

**Loki (LogQL):**
```bash
{service="api"} |= "error" | logfmt | duration > 1s
```

**Jaeger (dev):**
```bash
docker run -d -p 16686:16686 -p 4317:4317 jaegertracing/all-in-one
```

**TraceQL (Tempo):**
```
{ resource.service.name = "api" && duration > 2s }
{ resource.service.name = "api" } >> { resource.service.name = "payment" }
{ span.db.system = "redis" } && { span.db.system = "postgresql" }
```

**Cross-Signal Correlation:**
```
Metric spike (Mimir) → click exemplar → Trace (Tempo) → click span → Logs (Loki) → click service → Metrics (Mimir)
```

| Component | Signal | Storage | Query Language |
|-----------|--------|---------|----------------|
| Mimir | Metrics | S3/GCS | PromQL |
| Loki | Logs | S3/GCS | LogQL |
| Tempo | Traces | S3/GCS | TraceQL |
| Pyroscope | Profiles | S3/GCS | ProfileQL |

## Step 7: Continuous Profiling

**Pyroscope (always-on CPU/memory):**
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

**Cilium / Hubble — Network Observability:**
```bash
cilium install --enable-hubble --enable-hubble-ui
hubble observe --namespace production --verdict DROPPED
```
Hubble observes: L3/L4/L7 flows (DNS, HTTP, Kafka), dropped packets, service map.

**Tetragon — Security Observability:**
```yaml
apiVersion: cilium.io/v1alpha1
kind: TracingPolicy
metadata:
  name: sensitive-file-access
spec:
  kprobes:
  - call: "fd_install"
    syscall: false
    args:
    - index: 1
      type: "file"
    selectors:
    - matchArgs:
      - index: 1
        operator: "Prefix"
        values:
        - "/etc/shadow"
```

```bash
helm install tetragon cilium/tetragon -n kube-system
tetra getevents --namespace production --process api-server
```

**Pixie — Auto-Telemetry:**
```bash
px deploy
px run px/http_data -- --start_rel=-1h
px run px/perf_flamegraph -- -p <pod_name>
```

Zero-instrumentation. Auto-discovers HTTP, gRPC, MySQL, PostgreSQL, Kafka, DNS. In-cluster storage (short retention). Export to Grafana for long-term.

| Tool | Focus | Instrumentation | Query Lang |
|------|-------|-----------------|-----------|
| Cilium/Hubble | Network (L3-L7) | Kernel eBPF | Hubble CLI |
| Pixie | Full-stack APM | eBPF + kprobe | PxL |
| Falco | Runtime security | Syscall (eBPF) | Rules YAML |
| Tetragon | Security + observ | eBPF + kprobe | JSON filters |

## Step 10: Sidecar-less Service Mesh

| Aspect | Envoy Sidecar (Istio) | Cilium (sidecar-less) |
|--------|----------------------|----------------------|
| Data plane | L7 proxy per pod (Envoy) | eBPF in kernel + shared Envoy |
| Overhead | ~50-100MB RAM per sidecar | ~0 additional RAM per pod |
| Latency added | +2-5ms per hop | +0.1-0.5ms |
| mTLS | Envoy terminates | eBPF + WireGuard or IPsec |

**When to use:**
- Cilium mesh: new clusters, latency-sensitive, resource-constrained
- Istio/Envoy: existing Envoy investment, complex L7 policies, multi-cluster

## Step 11: Distributed Tracing

**W3C Context Propagation:**
- `traceparent` header: version-traceId-spanId-traceFlags
- `tracestate`: vendor-specific key-value pairs
- **Baggage:** key-value pairs propagated across service boundaries (tenant-id, user-tier)

**Patterns:** Span links (relate spans from different traces), Baggage-driven sampling, Cross-process propagation through message queues.

## Step 12: Observability-Driven Development (ODD)

"If I can't observe it, I can't ship it."

- Design: define RED metrics (Rate, Errors, Duration) per endpoint
- Implementation: add semantic spans with business attributes before unit tests
- Review: PR checklist includes instrumentation
- Deploy: define SLI → SLO → error budget before deploy

## Step 13: Serverless Observability

| Challenge | Why | Impact |
|-----------|-----|--------|
| Cold starts | New execution environment on demand | Latency spike 100ms-10s |
| No persistent host | Container recycled after idle | Can't tail logs on disk |
| Ephemeral spans | Context dies when function returns | Must flush before return |

**Pattern:** `[Lambda] → OTLP → [OTel Collector Lambda Extension] → [Backend]`

```bash
aws lambda update-function-configuration \
  --function-name order-processor \
  --layers arn:aws:lambda:us-east-1:123456:layer:opentelemetry-collector-arm64-0_98_0:1

OTEL_SERVICE_NAME=order-processor
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
OTEL_TRACES_SAMPLER=parentbased_traceidratio
OTEL_TRACES_SAMPLER_ARG=0.1
```

## Step 14: LLM/AI Observability

### GenAI Attributes Reference

| Attribute | Description | Example |
|-----------|-------------|---------|
| `gen_ai.system` | Provider | openai, anthropic |
| `gen_ai.request.model` | Requested model | gpt-4-turbo, claude-3-opus |
| `gen_ai.usage.input_tokens` | Prompt tokens | 350 |
| `gen_ai.usage.output_tokens` | Completion tokens | 120 |
| `gen_ai.response.finish_reason` | Stop condition | stop, length, content_filter |
| `gen_ai.operation.name` | Operation type | chat, embeddings |

**Token Usage Tracking:**
```python
from opentelemetry import trace
import time

tracer = trace.get_tracer("genai.tracer")

def call_llm(prompt: str, model: str = "gpt-4-turbo"):
    with tracer.start_as_current_span("chat_completion") as span:
        span.set_attribute("gen_ai.system", "openai")
        span.set_attribute("gen_ai.request.model", model)
        start = time.time()
        response = openai.chat.completions.create(
            model=model,
            messages=[{"role": "user", "content": prompt}],
        )
        latency_ms = (time.time() - start) * 1000
        span.set_attribute("gen_ai.usage.input_tokens", response.usage.prompt_tokens)
        span.set_attribute("gen_ai.usage.output_tokens", response.usage.completion_tokens)
        span.set_attribute("gen_ai.response.finish_reason", response.choices[0].finish_reason)
        span.set_attribute("llm.latency_ms", latency_ms)
        span.set_attribute("llm.cost_usd", calculate_cost(model, response.usage))
        return response
```

**Cost Tracking:**
```python
MODEL_PRICING = {
    "gpt-4o":          {"input": 2.50 / 1_000_000, "output": 10.00 / 1_000_000},
    "gpt-4o-mini":     {"input": 0.15 / 1_000_000, "output": 0.60 / 1_000_000},
    "claude-3.5-sonnet": {"input": 3.00 / 1_000_000, "output": 15.00 / 1_000_000},
}

def calculate_cost(model: str, usage) -> float:
    pricing = MODEL_PRICING.get(model, MODEL_PRICING["gpt-4o"])
    return (usage.prompt_tokens * pricing["input"]
            + usage.completion_tokens * pricing["output"])
```

**Prometheus LLM Queries:** p50 latency: `histogram_quantile(0.5, sum(rate(gen_ai_latency_ms_bucket[5m])) by (le, gen_ai_request_model))`. Error rate: `sum(rate(gen_ai_requests_total{status="error"}[5m])) by (model) / sum(rate(gen_ai_requests_total[5m])) by (model)`. Hourly cost: `sum(rate(gen_ai_usage_cost_usd_sum[1h])) by (gen_ai_request_model)`.

### LLM Observability Platform Comparison

| Platform | Open Source | Tracing | Cost Tracking | Self-Hosted | OTel Export |
|----------|-----------|---------|---------------|-------------|-------------|
| **LangSmith** | No | Full LLM call tree | Per-run token cost | No (SaaS) | OTLP |
| **Langfuse** | Apache-2.0 | Traces + sessions | Per-model cost | Docker/K8s | OTLP |
| **Helicone** | MIT | Request logging | Real-time dashboard | Docker | Proxied |
| **Arize Phoenix** | Apache-2.0 | Full trace graph | Token + latency | Python | OTLP |

**Selection:** LangSmith for LangChain teams. Langfuse for multi-framework. Helicone for proxy-based capture. Arize Phoenix for eval-centric workflows.

**GenAI Agent/Tool Calls:** Use `gen_ai.tool.message` events for tool calls, nested `tool.<name>` spans for execution. Track `gen_ai.agent.iterations` and `gen_ai.agent.total_tokens`.

**Standard GenAI metrics (OTel):** `gen_ai.client.token.usage` (counter), `gen_ai.client.operation.duration` (histogram), `gen_ai.server.time_to_first_token` (histogram).

## Step 15: Edge Observability

**Cloudflare Workers Analytics Engine:**
```javascript
export default {
  async fetch(request, env) {
    const start = Date.now();
    const response = await handleRequest(request);
    env.WAE.writeDataPoint({
      blobs: [request.url, request.method, request.cf.colo],
      doubles: [Date.now() - start, response.status],
    });
    return response;
  }
};
```
Pattern: Worker → WAE (dashboards) + Logpush (S3) + OTLP (Tempo).

## Step 16: OTel Collector Deployment Patterns

| Pattern | Resource Cost | Tail Sampling | Recommended Scale |
|---------|--------------|---------------|-------------------|
| Sidecar | High (N× pods) | No | < 50 services |
| DaemonSet | Medium (N× nodes) | No | 50-500 services |
| Gateway | Low + gateway cost | Yes | 100+ services |

**Hybrid (recommended for production):**
```
[App Pods] → [Agent Collector (daemonset)] → [Gateway Collector Cluster] → [Backends]
```

**DaemonSet agent config:**
```yaml
receivers:
  otlp:
    protocols: { grpc: { endpoint: "0.0.0.0:4317" }, http: { endpoint: "0.0.0.0:4318" } }
  hostmetrics:
    collection_interval: 15s
    scrapers: [cpu, memory, disk, network]
processors:
  batch: { timeout: 5s, send_batch_size: 1000 }
  memory_limiter: { check_interval: 1s, limit_mib: 256 }
  k8sattributes:
    auth_type: serviceAccount
    extract: { metadata: [k8s.pod.name, k8s.namespace.name, k8s.deployment.name] }
exporters:
  otlp: { endpoint: "otel-gateway:4317", tls: { insecure: true } }
service:
  pipelines:
    traces: { receivers: [otlp], processors: [memory_limiter, k8sattributes, batch], exporters: [otlp] }
    metrics: { receivers: [otlp, hostmetrics], processors: [memory_limiter, k8sattributes, batch], exporters: [otlp] }
```

**Gateway config (tail sampling):**
```yaml
processors:
  tail_sampling:
    decision_wait: 10s
    num_traces: 500000
    policies:
      - { name: errors, type: status_code, status_code: { status_codes: [ERROR] } }
      - { name: slow, type: latency, latency: { threshold_ms: 2000 } }
      - { name: sample-rest, type: probabilistic, probabilistic: { sampling_percentage: 10 } }
exporters:
  otlp/jaeger: { endpoint: "jaeger:4317" }
  prometheusremotewrite: { endpoint: "https://mimir/api/v1/push" }
  loki: { endpoint: "https://loki/loki/api/v1/push" }
service:
  pipelines:
    traces: { receivers: [otlp], processors: [tail_sampling], exporters: [otlp/jaeger] }
    metrics: { receivers: [otlp], exporters: [prometheusremotewrite] }
    logs: { receivers: [otlp], exporters: [loki] }
```

## Step 17: Microservices Golden Signals

| Signal | What It Measures | Metric Type | Example |
|--------|-----------------|-------------|---------|
| **Latency** | Time to serve request | Histogram | `http_request_duration_seconds` |
| **Traffic** | Demand on system | Counter | `http_requests_total` |
| **Errors** | Rate of failed requests | Counter | `http_requests_total{code=~"5.."}` |
| **Saturation** | How "full" system is | Gauge | `container_cpu_usage` |

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

### USE Method (Infrastructure)

| Resource | Utilization | Saturation | Errors |
|----------|-------------|------------|--------|
| CPU | `container_cpu_usage / limit` | `container_cpu_cfs_throttled_periods` | OOM kills |
| Memory | `container_memory_working_set / limit` | `container_memory_swap` | OOM kills |
| Disk | `node_disk_io_time_seconds` | `node_disk_io_queue_length` | `node_disk_io_errors_total` |
| Network | `container_network_transmit_bytes` | `TcpExt_TCPSynOverflow` | `node_network_receive_errs_total` |

## Step 18: Log Aggregation

| Aspect | Elasticsearch | Loki | ClickHouse |
|--------|--------------|------|------------|
| Indexing | Full-text (Lucene) | Labels only | Column-oriented |
| Storage cost | High | Low | Medium |
| Query power | Full Lucene queries | LogQL (label filter) | Full SQL |
| Resource usage | Heavy (JVM) | Light | Medium |
| Best for | Full-text search | Prometheus-native | Analytics at scale |

**ClickHouse logs table:** `CREATE TABLE logs (timestamp DateTime64(3), service LowCardinality(String), level LowCardinality(String), message String, trace_id String, attributes Map(String, String)) ENGINE = MergeTree() PARTITION BY toYYYYMM(timestamp) ORDER BY (service, timestamp, level) TTL timestamp + INTERVAL 90 DAY;`

## Step 19: Metrics Aggregation

**Recording Rules:**
```yaml
groups:
  - name: slo_recordings
    interval: 1m
    rules:
      - record: service:error_rate:rate5m
        expr: sum(rate(http_requests_total{code=~"5.."}[5m])) by (service) / sum(rate(http_requests_total[5m])) by (service)
      - record: service:latency_p99:histogram5m
        expr: histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, service))
```

**Long-Term Storage:**

| Solution | Architecture | Best For |
|----------|-------------|----------|
| **Thanos** | Sidecar + object storage | Multi-cluster, existing Prometheus |
| **Grafana Mimir** | Horizontally scalable TSDB | Large scale, Grafana-native |
| **VictoriaMetrics** | Drop-in Prometheus replacement | Cost-sensitive, single binary |

| Aspect | Mimir | Thanos |
|--------|-------|--------|
| Architecture | Microservices | Sidecar + Compactor + Store |
| Multi-tenant | Native (X-Scope-OrgID) | Manual (separate buckets) |
| Best for | Green-field, Grafana-native | Existing Prometheus + sidecar |

## Step 20: Alert Design Patterns

**Symptom vs Cause:**

| Type | What | Example | When to Page |
|------|------|---------|-------------|
| **Symptom-based** | User-visible impact | Error rate > 1% | Yes — always page |
| **Cause-based** | Internal condition | CPU > 80% | Maybe — supplementary |

**Principle:** Alert on symptoms (what users see), not causes (what broke).

**Alert Routing:**
```yaml
route:
  receiver: default-slack
  group_by: ['service', 'namespace']
  routes:
    - { match: { severity: page, slo_breach: true }, receiver: pagerduty-oncall, repeat_interval: 15m }
    - { match: { team: payments }, receiver: payments-slack }
    - { match: { severity: warn }, receiver: jira-webhook, repeat_interval: 24h }
```

**Escalation Levels:**

| Level | Channel | Response Time |
|-------|---------|--------------|
| L1 (info) | Slack/email | Next business day |
| L2 (warn) | Slack + ticket | Within 4h |
| L3 (page) | PagerDuty | Within 15m |
| L4 (sev1) | PagerDuty + phone + bridge | Immediate |

## Step 21: Observability Maturity Model

### Crawl (Level 1) — "We can see what's broken"
- Basic Prometheus/Grafana dashboards
- Structured logging, centralized
- Manual instrumentation on critical paths
- CPU/memory/disk threshold alerts
- No SLOs. Core services only (5-10).

### Walk (Level 2) — "We can predict and prevent"
- Recording rules, standard dashboards per service
- Trace correlation, log sampling
- Auto-instrumented via OTel, tail-based sampling
- SLO-based burn-rate alerts, alert routing by team
- All production services (50+).

### Run (Level 3) — "We understand system behavior"
- Long-term storage (Mimir/Thanos), cross-cluster
- Multi-surface (Loki + ClickHouse)
- 100% tail-sampled, continuous profiling correlated
- Symptom-based, auto-escalation, runbook automation
- Error budget policy enforced. All services + infra + edge.

### Assessment Checklist
```
[ ] All services emit structured logs with trace_id
[ ] >80% services auto-instrumented with OTel
[ ] SLIs defined for all customer-facing services
[ ] SLOs with burn-rate alerts (not threshold alerts)
[ ] Alert routing by team (not broadcast)
[ ] Runbooks attached to every page-level alert
[ ] Dashboards generated as code (not hand-crafted)
[ ] Log-trace-metric correlation working end-to-end
[ ] Tail-based sampling deployed
[ ] Long-term metrics storage (>30 days)
[ ] Continuous profiling on critical services
[ ] Error budget policy drives deployment decisions
```

## Step 22: ML Model Monitoring

**Data Drift Detection:**
```python
from scipy.stats import ks_2samp
stat, p_value = ks_2samp(reference_data["feature_1"], production_data["feature_1"])
drifted = p_value < 0.05
```

**PSI (categorical or binned continuous):**
```python
def psi(reference, production, bins=10):
    """PSI > 0.2 = significant drift, 0.1-0.2 = moderate, < 0.1 = stable"""
    ref_pct = np.histogram(reference, bins=bins)[0] / len(reference)
    prod_pct = np.histogram(production, bins=np.histogram(reference, bins=bins)[1])[0] / len(production)
    prod_pct = np.where(prod_pct == 0, 0.0001, prod_pct)
    ref_pct = np.where(ref_pct == 0, 0.0001, ref_pct)
    return np.sum((prod_pct - ref_pct) * np.log(prod_pct / ref_pct))
```

**Concept Drift (streaming):** `drift.ADWIN(delta=0.002)` — call `.update(error)`, check `.drift_detected`.

| Method | Type | Mechanism | Best For |
|--------|------|-----------|----------|
| KS Test | Data drift | Statistical test | Continuous features |
| PSI | Data drift | Binned distribution shift | Categorical, batch |
| ADWIN | Concept drift | Adaptive sliding window | Streaming |
| DDM | Concept drift | Error rate + std dev | Binary classification |

| Feature | Evidently | WhyLabs | NannyML |
|---------|-----------|---------|---------|
| Open Source | Apache-2.0 | No | Apache-2.0 |
| Data Drift | KS, PSI, Wasserstein | Custom profiles | Univariate + multivariate |
| Target-free | No | Yes | Yes (CBPE) |
| Dashboard | HTML reports | SaaS | HTML reports |

**Evidently example:** `Report(metrics=[DataDriftPreset()]).run(ref_df, prod_df).save_html("drift_report.html")`

## Step 23: AI Agent Observability

**Agent Run Anatomy:** agent_run → [llm_call, tool_call, llm_call, llm_call]

**Instrumenting Agent Traces:**
```python
from opentelemetry import trace
tracer = trace.get_tracer("ai.agent")

def run_agent(user_query: str):
    with tracer.start_as_current_span("agent.run") as root:
        root.set_attribute("agent.query", user_query)
        root.set_attribute("agent.name", "customer-support")
        total_in, total_out, step = 0, 0, 0
        while not done:
            with tracer.start_as_current_span(f"agent.step.{step}"):
                with tracer.start_as_current_span("gen_ai.chat") as llm:
                    llm.set_attribute("gen_ai.system", "openai")
                    response = call_llm(messages)
                    total_in += response.usage.prompt_tokens
                    total_out += response.usage.completion_tokens
                if should_call_tool(response):
                    with tracer.start_as_current_span("tool.call") as t:
                        t.set_attribute("tool.name", tool_name)
                        execute_tool(tool_name, tool_input)
                step += 1
        root.set_attribute("agent.total_steps", step)
        root.set_attribute("agent.total_tokens", total_in + total_out)
```

**Agent PromQL:** Duration p99: `histogram_quantile(0.99, sum(rate(agent_run_duration_seconds_bucket[5m])) by (le, agent_name))`. Cost/run: `sum(rate(agent_total_cost_usd_sum[1h])) by (agent_name) / sum(rate(agent_run_total[1h])) by (agent_name)`. Slowest tool: `histogram_quantile(0.95, sum(rate(agent_tool_duration_seconds_bucket[5m])) by (le, tool_name))`.

**Agent Anti-Patterns:**

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| Logging only final output | Can't debug reasoning failures | Trace every step + tool call |
| No cost attribution | Surprise LLM bills | Tag every span with cost, alert on threshold |
| Infinite agent loops | Agent retries tool forever | Set max_steps, alert on step_count > N |
| Single monolithic span | Can't identify bottleneck | Separate spans for llm_call, tool_call |
| Missing timeout on LLM calls | Hung agent, wasted cost | Set per-call timeout, record timeout as error |

## Step 24: MLOps Observability

**MLflow Tracking:**
```python
import mlflow
mlflow.set_tracking_uri("http://mlflow-server:5000")
mlflow.set_experiment("fraud-detection-v2")
with mlflow.start_run(run_name="xgboost-hyperopt"):
    mlflow.log_params({"model": "xgboost", "n_estimators": 500, "max_depth": 6})
    model = train_model(X_train, y_train, {"n_estimators": 500, "max_depth": 6})
    mlflow.log_metrics({"roc_auc": 0.95, "f1": 0.87})
    mlflow.sklearn.log_model(model, artifact_path="model", registered_model_name="fraud-detection")
```

**Feature Store Alerts:** `StaleFeatures` (>24h, page), `FeatureDrift` (PSI>0.2, warn).

**Model Registry Lineage:** Raw Data → Feature Pipeline → Feature Store → Training → Model Registry → Serving (each tagged with version/run_id).

| Attribute | Description | Example |
|-----------|-------------|---------|
| `ml.pipeline.name` | Pipeline identifier | fraud-detection-v2 |
| `ml.model.name` | Registered model name | fraud-detection |
| `ml.model.version` | Model version | 3 |
| `ml.data.version` | Training data version | dataset-2024-06 |
| `ml.metric.roc_auc` | Training metric | 0.95 |

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

**Common Commands:**
```bash
curl http://localhost:8888/metrics                                       # OTel Collector health
curl -G 'http://localhost:9090/api/v1/query' --data-urlencode 'query=up' # Prometheus query
curl -G 'http://localhost:3100/loki/api/v1/query_range' \                # Loki query
  --data-urlencode 'query={service="api"} |= "error"'
curl http://localhost:3200/api/traces/<traceID>                          # Tempo trace
hubble observe --namespace production --verdict dropped --follow          # Hubble
```

**SLO Definition Template:**
```yaml
slo:
  service: api-gateway
  sli:
    name: availability
    query: |
      sum(rate(http_requests_total{code!~"5.."}[30d]))
      /
      sum(rate(http_requests_total[30d]))
  target: 0.999
  window: 30d rolling
  alerting:
    fast_burn: { rate: 14.4, window: 1h, severity: page }
    slow_burn: { rate: 6, window: 6d, severity: ticket }
  error_budget_policy:
    budget_remaining_gt_50: "Ship features at normal pace"
    budget_remaining_lt_20: "Freeze non-critical deploys"
    budget_exhausted: "Full deployment freeze"
```

**Observability Stack Sizing:**

| Scale | Metrics Series | Logs/day | Traces/day | Recommended |
|-------|---------------|----------|------------|-------------|
| Small (<10 services) | <100K | <10GB | <50GB | Single Prometheus + Loki + Jaeger |
| Medium (10-100) | 100K-1M | 10-100GB | 50-500GB | Mimir + Loki + Tempo (microservices) |
| Large (100+) | 1M-10M | 100GB-1TB | 500GB-5TB | Mimir + Loki + Tempo (scaled) + Gateway Collector |
| Very Large (1000+) | 10M+ | 1TB+ | 5TB+ | Multi-region Mimir/Loki/Tempo + dedicated teams |

**Cost Comparison:** 100GB logs/day → Loki ~$200/mo | 10M series → Mimir ~$300/mo | 500GB traces/day → Tempo ~$400/mo | Total ~$900/mo vs SaaS $5000-15000/mo.
