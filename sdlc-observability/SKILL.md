---
name: sdlc-observability
description: "Observability stack: structured logging (ELK/Loki), metrics (Prometheus/Grafana), distributed tracing (OpenTelemetry/Jaeger), incident management (PagerDuty), post-mortems. Includes Google SRE practices."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, observability, logging, metrics, tracing, opentelemetry, prometheus, grafana, sre, google]
    related_skills: [sdlc-deployment, sdlc-testing-qa, sdlc-cicd-pipeline]
---

# Observability & Incident Management

Logging, metrics, distributed tracing, alerting, incident response, post-mortems. Includes Google SRE practices (SLIs/SLOs/error budgets).

## When to Use

Trigger when user:
- Sets up logging, metrics, or tracing
- Configures OpenTelemetry, Prometheus, Grafana, ELK, Loki
- Handles incidents (PagerDuty, Opsgenie)
- Writes post-mortems or runbooks
- Sets up alerting or SLOs

## Step 1: Structured Logging

### Principles
- JSON format, always
- Correlation IDs across services
- Log levels: DEBUG, INFO, WARN, ERROR, FATAL
- Never log PII, passwords, tokens

### Python (structlog)
```python
import structlog
log = structlog.get_logger()
log.info("user_login", user_id="123", method="oauth2", duration_ms=45)
```

### Node.js (Pino)
```javascript
const pino = require('pino');
const logger = pino({ level: 'info' });
logger.info({ userId: '123' }, 'user login');
```

### Log Tools
| Tool | Best For |
|------|----------|
| ELK Stack | Full-text search |
| Loki (Grafana) | Label-based, cheaper |
| Fluent Bit | Log forwarding |
| Datadog Logs | All-in-one |

## Step 2: Metrics

### RED Method (for services)
- **R**ate — requests per second
- **E**rrors — error rate
- **D**uration — latency (p50, p95, p99)

### USE Method (for resources)
- **U**tilization — % used
- **S**aturation — queue depth
- **E**rrors — error count

### Prometheus
```yaml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'myapp'
    static_configs:
      - targets: ['myapp:8080']
```

### Prometheus Client (Python)
```python
from prometheus_client import Counter, Histogram, start_http_server

REQUEST_COUNT = Counter('http_requests_total', 'Total requests', ['method', 'endpoint', 'status'])
REQUEST_DURATION = Histogram('http_request_duration_seconds', 'Duration', ['method', 'endpoint'])

start_http_server(9090)
```

### Alerting
```yaml
# prometheus-rules.yml
groups:
- name: myapp
  rules:
  - alert: HighErrorRate
    expr: sum(rate(http_requests_total{code=~"5.."}[5m])) / sum(rate(http_requests_total[5m])) > 0.01
    for: 5m
    labels:
      severity: critical
    annotations:
      summary: "High error rate"
```

## Step 3: Distributed Tracing

### OpenTelemetry
```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor

provider = TracerProvider()
provider.add_span_processor(BatchSpanProcessor(OTLPSpanExporter(endpoint="otel-collector:4317")))
trace.set_tracer_provider(provider)
FastAPIInstrumentor.instrument_app(app)
```

### Auto-instrumentation CLI
```bash
opentelemetry-instrument python app.py
node --require @opentelemetry/auto-instrumentations-node app.js
```

### Jaeger
```bash
docker run -d --name jaeger -p 16686:16686 -p 4317:4317 jaegertracing/all-in-one
```

## Step 4: Incident Management

### Tools
| Tool | Type |
|------|------|
| PagerDuty | On-call scheduling, escalation |
| Opsgenie (Atlassian) | Similar to PagerDuty |
| Grafana OnCall | Open-source |
| Firehydrant / Rootly | Incident orchestration |

### Incident Response Flow
1. **Alert fires** → PagerDuty
2. **On-call acks** within SLA (5 min for SEV1)
3. **Declare incident**, assign roles (IC, comms, ops)
4. **Mitigate first** (rollback, feature flag, scale up)
5. **Communicate** (Slack #incidents, status page)
6. **Resolve**, close incident
7. **Post-mortem** within 48-72h

### PagerDuty CLI
```bash
pd incident list --status triggered
pd incident ack INC-123
pd incident resolve INC-123
```

## Step 5: Post-Mortems

### Template
```markdown
# [Service] [Impact] — [Date]

## Summary
- Severity: SEV1/SEV2/SEV3
- Duration: X hours Y minutes
- Impact: N users affected

## Timeline (UTC)
- HH:MM — [Event]

## Root Cause
[Technical explanation]

## Detection
[Alert vs customer report]

## Resolution
[What fixed it]

## Action Items
- [ ] @person — [prevention] by [date]

## Lessons Learned
- [Blameless observations]
```

### Blameless Culture
- Focus on systems, not individuals
- "What allowed this to happen?" not "Who caused this?"

## Step 6: SRE Practices (from Google SRE Book)

### SLIs, SLOs, SLAs

**SLI** = what you measure
```
SLI = good events / total events
Example: successful requests / total requests
```

**SLO** = target for SLI
```
SLO = 99.9% availability (43.8 min downtime/month)
```

**SLA** = contractual SLO with consequences

### Error Budget
```
Error Budget = 1 - SLO
SLO 99.9% → Error Budget = 0.1% = 43.8 min/month

If error budget exhausted:
  → Feature velocity STOPS
  → Team focuses on reliability
```

### Error Budget Policy
```markdown
## Error Budget Policy

### When budget > 50% remaining
- Normal feature development

### When budget 25-50% remaining
- Increased testing, canary mandatory

### When budget < 25% remaining
- Feature freeze, reliability only

### When budget exhausted
- Full freeze, post-mortem for all incidents
```

### Toil Elimination
**Toil** = manual, repetitive, automatable, reactive work

Rule: SREs spend max 50% on ops. Rest on engineering.

### Incident Severity (Google)
| Level | Description | Response |
|-------|-------------|----------|
| SEV1 | User-facing outage | Immediate (15 min) |
| SEV2 | Degraded service | 1 hour |
| SEV3 | Non-critical | Next business day |
| SEV4 | Minor | Sprint backlog |

### Incident Commander
- IC owns the incident, doesn't fix it
- IC delegates: comms, ops, debugging leads
- IC makes decisions: rollback, escalate, communicate
- IC writes post-mortem

## Pitfalls

1. **Don't log at DEBUG in production** — use dynamic log level
2. **Don't alert on everything** — alert on symptoms (user impact)
3. **Don't skip distributed tracing** — debugging cross-service is blind
4. **Don't write post-mortems weeks later** — memory fades
5. **Don't assign blame** — blameless culture prerequisite
6. **Don't forget SLOs** — define SLIs, set targets, track error budget
7. **Don't ignore toil** — track and eliminate systematically
