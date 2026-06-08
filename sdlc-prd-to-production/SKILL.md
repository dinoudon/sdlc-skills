---
name: sdlc-prd-to-production
description: "End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective. Includes Ship/Show/Ask branching, design doc templates, PRD patterns (YC, Amazon Working Backwards), ephemeral environments, DORA 2024 insights, and Score spec."
version: 3.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, prd, design-doc, rfc, ship-show-ask, workflow, end-to-end, product-development, yc, amazon-working-backwards, ephemeral-envs, score-spec, dora]
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

## Pitfalls

1. **Don't skip PRD** — even one-pager prevents scope creep
2. **Don't skip design doc for complex features** — technical decisions need documentation
3. **Don't use ASK for trivial changes** — Ship/Show/Ask reduces bottlenecks
4. **Don't skip feature flags** — decouple deploy from release
5. **Don't skip retrospective** — continuous improvement requires reflection
6. **Don't skip monitoring setup** — deploy without observability is blind
7. **Don't skip rollback plan** — always have a way back
8. **Don't skip stakeholder communication** — keep everyone informed
