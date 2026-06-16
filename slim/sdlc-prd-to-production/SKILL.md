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

---
name: sdlc-prd-to-production
description: "End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective. Ship/Show/Ask, design docs, PRD patterns, ephemeral environments, DORA 2024, technical specs, GitOps, metrics-driven development, production readiness, launch strategies, incident management, blameless postmortems."
version: 4.8.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    ta
## When to Use
Trigger when user:
- Starts a new feature from scratch
- Writes PRD or design doc
- Plans implementation workflow
- Defines branching strategy for a feature
- Reviews end-to-end process
## Step 1: PRD (Product Requirements Document)
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
```
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
```
## Step 3: Ship / Show / Ask Branching
Source: https://martinfowler.com/articles/ship-show-ask.html
Three categories for branch merging decisions:
- Merge directly to main. No PR, no review needed.
- For: tiny changes, typo fixes, config tweaks, doc-only.
- Trust the author. Speed matters here.
- Merge to main immediately. Open PR afterward for visibility/async review.
- For: small, low-risk changes that still benefit from eyes.
- "Show, don't ask." Review happens after merge.
- Open PR, wait for review and approval before merging.
- For: architectural changes, new dependencies, security-sensitive code, public API changes.
- Standard PR workflow. No shortcuts.
- Not all changes need same review rigor
- Reduces PR bottlenecks on trivial changes
- Keeps focus on high-signal reviews (ASK items)
- Maintains trunk-based development velocity
- Works with feature flags for safe production deploys
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
Source: https://tag-app-delivery.cncf.io/whitepapers/platforms/
IDP = self-service abstraction layer between infra and devs. Reduces cognitive load.
**Key components:**
- Service catalog (what exists, who owns it)
- Self-service provisioning (databases, queues, envs)
- Golden path templates (best practices built-in)
- Observability portal (logs, metrics, traces linked to services)
- RBAC + policy-as-code (OPA/Kyverno gates)
Source: https://backstage.io/docs/
CNCF Incubating. Developer portal + scaffolder + catalog.
**Core features:**
- **Software Catalog:** register all services, APIs, data pipelines
- **Software Templates:** scaffolder creates new services from templates
- **TechDocs:** docs-as-code rendered in portal
- **Plugins:** 100+ community plugins (K8s, ArgoCD, Grafana, PagerDuty)
Source: https://github.com/backstage/backstage
PR-based preview environments:
```
1. PR opened → CI builds image, tags with PR number
2. ApplicationSet PR generator detects new PR
3. Renders Application pointing to PR-specific overlay
4. ArgoCD syncs new namespace (pr-123.myapp.staging.example.com)
5. PR merged → namespace deleted, Application removed
```
## DORA 2024 Insights
Source: https://dora.dev/research/2024/
- Documentation quality is a **strong predictor** of organizational performance
- Teams with high doc quality have 2x+ better software delivery performance
- Doc quality = accuracy, findability, freshness, completeness
- Docs-as-code (in repo, reviewed in PRs) correlates with better outcomes
- AI coding assistants (Copilot, etc.) show **moderate productivity gains**
- Gains strongest for: boilerplate, tests, docs, unfamiliar languages
- Gains weakest for: architecture decisions, complex debugging, novel logic
- **Risk:** over-reliance on AI without code review degrades quality
- **Recommendation:** AI augments review, not replaces it. Pair AI output with human judgment.
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
```
## PRD-to-Production Automation Patterns
Source: https://opengitops.dev/
```
PRD → Design Doc → Code → PR → CI → Image → Git Repo → ArgoCD/Flux → Cluster

All infrastructure and app config lives in Git. No kubectl apply in CI.
ArgoCD/Flux watches Git, reconciles cluster state to desired state.
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
## Metrics-Driven Development
Source: Google SRE book — define SLIs before writing code.
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
## PR checklist addition:
- [ ] SLI metrics emitted (latency, error rate, throughput)
- [ ] Structured logs with correlation IDs
- [ ] Trace spans for new operations
- [ ] Dashboard panels defined (or link to existing)
- [ ] Alert thresholds set based on SLO
```

**OpenTelemetry instrumentation example:**
```python
## Production Readiness Review (PRR)
Gate before production launch. Every item must be signed off by owner.
- [ ] Health check endpoints defined and tested (`/healthz`, `/readyz`)
- [ ] Graceful shutdown handles in-flight requests (drain period configured)
- [ ] Retry logic with exponential backoff + jitter on all external calls
- [ ] Circuit breakers on critical downstream dependencies
- [ ] Bulkhead isolation — failure in one path doesn't cascade
- [ ] Idempotency keys on all write operations
- [ ] Chaos testing results reviewed (pod kill, network partition, latency injection)
- [ ] Load test completed at 2x expected peak traffic
- [ ] Horizontal autoscaling configured (HPA/KEDA) with tested thresholds
- [ ] Database connection pooling sized for max concurrent connections
- [ ] No single-threaded bottlenecks in request path
- [ ] Caching strategy defined (L1/L2/invalidation policy)
- [ ] Queue depth monitoring and backpressure handling
- [ ] Resource requests and limits set in container spec
- [ ] RED metrics emitted for every endpoint (Rate, Errors, Duration)
- [ ] USE metrics emitted for every resource (Utilization, Saturation, Errors)
- [ ] Structured logging with correlation IDs (trace_id, request_id)
- [ ] Distributed tracing spans cover full request path
- [ ] Dashboard exists: service health, business metrics, infrastructure
- [ ] Alerts defined with runbook links for every SLO
## Launch Strategies
Deploy to production with limited exposure. Measure before scaling.
```
Day 1: Internal dogfooding (employees only)
Day 3: 1% of traffic (random sampling or cohort)
Day 5: 5% of traffic
Day 7: 25% of traffic
Day 10: 100% of traffic
```
## Post-Launch Monitoring
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
**Automated detection approaches:**
1. **Static thresholds** — alert if metric crosses fixed value (simple, noisy)
## Stakeholder Communication Templates
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
```
## Step 18: Incident Management
Source: PagerDuty Incident Response, Google SRE book.
Structured incident response = faster resolution + better learning.
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
```
## Gstack-Inspired CEO Review
Source: Gary Tan / Y Combinator gstack framework
Before shipping major features, conduct a CEO-level review covering:
1. **Problem-solution fit**: Does this solve a real problem? Evidence?
2. **User impact**: Who benefits? How many? How much?
3. **Strategic alignment**: Does this advance the company mission?
4. **Competitive moat**: Does this create defensible advantage?
1. **Architecture soundness**: Component boundaries clear? Tech debt manageable?
2. **Scalability**: Will this handle 10x current load?
3. **Security posture**: Threat model reviewed? OWASP checklist?
4. **Operational readiness**: Monitoring, alerts, runbooks in place?
1. **Unit economics**: Does this improve LTV:CAC ratio?
2. **Pricing impact**: Does this justify price increase or new tier?
3. **Market timing**: Is this the right time for this feature?
4. **Resource allocation**: Is this the best use of engineering time?
## Unicorn Launch Patterns
- Ship with 7 lines of code example
- Interactive API explorer live on day one
- SDKs in top 3 languages at launch
- Sandbox environment available immediately
- Transparent pricing published
- Browser-based (zero install)
- Real-time collaboration demo
- Share link = product demo
- Community templates ready
- 50+ community templates at launch
- Power user content on YouTube/Twitter
- Import from competitors (Evernote, Confluence)
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