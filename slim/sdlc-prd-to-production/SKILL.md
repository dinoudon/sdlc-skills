---
name: sdlc-prd-to-production
description: "End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monitoring → retrospective. Ship/Show/Ask, design docs, PRD patterns, ephemeral environments, DORA 2024, technical specs, GitOps, metrics-driven development, production readiness, launch strategies, incident management, blameless postmortems."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, prd, design-doc, rfc, ship-show-ask, workflow, end-to-end, product-development, dora, metrics-driven, production-readiness, incident-management, blameless-postmortems]
    related_skills: [sdlc-requirements-engineering, sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-deployment, sdlc-retrospective]
---## When to Use

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


## Pitfalls

1. **Don't skip PRD** — even one-pager prevents scope creep
2. **Don't skip design doc for complex features** — technical decisions need documentation
3. **Don't use ASK for trivial changes** — Ship/Show/Ask reduces bottlenecks
4. **Don't skip feature flags** — decouple deploy from release
5. **Don't skip retrospective** — continuous improvement requires reflection