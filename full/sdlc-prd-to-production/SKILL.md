---
name: sdlc-prd-to-production
description: "End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective. Ship/Show/Ask, design docs, PRD patterns, ephemeral environments, DORA 2024, technical specs, GitOps, metrics-driven development, production readiness, launch strategies, incident management, blameless postmortems."
version: 4.8.0-moderate
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

### PRD Structure
Source: https://www.atlassian.com/software/jira/guides/use-cases/what-is-a-prd

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

### PRD-lite (One-Pager)
For small features. Problem + Solution + Success.

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

### YC-Style Fast Iteration PRD
Source: Paul Graham's "Launch Fast" + YC playbook.

Lean PRD for speed-to-market. Ship v0.1, measure, iterate.

```
# [Feature Name] — v0.1 Launch Spec

## Hypothesis
[One sentence: what we believe users need]

## Minimum Viable Slice
[Smallest thing that tests the hypothesis. 1-2 weeks max.]

## Kill Metric
[Metric that proves hypothesis wrong. If X < Y after Z days, pivot.]

## Success Metric
[Metric that proves hypothesis right. If X > Y, double down.]

## Measurement Plan
[What to instrument from day 1. Events, funnels, cohorts.]

## Iteration Backlog
[Post-v0.1 ideas — only populated after first measurement]
```

**Rules:**
- No feature > 2 weeks in first cut
- Ship with instrumentation, not just functionality
- Every PRD has a kill metric — precommit to abandoning if wrong
- v0.1 scope = absolute minimum to learn
- Iterate weekly based on real data, not opinions

### Amazon Working Backwards Press Release
Source: Amazon press release FAQ method.

Write the press release FIRST. Work backwards to requirements.

```
# Press Release: [Product/Feature Name]

## Headline
[Customer-benefit-focused headline]

## Sub-headline
[One sentence: who benefits and how]

## Problem
[Paragraph: the pain point, in customer language]

## Solution
[Paragraph: how the product solves it, in customer language]

## Quote from Spokesperson
[What the PM/CEO would say. Customer empathy, not tech.]

## Customer Experience
[Step-by-step: what the customer does, sees, feels]

## Call to Action
[What the customer does next]

# FAQ (Internal)

## Customer FAQ
Q: [Anticipated customer questions]
A: [Answers in plain language]

## Internal FAQ
Q: Why build this now?
Q: What's the tech approach?
Q: What are the dependencies?
Q: What's the cost/effort?
Q: What are the risks?
```

**Rules:**
- If the press release is boring, the product is boring — rewrite
- FAQ section surfaces hidden assumptions early
- Share press release with customers before building
- Customer language only — no jargon in PR section

## Step 2: Design Doc / RFC

### Google Design Doc Template
Source: https://www.industrialempathy.com/posts/design-docs-at-google/

```
1. Authors
2. Status (Draft / In Review / Approved / Superseded)
3. Background / Context
   - Why are we doing this? What is the problem?
4. Goals & Non-Goals
   - Explicit non-goals prevent scope creep.
5. Overview
   - High-level approach, one paragraph.
6. Detailed Design
   - Architecture, data models, APIs, sequence diagrams.
   - Include error handling, edge cases.
7. Alternatives Considered
   - What else was evaluated? Why rejected?
8. Cross-Cutting Concerns
   - Security, privacy, monitoring, logging, i18n.
9. Operations
   - Rollout plan, feature flags, rollback strategy.
10. Risks & Tradeoffs
11. Open Questions
12. Milestones / Timeline
13. Appendix
```

### Enhanced Design Doc: Observability & SLO Sections

Add these sections to any design doc for production-grade features:

```
14. Observability Requirements
    a. Instrumentation Plan
       - Metrics: [which RED/USE metrics to emit]
       - Logs: [structured log format, levels, correlation IDs]
       - Traces: [span boundaries, propagation strategy]
    b. Dashboards
       - Service health dashboard (latency, error rate, throughput)
       - Business metrics dashboard (conversion, adoption)
       - Infrastructure dashboard (CPU, memory, disk, network)
    c. Alerting
       - Burn-rate alerts (SLO-based)
       - Anomaly detection alerts
       - Runbook links for each alert

15. SLO Definition
    a. SLIs (Service Level Indicators)
       - Availability: [e.g., ratio of successful requests]
       - Latency: [e.g., p99 < 250ms for /api/v1/orders]
       - Throughput: [e.g., 10K requests/sec sustained]
       - Correctness: [e.g., 99.99% of orders processed correctly]
    b. SLOs (Service Level Objectives)
       - [e.g., 99.9% availability over 30-day window]
       - [e.g., p95 latency < 100ms, p99 < 250ms]
    c. Error Budget
       - Budget: [e.g., 0.1% = 43.2 min downtime/month]
       - Policy: what happens when budget exhausted
       - Burn rate thresholds: [e.g., alert at 2x burn rate]
    d. Dependencies
       - Upstream SLOs that constrain this service's SLO
       - Downstream SLOs this service constrains
```

**Why this matters:**
- Forces teams to think about production behavior before writing code
- SLOs create shared language between eng and product
- Error budgets enable data-driven release velocity decisions
- Observability as first-class design concern, not afterthought

### RFC (Request for Comments) Process
Source: https://github.com/reactjs/rfcs

1. Author writes RFC doc
2. Shared with stakeholders (Slack channel, doc link, PR)
3. Review period (typically 1-5 business days)
4. Reviewers comment inline. Author addresses.
5. Final approver signs off (usually tech lead or architect)
6. RFC becomes source of truth for implementation

**RFC vs Design Doc:**
- RFC: process-oriented, emphasizes review/approval workflow
- Design Doc: content-oriented, emphasizes technical detail
- Many orgs use both terms interchangeably

## Step 3: Ship / Show / Ask Branching

Source: https://martinfowler.com/articles/ship-show-ask.html

Three categories for branch merging decisions:

### SHIP
- Merge directly to main. No PR, no review needed.
- For: tiny changes, typo fixes, config tweaks, doc-only.
- Trust the author. Speed matters here.

### SHOW
- Merge to main immediately. Open PR afterward for visibility/async review.
- For: small, low-risk changes that still benefit from eyes.
- "Show, don't ask." Review happens after merge.

### ASK
- Open PR, wait for review and approval before merging.
- For: architectural changes, new dependencies, security-sensitive code, public API changes.
- Standard PR workflow. No shortcuts.

### Why This Matters
- Not all changes need same review rigor
- Reduces PR bottlenecks on trivial changes
- Keeps focus on high-signal reviews (ASK items)
- Maintains trunk-based development velocity
- Works with feature flags for safe production deploys

### Implementation
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

See `sdlc-adversarial-review` for full review process.

**Quick checklist:**
- [ ] PR < 400 LOC (or justified)
- [ ] Design reviewed (right approach?)
- [ ] Security reviewed (OWASP Top 10)
- [ ] Tests present and passing
- [ ] Comments explain WHY
- [ ] No hardcoded secrets

**Google code review SLA:** respond within 4 hours.

## Step 6: Testing

See `sdlc-testing-qa` for full testing process.

**Quick checklist:**
- [ ] Unit tests for business logic
- [ ] Integration tests for module boundaries
- [ ] E2E tests for critical flows
- [ ] Coverage > 80%
- [ ] No flaky tests

## Step 7: Deployment

See `sdlc-deployment` for full deployment strategies.

**Quick checklist:**
- [ ] Canary or progressive rollout
- [ ] Health checks configured
- [ ] Rollback plan ready
- [ ] Monitoring and alerting active
- [ ] Feature flags for gradual rollout

## Step 8: Monitoring

See `sdlc-observability` for full observability process.

**Quick checklist:**
- [ ] SLIs defined (availability, latency)
- [ ] SLOs set (99.9%, p99 < 250ms)
- [ ] Error budgets tracked
- [ ] Dashboards created
- [ ] Alerts configured (burn-rate based)

## Step 9: Retrospective

See `sdlc-retrospective` for full retrospective formats.

**Quick checklist:**
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

## Step 10: Platform Engineering

### Internal Developer Platforms (IDP)

Source: https://tag-app-delivery.cncf.io/whitepapers/platforms/

IDP = self-service abstraction layer between infra and devs. Reduces cognitive load.

**Key components:**
- Service catalog (what exists, who owns it)
- Self-service provisioning (databases, queues, envs)
- Golden path templates (best practices built-in)
- Observability portal (logs, metrics, traces linked to services)
- RBAC + policy-as-code (OPA/Kyverno gates)

### Backstage (Spotify)
Source: https://backstage.io/docs/

CNCF Incubating. Developer portal + scaffolder + catalog.

**Core features:**
- **Software Catalog:** register all services, APIs, data pipelines
- **Software Templates:** scaffolder creates new services from templates
- **TechDocs:** docs-as-code rendered in portal
- **Plugins:** 100+ community plugins (K8s, ArgoCD, Grafana, PagerDuty)

Source: https://github.com/backstage/backstage

### Ephemeral Environments

PR-based preview environments:

```
1. PR opened → CI builds image, tags with PR number
2. ApplicationSet PR generator detects new PR
3. Renders Application pointing to PR-specific overlay
4. ArgoCD syncs new namespace (pr-123.myapp.staging.example.com)
5. PR merged → namespace deleted, Application removed
```

**Tools:** Argo CD ApplicationSets, Garden.io, Uffizzi, Loft vCluster

#### Argo CD ApplicationSets (PR Generator)
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
        requeueAfterSeconds: 30
  template:
    metadata:
      name: 'pr-{{number}}'
    spec:
      project: default
      source:
        repoURL: https://github.com/myorg/myapp
        targetRevision: '{{head_sha}}'
        path: k8s/overlay/preview
      destination:
        server: https://kubernetes.default.svc
        namespace: 'pr-{{number}}'
  templatePatch: |
    spec:
      source:
        helm:
          parameters:
            - name: ingress.host
              value: 'pr-{{number}}.preview.example.com'
```

#### Garden.io
Source: https://docs.garden.io/

Define full stack in `garden.yml`. Spin up dev/preview envs per PR.

```yaml
# garden.yml
kind: Deploy
name: api
type: container
dependencies: [build-api]
spec:
  image: ${actions.build.api.outputs.deploymentImageId}
  ports:
    - name: http
      containerPort: 8080
  ingresses:
    - path: /
      port: http
  env:
    DATABASE_URL: postgres://...
```

#### Uffizzi
Source:***@v2
    with:
      compose-file: docker-compose.uffizzi.yml
    permissions:
      contents: read
      pull-requests: write
```

**Comparison:**

| Feature | Argo CD AppSets | Garden.io | Uffizzi |
|---|---|---|---|
| Cluster needed | Yes (existing) | Yes (existing) | No (SaaS) |
| Config complexity | Medium | Low | Low |
| Full-stack support | Manual overlays | Built-in | Docker Compose |
| Best for | K8s-native teams | Multi-service dev | OSS/small teams |
| Cost | Self-hosted | Self-hosted/paid | SaaS free tier |

## DORA 2024 Insights

Source: https://dora.dev/research/2024/

### Documentation Quality
- Documentation quality is a **strong predictor** of organizational performance
- Teams with high doc quality have 2x+ better software delivery performance
- Doc quality = accuracy, findability, freshness, completeness
- Docs-as-code (in repo, reviewed in PRs) correlates with better outcomes

### AI-Augmented Development
- AI coding assistants (Copilot, etc.) show **moderate productivity gains**
- Gains strongest for: boilerplate, tests, docs, unfamiliar languages
- Gains weakest for: architecture decisions, complex debugging, novel logic
- **Risk:** over-reliance on AI without code review degrades quality
- **Recommendation:** AI augments review, not replaces it. Pair AI output with human judgment.

### AI-Augmented Development Workflow (Expanded)
Source: DORA 2024 — AI usage across the SDLC, not just code generation.

**Where AI helps most (DORA findings):**
1. **Code review:** AI flags style issues, security patterns, test gaps before human review
2. **Test generation:** AI generates test scaffolds, edge cases, property-based tests
3. **Documentation:** AI drafts PR descriptions, changelogs, API docs from diffs
4. **Incident response:** AI triages alerts, suggests root cause, drafts postmortems

**Where AI hurts (anti-patterns):**
- Blind acceptance of AI-generated code without understanding
- AI-generated tests that pass but don't test meaningful behavior
- Over-reliance reducing developer skill atrophy
- AI suggestions that introduce subtle bugs in concurrent/security code

**Recommended AI integration points in this workflow:**

```
PRD Step:         AI assists with user story expansion, acceptance criteria
Design Doc:       AI reviews for completeness, suggests missing sections
Implementation:  AI generates boilerplate, suggests patterns
Code Review:      AI pre-reviews for style/security/smell BEFORE human review
Testing:          AI generates test scaffolds, humans validate coverage quality
Deployment:       AI generates deployment configs, validates against policy
Monitoring:       AI triages alerts, suggests runbook actions
Retrospective:    AI summarizes PR data, identifies patterns in reviews
```

**AI review integration pattern:**
```yaml
# .github/workflows/ai-review.yml
name: AI Pre-Review
on: [pull_request]
jobs:
  ai-review:
    runs-on: ubuntu-latest
    steps:
      - uses: ai-reviewer/action@v2
        with:
          focus: security,performance,test-coverage
          # AI reviews first, human reviewer sees AI comments
          mode: pre-review
          # Do NOT auto-approve — always require human sign-off
          auto-approve: false
```

**Metrics to track AI impact:**
- PR cycle time (before/after AI adoption)
- Defect escape rate (are AI-reviewed PRs higher quality?)
- Review comment density (are humans finding fewer issues?)
- Test coverage delta (is AI generating meaningful tests?)

### Key Takeaways for This Workflow
1. Write docs in the PRD/Design Doc step — treat them as first-class deliverables
2. Use AI for code generation in implementation, but enforce Ship/Show/Ask review
3. Track doc freshness as a team metric (stale docs = tech debt)
4. Invest in doc tooling (backstage TechDocs, Docusaurus, mkdocs)

## Score Spec (Workload Specification)

Source: https://score.dev/

Score is platform-agnostic workload spec. Defines what workload needs, not how platform provides it.

```yaml
# score.yaml
apiVersion: score.dev/v1b1
metadata:
  name: orders-api
  annotations:
    score.dev/description: "Orders API service"

containers:
  main:
    image: ${resources.container-image}
    variables:
      PORT: "8080"
      DATABASE_URL: ${resources.db.connection-string}
      REDIS_URL: ${resources.cache.connection-string}
    files:
      - target: /app/config.yaml
        content: |
          log_level: info
    resources:
      limits:
        memory: "512Mi"
        cpu: "500m"

service:
  ports:
    http:
      port: 8080

resources:
  container-image:
    type: container-image
  db:
    type: postgres
  cache:
    type: redis
```

**Why Score:**
- One spec, many targets (K8s, Docker Compose, Nomad, PaaS)
- Separates "what I need" from "how platform provides it"
- Enables self-service: devs declare needs, platform fulfills
- Works with Humanitec, score-compose, score-k8s

**Integration with this workflow:**
- PRD defines business requirements
- Design Doc defines technical approach + observability + SLOs
- Score spec defines workload resource requirements
- Platform team maps Score resources to actual infra

## PRD-to-Production Automation Patterns

### GitOps Workflow

Source: https://opengitops.dev/

```
PRD → Design Doc → Code → PR → CI → Image → Git Repo → ArgoCD/Flux → Cluster

All infrastructure and app config lives in Git. No kubectl apply in CI.
ArgoCD/Flux watches Git, reconciles cluster state to desired state.
```

**GitOps principles:**
1. Declarative: desired state described declaratively in Git
2. Versioned: Git is single source of truth, full audit trail
3. Automated: agents pull and reconcile, CI never pushes to cluster
4. Self-healing: drift detected and corrected automatically

**GitOps repo structure:**
```
environments/
├── base/
│   ├── kustomization.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   └── hpa.yaml
├── staging/
│   ├── kustomization.yaml
│   └── patches/
│       └── replicas.yaml
└── production/
    ├── kustomization.yaml
    └── patches/
        ├── replicas.yaml
        └── resources.yaml
```

**ArgoCD Application (GitOps):**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: orders-api-production
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/myorg/k8s-configs
    targetRevision: main
    path: environments/production
  destination:
    server: https://kubernetes.default.svc
    namespace: orders
  syncPolicy:
    automated:
      prune: true          # delete resources removed from Git
      selfHeal: true       # revert manual cluster changes
    syncOptions:
      - CreateNamespace=true
```

### Progressive Delivery

Source: https://fluxcd.io/flux/flagger/

```
Canary → 5% traffic → 20% → 50% → 100%
         ↓ (failure)         ↓ (failure)
      Auto-rollback       Auto-rollback
```

**Flagger Canary (progressive delivery with automated rollback):**
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
  progressDeadlineSeconds: 600
  service:
    port: 8080
  analysis:
    # Canary promotion criteria
    interval: 30s
    threshold: 5          # max failed checks before rollback
    maxWeight: 50         # max traffic percentage for canary
    stepWeight: 10        # traffic increment per step
    metrics:
      - name: request-success-rate
        thresholdRange:
          min: 99         # rollback if success rate < 99%
        interval: 1m
      - name: request-duration
        thresholdRange:
          max: 500        # rollback if p99 > 500ms
        interval: 30s
    webhooks:             # optional: run tests before promotion
      - name: integration-tests
        type: pre-rollout
        url: http://flagger-loadtester.test/
        timeout: 120s
        metadata:
          cmd: "hey -z 1m -q 10 -c 2 http://orders-api-canary.orders:8080/"
```

### Automated Rollback Patterns

```
## Rollback Triggers
1. Error rate > SLO threshold → immediate rollback
2. Latency p99 > threshold for 2+ analysis windows → rollback
3. Health check failures > 3 consecutive → rollback
4. Integration test failure in canary → rollback

## Rollback Process (Flagger)
1. Flagger detects metric violation
2. Flagger shifts traffic back to primary (100%)
3. Flagger scales canary replica to 0
4. Alert fires to team with reason

## Manual Rollback (GitOps)
1. `git revert <commit>` on k8s-configs repo
2. Push to main
3. ArgoCD syncs automatically
4. Cluster returns to previous state
```

**Blue-Green variant:**
```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: orders-api
spec:
  analysis:
    interval: 1m
    iterations: 5        # run 5 checks before switching
    metrics:
      - name: request-success-rate
        thresholdRange:
          min: 99
  strategy:
    blueGreen:           # instead of canary percentage
      activeService: orders-api-active
      previewService: orders-api-preview

## Metrics-Driven Development

Source: Google SRE book — define SLIs before writing code.

### SLI-First Development

**Pattern: define what to measure before writing implementation.**

```
# In design doc (Step 2), before any code:

## SLI Definitions
| SLI              | Measurement                    | Target (SLO)     |
|------------------|--------------------------------|-------------------|
| Availability     | successful requests / total    | 99.9%             |
| Latency (p99)    | request duration at 99th pct   | < 250ms           |
| Correctness      | correct orders / total orders  | 99.99%            |
| Freshness        | time from event to visibility  | < 5s              |

## Instrumentation Plan
- Add: http_request_duration_ms histogram in handler
- Add: http_requests_total counter with status label
- Add: order_processed_total counter with result label
- Emit from day 1 of implementation, not after launch
```

### Instrumentation During Implementation

**Every PR should include instrumentation alongside features:**

```
# Pattern: instrument in the same PR as the feature

## PR checklist addition:
- [ ] SLI metrics emitted (latency, error rate, throughput)
- [ ] Structured logs with correlation IDs
- [ ] Trace spans for new operations
- [ ] Dashboard panels defined (or link to existing)
- [ ] Alert thresholds set based on SLO
```

**OpenTelemetry instrumentation example:**
```python
from opentelemetry import metrics, trace

meter = metrics.get_meter("orders-service")
request_duration = meter.create_histogram(
    "http_request_duration_ms",
    description="Request duration in milliseconds",
    unit="ms",
)
order_counter = meter.create_counter(
    "orders_processed_total",
    description="Total orders processed",
)

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
            1 - (
              sum(rate(http_requests_total{service="orders",status=~"2.."}[1h]))
              /
              sum(rate(http_requests_total{service="orders"}[1h]))
            )
          ) > (14.4 * 0.001)
          and
          (
            1 - (
              sum(rate(http_requests_total{service="orders",status=~"2.."}[5m]))
              /
              sum(rate(http_requests_total{service="orders"}[5m]))
            )
          ) > (14.4 * 0.001)
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Orders API error budget burning 14.4x faster than allowed"
          runbook: "https://wiki/runbooks/orders-high-error-rate"
```

### Metrics Review in Retrospective

```
## Production Readiness Review (PRR)

Gate before production launch. Every item must be signed off by owner.

### Reliability
- [ ] Health check endpoints defined and tested (`/healthz`, `/readyz`)
- [ ] Graceful shutdown handles in-flight requests (drain period configured)
- [ ] Retry logic with exponential backoff + jitter on all external calls
- [ ] Circuit breakers on critical downstream dependencies
- [ ] Bulkhead isolation — failure in one path doesn't cascade
- [ ] Idempotency keys on all write operations
- [ ] Chaos testing results reviewed (pod kill, network partition, latency injection)

### Scalability
- [ ] Load test completed at 2x expected peak traffic
- [ ] Horizontal autoscaling configured (HPA/KEDA) with tested thresholds
- [ ] Database connection pooling sized for max concurrent connections
- [ ] No single-threaded bottlenecks in request path
- [ ] Caching strategy defined (L1/L2/invalidation policy)
- [ ] Queue depth monitoring and backpressure handling
- [ ] Resource requests and limits set in container spec

### Observability
- [ ] RED metrics emitted for every endpoint (Rate, Errors, Duration)
- [ ] USE metrics emitted for every resource (Utilization, Saturation, Errors)
- [ ] Structured logging with correlation IDs (trace_id, request_id)
- [ ] Distributed tracing spans cover full request path
- [ ] Dashboard exists: service health, business metrics, infrastructure
- [ ] Alerts defined with runbook links for every SLO
- [ ] Log retention policy documented and tested

### Security
- [ ] AuthN/AuthZ verified on every endpoint
- [ ] Input validation and output encoding applied (OWASP Top 10)
- [ ] Secrets managed via vault/K8s secrets — no hardcoded credentials
- [ ] Dependency vulnerability scan passed (no critical/high CVEs)
- [ ] TLS enforced end-to-end (no plaintext in transit)
- [ ] Rate limiting and abuse protection configured
- [ ] Data classification reviewed (PII handling, encryption at rest)
- [ ] Security review signed off by security team

### Rollback
- [ ] Rollback procedure documented and tested in staging
- [ ] Database migrations are backward-compatible (works with N-1 code)
- [ ] Feature flag kill switch tested (instant disable, no deploy needed)
- [ ] Rollback time measured: target < 5 min from decision to restored state
- [ ] GitOps revert tested (`git revert` + ArgoCD/Flux sync)
- [ ] Data migration rollback plan (if schema changes involved)

**PRR Sign-off:**
```
| Area          | Owner       | Status | Date       |
|---------------|-------------|--------|------------|
| Reliability   | [eng lead]  | ☐      |            |
| Scalability   | [eng lead]  | ☐      |            |
| Observability | [sre lead]  | ☐      |            |
| Security      | [sec lead]  | ☐      |            |
| Rollback      | [eng lead]  | ☐      |            |
```

## Launch Strategies

### Soft Launch (Gradual Rollout)
Deploy to production with limited exposure. Measure before scaling.

```
Day 1: Internal dogfooding (employees only)
Day 3: 1% of traffic (random sampling or cohort)
Day 5: 5% of traffic
Day 7: 25% of traffic
Day 10: 100% of traffic
```

**When to use:** new features with uncertain impact, user-facing changes.
**Metrics gate:** only advance if error rate and latency within SLO at each stage.

### Hard Launch (Big Bang)
Full release to all users at once. Immediate full exposure.

```
Deploy → 100% traffic immediately
```

**When to use:** internal tools, low-risk changes, regulatory requirements (must ship by date).
**Risk mitigation:** feature flag as kill switch, staged rollback plan, war room active.

### Dark Launch
Code deployed to production, receives real traffic, but results invisible to users.

```
Production traffic → Feature processes request
                   → Result logged/metrics emitted
                   → User sees OLD behavior (shadow response discarded)
```

**When to use:** testing performance under real load, validating new algorithms, migration dry-runs.
**Implementation:**
```python
def handle_request(req):
    old_result = old_handler(req)
    try:
        new_result = new_handler(req)
        emit_metric("dark_launch.compare", old_result == new_result)
        emit_metric("dark_launch.latency", new_duration)
    except Exception:
        emit_metric("dark_launch.error", 1)
    return old_result  # user always sees old behavior
```

### Feature Flag Rollout
Decouple deployment from release. Ship code to production behind flag, enable selectively.

```
Flag states:
  OFF          → code deployed but unreachable
  INTERNAL     → employees only
  BETA         → opted-in users / beta cohort
  PERCENTAGE   → X% of random users
  ON           → all users
  KILL_SWITCH  → emergency disable (no deploy)
```

**Flag lifecycle:**
1. Create flag in flag system (LaunchDarkly, Unleash, Flagsmith, point solution)
2. Implement feature behind flag check
3. Ship to production (flag OFF by default)
4. Enable per strategy above (internal → beta → % → all)
5. Monitor metrics at each stage
6. Once stable: remove flag, clean up code paths

**Flag hygiene rules:**
- Every flag has an owner and expiry date
- Stale flags (> 90 days) trigger cleanup alert
- Never nest flags (flag-in-flag = untestable complexity)
- Flag changes are auditable (who toggled, when, why)

**Tool comparison:**

| Tool | Type | Best for |
|------|------|----------|
| LaunchDarkly | SaaS | Enterprise, multi-platform |
| Unleash | OSS/SaaS | Self-hosted, cost-sensitive |
| Flagsmith | OSS/SaaS | Simple flag management |
| Custom (env vars) | Point | Small teams, few flags |

## Post-Launch Monitoring

### Key Metrics to Watch (First 72 Hours)

**Technical metrics (continuous):**
| Metric | Threshold | Action if breached |
|--------|-----------|-------------------|
| Error rate | > 1% of requests | Investigate immediately, prepare rollback |
| Latency p99 | > SLO target for 5+ min | Check dependencies, scale or rollback |
| Latency p50 | > 2x baseline | Profile, check resource contention |
| CPU utilization | > 80% sustained | Scale horizontally, check for leaks |
| Memory utilization | > 85% sustained | Check for memory leaks, scale |
| Queue depth | > 1000 or growing | Check consumer health, scale consumers |
| Disk I/O | > 80% of provisioned IOPS | Scale storage, optimize queries |
| Restart count | > 0 unexpected | Check logs for crash reason |

**Business metrics (hourly for first 24h, then daily):**
| Metric | What to watch |
|--------|-------------|
| Conversion rate | Drop vs baseline = UX regression |
| Adoption rate | New feature usage vs eligible users |
| Engagement | Session duration, pages per session |
| Support tickets | Spike = user confusion or bugs |
| Revenue impact | For commerce features, immediate delta |

### Anomaly Detection

**Automated detection approaches:**
1. **Static thresholds** — alert if metric crosses fixed value (simple, noisy)
2. **Standard deviation** — alert if value > 3σ from rolling mean (adaptive)
3. **Seasonal decomposition** — account for daily/weekly patterns (best for traffic)
4. **ML-based** — train on 30 days of data, detect deviations (fewest false positives)

**Minimum viable anomaly detection:**
```yaml
# Prometheus recording rule: 3σ anomaly detection
- record: http_error_rate_anomaly
  expr: |
    (
      rate(http_requests_total{status=~"5.."}[5m])
      / rate(http_requests_total[5m])
    ) > (
      avg_over_time(http_error_rate[7d]) + 3 * stddev_over_time(http_error_rate[7d])
    )
```

### Rollback Triggers

Automatic rollback if ANY of these hit:
1. Error rate > 2x baseline for 5 minutes
2. p99 latency > SLO target for 10 minutes
3. Health check failures > 3 consecutive
4. Canary analysis threshold exceeded (Flagger)
5. Business metric drop > 10% from baseline (conversion, revenue)

Manual rollback consideration:
6. Spike in support tickets (> 3x normal rate)
7. Security alert triggered by new code path
8. Data integrity issue detected (duplicate records, missing data)

**Rollback decision matrix:**
```
Impact + Urgency → Action

P0 (service down):        Auto-rollback + war room + incident process
P1 (degraded, many users): Manual rollback within 15 min + notify stakeholders
P2 (edge case, few users):  Feature flag disable + fix forward
P3 (cosmetic, no data loss):  Fix forward in next release
```

## Stakeholder Communication Templates

### Launch Announcement

```
Subject: [Launch] [Feature Name] is live in production

Hi team,

[Feature Name] is now live for [audience: all users / beta users / X%].

## What changed
[2-3 sentence summary of what the feature does and why it matters]

## What to watch for
[Known limitations, expected behavior changes, things that look different]

## Links
- Dashboard: [link]
- Documentation: [link]
- Rollback plan: [link]
- Feature flag: [link to flag system]

## Support
- On-call: [name/handle]
- Escalation: [name/handle]

Questions? Reach out in #[channel].
```

### Status Update (During Rollout)

```
Subject: [Status] [Feature Name] rollout — [stage]

## Current status: [ON TRACK / AT RISK / BLOCKED]

## Rollout progress
- [x] Internal dogfooding (completed [date])
- [x] 1% rollout (completed [date])
- [→] 5% rollout (in progress, started [date])
- [ ] 25% rollout
- [ ] 100% rollout

## Metrics
- Error rate: [X%] (target: < 1%) — [OK / ELEVATED / CRITICAL]
- Latency p99: [Xms] (target: < Yms) — [OK / ELEVATED / CRITICAL]
- Adoption: [X% of eligible users] — [ON TRACK / BELOW EXPECTATIONS]

## Issues
- [Issue 1]: [status, owner, ETA]

## Next milestone
[What's next and when]
```

### Incident Communication

```
Subject: [INCIDENT-SEV[X]] [Feature Name] — [brief description]

## Status: [INVESTIGATING / IDENTIFIED / MONITORING / RESOLVED]

## Impact
- Who is affected: [all users / segment / region]
- What is broken: [symptom description]
- Since when: [timestamp]
- Current error rate: [X%]

## Timeline
- [HH:MM] Issue detected via [alert/dashboard]
- [HH:MM] Investigation started. [finding]
- [HH:MM] Root cause identified: [cause]
- [HH:MM] Mitigation applied: [action taken]

## Current action
[What's being done right now]

## Next update
[When the next status update will be sent]

## Incident commander: [name]
```

### Post-Incident Summary (for Stakeholders)

```
Subject: [Post-Incident] [Feature Name] — [date]

## Summary
[1 paragraph: what happened, impact, resolution]

## Impact
- Duration: [X hours Y minutes]
- Users affected: [count/percentage]
- Revenue impact: [if applicable]

## Root cause
[Technical explanation, simplified for audience]

## What we're doing to prevent recurrence
- [Action 1] — owner: [name], ETA: [date]
- [Action 2] — owner: [name], ETA: [date]

## Lessons learned
- [Lesson 1]
- [Lesson 2]

Full postmortem: [link to internal doc]

## Product-Engineering Alignment

### OKR Tracking

Connect feature launches to measurable objectives.

```
## OKR Mapping

| Objective | Key Result | Feature | SLI | Current | Target |
|-----------|------------|---------|-----|---------|--------|
| Improve checkout speed | Reduce p99 to < 2s | Optimized checkout | checkout_latency_p99 | 3.2s | < 2s |
| Grow user base | Increase DAU by 20% | Referral program | daily_active_users | 10K | 12K |
| Reduce support load | Cut tickets by 30% | Self-service portal | support_tickets_daily | 150 | 105 |
```

**OKR review cadence:**
- Weekly: check SLI trends against OKR targets
- Monthly: review OKR progress with product + eng leads
- Quarterly: assess OKR completion, set next quarter's targets

**Engineering health OKRs (don't neglect):**
| Objective | Key Result | Measurement |
|-----------|------------|-------------|
| Improve reliability | SLO compliance > 99.9% | SLI dashboards |
| Reduce tech debt | < 10% sprint capacity on debt | Sprint tracking |
| Improve deploy velocity | DORA deploy frequency > 1/day | CI/CD metrics |
| Improve developer experience | PR cycle time < 24h | Git analytics |

### Feature Usage Analytics

Instrument features to measure actual usage vs expected usage.

**Minimum instrumentation per feature:**
```yaml
events:
  - name: feature_impression
    properties:
      feature_name: string
      user_id: string
      user_segment: string
      timestamp: datetime

  - name: feature_interaction
    properties:
      feature_name: string
      interaction_type: string  # click, submit, dismiss
      user_id: string
      session_id: string
      timestamp: datetime

  - name: feature_outcome
    properties:
      feature_name: string
      outcome: string  # success, error, abandon
      value: number    # revenue, time_saved, etc.
      user_id: string
      timestamp: datetime
```

**Feature adoption funnel:**
```
Impression → Interaction → Engagement → Value Created → Retained Use
100%         45%            20%          12%             8%

Where does the funnel break? Focus improvement at biggest drop.
```

**Analytics tools:**
| Tool | Best for |
|------|----------|
| Amplitude / Mixpanel | Product analytics, funnels, cohorts |
| PostHog | Open-source, self-hosted option |
| Grafana + Prometheus | Technical metrics, SLO tracking |
| BigQuery / Snowflake | Custom analysis on raw event data |

### A/B Testing

Validate feature impact with controlled experiments.

**A/B test lifecycle:**
```
1. Hypothesis: "Changing X will improve Y by Z%"
2. Design: control (A) vs variant (B), sample size, duration
3. Implement: feature flag routes users to A or B
4. Run: minimum 1 week, or until statistical significance
5. Analyze: check primary metric + guardrail metrics
6. Decide: ship winner, iterate, or kill
```

**Sample size calculation:**
```
n = (Z_α/2 + Z_β)² * 2p(1-p) / δ²

Where:
  Z_α/2 = 1.96 (95% confidence)
  Z_β = 0.84 (80% power)
  p = baseline conversion rate
  δ = minimum detectable effect

Example: baseline 5%, want to detect 10% relative lift (5% → 5.5%)
  n = (1.96 + 0.84)² * 2 * 0.05 * 0.95 / 0.005²
  n ≈ 29,645 per variant
```

**Guardrail metrics (check even if primary metric improves):**
- Error rate must not increase
- Latency must not increase > 10%
- Support tickets must not spike
- Revenue per user must not decrease

**A/B test pitfalls:**
- Peeking at results early (inflates false positive rate)
- Running during holidays or unusual traffic patterns
- Testing too many variants simultaneously (interaction effects)
- Not accounting for novelty effect (users interact with anything new)
- Small sample sizes (need statistical power)

**Integration with feature flags:**
```yaml
# Feature flag config with A/B variant
feature: new-checkout-flow
variants:
  - name: control
    weight: 50
    value: { layout: "current" }
  - name: streamlined
    weight: 50
    value: { layout: "minimal" }
targeting:
  - segment: beta_users
    variant: streamlined
metrics:
  primary: checkout_conversion_rate
  guardrails: [error_rate, p99_latency, support_tickets]
  minimum_sample_size: 30000
  minimum_duration: 7d
```

## Step 18: Incident Management

Source: PagerDuty Incident Response, Google SRE book.

Structured incident response = faster resolution + better learning.

### PagerDuty Severity Levels

```
| Level | Name     | Definition                                        | Response Time  | Examples                                      |
|-------|----------|---------------------------------------------------|----------------|-----------------------------------------------|
| SEV1  | Critical | Service fully down, data loss, security breach    | < 15 min       | Production down, data breach, payment failure |
| SEV2  | Major    | Major feature broken, significant user impact     | < 30 min       | Checkout broken, API errors > 5%, data lag    |
| SEV3  | Minor    | Minor feature degraded, workaround available      | < 2 hours      | Slow page load, minor UI bug, non-critical    |
| SEV4  | Low      | Cosmetic issue, no user impact                    | Next business  | Typo, minor visual glitch, dev env issue      |

# Escalation rules:
SEV1: Auto-page on-call + engineering manager + VP Eng immediately
SEV2: Auto-page on-call, escalate if not acked in 15 min
SEV3: Create ticket, on-call triages during business hours
SEV4: Create ticket, assign to team backlog
```

### Response Lifecycle: Detect → Respond → Triage → Mitigate → Resolve → Review

```
# Incident response phases

1. DETECT
   Sources: monitoring alert, customer report, status page check
   Actions:
   - Acknowledge alert in PagerDuty (< 5 min)
   - Open incident channel (#inc-YYYY-MM-DD-brief-description)
   - Post initial status: "Investigating — [service] showing [symptom]"

2. RESPOND
   Actions:
   - Incident commander (IC) assigned (on-call or first responder)
   - IC declares severity level
   - IC assigns roles: communications lead, technical lead
   - IC posts to status page: "Investigating [service] issues"

3. TRIAGE
   Actions:
   - Identify blast radius (which users, which services, which regions)
   - Determine root cause hypothesis
   - Check recent deployments (last 2 hours)
   - Check dependency health (upstream services, databases, third-party)
   - Update incident channel with findings

4. MITIGATE
   Actions:
   - Stop the bleeding (rollback, feature flag off, scale up, failover)
   - Mitigation may not be a fix — just stop user impact
   - Document every action taken with timestamp
   - Update status page: "Mitigating — we've [action] to reduce impact"

5. RESOLVE
   Actions:
   - Confirm metrics returned to normal (SLIs green)
   - Update status page: "Resolved — [root cause summary]"
   - Close incident channel (post-mortem moves to separate doc)
   - Notify stakeholders: "Resolved, post-mortem scheduled"

6. REVIEW (Post-Mortem)
   Actions:
   - Schedule post-mortem within 48 hours (blameless)
   - Write post-mortem doc using template (below)
   - Identify action items with owners and due dates
   - Share post-mortem with organization
   - Track action items to completion
```

### Communication Templates

**Incident channel message (initial):**
```
🚨 INCIDENT — SEV[X] — [Brief Description]

**Status:** Investigating
**Impact:** [What's broken, who's affected]
**IC:** @[name]
**Started:** [timestamp]
**Channel:** #inc-YYYY-MM-DD-slug

Updates every [15 min for SEV1/2, 30 min for SEV3].
```

**Status page update:**
```
[Investigating / Identified / Monitoring / Resolved]

We are investigating reports of [description of issue].
Some users may experience [specific impact].
We are actively working on a resolution.

Next update: [time]
```

**Stakeholder email (SEV1/2):**
```
Subject: [SEV{X}] {Service} Incident — {Date}

Summary:
{One paragraph: what happened, impact, current status}

Impact:
- Duration: {start time} to {end time} ({duration})
- Affected users: {number or percentage}
- Affected services: {list}

Timeline:
{HH:MM} — {event}
{HH:MM} — {event}
{HH:MM} — {event}

Root Cause:
{Brief technical explanation}

Mitigation:
{What we did to stop the bleeding}

Resolution:
{Permanent fix, or planned fix with timeline}

Action Items:
- [ ] {Action} — Owner: {name} — Due: {date}
- [ ] {Action} — Owner: {name} — Due: {date}
```

**Post-mortem template:**
```markdown
# Post-Mortem: {Title}

| Field          | Value                          |
|----------------|--------------------------------|
| Date           | YYYY-MM-DD                     |
| Severity       | SEV{X}                         |
| Duration       | {start} → {end} ({duration})   |
| Impact         | {users affected, revenue impact}|
| IC             | {name}                         |
| Author         | {name}                         |

## Summary
{2-3 sentence summary}

## Timeline (UTC)
| Time  | Event |
|-------|-------|
| HH:MM | {event} |

## Root Cause
{Technical explanation of what went wrong and why}

## Contributing Factors
- {Factor 1}
- {Factor 2}

## What Went Well
- {Thing 1}
- {Thing 2}

## What Went Poorly
- {Thing 1}
- {Thing 2}

## Action Items
| # | Action | Owner | Due Date | Priority |
|---|--------|-------|----------|----------|
| 1 | {action} | @name | YYYY-MM-DD | P1 |
| 2 | {action} | @name | YYYY-MM-DD | P2 |

## Lessons Learned
- {Lesson 1}
- {Lesson 2}
```

## Step 33: Blameless Postmortems

### Google Blameless Postmortem Pattern

Source: Google SRE Book Chapter 15, https://sre.google/workbook/postmortem-culture/

```
TRIGGERS (when to write a postmortem):
  - User-visible downtime or degradation > 5 minutes
  - Data loss (any amount)
  - On-call engineer paged (any severity)
  - Manual intervention required to resolve
  - Incident required >2 people to resolve
  - Postmortem requested by any team member (regardless of severity)

DOCUMENT STRUCTURE:
  # Postmortem: [Incident Title]

  ## Summary
  - Incident severity: [SEV1/SEV2/SEV3]
  - Impact: [duration, users affected, revenue impact]
  - Root cause: [one sentence]

  ## Timeline (UTC)
  - HH:MM — [what happened, who noticed, what action taken]
  - HH:MM — [escalation, investigation finding, mitigation applied]
  - HH:MM — [resolution confirmed, monitoring verified]

  ## Root Cause(s)
  - Primary: [the technical/systemic cause]
  - Contributing: [other factors that made it worse or harder to fix]

  ## What Went Well
  - [detection was fast because...]
  - [communication was clear because...]

  ## What Went Poorly
  - [took too long to find because...]
  - [runbook was outdated because...]

  ## Action Items
  - [ ] [Specific fix] — Owner: @[name] — Due: [date]
  - [ ] [Monitoring improvement] — Owner: @[name] — Due: [date]
  - [ ] [Process change] — Owner: @[name] — Due: [date]

  ## Lessons Learned
  - [What we now understand that we didn't before]

BLAMELESSNESS PRINCIPLES:
  1. Assume everyone acted with best intentions given what they knew at the time
  2. Focus on SYSTEM causes, not individual mistakes
  3. Replace "X made a mistake" with "the system allowed X to happen"
  4. Questions to ask:
     - "What information did the person have at the time?"
     - "Why did that action seem reasonable?"
     - "What would have made the right action obvious?"
  5. NEVER use postmortem findings in performance reviews
  6. Postmortem is for LEARNING, not BLAMING

  Anti-pattern: "John deployed the broken code"
  Blameless: "The deployment pipeline lacked automated canary analysis"

  Anti-pattern: "The on-call engineer didn't respond fast enough"
  Blameless: "The alerting threshold was set too low, causing alert fatigue"

POSTMORTEM REVIEW:
  - Written within 48h of incident
  - Reviewed in team meeting within 1 week
  - Action items tracked to completion (not forgotten)
  - Quarterly: review all postmortems for systemic patterns
```

### Etsy Blameless Postmortem Pattern

Source: Etsy Code as Craft, John Allspaw

```
PRINCIPLE: Local Rationality
  - People make rational decisions given their LOCAL context
  - "At the time, given what they knew, what they did made sense"
  - The failure is in the SYSTEM, not the person
  - Goal: understand the system conditions that made the failure possible

  Key questions:
  - "What did the person know at the time?"
  - "What were they trying to accomplish?"
  - "What tools/information were available to them?"
  - "Why did their action seem like the right one?"

FACILITATED DEBRIEFS:
  - Postmortem meeting is FACILITATED (not led by the incident commander)
  - Facilitator role: keep discussion blameless, redirect personal attacks
  - Facilitator asks: "What can we learn?" not "Who is responsible?"
  - Facilitator interrupts blame language: "Let's focus on the system, not individuals"
  - Separate the PERSON from the ACTION: "The deploy was bad" not "John's deploy was bad"

5 WHYS AT SYSTEM LEVEL:
  - Not 5 Whys to find a person — 5 Whys to find systemic causes

  Example:
  Why did the site go down?
  → Because the database ran out of connections
  Why did the database run out of connections?
  → Because a new feature opened connections but didn't close them
  Why didn't we catch the connection leak?
  → Because we don't have connection pool monitoring in staging
  Why don't we have connection pool monitoring in staging?
  → Because monitoring setup is manual and wasn't done for this service
  Why is monitoring setup manual?
  → Because we haven't invested in monitoring-as-code

  ACTION: automate monitoring setup (systemic fix)
  NOT: "train developers to set up monitoring" (blame the person)

ETTY POSTMORTEM TEMPLATE ADDITIONS:
  ## Cultural Context
  - What was the team's workload at the time?
  - Were there recent changes that affected team capacity?
  - What was the organizational pressure (deadline, incident streak)?

  ## Systemic Factors
  - What organizational/process gaps contributed?
  - What tooling gaps contributed?
  - What knowledge gaps contributed?
```

### PagerDuty Incident Response Pattern

Source: PagerDuty Incident Response documentation, https://response.pagerduty.com/

```
INCIDENT COMMANDER (IC) ROLE:
  - IC owns the incident from declaration to resolution
  - IC responsibilities:
    - Coordinate responders (who does what)
    - Communicate status to stakeholders (every 30 min for SEV1)
    - Make tactical decisions (rollback vs. fix-forward)
    - Ensure postmortem is scheduled
  - IC does NOT:
    - Fix the problem themselves (they coordinate, responders fix)
    - Get pulled into debugging (stays high-level)
  - IC transfer: if original IC needs to do deep investigation, hand off IC role

SEVERITY LEVELS:
  SEV1 (Critical):
    - Complete service outage or data loss
    - All hands, war room, exec notification
    - Response: 5 minutes, update: every 30 minutes
    - Postmortem: mandatory, within 48h

  SEV2 (Major):
    - Significant degradation, partial outage
    - On-call + team lead engaged
    - Response: 15 minutes, update: every 1 hour
    - Postmortem: mandatory, within 48h

  SEV3 (Minor):
    - Minor degradation, workaround available
    - On-call only
    - Response: 1 hour, update: every 4 hours
    - Postmortem: optional (recommended)

  SEV4 (Low):
    - Cosmetic issue, no user impact
    - Business hours response
    - Response: next business day
    - Postmortem: not required

48-HOUR COOL-DOWN:
  - Postmortem meeting: no earlier than 48h after incident resolution
  - Why: emotions are high, people are defensive, recollection is biased immediately after
  - 48h allows: sleep, perspective, calmer analysis, better documentation
  - Exception: if same incident recurs within 48h, expedite postmortem
  - Timeline doc: write within 24h (facts while fresh), review at 48h (with perspective)

MTTx METRICS:
  MTTD — Mean Time to Detect:
    - Time from incident start to first alert/notification
    - Target: < 5 min for SEV1
    - Improvement: better monitoring, synthetic checks, anomaly detection

  MTTA — Mean Time to Acknowledge:
    - Time from alert to human acknowledges + begins investigation
    - Target: < 5 min for SEV1, < 15 min for SEV2
    - Improvement: better on-call tooling, escalation policies, runbooks

  MTTR — Mean Time to Resolve:
    - Time from incident start to full resolution
    - Target: < 30 min for SEV1 (varies by complexity)
    - Improvement: better runbooks, faster rollback, feature flags

  MTTM — Mean Time to Mitigate:
    - Time from incident start to impact reduced (not fully resolved)
    - Often more useful than MTTR: "How fast did we reduce user pain?"
    - Target: < 15 min for SEV1
    - Improvement: pre-approved rollback, load shedding, circuit breakers

INCIDENT RESPONSE CHECKLIST:
  [ ] Severity levels defined with response SLAs
  [ ] IC role documented and trained (rotation + shadowing)
  [ ] Escalation policies configured in paging tool
  [ ] 48h cool-down policy adopted for postmortems
  [ ] MTTx metrics tracked and reviewed monthly
  [ ] Postmortem action items tracked to completion
  [ ] Quarterly: review postmortems for systemic patterns
  [ ] Blameless culture: leadership models blamelessness publicly
```

---

## Gstack-Inspired CEO Review
Source: Gary Tan / Y Combinator gstack framework

Before shipping major features, conduct a CEO-level review covering:

### Product Review
1. **Problem-solution fit**: Does this solve a real problem? Evidence?
2. **User impact**: Who benefits? How many? How much?
3. **Strategic alignment**: Does this advance the company mission?
4. **Competitive moat**: Does this create defensible advantage?

### Technical Review
1. **Architecture soundness**: Component boundaries clear? Tech debt manageable?
2. **Scalability**: Will this handle 10x current load?
3. **Security posture**: Threat model reviewed? OWASP checklist?
4. **Operational readiness**: Monitoring, alerts, runbooks in place?

### Business Review
1. **Unit economics**: Does this improve LTV:CAC ratio?
2. **Pricing impact**: Does this justify price increase or new tier?
3. **Market timing**: Is this the right time for this feature?
4. **Resource allocation**: Is this the best use of engineering time?

## Unicorn Launch Patterns

### Stripe: API-First Launch
- Ship with 7 lines of code example
- Interactive API explorer live on day one
- SDKs in top 3 languages at launch
- Sandbox environment available immediately
- Transparent pricing published

### Figma: Collaboration-First Launch
- Browser-based (zero install)
- Real-time collaboration demo
- Share link = product demo
- Community templates ready

### Notion: Template-First Launch
- 50+ community templates at launch
- Power user content on YouTube/Twitter
- Import from competitors (Evernote, Confluence)

### Slack: Viral-First Launch
- Free tier with generous limits
- Integration ecosystem (100+ at launch)
- Custom emoji and fun UX
- Team invite flow as core loop

## Pitfalls

1. **Don't skip PRD** — even one-pager prevents scope creep
2. **Don't skip design doc for complex features** — technical decisions need documentation
3. **Don't use ASK for trivial changes** — Ship/Show/Ask reduces bottlenecks
4. **Don't skip feature flags** — decouple deploy from release
5. **Don't skip retrospective** — continuous improvement requires reflection
6. **Don't skip monitoring setup** — deploy without observability is blind
7. **Don't skip rollback plan** — always have a way back
8. **Don't skip stakeholder communication** — keep everyone informed
## Related Skills

  - [sdlc-architecture-design](sdlc-architecture-design): System design, C4 diagrams, API design, database schema, code architecture, ADRs, branching, depende
  - [sdlc-requirements-engineering](sdlc-requirements-engineering): Requirements: user story mapping (Jeff Patton), BDD/Gherkin (Cucumber, pytest-bdd), acceptance crite
  - [sdlc-product-growth](sdlc-product-growth): Product-led growth (PLG), developer-led growth, growth loops, activation funnels, A/B testing, SaaS 


## Step 33: Product Operations

### Product Ops Function

```
Responsibilities:
  - Tool administration (Jira, Productboard, analytics)
  - Process standardization (templates, workflows)
  - Data management (metrics, dashboards)
  - Cross-team coordination (dependencies, timelines)
  - Customer feedback aggregation (support, sales, success)

Value proposition:
  - Frees PMs for strategic work
  - Ensures consistent processes
  - Improves data quality
  - Reduces coordination overhead
  - Enables scalable product org

When to hire:
  - >5 PMs in the org
  - >3 product teams
  - Significant tool sprawl
  - Inconsistent processes
  - Data quality issues
```

### Product Operations Metrics

```
Process metrics:
  - Cycle time (idea to shipped)
  - Backlog health (age, size, priority distribution)
  - Planning accuracy (estimated vs actual)
  - Stakeholder satisfaction (survey)

Tool metrics:
  - Tool adoption rate
  - Data completeness
  - Report accuracy
  - Integration health

Coordination metrics:
  - Dependency resolution time
  - Cross-team alignment score
  - Meeting efficiency
  - Communication clarity


## Step 34: Product Operations Tools

### Tool Stack

```
Product management:
  - Productboard (feedback, roadmap)
  - Aha! (roadmapping, strategy)
  - Jira (backlog, sprints)
  - Linear (issue tracking)

Analytics:
  - Mixpanel (product analytics)
  - Amplitude (user analytics)
  - PostHog (open-source analytics)
  - Heap (auto-capture)

Research:
  - UserTesting (user research)
  - Maze (usability testing)
  - Hotjar (heatmaps, recordings)
  - FullStory (session replay)

Communication:
  - Loom (async video)
  - Notion (documentation)
  - Confluence (wiki)
  - Slack (team communication)
