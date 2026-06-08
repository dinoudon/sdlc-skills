---
name: sdlc-observability
description: "Observability: OpenTelemetry 2024, GenAI semantic conventions, eBPF (Cilium/Hubble/Tetragon), sidecar-less mesh, profiling signal, structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM, distributed tracing, cost optimization, serverless observability, LLM/AI observability, edge observability, OTel Collector deployment patterns, microservices golden signals, log aggregation (ELK/Loki/ClickHouse), metrics aggregation, alert design patterns, observability maturity model, LLM platform comparison, ML model monitoring, AI agent observability, MLOps observability."
version: 4.2.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, observability, opentelemetry, prometheus, grafana, loki, jaeger, sli, slo, error-budget, tracing, logging, sre, ebpf, cilium, genai, profiling, serverless, lambda, cloudwatch, emf, edge, llm, ai, collector, daemonset, sidecar, gateway, golden-signals, elk, clickhouse, alerting, maturity-model, langsmith, langfuse, helicone, arize, ml-monitoring, drift-detection, agent-observability, mlops, mlflow, kubeflow]
    related_skills: [sdlc-deployment, sdlc-cicd-pipeline, sdlc-testing-qa]
---

# Observability

OpenTelemetry 2024 (GenAI conventions, Logs bridge API, Collector Connectors), eBPF observability (Cilium/Hubble/Tetragon, Pixie), sidecar-less service mesh, OTel Profiling signal, structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM stack, distributed tracing, cost optimization.

## When to Use

Trigger when user:
- Instruments services with OpenTelemetry
- Instruments LLM/AI services (GenAI observability)
- Sets up dashboards, alerts, or SLOs
- Configures structured logging
- Implements distributed tracing
- Configures eBPF-based observability (Cilium, Tetragon, Pixie)
- Migrates from Envoy sidecars to sidecar-less mesh
- Designs monitoring architecture
- Defines error budgets
|- Deploys OTel Collector (sidecar, daemonset, gateway)
|- Designs observability for microservices (golden signals, dependency mapping)
|- Chooses log aggregation stack (ELK, Loki, ClickHouse)
|- Configures metrics aggregation (recording rules, federation, long-term storage)
|- Designs alert routing and escalation policies
|- Assesses observability maturity
- Compares LLM observability platforms (LangSmith, Langfuse, Helicone, Arize Phoenix)
- Monitors ML model drift and degradation (Evidently, WhyLabs, NannyML)
- Instruments AI agents for cost/latency/reasoning visibility
- Tracks ML experiments and pipelines (MLflow, Kubeflow)

## Step 1: OpenTelemetry (OTEL)

Source: https://opentelemetry.io/

CNCF observability framework. Standardizes telemetry data collection. Vendor-neutral. Merged OpenTracing + OpenCensus.

**Components:**
- **API:** Interfaces for instrumentation (traces, metrics, logs)
- **SDK:** Configurable implementations (sampling, exporters, processors)
- **Collector:** Receives, processes, exports telemetry
- **OTLP:** Native protocol (gRPC/HTTP) for all signal types

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
| HTTP | `http.request.method` | GET, POST, etc. (replaces deprecated `http.method`) |
| HTTP | `http.response.status_code` | 200, 404, 500 (replaces `http.status_code`) |
| HTTP | `url.full` | Full request URL |
| DB | `db.system` | postgresql, mysql, redis, mongodb |
| DB | `db.namespace` | Database name (replaces `db.name`) |
| DB | `db.operation.name` | SELECT, INSERT, etc. (replaces `db.operation`) |
| Messaging | `messaging.system` | kafka, rabbitmq, sqs |
| Messaging | `messaging.destination.name` | Topic/queue name |
| Messaging | `messaging.operation.type` | publish, receive, process, create |
| RPC | `rpc.system` | grpc, java_rmi, dotnet_wcf |
| RPC | `rpc.service` | Service name |
| GenAI | `gen_ai.system` | openai, anthropic, cohere, vertex_ai |
| GenAI | `gen_ai.request.model` | gpt-4, claude-3, etc. |
| GenAI | `gen_ai.request.max_tokens` | Max output tokens requested |
| GenAI | `gen_ai.usage.input_tokens` | Prompt tokens consumed |
| GenAI | `gen_ai.usage.output_tokens` | Completion tokens generated |
| GenAI | `gen_ai.response.finish_reason` | stop, length, content_filter |

Source: https://opentelemetry.io/docs/specs/semconv/

### OpenTelemetry GenAI Semantic Conventions (2024)

Standardized telemetry for LLM/AI applications. Covers request/response lifecycle, token usage, model parameters.

```python
from opentelemetry import trace

tracer = trace.get_tracer("genai.tracer")
with tracer.start_as_current_span("chat_completion") as span:
    span.set_attribute("gen_ai.system", "openai")
    span.set_attribute("gen_ai.request.model", "gpt-4")
    span.set_attribute("gen_ai.request.max_tokens", 1024)
    # ... call LLM ...
    span.set_attribute("gen_ai.usage.input_tokens", response.usage.prompt_tokens)
    span.set_attribute("gen_ai.usage.output_tokens", response.usage.completion_tokens)
    span.set_attribute("gen_ai.response.finish_reason", response.choices[0].finish_reason)
```

Source: https://opentelemetry.io/docs/specs/semconv/gen-ai/

### Logs Bridge API (Stable 2024)

Connects existing logging libraries to OTel without rewriting. Bridge translates log records to OTel LogRecords, enabling correlation with traces via trace_id.

```python
import logging
from opentelemetry._logs import set_logger_provider
from opentelemetry.sdk._logs import LoggerProvider, LoggingHandler

provider = LoggerProvider()
set_logger_provider(provider)
handler = LoggingHandler(level=logging.NOTSET, logger_provider=provider)
logging.getLogger().addHandler(handler)
# Standard logging calls now export via OTLP
```

Source: https://opentelemetry.io/docs/specs/otel/logs/bridge-api/

### Collector Connectors

Connectors join two pipelines within the same Collector — no external network hop. Enables routing, deduplication, and cross-signal correlation.

```yaml
connectors:
  count:
    spanevents:
      my.service.span.count:
        description: "Span count by service"

service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [count, otlp]
    metrics:
      receivers: [count]
      exporters: [prometheus]
```

Source: https://opentelemetry.io/docs/collector/configuration/#connectors

## Step 2: Three Pillars — Logs, Metrics, Traces

Source: https://opentelemetry.io/docs/concepts/signals/

| Pillar | What | Why | Example |
|--------|------|-----|---------|
| **Metrics** | Numerical measurements over time | WHAT is happening (dashboards, alerts) | requests_total, duration_seconds |
| **Traces** | End-to-end request path across services | WHERE the problem is (request flow) | trace_id → [span1 → span2 → span3] |
| **Logs** | Timestamped text records | WHY it happened (detailed context) | "Connection refused to db-prod:5432" |

**OpenTelemetry unifies all three under one API/SDK.**

### Metric Types
- **Counter:** Monotonically increasing (requests_total)
- **Gauge:** Point-in-time value (cpu_usage, queue_depth)
- **Histogram:** Distribution of values (request_duration_seconds)

### Trace Concepts
- **Trace:** Full journey of a request (tree of spans)
- **Span:** Single unit of work (trace_id, span_id, parent_span_id, name, start_time, duration, attributes, status)
- **Context propagation:** W3C TraceContext (traceparent, tracestate headers) or B3 headers

## Step 3: Structured Logging

### Unstructured (bad)
```
2024-01-15 ERROR: Connection refused to db-prod:5432
```

### Structured JSON (good)
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
  "user_id": "u-1001",
  "duration_ms": 5023,
  "error": "dial tcp: connection refused"
}
```

**Benefits:** machine-parseable, filterable, correlatable (trace_id links to distributed trace), aggregatable.

**Libraries:**
| Language | Library |
|----------|---------|
| Go | zerolog, zap (uber) |
| Python | structlog, python-json-logger |
| Java | Logback + logstash-logback-encoder |
| Node.js | pino, winston |
| .NET | Serilog |

**Best practices:**
- Always include trace_id and span_id
- Use consistent field names across services
- Log at appropriate levels: DEBUG < INFO < WARN < ERROR < FATAL
- Include request context (user_id, request_id, correlation_id)
- Avoid logging PII/secrets
- Use log sampling in high-throughput paths

## Step 4: SLIs / SLOs / SLAs

Source: https://sre.google/sre-book/service-level-objectives/

### SLI (Service Level Indicator)
Quantitative measure of service behavior.

**Common SLIs:**
- **Availability:** successful requests / total requests
- **Latency:** % of requests faster than threshold (p50, p95, p99)
- **Throughput:** requests per second
- **Correctness:** responses with correct data / total responses

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

### SLO (Service Level Objective)
Target value for an SLI.

**Example:** "99.9% of requests complete in < 250ms over 30-day window"
- SLO = availability: 99.9% AND latency_p99: < 250ms
- Multiple SLOs per service
- Window: rolling (last N days) vs calendar (monthly)

### SLA (Service Level Agreement)
Business contract with consequences. Contains SLOs + penalties for breach.
- SLA is external/customer-facing
- SLO is internal/target
- SLO should be stricter than SLA (buffer for error)

## Step 5: Error Budgets

Source: https://sre.google/sre-book/embracing-risk/

**Error budget = 1 - SLO target**

| SLO | Error Budget | Monthly Downtime |
|-----|-------------|------------------|
| 99.9% | 0.1% | 43.2 minutes |
| 99.95% | 0.05% | 21.6 minutes |
| 99.99% | 0.01% | 4.32 minutes |

**Budget-based decision making:**
- Budget remaining > 50%: push features faster
- Budget remaining 20-50%: normal pace
- Budget remaining < 20%: slow down, focus on reliability
- Budget exhausted: freeze deployments, fix reliability

### Multi-Window Burn-Rate Alerting

Source: https://sre.google/workbook/alerting-on-slos/

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

## Step 6: Grafana LGTM Stack

### LGTM Architecture
```
Loki (logs) ← Promtail/Alloy (agent)
Grafana (visualization) ← all datasources
Tempo (traces) ← OTel Collector
Mimir (metrics) ← Prometheus remote_write
```

### Prometheus
```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'api'
    static_configs:
      - targets: ['api:8080']
    metrics_path: /metrics
    scrape_interval: 15s
```

**Key:** Pull model. Scrapes /metrics endpoints. PromQL query language.

### Loki
```bash
# LogQL query
{service="api"} |= "error" | logfmt | duration > 1s
```

**Key:** Indexes labels, not full text (cheap storage). Like Prometheus for logs.

### Jaeger
```bash
# Run in dev
docker run -d -p 16686:16686 -p 4317:4317 jaegertracing/all-in-one
```

**Key:** Distributed tracing backend (CNCF graduated). Supports OTLP, Zipkin.

### Grafana Dashboard Panels
- Time series, stat, table, heatmap, flamegraph, node graph
- Datasources: Prometheus, Loki, Tempo, Jaeger, Elasticsearch, CloudWatch

## Step 7: Continuous Profiling & OTel Profiling Signal

### OpenTelemetry Profiling Signal (Experimental)

Fourth OTel signal alongside traces, metrics, logs. Standardizes profiling data format (pprof → OTLP Profiling).

```go
// OTel profiling SDK (experimental)
import "go.opentelemetry.io/otel/sdk/trace"
// Profiling data exported as OTLP profiles, correlated with traces via trace_id
```

**Status:** Experimental as of 2024. Aim: unify profiling with traces/metrics/logs in single pipeline.
**Providers:** Grafana Pyroscope, Elastic Profiler already support OTLP profiling export.

Source: https://opentelemetry.io/docs/specs/otel/profiles/

### Pyroscope
Source: https://pyroscope.io/

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

**Key:** Always-on CPU/memory profiling. Flame graphs show hot paths.

## Step 8: Observability Anti-Patterns

| Anti-Pattern | Why It's Bad | Fix |
|--------------|-------------|-----|
| Unstructured logs | Can't filter, aggregate, correlate | Structured JSON with trace_id |
| No trace correlation | Can't follow request across services | OTel auto-instrumentation |
| Alert on everything | Alert fatigue, real issues missed | SLO-based burn-rate alerts |
| Dashboards without SLOs | No business-relevant health metric | Define SLIs → SLOs → dashboards |
| No log sampling | High-throughput paths generate TB of logs | Sample at 1-10% for debug logs |
| Vendor lock-in | Can't switch observability tools | OTel Collector as abstraction layer |
| No correlation IDs | Can't trace request across services | trace_id in every log and span |

## Step 9: eBPF Observability

eBPF runs sandboxed programs in kernel space. No code changes, no sidecars. Observes network, security, and application behavior from kernel level.

### Cilium / Hubble — Network Observability

Cilium is CNI for Kubernetes using eBPF (replaces iptables/kube-proxy). Hubble is its observability layer.

```bash
# Install Cilium with Hubble
cilium install --enable-hubble --enable-hubble-ui
# Hubble CLI: observe network flows
hubble observe --namespace production --verdict DROPPED
# Hubble Relay for multi-node
hubble observe --follow --protocol tcp --to-pod api/deployment
```

**What Hubble observes:**
- L3/L4/L7 network flows (DNS, HTTP, Kafka)
- Dropped packets with reason (policy denied, auth failure)
- Service dependency map (auto-generated)
- TCP retransmissions, RTT latency

Source: https://docs.cilium.io/en/stable/observability/

### Tetragon — Security Observability

eBPF-based security observability and enforcement. Monitors process execution, file access, network connections, privilege escalation at kernel level.

```yaml
# TracingPolicy: watch for sensitive file access
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

**Capabilities:**
- Process lifecycle events (exec, exit, setuid)
- File access monitoring (read, write, delete)
- Network observability (connect, accept, bind)
- Privilege escalation detection
- Runtime policy enforcement (kill process, block syscall)

Source: https://tetragon.io/docs/

### Pixie — Auto-Telemetry

eBPF-powered auto-instrumentation for Kubernetes. Captures HTTP, gRPC, MySQL, PostgreSQL, Kafka, DNS traffic without code changes. Data stays in-cluster (edge processing).

```bash
# Install Pixie
px deploy
# Query: HTTP requests per service
px run px/http_data -- --start_rel=-1h
# Query: flame graph for CPU
px run px/perf_flamegraph -- -p <pod_name>
```

**Key:** Zero-instrumentation telemetry. Auto-discovers services. In-cluster storage (short retention). Export to Grafana for long-term.

Source: https://docs.pixielabs.ai/

## Step 10: Sidecar-less Service Mesh Observability

### Cilium Service Mesh (replaces Envoy sidecars)

Traditional service mesh (Istio/Linkerd) injects Envoy sidecar per pod. Cilium provides mesh features via eBPF — no sidecars, lower latency, less resource overhead.

**Comparison:**

| Aspect | Envoy Sidecar (Istio) | Cilium (sidecar-less) |
|--------|----------------------|----------------------|
| Data plane | L7 proxy per pod (Envoy) | eBPF in kernel + shared Envoy |
| Overhead | ~50-100MB RAM per sidecar | ~0 additional RAM per pod |
| Latency added | +2-5ms per hop | +0.1-0.5ms |
| mTLS | Envoy terminates | eBPF + WireGuard or IPsec |
| Observability | Envoy access logs, Istio metrics | Hubble L7 flows, Prometheus metrics |
| Traffic management | VirtualService, DestinationRule | CiliumNetworkPolicy, CiliumEnvoyConfig |

**Cilium mesh observability:**
```bash
# L7-aware flow visibility (HTTP, gRPC, Kafka)
hubble observe --protocol http --namespace production
# Service map with encrypted connections marked
hubble observe --type l7 --output json | jq '.flow.destination'
# mTLS verification status
cilium encrypt status
```

**When to use which:**
- Cilium mesh: new clusters, latency-sensitive, resource-constrained, eBPF-native
- Istio/Envoy: existing Envoy investment, complex L7 policies, multi-cluster with Citadel

Source: https://docs.cilium.io/en/stable/network/servicemesh/

## Step 11: Distributed Tracing Advanced Patterns

### W3C Trace Context
Source: https://www.w3.org/TR/trace-context/

- `traceparent` header: version-traceId-spanId-traceFlags
- `tracestate`: vendor-specific key-value pairs

### W3C Baggage
Source: https://www.w3.org/TR/baggage/

Key-value pairs propagated across service boundaries independent of trace. Used for business context (tenant-id, user-tier, feature-flags).

### Advanced Patterns
- **Span links:** relate spans from different traces (e.g., batch processor linking to producer spans)
- **Baggage-driven sampling:** use baggage to force-include high-value requests
- **Cross-process propagation:** through message queues (Kafka, RabbitMQ) — explicit inject/extract at boundaries

Source: https://opentelemetry.io/docs/concepts/context-propagation/

## Step 12: Observability-Driven Development (ODD)

Source: https://www.honeycomb.io/blog/observability-driven-development

"If I can't observe it, I can't ship it."

- During design: define RED metrics (Rate, Errors, Duration) for each endpoint
- During impl: add semantic spans with business-meaningful attributes before writing unit tests
- During review: PR checklist includes "did you add instrumentation?"
- SLO-driven: define SLI → SLO → error budget before deploy

## Step 13: Incident Management

| Tool | Focus | Source |
|------|-------|--------|
| PagerDuty | Event-driven, auto-escalation | https://developer.pagerduty.com/ |
| Opsgenie | Atlassian-integrated, on-call scheduling | https://docs.opsgenie.com/docs/api-overview |
| incident.io | Slack-native, incident roles | https://incident.io/docs/api |

**Best practices:**
- Severity classification (SEV1-SEV5) with defined response times
- Blameless postmortems: timeline, root cause, action items
- Runbooks attached to alerts
- Incident retrospectives feed back into SLO definitions

## Step 14: AIOps and Anomaly Detection

**Techniques:**
- Time-series anomaly detection: z-score, STL decomposition, isolation forest, autoencoders
- Metric correlation: auto-correlate spikes across services
- Log pattern mining: unsupervised clustering of log templates
- Trace anomaly: compare span duration distributions to baseline

**Tools:**
- Datadog Watchdog: auto-detect anomalies across metrics/logs/traces
- Dynatrace Davis AI: deterministic + AI root cause analysis
- Grafana ML: forecast, outlier detection
- Open source: LinkedIn ThirdEye, Salesforce Merlion

Source: https://github.com/linkedin/thirdeye, https://github.com/salesforce/Merlion

## Step 15: Observability as Code

### Grafana Dashboards as Code
- Grafonnet (Jsonnet): https://github.com/grafana/grafonnet
- Terraform `grafana_dashboard`: https://registry.terraform.io/providers/grafana/grafana/latest/docs/resources/dashboard

### Terraform for Monitoring
- Grafana provider: dashboards, alert rules, data sources
- Datadog provider: monitors, dashboards, SLOs
- PagerDuty provider: services, schedules, escalation policies

### GitOps for Observability
- Dashboards/alerts/rules in Git, applied via CI pipeline
- PR review for alert threshold changes
- Drift detection: terraform plan catches manual changes

## Step 16: Cost Optimization

### Sampling Strategies
- **Head-based:** decision at trace start, simple but loses tail data
- **Rate limiting:** cap traces per service per second

### Tail-Based Sampling Strategies

Decision made after trace completes. Retains error traces, slow traces, high-value traces. Requires Collector buffering (memory/disk).

```yaml
# Tail-based sampling with OTel Collector
processors:
  tail_sampling:
    decision_wait: 10s          # buffer trace for 10s before deciding
    num_traces: 100000          # max traces in memory buffer
    expected_new_traces_per_sec: 1000
    policies:
      # Always keep errors
      - name: errors
        type: status_code
        status_code: { status_codes: [ERROR] }
      # Always keep slow traces (>2s)
      - name: slow-traces
        type: latency
        latency: { threshold_ms: 2000 }
      # Keep 100% of traces with specific attribute
      - name: high-value
        type: string_attribute
        string_attribute:
          key: user.tier
          values: [enterprise, premium]
      # Sample 5% of remaining (healthy, fast, low-value)
      - name: probabilistic
        type: probabilistic
        probabilistic: { sampling_percentage: 5 }
```

**Multi-Collector architecture for tail sampling at scale:**
```
  [Service Pods] → OTLP → [Gateway Collector: tail_sampling] → [Backend]
                              ↑
       [Load Balancer Collector: round-robin routing]
```
Gateway Collector buffers traces for decision. Load balancer ensures same trace_id routes to same gateway instance (consistent hashing on trace_id).

**Cost levers:**
- Retain 100% errors + slow traces (debug value high)
- Sample 1-5% healthy fast traces (lower debug value)
- Use attribute-based policies: high-value users, critical paths get 100%
- Drop noisy spans: health checks, readiness probes, metrics scrapes

Source: https://opentelemetry.io/docs/collector/configuration/#processors

### Tiered Storage
- **Hot:** 1-7 days, full resolution, fast queries
- **Warm:** 7-30 days, aggregated or sampled
- **Cold:** 30-365 days, object storage (S3/GCS)
- **Archive:** >1yr, compliance retention

## Pitfalls

1. **Don't skip OpenTelemetry** — it's the standard, use auto-instrumentation
2. **Don't log without trace_id** — logs and traces must be correlated
3. **Don't alert on raw metrics** — use SLO-based burn-rate alerting
4. **Don't skip structured logging** — unstructured logs are useless at scale
5. **Don't vendor-lock** — OTel Collector abstracts away backend choice
6. **Don't create too many dashboards** — one per service with SLIs
7. **Don't skip error budgets** — they balance reliability vs velocity
8. **Don't ignore log sampling** — high-throughput paths need sampling

## Step 17: Serverless Observability

### Challenges

Serverless (Lambda, Cloud Functions, Workers) introduces observability gaps traditional host-based monitoring can't fill:

| Challenge | Why | Impact |
|-----------|-----|--------|
| **Cold starts** | New execution environment spun up on demand | Latency spike 100ms-10s, inconsistent baselines |
| **No persistent host** | Container recycled after idle period | Can't tail logs on disk, no host-level agent |
| **Ephemeral spans** | Execution context dies when function returns | Must flush telemetry before return or lose data |
| **Distributed spans** | Single request may invoke multiple functions asynchronously | Cross-function correlation requires explicit propagation |
| **Concurrency = cost** | Parallel invocations = parallel telemetry exporters | OTLP backpressure = dropped spans or throttled Lambda |
| **Short-lived execution** | Sub-second to few seconds max | Batching windows must fit within timeout |

### Solutions Pattern

```
[Lambda] → OTLP → [OTel Collector sidecar or Lambda Extension] → [Backend]
                  (runs as Lambda Extension layer, keeps telemetry buffer across invocations)
```

**Key principles:**
- Use Lambda Extensions (init outside handler) to keep warm across invocations
- Flush telemetry at end of handler, not mid-invocation
- Propagate trace context via environment variables or SNS/SQS message attributes
- Set short OTLP export timeouts (2-5s) to fit within Lambda timeout

### AWS Lambda Observability

**AWS X-Ray:**
```python
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.core import patch_all
patch_all()  # auto-patch requests, boto3, etc.

def handler(event, context):
    with xray_recorder.capture('process_request'):
        subsegment = xray_recorder.begin_subsegment('db_query')
        # ... db call ...
        xray_recorder.end_subsegment()
```
**Limitation:** X-Ray SDK is AWS-only, doesn't export to OTLP backends.

**Powertools Logger (AWS Lambda Powertools for Python):**
```python
from aws_lambda_powertools import Logger

logger = Logger(service="order-processor", level="INFO")

@logger.inject_lambda_context(correlation_id_path="correlation_id")
def handler(event, context):
    logger.info("Processing order", extra={
        "order_id": event["order_id"],
        "customer_id": event["customer_id"],
    })
```
**Output (structured JSON with correlation):**
```json
{
  "level": "INFO",
  "location": "handler:4",
  "message": "Processing order",
  "timestamp": "2024-06-15T10:30:00.000Z",
  "service": "order-processor",
  "cold_start": true,
  "function_name": "order-processor-prod",
  "function_memory_size": 128,
  "function_arn": "arn:aws:lambda:us-east-1:123456:function:order-processor-prod",
  "correlation_id": "abc-123-def",
  "xray_trace_id": "1-665f1a3e-1234567890abcdef",
  "order_id": "ord-42",
  "customer_id": "cust-100"
}
```
**Key:** `correlation_id_path` extracts ID from event payload and injects into every log line. `cold_start` field auto-detected.

**OTEL Lambda Layer:**
```bash
# Add OTEL layer to Lambda
aws lambda update-function-configuration \
  --function-name order-processor \
  --layers arn:aws:lambda:us-east-1:123456:layer:opentelemetry-collector-arm64-0_98_0:1

# Environment variables
OTEL_SERVICE_NAME=order-processor
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
OTEL_TRACES_SAMPLER=parentbased_traceidratio
OTEL_TRACES_SAMPLER_ARG=0.1
```
**Architecture:** OTEL Lambda Extension runs as Lambda layer. Collector process starts during init phase, buffers telemetry, exports on flush. Spans survive across warm invocations.

Source: https://aws-otel.github.io/docs/getting-started/lambda

### CloudWatch Embedded Metric Format (EMF)

EMF lets Lambda emit custom metrics as structured log lines — CloudWatch extracts metrics automatically. No API calls, no metric dimension limits hit, zero extra latency.

```python
import json
EMF_METRIC = {
    "_aws": {
        "Timestamp": 1623456789000,
        "CloudWatchMetrics": [{
            "Namespace": "OrderService",
            "Dimensions": [["environment"]],
            "Metrics": [
                {"Name": "OrderProcessingDuration", "Unit": "Milliseconds"},
                {"Name": "OrderCount", "Unit": "Count"}
            ]
        }]
    },
    "environment": "prod",
    "OrderProcessingDuration": 145.2,
    "OrderCount": 1
}
print(json.dumps(EMF_METRIC))  # stdout → CloudWatch Logs → auto-extracted metric
```
**Powertools Metrics shortcut:**
```python
from aws_lambda_powertools import Metrics
from aws_lambda_powertools.metrics import MetricUnit

metrics = Metrics(namespace="OrderService", service="order-processor")

@metrics.log_metrics(capture_cold_start_metric=True)
def handler(event, context):
    metrics.add_metric(name="OrderCount", unit=MetricUnit.Count, value=1)
    metrics.add_metric(name="OrderProcessingDuration", unit=MetricUnit.Milliseconds, value=145.2)
    metrics.add_dimension(name="environment", value="prod")
```
**Key:** EMF metrics appear in CloudWatch as standard custom metrics. Can alarm on them. No PutMetricData API call needed. Cost: just normal CloudWatch Logs ingestion.

Source: https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format.html

## Step 18: LLM/AI Observability

### GenAI Semantic Conventions (Expanded)

Beyond `gen_ai.system` and token counts, track request parameters, response quality, and operational cost:

| Attribute | Description | Example |
|-----------|-------------|---------|
| `gen_ai.system` | Provider | openai, anthropic, cohere, vertex_ai |
| `gen_ai.request.model` | Model name | gpt-4-turbo, claude-3-opus |
| `gen_ai.request.max_tokens` | Max output tokens | 4096 |
| `gen_ai.request.temperature` | Sampling temperature | 0.7 |
| `gen_ai.request.top_p` | Nucleus sampling | 0.95 |
| `gen_ai.request.encoding_format` | Embedding format | float, base64 |
| `gen_ai.usage.input_tokens` | Prompt tokens | 350 |
| `gen_ai.usage.output_tokens` | Completion tokens | 120 |
| `gen_ai.response.finish_reason` | Stop condition | stop, length, content_filter |
| `gen_ai.response.model` | Actual model used (may differ) | gpt-4-0613 |
| `gen_ai.response.id` | Provider response ID | chatcmpl-abc123 |
| `gen_ai.operation.name` | Operation type | chat, text_completion, embeddings |

### Token Usage Tracking

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
            messages=[{"role": "user", "content": prompt}],
        )
        latency_ms = (time.time() - start) * 1000

        span.set_attribute("gen_ai.usage.input_tokens", response.usage.prompt_tokens)
        span.set_attribute("gen_ai.usage.output_tokens", response.usage.completion_tokens)
        span.set_attribute("gen_ai.response.finish_reason", response.choices[0].finish_reason)
        span.set_attribute("gen_ai.response.model", response.model)
        span.set_attribute("gen_ai.response.id", response.id)
        span.set_attribute("llm.latency_ms", latency_ms)
        span.set_attribute("llm.cost_usd", calculate_cost(model, response.usage))

        return response
```

### Latency Per Model Dashboard

**Prometheus queries for LLM observability:**
```promql
# p50 latency by model
histogram_quantile(0.5,
  sum(rate(gen_ai_latency_ms_bucket[5m])) by (le, gen_ai_request_model)
)

# Token cost per model (input + output)
sum(rate(gen_ai_usage_input_tokens_total[1h]) * on() group_left() input_cost_per_token{model="gpt-4-turbo"})
+
sum(rate(gen_ai_usage_output_tokens_total[1h]) * on() group_left() output_cost_per_token{model="gpt-4-turbo"})

# Error rate by model
sum(rate(gen_ai_requests_total{status="error"}[5m])) by (gen_ai_request_model)
/
sum(rate(gen_ai_requests_total[5m])) by (gen_ai_request_model)
```

**Key metrics to track:**
- **Token usage:** input_tokens, output_tokens, total_tokens (alert on runaway usage)
- **Latency:** p50/p95/p99 per model, per operation (chat vs embeddings)
- **Cost:** USD per request based on token pricing table
- **Error rate:** by model, by finish_reason (content_filter rate = prompt policy issue)
- **Cache hit rate:** if using semantic cache (embedding distance < threshold)

Source: https://opentelemetry.io/docs/specs/semconv/gen-ai/

## Step 19: Edge Observability

### Challenges

Edge compute (Cloudflare Workers, Deno Deploy, Vercel Edge) runs in hundreds of PoPs. Standard observability patterns break:

- **No persistent process:** worker starts, handles request, dies
- **No local exporter:** can't run OTel Collector on edge runtime
- **Limited execution time:** 30ms-30s depending on platform
- **High cardinality from geo:** every PoP is a dimension
- **Cost:** per-request logging to external backends gets expensive fast

### Cloudflare Workers Analytics Engine

Workers Analytics Engine (WAE) writes datapoints directly from Workers code — no external exporter, zero network hop. Data lands in ClickHouse-backed analytics store.

```javascript
export default {
  async fetch(request, env) {
    const start = Date.now();
    const response = await handleRequest(request);
    const duration = Date.now() - start;

    // Write to Analytics Engine (no network call, batches internally)
    env.WAE.writeDataPoint({
      blobs: [
        request.url,           // blob1: route
        request.method,        // blob2: method
        request.cf.colo,       // blob3: colo (PoP)
        request.cf.country,    // blob4: country
      ],
      doubles: [
        duration,              // double1: latency_ms
        response.status,       // double2: status_code
      ],
    });

    return response;
  }
};
```

**Query via GraphQL:**
```graphql
query {
  viewer {
    accounts(filter: {accountTag: "abc123"}) {
      workersAnalyticsEngineAdaptiveGroups(
        limit: 100
        filter: {
          datetime_gt: "2024-06-15T00:00:00Z"
          blob1_like: "/api/%"
        }
        orderBy: [double1_DESC]
      ) {
        dimensions { blob3 }      # colo
        max { double1 }           # max latency
        avg { double1 }           # avg latency
        count
      }
    }
  }
}
```
**Key:** Datapoints are free (included in Workers paid plan). Query via GraphQL API or Grafana plugin. Max 20 blobs + 20 doubles per datapoint.

### Cloudflare Logpush

Logpush ships HTTP request logs to S3, GCS, or HTTP endpoint. For long-term storage and deep analysis.

```bash
# Create Logpush job → S3
curl -X POST "https://api.cloudflare.com/client/v4/accounts/{account_id}/logpush/jobs" \
  -H "Authorization: Bearer {token}" \
  -d '{
    "name": "worker-logs",
    "destination_conf": "s3://my-bucket/worker-logs?region=us-east-1",
    "dataset": "workers_trace_logs",
    "output_options": {
      "field_names": ["RayID", "Outcome", "EventTimestampMs", "ScriptName", "CpuTimeMs", "WallTimeMs", "Logs"],
      "timestamp_format": "unix"
    }
  }'
```

**Combined pattern:**
```
[Worker] ──→ WAE (real-time dashboards, aggregation)
         ──→ Logpush (full request logs → S3 → Athena/BigQuery)
         ──→ OTLP over HTTP (sampled traces → Grafana Tempo)
```
**Key:** WAE for fast aggregates and alerting. Logpush for forensics and compliance. OTLP for trace-level debugging on sampled requests.

Source: https://developers.cloudflare.com/workers/observability/

## Step 20: OpenTelemetry Collector Deployment Patterns

Three deployment patterns. Choice depends on scale, control, and operational model.

### Pattern 1: Sidecar (per-pod)

One Collector instance per application pod. Isolates telemetry per workload. Best for: fine-grained config per service, multi-tenant clusters, teams with different export backends.

```yaml
# Kubernetes sidecar container
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: app
        image: myapp:latest
        env:
        - name: OTEL_EXPORTER_OTLP_ENDPOINT
          value: "http://localhost:4317"
      - name: otel-collector
        image: otel/opentelemetry-collector-contrib:latest
        args: ["--config=/etc/otel/config.yaml"]
        ports:
        - containerPort: 4317
```

**Trade-offs:**
- (+) Full config isolation per service, no noisy-neighbor
- (-) High resource overhead (1 collector × N pods), operational complexity

### Pattern 2: DaemonSet (per-node)

One Collector per Kubernetes node. All pods on node send telemetry to node-local collector. Best for: moderate scale, node-level processing, eBPF integration.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: otel-collector
spec:
  selector:
    matchLabels:
      app: otel-collector
  template:
    spec:
      containers:
      - name: otel-collector
        image: otel/opentelemetry-collector-contrib:latest
        ports:
        - containerPort: 4317  # OTLP gRPC
        - containerPort: 4318  # OTLP HTTP
        resources:
          requests:
            cpu: 200m
            memory: 256Mi
          limits:
            cpu: 500m
            memory: 512Mi
        volumeMounts:
        - name: hostfs
          mountPath: /hostfs
          readOnly: true
      volumes:
      - name: hostfs
        hostPath:
          path: /
```

**Trade-offs:**
- (+) Lower overhead than sidecar, shared across pods on node
- (-) Config shared per node, hot-node bottleneck possible

### Pattern 3: Gateway (centralized)

Central Collector cluster sits between agents/sidecars and backends. Best for: tail-based sampling at scale, central routing, multi-backend fanout, cross-team policy enforcement.

```
[App Pods] → [OTLP] → [Agent Collector (sidecar/daemonset)] → [Gateway Collector Cluster] → [Backends]
                                                  (centralized: tail sampling, routing, redaction)
```

```yaml
# Gateway Collector config
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317

processors:
  tail_sampling:
    decision_wait: 10s
    num_traces: 500000
    policies:
      - name: errors
        type: status_code
        status_code: { status_codes: [ERROR] }
      - name: slow
        type: latency
        latency: { threshold_ms: 2000 }
      - name: sample-rest
        type: probabilistic
        probabilistic: { sampling_percentage: 10 }
  attributes:
    actions:
      - key: token
        action: hash   # redact sensitive data

exporters:
  otlp/jaeger:
    endpoint: jaeger:4317
  prometheusremotewrite:
    endpoint: https://mimir/api/v1/push
  loki:
    endpoint: https://loki/loki/api/v1/push

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [tail_sampling, attributes]
      exporters: [otlp/jaeger]
    metrics:
      receivers: [otlp]
      processors: [attributes]
      exporters: [prometheusremotewrite]
    logs:
      receivers: [otlp]
      processors: [attributes]
      exporters: [loki]
```

### Decision Matrix

| Pattern | Resource Cost | Config Flexibility | Tail Sampling | Recommended Scale |
|---------|--------------|-------------------|---------------|-------------------|
| Sidecar | High (N× pods) | Per-service | No (unless gateway) | < 50 services |
| DaemonSet | Medium (N× nodes) | Per-node | No (unless gateway) | 50-500 services |
| Gateway | Low overhead + gateway cost | Centralized | Yes | 100+ services |

**Hybrid:** Agent (daemonset/sidecar) → Gateway for production. Agent handles protocol translation and initial buffering. Gateway does tail sampling, redaction, multi-backend export.

Source: https://opentelemetry.io/docs/collector/deployment/

## Step 21: Microservices Observability

### Golden Signals (Google SRE)

Four metrics that capture user-facing system health. Monitor all four per service:

| Signal | What It Measures | Metric Type | Example |
|--------|-----------------|-------------|---------|
| **Latency** | Time to serve a request | Histogram | `http_request_duration_seconds` |
| **Traffic** | Demand on the system | Counter | `http_requests_total` |
| **Errors** | Rate of failed requests | Counter | `http_requests_total{code=~"5.."}` |
| **Saturation** | How "full" the system is | Gauge/Histogram | `container_cpu_usage`, `process_resident_memory` |

```promql
# Latency: p99 request duration
histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, service))

# Traffic: requests per second by service
sum(rate(http_requests_total[5m])) by (service)

# Errors: error rate percentage
sum(rate(http_requests_total{code=~"5.."}[5m])) by (service) / sum(rate(http_requests_total[5m])) by (service)

# Saturation: CPU utilization
sum(rate(container_cpu_usage_seconds_total[5m])) by (pod, namespace) / sum(container_cpu_cores) by (pod, namespace)
```

### RED Method (Tom Wilkie)

RED = Rate + Errors + Duration. Subset of golden signals focused on request-driven services:

```promql
# Rate
sum(rate(http_requests_total[5m])) by (service)
# Errors
sum(rate(http_requests_total{code=~"5.."}[5m])) by (service)
# Duration
histogram_quantile(0.99, sum(rate(http_request_duration_seconds_bucket[5m])) by (le, service))
```

### USE Method (Brendan Gregg)

USE = Utilization + Saturation + Errors. For infrastructure resources:

| Resource | Utilization | Saturation | Errors |
|----------|-------------|------------|--------|
| CPU | `container_cpu_usage / limit` | `container_cpu_cfs_throttled_periods` | `machine_cpu_cores - available` |
| Memory | `container_memory_working_set / limit` | `container_memory_swap` (kswapd) | OOM kills |
| Disk | `node_disk_io_time_seconds` | `node_disk_io_queue_length` | `node_disk_io_errors_total` |
| Network | `container_network_transmit_bytes` | `node_netstat_TcpExt_TCPSynOverflow` | `node_network_receive_errs_total` |

### Service Dependency Mapping

Auto-discover service-to-service relationships from telemetry data.

**Method 1: From traces (OTel)**
```promql
# Which services call which (from span names and service attributes)
sum(rate(traces_spanmetrics_calls_total[5m])) by (service_name, span_name, status_code)
```

**Method 2: From network flows (Cilium Hubble)**
```bash
hubble observe --output json --type l7 | \
  jq -r '[.flow.source.identity, .flow.destination.identity] | @tsv' | \
  sort | uniq -c | sort -rn
```

**Method 3: Service mesh (Istio/Kiali)**
```bash
# Kiali auto-generates service graph from Envoy metrics
# Prometheus query for service graph edges
istio_requests_total{destination_service!="unknown"}
```

**Dependency visualization tools:**
- Grafana Tempo service graph (auto-generated from traces)
- Kiali for Istio
- Hubble UI for Cilium
- Datadog Service Map

## Step 22: Log Aggregation Patterns

### ELK Stack (Elasticsearch + Logstash + Kibana)

Full-text indexing. Powerful search (Lucene). Heavy on resources.

```yaml
# Filebeat → Logstash → Elasticsearch → Kibana
# docker-compose.yml (simplified)
services:
  elasticsearch:
    image: elasticsearch:8.14.0
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=true
    volumes:
      - esdata:/usr/share/elasticsearch/data

  logstash:
    image: logstash:8.14.0
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf

  kibana:
    image: kibana:8.14.0
    ports:
      - "5601:5601"
```

```ruby
# logstash.conf
input {
  beats { port => 5044 }
}
filter {
  json { source => "message" }
  date { match => ["timestamp", "ISO8601"] }
  mutate { add_field => { "[@metadata][index]" => "logs-%{service}-%{+YYYY.MM.dd}" } }
}
output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "%{[@metadata][index]}"
  }
}
```

**When to use:** Need full-text search across log content, compliance/safety logs requiring fast keyword search, team already familiar with Elasticsearch.

**Scaling:** Index lifecycle management (ILM) — hot/warm/cold nodes. Shard sizing: 10-50GB per shard. Replica count: 1 for HA.

### Grafana Loki

Label-indexed only (like Prometheus for logs). Cheap storage. Query via LogQL.

```yaml
# Loki config
auth_enabled: false
server:
  http_listen_port: 3100
common:
  path_prefix: /loki
  storage:
    filesystem:
      chunks_directory: /loki/chunks
      rules_directory: /loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory
schema_config:
  configs:
    - from: 2024-01-01
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h
```

```bash
# LogQL examples
# All error logs from api service
{service="api"} |= "error" | logfmt | duration > 1s

# Count errors per minute by service
sum(count_over_time({namespace="production"} |= "error" [1m])) by (service)

# Extract and filter structured fields
{job="api"} | json | status >= 500 | line_format "{{.method}} {{.path}} {{.status}}"
```

**When to use:** Cost-sensitive, already using Prometheus/Grafana, label-based filtering sufficient, large log volumes.

### ClickHouse for Logs

Column-oriented OLAP database. Fast analytical queries on structured logs. Handles billions of rows.

```sql
-- Create logs table
CREATE TABLE logs (
    timestamp DateTime64(3),
    service LowCardinality(String),
    level LowCardinality(String),
    message String,
    trace_id String,
    span_id String,
    attributes Map(String, String)
) ENGINE = MergeTree()
PARTITION BY toYYYYMM(timestamp)
ORDER BY (service, timestamp, level)
TTL timestamp + INTERVAL 90 DAY;

-- Query: error count by service, last hour
SELECT service, count() as errors
FROM logs
WHERE timestamp > now() - INTERVAL 1 HOUR AND level = 'ERROR'
GROUP BY service
ORDER BY errors DESC;

-- Query: full-text search with hasSubsequence
SELECT *
FROM logs
WHERE service = 'api' AND hasSubsequence(message, ['connection', 'refused'])
ORDER BY timestamp DESC
LIMIT 100;
```

**When to use:** High-volume structured logs (>100GB/day), need SQL queries, analytics on log data (trends, aggregations), cost-sensitive at scale.

### Comparison

| Aspect | Elasticsearch | Loki | ClickHouse |
|--------|--------------|------|------------|
| Indexing | Full-text (Lucene) | Labels only | Column-oriented |
| Storage cost | High | Low | Medium |
| Query power | Full Lucene queries | LogQL (label filter) | Full SQL |
| Ingestion speed | Fast | Fast | Very fast |
| Resource usage | Heavy (JVM) | Light | Medium |
| Best for | Full-text search | Prometheus-native teams | Analytics at scale |

## Step 23: Metrics Aggregation

### Recording Rules

Pre-compute expensive queries. Reduce dashboard query time. Standardize SLI calculations.

```yaml
# prometheus.yml — recording rules
rule_files:
  - /etc/prometheus/recording_rules.yml
```

```yaml
# recording_rules.yml
groups:
  - name: slo_recordings
    interval: 1m
    rules:
      # Pre-compute error ratio per service
      - record: service:error_rate:rate5m
        expr: |
          sum(rate(http_requests_total{code=~"5.."}[5m])) by (service)
          /
          sum(rate(http_requests_total[5m])) by (service)

      # Pre-compute p99 latency
      - record: service:latency_p99:histogram5m
        expr: |
          histogram_quantile(0.99,
            sum(rate(http_request_duration_seconds_bucket[5m])) by (le, service)
          )

      # Pre-compute request rate
      - record: service:request_rate:rate5m
        expr: |
          sum(rate(http_requests_total[5m])) by (service)

  - name: burn_rate_recordings
    rules:
      # Fast burn (14.4x over 1h)
      - record: service:error_burn_rate:1h
        expr: |
          service:error_rate:rate5m / (1 - 0.999)

      # Slow burn (6x over 6d)
      - record: service:error_burn_rate:6d
        expr: |
          sum(rate(http_requests_total{code=~"5.."}[6d])) by (service)
          /
          sum(rate(http_requests_total[6d])) by (service)
          /
          (1 - 0.999)
```

**Key rules:**
- Record SLI computations (error rate, latency percentiles)
- Record burn rates for SLO alerting
- Use `_record` suffix convention for recordings
- Group by service/team for organization

### Federation

Pull metrics from one Prometheus into another. Used for cross-cluster aggregation or hierarchical monitoring.

```yaml
# Global Prometheus scrapes per-cluster Prometheus
scrape_configs:
  - job_name: 'federate-cluster-us-east'
    honor_labels: true
    metrics_path: /federate
    params:
      'match[]':
        - '{job="api"}'
        - 'service:error_rate:rate5m'
    static_configs:
      - targets: ['prometheus-us-east:9090']

  - job_name: 'federate-cluster-eu-west'
    honor_labels: true
    metrics_path: /federate
    params:
      'match[]':
        - '{job="api"}'
        - 'service:error_rate:rate5m'
    static_configs:
      - targets: ['prometheus-eu-west:9090']
```

**Limitation:** Federation pulls full metric sets. Doesn't scale well beyond ~10 clusters. Use Thanos or Mimir for large-scale.

### Long-Term Storage

Prometheus defaults to 15-day retention. Solutions for long-term:

| Solution | Architecture | Best For |
|----------|-------------|----------|
| **Thanos** | Sidecar + object storage (S3/GCS). Global query view. Dedup across replicas. | Multi-cluster, existing Prometheus |
| **Grafana Mimir** | Horizontally scalable TSDB. Native remote_write. Multi-tenant. | Large scale, Grafana-native |
| **VictoriaMetrics** | Drop-in Prometheus replacement. 10x compression. Fast queries. | Cost-sensitive, single binary |
| **Cortex** | Predecessor to Mimir. Microservices architecture. | Legacy, Mimir is preferred |

```yaml
# Mimir config (simplified)
multitenancy_enabled: false
blocks_storage:
  backend: s3
  s3:
    bucket_name: mimir-blocks
    endpoint: s3.us-east-1.amazonaws.com
compactor:
  block_ranges: [2h, 12h, 24h]
limits:
  max_global_series_per_user: 10000000

# Prometheus remote_write to Mimir
remote_write:
  - url: https://mimir:9009/api/v1/push
```

## Step 24: Alert Design Patterns

### Symptom-Based vs Cause-Based Alerts

| Type | What | Example | When to Page |
|------|------|---------|-------------|
| **Symptom-based** | User-visible impact | Error rate > 1%, p99 > 500ms | Yes — always page |
| **Cause-based** | Internal condition | CPU > 80%, disk 90% full | Maybe — only if no symptom alert exists |

**Principle:** Alert on symptoms (what users see), not causes (what broke). Symptoms are actionable; causes may not require action if no user impact.

```yaml
# Symptom-based (preferred)
- alert: HighErrorRate
  expr: service:error_rate:rate5m > 0.01
  for: 5m
  labels:
    severity: page
  annotations:
    summary: "{{ $labels.service }} error rate {{ $value | humanizePercentage }}"

# Cause-based (supplementary, lower severity)
- alert: HighCPU
  expr: container_cpu_usage / container_cpu_limit > 0.9
  for: 15m
  labels:
    severity: warn
  annotations:
    summary: "{{ $labels.pod }} CPU at {{ $value | humanizePercentage }}"
```

### Alert Routing

Route alerts to the right team. Use labels for routing, not just severity.

```yaml
# Alertmanager routing
route:
  receiver: default-slack
  group_by: ['service', 'namespace']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 4h
  routes:
    # Critical SLA-breach alerts → PagerDuty
    - match:
        severity: page
        slo_breach: true
      receiver: pagerduty-oncall
      repeat_interval: 15m

    # Service-specific routing
    - match:
        team: payments
      receiver: payments-slack
      routes:
        - match:
            severity: page
          receiver: payments-pagerduty

    # Low severity → ticket system
    - match:
        severity: warn
      receiver: jira-webhook
      repeat_interval: 24h

receivers:
  - name: pagerduty-oncall
    pagerduty_configs:
      - service_key: '<key>'
        severity: '{{ if eq .CommonLabels.severity "page" }}critical{{ else }}warning{{ end }}'
  - name: payments-slack
    slack_configs:
      - channel: '#payments-alerts'
  - name: jira-webhook
    webhook_configs:
      - url: 'http://jira-webhook:8080/create-ticket'
```

### Escalation

| Level | Channel | Response Time | Example |
|-------|---------|--------------|---------|
| L1 (info) | Slack/email | Next business day | Disk trending up, non-critical |
| L2 (warn) | Slack + ticket | Within 4h | SLO budget draining |
| L3 (page) | PagerDuty | Within 15m | Error rate breach, p99 breach |
| L4 (sev1) | PagerDuty + phone + bridge | Immediate | Full outage, data loss |

**Alert lifecycle:**
```
firing → acknowledged → investigating → resolved
        (auto-escalate if not ack'd within 15m)
```

**Anti-patterns to avoid:**
- Alerting on causes without symptoms → false positives
- No runbook → responder can't act
- Repeat interval too short → fatigue
- No grouping → 100 identical alerts for same root cause
- Single threshold for all services → different services have different baselines

## Step 25: Observability Maturity Model

### Crawl (Level 1) — "We can see what's broken"

| Capability | Status |
|-----------|--------|
| Metrics | Basic Prometheus/Grafana dashboards |
| Logs | Structured logging, centralized in Loki or ELK |
| Traces | Manual instrumentation on critical paths |
| Alerts | CPU/memory/disk thresholds, page on outage |
| SLOs | None — monitoring only, no SLI definitions |
| Coverage | Core services only (5-10) |

**Actions:** Set up Prometheus + Grafana. Structured JSON logs. Auto-instrument top 5 services with OTel. Define error rate as first SLI.

### Walk (Level 2) — "We can predict and prevent"

| Capability | Status |
|-----------|--------|
| Metrics | Recording rules, standard dashboards per service |
| Logs | Trace correlation, log sampling, centralized search |
| Traces | Auto-instrumented via OTel, tail-based sampling |
| Alerts | SLO-based burn-rate alerts, alert routing by team |
| SLOs | Defined per service (availability + latency) |
| Coverage | All production services (50+) |

**Actions:** Define SLIs/SLOs for top services. Implement burn-rate alerting. Set up OTel Collector (daemonset). Log-trace correlation everywhere. Service dependency map.

### Run (Level 3) — "We understand system behavior"

| Capability | Status |
|-----------|--------|
| Metrics | Long-term storage (Mimir/Thanos), cross-cluster federation |
| Logs | Multi-surface (Loki + ClickHouse for analytics) |
| Traces | 100% tail-sampled, continuous profiling correlated |
| Alerts | Symptom-based, auto-escalation, runbook automation |
| SLOs | Error budget policy enforced (deployment freeze on budget exhaustion) |
| Coverage | All services + infrastructure + edge |
| Advanced | AIOps anomaly detection, observability-driven development |

**Actions:** Long-term storage. Continuous profiling (Pyroscope). Error budget policies drive deployment velocity. Observability-as-code (Terraform/GitOps). Anomaly detection baseline. Team-level SLO ownership.

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

## Step 26: LLM/AI Observability Platforms

### Platform Comparison

| Platform | Open Source | Tracing | Cost Tracking | Eval Metrics | Self-Hosted | OTel Export |
|----------|-----------|---------|---------------|-------------|-------------|-------------|
| **LangSmith** | No | ✅ Full LLM call tree | ✅ Per-run token cost | ✅ Custom evals, pairwise | No (SaaS) | ✅ OTLP |
| **Langfuse** | ✅ Apache-2.0 | ✅ Traces + sessions | ✅ Per-model cost calc | ✅ LLM-as-judge, human eval | ✅ Docker/K8s | ✅ OTLP |
| **Helicone** | ✅ MIT | ✅ Request logging + replay | ✅ Real-time cost dashboard | ✅ Custom feedback scores | ✅ Docker | ❌ (proxied) |
| **Arize Phoenix** | ✅ Apache-2.0 | ✅ Full trace graph | ✅ Token + latency cost | ✅ Evals, embeddings viz | ✅ Python | ✅ OTLP |

**Selection criteria:**
- **LangSmith:** Best for LangChain/LangGraph teams. Deep integration with framework. SaaS-only (self-hosted enterprise).
- **Langfuse:** Best for multi-framework (LlamaIndex, Haystack, OpenAI SDK). Self-hostable. Strong community.
- **Helicone:** Best for proxy-based capture. Drop-in reverse proxy intercepts all LLM calls. Minimal code change.
- **Arize Phoenix:** Best for eval-centric workflows. Embedding visualization, drift detection on LLM outputs. Notebook-first.

### OpenTelemetry GenAI Conventions (Full)

OTel GenAI semantic conventions (stable 2024+) standardize LLM telemetry across providers:

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter

provider = TracerProvider()
provider.add_span_processor(BatchSpanProcessor(OTLPSpanExporter("http://otel-collector:4317")))
trace.set_tracer_provider(provider)
tracer = trace.get_tracer("genai.application")

def traced_llm_call(prompt: str, model: str = "gpt-4o"):
    with tracer.start_as_current_span("gen_ai.chat") as span:
        # Required GenAI attributes
        span.set_attribute("gen_ai.system", "openai")
        span.set_attribute("gen_ai.operation.name", "chat")
        span.set_attribute("gen_ai.request.model", model)
        span.set_attribute("gen_ai.request.max_tokens", 4096)
        span.set_attribute("gen_ai.request.temperature", 0.7)

        response = client.chat.completions.create(
            model=model,
            messages=[{"role": "user", "content": prompt}],
        )

        # Response attributes
        span.set_attribute("gen_ai.usage.input_tokens", response.usage.prompt_tokens)
        span.set_attribute("gen_ai.usage.output_tokens", response.usage.completion_tokens)
        span.set_attribute("gen_ai.response.finish_reason", response.choices[0].finish_reason.value)
        span.set_attribute("gen_ai.response.model", response.model)
        span.set_attribute("gen_ai.response.id", response.id)

        # Custom cost calculation
        cost = calculate_cost(model, response.usage)
        span.set_attribute("gen_ai.usage.cost_usd", cost)

        return response
```

**Key GenAI attributes (stable):**

| Attribute | Type | Description |
|-----------|------|-------------|
| `gen_ai.system` | string | Provider (openai, anthropic, cohere, vertex_ai) |
| `gen_ai.operation.name` | string | chat, text_completion, embeddings, image_generation |
| `gen_ai.request.model` | string | Requested model name |
| `gen_ai.request.max_tokens` | int | Max output tokens |
| `gen_ai.request.temperature` | float | Sampling temperature |
| `gen_ai.usage.input_tokens` | int | Prompt token count |
| `gen_ai.usage.output_tokens` | int | Completion token count |
| `gen_ai.usage.cost_usd` | float | Computed cost (custom, not in OTel spec) |
| `gen_ai.response.finish_reason` | string | stop, length, content_filter |
| `gen_ai.response.model` | string | Actual model used |
| `gen_ai.response.id` | string | Provider request ID |

Source: https://opentelemetry.io/docs/specs/semconv/gen-ai/

### Cost Tracking Per Model

```python
# Token pricing table (update as prices change)
MODEL_PRICING = {
    "gpt-4o":       {"input": 2.50 / 1_000_000, "output": 10.00 / 1_000_000},
    "gpt-4o-mini":  {"input": 0.15 / 1_000_000, "output": 0.60 / 1_000_000},
    "claude-3.5-sonnet": {"input": 3.00 / 1_000_000, "output": 15.00 / 1_000_000},
    "claude-3-haiku":    {"input": 0.25 / 1_000_000, "output": 1.25 / 1_000_000},
}

def calculate_cost(model: str, usage) -> float:
    pricing = MODEL_PRICING.get(model, MODEL_PRICING["gpt-4o"])
    return (usage.prompt_tokens * pricing["input"]
            + usage.completion_tokens * pricing["output"])
```

**PromQL cost dashboard:**
```promql
# Hourly cost per model
sum(rate(gen_ai_usage_cost_usd_sum[1h])) by (gen_ai_request_model)

# Daily cost breakdown
sum(increase(gen_ai_usage_cost_usd_sum[24h])) by (gen_ai_request_model)

# Cost per user (if user_id in span attributes)
sum(rate(gen_ai_usage_cost_usd_sum[1h])) by (user_id)
```

### Eval Metrics Integration

**Langfuse example — LLM-as-judge:**
```python
from langfuse import Langfuse
langfuse = Langfuse()

# Score a generation with custom eval
langfuse.score(
    trace_id="trace-123",
    name="relevance",
    value=0.85,
    comment="Response directly addresses the query"
)

# Score with LLM-as-judge
langfuse.score(
    trace_id="trace-123",
    name="faithfulness",
    value=1,  # 0 or 1 for binary
    source="model:claude-3-sonnet"
)
```

**Key eval categories:**
- **Relevance:** Does response address the query?
- **Faithfulness:** Is response grounded in retrieved context (RAG)?
- **Toxicity:** Does output contain harmful content?
- **Latency cost ratio:** Quality per dollar spent

## Step 27: ML Model Monitoring

### Data Drift Detection

Drift = production data distribution differs from training data. Causes silent model degradation.

**Kolmogorov-Smirnov (KS) Test — continuous features:**
```python
from scipy.stats import ks_2samp

stat, p_value = ks_2samp(reference_data["feature_1"], production_data["feature_1"])
drifted = p_value < 0.05  # reject null: distributions differ
# stat: max distance between CDFs (0 = identical, 1 = no overlap)
```

**Population Stability Index (PSI) — categorical or binned continuous:**
```python
def psi(reference, production, bins=10):
    """PSI > 0.2 = significant drift, 0.1-0.2 = moderate, < 0.1 = stable"""
    ref_pct = np.histogram(reference, bins=bins)[0] / len(reference)
    prod_pct = np.histogram(production, bins=np.histogram(reference, bins=bins)[1])[0] / len(production)
    prod_pct = np.where(prod_pct == 0, 0.0001, prod_pct)  # avoid log(0)
    ref_pct = np.where(ref_pct == 0, 0.0001, ref_pct)
    return np.sum((prod_pct - ref_pct) * np.log(prod_pct / ref_pct))

# Usage
psi_score = psi(train_data["age"], prod_data["age"])
if psi_score > 0.2:
    alert("Significant drift in age feature")
```

### Concept Drift Detection

Concept drift = relationship between features and target changes over time.

**ADWIN (Adaptive Windowing):**
```python
from river import drift

adwin = drift.ADWIN(delta=0.002)  # delta = sensitivity
for i, prediction_error in enumerate(error_stream):
    adwin.update(prediction_error)
    if adwin.drift_detected:
        print(f"Drift detected at index {i}, window size: {adwin.width}")
        # Trigger model retraining
```

**DDM (Drift Detection Method):**
```python
from river import drift

ddm = drift.DDM()
for i, y_true, y_pred in zip(range(n), y_true_stream, y_pred_stream):
    error = int(y_true != y_pred)
    ddm.update(error)
    if ddm.drift_detected:
        print(f"DDM drift at index {i}")
    elif ddm.warning_detected:
        print(f"DDM warning at index {i} — start collecting new training data")
```

**Comparison:**

| Method | Type | Mechanism | Best For |
|--------|------|-----------|----------|
| KS Test | Data drift | Statistical test on feature distributions | Continuous features |
| PSI | Data drift | Binned distribution shift | Categorical, batch monitoring |
| ADWIN | Concept drift | Adaptive sliding window on error rates | Streaming, non-stationary |
| DDM | Concept drift | Error rate + std deviation thresholds | Binary classification streams |
| Page-Hinkley | Concept drift | Cumulative sum of deviations | Mean shift detection |

### Model Degradation Patterns

| Pattern | Symptom | Detection | Mitigation |
|---------|---------|-----------|------------|
| **Gradual drift** | Accuracy slowly decreases over weeks | PSI trending up, rolling accuracy declining | Scheduled retraining (weekly) |
| **Sudden drift** | Accuracy drops after external event | DDM/ADWIN alarm | Emergency retrain + fallback to previous model |
| **Recurring patterns** | Seasonal accuracy swings | Year-over-year comparison | Retrain with seasonal features |
| **Feature drift** | Input distribution shifts but labels unknown | KS test on features per batch | Retrain with recent data, feature engineering |
| **Label drift** | Target distribution changes | Monitor prediction distribution vs baseline | Collect new labeled data |
| **Upstream data break** | Nulls, schema changes, stale data | Schema validation, freshness checks | Fix pipeline, data quality gates |

### Monitoring Tools Comparison

| Feature | Evidently | WhyLabs | NannyML |
|---------|-----------|---------|---------|
| Open Source | ✅ Apache-2.0 | ❌ (WhyLabs OSS profile only) | ✅ Apache-2.0 |
| Data Drift | ✅ KS, PSI, Wasserstein, Jensen-Shannon | ✅ Custom profiles | ✅ Univariate + multivariate |
| Concept Drift | ✅ (via model perf metrics) | ❌ (indirect) | ✅ CBPE (no ground truth needed) |
| Target-free monitoring | ❌ (needs reference + production) | ✅ | ✅ CBPE estimates perf without labels |
| Dashboard | ✅ HTML reports | ✅ SaaS platform | ✅ HTML reports |
| Integration | Python lib, Evidently Cloud | Python SDK, SaaS | Python library |
| Real-time | Batch + streaming | Streaming | Batch |
| Alerting | Via API/webhooks | ✅ Built-in | Via API |

**Evidently example:**
```python
from evidently import ColumnMapping
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset, DataQualityPreset
from evidently.test_suite import TestSuite
from evidently.tests import TestShareOfDriftedColumns

# Generate drift report
report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=ref_df, current_data=prod_df)
report.save_html("drift_report.html")

# Automated test suite (CI/CD integration)
suite = TestSuite(tests=[TestShareOfDriftedColumns(lt=0.3)])
suite.run(reference_data=ref_df, current_data=prod_df)
assert suite.as_dict()["tests"][0]["status"] == "Success"
```

**NannyML CBPE (no ground truth):**
```python
import nannyml as nml

cbpe = nml.CBPE(
    y_pred_proba="predicted_probability",
    y_pred="prediction",
    y_true="target",
    problem_type="classification_binary",
    metrics=["roc_auc", "f1"],
)
cbpe.fit(reference_df)
estimates = cbpe.estimate(production_df)
estimates.plot().show()  # estimated performance over time
```

## Step 28: AI Agent Observability

### Agent Run Anatomy

AI agents (LangChain, CrewAI, AutoGen, custom) involve multi-step reasoning, tool calls, and LLM invocations. Each run needs:

```
agent_run (trace)
├── llm_call: plan next action (span)
│   ├── gen_ai.usage.input_tokens = 450
│   └── gen_ai.usage.output_tokens = 80
├── tool_call: search_database (span)
│   ├── tool.name = "search_database"
│   ├── tool.input = {"query": "..."}
│   ├── tool.output = "..."
│   └── db.duration_ms = 45
├── llm_call: synthesize results (span)
│   ├── gen_ai.usage.input_tokens = 1200
│   └── gen_ai.usage.output_tokens = 300
├── tool_call: send_email (span)
│   ├── tool.name = "send_email"
│   └── tool.duration_ms = 230
└── llm_call: final answer (span)
    ├── gen_ai.usage.input_tokens = 800
    └── gen_ai.usage.output_tokens = 150
```

### Instrumenting Agent Traces

```python
from opentelemetry import trace

tracer = trace.get_tracer("ai.agent")

def run_agent(user_query: str):
    with tracer.start_as_current_span("agent.run") as root_span:
        root_span.set_attribute("agent.query", user_query)
        root_span.set_attribute("agent.name", "customer-support")
        root_span.set_attribute("agent.model", "gpt-4o")

        total_input_tokens = 0
        total_output_tokens = 0
        total_cost = 0.0
        step = 0

        while not done:
            # Trace each reasoning step
            with tracer.start_as_current_span(f"agent.step.{step}") as step_span:
                step_span.set_attribute("agent.step_number", step)

                # LLM call within step
                with tracer.start_as_current_span("gen_ai.chat") as llm_span:
                    llm_span.set_attribute("gen_ai.system", "openai")
                    llm_span.set_attribute("gen_ai.request.model", "gpt-4o")
                    response = call_llm(messages)
                    tokens_in = response.usage.prompt_tokens
                    tokens_out = response.usage.completion_tokens
                    total_input_tokens += tokens_in
                    total_output_tokens += tokens_out
                    llm_span.set_attribute("gen_ai.usage.input_tokens", tokens_in)
                    llm_span.set_attribute("gen_ai.usage.output_tokens", tokens_out)

                # Tool call within step
                if should_call_tool(response):
                    with tracer.start_as_current_span("tool.call") as tool_span:
                        tool_span.set_attribute("tool.name", tool_name)
                        tool_span.set_attribute("tool.input", str(tool_input))
                        tool_result = execute_tool(tool_name, tool_input)
                        tool_span.set_attribute("tool.output", str(tool_result)[:500])

                step += 1

        # Final attributes on root span
        root_span.set_attribute("agent.total_steps", step)
        root_span.set_attribute("agent.total_input_tokens", total_input_tokens)
        root_span.set_attribute("agent.total_output_tokens", total_output_tokens)
        root_span.set_attribute("agent.total_cost_usd", total_cost)
        root_span.set_attribute("agent.success", done_successfully)
```

### Latency Breakdown

```promql
# Total agent run duration p50/p99
histogram_quantile(0.5, sum(rate(agent_run_duration_seconds_bucket[5m])) by (le, agent_name))
histogram_quantile(0.99, sum(rate(agent_run_duration_seconds_bucket[5m])) by (le, agent_name))

# Time spent in LLM calls vs tool calls vs orchestration
sum(rate(agent_llm_duration_seconds_sum[5m])) by (agent_name)
sum(rate(agent_tool_duration_seconds_sum[5m])) by (agent_name)

# LLM latency vs tool latency ratio
sum(rate(agent_llm_duration_seconds_sum[5m])) by (agent_name)
/
sum(rate(agent_run_duration_seconds_sum[5m])) by (agent_name)
# Result: % of run time spent waiting for LLM

# Slowest tool calls
histogram_quantile(0.95, sum(rate(agent_tool_duration_seconds_bucket[5m])) by (le, tool_name))
```

### Cost Attribution Per Agent Run

```promql
# Average cost per agent run
sum(rate(agent_total_cost_usd_sum[1h])) by (agent_name)
/
sum(rate(agent_run_total[1h])) by (agent_name)

# Cost breakdown: LLM vs tool vs total
sum(rate(agent_llm_cost_usd_sum[1h])) by (agent_name)

# Most expensive runs (top 10)
topk(10, agent_total_cost_usd_sum)

# Cost by model within agent
sum(rate(gen_ai_usage_cost_usd_sum[1h])) by (gen_ai_request_model, agent_name)
```

### Agent Observability Anti-Patterns

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| Logging only final output | Can't debug reasoning failures | Trace every step + tool call |
| No cost attribution | Surprise LLM bills | Tag every span with cost, alert on threshold |
| Infinite agent loops | Agent retries tool forever | Set max_steps on root span, alert on step_count > N |
| No tool input/output logging | Can't reproduce tool failures | Log tool I/O (truncated) in span attributes |
| Single monolithic span | Can't identify bottleneck | Separate spans for llm_call, tool_call, orchestration |
| Missing timeout on LLM calls | Hung agent, wasted cost | Set per-call timeout, record timeout as error span |

## Step 29: MLOps Observability

### MLflow Tracking

```python
import mlflow
import mlflow.sklearn

mlflow.set_tracking_uri("http://mlflow-server:5000")
mlflow.set_experiment("fraud-detection-v2")

with mlflow.start_run(run_name="xgboost-hyperopt"):
    # Log parameters
    mlflow.log_params({
        "model": "xgboost",
        "n_estimators": 500,
        "max_depth": 6,
        "learning_rate": 0.1,
        "subsample": 0.8,
    })

    model = train_model(X_train, y_train, params)

    # Log metrics
    mlflow.log_metrics({
        "roc_auc": 0.95,
        "f1": 0.87,
        "precision": 0.89,
        "recall": 0.85,
        "train_time_sec": 342,
    })

    # Log model artifact
    mlflow.sklearn.log_model(
        model,
        artifact_path="model",
        registered_model_name="fraud-detection",
    )

    # Log data profile as artifact
    mlflow.log_artifact("data_profile.html")
```

**MLflow + OTel integration (emit MLflow events as OTel spans):**
```python
from opentelemetry import trace
import mlflow

tracer = trace.get_tracer("mlops.tracking")

def tracked_training_run(params, X_train, y_train):
    with tracer.start_as_current_span("mlflow.training_run") as span:
        span.set_attribute("mlflow.experiment", "fraud-detection-v2")
        with mlflow.start_run():
            mlflow.log_params(params)
            model = train_model(X_train, y_train, params)
            metrics = evaluate(model)
            mlflow.log_metrics(metrics)
            span.set_attribute("ml.metrics.roc_auc", metrics["roc_auc"])
            span.set_attribute("ml.metrics.f1", metrics["f1"])
```

### Kubeflow Pipeline Metrics

```python
# Kubeflow Pipelines — emit metrics from components
from kfp import dsl
from kfp.dsl import Output, Metrics

@dsl.component
def evaluate_model(
    model_path: str,
    test_data: str,
    metrics: Output[Metrics],
):
    model = load_model(model_path)
    X_test, y_test = load_data(test_data)
    predictions = model.predict(X_test)

    auc = roc_auc_score(y_test, predictions)
    f1 = f1_score(y_test, predictions)

    # Log to Kubeflow UI + MLflow
    metrics.log_metric("roc_auc", auc)
    metrics.log_metric("f1_score", f1)
```

**Kubeflow pipeline monitoring:**
```yaml
# Prometheus metrics from Kubeflow
# kfp-server exposes pipeline run metrics
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: kubeflow-pipelines
spec:
  selector:
    matchLabels:
      app: ml-pipeline
  endpoints:
    - port: http
      path: /metrics
      interval: 30s
```

**Key Kubeflow metrics:**
- `kfp_run_duration_seconds` — total pipeline run time
- `kfp_step_duration_seconds` — per-step duration (training, preprocessing, eval)
- `kfp_run_status_total` — success/failure count by pipeline

### Feature Store Monitoring

```python
# Feast feature store — monitor feature freshness + drift
from feast import FeatureStore
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset

store = FeatureStore(repo_path=".")

# Get feature values from online store
features = store.get_online_features(
    features=["user_features:age", "user_features:income", "user_features:transaction_count"],
    entity_rows=[{"user_id": 123}]
).to_dict()

# Monitor feature freshness (time since last materialization)
feature_views = store.list_feature_views()
for fv in feature_views:
    stats = store.registry.list_entity_stats()  # freshness per entity
    alert_on_stale_features(fv.name, max_age_hours=24)

# Monitor feature drift between training and serving
training_features = store.get_historical_features(
    entity_df=entities,
    features=["user_features:age", "user_features:income"],
).to_df()

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=training_features, current_data=serving_features)
report.save_html("feature_drift.html")
```

**Feature store alerting:**
```yaml
# Prometheus alert on stale features
- alert: StaleFeatures
  expr: feature_store_last_materialization_age_hours > 24
  for: 1h
  labels:
    severity: page
  annotations:
    summary: "Feature store {{ $labels.feature_view }} stale for {{ $value }}h"

# Feature distribution shift
- alert: FeatureDrift
  expr: feature_store_psi_score > 0.2
  for: 30m
  labels:
    severity: warn
  annotations:
    summary: "Feature {{ $labels.feature_name }} PSI={{ $value }} (drifted)"
```

### Model Registry Lineage

Track full lineage: data → features → training → model → deployment → predictions.

```
[Raw Data] → [Feature Pipeline] → [Feature Store] → [Training] → [Model Registry] → [Serving]
     │              │                    │              │              │                │
     └── data_version    └── feature_version  └── run_id      └── model_version  └── deployment_id
         freshness            drift_metrics       params             artifacts          predictions
         schema_hash          null_rate           metrics            approval_status    latency
```

**MLflow model registry lineage:**
```python
import mlflow
from mlflow import MlflowClient

client = MlflowClient("http://mlflow-server:5000")

# Get model version details
model_version = client.get_model_version("fraud-detection", version=3)
print(f"Run ID: {model_version.run_id}")
print(f"Source: {model_version.source}")
print(f"Status: {model_version.status}")  # PENDING_REVIEW, READY, ARCHIVED

# Get training run details for lineage
run = client.get_run(model_version.run_id)
print(f"Training data: {run.data.params.get('training_data_path')}")
print(f"Git commit: {run.data.tags.get('mlflow.source.name')}")
print(f"Metrics: {run.data.metrics}")

# Transition model stage (staging → production)
client.transition_model_version_stage(
    name="fraud-detection",
    version=3,
    stage="Production",
    archive_existing_versions=True,
)

# Log deployment event as OTel span
with tracer.start_as_current_span("model.deploy") as span:
    span.set_attribute("model.name", "fraud-detection")
    span.set_attribute("model.version", 3)
    span.set_attribute("model.stage", "Production")
    span.set_attribute("model.run_id", model_version.run_id)
```

**Lineage tracking OTel attributes:**

| Attribute | Description | Example |
|-----------|-------------|---------|
| `ml.pipeline.name` | Pipeline identifier | fraud-detection-v2 |
| `ml.pipeline.run_id` | Specific pipeline run | run-abc123 |
| `ml.model.name` | Registered model name | fraud-detection |
| `ml.model.version` | Model version | 3 |
| `ml.model.stage` | Registry stage | Staging, Production |
| `ml.data.version` | Training data version | dataset-2024-06 |
| `ml.feature.store_version` | Feature store snapshot | feast-v2-materialized |
| `ml.metric.roc_auc` | Training metric | 0.95 |
| `ml.training.git_commit` | Source code version | abc123f |