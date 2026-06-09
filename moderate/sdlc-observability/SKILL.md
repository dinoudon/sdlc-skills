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

# Observability

OpenTelemetry 2024, eBPF observability, structured logging, SLIs/SLOs/SLAs, error budgets, burn-rate alerting, Grafana LGTM stack, distributed tracing, cost optimization, serverless/edge/LLM/AI observability.

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
| HTTP | `url.full` | Full request URL |
| DB | `db.system` | postgresql, mysql, redis, mongodb |
| DB | `db.namespace` | Database name (replaces `db.name`) |
| DB | `db.operation.name` | SELECT, INSERT (replaces `db.operation`) |
| Messaging | `messaging.system` | kafka, rabbitmq, sqs |
| Messaging | `messaging.destination.name` | Topic/queue name |
| GenAI | `gen_ai.system` | openai, anthropic, cohere, vertex_ai |
| GenAI | `gen_ai.request.model` | gpt-4, claude-3, etc. |
| GenAI | `gen_ai.usage.input_tokens` | Prompt tokens consumed |
| GenAI | `gen_ai.usage.output_tokens` | Completion tokens generated |
| GenAI | `gen_ai.response.finish_reason` | stop, length, content_filter |

### GenAI Semantic Conventions (2024)

Standardized telemetry for LLM/AI applications.

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

### Logs Bridge API (Stable 2024)

Connects existing logging libraries to OTel without rewriting.

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

### Collector Connectors

Connectors join two pipelines within the same Collector — no external network hop.

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

**Good (structured JSON):**
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
  "duration_ms": 5023
}
```

**Libraries:**
| Language | Library |
|----------|---------|
| Go | zerolog, zap |
| Python | structlog, python-json-logger |
| Java | Logback + logstash-logback-encoder |
| Node.js | pino, winston |
| .NET | Serilog |

**Best practices:** Always include trace_id/span_id. Consistent field names across services. Use appropriate log levels. Include request context (user_id, request_id). Avoid logging PII/secrets. Use log sampling in high-throughput paths.

## Step 4: SLIs / SLOs / SLAs

### SLI (Service Level Indicator)

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

### SLO vs SLA

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

### Multi-Window Burn-Rate Alerting

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

**Burn Rate Quick Reference:**

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
# prometheus.yml
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

## Step 7: Continuous Profiling

### OTel Profiling Signal (Experimental)

Fourth OTel signal. Standardizes profiling data format (pprof → OTLP Profiling). Providers: Grafana Pyroscope, Elastic Profiler.

### Pyroscope

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

Always-on CPU/memory profiling. Flame graphs show hot paths.

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

### Cilium / Hubble — Network Observability

```bash
# Install Cilium with Hubble
cilium install --enable-hubble --enable-hubble-ui
# Observe network flows
hubble observe --namespace production --verdict DROPPED
hubble observe --follow --protocol tcp --to-pod api/deployment
```

**What Hubble observes:** L3/L4/L7 flows (DNS, HTTP, Kafka), dropped packets with reason, service dependency map, TCP retransmissions.

### Tetragon — Security Observability

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

**Capabilities:** Process lifecycle, file access, network connections, privilege escalation detection, runtime policy enforcement.

**Tetragon install:**
```bash
helm install tetragon cilium/tetragon -n kube-system
# Watch events
kubectl logs -n kube-system -l app.kubernetes.io/name=tetragon -c export-stdout -f
# Tetragon CLI
tetra getevents --namespace production --process api-server
```

**Tetragon TracingPolicy examples:**
```yaml
# Monitor network connections
apiVersion: cilium.io/v1alpha1
kind: TracingPolicy
metadata:
  name: network-monitor
spec:
  kprobes:
  - call: "tcp_connect"
    syscall: false
    args:
    - index: 0
      type: "sock"
    selectors:
    - matchActions:
      - action: FollowFD
```

### Pixie — Auto-Telemetry

```bash
px deploy
px run px/http_data -- --start_rel=-1h
px run px/perf_flamegraph -- -p <pod_name>
```

Zero-instrumentation. Auto-discovers services. Captures HTTP, gRPC, MySQL, PostgreSQL, Kafka, DNS. In-cluster storage (short retention). Export to Grafana for long-term.

**PxL scripts:**
```python
# HTTP request metrics
import px
df = px.DataFrame(table='http_events')
df = df[['time_ns', 'source', 'destination', 'req_path', 'resp_status', 'resp_latency_ns']]
df = df[df.resp_status >= 400]
px.display(df, 'http_errors')

# Slow MySQL queries
df = px.DataFrame(table='mysql_events')
df = df[df.resp_latency_ns > 1000000000]  # > 1s queries
px.display(df, 'slow_queries')

# TCP retransmits
df = px.DataFrame(table='tcp_stats')
df = df[df.retransmits > 0]
px.display(df, 'tcp_retransmits')
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

### W3C Context Propagation

- `traceparent` header: version-traceId-spanId-traceFlags
- `tracestate`: vendor-specific key-value pairs
- **Baggage:** key-value pairs propagated across service boundaries (tenant-id, user-tier)

### Patterns

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

**AWS Lambda OTel:**
```bash
aws lambda update-function-configuration \
  --function-name order-processor \
  --layers arn:aws:lambda:us-east-1:123456:layer:opentelemetry-collector-arm64-0_98_0:1

# Environment variables
OTEL_SERVICE_NAME=order-processor
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317
OTEL_TRACES_SAMPLER=parentbased_traceidratio
OTEL_TRACES_SAMPLER_ARG=0.1
```

**Powertools Logger:**
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

**CloudWatch EMF (Embedded Metric Format):**
```python
from aws_lambda_powertools import Metrics
from aws_lambda_powertools.metrics import MetricUnit

metrics = Metrics(namespace="OrderService", service="order-processor")

@metrics.log_metrics(capture_cold_start_metric=True)
def handler(event, context):
    metrics.add_metric(name="OrderCount", unit=MetricUnit.Count, value=1)
    metrics.add_dimension(name="environment", value="prod")
```

EMF metrics appear in CloudWatch as standard custom metrics. No PutMetricData API call needed.

## Step 15: LLM/AI Observability

### GenAI Attributes Reference

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
        span.set_attribute("llm.latency_ms", latency_ms)
        span.set_attribute("llm.cost_usd", calculate_cost(model, response.usage))

        return response
```

### Prometheus LLM Queries

```promql
# p50 latency by model
histogram_quantile(0.5,
  sum(rate(gen_ai_latency_ms_bucket[5m])) by (le, gen_ai_request_model)
)

# Error rate by model
sum(rate(gen_ai_requests_total{status="error"}}[5m])) by (gen_ai_request_model)
/
sum(rate(gen_ai_requests_total[5m])) by (gen_ai_request_model)

# Hourly cost per model
sum(rate(gen_ai_usage_cost_usd_sum[1h])) by (gen_ai_request_model)
```

### LLM Observability Platform Comparison

| Platform | Open Source | Tracing | Cost Tracking | Self-Hosted | OTel Export |
|----------|-----------|---------|---------------|-------------|-------------|
| **LangSmith** | No | ✅ Full LLM call tree | ✅ Per-run token cost | No (SaaS) | ✅ OTLP |
| **Langfuse** | ✅ Apache-2.0 | ✅ Traces + sessions | ✅ Per-model cost | ✅ Docker/K8s | ✅ OTLP |
| **Helicone** | ✅ MIT | ✅ Request logging | ✅ Real-time dashboard | ✅ Docker | ❌ (proxied) |
| **Arize Phoenix** | ✅ Apache-2.0 | ✅ Full trace graph | ✅ Token + latency | ✅ Python | ✅ OTLP |

**Selection:** LangSmith for LangChain teams. Langfuse for multi-framework. Helicone for proxy-based capture. Arize Phoenix for eval-centric workflows.

### Cost Tracking

```python
MODEL_PRICING = {
    "gpt-4o":       {"input": 2.50 / 1_000_000, "output": 10.00 / 1_000_000},
    "gpt-4o-mini":  {"input": 0.15 / 1_000_000, "output": 0.60 / 1_000_000},
    "claude-3.5-sonnet": {"input": 3.00 / 1_000_000, "output": 15.00 / 1_000_000},
}

def calculate_cost(model: str, usage) -> float:
    pricing = MODEL_PRICING.get(model, MODEL_PRICING["gpt-4o"])
    return (usage.prompt_tokens * pricing["input"]
            + usage.completion_tokens * pricing["output"])
```

### Eval Metrics (Langfuse)

```python
from langfuse import Langfuse
langfuse = Langfuse()

langfuse.score(trace_id="trace-123", name="relevance", value=0.85)
langfuse.score(trace_id="trace-123", name="faithfulness", value=1, source="model:claude-3-sonnet")
```

**Key eval categories:** Relevance, Faithfulness (RAG grounding), Toxicity, Latency cost ratio.

### GenAI Agent/Tool Call Conventions

```python
# Agent span with tool calls
with tracer.start_as_current_span("agent.run") as agent_span:
    agent_span.set_attribute("gen_ai.agent.name", "code-assistant")
    agent_span.set_attribute("gen_ai.agent.max_iterations", 10)

    # Tool call event
    agent_span.add_event("gen_ai.tool.message", attributes={
        "gen_ai.role": "tool",
        "gen_ai.tool_call.id": "call_abc123",
        "gen_ai.tool_call.name": "search_docs",
        "gen_ai.tool_call.arguments": '{"query": "linked list reversal"}',
    })

    # Nested span for tool execution
    with tracer.start_as_current_span("tool.search_docs") as tool_span:
        tool_span.set_attribute("gen_ai.tool.name", "search_docs")
        tool_span.set_attribute("gen_ai.tool.type", "function")
        result = search_docs("linked list reversal")
        tool_span.set_attribute("gen_ai.tool.result_size", len(result))

    agent_span.set_attribute("gen_ai.agent.iterations", 3)
    agent_span.set_attribute("gen_ai.agent.total_tokens", 2500)
```

**Span naming conventions:**
```
"chat gpt-4o"                    # Chat completion
"embed text-embedding-3-small"   # Embeddings
"image dall-e-3"                 # Image generation
"text_completion gpt-3.5-turbo"  # Legacy completions
```

**Standard GenAI metrics (OTel):**
```yaml
gen_ai.client.token.usage:
  type: counter
  unit: "{token}"
  attributes: [gen_ai.system, gen_ai.request.model, gen_ai.operation.name]

gen_ai.client.operation.duration:
  type: histogram
  unit: "s"
  attributes: [gen_ai.system, gen_ai.request.model, gen_ai.response.finish_reason]

gen_ai.server.time_to_first_token:
  type: histogram
  unit: "s"
  description: "Time to first token in streaming response"
```

## Step 16: Edge Observability

### Cloudflare Workers Analytics Engine

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

**Combined pattern:**
```
[Worker] ──→ WAE (real-time dashboards, aggregation)
         ──→ Logpush (full request logs → S3 → Athena/BigQuery)
         ──→ OTLP over HTTP (sampled traces → Grafana Tempo)
```

## Step 17: OTel Collector Deployment Patterns

### Decision Matrix

| Pattern | Resource Cost | Config Flexibility | Tail Sampling | Recommended Scale |
|---------|--------------|-------------------|---------------|-------------------|
| Sidecar | High (N× pods) | Per-service | No (unless gateway) | < 50 services |
| DaemonSet | Medium (N× nodes) | Per-node | No (unless gateway) | 50-500 services |
| Gateway | Low overhead + gateway cost | Centralized | Yes | 100+ services |

**Hybrid (recommended for production):**
```
[App Pods] → [Agent Collector (daemonset)] → [Gateway Collector Cluster] → [Backends]
```

**DaemonSet agent config:**
```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: 0.0.0.0:4317
      http:
        endpoint: 0.0.0.0:4318
  hostmetrics:
    collection_interval: 15s
    scrapers:
      cpu:
      memory:
      disk:
      network:

processors:
  batch:
    timeout: 5s
    send_batch_size: 1000
  memory_limiter:
    check_interval: 1s
    limit_mib: 256
  k8sattributes:
    auth_type: serviceAccount
    extract:
      metadata:
        - k8s.pod.name
        - k8s.namespace.name
        - k8s.deployment.name

exporters:
  otlp:
    endpoint: "otel-gateway:4317"
    tls:
      insecure: true

service:
  pipelines:
    traces:
      receivers: [otlp]
      processors: [memory_limiter, k8sattributes, batch]
      exporters: [otlp]
    metrics:
      receivers: [otlp, hostmetrics]
      processors: [memory_limiter, k8sattributes, batch]
      exporters: [otlp]
```

**Gateway config (tail sampling, multi-backend):**
```yaml
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
        action: hash

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

**Load balancing exporter (traceID routing for tail sampling):**
```yaml
exporters:
  loadbalancing:
    protocol:
      otlp:
        tls:
          insecure: true
    resolver:
      dns:
        hostname: otel-gateway-headless.observability.svc.cluster.local
        port: 4317
    routing_key: traceID
```

## Step 18: Microservices Golden Signals

### Golden Signals (Google SRE)

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

### USE Method (Infrastructure)

| Resource | Utilization | Saturation | Errors |
|----------|-------------|------------|--------|
| CPU | `container_cpu_usage / limit` | `container_cpu_cfs_throttled_periods` | OOM kills |
| Memory | `container_memory_working_set / limit` | `container_memory_swap` | OOM kills |
| Disk | `node_disk_io_time_seconds` | `node_disk_io_queue_length` | `node_disk_io_errors_total` |
| Network | `container_network_transmit_bytes` | `TcpExt_TCPSynOverflow` | `node_network_receive_errs_total` |

### Service Dependency Mapping

**From traces (OTel):**
```promql
sum(rate(traces_spanmetrics_calls_total[5m])) by (service_name, span_name, status_code)
```

**From network flows (Cilium Hubble):**
```bash
hubble observe --output json --type l7 | \
  jq -r '[.flow.source.identity, .flow.destination.identity] | @tsv' | \
  sort | uniq -c | sort -rn
```

**Visualization tools:** Grafana Tempo service graph, Kiali (Istio), Hubble UI, Datadog Service Map.

## Step 19: Log Aggregation

### Comparison

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

**Loki LogQL advanced:**
```bash
# Pattern parsing
{service="api"} | pattern `<method> <path> <status> <duration_ms>ms` | duration_ms > 500
# Rate of errors per service
sum(rate({namespace="prod"} |= "error" [5m])) by (service)
```

## Step 20: Metrics Aggregation

### Recording Rules

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

### Long-Term Storage

| Solution | Architecture | Best For |
|----------|-------------|----------|
| **Thanos** | Sidecar + object storage | Multi-cluster, existing Prometheus |
| **Grafana Mimir** | Horizontally scalable TSDB | Large scale, Grafana-native |
| **VictoriaMetrics** | Drop-in Prometheus replacement | Cost-sensitive, single binary |

## Step 21: Alert Design Patterns

### Symptom vs Cause

| Type | What | Example | When to Page |
|------|------|---------|-------------|
| **Symptom-based** | User-visible impact | Error rate > 1% | Yes — always page |
| **Cause-based** | Internal condition | CPU > 80% | Maybe — supplementary |

**Principle:** Alert on symptoms (what users see), not causes (what broke).

### Alert Routing

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
      repeat_interval: 24h
```

### Escalation

| Level | Channel | Response Time |
|-------|---------|--------------|
| L1 (info) | Slack/email | Next business day |
| L2 (warn) | Slack + ticket | Within 4h |
| L3 (page) | PagerDuty | Within 15m |
| L4 (sev1) | PagerDuty + phone + bridge | Immediate |

**Anti-patterns:** Alerting on causes without symptoms. No runbook attached. Repeat interval too short. No grouping. Single threshold for all services.

## Step 22: Observability Maturity Model

### Crawl (Level 1) — "We can see what's broken"

| Capability | Status |
|-----------|--------|
| Metrics | Basic Prometheus/Grafana dashboards |
| Logs | Structured logging, centralized |
| Traces | Manual instrumentation on critical paths |
| Alerts | CPU/memory/disk thresholds |
| SLOs | None |
| Coverage | Core services only (5-10) |

### Walk (Level 2) — "We can predict and prevent"

| Capability | Status |
|-----------|--------|
| Metrics | Recording rules, standard dashboards per service |
| Logs | Trace correlation, log sampling |
| Traces | Auto-instrumented via OTel, tail-based sampling |
| Alerts | SLO-based burn-rate alerts, alert routing by team |
| SLOs | Defined per service (availability + latency) |
| Coverage | All production services (50+) |

### Run (Level 3) — "We understand system behavior"

| Capability | Status |
|-----------|--------|
| Metrics | Long-term storage (Mimir/Thanos), cross-cluster |
| Logs | Multi-surface (Loki + ClickHouse) |
| Traces | 100% tail-sampled, continuous profiling correlated |
| Alerts | Symptom-based, auto-escalation, runbook automation |
| SLOs | Error budget policy enforced |
| Coverage | All services + infrastructure + edge |

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

## Step 23: ML Model Monitoring

### Data Drift Detection

**KS Test (continuous features):**
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

### Concept Drift Detection

```python
from river import drift

# ADWIN (streaming)
adwin = drift.ADWIN(delta=0.002)
for i, prediction_error in enumerate(error_stream):
    adwin.update(prediction_error)
    if adwin.drift_detected:
        print(f"Drift detected at index {i}")
```

| Method | Type | Mechanism | Best For |
|--------|------|-----------|----------|
| KS Test | Data drift | Statistical test | Continuous features |
| PSI | Data drift | Binned distribution shift | Categorical, batch |
| ADWIN | Concept drift | Adaptive sliding window | Streaming |
| DDM | Concept drift | Error rate + std dev | Binary classification |

### Monitoring Tools

| Feature | Evidently | WhyLabs | NannyML |
|---------|-----------|---------|---------|
| Open Source | ✅ Apache-2.0 | ❌ | ✅ Apache-2.0 |
| Data Drift | ✅ KS, PSI, Wasserstein | ✅ Custom profiles | ✅ Univariate + multivariate |
| Target-free | ❌ | ✅ | ✅ CBPE |
| Dashboard | ✅ HTML reports | ✅ SaaS | ✅ HTML reports |

**Evidely example:**
```python
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset
from evidently.test_suite import TestSuite
from evidently.tests import TestShareOfDriftedColumns

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=ref_df, current_data=prod_df)
report.save_html("drift_report.html")

suite = TestSuite(tests=[TestShareOfDriftedColumns(lt=0.3)])
suite.run(reference_data=ref_df, current_data=prod_df)
assert suite.as_dict()["tests"][0]["status"] == "Success"
```

## Step 24: AI Agent Observability

### Agent Run Anatomy

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
        step = 0

        while not done:
            with tracer.start_as_current_span(f"agent.step.{step}") as step_span:
                step_span.set_attribute("agent.step_number", step)

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

                if should_call_tool(response):
                    with tracer.start_as_current_span("tool.call") as tool_span:
                        tool_span.set_attribute("tool.name", tool_name)
                        tool_span.set_attribute("tool.input", str(tool_input))
                        tool_result = execute_tool(tool_name, tool_input)
                        tool_span.set_attribute("tool.output", str(tool_result)[:500])

                step += 1

        root_span.set_attribute("agent.total_steps", step)
        root_span.set_attribute("agent.total_input_tokens", total_input_tokens)
        root_span.set_attribute("agent.total_output_tokens", total_output_tokens)
        root_span.set_attribute("agent.success", done_successfully)
```

### Agent PromQL

```promql
# Total agent run duration p99
histogram_quantile(0.99, sum(rate(agent_run_duration_seconds_bucket[5m])) by (le, agent_name))
# Time in LLM vs tool calls
sum(rate(agent_llm_duration_seconds_sum[5m])) by (agent_name)
# Average cost per run
sum(rate(agent_total_cost_usd_sum[1h])) by (agent_name) / sum(rate(agent_run_total[1h])) by (agent_name)
# Slowest tool calls
histogram_quantile(0.95, sum(rate(agent_tool_duration_seconds_bucket[5m])) by (le, tool_name))
```

### Agent Anti-Patterns

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| Logging only final output | Can't debug reasoning failures | Trace every step + tool call |
| No cost attribution | Surprise LLM bills | Tag every span with cost, alert on threshold |
| Infinite agent loops | Agent retries tool forever | Set max_steps, alert on step_count > N |
| Single monolithic span | Can't identify bottleneck | Separate spans for llm_call, tool_call |
| Missing timeout on LLM calls | Hung agent, wasted cost | Set per-call timeout, record timeout as error |

## Step 25: MLOps Observability

### MLflow Tracking

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
        "f1": 0.87,
        "precision": 0.89,
        "recall": 0.85,
    })
    mlflow.sklearn.log_model(model, artifact_path="model", registered_model_name="fraud-detection")
```

### Feature Store Monitoring

```python
from feast import FeatureStore
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset

store = FeatureStore(repo_path=".")

# Monitor feature freshness
feature_views = store.list_feature_views()
for fv in feature_views:
    alert_on_stale_features(fv.name, max_age_hours=24)

# Monitor feature drift
report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=training_features, current_data=serving_features)
report.save_html("feature_drift.html")
```

### Feature Store Alerts

```yaml
- alert: StaleFeatures
  expr: feature_store_last_materialization_age_hours > 24
  for: 1h
  labels:
    severity: page

- alert: FeatureDrift
  expr: feature_store_psi_score > 0.2
  for: 30m
  labels:
    severity: warn
```

### Model Registry Lineage

```
[Raw Data] → [Feature Pipeline] → [Feature Store] → [Training] → [Model Registry] → [Serving]
     │              │                    │              │              │                │
     └── data_version    └── feature_version  └── run_id      └── model_version  └── deployment_id
```

**MLflow lineage:**
```python
from mlflow import MlflowClient

client = MlflowClient("http://mlflow-server:5000")
model_version = client.get_model_version("fraud-detection", version=3)
print(f"Run ID: {model_version.run_id}")
print(f"Status: {model_version.status}")  # PENDING_REVIEW, READY, ARCHIVED

run = client.get_run(model_version.run_id)
print(f"Training data: {run.data.params.get('training_data_path')}")
print(f"Metrics: {run.data.metrics}")

# Transition model stage
client.transition_model_version_stage(
    name="fraud-detection", version=3, stage="Production",
    archive_existing_versions=True,
)
```

**Lineage OTel attributes:**

| Attribute | Description | Example |
|-----------|-------------|---------|
| `ml.pipeline.name` | Pipeline identifier | fraud-detection-v2 |
| `ml.model.name` | Registered model name | fraud-detection |
| `ml.model.version` | Model version | 3 |
| `ml.model.stage` | Registry stage | Production |
| `ml.data.version` | Training data version | dataset-2024-06 |
| `ml.metric.roc_auc` | Training metric | 0.95 |

## Step 26: Chaos Engineering

### Litmus (CNCF, K8s-native)

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
            - name: TOTAL_CHAOS_DURATION
              value: "30"
            - name: CHAOS_INTERVAL
              value: "10"
```

### Chaos Maturity Model

| Level | Name | Prerequisites |
|-------|------|---------------|
| 1 | Ad-hoc (staging only) | Basic monitoring |
| 2 | Exploratory (regular game days) | SLOs defined, rollback automation |
| 3 | Automated (CI/CD chaos) | Error budget policies, monitoring |
| 4 | Resilient (self-healing) | Full observability, automated remediation |

### Game Day Format (4-hour event)

```
00:00 - 00:30  Kickoff
  - Review steady-state hypothesis (SLIs)
  - Confirm monitoring dashboards open
  - Assign roles: Game Master, Observers, Responders

00:30 - 01:30  Round 1: Known failure modes
  - Kill primary database replica
  - Inject 500ms latency on payment service
  - Fill disk on logging node

01:45 - 02:45  Round 2: Unknown territory
  - Simulate region failure (DNS + failover)
  - Network partition between services
  - Certificate expiry on internal TLS

03:00 - 03:45  Round 3: Combined scenarios
  - Traffic spike + dependency failure
  - Deploy during partial outage

03:45 - 04:00  Final Debrief
  - What broke? What didn't?
  - Action items (fix monitoring, automation, architecture gaps)
  - Update runbooks and SLOs
```

**Roles:** Game Master (controls experiments), Observers (watch dashboards), Responders (act as on-call), Scribe (documents timeline).

## Step 27: Toil Reduction & Capacity Planning

### Toil (Google SRE)

Manual, repetitive, automatable, reactive, no lasting value. Target: < 50% of engineering time (< 30% mature orgs).

**Automation priority:**
```
High frequency × High duration = automate first (deployments, scaling)
High frequency × Low duration = automate second (ticket routing)
Low frequency  × High duration = automate third (disaster recovery)
Low frequency  × Low duration = document, skip automation
```

### Capacity Planning

```promql
# Linear regression: predict when disk hits 90%
predict_linear(node_filesystem_avail_bytes[30d], 90*24*3600) < 0

# Request rate growth
predict_linear(sum(rate(http_requests_total[7d]))[30d:], 30*24*3600)
```

**Capacity formula:**
```
provisioned = peak_load * (1 + headroom_factor) / (1 - failure_domain_fraction)
# headroom_factor: 0.15-0.30 (15-30% buffer)
# failure_domain_fraction: if N+1 redundancy across 3 zones = 1/3
```

**Signals to monitor:**
| Signal | Metric | Threshold |
|--------|--------|-----------|
| CPU saturation | container_cpu_cfs_throttled_periods | > 25% throttled |
| Memory pressure | container_memory_working_set / limit | > 80% |
| Disk I/O | node_disk_io_time_seconds | > 90% busy |
| Queue depth | kafka_consumer_group_lag | growing > 10%/hour |
| Connection pools | db_connections_active / max | > 80% |

## Step 28: Honeycomb Patterns

### Wide Events (80-200 fields)

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
  "k8s.zone": "us-east-1a"
}
```

**Why wide events?** Any field queryable without pre-aggregation. Post-hoc debugging on dimensions you didn't anticipate. High-cardinality fields (user_id, request_id) work fine.

### Bubble Up

1. Select spike on graph
2. Bubble Up auto-calculates which fields differ between "interesting" and "normal"
3. Ranked list of suspect fields by statistical significance
4. Click to drill down

### Observability vs Monitoring

| Aspect | Monitoring | Observability |
|--------|-----------|---------------|
| Question | "Is the system broken?" | "Why is it broken?" |
| Approach | Pre-defined dashboards + alerts | Exploratory queries on raw events |
| Data model | Aggregated metrics | Wide events (80-200 fields) |
| New questions | Requires new instrumentation | Ask immediately on existing data |
| Debugging | Known failure modes | Discover unknown unknowns |

### Honeycomb Query Patterns

```
# "Which endpoints are slow?"
VISUALIZATION: HEATMAP
CALCULATION: P95(duration_ms)
GROUP BY: http.path
WHERE: http.status_code < 500

# "Which users are affected by errors?"
VISUALIZATION: TABLE
CALCULATION: COUNT
GROUP BY: user.id, user.tier
WHERE: http.status_code >= 500

# "What changed between now and last week?"
VISUALIZATION: COMPARISON (compare two time ranges)
CALCULATION: P99(duration_ms)
GROUP BY: *

# "How does deploy version affect latency?"
VISUALIZATION: LINE
CALCULATION: AVG(duration_ms)
GROUP BY: deploy.version
WHERE: http.path = "/api/checkout"
```

**High-cardinality queries** (Honeycomb's differentiator):
```
GROUP BY: user.id              # millions of users
GROUP BY: request.id           # billions of requests
GROUP BY: http.request.header.x-request-id
GROUP BY: db.query             # full SQL text
```

## Step 29: Grafana LGTM Deep Dive

### Component Roles

| Component | Signal | Storage | Query Language |
|-----------|--------|---------|----------------|
| Mimir | Metrics | S3/GCS | PromQL |
| Loki | Logs | S3/GCS | LogQL |
| Tempo | Traces | S3/GCS | TraceQL |
| Pyroscope | Profiles | S3/GCS | ProfileQL |

### TraceQL Examples

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

### Loki Config (Production)

```yaml
auth_enabled: false
server:
  http_listen_port: 3100
common:
  ring:
    kvstore:
      store: inmemory
  replication_factor: 1
  storage:
    s3:
      endpoint: minio:9000
      bucketnames: loki-chunks
schema_config:
  configs:
    - from: 2024-01-01
      store: tsdb
      object_store: s3
      schema: v13
      index:
        prefix: index_
        period: 24h
compactor:
  working_directory: /loki/compactor
  compaction_interval: 10m
limits_config:
  max_query_series: 5000
  max_query_parallelism: 32
```

### Tempo Config (Production)

```yaml
server:
  http_listen_port: 3200
distributor:
  receivers:
    otlp:
      protocols:
        grpc:
          endpoint: 0.0.0.0:4317
        http:
          endpoint: 0.0.0.0:4318
metrics_generator:
  registry:
    external_labels:
      source: tempo
  storage:
    path: /var/tempo/generator/wal
    remote_write:
      - url: http://mimir:9009/api/v1/push
        send_exemplars: true
storage:
  trace:
    backend: s3
    s3:
      bucket: tempo-traces
      endpoint: s3.us-east-1.amazonaws.com
    wal:
      path: /var/tempo/wal
    local:
      path: /var/tempo/blocks
overrides:
  defaults:
    metrics_generator:
      processors: [service-graphs, span-metrics]
```

### Production Helm Deployment

```bash
helm repo add grafana https://grafana.github.io/helm-charts

# Mimir (metrics)
helm install mimir grafana/mimir-distributed -f values-mimir.yaml -n monitoring

# Loki (logs)
helm install loki grafana/loki -f values-loki.yaml -n monitoring

# Tempo (traces)
helm install tempo grafana/tempo-distributed -f values-tempo.yaml -n monitoring

# Pyroscope (profiles)
helm install pyroscope grafana/pyroscope -n monitoring

# Grafana (UI + correlation)
helm install grafana grafana/grafana -n monitoring \
  --set persistence.enabled=true \
  --set adminPassword=$GRAFANA_ADMIN_PW
```

### Grafana Alloy (Unified Agent)

Replaces Promtail, Grafana Agent, and OTel Collector for Grafana pipelines.

```alloy
// Collect logs from files
loki.source.file "app" {
  targets    = discovery.relabel.app_logs.output
  forward_to = [loki.write.default.receiver]
}

loki.write "default" {
  endpoint { url = "http://loki:3100/loki/api/v1/push" }
}

// Collect metrics (Prometheus scrape)
prometheus.scrape "api" {
  targets    = [{"__address__" = "api:8080"}]
  forward_to = [prometheus.remote_write.mimir.receiver]
}

prometheus.remote_write "mimir" {
  endpoint { url = "http://mimir:9009/api/v1/push" }
}

// Collect traces via OTLP
otelcol.receiver.otlp "default" {
  grpc { endpoint = "0.0.0.0:4317" }
  http { endpoint = "0.0.0.0:4318" }
  output { traces = [otelcol.exporter.otlp.tempo.input] }
}

otelcol.exporter.otlp "tempo" {
  client {
    endpoint = "tempo:4317"
    tls { insecure = true }
  }
}
```

### Mimir vs Thanos

| Aspect | Mimir | Thanos |
|--------|-------|--------|
| Architecture | Microservices | Sidecar + Compactor + Store |
| Multi-tenant | Native (X-Scope-OrgID) | Manual (separate buckets) |
| Query path | Single endpoint | Query + Store Gateway |
| Best for | Green-field, Grafana-native | Existing Prometheus + sidecar |

### Cross-Signal Correlation

```
Metric spike (Mimir)
    → click exemplar → Trace view (Tempo)
    → click span → Logs for that span (Loki)
    → click service → Metrics for that service (Mimir)
```

**Grafana datasource correlation config:**
```yaml
apiVersion: 1
datasources:
  - name: Tempo
    type: tempo
    uid: tempo
    url: http://tempo:3200
    jsonData:
      tracesToLogsV2:
        datasourceUid: loki
        filterByTraceID: true
        filterBySpanID: true
        tags: ['service']
      tracesToMetrics:
        datasourceUid: prometheus
        queries:
          - name: "Request rate"
            query: "sum(rate(http_requests_total{$$__tags}[5m]))"
      serviceMap:
        datasourceUid: prometheus

  - name: Loki
    type: loki
    uid: loki
    url: http://loki:3100
    jsonData:
      derivedFields:
        - datasourceUid: tempo
          matcherRegex: "trace_id=(\\w+)"
          name: TraceID
          url: "$${__value.raw}"

  - name: Mimir
    type: prometheus
    uid: prometheus
    url: http://mimir:9009
    jsonData:
      exemplarTraceIdDestinations:
        - name: trace_id
          datasourceUid: tempo
```

### SpanMetrics Connector (RED from spans)

```yaml
connectors:
  spanmetrics:
    histogram:
      explicit:
        buckets: [5ms, 10ms, 25ms, 50ms, 100ms, 250ms, 500ms, 1s, 5s]
    dimensions:
      - name: http.method
      - name: http.status_code
    metrics_flush_interval: 15s

service:
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [otlp, spanmetrics]
    metrics:
      receivers: [spanmetrics]
      exporters: [prometheusremotewrite]
```

### Cost Optimization

```
100GB logs/day  → Loki (S3)     ~$200/mo
10M active series → Mimir (S3)  ~$300/mo
500GB traces/day → Tempo (S3)   ~$400/mo
Total: ~$900/mo vs SaaS $5000-15000/mo
```

## Step 30: Monitoring as Code

### Terraform Grafana Provider

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
    model = jsonencode({
      expr = "sum(rate(http_requests_total{code=~\"5..\"}[5m])) / sum(rate(http_requests_total[5m]))"
    })
  }
}
```

### Alerting Rules with Alertmanager

```yaml
groups:
  - name: slo_alerts
    rules:
      - alert: HighErrorBudgetBurn
        expr: |
          slo:error_ratio:rate5m > (14.4 * (1 - 0.999))
          and
          slo:error_ratio:rate1h > (14.4 * (1 - 0.999))
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Error budget burning at 14.4x rate"
          runbook_url: "https://wiki/runbooks/error-budget-burn"

# alertmanager.yml
route:
  receiver: default
  group_by: ['alertname', 'service']
  routes:
    - match:
        severity: critical
      receiver: pagerduty-critical
      repeat_interval: 1h

inhibit_rules:
  - source_match:
      severity: critical
    target_match:
      severity: warning
    equal: ['alertname', 'service']
```

### promtool Validation

```bash
promtool check rules recording_rules.yml
promtool check rules alerting_rules.yml
promtool test rules test_cases.yml
```

## Step 31: Incident Response Automation

### Alert → Incident → Runbook Flow

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

### Webhook Handler

```python
from fastapi import FastAPI, Request
import httpx

app = FastAPI()

@app.post("/webhook/alertmanager")
async def handle_alertmanager(request: Request):
    payload = await request.json()
    for alert in payload.get("alerts", []):
        if alert["status"] == "firing":
            severity = alert["labels"].get("severity", "warning")
            if severity == "critical":
                await create_incident(alert)
                await page_oncall(alert)
            elif severity == "warning":
                await notify_slack(alert)
    return {"status": "ok"}
```

## Step 32: eBPF Deep Dive

### Cilium Hubble

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

### Falco (Runtime Security)

```yaml
- rule: Shell in Container
  desc: Detect shell started in container
  condition: >
    spawned_process and container and
    proc.name in (bash, sh, zsh, csh) and
    not proc.pname in (init, containerd-shim)
  output: >
    Shell spawned in container
    (user=%user.name container=%container.name shell=%proc.name)
  priority: CRITICAL
```

```bash
helm install falco falcosecurity/falco \
  --namespace falco --create-namespace \
  --set driver.kind=ebpf \
  --set falcosidekick.enabled=true \
  --set falcosidekick.config.slack.webhookurl="$SLACK_WEBHOOK"
```

### eBPF Tool Comparison

| Tool | Focus | Instrumentation | Query Lang |
|------|-------|-----------------|-----------|
| Cilium | Network (L3-L7) | Kernel network | Hubble CLI |
| Hubble | Network flows | eBPF + Cilium | Observers |
| Pixie | Full-stack APM | eBPF + kprobe | PxL |
| Falco | Runtime security | Syscall (eBPF) | Rules YAML |
| Tetragon | Security + observ | eBPF + kprobe | JSON filters |

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

### Common Commands

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

# Grafana API: create dashboard
curl -X POST http://admin:admin@localhost:3000/api/dashboards/db \
  -H "Content-Type: application/json" \
  -d '{"dashboard": {...}, "overwrite": true}'

# Hubble observe
hubble observe --namespace production --verdict dropped --follow
hubble observe --protocol http --to-pod production/api

# Cilium status
cilium status
cilium encrypt status
```

### SLO Definition Template

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
    fast_burn:
      rate: 14.4
      window: 1h
      severity: page
    slow_burn:
      rate: 6
      window: 6d
      severity: ticket
  error_budget_policy:
    budget_remaining_gt_50: "Ship features at normal pace"
    budget_remaining_20_50: "Slow down, prioritize reliability"
    budget_remaining_lt_20: "Freeze non-critical deploys"
    budget_exhausted: "Full deployment freeze"
```

### Observability Stack Sizing

| Scale | Metrics Series | Logs/day | Traces/day | Recommended |
|-------|---------------|----------|------------|-------------|
| Small (< 10 services) | < 100K | < 10GB | < 50GB | Single Prometheus + Loki + Jaeger |
| Medium (10-100 services) | 100K-1M | 10-100GB | 50-500GB | Mimir + Loki + Tempo (microservices) |
| Large (100+ services) | 1M-10M | 100GB-1TB | 500GB-5TB | Mimir + Loki + Tempo (scaled) + Gateway Collector |
| Very Large (1000+ services) | 10M+ | 1TB+ | 5TB+ | Multi-region Mimir/Loki/Tempo + dedicated teams |
