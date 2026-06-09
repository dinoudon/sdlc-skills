---
name: sdlc-prd-to-production
description: "End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective. Ship/Show/Ask, design docs, PRD patterns, GitOps, metrics-driven development, production readiness, launch strategies, incident management, blameless postmortems."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, prd, design-doc, rfc, ship-show-ask, workflow, end-to-end, product-development, dora, metrics-driven, production-readiness, incident-management, blameless-postmortems]
    related_skills: [sdlc-requirements-engineering, sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-deployment, sdlc-retrospective]
---

# PRD to Production

End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective.

## When to Use

Trigger when user:
- Starts a new feature from scratch
- Writes PRD or design doc
- Plans implementation workflow
- Defines branching strategy for a feature
- Reviews end-to-end process

## Step 1: PRD (Product Requirements Document)

### PRD-lite (One-Pager)

For small/medium features. Problem + Solution + Success.

```
# [Feature Name]

## Problem
[One paragraph: what's broken or missing]

## Solution
[One paragraph: what we'll build]

## Success
[How we know it worked: metric + target]

## Out of Scope
[What we're explicitly NOT building]
```

### Full PRD Structure (for complex features)

```
1. Overview / Problem Statement
   - What problem are we solving? For whom? Why now?
2. Goals & Success Metrics
   - Measurable outcomes. OKRs if applicable.
3. User Stories / Scenarios
   - "As a [user], I want [capability] so that [outcome]."
4. Requirements
   - Functional: what it must do.
   - Non-functional: performance, security, accessibility.
5. Scope / Out of Scope
   - Explicit boundaries. What NOT building.
6. Design / UX
   - Wireframes, user flows, interaction notes.
7. Technical Considerations
   - Dependencies, constraints, API contracts.
8. Milestones / Timeline
   - Phases, release plan.
9. Open Questions
   - Unresolved decisions.
10. Appendix / References
```

### Fast Iteration Rules

- No feature > 2 weeks in first cut
- Ship with instrumentation, not just functionality
- Every PRD has a kill metric — precommit to abandoning if wrong
- v0.1 scope = absolute minimum to learn
- Iterate weekly based on real data, not opinions

## Step 2: Design Doc / RFC

### Design Doc Template

Source: https://www.industrialempathy.com/posts/design-docs-at-google/

```
1. Authors
2. Status (Draft / In Review / Approved / Superseded)
3. Background / Context — Why are we doing this?
4. Goals & Non-Goals — Explicit non-goals prevent scope creep.
5. Overview — High-level approach, one paragraph.
6. Detailed Design — Architecture, data models, APIs, sequence diagrams, error handling, edge cases.
7. Alternatives Considered — What else was evaluated? Why rejected?
8. Cross-Cutting Concerns — Security, privacy, monitoring, logging, i18n.
9. Operations — Rollout plan, feature flags, rollback strategy.
10. Risks & Tradeoffs
11. Open Questions
12. Milestones / Timeline
13. Appendix
```

### Observability & SLO Sections (add to any design doc)

```
14. Observability Requirements
    a. Metrics: [RED/USE metrics to emit]
    b. Logs: [structured format, levels, correlation IDs]
    c. Traces: [span boundaries, propagation strategy]
    d. Dashboards: service health, business metrics, infrastructure
    e. Alerting: burn-rate alerts, anomaly detection, runbook links

15. SLO Definition
    a. SLIs — e.g., availability: 99.9%, latency p99 < 250ms
    b. SLOs — e.g., 99.9% availability over 30-day window
    c. Error Budget — e.g., 0.1% = 43.2 min downtime/month
       Policy: what happens when budget exhausted
    d. Dependencies — upstream/downstream SLO constraints
```

### RFC Process

1. Author writes RFC doc
2. Shared with stakeholders (Slack, doc link, PR)
3. Review period (1-5 business days)
4. Reviewers comment inline, author addresses
5. Final approver signs off (tech lead or architect)
6. RFC becomes source of truth for implementation

RFC = process-oriented (review/approval workflow). Design Doc = content-oriented (technical detail). Many orgs use both interchangeably.

## Step 3: Ship / Show / Ask Branching

Source: https://martinfowler.com/articles/ship-show-ask.html

| Category | When | Process |
|----------|------|---------|
| **SHIP** | Tiny changes: typos, config tweaks, doc-only | Merge directly to main. No PR. Trust author. |
| **SHOW** | Small, low-risk changes that benefit from eyes | Merge to main immediately. Open PR after for async review. |
| **ASK** | Architecture changes, new deps, security-sensitive, public API | Open PR, wait for review and approval before merge. |

**Why:** Not all changes need same review rigor. Reduces PR bottlenecks. Keeps focus on high-signal reviews.

**Implementation:**
- Define team agreement: which changes fall in each bucket
- SHIP: author self-merges. Log it.
- SHOW: merge first, PR after. Bot can enforce opening PR.
- ASK: standard branch protection rules. Require approvals.

## Step 4: Implementation Workflow

```
PRD Written
    │
    ▼
Design Doc / RFC
    │
    ▼
Implementation
    ├── Ship/Show/Ask classification per change
    ├── CI runs (lint, test, build)
    ├── Code review per classification
    └── Feature flags for progressive rollout
    │
    ▼
Testing & QA
    ├── Unit tests (fast, isolated)
    ├── Integration tests (module boundaries)
    ├── E2E tests (critical user flows)
    └── Staging environment validation
    │
    ▼
Deployment
    ├── Canary or progressive rollout
    ├── Monitoring, alerting
    └── Rollback plan ready
    │
    ▼
Monitoring & Observability
    ├── SLIs/SLOs defined
    ├── Error budgets tracked
    └── Dashboards created
    │
    ▼
Retrospective
    ├── What went well?
    ├── What could improve?
    └── 1-3 action items
```

## Step 5: Code Review

**Checklist:**
- [ ] PR < 400 LOC (or justified)
- [ ] Design reviewed (right approach?)
- [ ] Security reviewed (OWASP Top 10)
- [ ] Tests present and passing
- [ ] Comments explain WHY
- [ ] No hardcoded secrets

Google SLA: respond within 4 hours.

## Step 6: Testing

**Checklist:**
- [ ] Unit tests for business logic
- [ ] Integration tests for module boundaries
- [ ] E2E tests for critical flows
- [ ] Coverage > 80%
- [ ] No flaky tests

## Step 7: Deployment

**Checklist:**
- [ ] Canary or progressive rollout
- [ ] Health checks configured
- [ ] Rollback plan ready
- [ ] Monitoring and alerting active
- [ ] Feature flags for gradual rollout

## Step 8: Monitoring

**Checklist:**
- [ ] SLIs defined (availability, latency)
- [ ] SLOs set (99.9%, p99 < 250ms)
- [ ] Error budgets tracked
- [ ] Dashboards created
- [ ] Alerts configured (burn-rate based)

## Step 9: Retrospective

**Checklist:**
- [ ] Format chosen (Start/Stop/Continue, 4Ls, Sailboat)
- [ ] Prime Directive read
- [ ] 1-3 action items with owners
- [ ] Previous actions reviewed

## End-to-End Checklist

```
□ PRD written (problem, goals, stories, requirements, scope)
□ Design doc / RFC reviewed and approved
□ Implementation plan (tasks, timeline, feature flags)
□ Code review (Ship/Show/Ask classification)
□ Tests (unit, integration, e2e)
□ Staging validation
□ Deployment plan (canary, rollback)
□ Monitoring (SLIs, SLOs, dashboards, alerts)
□ Retrospective (action items with owners)
□ Documentation updated
□ Stakeholders notified
```

## Step 10: Ephemeral Environments

PR-based preview environments: PR opened → CI builds image → ArgoCD syncs namespace (pr-123.staging.example.com) → PR merged → namespace deleted.

**Tools comparison:**

| Feature | Argo CD AppSets | Garden.io | Uffizzi |
|---------|----------------|-----------|---------|
| Cluster needed | Yes | Yes | No (SaaS) |
| Config complexity | Medium | Low | Low |
| Full-stack support | Manual overlays | Built-in | Docker Compose |
| Best for | K8s-native teams | Multi-service dev | OSS/small teams |
| Cost | Self-hosted | Self-hosted/paid | SaaS free tier |

**Argo CD ApplicationSets (PR Generator) — key config:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: pr-preview
spec:
  generators:
    - pullRequest:
        github:
          owner: myorg
          repo: myapp
          tokenRef:
            secretName: github-token
            key: token
  template:
    metadata:
      name: 'pr-{{number}}'
    spec:
      source:
        repoURL: https://github.com/myorg/myapp
        targetRevision: '{{head_sha}}'
        path: k8s/overlay/preview
      destination:
        namespace: 'pr-{{number}}'
```

## DORA 2024 Insights

Source: https://dora.dev/research/2024/

**Key findings:**
- **Documentation quality** is a strong predictor of organizational performance (2x+ better delivery)
- **AI coding assistants** show moderate productivity gains; strongest for boilerplate, tests, docs; weakest for architecture, complex debugging
- **Risk:** over-reliance on AI without code review degrades quality

**Where AI helps most:** code review pre-screening, test generation, doc drafting, incident triage.
**Where AI hurts:** blind acceptance without understanding, AI-generated tests that don't test meaningful behavior.

**AI integration points in this workflow:**
```
PRD:         AI assists with user story expansion, acceptance criteria
Design Doc:  AI reviews for completeness, suggests missing sections
Code Review: AI pre-reviews for style/security BEFORE human review
Testing:     AI generates test scaffolds, humans validate quality
Monitoring:  AI triages alerts, suggests runbook actions
```

## GitOps Workflow

Source: https://opengitops.dev/

```
PRD → Design Doc → Code → PR → CI → Image → Git Repo → ArgoCD/Flux → Cluster
```

All infrastructure and app config lives in Git. No kubectl apply in CI.

**GitOps principles:**
1. Declarative: desired state described declaratively in Git
2. Versioned: Git is single source of truth, full audit trail
3. Automated: agents pull and reconcile, CI never pushes to cluster
4. Self-healing: drift detected and corrected automatically

**Repo structure:**
```
environments/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   └── service.yaml
├── staging/
│   ├── kustomization.yaml
│   └── patches/replicas.yaml
└── production/
    ├── kustomization.yaml
    └── patches/
        ├── replicas.yaml
        └── resources.yaml
```

**ArgoCD Application:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: orders-api-production
  namespace: argocd
spec:
  source:
    repoURL: https://github.com/myorg/k8s-configs
    targetRevision: main
    path: environments/production
  destination:
    namespace: orders
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

## Progressive Delivery

```
Canary → 5% traffic → 20% → 50% → 100%
         ↓ (failure)         ↓ (failure)
      Auto-rollback       Auto-rollback
```

**Flagger Canary:**
```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: orders-api
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: orders-api
  analysis:
    interval: 30s
    threshold: 5          # max failed checks before rollback
    maxWeight: 50
    stepWeight: 10
    metrics:
      - name: request-success-rate
        thresholdRange:
          min: 99
      - name: request-duration
        thresholdRange:
          max: 500        # rollback if p99 > 500ms
```

**Rollback triggers:**
1. Error rate > SLO threshold → immediate rollback
2. Latency p99 > threshold for 2+ analysis windows → rollback
3. Health check failures > 3 consecutive → rollback

**Manual rollback (GitOps):** `git revert <commit>` → push → ArgoCD syncs.

## Metrics-Driven Development

### SLI-First Development

Define what to measure before writing implementation.

| SLI | Measurement | Target (SLO) |
|-----|-------------|--------------|
| Availability | successful requests / total | 99.9% |
| Latency (p99) | request duration at 99th pct | < 250ms |
| Correctness | correct orders / total orders | 99.99% |
| Freshness | time from event to visibility | < 5s |

### Instrumentation During Implementation

Every PR should include instrumentation alongside features:
- [ ] SLI metrics emitted (latency, error rate, throughput)
- [ ] Structured logs with correlation IDs
- [ ] Trace spans for new operations
- [ ] Dashboard panels defined
- [ ] Alert thresholds set based on SLO

**OpenTelemetry example:**
```python
from opentelemetry import metrics, trace

meter = metrics.get_meter("orders-service")
request_duration = meter.create_histogram("http_request_duration_ms", unit="ms")
order_counter = meter.create_counter("orders_processed_total")
tracer = trace.get_tracer("orders-service")

def handle_order(req):
    with tracer.start_as_current_span("handle_order") as span:
        span.set_attribute("order.customer_id", req.customer_id)
        start = time.time()
        try:
            result = process_order(req)
            order_counter.add(1, {"result": "success"})
            return result
        except Exception as e:
            order_counter.add(1, {"result": "error"})
            span.set_status(trace.Status(trace.StatusCode.ERROR, str(e)))
            raise
        finally:
            duration = (time.time() - start) * 1000
            request_duration.record(duration, {"method": "POST", "path": "/orders"})
```

### SLO-Based Alerting

```yaml
# Prometheus burn-rate alert (multi-window)
groups:
  - name: orders-slo
    rules:
      - alert: OrdersHighErrorBurnRate
        expr: |
          (
            1 - (sum(rate(http_requests_total{service="orders",status=~"2.."}[1h]))
            / sum(rate(http_requests_total{service="orders"}[1h])))
          ) > (14.4 * 0.001)
          and
          (
            1 - (sum(rate(http_requests_total{service="orders",status=~"2.."}[5m]))
            / sum(rate(http_requests_total{service="orders"}[5m])))
          ) > (14.4 * 0.001)
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Orders API error budget burning 14.4x faster than allowed"
          runbook: "https://wiki/runbooks/orders-high-error-rate"
```

## Production Readiness Review (PRR)

Gate before production launch. Every item must be signed off.

### Reliability
- [ ] Health check endpoints (`/healthz`, `/readyz`)
- [ ] Graceful shutdown handles in-flight requests
- [ ] Retry logic with exponential backoff + jitter
- [ ] Circuit breakers on critical dependencies
- [ ] Idempotency keys on write operations

### Scalability
- [ ] Load test at 2x expected peak traffic
- [ ] Horizontal autoscaling (HPA/KEDA)
- [ ] Database connection pooling sized
- [ ] Caching strategy defined
- [ ] Resource requests and limits set

### Observability
- [ ] RED metrics for every endpoint
- [ ] USE metrics for every resource
- [ ] Structured logging with correlation IDs
- [ ] Distributed tracing spans
- [ ] Dashboards + alerts with runbook links

### Security
- [ ] AuthN/AuthZ verified on every endpoint
- [ ] Input validation, output encoding (OWASP Top 10)
- [ ] Secrets via vault — no hardcoded credentials
- [ ] Dependency scan passed (no critical CVEs)
- [ ] TLS enforced end-to-end
- [ ] Rate limiting configured

### Rollback
- [ ] Rollback procedure documented and tested
- [ ] DB migrations backward-compatible (works with N-1 code)
- [ ] Feature flag kill switch tested
- [ ] Rollback time < 5 min from decision to restored state

**PRR Sign-off:**

| Area | Owner | Status | Date |
|------|-------|--------|------|
| Reliability | [eng lead] | ☐ | |
| Scalability | [eng lead] | ☐ | |
| Observability | [sre lead] | ☐ | |
| Security | [sec lead] | ☐ | |
| Rollback | [eng lead] | ☐ | |

## Launch Strategies

| Strategy | When | Risk |
|----------|------|------|
| **Soft Launch** | New features, uncertain impact | Low — gradual exposure |
| **Hard Launch** | Internal tools, low-risk, regulatory deadline | High — full exposure |
| **Dark Launch** | Testing under real load, migration dry-runs | Low — invisible to users |
| **Feature Flag** | Decouple deploy from release | Low — instant disable |

**Soft Launch rollout:**
```
Day 1:  Internal dogfooding (employees only)
Day 3:  1% of traffic
Day 5:  5% of traffic
Day 7:  25% of traffic
Day 10: 100% of traffic
```
Only advance if error rate and latency within SLO at each stage.

**Dark Launch pattern:**
```python
def handle_request(req):
    old_result = old_handler(req)
    try:
        new_result = new_handler(req)
        emit_metric("dark_launch.compare", old_result == new_result)
    except Exception:
        emit_metric("dark_launch.error", 1)
    return old_result  # user always sees old behavior
```

### Feature Flags

| Tool | Type | Best for |
|------|------|----------|
| LaunchDarkly | SaaS | Enterprise, multi-platform |
| Unleash | OSS/SaaS | Self-hosted, cost-sensitive |
| Flagsmith | OSS/SaaS | Simple flag management |
| Custom (env vars) | Point | Small teams, few flags |

**Flag lifecycle:** Create → Implement behind flag → Ship (OFF) → Enable (INTERNAL → BETA → % → ALL) → Monitor → Remove flag

**Flag hygiene rules:**
- Every flag has owner and expiry date
- Stale flags (> 90 days) trigger cleanup alert
- Never nest flags
- Flag changes are auditable

## Post-Launch Monitoring (First 72 Hours)

**Technical metrics:**

| Metric | Threshold | Action |
|--------|-----------|--------|
| Error rate | > 1% | Investigate, prepare rollback |
| Latency p99 | > SLO for 5+ min | Check deps, scale or rollback |
| CPU | > 80% sustained | Scale horizontally |
| Memory | > 85% sustained | Check for leaks |
| Queue depth | > 1000 or growing | Scale consumers |
| Restart count | > 0 unexpected | Check logs |

**Business metrics (hourly first 24h):**

| Metric | What to watch |
|--------|---------------|
| Conversion rate | Drop = UX regression |
| Adoption rate | New feature usage vs eligible |
| Support tickets | Spike = confusion or bugs |

**Rollback decision matrix:**
```
P0 (service down):        Auto-rollback + war room + incident process
P1 (degraded, many users): Manual rollback within 15 min
P2 (edge case, few users):  Feature flag disable + fix forward
P3 (cosmetic, no data loss):  Fix forward in next release
```

## Incident Management

Source: PagerDuty Incident Response, Google SRE book.

### Severity Levels

| Level | Definition | Response | Examples |
|-------|-----------|----------|----------|
| SEV1 | Service down, data loss, security breach | < 5 min, update every 30 min | Production down, data breach |
| SEV2 | Major feature broken, significant impact | < 15 min, update every 1 hr | Checkout broken, API errors > 5% |
| SEV3 | Minor degradation, workaround exists | < 1 hr, update every 4 hr | Slow page load, minor UI bug |
| SEV4 | Cosmetic, no user impact | Next business day | Typo, dev env issue |

### Response Lifecycle

```
1. DETECT — Acknowledge alert (< 5 min), open incident channel
2. RESPOND — IC assigned, severity declared, roles assigned
3. TRIAGE — Blast radius, root cause hypothesis, check recent deploys
4. MITIGATE — Stop bleeding (rollback, flag off, scale up). Document actions.
5. RESOLVE — Metrics normal, status page updated, stakeholders notified
6. REVIEW — Postmortem within 48h (blameless), action items tracked
```

**Incident Commander (IC) role:**
- Coordinates responders, communicates status, makes tactical decisions
- Does NOT fix the problem (coordinator, not debugger)
- Transfer IC if deep investigation needed

**Incident channel message:**
```
🚨 INCIDENT — SEV[X] — [Brief Description]
**Status:** Investigating
**Impact:** [What's broken, who's affected]
**IC:** @[name]
**Started:** [timestamp]
Updates every [15 min for SEV1/2, 30 min for SEV3].
```

**MTTx Metrics:**
| Metric | Definition | SEV1 Target |
|--------|-----------|-------------|
| MTTD | Mean Time to Detect | < 5 min |
| MTTA | Mean Time to Acknowledge | < 5 min |
| MTTR | Mean Time to Resolve | < 30 min |
| MTTM | Mean Time to Mitigate | < 15 min |

## Blameless Postmortems

Sources: Google SRE Book Ch.15, Etsy/John Allspaw.

### Triggers

- User-visible downtime > 5 minutes
- Data loss (any amount)
- On-call paged (any severity)
- Manual intervention required
- Required >2 people to resolve
- Requested by any team member

### Postmortem Template

```markdown
# Postmortem: {Title}

| Field | Value |
|-------|-------|
| Date | YYYY-MM-DD |
| Severity | SEV{X} |
| Duration | {start} → {end} ({duration}) |
| Impact | {users affected, revenue impact} |
| IC | {name} |

## Summary
{2-3 sentence summary}

## Timeline (UTC)
| Time | Event |
|------|-------|
| HH:MM | {event} |

## Root Cause
{Technical explanation}

## Contributing Factors
- {Factor 1}

## What Went Well
- {Thing 1}

## What Went Poorly
- {Thing 1}

## Action Items
| # | Action | Owner | Due Date | Priority |
|---|--------|-------|----------|----------|
| 1 | {action} | @name | YYYY-MM-DD | P1 |

## Lessons Learned
- {Lesson 1}
```

### Blamelessness Principles

1. Assume everyone acted with best intentions given what they knew
2. Focus on SYSTEM causes, not individual mistakes
3. Replace "X made a mistake" with "the system allowed X to happen"
4. Key questions:
   - "What information did the person have at the time?"
   - "Why did that action seem reasonable?"
   - "What would have made the right action obvious?"
5. NEVER use postmortem findings in performance reviews
6. Facilitated debriefs — facilitator redirects blame language

**Anti-pattern:** "John deployed the broken code"
**Blameless:** "The deployment pipeline lacked automated canary analysis"

**5 Whys at system level:**
```
Why did the site go down? → DB ran out of connections
Why? → New feature opened connections, didn't close them
Why didn't we catch it? → No connection pool monitoring in staging
Why? → Monitoring setup is manual
Why is it manual? → No investment in monitoring-as-code

ACTION: automate monitoring setup (systemic fix)
NOT: "train developers to set up monitoring" (blame person)
```

### Postmortem Process

- Written within 48h of incident
- 48h cool-down before meeting (emotions, perspective)
- Reviewed in team meeting within 1 week
- Action items tracked to completion
- Quarterly: review all postmortems for systemic patterns
- Leadership models blamelessness publicly

## Pitfalls

1. **Don't skip PRD** — even one-pager prevents scope creep
2. **Don't skip design doc for complex features** — technical decisions need documentation
3. **Don't use ASK for trivial changes** — Ship/Show/Ask reduces bottlenecks
4. **Don't skip feature flags** — decouple deploy from release
5. **Don't skip retrospective** — continuous improvement requires reflection
6. **Don't skip monitoring setup** — deploy without observability is blind
7. **Don't skip rollback plan** — always have a way back
8. **Don't skip stakeholder communication** — keep everyone informed
