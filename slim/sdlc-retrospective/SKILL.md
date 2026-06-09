---
name: sdlc-retrospective
description: "Retrospective formats, blameless postmortems, Kaizen/PDCA, Toyota Kata, DORA metrics, SPACE framework, Team Topologies, flow metrics, value stream mapping, psychological safety, Westrum culture, Lean 7 wastes, technical debt management, DevEx, cognitive load, action item tracking."
version: 4.9.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc-slim, sdlc, retrospective, kaizen, continuous-improvement, postmortem, agile, dora-metrics, team-topologies, value-stream-mapping, psychological-safety, space-framework, toyota-kata, flow-metrics, cognitive-load, westrum, lean, technical-debt, devex]
    related_skills: [sdlc-prd-to-production, sdlc-requirements-engineering]
---

# Retrospectives & Continuous Improvement

## When to Use

Trigger when user:
- Runs sprint retrospective
- Plans continuous improvement
- Conducts blameless postmortem
- Measures team health or improvement

## Step 1: Retrospective Formats

### Start / Stop / Continue
| Start | Stop | Continue |
|-------|------|----------|
| Things to begin doing | Things not adding value | Things working well |

**Good for:** new teams, first retros, quick sessions.

### 4Ls (Liked, Learned, Lacked, Longed For)
| Liked | Learned | Lacked | Longed For |
|-------|---------|--------|------------|
| What went well | New insights | Missing resources | Things wished existed |

**Good for:** cross-functional teams.

### Mad / Sad / Glad
| Mad | Sad | Glad |
|-----|-----|------|
| Frustrations, blockers | Disappointments | Wins, celebrations |

**Good for:** surfacing hidden frustrations.

### Sailboat Retrospective
```
         ☀️ Sun (happiness)
          |
    ⛵ Sailboat
   /  |  \
  Wind    Rocks (risks)
 (help)   |
    \    /
     Anchor (blockers)
      |
      🏝️ Island (goal)
```
- **Wind:** propellers helping move forward
- **Anchor:** things slowing team down
- **Rocks:** risks, upcoming dangers

**Good for:** creative teams, visual thinkers.

## Step 2: Running a Retrospective

### Best Practices
1. **Timebox:** 60-90 min for 2-week sprint
2. **Facilitator rotates** (not always SM)
3. **Anonymous input** via sticky notes or digital tools
4. **Limit action items** to 1-3 max. Assign owners.
5. **Follow up previous retro actions** first
6. **Prime Directive:** "Regardless of what we discover, we understand and truly believe that everyone did the best job they could, given what they knew at the time, their skills and abilities, the resources available, and the situation at hand." — Norm Kerth

### Retro Agenda
```
1. Set the stage (5 min)      — Prime Directive, check-in
2. Gather data (15 min)       — What happened? Timeline, metrics.
3. Generate insights (15 min) — Why? Patterns, root causes.
4. Decide what to do (15 min) — 1-3 action items with owners
5. Close (5 min)              — Appreciation round, rate the retro
```

### Digital Tools
- **EasyRetro** — https://easyretro.io/
- **Miro** — https://miro.com/
- **Parabol** — https://www.parabol.co/
- **FunRetro** — https://funretro.io/

## Step 3: Kaizen & Toyota Kata

### PDCA Cycle (Plan-Do-Check-Act)
```
PLAN → Identify problem, analyze root cause, propose countermeasure
  ↓
DO   → Implement on small scale (pilot)
  ↓
CHECK → Measure results against hypothesis
  ↓
ACT  → Standardize if successful, or iterate
```

### Improvement Kata (4 Steps)
```
1. UNDERSTAND THE DIRECTION    — Long-term vision/challenge
2. GRASP THE CURRENT CONDITION — Facts, data, process observation
3. ESTABLISH NEXT TARGET       — Specific, measurable, just beyond current ability
4. EXPERIMENT TOWARD TARGET    — PDCA cycles, encounter obstacles, learn
   → Loop back to Step 3 with new knowledge
```

### Coaching Kata (5 Questions)
| # | Question | Purpose |
|---|----------|---------|
| 1 | What is the target condition? | Clarity on next goal |
| 2 | What is the actual condition now? | Fact-based awareness |
| 3 | What obstacles are you working on now? | Focus on one at a time |
| 4 | What is your next experiment? | Scientific thinking |
| 5 | When can we see what we learned? | Commitment to PDCA |

**Key rule:** Coach does NOT give answers. Coach asks questions.

### Kata vs. Standard Retro
| Aspect | Standard Retro | Kata-Infused Retro |
|--------|---------------|-------------------|
| Frequency | End of sprint | Daily coaching + sprint retro |
| Focus | What happened | What did we learn from experiments |
| Data | Sprint-level | Daily observable conditions |
| Improvement | Ad-hoc actions | Systematic target conditions |
| Outcome | Action items | Scientific thinking habit |

### Kata Board
```
┌────────────────────┬────────────────────┬────────────────────┐
│   TARGET CONDITION │    CURRENT STATE   │    OBSTACLES       │
│ Lead time < 3 days│ Lead time = 5 days │ Slow env provision │
│                    │                    │ Manual QA gates    │
├────────────────────┴────────────────────┴────────────────────┤
│ EXPERIMENT LOG                                               │
│ Experiment      │ Prediction    │ Result      │ Learning     │
│ Auto staging    │ Lead time -1d │ -0.5d only  │ Env=bottleneck│
│ PR pair review  │ Lead time -1d │ [pending]   │ [pending]    │
└──────────────────────────────────────────────────────────────┘
```

### Sprint Integration
| When | Activity |
|------|----------|
| Daily standup | Coaching questions #2-3 (2 min/person) |
| Mid-sprint | Check experiment results |
| Sprint retro | Review target condition, update obstacles, plan experiments |
| Quarterly | Review direction, set new target conditions |

## Step 4: Blameless Postmortems

### Template
```markdown
# Postmortem: [Incident Title]
## Summary
- Date: YYYY-MM-DD | Duration: X hours | Impact: [description] | Severity: P1/P2/P3

## Timeline
| Time | Event |
|------|-------|
| HH:MM | [Trigger/Detection/Response/Resolution] |

## Root Cause — [5 Whys or fishbone]
## What Went Well — [Thing that helped]
## What Went Wrong — [Thing that slowed resolution]
## Where We Got Lucky — [Near-misses]

## Action Items
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Preventive action] | [Name] | [Date] | Open |
```

### Blameless Culture Rules
- Focus on systems, not individuals
- Ask "what allowed this to happen?" not "who caused this?"
- Share learnings widely, track actions to completion

## Step 5: Team Health Check (Spotify Model)

| Indicator | Awesome (😊) | Crappy (😢) |
|-----------|-------------|-------------|
| Easy to release | No drama | Releasing is painful |
| Tech quality | Clean code, good tests | Tech debt everywhere |
| Speed | Fast, no blockers | Slow, waiting |
| Mission | Clear purpose | Confused, no direction |
| Learning | Growing skills | Stagnant |
| Pawns or players | Empowered to decide | Told what to do |
| Health of codebase | Easy to change | Scary to touch |

**Usage:** Vote anonymously → Discuss → Pick 1-2 areas → Track in next retro.

## Step 6: DORA Metrics & Transformation

### 4+1 Metrics
| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| **Deployment Frequency** | Multiple/day | Weekly–Daily | Monthly–Weekly | Monthly+ |
| **Lead Time for Changes** | < 1 hour | 1 day–1 week | 1 week–1 month | 1 month+ |
| **Change Failure Rate** | < 5% | 5–10% | 10–15% | 15%+ |
| **MTTR** | < 1 hour | < 1 day | 1 day–1 week | 1 week+ |
| **Reliability** (+1) | Meets SLOs | Near SLOs | Misses occasionally | Misses often |

### DORA-to-Action Mapping
| Metric Problem | Likely Root Area | Retro Focus |
|---------------|-----------------|-------------|
| Low deploy frequency | Batch size too large, manual gates | Automation, trunk-based dev |
| Long lead time | Slow reviews, env provisioning | Code review process, CI/CD |
| High change failure rate | Insufficient testing | Test coverage, feature flags |
| Slow MTTR | Poor observability | Monitoring, alerting, runbooks |

### DORA 24 Capabilities (Key Ones)
**Technical:** Version control, deployment automation, CI, trunk-based dev, test automation, shift-left security, CD, database change mgmt, loosely coupled architecture, monitoring/observability.

**Management:** Lightweight change approval, release management, lean product management.

**Cultural:** Westrum culture, job satisfaction, learning culture, transformational leadership, psychological safety.

### Transformation Path
```
Phase 1: FOUNDATION (Low → Medium)
  Version control, basic CI, automated unit tests, deployment automation,
  monitoring basics, blameless postmortems, peer review, blame→learning culture

Phase 2: ACCELERATION (Medium → High)
  Trunk-based dev, full CI/CD, test automation, IaC, feature flags,
  observability, loosely coupled architecture, security shift-left,
  psychological safety, learning culture

Phase 3: OPTIMIZATION (High → Elite)
  On-demand deploy, sub-hour lead time, CFR < 15%, sub-hour recovery,
  empowered teams, CI/CD telemetry, proactive failure detection,
  transformational leadership, continuous improvement routine
```

### Technical + Culture Co-Evolution
| Technical Practice | Cultural Pair | Why |
|-------------------|--------------|-----|
| Trunk-based dev | Psychological safety | Safe to merge small changes frequently |
| Feature flags | Empowered teams | Team decides when to enable |
| CI/CD | Blameless postmortems | Fast recovery requires learning |
| Monitoring | Learning culture | Data drives improvement |
| Loosely coupled | Team autonomy | Independent deploy = independent decisions |

**Key finding:** Neither technical nor cultural practices alone drive high performance. Must co-evolve.

### Capability-to-Metric Mapping
| If Weak... | Focus On |
|------------|---------|
| Low Deployment Frequency | Trunk-based dev, deployment automation, CI/CD, small batches |
| Long Lead Time | CI/CD, test automation, database change mgmt, small batches |
| High Change Failure Rate | Test automation, shift-left security, trunk-based dev |
| Slow MTTR | Proactive monitoring, observability, loosely coupled architecture |

### Retro Integration
| Retro Phase | DORA Activity |
|-------------|--------------|
| Set the Stage | State current DORA tier and target |
| Gather Data | 4 key metrics + capability checklist |
| Generate Insights | Which capability gap limits tier advancement? |
| Decide What to Do | One capability improvement (check co-evolution pair) |
| Close | Reassess target tier, celebrate progress |

## Step 7: Incident Deep-Dive

### Timeline Construction
```
T+0:00  [TRIGGER]     What changed?
T+0:XX  [ONSET]       First impact begins
T+0:XX  [DETECTION]   Who/what detected?
T+0:XX  [DIAGNOSIS]   Investigation starts
T+0:XX  [MITIGATE]    First mitigation (rollback, feature flag, scale up)
T+0:XX  [ROOT CAUSE]  Root cause identified
T+0:XX  [RESOLVE]     Service restored
```

### Key Time Intervals
| Interval | Definition | Target | Elite |
|----------|-----------|--------|-------|
| Time to Detect (TTD) | Onset → Detection | < 5 min | < 1 min |
| Time to Diagnose | Detection → Root cause | < 30 min | < 10 min |
| Time to Mitigate | Detection → First mitigation | < 15 min | < 5 min |
| Time to Resolve | Detection → Full resolution | < 1 hr | < 15 min |

### 5 Whys Example
```
Why did the service go down?    → DB connection pool exhausted
Why was pool exhausted?         → New feature made N+1 queries
Why did N+1 queries get merged? → PR review didn't catch it
Why didn't review catch it?     → No integration test for that endpoint
Why no integration test?        → Team lacks testing convention for DB queries

Root cause: Missing testing convention (process gap)
```

### Swiss Cheese Model
```
┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐
│  Code   │  │ Review  │  │  Test   │  │ Canary  │  │ Monitor │
│ Quality │  │ Process │  │  Suite  │  │ Deploy  │  │  Alert  │
│    ○────│──│─────────│──│────○────│──│─────────│──│────○────│──→ INCIDENT
└─────────┘  └─────────┘  └─────────┘  └─────────┘  └─────────┘
```

| Layer | Holes (Vulnerabilities) |
|-------|------------------------|
| Code quality | Linting gaps, no type safety, missing validation |
| Code review | Rubber-stamp reviews, large PRs |
| Automated testing | Missing coverage, flaky tests |
| Deployment controls | No canary, no feature flags |
| Monitoring & alerting | Missing alerts, alert fatigue |
| Architecture | Tightly coupled, single points of failure |

## Step 8: SPACE Framework

| Dimension | What It Measures | Example Metrics |
|-----------|-----------------|-----------------|
| **Satisfaction** | Well-being, fulfillment | eNPS, burnout indicators, tool satisfaction |
| **Performance** | Quality of work | Code review thoroughness, defect escape rate |
| **Activity** | Volume of outputs | Commits, PRs merged, deploys |
| **Communication** | Collaboration effectiveness | PR review response time, knowledge sharing |
| **Efficiency** | Minimal interruptions | Flow efficiency, context-switch frequency |

### SPACE + DORA Combined Insights
| Insight | SPACE Signal | DORA Signal | Retro Action |
|---------|-------------|-------------|--------------|
| "Busy but slow" | High Activity, Low Efficiency | Low deploy freq, long lead time | Reduce WIP, automate |
| "Fast but fragile" | High Activity, Low Performance | High change failure rate | Better testing, smaller batches |
| "Careful but demoralized" | High Performance, Low Satisfaction | Low CFR, slow MTTR | Celebrate wins, reduce toil |

### Anti-Pattern: Single-Metric Fixation
> Measuring only Activity rewards volume over quality. Measuring only Performance rewards risk-aversion. SPACE requires balance across dimensions.

## Step 9: Psychological Safety

Source: Amy Edmondson, "The Fearless Organization" (2018)

### Edmondson's 7-Item Survey (Rate 1-7)
| # | Statement |
|---|-----------|
| 1 | Mistakes on this team are held against you. (R) |
| 2 | Members can bring up problems and tough issues. |
| 3 | People reject others for being different. (R) |
| 4 | It is safe to take a risk on this team. |
| 5 | It is difficult to ask for help. (R) |
| 6 | No one would deliberately undermine my efforts. |
| 7 | My unique skills and talents are valued. |

(R) = Reverse-scored.

### Scoring
```
6.0-7.0 → High safety (healthy retro environment)
4.5-5.9 → Moderate (some topics avoided)
3.0-4.4 → Low (address before deep retros)
1.0-2.9 → Very low (retros will be performative)
```

### Improving Low Scores
| Score | Intervention |
|-------|-------------|
| < 3.0 | Leader vulnerability modeling. 1-on-1s, not group retro. |
| 3.0–4.4 | Anonymous-only retros. External facilitator. |
| 4.5–5.9 | Standard retros with anonymous option. Celebrate risk-taking. |
| 6.0+ | Full open retros. Team facilitates own sessions. |

## Step 10: Westrum Culture Model

| Characteristic | Pathological | Bureaucratic | Generative |
|---------------|-------------|-------------|-----------|
| Power | Based on fear | Based on rules | Based on respect |
| Information flow | Weaponized, hoarded | Guarded, siloed | Shared, sought |
| Messenger treatment | Punished | Tolerated | Protected |
| Failure response | Scapegoating | Blame assignment | Learning inquiry |
| Collaboration | Discouraged | Requires approval | Encouraged |
| Innovation | Crushed | Creates discomfort | Welcomed |

### Assessment (Rate 1-5, 20 items)
```
80-100 → GENERATIVE — optimize for improvement
60-79  → BUREAUCRATIC — focus on information flow
< 60   → PATHOLOGICAL — fix culture FIRST
```

### Culture in Retrospectives
| Culture Type | Retro Behavior | Intervention |
|-------------|---------------|-------------|
| Pathological | People afraid to speak, blame dominant | Leader vulnerability, anonymous-only, external facilitator |
| Bureaucratic | People share but nothing changes | Small team-owned actions, reduce approval chains |
| Generative | Open, honest, productive | Maintain and deepen, share learnings across org |

## Step 11: Team Topologies

### Four Team Types
| Type | Purpose | Retro Focus |
|------|---------|-------------|
| **Stream-aligned** | Single value stream | Flow efficiency, fast feedback, cognitive load |
| **Enabling** | Help teams acquire capabilities | Knowledge transfer effectiveness |
| **Complicated-subsystem** | Deep expertise subsystem | API stability, documentation |
| **Platform** | Internal services, reduce cognitive load | Self-service quality, DX scores |

### Three Interaction Modes
| Mode | When | Retro Question |
|------|------|---------------|
| **Collaboration** | Discovery, novel problems | "Learning fast enough to split back?" |
| **X-as-a-Service** | Known interface, stable domain | "Meeting consumers' needs?" |
| **Facilitating** | Knowledge transfer | "Has team gained the capability?" |

### Anti-Patterns
| Anti-Pattern | Fix |
|-------------|-----|
| Spaghetti teams (everyone depends on everyone) | Map value streams, restructure to stream-aligned |
| Permanent collaboration (6+ months) | Timebox, transition to XaaS or merge |
| One team, all domains (5+ domains) | Split by domain boundary |

## Step 12: Cognitive Load

### Three Types (Sweller)
| Type | Definition | Can Reduce? | Example |
|------|-----------|-------------|---------|
| **Intrinsic** | Core domain complexity | No | Understanding financial trading rules |
| **Extraneous** | Unnecessary env complexity | Yes | Poor docs, unclear APIs, manual processes |
| **Germane** | Learning worth investing in | Increase | Business domain understanding |

### Assessment Survey (Rate 1-7, 10 items)
```
Intrinsic Load  = avg(items 1R, 8)
Extraneous Load = avg(items 2R, 3R, 4R, 9, 10R)
Germane Load    = avg(items 5, 6, 7)

Total Load < 3.0  → Manageable
Total Load 3.0–4.5 → Moderate — watch for overload
Total Load > 4.5  → Overloaded — reduce scope or add help
Germane < 3.0     → Under-investing in learning
```

### Reduction Patterns
| Pattern | Applied By |
|---------|------------|
| Self-service platform | Platform team |
| Clear team APIs | Stream-aligned + platform teams |
| Domain-driven team boundaries | Architecture + org design |
| WIP limits | Team itself |

## Step 13: Flow Metrics

### Core Metrics
```
Lead Time  = clock time from request to delivery (includes all wait)
Cycle Time = clock time from work started to work done
Lead Time  = Wait Time + Cycle Time

Flow Efficiency = (Active Work Time / Lead Time) × 100
  Industry avg: 5-15% | Good: 25-40% | Excellent: 40%+

WIP Limit (starting): Team size × 1.5 for Kanban, adjust based on data
```

### Little's Law
```
L = λ × W  →  Cycle Time = WIP / Throughput

Reducing WIP directly reduces cycle time. Usually the faster lever.
```

### Dashboard Targets
| Metric | Target | Elite |
|--------|--------|-------|
| Lead time P50 | < 5 days | < 2 days |
| Cycle time P50 | < 3 days | < 1 day |
| Flow efficiency | > 25% | > 40% |
| Throughput | Stable/increasing | Increasing |

### Flow Anti-Patterns
| Anti-Pattern | Fix |
|-------------|-----|
| No WIP limits | Set WIP = team size × 1.5, adjust down |
| Large batch sizes | Break down, enforce small batches |
| Handoff queues | Cross-functional teams, automate testing |
| Multi-tasking (4+ items/person) | Enforce per-person WIP = 2 max |

## Step 14: Value Stream Mapping

```
[Request] → [Analysis] → [Development] → [Review] → [Test] → [Deploy] → [Value]
  Wait: 2d    VA: 1d      VA: 3d        Wait: 2d   VA: 1d   VA: 0.5d

Total Lead Time: ~10 days | Value-Add: ~5.5 days | Flow Efficiency: ~34%
```

### 7 Wastes of Software Development (Lean)
| # | Waste | Software Example | Fix First? |
|---|-------|-----------------|------------|
| 1 | **Overproduction** | Unused features | Plan long-term |
| 2 | **Extra Processing** | Over-engineering, unnecessary approvals | Quick win |
| 3 | **Waiting** | PR review queues, blocked items | **Fix first** |
| 4 | **Motion** | Context switching, excessive meetings | Opportunistic |
| 5 | **Inventory** | High WIP, stale PRs | **Fix second** |
| 6 | **Transportation** | Multi-team handoffs | Plan long-term |
| 7 | **Defects** | Bugs, rework | **Fix third** |

## Step 15: DevEx (Developer Experience)

### Three Dimensions
| Dimension | Key Question | Signs of Problems |
|-----------|-------------|-------------------|
| **Cognitive Load** | "How much mental energy goes to TOOLING vs WORK?" | Long onboarding, devs avoid parts of codebase |
| **Flow State** | "Can I get into and stay in flow?" | Meetings scattered, unclear requirements, slow builds |
| **Feedback Loops** | "How quickly do I know if work is correct?" | Slow CI, long PR review turnaround |

### Key DevEx Metrics
| Metric | Target | Elite |
|--------|--------|-------|
| Time to first commit | < 1 week | < 1 day |
| CI build time | < 10 min | < 5 min |
| PR review turnaround | < 4 hours | < 1 hour |
| Uninterrupted work hours/day | > 4 hours | > 6 hours |
| Developer satisfaction | > 5.0/7 | > 6.0/7 |

### Improvement Patterns
| Pattern | Targets | How |
|---------|---------|-----|
| Golden paths | Cognitive Load | Opinionated ways to build common things |
| Meeting-free blocks | Flow State | 2+ hours/day no meetings |
| Fast CI | Feedback Loops | < 10 min build, parallelize tests |
| PR review SLA | Feedback Loops | < 4 hour first review response |

## Step 16: Technical Debt Management

### Fowler's Quadrant
```
                    Deliberate              Inadvertent
                ┌─────────────────────┬─────────────────────┐
   Prudent      │ "Ship now, refactor │ "Now we know how    │
                │  next sprint"       │  we should have     │
                │  → Track, schedule  │  done it"           │
                │                     │  → Refactor, share  │
                ├─────────────────────┼─────────────────────┤
   Reckless     │ "No time for tests" │ "What's layered     │
                │  → Stop. Escalate.  │  design?"           │
                │                     │  → Training, mentor │
                └─────────────────────┴─────────────────────┘
```

### Quantifying Debt
```
TDR = (cost to fix debt) / (cost to rewrite from scratch)
  < 5% healthy | 5-10% attention needed | > 10% critical

Cost of Delay = (debt impact per sprint) × (sprints until fixed)
  Example: 2 days/sprint × $25k/sprint × 6 sprints = $150k delay
  Fix costs $40k now → ROI = 275%. Fix now.
```

### Strangler Fig Pattern
```
Phase 1: WRAP    — Add facade/API around legacy code (zero risk)
Phase 2: REDIRECT — Route traffic to new implementation (low risk)
Phase 3: REPLACE  — Remove legacy code (debt eliminated)
Phase 4: CLEAN UP — Remove facade, update docs
```

### Retro Integration
| Retro Phase | Debt Activity |
|-------------|--------------|
| Set the Stage | Current TDR, top 3 debt items |
| Gather Data | Cost-of-delay for top items, new debt this sprint |
| Generate Insights | Categorize (Fowler quadrant), root causes |
| Decide What to Do | Plan one repayment (Strangler Fig if large) |
| Close | Update debt register, celebrate reduction |

## Step 17: Action Item Tracking

### Kanban Board
```
┌──────────┬──────────────┬──────────┬────────────┐
│  BACKLOG │  IN PROGRESS │ BLOCKED  │    DONE    │
│ Action 4 │  Action 2    │ Action 5 │  Action 1  │
│ Action 6 │  (Owner: JD) │ (needs   │  (Owner:   │
│          │              │  infra)  │   AS)      │
└──────────┴──────────────┴──────────┴────────────┘
```

**Rules:** Visible to team. Review start of every retro. WIP limit: max 3 in-progress. Stuck >2 sprints → escalate. "Done" requires evidence.

### Escalation Protocol
```
Sprint N:   Action created → Backlog
Sprint N+1: Owner started → In Progress
Sprint N+2: Still in progress → flag in retro
Sprint N+3: Still open → escalate to team lead
Sprint N+4: Still open → escalate to management, re-evaluate
```

### OKR Alignment
| OKR | Key Result | Retro Action Example |
|-----|-----------|---------------------|
| Improve delivery speed | Lead time < 2 days | Automate staging deployment |
| Reduce incidents | MTTR < 1 hour | Create runbook for top 5 failures |
| Team satisfaction | Health check avg > 4.0 | Reduce WIP limit from 5 to 3 |

## Step 18: Remote Retrospective Patterns

### Tools
| Tool | Best For |
|------|----------|
| Miro | Complex visual retros |
| EasyRetro | Simple column-based |
| Parabol | Sprint-integrated, async mode |
| Geekbot | Async Slack-native |

### Facilitation Patterns
- **Silent brainstorming first** — 3-5 min, everyone writes simultaneously
- **Dot voting with limits** — 3-5 votes per person
- **Breakout rooms** — pairs/triads for 10-min deep dives
- **Async retro** — 48hr async input → 30min sync for actions
- **Camera-on encouraged, not mandated** — use reaction emojis

### Remote Anti-Patterns
- **Multitasking:** Use polls, direct questions
- **Time zone exclusion:** Rotate meeting times
- **No warm-up:** 2-min check-in question first
- **Screen sharing only:** Everyone should edit board simultaneously

## Pitfalls

1. **Don't skip the Prime Directive** — sets blameless tone
2. **Don't allow more than 3 action items** — focus beats breadth
3. **Don't assign actions to "the team"** — assign to named person
4. **Don't skip following up previous actions** — accountability matters
5. **Don't use same format every time** — rotate formats
6. **Don't skip anonymous input** — people self-censor in groups
7. **Don't run retros longer than 90 min** — fatigue kills quality
8. **Don't skip postmortems for incidents** — every P1/P2 gets one
9. **Don't blame individuals** — focus on systems and processes
10. **Don't skip data** — DORA metrics, incident counts, team health scores

## Retro Dashboard

| Metric | Target |
|--------|--------|
| Action completion rate | > 80% |
| Time to complete actions | < 2 sprints |
| Recurring themes | Decreasing |
| Team satisfaction with retro | > 4/5 |
| Psychological safety score | > 5.0 |
| DORA metrics trend | Improving |
