---
name: sdlc-prd-to-production
description: "End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective. Includes Ship/Show/Ask branching, design doc templates, PRD patterns (YC, Amazon Working Backwards), ephemeral environments, DORA 2024 insights, Score spec, AI-augmented development, technical specification templates, GitOps automation, documentation-as-code pipelines, metrics-driven development, production readiness reviews, launch strategies, post-launch monitoring, stakeholder communication templates, product-engineering alignment, continuous discovery habits, product-led growth, technical debt management, engineering metrics, incident management, engineering leadership, developer onboarding, inner source patterns, product analytics, A/B testing at scale, feature flag experimentation, product metrics frameworks, Shape Up cycles, continuous deployment culture, engineering team building, product-market fit, high growth handbook, engineering culture patterns, Google SRE, and blameless postmortems."
version: 4.7.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, prd, design-doc, rfc, ship-show-ask, workflow, end-to-end, product-development, yc, amazon-working-backwards, ephemeral-envs, score-spec, dora, ai-augmented, gitops, metrics-driven, docs-as-code, tech-spec, production-readiness, launch-strategy, post-launch, stakeholder-comms, okr, ab-testing, continuous-discovery, plg, product-led-growth, tech-debt, devex, incident-management, pagerduty, engineering-leadership, developer-onboarding, inner-source, shape-up, continuous-deployment, devops-culture, engineering-teams, product-market-fit, high-growth-handbook, engineering-culture, google-sre, blameless-postmortems]
    tags: [sdlc, prd, design-doc, rfc, ship-show-ask, workflow, end-to-end, product-development, yc, amazon-working-backwards, ephemeral-envs, score-spec, dora, ai-augmented, gitops, metrics-driven, docs-as-code, tech-spec, production-readiness, launch-strategy, post-launch, stakeholder-comms, okr, ab-testing, continuous-discovery, plg, product-led-growth, tech-debt, devex, incident-management, pagerduty, engineering-leadership, developer-onboarding, inner-source, product-analytics, amplitude, mixpanel, posthog, feature-flags, launchdarkly, unleash, statsig, flipt, flagsmith, aarrar, north-star-metric, heart-framework, experiment-platform, shape-up, continuous-deployment, devops-culture, engineering-teams, product-market-fit, high-growth-handbook, engineering-culture, google-sre, blameless-postmortems]
    related_skills: [sdlc-requirements-engineering, sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-deployment, sdlc-retrospective]
---

# PRD to Production

End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective. Includes Ship/Show/Ask branching and design doc templates.

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
Source: https://docs.uffizzi.com/

GitHub Action-based preview environments. No cluster needed.

```yaml
# .github/workflows/uffizzi-preview.yml
on:
  pull_request:
    types: [opened, reopened, synchronize, closed]
jobs:
  preview:
    uses: UffizziCloud/preview-action@v2
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

## Technical Specification Template

Use after design doc approval, before implementation begins. Captures contract-level detail.

### API Specification

```yaml
# specs/api.yaml (OpenAPI 3.1)
openapi: 3.1.0
info:
  title: [Service Name]
  version: 1.0.0
paths:
  /api/v1/resource:
    get:
      summary: [What it does]
      parameters: [query/path params with types and constraints]
      responses:
        '200':
          description: Success
          content:
            application/json:
              schema: { $ref: '#/components/schemas/Resource' }
        '400': { description: Validation error, content: { application/json: { schema: { $ref: '#/components/schemas/Error' } } } }
        '401': { description: Unauthorized }
        '429': { description: Rate limited }
        '500': { description: Internal error }
components:
  schemas:
    Resource:
      type: object
      required: [id, created_at]
      properties:
        id: { type: string, format: uuid }
        created_at: { type: string, format: date-time }
    Error:
      type: object
      required: [code, message]
      properties:
        code: { type: string }
        message: { type: string }
        details: { type: array, items: { type: object } }
```

### Data Model

```
# specs/data-model.md

## Entity: [Name]
- Table: [table_name]
- Primary key: [id type]
- Indexes: [list with rationale]
- Constraints: [unique, foreign key, check]

| Field       | Type      | Nullable | Default   | Description       |
|-------------|-----------|----------|-----------|-------------------|
| id          | uuid      | no       | gen_...   | Primary key       |
| created_at  | timestamp | no       | now()     | Creation time     |
| updated_at  | timestamp | no       | now()     | Last update       |

## Relationships
- [Entity A] 1:N [Entity B] via [foreign_key]
- Cascade rules: [delete cascade / restrict / set null]

## Migration Strategy
- Forward-only migrations (no rollback scripts)
- Backward-compatible schema changes only in shared tables
- Blue-green compatible: schema works with N and N+1 code versions
```

### Error Handling Strategy

```
# specs/error-handling.md

## Error Taxonomy
| Code   | Category     | Retryable | User Action          |
|--------|-------------|-----------|----------------------|
| 400    | Validation  | No        | Fix input            |
| 401    | Auth        | No        | Re-authenticate      |
| 403    | Authorization | No      | Request access       |
| 404    | Not Found   | No        | Check resource ID    |
| 409    | Conflict    | Maybe     | Retry or resolve     |
| 429    | Rate Limit  | Yes       | Backoff + retry      |
| 500    | Internal    | Yes       | Retry, then escalate |
| 503    | Unavailable | Yes       | Retry with backoff   |

## Error Response Format
```json
{
  "error": {
    "code": "VALIDATION_FAILED",
    "message": "Human-readable description",
    "details": [
      { "field": "email", "issue": "invalid format", "value": "abc" }
    ],
    "request_id": "req-uuid",
    "timestamp": "2024-01-15T10:30:00Z"
  }
}
```

## Retry Policy
- Exponential backoff: base 1s, max 30s, jitter ±50%
- Max retries: 3 for idempotent operations, 0 for non-idempotent
- Circuit breaker: open after 5 consecutive failures, half-open after 30s

## Observability Hooks
- Every error emits structured log: {error_code, request_id, user_id, duration_ms}
- Error rate tracked per endpoint as SLI input
- Anomaly alert on error rate > 2x baseline
```

### Observability Spec

```
# specs/observability.md

## Structured Logging Format
```json
{
  "timestamp": "ISO8601",
  "level": "INFO|WARN|ERROR",
  "service": "service-name",
  "trace_id": "otel-trace-id",
  "span_id": "otel-span-id",
  "request_id": "req-uuid",
  "user_id": "user-uuid",
  "method": "GET",
  "path": "/api/v1/resource",
  "status": 200,
  "duration_ms": 42,
  "message": "Human readable"
}
```

## Metrics (OpenTelemetry)
| Metric Name              | Type      | Labels                      | Description               |
|--------------------------|-----------|-----------------------------|---------------------------|
| http_request_duration_ms | Histogram | method, path, status        | Request latency           |
| http_requests_total      | Counter   | method, path, status        | Total requests            |
| db_query_duration_ms     | Histogram | operation, table            | Database query latency    |
| queue_depth              | Gauge     | queue_name                  | Current queue depth       |

## Tracing
- Propagate W3C traceparent header
- Span boundaries: HTTP handler → service → repository → external call
- Sample rate: 100% for errors, 10% for success (adjust per traffic)

## Dashboards (minimum)
1. Service health: latency p50/p95/p99, error rate, throughput
2. Business metrics: conversion, adoption, feature usage
3. Infrastructure: CPU, memory, disk I/O, network
```

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
```

## Documentation-as-Code Pipeline

Source: DORA 2024 — doc quality predicts org performance.

### Auto-Generated API Docs

```
# Generate from OpenAPI spec at build time
openapi-generator generate -i specs/api.yaml -g html2 -o docs/api/

# Or render in Backstage via TechDocs
# mkdocs.yml at repo root, source in /docs folder
```

**mkdocs.yml for service docs:**
```yaml
site_name: Orders API
nav:
  - Home: index.md
  - API Reference: api.md      # auto-generated from OpenAPI
  - Data Model: data-model.md
  - Runbook: runbook.md
  - Changelog: changelog.md    # auto-generated from commits
plugins:
  - techdocs-core              # Backstage integration
```

### Auto-Generated Changelog

**Conventional Commits + changelog generation:**
```yaml
# .github/workflows/changelog.yml
name: Generate Changelog
on:
  push:
    tags: ['v*']
jobs:
  changelog:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: conventional-changelog/standard-version@v1
        with:
          release-count: 1
      - run: git push --follow-tags
```

**Commit format (enforce via CI):**
```
feat: add order cancellation endpoint
fix: prevent duplicate payment processing
docs: update API reference for v2
perf: optimize order query with composite index
BREAKING CHANGE: remove deprecated /v1/orders/search endpoint
```

### Architecture Diagrams as Code

**Use Mermaid or Structurizr in design docs:**

```markdown
## Architecture

```mermaid
graph LR
  Client[Web Client] --> Gateway[API Gateway]
  Gateway --> Orders[Orders Service]
  Gateway --> Payments[Payments Service]
  Orders --> DB[(PostgreSQL)]
  Orders --> Queue[Redis Queue]
  Queue --> Payments
  Payments --> Stripe[Stripe API]
```
```

**Structurizr DSL for full architecture:**
```
workspace {
  model {
    user = person "Customer"
    orders = softwareSystem "Orders Service"
    payments = softwareSystem "Payments Service"
    user -> orders "Places orders"
    orders -> payments "Processes payments"
  }
  views {
    systemContext orders {
      include *
      autolayout lr
    }
  }
}
```

### Documentation Pipeline Integration

```
PR opened
  → CI checks: API spec valid, no broken links, diagram renders
  → TechDocs preview deployed to ephemeral environment
  → Reviewer validates docs alongside code

PR merged
  → TechDocs built and published to Backstage
  → Changelog updated (if conventional commits used)
  → Architecture diagrams re-rendered
  → API docs regenerated from OpenAPI spec

Release tagged
  → Versioned docs snapshot created
  → Changelog entry generated from commits
  → Release notes published
```

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
## Retrospective addition — metrics review:
- Did we hit SLOs this sprint?
- Which SLIs are we missing?
- Are alerts actionable? (false positive rate)
- Should we tighten or loosen any SLO?
- What new instrumentation should we add next sprint?
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
```

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

## Step 14: Continuous Discovery Habits

Source: Teresa Torres — *Continuous Discovery Habits*.

Weekly customer touchpoints + structured assumption testing = better product decisions.

### Weekly Customer Touchpoints

**Rule: talk to customers every single week, no exceptions.**

```
# Weekly discovery cadence

| Day       | Activity                          | Duration | Output              |
|-----------|-----------------------------------|----------|---------------------|
| Monday    | Review last week's interview notes | 30 min   | Insight summary     |
| Tuesday   | Customer interview (1:1)          | 30 min   | Interview notes     |
| Wednesday | Analyze feedback + update OST     | 45 min   | Updated opportunity |
| Thursday  | Prototype test with user          | 30 min   | Test results        |
| Friday    | Share discovery insights w/ team  | 15 min   | Slack update/doc    |
```

**Interview question framework (opportunity-based):**
```
1. Context: "Tell me about the last time you [did task]..."
2. Struggle: "What was hardest about that?"
3. Workaround: "How do you deal with that today?"
4. Ideal: "If you could wave a magic wand, what would happen?"
5. Priority: "On a scale of 1-10, how important is fixing this?"
```

### Opportunity Solution Tree (OST)

Visual map: desired outcome → opportunities → solutions → experiments.

```
                    ┌─────────────────┐
                    │  Desired Outcome │
                    │ (Increase trial  │
                    │  → paid by 20%)  │
                    └────────┬────────┘
               ┌─────────────┼─────────────┐
               ▼             ▼             ▼
        ┌──────────┐  ┌──────────┐  ┌──────────┐
        │Onboarding│  │Feature   │  │Pricing   │
        │too complex│  │discover- │  │confusion │
        │          │  │ability   │  │          │
        └────┬─────┘  └────┬─────┘  └────┬─────┘
        ┌────┼────┐   ┌────┼────┐   ┌────┼────┐
        ▼    ▼    ▼   ▼    ▼    ▼   ▼    ▼    ▼
      [S1] [S2] [S3] [S4] [S5] [S6] [S7] [S8] [S9]

S = Solution experiment (assumption test)
```

**OST maintenance rules:**
- Update weekly after customer touchpoints
- Max 3-5 opportunities per outcome (forced prioritization)
- Each opportunity maps to 2-4 solution experiments
- Prune dead branches monthly

### Assumption Testing Framework

**Four types of assumptions to test before building:**

```
| Assumption Type | Question                        | Test Method              |
|-----------------|---------------------------------|--------------------------|
| Desirability    | Do users want this?             | Fake door test, survey   |
| Viability       | Will this drive business value? | Wizard of Oz, concierge  |
| Feasibility     | Can we build this?              | Prototype, spike         |
| Usability       | Can users use this?             | Usability test, 5-second |
```

**Assumption test template:**
```yaml
assumption:
  statement: "Users will invite teammates during onboarding"
  type: desirability          # desirability | viability | feasibility | usability
  risk_level: high            # high = test first
  confidence: low             # low | medium | high
  test:
    method: "Fake door test"
    metric: "% of users clicking 'Invite Team' button"
    threshold: ">= 15% click rate"
    duration: "5 days, 200 users"
  result:
    observed: "8% click rate"
    verdict: invalidated       # validated | invalidated | inconclusive
    next_action: "Redesign invite flow, test new placement"
```

**Testing cadence:**
- Test highest-risk assumption first
- Run 1-2 tests per week
- Timebox each test (max 5 business days)
- Kill or pivot on invalidated assumptions — don't rationalize

---

## Step 15: Product-Led Growth (PLG)

Source: Wes Bush — *Product-Led Growth*.

Let the product sell itself. Users experience value before paying.

### Self-Serve Onboarding

**Goal: time-to-value (TTV) < 5 minutes. No sales call required.**

```
# Onboarding funnel stages

1. Sign-up        → < 30 seconds (SSO, magic link, no credit card)
2. Activation     → First meaningful action within 2 minutes
3. Aha moment     → User sees core value (milestone varies by product)
4. Habit loop     → User returns 3+ times in first week

# Activation examples by product type:
| Product Type   | Activation Event                    | Target TTV |
|----------------|-------------------------------------|------------|
| SaaS tool      | Create first project/dashboard      | 2 min      |
| API product    | First successful API call           | 5 min      |
| Data platform  | First query returns results         | 3 min      |
| Collaboration  | Invite first teammate               | 1 min      |
```

**Onboarding checklist (in-product):**
```yaml
onboarding:
  steps:
    - id: create_workspace
      label: "Create your workspace"
      required: true
      skip_if: "workspace exists"
    - id: import_data
      label: "Import your data"
      required: false
      help_text: "Connect CSV, API, or database"
    - id: first_action
      label: "Create your first [core object]"
      required: true
      tooltip: "This is where the magic happens"
    - id: invite_team
      label: "Invite your team"
      required: false
      incentive: "Unlock collaboration features"
  progress_bar: true
  skip_option: true  # let power users bypass
```

### Usage-Based Pricing

**Align cost with value delivered. Users pay as they grow.**

```
# Pricing tiers (example)

| Tier       | Price        | Included         | Overage   | Target Segment    |
|------------|--------------|------------------|-----------|-------------------|
| Free       | $0/mo        | 1K API calls     | N/A       | Evaluation        |
| Starter    | $29/mo       | 50K API calls    | $0.001/ea | Small teams       |
| Growth     | $99/mo       | 500K API calls   | $0.0008/ea| Growing companies |
| Enterprise | Custom       | Unlimited        | Volume    | Large orgs        |

# Usage metrics to price on:
- API calls, events processed, seats, storage, compute minutes
- Pick metric that correlates with customer value received
- Avoid pricing on metric users will optimize away (e.g., don't price on log volume if users will log less)
```

### Viral Loops

**Built-in sharing mechanics that drive organic growth.**

```
# Viral loop types

1. Collaboration invite    → "Invite teammate to edit" (Slack, Figma)
2. Shareable output        → "Share dashboard link" (Notion, Amplitude)
3. Embed/widget            → "Powered by [Product]" badges
4. Referral program        → "Give $X, get $X" credit
5. Template sharing        → "Publish template to marketplace"

# Viral coefficient (k-factor):
k = (invites sent per user) × (conversion rate per invite)
Target: k > 1.0 for exponential growth

Example: avg user sends 3 invites, 40% sign up → k = 1.2 ✓
```

### Product-Qualified Lead (PQL) Metrics

**Users who've experienced product value — better than MQLs.**

```
# PQL definition template

pql_criteria:
  must_match:
    - "Completed onboarding (activation event)"
    - "Used core feature 3+ times in 7 days"
  scoring:
    - action: "Created workspace"       points: 10
    - action: "Invited teammate"        points: 15
    - action: "Used API integration"    points: 20
    - action: "Hit usage limit"         points: 25
    - action: "Viewed pricing page"     points: 10
  threshold: 40  # PQL when score >= 40
  handoff: "Route to sales with product usage context"
```

**PQL funnel metrics to track:**
```
| Metric                  | Definition                          | Target    |
|-------------------------|-------------------------------------|-----------|
| Sign-up → Activation    | % completing activation event       | > 60%     |
| Activation → PQL        | % reaching PQL score threshold      | > 25%     |
| PQL → Conversion        | % converting to paid                | > 15%     |
| Free → Paid (overall)   | % of sign-ups converting            | > 5%      |
| Time to PQL             | Median days from sign-up to PQL     | < 14 days |
```

---

## Step 16: Technical Debt Management

Source: Martin Fowler — Technical Debt Quadrant.

Not all debt is equal. Classify, quantify, remediate strategically.

### Fowler's Debt Quadrant

```
                    Deliberate                Inadvertent
                ┌─────────────────────┬─────────────────────┐
   Reckless     │ "We don't have      │ "What's layered     │
                │  time for design"   │  architecture?"     │
                │                     │                     │
                │  DANGER: ships fast │  DANGER: unaware    │
                │  but compounds      │  of the mess        │
                ├─────────────────────┼─────────────────────┤
   Prudent      │ "We must ship now   │ "Now we know how    │
                │  and deal with      │  we should have     │
                │  consequences"      │  done it"           │
                │                     │                     │
                │  ACCEPTABLE: aware  │  VALUABLE: learning │
                │  tradeoff           │  happened           │
                └─────────────────────┴─────────────────────┘
```

**Classification action matrix:**
```
| Quadrant             | Action                                | Priority        |
|----------------------|---------------------------------------|-----------------|
| Reckless + Deliberate| STOP. Escalate. Refuse to ship.       | P0 — block      |
| Reckless + Inadvertent| Educate team. Add architecture review | P1 — urgent     |
| Prudent + Deliberate | Track in debt register. Schedule fix. | P2 — planned    |
| Prudent + Inadvertent| Document. Fix opportunistically.      | P3 — background |
```

### Quantifying Technical Debt: Principal vs Interest

```
# Debt tracking template

debt_item:
  id: TD-042
  name: "Monolith order service — no boundary isolation"
  quadrant: prudent_deliberate
  principal:
    effort_to_fix: "3-4 sprints (2 engineers)"
    affected_files: 47
    affected_services: 3
  interest:
    monthly_cost:
      extra_development_time: "15% slower feature delivery"
      incidents: "1-2 cross-service failures per month"
      cognitive_load: "All 8 engineers must understand order flow"
    annualized_cost: "$180K (based on engineer time × incident hours)"
  decision:
    status: tracked        # tracked | fixing | resolved
    scheduled_fix: "Q3 2026"
    accepted_because: "Revenue-critical feature taking priority"
```

**Debt register rules:**
- Every debt item has principal + interest estimates
- Review quarterly: interest growing? Fix faster.
- If interest > cost to fix → fix immediately
- Track debt ratio: debt sprint points / total sprint points

### Remediation Strategies

**1. Boy Scout Rule — "Leave code better than you found it"**
```
# PR template addition:
- [ ] If I touched a file, I improved:
      - [ ] Naming / readability
      - [ ] Removed dead code
      - [ ] Added missing test
      - [ ] Extracted magic numbers to constants
Timebox: max 30 min per PR on cleanup. Don't refactor entire module.
```

**2. Strangler Fig Pattern — Incrementally replace legacy systems**
```
# Migration strategy:

Phase 1: Identify boundaries (1 week)
  └─ Map all inputs/outputs of legacy component

Phase 2: Build facade (1-2 weeks)
  └─ New component routes to old + new based on feature flag

Phase 3: Migrate incrementally (ongoing)
  └─ Move one route/endpoint at a time to new component
  └─ Each migration = 1 small PR, independently deployable

Phase 4: Remove old (when traffic = 0)
  └─ Delete legacy code. Celebrate.

# Routing example:
if feature_flag("use-new-order-service", user_id):
    return new_order_service.handle(req)   # ← new
else:
    return legacy_order_service.handle(req) # ← old
```

**3. 20% Sprints — Dedicated debt reduction capacity**
```
# Sprint allocation:
| Capacity        | Percentage | Purpose                          |
|-----------------|------------|----------------------------------|
| Feature work    | 70%        | Product features + improvements  |
| Tech debt       | 20%        | Planned debt reduction           |
| Innovation      | 10%        | Spikes, experiments, tooling     |

# Rules:
- 20% is non-negotiable — don't borrow from it for features
- Debt items selected from register based on interest cost
- Track: sprint velocity on debt items (trend should be stable/increasing)
- Demo debt work in sprint review (visibility = accountability)
```

---

## Step 17: Engineering Metrics Beyond DORA

DORA metrics (deploy frequency, lead time, MTTR, change failure rate) are necessary but not sufficient.

### Code Health Trends

```yaml
# Code health dashboard metrics

complexity:
  metric: "Cyclomatic complexity per function"
  tool: "radon (Python), complexity-report (JS), lizard (multi-lang)"
  threshold: "avg < 10, max < 20 per function"
  trend: "Track monthly — should be stable or decreasing"

duplication:
  metric: "Duplicated lines %"
  tool: "jscpd, PMD CPD"
  threshold: "< 3% of codebase"
  trend: "Increasing = architecture problem"

dead_code:
  metric: "Unreachable code / unused exports"
  tool: "vulture (Python), ts-prune (TS), deadcode (Go)"
  threshold: "0 new dead code per quarter"
  trend: "Should decrease over time"

file_size:
  metric: "Lines per file"
  threshold: "< 500 lines (warn), < 1000 (error)"
  trend: "Watch for files growing beyond threshold"
```

### Dependency Freshness

```yaml
# Dependency health tracking

freshness:
  metric: "Days since last dependency update"
  tool: "Renovate, Dependabot, pip-audit, npm audit"
  thresholds:
    critical: "Dependencies > 180 days old"
    warning: "Dependencies > 90 days old"
    healthy: "Dependencies < 30 days old"
  policy: "Update patch versions automatically, minor versions in PR, major versions manually"

vulnerability_sla:
  critical: "Fix within 24 hours"
  high: "Fix within 7 days"
  medium: "Fix within 30 days"
  low: "Fix within 90 days"

license_compliance:
  tool: "FOSSA, license-checker, pip-licenses"
  banned: ["GPL-3.0", "AGPL-3.0"]  # adjust per org policy
  check: "CI pipeline blocks on banned license"
```

### Test Coverage Trends

```yaml
# Coverage tracking (absolute numbers less important than trends)

overall_coverage:
  metric: "Line coverage %"
  tool: "coverage.py, istanbul/nyc, go cover"
  minimum: "No new code below 80%"
  trend: "Must not decrease month-over-month"

differential_coverage:
  metric: "Coverage of changed lines only"
  tool: "diff-cover, Codecov patch coverage"
  minimum: "> 90% of changed lines covered"
  enforcement: "CI fails if differential coverage < threshold"

test_health:
  flaky_test_rate: "< 2% of test runs"
  test_duration:
    unit: "< 5 minutes total"
    integration: "< 15 minutes total"
    e2e: "< 30 minutes total"
  quarantine: "Flaky tests moved to quarantine suite, fixed within 2 weeks or deleted"
```

### Developer Experience (DevEx)

Source: DX (dx.dev) framework — measure what developers feel, not just what tools measure.

```yaml
# DevEx metrics framework

# Quantitative metrics (from tooling):
cycle_time:
  metric: "PR open → merge"
  target: "< 24 hours"
  tool: "LinearB, Sleuth, Jellyfish"

pr_review_wait:
  metric: "Time to first review"
  target: "< 4 hours (business hours)"
  tool: "GitHub Insights, LinearB"

build_time:
  metric: "CI pipeline duration"
  target: "< 10 minutes for main branch"
  tool: "CI platform analytics"

environment_setup:
  metric: "New dev → first successful build"
  target: "< 1 hour"
  tool: "Track manually, optimize with devcontainer/Nix"

# Qualitative metrics (from surveys — quarterly):
survey_dimensions:
  - name: "Ease of shipping"
    question: "How easy is it to get changes to production?"
    scale: "1-5 Likert"
  - name: "Codebase health"
    question: "How confident are you making changes to unfamiliar code?"
    scale: "1-5 Likert"
  - name: "Tooling satisfaction"
    question: "How satisfied are you with dev tools and CI/CD?"
    scale: "1-5 Likert"
  - name: "Cognitive load"
    question: "How much context do you need to make a simple change?"
    scale: "1-5 Likert"
  - name: "Onboarding experience"
    question: "How quickly could you contribute in your first week?"
    scale: "1-5 Likert"
```

**Engineering productivity dashboard:**
```
┌──────────────────────────────────────────────────────────────┐
│                 Engineering Metrics Dashboard                 │
├──────────────┬──────────────┬──────────────┬─────────────────┤
│  DORA        │  Code Health │  Dep Fresh   │  DevEx          │
│              │              │              │                 │
│  Deploy: 2/d │  Complexity: │  Avg age:    │  Cycle time:    │
│  Lead: 3h    │    avg 7.2   │    23 days   │    18h ✓        │
│  MTTR: 45m   │  Dup: 1.8%   │  Vulns: 0    │  Review wait:   │
│  CFR: 2.1%   │    ✓        │    critical  │    2.5h ✓       │
│              │  Dead: -3%   │    ✓        │  Build: 8m ✓    │
│              │    ✓        │              │  Survey: 4.1/5  │
└──────────────┴──────────────┴──────────────┴─────────────────┘
```

---

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

---

## Step 19: Engineering Leadership

Source: Camille Fournier — *The Manager's Path*; Will Larson — *An Elegant Puzzle*; Charity Majors.

Engineering orgs grow in layers. Each layer requires different skills, tools, and mindset shifts. Getting this wrong creates chaos, burnout, and attrition.

### Role Progression: IC → Tech Lead → Manager → Director → VP
Source: Camille Fournier — *The Manager's Path*.

```
IC (Individual Contributor)
  → Owns technical execution within a team
  → Deep expertise in one or more domains
  → Impact through own work + code review + mentoring

Tech Lead
  → Owns technical direction for a team
  → Still writes code (50-70% of time)
  → Resolves technical disagreements, sets architecture standards
  → NOT a people manager — no hiring/firing/comp decisions
  → Key skill: influence without authority, writing technical docs

Engineering Manager
  → Owns people + delivery for a team
  → Writes little to no code (0-20% of time)
  → Hiring, firing, career growth, comp reviews, 1:1s
  → Key skill: giving feedback, having hard conversations, protecting team from chaos
  → Common trap: micromanaging code reviews, staying in IC comfort zone

Director of Engineering
  → Owns multiple teams (2-4) or a department
  → Sets process, standards, cross-team coordination
  → Hires managers, builds organizational structure
  → Key skill: org design, managing managers, strategic planning
  → Common trap: skipping middle management, creating matrix confusion

VP of Engineering
  → Owns engineering org as a whole
  → Partners with product, design, exec on strategy
  → Sets culture, values, hiring bar at org level
  → Key skill: organizational communication, executive presence, resource allocation
  → Common trap: becoming disconnected from technical reality
```

### Org Design Principles (Will Larson)
Source: Will Larson — *An Elegant Puzzle*.

#### Team Sizing: 4-8 Engineers

```
Below 4: team is too fragile
  - 1 departure = 25-33% capacity loss
  - No slack for sick days, vacations, on-call rotation
  - Risk: single point of failure on key knowledge

Sweet spot 4-8: ideal team size
  - Enough capacity for meaningful work
  - Small enough for everyone to have context
  - On-call rotation works (1 in 5-8)
  - 1:1s and code review manageable

Above 8: split into sub-teams
  - Communication overhead grows quadratically (n*(n-1)/2)
  - Managers can't give individual attention
  - Risk: team within a team, invisible politics

Exception: teams of 2-3 can work for focused, time-boxed initiatives (skunkworks, prototypes)
but should not be permanent structures.
```

#### Migration Patterns

```
# Larson's org migration approach:

1. IDENTIFY the migration (new platform, reorg, tool change)
2. DEFINE the migration contract
   - What's changing, what's NOT changing
   - Timeline (migrations take 2-4x longer than estimate)
   - Success criteria (what does "done" look like?)
3. CREATE a migration team (temporary, 4-6 engineers)
   - Not the team that owns the destination platform
   - Focused entirely on migration, not dual-tasked
4. EXECUTE incrementally
   - Migrate one team/service at a time
   - Validate each before moving to next
   - Document patterns and blockers as you go
5. DISSOLVE migration team once complete
   - Knowledge transfers to receiving teams
   - Migration team members become advocates

# Common migration anti-patterns:
- Big bang: "we'll migrate everything next quarter" → never happens
- Dual-tasking: same team owns product AND migration → migration starves
- No rollback: "we can always go back" → you can't, plan accordingly
- Executive sponsorship without IC buy-in → grudging compliance, poor execution
```

#### Org Debt

```
# Org debt = structural problems that slow work down over time
# Analogous to tech debt but in organizational structure

Examples of org debt:
  - Ambiguous ownership: "who owns the auth service?" → nobody, everybody
  - Cross-cutting responsibilities: every change requires 3+ team approvals
  - Stale org charts: team names don't match actual work
  - Unclear decision rights: nobody knows who makes the call → death by committee
  - Informal power structures: "ask Sarah, she knows everything" → bus factor of 1

Remediation:
  - Quarterly org review: map teams to domains, flag gaps/overlaps
  - Decision rights matrix (RACI or equivalent): who decides what
  - Ownership registry: service → owning team (updated automatically from CODEOWNERS)
  - Retro on org issues: "what process/team structure slowed us down this quarter?"

Cost of ignoring org debt:
  - Onboarding takes 3x longer (nobody knows who to ask)
  - Projects stall in review/approval limbo
  - Best engineers leave (frustrated by bureaucracy)
```

### Management ≠ Promotion: The IC Track (Charity Majors)
Source: Charity Majors — "The Manager's Path is Not a Promotion", "The IC Track".

```
# Core principle: management is a CAREER CHANGE, not a promotion
# Promoting best IC to manager = losing best IC, gaining bad manager

Anti-pattern: "You're a senior IC, now manage people"
  - IC skills ≠ management skills (empathy, feedback, delegation ≠ code quality)
  - No training offered: "figure it out" → manager fails, team suffers
  - Reverting to IC seen as demotion → cultural trap

# Charity's advice for orgs:

1. Create compelling Staff+ IC track
   - Staff Engineer = same influence as Engineering Manager
   - Principal Engineer = same influence as Director
   - Distinguished Engineer = same influence as VP
   - Equal pay, equal voice in org decisions

2. Never promote to management as reward
   - "You're doing great as IC, want to try management?" = wrong framing
   - Ask: "Do you want to be responsible for people's careers and emotional well-being?"
   - If answer is not enthusiastic YES → don't do it

3. Make it easy to switch tracks
   - IC → Manager → IC should be normal career movement
   - No stigma, no pay cut, no "step back"
   - 6-month trial: try management, revert easily if not a fit

4. Staff+ IC responsibilities:
   - Technical vision and strategy (cross-team)
   - Architecture decisions and RFCs
   - Mentoring and growing other engineers
   - Bridging between eng and product/business
   - NOT: "just a really senior coder"

5. Common failure mode: "We can't afford Staff+ ICs, we need managers"
   - Response: "You can't afford NOT to. Every team needs technical leadership."
   - Orgs with only managers drift technically → architecture debt compounds
```

---

## Step 20: Developer Onboarding

Source: Google Engineering Practices, Stripe engineering culture.

Onboarding is a system, not a checklist. Poor onboarding costs 3-6 months of productivity. Great onboarding ships code in week 1.

### Google Codelab System
Source: Google engineering onboarding — internal codelabs.

```
# Codelab = self-paced, hands-on tutorial with real code
# Not documentation — interactive exercises with working examples

Google's approach:
  1. New hire receives curated list of codelabs (role-specific)
  2. Codelabs cover: codebase overview, team conventions, common patterns
  3. Each codelab: 30-90 min, ends with working code change
  4. Mentor reviews codelab output — not test, but learning conversation
  5. Codelabs updated by team (not HR) — always current

# Creating codelabs for your org:

Template:
  1. Objective: "After this codelab, you can [specific skill]"
  2. Prerequisites: what you need before starting
  3. Environment setup: copy-paste commands (devcontainer, Docker, Nix)
  4. Step-by-step: each step = code change + explanation + verification
  5. Cleanup: how to reset / remove artifacts
  6. Next steps: what to learn next, links to related codelabs

# Codelab topics (minimum viable set):
  - "Build and run [service] locally"
  - "Make your first PR (ship to production)"
  - "Debug a failing test"
  - "Add a new API endpoint"
  - "Trace a request through the system"
  - "Deploy to staging via GitOps"
```

### Stripe's Incremental Onboarding
Source: Stripe engineering blog — onboarding practices.

```
# Core principle: ship real code in week 1
# Not "read docs for 2 weeks then shadow someone"

Week 1: Ship a real change to production
  - Day 1: setup environment, get access, meet buddy
  - Day 2: complete 2-3 codelabs
  - Day 3: pick starter bug from backlog
  - Day 4: implement fix, open PR, get review
  - Day 5: ship to production (with buddy review)
  - Celebration: team acknowledges first deploy (Slack channel, lunch, etc.)

Week 2-3: Expand scope
  - Own a small feature (1-2 PRs)
  - Participate in code review (review others' PRs)
  - Attend team rituals (standup, retro, planning)
  - Shadow on-call rotation (observe, don't respond)

Week 4-6: Full contributor
  - Own a medium-sized feature
  - Take on-call shifts (paired with experienced member first)
  - Present a tech talk or write a doc for next new hire
  - Give feedback on onboarding process (meta-improvement)

# Why this works:
  - Learning by doing > learning by reading
  - First PR builds confidence and belonging
  - Real code = real context (not toy examples)
  - Buddy system prevents getting stuck for days
```

### Time-to-First-Commit Targets

```
| Metric                    | Poor    | Acceptable | Excellent |
|---------------------------|---------|------------|-----------|
| Time to first commit      | > 2 weeks | 3-5 days | < 1 day   |
| Time to first deploy      | > 4 weeks | 1-2 weeks | < 1 week |
| Time to on-call ready     | > 3 months | 4-6 weeks | < 4 weeks |
| Time to full productivity | > 6 months | 3-4 months | < 2 months |

# How to measure:
  - Track first PR date from HR start date
  - Track first on-call shift date
  - Survey new hires at 30/60/90 days: "How productive do you feel?" (1-5)
  - Compare across teams — if one team has 2x longer onboarding, investigate

# Common blockers (fix before hiring):
  - Access provisioning takes > 1 day → automate with SSO/SCIM
  - Environment setup takes > 4 hours → devcontainer/Nix/flake.nix
  - No starter bugs in backlog → create them in advance
  - Buddy system doesn't exist → assign buddy before day 1
  - Documentation stale → docs-as-code, CI checks freshness
```

### Buddy System

```
# Every new hire gets a buddy (not their manager)
# Buddy = same team, 6+ months tenure, volunteered (not voluntold)

Buddy responsibilities:
  - Answer "stupid questions" (no such thing) without judgment
  - Pair on first 2-3 PRs (not do it for them — guide)
  - Introduce to people: "meet Sarah, she owns payments; Tom, infra"
  - Explain unwritten rules: "we always squash merge", "ping @channel for deploy"
  - Check in daily week 1, weekly weeks 2-4
  - Escalate to manager if new hire seems stuck/unhappy

Time commitment: ~2-3 hours/week for first 4 weeks, then decreasing

# Buddy rotation: different buddy per new hire (don't overload one person)
# Track buddy effectiveness: survey new hires, buddy should score 4+/5
```

### Starter Bugs

```
# Pre-curated list of bugs for new hires to fix on day 3-5
# Not throwaway work — real issues that need fixing

Criteria for good starter bugs:
  - Well-defined (clear description, reproduction steps)
  - Low-risk (no data loss, no security implications)
  - Self-contained (minimal cross-team dependencies)
  - Good learning opportunity (touches key system areas)
  - Can be completed in 1-3 days
  - Has clear acceptance criteria

Examples:
  - Fix a broken link in docs
  - Add missing error handling in a code path
  - Improve a confusing error message
  - Add unit test for untested function
  - Fix a minor UI bug (button alignment, missing label)
  - Update a stale README

# Maintenance: team reviews and refreshes starter bugs monthly
# Labels: `good-first-issue`, `starter-bug`, `onboarding`
```

---

## Step 21: Inner Source Patterns

Source: InnerSource Commons — https://innersourcecommons.org/.

Inner source = apply open-source practices within your organization. Teams contribute to other teams' repositories instead of forking, duplicating, or waiting for the owning team.

### Trusted Committer Role

```
# Trusted Committer (TC) = maintainer of a shared repository
# Analogous to open-source maintainer, but within the org

TC responsibilities:
  - Review PRs from other teams (within 48 hours)
  - Maintain contribution guidelines (CONTRIBUTING.md)
  - Triage issues from contributors outside the owning team
  - Mentor contributors: explain architecture, conventions, patterns
  - Merge or delegate merge authority to frequent contributors
  - Maintain CODEOWNERS for their repository

TC selection criteria:
  - Deep knowledge of the repository/service
  - Willingness to review external PRs (not just own team's)
  - Good communication (explain WHY, not just WHAT to change)
  - Time allocation: TC duties should be part of sprint capacity (10-20%)

# Scaling TCs:
  - 1 TC per repo minimum, 2-3 for high-traffic repos
  - Promote frequent contributors to TC after 3+ accepted PRs
  - TC burns out if too many contributions → add more TCs, not restrict contributions
```

### 30-Minute Rule

```
# Rule: if you can't figure out how to contribute to a repo in 30 minutes, the repo failed

What "30 minutes" means:
  - Clone repo
  - Read README and CONTRIBUTING.md
  - Run tests locally (all pass)
  - Understand directory structure
  - Find where your change would go

What makes a repo pass the 30-minute rule:
  - Clear README: what this is, how to run it, who owns it
  - CONTRIBUTING.md: how to fork, branch, PR, test, get review
  - Makefile / script: `make setup && make test` works on fresh clone
  - Devcontainer or Nix flake: zero-config environment
  - CI runs on PR: contributor sees pass/fail without manual setup
  - CODEOWNERS: contributor knows who to ping for review

What fails the 30-minute rule:
  - README says "ask @person for setup instructions"
  - Tests require specific environment vars, databases, or third-party credentials
  - No clear entry point (20 top-level directories, no structure guide)
  - CI only runs on main branch (PRs don't get validation)
```

### GIG Model (Governed Inner Source Gateway)

```
# GIG = structured process for accepting contributions from outside the owning team

# Governance layers:

1. CONTRIBUTION GUIDE (published to internal portal)
   - Which repos accept inner-source contributions
   - How to propose a change (issue first, then PR)
   - SLA for PR review: 48 hours for initial response
   - What changes are appropriate (bug fix, small feature, docs)
   - What changes require owning team involvement (architecture, API changes)

2. CONTRIBUTION AGREEMENT
   - Contributing team owns the code they add (maintains it, fixes bugs)
   - Owning team retains architectural control
   - Breaking changes require owning team approval
   - Both teams listed in CODEOWNERS for shared areas

3. QUALITY GATES
   - All contributions pass same CI as owning team's code
   - Same test coverage threshold
   - Same linting and style enforcement
   - Trusted Committer signs off on merge

4. ESCALATION PATH
   - If TC not responsive within 48h → escalate to TC's manager
   - If teams disagree on architecture → escalate to shared architect or RFC process
   - If contribution rejected → TC provides clear reason and alternative approach
```

### Repository Model

```
# Two models for inner-source repositories:

MODEL A: Shared Repository (recommended for most orgs)
  - One canonical repo per service/library
  - Owning team maintains it
  - Other teams contribute via PRs (governed by GIG)
  - CODEOWNERS lists both owning team and frequent contributors
  - Pros: single source of truth, no fork divergence
  - Cons: owning team review bottleneck if TCs are scarce

MODEL B: Fork + Upstream
  - Teams fork the canonical repo
  - Work in their fork, submit PRs to upstream
  - Upstream team reviews and merges
  - Pros: contributors have autonomy, no blocking on main repo
  - Cons: fork divergence risk, merge conflicts, sync overhead
  - Best for: large orgs with many contributors, regulatory separation

# Decision matrix:
| Factor                    | Shared Repo | Fork + Upstream |
|---------------------------|-------------|-----------------|
| Contributor frequency     | Low-Medium  | High            |
| Contributor team count    | < 10        | > 10            |
| Code divergence risk      | Low         | Medium          |
| Maintenance overhead      | Low         | Medium-High     |
| Regulatory requirements   | None        | Some            |
```

### Benefits: 20-40% Code Dedup

```
# Measured benefits from inner-source adoption (industry data):

Code deduplication: 20-40% reduction in duplicated code
  - Teams stop building their own auth, logging, config, etc.
  - One well-maintained library beats 5 forked copies
  - Metric: count unique implementations of same functionality before/after

Other quantified benefits:
  - Bug fix propagation: fix once, all consumers get it (vs. 5 fixes in 5 forks)
  - New service bootstrap: 2-3 days (using shared templates/libs) vs. 2-3 weeks
  - Cross-team knowledge: engineers understand other services (reduces bus factor)
  - Review quality: more eyes on shared code = fewer bugs shipped

Costs and risks:
  - TC review time (10-20% of sprint capacity)
  - Governance overhead (GIG process, agreements)
  - Political friction ("you're changing MY code")
  - Initial investment: 2-4 weeks to set up contribution guides + CI for shared repos

# Rollout strategy:
  Phase 1 (1 month): Identify 3-5 highest-duplication libraries/services
  Phase 2 (2 weeks): Add CONTRIBUTING.md, devcontainer, starter bugs to each
  Phase 3 (1 month): Pilot inner-source with 2-3 contributing teams
  Phase 4 (ongoing): Measure dedup, contributor satisfaction, review SLA
  Phase 5: Expand to more repos, formalize GIG process
```

---

## Step 22: Product Analytics

### Tool Selection: Amplitude vs Mixpanel vs PostHog

```
# Product analytics platform comparison — 2024/2025 state

| Dimension       | Amplitude                        | Mixpanel                         | PostHog                           |
|-----------------|----------------------------------|----------------------------------|-----------------------------------|
| Type            | Cloud SaaS                       | Cloud SaaS                       | Open-source / self-host or cloud  |
| Core Strength   | Behavioral cohorts, predictive   | Funnel/retention, simplicity     | All-in-one (analytics, flags,     |
|                 | analytics, experimentation       |                                  | session replay, surveys)          |
| Pricing Model   | MTU-based (free tier: 50K MTU)   | Events-based (free: 20M/mo)     | Events-based (free: 1M/mo self-  |
|                 | Enterprise: $$$                  | Growth: $20+/mo                  | hosted). Cloud: $0.00005/event    |
| Privacy         | US-hosted, SOC2, HIPAA (ent.)    | US-hosted, SOC2, GDPR           | Self-host option = full data      |
|                 |                                  |                                  | control. EU cloud available.      |
| Best For        | B2C at scale, growth teams,      | Mid-market SaaS, quick setup,    | Privacy-first orgs, startups,     |
|                 | advanced experimentation         | event-driven products            | teams wanting unified stack       |

# Decision guide:

1. Need self-hosting or data sovereignty? -> PostHog
2. Need advanced behavioral cohorts + ML predictions? -> Amplitude
3. Need fastest time-to-value with clean UI? -> Mixpanel
4. Want analytics + feature flags + replays in one tool? -> PostHog
5. Already on GCP/AWS with enterprise budget? -> Amplitude

# Implementation checklist:
  - Define event taxonomy BEFORE instrumenting (avoid event sprawl)
  - Use tracking plan with schema validation (Amplitude Taxonomy, Mixpanel Lexicon)
  - Instrument server-side for revenue/auth events (client-side blocks by ad blockers)
  - Set up identity resolution (anonymous -> identified user merge)
  - Create 5-10 core dashboards: activation funnel, retention cohorts, feature adoption,
    revenue by segment, experiment results
  - Add data governance: naming conventions, PII scrubbing, retention policies
  - Weekly data quality audit: check for duplicate events, schema drift, missing properties
```

### Event Taxonomy Template

```
# Standard event naming: [noun].[verb] or [object]_[action]

# Examples:
user.signup
user.login
feature.activated
checkout.started
checkout.completed
subscription.upgraded
subscription.cancelled

# Properties (standard across all events):
  user_id        - authenticated user ID
  anonymous_id   - device/session ID
  timestamp      - ISO 8601
  platform       - web | ios | android | api
  app_version    - semver
  experiment_*   - active experiment variants (for attribution)

# Anti-patterns:
  - camelCase vs snake_case inconsistency
  - Usernames or emails in event names
  - Events tied to UI element names (button_clicked_v2)
  - Missing properties on conversion events
```

---

## Step 23: A/B Testing at Scale

### Industry Experimentation Platforms

```
# How the biggest tech companies run experiments at scale

# NETFLIX — XP (Experimentation Platform)
# Scale: 250+ concurrent experiments at any time
# Key innovations:
  - Causal inference methods for observational data
  - "Cell-based" randomization: users assigned to cells, experiments
    assigned to cells (avoids interaction effects)
  - Per-metric confidence intervals (not just p-values)
  - Guardrail metrics: every experiment must not degrade latency,
    error rates, or engagement baseline
  - Culture: nearly every product change is A/B tested before full rollout

# MICROSOFT — ExP (Experimentation Platform)
# Scale: 10,000+ experiments/year across Bing, Office, Azure, etc.
# Key innovations:
  - OEC (Overall Evaluation Criterion): single metric combining
    short-term + long-term value (prevents metric gaming)
  - Variance reduction: CUPED (Controlled-experiment Using Pre-Experiment Data)
    reduces variance by 50%, enabling smaller sample sizes
  - Trustworthy Online Controlled Experiments (book by Tang et al.)
  - SRM (Sample Ratio Mismatch) detection: automatic alerts when
    treatment/control split deviates from expected

# GOOGLE — Overlapping Experiments Framework
# Scale: thousands of concurrent experiments on search, ads, etc.
# Key innovations:
  - Layered experiment framework: each traffic layer is independently
    randomized. Experiments in different layers don't interact.
  - Domain-level isolation: one experiment can't affect another's results
  - Traffic splitting: fractional factorial designs for interaction testing
  - Bayesian + frequentist hybrid approaches

# Common lessons across all three:

  1. GUARDRAIL METRICS are non-negotiable
     - Every experiment checks latency, error rate, engagement floor
     - If guardrail breaks, experiment auto-stops or blocks launch
     - Prevents "win on primary metric, lose on everything else"

  2. SRM (Sample Ratio Mismatch) detection
     - If you expect 50/50 split but get 49.2/50.8, something is wrong
     - Common causes: different cookie persistence, bot traffic, AAU issues
     - Always check SRM before analyzing results (invalidates experiment otherwise)

  3. Sequential testing
     - Don't peek at results and stop early (inflates false positive rate)
     - Use sequential methods: always-valid p-values, mSPRT, or Bayesian
     - Allows valid early stopping when effect is obvious
     - Netflix uses always-valid confidence sequences

  4. Multiple testing correction
     - 20 metrics × 4 variants = 80 comparisons. Expect 4 false positives.
     - Use Benjamini-Hochberg FDR or hierarchical testing
     - Pre-register primary metric to avoid garden of forking paths

  5. Long-term holdouts
     - Keep 5-10% control group for weeks/months to measure lasting impact
     - Short-term wins (novelty effect) vs. long-term value differ
     - Microsoft requires holdouts for all major changes

  6. Experimentation maturity model:
     Level 0: No experiments, ship by opinion
     Level 1: Occasional A/B test for big features
     Level 2: Most features tested, basic tooling
     Level 3: Platform team, standard tooling, guardrails enforced
     Level 4: Culture of experimentation, every change tested, advanced stats
```

### Experiment Design Template

```
# Experiment Brief

Hypothesis: [If we change X, then Y metric will improve by Z% because reason]
Primary metric: [one metric that decides go/no-go]
Guardrail metrics: [metrics that must not degrade]
Target population: [who is eligible]
Variant split: [e.g., 50/50 or 90/10]
Sample size needed: [from power analysis — use Evan Miller calculator or similar]
Minimum detectable effect (MDE): [smallest change worth detecting]
Duration: [based on sample size + weekly traffic]
Pre-registration: [link to analysis plan written BEFORE experiment starts]

# Analysis plan (write BEFORE running):
  - Primary analysis: [metric, statistical test, significance level]
  - Segmentation: [dimensions to cut results by — device, geo, tenure]
  - Guardrail checks: [automated alerts if any guardrail degrades >X%]
  - SRM check: [chi-squared test on assignment ratios]
  - Multiple testing correction: [method if >1 primary metric]

# Launch criteria:
  - Primary metric significant at p < 0.05 (or Bayesian equivalent)
  - No guardrail metric degraded beyond threshold
  - No SRM detected
  - Effect validated in at least 2 key segments
  - Long-term holdout plan (if applicable)
```

---

## Step 24: Feature Flag Experimentation

### Platform Comparison

```
# Feature flag + experimentation platform comparison

| Dimension      | LaunchDarkly          | Unleash              | Statsig             | Flipt              | Flagsmith           |
|----------------|-----------------------|----------------------|---------------------|--------------------|---------------------|
| Type           | Cloud SaaS            | Open-source / cloud  | Cloud SaaS          | Open-source        | Open-source / cloud |
| Flags          | Yes (best-in-class)   | Yes                  | Yes                 | Yes                | Yes                 |
| Experimentation| A/B, multivariate     | Basic (via plugins)  | Advanced (Bayesian) | Basic (via API)    | Basic (A/B only)    |
| Analytics      | Built-in + integrations| Requires integration | Built-in stats engine| Requires integration| Requires integration|
| Pricing        | $$ (seat-based,       | Free self-host.      | Generous free tier. | Free self-host.    | Free self-host.     |
|                | enterprise $$$)       | Cloud: €€/mo         | Pay per event       | Cloud: $$          | Cloud: $$           |
| Self-Host      | No                    | Yes                  | No                  | Yes                | Yes                 |
| SDK Languages  | 20+                   | 12+                  | 10+                 | 15+ (server only)  | 12+                 |
| Best For       | Enterprise, complex   | Privacy-first,       | Data-driven teams,  | Lightweight,       | Budget-conscious,   |
|                | targeting at scale    | self-host priority   | full experimentation| minimal infra      | basic needs         |

# Decision guide:

1. Need advanced experimentation + built-in stats? -> Statsig
2. Need enterprise-grade flags with complex targeting? -> LaunchDarkly
3. Must self-host everything? -> Unleash or Flipt
4. Want simplest possible setup? -> Flipt (single binary)
5. Need mobile SDKs + remote config? -> LaunchDarkly or Flagsmith
```

### Architecture Pattern: SDK -> Exposure -> Analysis -> Guardrails

```
# Feature flag experimentation architecture

┌─────────────┐     ┌──────────────┐     ┌───────────────┐
│  Client SDK  │────>│  Flag Service │────>│  Assignment   │
│  (evaluate   │     │  (rules,     │     │  Logger       │
│   flags)     │     │   targeting) │     │  (who got     │
└─────────────┘     └──────────────┘     │   what)       │
                                          └───────┬───────┘
                                                  │
                                                  v
┌─────────────┐     ┌──────────────┐     ┌───────────────┐
│  Product     │<────│  Analysis    │<────│  Event        │
│  Dashboard   │     │  Pipeline    │     │  Pipeline     │
│  (decisions) │     │  (stats,     │     │  (outcomes,   │
│              │     │   CUPED,     │     │   metrics)    │
│              │     │   SRM check) │     │               │
└─────────────┘     └──────────────┘     └───────────────┘
       │
       v
┌─────────────┐
│  Guardrail   │
│  Monitor     │──> Auto-rollback if guardrail breaks
│  (latency,   │
│   errors)    │
└─────────────┘

# Key design principles:

1. ASSIGNMENT LOGGING IS CRITICAL
   - Log every flag evaluation with: user_id, flag_key, variant, timestamp
   - Without assignment logs, you can't attribute outcomes to variants
   - Server-side logging preferred (client can lose events)

2. SEPARATE ASSIGNMENT FROM OUTCOME
   - Assignment: who got which variant (logged at evaluation time)
   - Outcome: what they did (logged via analytics pipeline)
   - Join on user_id + timestamp for analysis
   - This separation prevents biases from late-arriving data

3. STICKINESS
   - Same user must always get same variant (hash-based assignment)
   - Use: hash(user_id + flag_key) % 100 < percentage
   - Never use random() on each request (non-sticky = useless for experiments)

4. GUARDRAIL INTEGRATION
   - Every flag rollout has automatic guardrail checks
   - Guardrails: p99 latency, error rate, core engagement metrics
   - If guardrail breaks: auto-disable flag, alert on-call
   - Think of guardrails as circuit breakers for experiments

5. FLAG HYGIENE
   - Every flag has an owner and expiry date
   - Stale flag cleanup: flags older than 90 days with 100% rollout -> remove
   - Flag naming convention: [team]-[feature]-[purpose]
     e.g., growth-checkout-redesign-experiment
   - Archive completed experiments (don't just leave flags around)
```

### Implementation Checklist

```
# Rolling out feature flag experimentation:

Phase 1 (1-2 weeks): Flag infrastructure
  [ ] Choose platform (evaluate with pilot team)
  [ ] Install SDKs (server-side first — most reliable)
  [ ] Define flag naming conventions and taxonomy
  [ ] Set up flag management UI access for PMs

Phase 2 (2-3 weeks): Experimentation plumbing
  [ ] Implement assignment logging (server-side)
  [ ] Connect assignment logs to analytics pipeline
  [ ] Build SRM check (automated, runs daily)
  [ ] Create experiment analysis template/dashboard

Phase 3 (2-4 weeks): Guardrails and process
  [ ] Define guardrail metrics per service
  [ ] Set up auto-rollback triggers
  [ ] Write experiment review checklist (template above)
  [ ] Train team on experiment design (power analysis, MDE)

Phase 4 (ongoing): Culture
  [ ] Every feature launch goes through flag -> experiment -> rollout
  [ ] Monthly experiment review (what shipped, what killed, what learned)
  [ ] Quarterly flag cleanup (remove stale flags)
  [ ] Share experiment wins/losses in team demos
```

---

## Step 25: Product Metrics Frameworks

### AARRR (Pirate Metrics)

```
# AARRR — Dave McClure's framework for startup metrics
# Maps the full user journey into 5 stages

ACQUISITION — How do users find you?
  Metrics: traffic sources, CAC (cost per acquisition), signup rate,
           channel breakdown (organic, paid, referral, direct)
  Question: "Which channels bring users who actually convert?"

ACTIVATION — Do users have a great first experience?
  Metrics: time to first value, onboarding completion rate,
           "aha moment" completion (e.g., Facebook's 7 friends in 10 days)
  Question: "What action predicts long-term retention?"

RETENTION — Do users come back?
  Metrics: DAU/MAU ratio, cohort retention curves (D1, D7, D30),
           feature-specific retention (not just login)
  Question: "Are we building a habit or a one-time use?"

REVENUE — Do users pay?
  Metrics: ARPU, LTV, conversion to paid, expansion revenue,
           churn rate, net revenue retention
  Question: "Is the business model sustainable?"

REFERRAL — Do users tell others?
  Metrics: viral coefficient (K-factor), NPS, referral conversion rate,
           invite rate, organic share rate
  Question: "Does growth compound or require constant input?"
```

### North Star Metric

```
# North Star Metric — one metric that captures core value delivery
# Every team optimizes toward this. Other metrics are input levers.

# Examples by company:

Airbnb         -> Nights booked
Spotify        -> Time spent listening
Slack          -> Messages sent in channels (weekly)
Facebook       -> Daily Active Users (DAU)
Uber           -> Rides completed per week
Dropbox        -> Files saved
Amazon         -> Purchase frequency (orders per customer per year)
Zoom           -> Weekly hosted meetings
Netflix        -> Viewing hours per subscriber
Stripe         -> Total payment volume processed
Notion         -> Weekly active editors
Discord        -> Messages sent per DAU
Figma          -> Files edited per week

# How to choose your North Star:

1. It must reflect value delivered to the customer (not revenue)
2. It must be a LEADING indicator of revenue (revenue is lagging)
3. It must be measurable and attributable
4. It must be actionable — teams can move it by building features
5. It should capture frequency AND depth of engagement

# Anti-patterns:
  - Revenue as North Star (lagging indicator, not directly actionable)
  - Vanity metrics (total signups without retention context)
  - Too complex (composite of 10 things = nobody understands it)
  - Not connected to daily product decisions

# North Star + Input Metrics:

North Star: Weekly Active Editors (Notion)
  Input lever 1: New user activation rate
  Input lever 2: Template usage rate
  Input lever 3: Collaboration features adoption
  Input lever 4: Mobile app DAU
  Input lever 5: Time to first edit

# Each team owns 1-2 input levers. Improving inputs moves the North Star.
```

### HEART Framework (Google)

```
# HEART — Google's framework for user-centered metrics at scale
# Designed for measuring UX quality, not just business outcomes

HAPPINESS — How do users feel about the product?
  Metrics: NPS, CSAT, SUS (System Usability Scale), survey ratings
  When to use: after major UX changes, quarterly surveys
  Example: "NPS improved from 32 to 45 after redesign"

ENGAGEMENT — How much are users interacting?
  Metrics: sessions per week, actions per session, feature usage depth,
           time in product, content created
  When to use: measuring stickiness and habit formation
  Example: "Average 4.2 sessions/week (up from 3.1)"

ADOPTION — Are new users starting to use the product/feature?
  Metrics: new user signups, feature adoption rate, first action completion,
           trial-to-paid conversion
  When to use: new feature launches, growth measurement
  Example: "38% of existing users tried new search within 2 weeks"

RETENTION — Are users coming back over time?
  Metrics: cohort retention curves, churn rate, reactivation rate,
           feature-level retention (not just account-level)
  When to use: long-term product health measurement
  Example: "D30 retention improved from 22% to 31% for cohort"

TASK SUCCESS — Can users accomplish what they came to do?
  Metrics: task completion rate, time on task, error rate,
           search success rate, support ticket rate per task
  When to use: measuring efficiency of core workflows
  Example: "Checkout completion rate: 68% (target: 75%)"

# How to use HEART:
  1. Pick 2-3 HEART categories most relevant to current goals
  2. Define specific signals for each category
  3. Convert signals into measurable metrics
  4. Set targets and track over time
  5. Don't try to optimize all 5 simultaneously — focus on weakest
```

### Choosing a Framework

```
# Framework selection guide:

┌────────────────────┬──────────────────────────────────────────────┐
│ Situation          │ Recommended Framework                        │
├────────────────────┼──────────────────────────────────────────────┤
│ Early-stage startup│ AARRR (focus on activation + retention)      │
│ Scaling startup    │ North Star + AARRR inputs                    │
│ Enterprise product │ HEART + North Star                           │
│ UX redesign        │ HEART (happiness + task success)             │
│ Growth team        │ AARRR (acquisition + referral) + experiments │
│ Platform/product   │ North Star (usage frequency) + HEART         │
│ B2B SaaS           │ North Star + AARRR (retention + revenue)     │
│ Consumer app       │ AARRR (activation + retention + referral)    │
└────────────────────┴──────────────────────────────────────────────┘

# You can combine frameworks. Common combos:

1. North Star (company level) + AARRR (team level)
   - Company rallies around one metric
   - Each team owns one AARRR stage as their primary focus

2. AARRR (funnel health) + HEART (UX quality)
   - AARRR tells you if the business is working
   - HEART tells you if users are happy
   - You can have great AARRR with terrible HEART (short-term growth, long-term churn)

3. North Star (outcome) + Input Metrics (leading indicators)
   - North Star is what you optimize
   - Input metrics are what teams can directly change
   - Weekly review: "did our input levers move? did North Star follow?"

# Metrics review cadence:
  Daily: operational metrics (errors, latency, uptime)
  Weekly: product metrics (North Star, key AARRR metrics, experiment results)
  Monthly: business metrics (revenue, churn, CAC, LTV)
  Quarterly: strategic metrics (HEART scores, market share, NPS trend)
```

---

## Step 26: Shape Up

### Overview
Source: Ryan Singer, Basecamp. "Shape Up: Stop Running Sprints and Ship in Six Weeks."

Shape Up is a product development methodology that replaces sprints/standups with fixed 6-week cycles, shaped work with appetite (time budget), and a betting table (no backlog).

### Core Cycle: 6 Weeks Build + 2 Weeks Cooldown

```
SHAPE UP CYCLE:

  ┌─────────────────────────────────────────────┐
  │            6-WEEK BUILD CYCLE                │
  │                                              │
  │  Shaping → Betting → Building → Presenting  │
  │  (pre-cycle) (day 1) (weeks 1-6) (week 6)  │
  └─────────────────────────────────────────────┘
  ┌─────────────────────────────────────────────┐
  │           2-WEEK COOLDOWN                    │
  │                                              │
  │  Bug fixes, refactoring, exploration,        │
  │  writing, learning, decompressing            │
  └─────────────────────────────────────────────┘

# Key rules:
- Fixed time, variable scope (not fixed scope, variable time)
- If it doesn't ship in 6 weeks, it doesn't ship (circuit breaker)
- No sprints, no story points, no velocity tracking
- Cooldown is NOT for finishing build cycle work
```

### Appetite (Time Budget Before Solutioning)

```
APPETITE: Time budget set BEFORE any solution is designed.

# Process:
1. Stakeholder raises a problem or opportunity
2. Shaper asks: "How much time is this worth?"
   - Small batch: 1-2 weeks
   - Big batch: 6 weeks (full cycle)
3. Appetite constrains the solution, not the other way around
4. "We can't afford a perfect solution, but we can afford X weeks"

# Why appetite-first:
- Prevents gold-plating (solution expands to fill available time)
- Forces honest tradeoffs ("Is this worth 6 weeks of the company's time?")
- Aligns cost/benefit before emotional attachment to solutions
- Appetite is a ceiling, not a target — ship when it's good enough

# Example:
  Problem: "Users can't find their saved reports"
  Appetite: 2 weeks
  Shaped solution: Quick-search with recent + pinned reports
  NOT shaped: Full reporting dashboard redesign (that's 6 weeks)
```

### Shaping (Problem + Solution Boundaries)

```
SHAPING: Define boundaries for a solution before committing to build.

# Three levels of definition:
  1. Raw idea      — vague, unbounded ("we need better reporting")
  2. Shaped idea   — bounded, with appetite and rough solution
  3. Under construction — detailed, being built

# Shaping happens in the "before" phase (previous cooldown or ongoing):
  - Problem definition: What exactly is broken? For whom? What's the context?
  - Solution boundaries: What's in/out? What's the rough UI/UX/tech?
  - Rabbit holes: What's tricky? What assumptions might fail?
  - No-gos: What are we explicitly NOT doing?

# Shaping output — a "pitch":
  ┌──────────────────────────────────────────┐
  │ PITCH TEMPLATE                           │
  ├──────────────────────────────────────────┤
  │ Problem: [What's wrong and for whom]     │
  │ Appetite: [Time budget]                  │
  │ Solution: [Rough sketch + boundaries]    │
  │ Rabbit holes: [Risks and unknowns]       │
  │ No-gos: [Explicit exclusions]            │
  └──────────────────────────────────────────┘

# Shaping rules:
- Shape at the right level of abstraction (not too vague, not too concrete)
- Shaper must NOT be the builder (avoid premature commitment)
- Fat marker sketches (low-fidelity) to avoid design rabbit holes
- Betting table evaluates pitches, not features
```

### Betting Table (No Backlog)

```
BETTING TABLE: Where pitches get funded. No persistent backlog.

# The anti-backlog:
- No product backlog. No grooming. No story pointing.
- Pitches that don't get bet on are DISCARDED (not queued)
- If the idea is good, someone will re-raise it next cycle
- This prevents backlog bloat, zombie tickets, and priority debates

# Betting table composition:
  - CEO / Head of Product
  - CTO / Head of Engineering
  - Key stakeholders (as needed)

# Betting process:
1. Review all pitches from current shaping cycle
2. Each pitch has appetite + rough solution + risks
3. Bet on pitches: commit 1-2 teams to each for the 6-week cycle
4. Leave capacity for "bugs and keep-the-lights-on" work (~15-20%)
5. Unbeted pitches are discarded (not deferred)

# Betting rules:
- Once a bet is placed, team has autonomy on HOW to build
- No mid-cycle scope changes (protect the team's focus)
- If a bet proves wrong, circuit breaker fires (see below)
```

### Circuit Breaker (Project Dies If Not Done)

```
CIRCUIT BREAKER: If the work doesn't ship in 6 weeks, it's dead.

# How it works:
1. Team commits to shipping within the 6-week cycle
2. At week 6, the team presents what they shipped
3. If it's not done → the project is KILLED (not extended, not reshaped)
4. The team moves on to the next cycle's bets

# Why this works:
- Forces ruthless scope cutting during the build ("is this essential?")
- Prevents death marches ("just one more sprint")
- Creates urgency without overtime (fixed time, variable scope)
- Dead projects can be re-shaped with fresh eyes in a future cycle

# Scope hammering (related technique):
  Ask for every feature during build:
  - "Does this matter for the core concept?"
  - "Can we ship without this?"
  - "Is this edge case worth the time?"
  If not → cut it. The circuit breaker doesn't care about completeness.

# Exceptions:
- Critical security issues get immediate attention (outside cycle)
- Infrastructure emergencies are handled by designated on-call
```

### Hill Chart (Unknowns → Knowns)

```
HILL CHART: Track progress by moving from unknowns to knowns.

# Concept:
  Left side of hill  = figuring out WHAT and HOW (unknowns)
  Top of hill         = the inflection point (plan is clear)
  Right side of hill  = executing the plan (knowns)

       ╱╲
      ╱  ╲
     ╱    ╲
    ╱      ╲
  UNKNOWN   KNOWN

# How to use:
1. Break project into scopes (independent pieces of work)
2. Place each scope on the hill chart
3. Update positions weekly (move scopes rightward)
4. If a scope moves LEFT (regression), that's a red flag

# Anti-patterns:
- Stuck at the top: "We know what to do but haven't started"
- Bouncing back and forth: scope is undershaped (needs more shaping)
- Everything on the right but not shipped: polish trap, cut scope

# Hill chart vs. task boards:
- Task boards show WHAT needs to be done (binary: done/not done)
- Hill charts show CONFIDENCE in each scope (spectrum: unknown → known)
- A task board with 8/10 tasks done can still have massive unknowns
```

### Scope Hammering

```
SCOPE HAMMERING: Relentlessly cut scope to fit the appetite.

# Technique: For every feature/requirement, ask:
1. "What's the core concept this feature supports?"
2. "Does removing this break the core concept?"
3. "Can users work around this missing piece?"
4. "Is this edge case worth X days of our 6-week budget?"

# Scope hammering toolkit:
  - Cut the feature entirely
  - Limit to a subset of users (e.g., only English, only desktop)
  - Manual workaround first (e.g., admin does it, not automated)
  - Simplify the UI (fewer options, fewer screens)
  - Remove error handling (fail loudly, fix later)
  - Defer polish (ugly but functional)

# Example:
  Feature: "Email notification preferences"
  Appetite: 3 days
  Full scope: per-notification-type toggles, quiet hours, digest mode
  Hammered scope: one toggle (on/off) + manual unsubscribe link
  Why: digest mode is a separate appetite; quiet hours is an edge case
```

---

## Step 27: Continuous Deployment Culture

### Overview
Source: Etsy, Flickr, GitHub engineering blogs. DevOps culture pioneers.

Continuous deployment is a culture, not just a pipeline. Companies that deploy 50-100x/day share common practices: feature flags, observability-first, small batches, and trust in engineers.

### Etsy: Deploy Train

```
ETSY DEPLOY MODEL (2010s-2020s):

# Deploy train concept:
- Scheduled deploys happen on a fixed cadence (e.g., every 2 hours)
- Engineers "board the train" by merging to main branch
- If your change causes issues, you get off (revert) and board next train
- No approvals, no release managers — just merge and ship

# "If it moves, graph it" (Etsy motto):
- Every metric gets a dashboard BEFORE the feature ships
- Engineers define graphs as part of their PR
- Monitoring is code: dashboards version-controlled alongside features
- Alert fatigue addressed by: fewer, better alerts + on-call rotation

# Feature flags at Etsy:
- Flags are first-class citizens, not afterthoughts
- Gradual rollout: 1% → 10% → 50% → 100%
- Every deploy is behind a flag until proven safe
- Flags allow "dark launching" (code runs but users don't see it)

# Etsy's cultural rules:
  1. Everyone deploys their own code (no "release engineer" role)
  2. If you break it, you fix it (blameless postmortem follows)
  3. Make it easy to deploy → people deploy more → smaller changes
  4. Small batches = smaller blast radius = faster recovery
```

### Flickr: 10+ Deploys/Day (Allspaw/Hammond)

```
FLICKR DEPLOY MODEL (2009 — "10+ Deploys Per Day"):

# The seminal DevOps moment:
- John Allspaw + Paul Hammond, Velocity 2009
- Proved that velocity and stability are NOT tradeoffs
- "Ops enables dev to deploy; dev enables ops to monitor"

# Key practices:
1. SINGLE BRANCH (trunk-based development)
   - No long-lived feature branches
   - Merge to main multiple times per day
   - Integration happens continuously, not at release time

2. AUTOMATED INFRASTRUCTURE
   - One-click deploy: engineer pushes button, code goes live
   - Rollback is equally easy (one-click revert)
   - No manual server configuration (Chef/Puppet → infrastructure as code)

3. DEVS ON CALL
   - Developers who write code carry pagers for their services
   - This creates natural incentive to write observable, reliable code
   - "You build it, you run it" (Werner Vogels, Amazon)

4. SMALL BATCHES
   - Each deploy = 1-3 changes (not 50 changes bundled)
   - If deploy breaks, the cause is obvious (few changes to inspect)
   - Rollback affects a small surface area

# Culture shift required:
  FROM: "Ops prevents devs from breaking production"
  TO:   "Ops empowers devs to deploy safely and frequently"
  FROM: "Stability requires slow, careful releases"
  TO:   "Stability requires fast, small, reversible releases"
```

### GitHub: Ship to Master

```
GITHUB DEPLOY MODEL (2010s):

# Ship-to-master philosophy:
- No release branches. Merge to main = deploy to production.
- Every merge is a deploy candidate.
- Continuous integration tests gate every merge.
- Production is always deployable (main branch = source of truth).

# ChatOps:
- Deploy via chat commands: `.deploy production` in Slack/Hubot
- Everyone sees who deployed what and when (transparency)
- Deploy is a conversation, not a ticket in a queue
- Other engineers can see, question, or block deploys in real-time

# Merge-to-deploy pipeline:
  1. Engineer opens PR
  2. CI runs (tests, lint, type check)
  3. Review by 1+ peer (async, not blocking)
  4. Merge to main
  5. Auto-deploy to staging
  6. Engineer verifies on staging
  7. Engineer runs `.deploy production` (ChatOps)
  8. Monitor dashboards + error rates for 15 min
  9. Done. Repeat 10-50x per day across teams.

# Async culture:
- No "deployment windows" or "release days"
- Engineers deploy when ready, not when scheduled
- On-call handles issues; deployer isn't blocked waiting for approval
- README/docs prioritized over meetings (written > verbal)

# Enabling conditions:
  - Comprehensive test suite (>90% coverage on critical paths)
  - Fast CI (<10 min from push to green)
  - Feature flags for incremental rollout
  - Robust rollback (< 5 min to revert any deploy)
  - Observability: logs, metrics, traces on every service
```

### Deploy Frequency Comparison

```
DEPLOY FREQUENCY SPECTRUM:

  Annual release ── Quarterly ── Monthly ── Weekly ── Daily ── Continuous
  │                  │            │          │         │         │
  "Big bang"       Waterfall    Scrum     Some      Etsy     GitHub
  releases        sprints      teams    startups   Flickr    Ideal

# DORA metrics connection:
  Elite performers: multiple deploys/day, <1hr lead time, <1hr MTTR
  High performers:  weekly deploys, <1day lead time, <1day MTTR
  Medium:           monthly, <1week lead time, <1day MTTR
  Low:              <monthly, >6mo lead time, >1week MTTR

# Path from monthly to daily:
  1. Automate deployment (remove manual steps)
  2. Add feature flags (decouple deploy from release)
  3. Shorten CI (parallel tests, caching, selective runs)
  4. Trunk-based development (eliminate long-lived branches)
  5. Put devs on call (they fix what they break)
  6. Measure DORA metrics (lead time, deploy freq, change fail rate, MTTR)
```

---

## Step 28: Building Engineering Team 0→50

### CTO vs VP Engineering

```
CTO vs VP ENGINEERING — different roles, often confused:

CTO (Chief Technology Officer):
  - VISION: Where is the technology going? What's our technical moat?
  - Owns: technical strategy, architecture decisions, build vs. buy
  - External: speaks at conferences, recruits senior talent, shapes brand
  - Reports to: CEO
  - Skills: deep technical expertise, strategic thinking, industry knowledge
  - Typical at: seed → Series B (may evolve into advisory role later)

VP ENGINEERING:
  - EXECUTION: How do we ship reliably at scale?
  - Owns: delivery, process, team health, hiring pipeline, eng metrics
  - External: rarely speaks externally, focused inward on team
  - Reports to: CTO or CEO
  - Skills: people management, process design, operational excellence
  - Typical at: Series A → Series C+ (often replaces or supplements CTO)

# When to have both:
  - <20 engineers: CTO who also manages (one role)
  - 20-50 engineers: CTO + VP Eng split (vision + execution)
  - 50+ engineers: CTO focuses on strategy, VP Eng + Directors manage

# Anti-pattern: "CTO who can't let go of IC work"
  → Blocks team growth, bottlenecks decisions, burns out
  → Solution: hire VP Eng, CTO shifts to architecture + strategy
```

### Phases of Engineering Team Growth

```
PHASE 1: 0-5 ENGINEERS (Foundation)
────────────────────────────────────
- ALL generalists. Everyone codes, deploys, and on-calls.
- No specialization. No "backend only" or "frontend only" hires.
- First 10 hires shape culture permanently (hiring bar, code quality, values)
- Titles are meaningless. Equity is the real incentive.
- Hire: "Can this person build an entire feature end-to-end?"
- Avoid: specialists, architects who don't code, managers

PHASE 2: 5-15 ENGINEERS (First Manager)
────────────────────────────────────
- First dedicated engineering manager (or CTO splits time)
- Begin specializing: someone owns infra, someone owns mobile, etc.
- First on-call rotation (no longer just CTO carrying pager)
- Introduce lightweight process: weekly syncs, retros, 1:1s
- Hire 1-2 levels ahead: if you need a senior IC now, hire a staff-level
- First "culture carriers" who weren't there at founding

PHASE 3: 15-30 ENGINEERS (Team Leads)
────────────────────────────────────
- Team leads emerge (tech leads + people management)
- Teams form around products/features (squads)
- Introduce: architecture reviews, RFC process, code review standards
- First dedicated QA, SRE, or platform roles
- Hiring pipeline needs process (recruiter, structured interviews, scorecards)
- VP Engineering role is full-time (if CTO was managing before)

PHASE 4: 30-50 ENGINEERS (Directors + Process)
────────────────────────────────────
- Directors manage managers (layer of abstraction)
- Clear career ladder: IC track (junior → senior → staff → principal)
  and management track (lead → manager → director)
- Dedicated teams: platform/infra, security, data engineering
- Process scales: sprint cadence, release trains, cross-team coordination
- CTO is now primarily strategic (architecture, vision, recruiting)
```

### Hire 1-2 Levels Ahead

```
HIRE AHEAD: When scaling fast, hire for where you'll be in 12-18 months.

# Why:
- Hiring takes 3-6 months (search + close + ramp)
- If you hire for today's needs, you'll be behind by the time they're productive
- Senior hires raise the bar for everyone around them

# How:
  Current need: senior IC → Hire: staff-level IC
    They'll: mentor juniors, set technical standards, future team lead
  Current need: tech lead → Hire: engineering manager
    They'll: run the team, hire more engineers, scale processes
  Current need: eng manager → Hire: director
    They'll: build the management layer, define eng org structure

# Exceptions:
- Don't hire 3+ levels ahead (misaligned incentives, boredom, cost)
- Don't hire only senior (no pipeline for future leaders)
- Balance: 60% at level, 30% one level ahead, 10% stretch hires

# First 10 hires shape culture:
  - Code quality bar (do they review each other's code?)
  - Speed vs. craft (ship fast or build to last?)
  - Communication style (async vs. sync, docs vs. meetings)
  - Values (customer obsession vs. tech elegance vs. team harmony)
  - These norms persist even after original 10 are gone
```

### Engineering Team Scaling Checklist

```
SCALING CHECKLIST:

0-5 Engineers:
  [ ] Founding team covers: backend, frontend, infra, product sense
  [ ] Single deploy pipeline (CI/CD from day 1)
  [ ] On-call: founder/CTO carries pager
  [ ] Communication: standup (5 min) + weekly retro

5-15 Engineers:
  [ ] First engineering manager hired (or promoted)
  [ ] 1:1s established for every engineer
  [ ] On-call rotation (no more single-person pager)
  [ ] Code review standard established
  [ ] Architecture RFC process started
  [ ] Hiring process documented (interview stages, scorecard)

15-30 Engineers:
  [ ] Team structure: 2-3 squads (product-aligned)
  [ ] Team leads identified (tech lead + EM per squad)
  [ ] Career ladder published (IC + management tracks)
  [ ] Dedicated SRE/platform team (at least 2 people)
  [ ] Cross-team coordination: guild meetings, architecture council
  [ ] Onboarding program (2-week ramp plan for new hires)

30-50 Engineers:
  [ ] Director-level managers (managing managers)
  [ ] Engineering-wide OKRs aligned to company OKRs
  [ ] Internal mobility (engineers can move between teams)
  [ ] Formal mentorship program
  [ ] Engineering brand (blog posts, open source, conference talks)
  [ ] DORA metrics tracked and reviewed quarterly
```

---

## Step 29: Product-Market Fit

### Overview
Source: Sean Ellis (GrowthHackers), Rahul Vohra (Superhuman).

Product-market fit (PMF) is when your product satisfies strong market demand. Before PMF, focus on retention and love (not growth). After PMF, pour fuel on the fire.

### Sean Ellis Test

```
SEAN ELLIS TEST — The simplest PMF measurement:

# The question (survey existing users):
  "How would you feel if you could no longer use [product]?"

  A) Very disappointed
  B) Somewhat disappointed
  C) Not disappointed
  D) N/A — I no longer use it

# THE THRESHOLD:
  ≥ 40% respond "Very disappointed" = you have product-market fit
  < 40% = you don't (yet). Focus on improving the product, not marketing.

# Why 40%?
- Ellis analyzed hundreds of startups
- Companies that hit 40%+ "very disappointed" grew sustainably
- Below 40%, growth tactics (ads, referrals) don't stick — users leak

# How to run the survey:
1. Survey ACTUAL USERS (not just signups — people who've used it)
2. Minimum sample size: 40-50 responses (more is better)
3. Include open-ended follow-up: "What would you use instead?"
4. Segment by user type (free vs. paid, recent vs. long-term)
5. Re-run quarterly to track trend

# What to do with results:
  ≥ 40% "very disappointed" → PMF achieved. Start scaling/growth.
  25-40% → Close. Double down on what power users love.
  < 25% → Not there yet. Pivot or fundamentally rethink.
```

### Superhuman Framework (Rahul Vohra)

```
SUPERHUMAN PMF FRAMEWORK — 7 Steps to Find and Increase PMF:
Source: Rahul Vohra, "How Superhuman Built Product-Market Fit"

STEP 1: SURVEY YOUR USERS
  Ask the Sean Ellis question + these follow-ups:
  - "What type of people do you think would benefit most?"
  - "What is the main benefit you receive?"
  - "How can we improve [product] for you?"

STEP 2: SEGMENT YOUR RESPONDENTS
  Categorize into 4 buckets:
  ┌─────────────────────┬──────────────────────┐
  │ Segment             │ Definition           │
  ├─────────────────────┼──────────────────────┤
  │ High-Expectation    │ "Very disappointed"  │
  │ Customers (HXC)     │ = your target market │
  ├─────────────────────┼──────────────────────┤
  │ Supporters          │ "Somewhat            │
  │                     │  disappointed"        │
  │                     │ = like it, don't love │
  ├─────────────────────┼──────────────────────┤
  │ Neutral             │ "Not disappointed"   │
  │                     │ = wrong market       │
  ├─────────────────────┼──────────────────────┤
  │ Disengaged          │ "No longer use it"   │
  │                     │ = churned — learn why│
  └─────────────────────┴──────────────────────┘

STEP 3: FIND YOUR HIGH-EXPECTATION CUSTOMERS (HXC)
  - HXC are the 40%+ who said "very disappointed"
  - Profile them: who are they? What do they do? What do they value?
  - HXC defines your TARGET MARKET (not the market you imagined)
  - Example: Superthought their market was "busy professionals"
    but HXC was "startup founders who live in email"

STEP 4: COMPARE WHAT HXC WANT VS. WHAT SUPPORTERS WANT
  - Ask HXC: "How can we improve for you?"
  - Ask Supporters: "How can we improve for you?"
  - Compare the lists:
    HXC wants: faster keyboard shortcuts, offline mode, integrations
    Supporters want: mobile app, social features, free tier
  - BUILD WHAT HXC WANTS (they're your core; supporters are marginal)

STEP 5: DOUBLE DOWN ON WHAT USERS ALREADY LOVE
  - Look at HXC open-ended responses: "What is the main benefit?"
  - Find the #1 benefit (most frequently mentioned)
  - Make that benefit 10x better (don't add new features — amplify strengths)
  - Example: HXC said "speed" → invest in making it even faster
    (not adding a mobile app that would slow down the core)

STEP 6: IGNORE WHAT USERS DON'T CARE ABOUT
  - Features that HXC don't mention → deprioritize
  - Features that only Supporters want → ignore (wrong audience)
  - "Not disappointed" users' opinions → don't act on them
  - Ruthless focus on what HXC loves, not what everyone wants

STEP 7: REPEAT QUARTERLY
  - Run the survey every quarter
  - Track: % "very disappointed" (should be increasing)
  - If % stalls → you've saturated current market OR product is plateauing
  - Each quarter: re-segment, re-profile HXC, re-prioritize

# Superhuman's actual results:
  - Started at 22% "very disappointed"
  - Applied the framework (focused on speed, keyboard shortcuts)
  - Reached 58% "very disappointed" in 6 months
  - Then poured fuel on growth (referral program, waitlist)
```

### PMF Checklist

```
PMF READINESS CHECKLIST:

Before PMF (focus: product, not growth):
  [ ] Ran Sean Ellis survey (≥ 40 responses)
  [ ] Identified HXC (high-expectation customers)
  [ ] Profiled HXC (who they are, what they value)
  [ ] Listed what HXC loves about product
  [ ] Listed what HXC wants improved
  [ ] Built improvement roadmap focused on HXC needs
  [ ] Re-ran survey quarterly to track % "very disappointed"

After PMF (focus: growth):
  [ ] Retention curves flattening (users aren't churning)
  [ ] Organic growth emerging (referrals, word-of-mouth)
  [ ] NPS > 50 (strong advocacy)
  [ ] CAC payback period < 12 months (growth is profitable)
  [ ] HXC profile informs marketing targeting (ICP defined)
  [ ] Growth experiments start (referral loops, paid acquisition)
  [ ] "Pour fuel on fire" — but only after PMF is confirmed
```

---

## Step 30: High Growth Handbook

Source: Elad Gil — "High Growth Handbook: Scaling Startups from 10 to 10,000 People"

### Key Patterns

#### Hire Executives Before You Think You Need Them
- Start recruiting VPs/directors 6+ months before you need them
- Good exec hires take 3-6 months to close; bad ones cost 6+ months to fix
- You need execs when you feel pain, but by then it's too late — hire proactively
- Pattern: founder does role part-time → recognizes need → starts search → exec arrives just as pain peaks
- Rule of thumb: when founder spends >50% time on a function, hire exec for it

#### 1-3-1 Problem Solving Framework
```
When escalating or bringing problems to leadership:
  1 — One concise problem statement (what's wrong)
  3 — Three viable options with trade-offs (not just one ask)
  1 — One recommended option with reasoning (take a position)

Anti-pattern: "Here's the problem, what should I do?"
Correct pattern: "Here's the problem, here are 3 options with pros/cons, I recommend #2 because..."

Why it works:
  - Forces the person raising the issue to think deeply
  - Prevents delegation of thinking upward
  - Gives decision-maker enough context to decide quickly
  - Builds judgment in the team over time
```

#### Split Teams by Product Area (Not Technology)
```
DON'T split by:
  - Frontend team / Backend team / Infra team (creates dependency chains)
  - "We need a feature" → 3 teams must coordinate → slow

DO split by:
  - Product area: Search team, Checkout team, Notifications team
  - Each team owns end-to-end: frontend + backend + infra + oncall
  - Team owns the outcome (conversion, latency, reliability) not just output (code)

When to split:
  - When team exceeds 8 engineers (communication overhead explodes)
  - When two product areas have divergent priorities
  - When oncall burden becomes unsustainable for single team
```

#### 5-8 Engineers Per Team
- Sweet spot: 5-8 engineers + 1 PM + 1 designer + 1 TL/EM
- Below 5: not enough capacity, bus factor too low
- Above 8: communication overhead dominates, sub-teams form naturally
- Two-pizza rule variant: if you can't feed the team with two pizzas, it's too big
- Exception: platform/infra teams can be 3-4 (different work pattern)

#### Add Process Only When Pain Is Felt
```
STAGE 1: No process (1-20 people)
  - Everyone knows everything, informal communication works
  - Adding process here slows things down for no benefit

STAGE 2: Lightweight process (20-50 people)
  - Add: weekly team syncs, basic sprint planning, RFC process
  - Trigger: "I didn't know that was happening" starts appearing
  - Only add process that solves an observed pain, not theoretical risk

STAGE 3: Structured process (50-200 people)
  - Add: OKRs, quarterly planning, formal oncall, postmortems
  - Trigger: teams duplicating work, inconsistent quality, oncall chaos
  - Process should reduce coordination cost, not create bureaucracy

STAGE 4: Scaled process (200+ people)
  - Add: org-level planning, architecture review boards, compliance
  - Trigger: cross-team failures, regulatory requirements, audit findings
  - Each new process needs a clear owner and sunset condition

ANTI-PATTERN: Adding Netflix/Google process at 15-person startup
ANTI-PATTERN: Running 200-person org with 15-person processes
```

### Scaling Decision Matrix
```
TEAM SIZE    PROCESS NEED           HIRE NEED
1-10         None (communicate)     Generalists who can wear many hats
10-30        Lightweight (syncs)    First specialists + first exec
30-100       Structured (OKRs)      VPs for each major function
100-500      Scaled (planning)      C-level, dedicated ops teams
500+         Enterprise (governance) COO, formal org design
```

### High Growth Handbook Checklist
```
SCALING READINESS CHECKLIST:

Org Design:
  [ ] Teams split by product area (not technology layer)
  [ ] Each team 5-8 engineers with clear ownership
  [ ] Exec search started for functions where founder spends >50% time
  [ ] 1-3-1 framework adopted for escalations

Process Calibration:
  [ ] Current process level matches team size stage
  [ ] Every process item has clear trigger (what pain it solved)
  [ ] No premature process (not adding Google-style at startup size)
  [ ] Quarterly review: which process is no longer needed?

Hiring:
  [ ] Executive pipeline active (even if no opening today)
  [ ] Hiring bar defined and documented
  [ ] Interview process tests for judgment, not just technical skill
  [ ] Onboarding plan exists for each role level
```

---

## Step 31: Engineering Culture Patterns

### Stripe Culture: API-First and Docs-as-Product

Source: Stripe engineering blog, Will Larson (staffeng.com), internal Stripe practices

```
PRINCIPLE: API-first means the API IS the product, not an afterthought

Pattern 1: Docs as Product
  - Documentation is a first-class product with its own PM and eng team
  - Docs ship WITH the feature, not after (blocks launch if missing)
  - Every API endpoint has: reference docs, tutorials, changelog entry
  - Docs are versioned alongside the API
  - Quality bar: "Could a new developer integrate in <30 minutes?"

Pattern 2: 7 Lines of Docs per 1 Line of Code
  - Ratio target: for every line of code, 7 lines of documentation
  - Not 7x comments — 7x of: reference, guide, tutorial, example, changelog
  - Breakdown:
    - 2 lines: API reference (parameters, return values, errors)
    - 2 lines: developer guide (when to use, common patterns)
    - 1 line: code example (copy-pasteable, tested)
    - 1 line: changelog (breaking changes, migration path)
    - 1 line: troubleshooting (common errors, debugging tips)
  - Enforced by: doc review in PR, doc coverage in CI, doc quality metrics

Pattern 3: Empathy Rotation
  - Every engineer spends 1 week/quarter doing support
  - Not tier-1 triage — real issue investigation and customer communication
  - Goal: build empathy for users, discover UX pain points firsthand
  - Output: each rotation produces 3+ "paper cuts" report (small UX fixes)
  - Engineers return to their team with user empathy + actionable insights
  - Leadership participates too (VP eng does rotation, not just ICs)

Pattern 4: API Review Board
  - Every new API endpoint reviewed by API design committee
  - Checklist: naming conventions, pagination, error format, versioning
  - Goal: consistency across 100+ services feels like one product
  - "API is forever" mindset — breaking changes are extremely costly
```

### Netflix Culture: Freedom & Responsibility

Source: Reed Hastings ("No Rules Rules"), Netflix culture deck, Netflix tech blog

```
PRINCIPLE: Hire exceptional people, give them context, get out of the way

Pattern 1: Context, Not Control
  - Manager's job: provide context (strategy, constraints, data) NOT control decisions
  - "Lead with context" — share the WHY, let team figure out the HOW
  - Anti-pattern: manager reviews every PR, approves every decision
  - Netflix pattern: "Farm for dissent" — actively seek disagreement before committing
  - Once decision made after debate, everyone commits (disagree & commit)

Pattern 2: Talent Density
  - Maintain high talent density by paying top of market
  - "Adequate performance gets generous severance" — no coasting
  - One brilliant engineer > three adequate engineers (not just 3x, often 10x)
  - Netflix rule: "Would you fight to keep this person?" If no → transition them out
  - High talent density enables: less process, more freedom, faster execution
  - Counter-intuitive: fewer people + higher talent = more output + less management

Pattern 3: Radical Candor
  - "4A" feedback model:
    - Aim to Assist: feedback must be intended to help
    - Actionable: specific, implementable suggestions
    - Appreciate: receiver listens with gratitude, doesn't have to accept
    - Accept or Discard: receiver decides, no retaliation
  - Feedback is continuous, not saved for performance reviews
  - "Sunshining" — making decisions and reasoning transparent to all
  - Anti-pattern: "feedback sandwich" (praise-criticism-praise) is banned — just say it

Pattern 4: High Alignment, Loose Coupling
  - Top-down: strategy, priorities, metrics (high alignment)
  - Bottom-up: implementation, architecture, process (loose coupling)
  - Teams don't need permission to deploy, experiment, or ship
  - Guard rails via: automated testing, canary deploys, rollback (not approvals)
```

### Cross-Cutting Culture Themes

```
THEME 1: Write Things Down
  - "If it's not written down, it didn't happen" — decisions, context, rationale
  - Benefits: async communication, onboarding speed, institutional memory
  - Tools: RFCs/ADRs, decision logs, team handbooks, architecture docs
  - Anti-pattern: tribal knowledge, "ask Dave, he knows"
  - Metric: "Could someone join tomorrow and understand our decisions?"

THEME 2: Small Autonomous Teams
  - Amazon "two-pizza teams" — small, end-to-end ownership
  - Spotify model variant: squad owns a user journey, not a tech layer
  - Autonomy = authority to decide HOW (not WHAT — strategy is top-down)
  - Autonomy requires: clear goals, measurable outcomes, trust
  - Anti-pattern: autonomous team with no metrics = autonomous team with no accountability

THEME 3: Hire for Density (Not Volume)
  - 10 exceptional engineers > 30 average engineers
  - Hiring bar: "Would I want to work for this person?"
  - Compensation: top 10% of market (total comp, not just salary)
  - Interview loop: technical + system design + culture/values + work simulation
  - Anti-pattern: "we need to hire 20 engineers fast" → lowers bar → more management overhead → slower
  - Netflix/Stripe pattern: be willing to have empty seats rather than fill with mediocre hires
```

### Culture Implementation Checklist
```
CULTURE MATURITY CHECKLIST:

Documentation Culture:
  [ ] RFC/ADR process exists and is used for technical decisions
  [ ] Team handbooks exist (onboarding takes <1 week, not "ask someone")
  [ ] Decision logs maintained (what was decided, why, alternatives considered)
  [ ] Docs ship with features (not "we'll document it later")

Talent & Feedback:
  [ ] Compensation reviewed semi-annually against market
  [ ] Regular feedback culture (not just annual reviews)
  [ ] Low performers addressed within 30 days (not tolerated for quarters)
  [ ] Interview process tests for culture add, not just culture fit

Team Autonomy:
  [ ] Teams own outcomes (metrics) not just outputs (features)
  [ ] Teams can deploy without multi-layer approval
  [ ] Strategy/priorities communicated top-down, implementation is bottom-up
  [ ] Guard rails automated (CI/CD, canary, rollback) not manual (approval gates)
```

---

## Step 32: Google SRE Deep Dive

Source: Google SRE Book, Google SRE Workbook, https://sre.google/

### Toil Measurement

```
DEFINITION: Toil is manual, repetitive, automatable, reactive work that scales linearly with service growth

Examples:
  - Manually deploying releases (not CI/CD)
  - Hand-editing config files across servers
  - Responding to same alert pattern every week
  - Manually scaling capacity before traffic spikes
  - Copy-pasting data between systems

NOT toil:
  - Designing new monitoring dashboards (creative, non-repetitive)
  - Writing automation scripts (reduces future toil)
  - Incident response for novel issues (reactive but not repetitive)
  - Architecture review (strategic, doesn't scale with traffic)

TOIL BUDGET: <50% of engineering time
  - Measure: each engineer logs toil time weekly (survey or time tracking)
  - Target: no more than 50% of SRE time on toil
  - If >50%: STOP feature work, invest in automation until below threshold
  - Quarterly budget review with leadership: "Here's our toil percentage, here's the plan to reduce it"

TOIL REDUCTION CYCLE:
  1. Measure current toil (time spent, categories, frequency)
  2. Categorize: automate, eliminate, or tolerate
  3. Prioritize by: frequency × time_per_occurrence × engineer_cost
  4. Invest 20% of sprint capacity in toil reduction
  5. Track: toil percentage over time (should decrease quarter over quarter)

QUARTERLY TOIL REVIEW TEMPLATE:
  - Current toil %: [X%]
  - Top 3 toil sources: [list]
  - Toil reduced this quarter: [what was automated]
  - Toil added this quarter: [new services/features that added toil]
  - Plan for next quarter: [automation roadmap]
  - Ask: [headcount, tooling, time investment]
```

### Error Budgets

```
CONCEPT: Error budget = 1 - SLO = allowed unreliability

Example:
  SLO: 99.9% availability
  Error budget: 0.1% = ~43 minutes/month of downtime allowed
  If budget remaining > 0%: ship features
  If budget exhausted: STOP feature work, invest in reliability

MULTI-TIER SLOs:
  - Availability SLO: 99.9% of requests succeed (2xx/3xx)
  - Latency SLO: 99% of requests < 200ms
  - Freshness SLO: 99% of data updated within 1 hour
  - Correctness SLO: 99.99% of writes persist correctly
  - Each SLO has its own error budget, tracked independently

BURN RATE ALERTING:
  - Burn rate = how fast you're consuming error budget
  - Burn rate 1.0 = consuming at steady pace (budget exhausted at end of window)
  - Burn rate 2.0 = consuming 2x as fast (budget exhausted in half the window)
  - Burn rate 14.4 = consuming 14.4x as fast (budget exhausted in ~50 min of a 12h window)

MULTI-WINDOW ALERTING (Google recommendation):
  - Short window (5 min): catches severe outages fast
    - Alert if: burn rate > 14.4 AND budget consumed > 2% in 1 hour
    - Action: page on-call immediately
  - Long window (6 hours): catches slow burns
    - Alert if: burn rate > 1.0 AND budget consumed > 5% in 6 hours
    - Action: page on-call, investigate
  - Why both: short window catches big outages, long window catches slow degradation

ERROR BUDGET POLICY:
  - Budget remaining > 50%: normal feature development
  - Budget remaining 20-50%: increased testing, slower rollout cadence
  - Budget remaining < 20%: reliability sprint (no new features)
  - Budget exhausted (0%): freeze feature work, all-hands reliability
  - Budget resets monthly (or per quarter, depending on SLO window)

ERROR BUDGET REPORTING:
  - Weekly: error budget dashboard shared with eng + product
  - Monthly: error budget review in team retro
  - Quarterly: SLO compliance review with leadership
  - Metric: "What % of our error budget did we consume?"
```

### Capacity Planning

```
THREE HORIZONS OF CAPACITY PLANNING:

Horizon 1: Reactive (days to weeks)
  - Trigger: traffic spike detected, approaching resource limits
  - Action: auto-scaling rules, manual scale-up, load shedding
  - Tools: auto-scalers, capacity alerts, runbooks
  - Goal: don't run out of capacity in the next 7 days

Horizon 2: Tactical (weeks to months)
  - Trigger: seasonal traffic patterns, new feature launches, marketing campaigns
  - Action: pre-provision capacity, optimize hot paths, cache warming
  - Tools: traffic forecasting models, load testing results, historical data
  - Goal: have capacity ready for planned events 1-3 months out

Horizon 3: Strategic (months to quarters)
  - Trigger: organic growth trends, new markets, service decomposition
  - Action: infrastructure budget planning, datacenter/cloud region expansion, architecture changes
  - Tools: growth models, capacity review meetings, budget planning cycles
  - Goal: infrastructure can support business plan 6-12 months out

HEADROOM TARGETS:
  - Critical services: 50% headroom (can handle 2x current load)
  - Standard services: 30% headroom (can handle 1.3x current load)
  - Best-effort services: 20% headroom (can handle 1.2x current load)
  - Headroom = (provisioned_capacity - peak_usage) / peak_usage × 100%
  - Review headroom monthly; if below target, trigger capacity increase

LOAD SHEDDING:
  - Definition: intentionally dropping requests when at capacity to protect core functionality
  - Priority tiers:
    - Tier 1 (never shed): authentication, checkout, payment
    - Tier 2 (shed last): core product features, search
    - Shed first: recommendations, analytics, non-critical batch jobs
  - Implementation:
    - Request prioritization headers (X-Priority: high/low)
    - Rate limiting per client/tier
    - Queue depth monitoring → shed when queue > threshold
    - Graceful degradation: return cached/stale data instead of failing
  - Test load shedding: regularly run game days to verify shedding works as expected

CAPACITY PLANNING CHECKLIST:
  [ ] Auto-scaling configured for all stateless services
  [ ] Headroom targets defined per service tier
  [ ] Traffic forecasting model exists (seasonal + organic growth)
  [ ] Load shedding priority tiers defined and tested
  [ ] Quarterly capacity review with eng + finance
  [ ] Game days run to test capacity limits (at least 2x/year)
```

---

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

## Pitfalls

1. **Don't skip PRD** — even one-pager prevents scope creep
2. **Don't skip design doc for complex features** — technical decisions need documentation
3. **Don't use ASK for trivial changes** — Ship/Show/Ask reduces bottlenecks
4. **Don't skip feature flags** — decouple deploy from release
5. **Don't skip retrospective** — continuous improvement requires reflection
6. **Don't skip monitoring setup** — deploy without observability is blind
7. **Don't skip rollback plan** — always have a way back
8. **Don't skip stakeholder communication** — keep everyone informed
