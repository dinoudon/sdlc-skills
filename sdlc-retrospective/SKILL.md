---
name: sdlc-retrospective
description: "Engineering retrospectives: weekly/daily/sprint-end reviews. Blameless post-mortems. Three Ways from Phoenix Project. Westrum culture types. Google post-mortem template."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, retrospective, post-mortem, phoenix-project, three-ways, blameless, culture, google]
    related_skills: [sdlc-observability, sdlc-deployment, sdlc-cicd-pipeline]
---

# Retrospectives & Post-Mortems

Engineering retrospectives (weekly/daily/sprint), blameless post-mortems, Three Ways from Phoenix Project, Westrum culture types, Google post-mortem template.

## When to Use

Trigger when user:
- Runs a weekly/daily/sprint retrospective
- Writes a post-mortem after an incident
- Wants to improve team processes
- Analyzes what went well/poorly
- Tracks action items from retros

## Retrospective Formats

### Weekly Retro (15 min)
```
🚀 Shipped: [list completed items]
🚧 Blocked: [list blockers]
🔥 Wins: [list positives]
😤 Frustrations: [list pain points]
📋 Action Items: [list concrete next steps]
📊 Metrics: [velocity, lead time, incidents]
```

### Sprint Review (1 hour)
```
## Sprint Goal
[Goal statement]

## Completed
[Done items with demo links]

## Not Completed
[Carried items with reasons]

## What Went Well
[Team positives]

## What Needs Improvement
[Process issues]

## Action Items
- [ ] @person — [action] by [date]
```

### Start/Stop/Continue
```
Start: [New practices to adopt]
Stop: [Practices to abandon]
Continue: [Practices to maintain]
```

### 4Ls
```
Liked: [What we enjoyed]
Learned: [What we discovered]
Lacked: [What was missing]
Longed For: [What we wished for]
```

### Sailboat
```
Wind (propellers): [What pushed us forward]
Anchor (drag): [What held us back]
Rocks (risks): [Potential dangers ahead]
Island (goal): [Where we want to be]
```

## Blameless Post-Mortems

### Template (Google SRE)
```markdown
# [Service] [Impact] — [Date]

## Summary
- Severity: SEV1/SEV2/SEV3
- Duration: X hours Y minutes
- Impact: N users affected, $X revenue impact

## Timeline (UTC)
- HH:MM — [Event description]
- HH:MM — [Detection method]
- HH:MM — [Mitigation action]
- HH:MM — [Resolution]

## Root Cause
[Technical explanation of what caused the incident]

## Detection
[How was it detected? Alert vs customer report?]

## Resolution
[What fixed it?]

## Action Items
- [ ] @person — [Preventive measure] by [date]
- [ ] @person — [Detection improvement] by [date]
- [ ] @person — [Process improvement] by [date]

## Lessons Learned
- [Blameless observations]
- [Systemic issues identified]
```

### Blameless Culture Rules
1. **No individual blame** — focus on systems
2. **Assume positive intent** — everyone did their best
3. **Ask "what" not "who"** — what allowed this?
4. **Celebrate learning** — post-mortems are wins
5. **Follow through** — action items get done

## Data Gathering

### Incident Data Points
- **Time to detect** (TTD) — alert vs customer report
- **Time to mitigate** (TTM) — how fast to stop bleeding
- **Time to resolve** (TTR) — how fast to fix root cause
- **Impact scope** — users, revenue, data loss
- **Contributing factors** — missing tests, missing monitoring, missing runbook

### Retrospective Data
- **Velocity trend** — last 5 sprints
- **Cycle time** — PR to production
- **Bug escape rate** — bugs found in production
- **Incident frequency** — SEV1/2/3 per sprint
- **On-call load** — pages per shift

## The Three Ways (from Phoenix Project — Kim)

### First Way: Flow/Systems Thinking
- Make work visible (Kanban board)
- Limit WIP at every stage
- Manage flow (left-to-right)
- Small batches = fast feedback
- Find and elevate the constraint (Theory of Constraints)

### Second Way: Feedback Loops
- Create feedback at every stage
- Telemetry everywhere
- Fail fast, recover faster
- Post-mortems → systemic improvements
- Automated tests as fast feedback

### Third Way: Continual Learning
- Foster experimentation
- Allocate time for improvement (20%)
- Blameless culture
- Repetition creates mastery
- Improvements come from the work, not outside it

## Westrum Culture Types

### Pathological (Fear-Based)
- Low cooperation, lots of fear
- Information is a personal power tool
- Messengers are shot
- Responsibilities are shirked

### Bureaucratic (Rule-Based)
- Modest cooperation
- Information flows through rigid channels
- Messengers are neglected
- New ideas are met with ridicule

### Generative (Performance-Based)
- High cooperation, shared vision
- Information is actively sought
- Messengers are trained
- Risks are shared, failure leads to inquiry

**Goal: Move toward generative culture.**

### Indicators
| Aspect | Pathological | Bureaucratic | Generative |
|--------|-------------|--------------|------------|
| Messengers | Shot | Neglected | Trouted |
| Responsibility | Shirked | Narrow | Bridged |
| Cross-functional | Messy | Ordered | Bridge |
| Failure | Punished | Causes justice | Leads to inquiry |
| Novelty | Crushed | Suffocated | Implemented |

## Blameless Post-Mortem Culture (from Google SRE + Netflix)

### Google SRE
- Every SEV1/2 gets a post-mortem
- Template-driven, time-boxed (1 week)
- Follow-up actions tracked to completion
- Published internally for learning

### Netflix
- "Lead with context, not control"
- Engineers own their incidents
- Post-mortems are public (within company)
- Focus on prevention, not punishment

### Action Item Quality
- **Good**: "@team — Add alert for X metric by Feb 15"
- **Bad**: "Be more careful next time"
- **Good**: "@team — Add circuit breaker for Y service by Mar 1"
- **Bad**: "Improve monitoring"

### Action Item Tracking
```yaml
# action-items.yaml
- id: AI-001
  incident: INC-2025-042
  description: "Add alert for payment gateway latency > 2s"
  owner: backend-team
  due: 2025-02-15
  status: open
```

## Pitfalls

1. **Don't assign blame** — it destroys psychological safety
2. **Don't skip retros** — regular cadence beats ad-hoc
3. **Don't let action items rot** — track to completion
4. **Don't use "Start/Stop/Continue" forever** — rotate formats
5. **Don't skip post-mortems for SEV3** — learn from small incidents too
6. **Don't write post-mortems weeks later** — memory fades, write within 48h
7. **Don't ignore culture** — Westrum type predicts team performance
