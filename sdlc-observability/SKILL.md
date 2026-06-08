---
name: sdlc-observability
description: "Observability: OpenTelemetry 2024, GenAI semantic conventions, eBPF (Cilium/Hubble/Tetragon), sidecar-less mesh, profiling signal, structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM, distributed tracing, cost optimization, serverless observability, LLM/AI observability, edge observability."
version: 3.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, observability, opentelemetry, prometheus, grafana, loki, jaeger, sli, slo, error-budget, tracing, logging, sre, ebpf, cilium, genai, profiling, serverless, lambda, cloudwatch, emf, edge, llm, ai]
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