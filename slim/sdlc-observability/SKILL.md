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
