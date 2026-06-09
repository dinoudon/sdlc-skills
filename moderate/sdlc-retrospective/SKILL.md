---
name: sdlc-retrospective
description: "Retrospective formats: Start/Stop/Continue, 4Ls, Mad/Sad/Glad, Sailboat, Kaizen PDCA cycle, Toyota Kata (Mike Rother), blameless postmortems, incident deep-dive (Swiss cheese model), continuous improvement patterns. DORA metrics integration, DORA capability assessment, SPACE framework productivity metrics, Team Topologies awareness, team cognitive load measurement, Value Stream Mapping, flow metrics (lead time, cycle time, flow efficiency, WIP limits), anti-patterns, remote retro patterns, psychological safety measurement, action item tracking, green software retrospective, FinOps retrospective, platform engineering retrospective, Toyota Kata practice, Lean Software Development (7 wastes), Theory of Constraints (5 focusing steps, thinking processes), DORA transformation patterns (24 capabilities, 4 tiers), Platform Engineering Maturity (CNCF maturity model, Gartner predictions), Developer Productivity Research (SPACE applied, Microsoft studies, DORA culture findings), Technical Debt Management (Fowler's quadrant, Strangler Fig, quantification), Inner Source Patterns (InnerSource Commons, trusted committer, 30-day warranty), Staff Engineer Role (Larson's 4 archetypes), Engineering Ladder Design (dual-track IC/management), 1:1 Meeting Patterns (Lara Hogan, SBI, Radical Candor), Technical Decision Making (ADR, RFC, consensus models), Team Topologies (4 team types, 3 interaction modes, cognitive load theory), Inverse Conway Maneuver (org-to-architecture alignment), Value Stream Mapping (flow efficiency, bottleneck elimination), Team API (code API, communication API, work-with-us API)."
version: 4.9.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc-moderate, sdlc, retrospective, kaizen, continuous-improvement, postmortem, agile, dora-metrics, team-topologies, value-stream-mapping, psychological-safety, space-framework, toyota-kata, flow-metrics, cognitive-load, incident-deepdive, dora-capabilities, green-software, finops, platform-engineering, sustainable-engineering, cloud-cost-optimization, developer-experience, lean-software-development, theory-of-constraints, dora-transformation, seven-wastes, throughput-accounting, platform-maturity, developer-productivity, technical-debt, inner-source, staff-engineer, engineering-ladder, one-on-ones, adr, rfc, technical-decisions, inverse-conway, team-api, stream-aligned, enabling-team]
    related_skills: [sdlc-prd-to-production, sdlc-requirements-engineering]
---

# Retrospectives & Continuous Improvement

Retrospective formats, Kaizen PDCA cycle, blameless postmortems, continuous improvement patterns.

## When to Use

Trigger when user:
- Runs sprint retrospective
- Plans continuous improvement
- Conducts blameless postmortem
- Measures team health or improvement

## Step 1: Retrospective Formats

### Start / Stop / Continue
Source: https://www.atlassian.com/team-playbook/plays/retrospective

Simplest format. Three columns on board.

| Start | Stop | Continue |
|-------|------|----------|
| Things team should begin doing | Things not adding value | Things working well |
| New practices to try | Wastes to eliminate | Keep doing these |

**Good for:** new teams, first retros, quick sessions.

### 4Ls (Liked, Learned, Lacked, Longed For)

| Liked | Learned | Lacked | Longed For |
|-------|---------|--------|------------|
| What went well | New insights | Missing resources/skills | Things wished existed |
| What enjoyed | Key takeaways | Support gaps | Ideal state |

**Good for:** cross-functional teams, product+engineering mix.

### Mad / Sad / Glad

| Mad | Sad | Glad |
|-----|-----|------|
| Frustrations, blockers | Disappointments, missed opportunities | Wins, celebrations |
| Waste that caused anger | Things that didn't happen | Things that worked |

**Good for:** surfacing hidden frustrations, psychological safety.

### Sailboat Retrospective

Visual metaphor. Draw sailboat on board:

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

- **Wind:** propellers, things helping move forward
- **Anchor:** things slowing team down
- **Rocks:** risks, upcoming dangers
- **Island:** goal, vision
- **Sun:** things making team happy

**Good for:** creative teams, visual thinkers, big-picture thinking.

Source: https://www.funretrospectives.com/sailboat/

## Step 2: Running a Retrospective

### Best Practices
Source: https://www.atlassian.com/team-playbook/plays/retrospective

1. **Timebox:** 60-90 min for 2-week sprint
2. **Facilitator rotates** (not always SM)
3. **Anonymous input** via sticky notes or digital tools
4. **Limit action items** to 1-3 max. Assign owners.
5. **Follow up previous retro actions** first
6. **Prime Directive:** "Regardless of what we discover, we understand and truly believe that everyone did the best job they could, given what they knew at the time, their skills and abilities, the resources available, and the situation at hand." — Norm Kerth

### Retro Agenda
```
1. Set the stage (5 min)
   - Prime Directive reading
   - Check-in question

2. Gather data (15 min)
   - What happened? Timeline, metrics, events.

3. Generate insights (15 min)
   - Why did it happen? Patterns, root causes.

4. Decide what to do (15 min)
   - 1-3 action items with owners

5. Close (5 min)
   - Appreciation round
   - Rate the retro itself
```

### Digital Tools
- **RetroTool** — https://retrotool.io/ (free, anonymous)
- **EasyRetro** — https://easyretro.io/ (popular, free tier)
- **Miro** — https://miro.com/ (general whiteboard)
- **FunRetro** — https://funretro.io/ (simple, clean)

## Step 3: Kaizen (Continuous Improvement)

Source: https://www.kaizen.com/what-is-kaizen.html

Japanese "change for better." Philosophy of ongoing incremental improvement.

### Core Principles
- Good processes bring good results
- Go see for yourself (genchi genbutsu)
- Speak with data, manage by facts
- Take action to contain and correct root causes
- Work as team
- Kaizen is everybody's business

### PDCA Cycle (Plan-Do-Check-Act)

```
    ┌───────┐
    │ PLAN  │ Identify problem, analyze root cause,
    │       │ propose countermeasure.
    └───┬───┘
        │
    ┌───▼───┐
    │  DO   │ Implement on small scale (pilot).
    │       │
    └───┬───┘
        │
    ┌───▼───┐
    │ CHECK │ Measure results against hypothesis.
    │       │
    └───┬───┘
        │
    ┌───▼───┐
    │  ACT  │ Standardize if successful, or iterate.
    │       │
    └───────┘
```

### Kata Pattern (Mike Rother)
Source: https://miketherother.com/ | Book: "Toyota Kata" (Mike Rother, 2009)

Toyota Kata = structured scientific-thinking routines that make continuous improvement a daily habit, not a quarterly event.

#### Two Kata

**Improvement Kata** — systematic method for moving from current state toward target condition.

```
┌─────────────────────────────────────────────────────────────┐
│  1. UNDERSTAND THE DIRECTION                               │
│     Long-term vision, challenge, or goal                   │
│     "Where are we trying to get to?"                       │
│                          │                                  │
│                          ▼                                  │
│  2. GRASP THE CURRENT CONDITION                            │
│     Map actual state: facts, data, process observation     │
│     "Where are we now? What is actually happening?"        │
│                          │                                  │
│                          ▼                                  │
│  3. ESTABLISH NEXT TARGET CONDITION                        │
│     Next achievable step (not end state)                   │
│     Specific, measurable, just beyond current ability      │
│     "Where do we want to be next?"                         │
│                          │                                  │
│                          ▼                                  │
│  4. EXPERIMENT TOWARD TARGET (PDCA)                        │
│     Run rapid experiments, encounter obstacles             │
│     "What obstacles are in the way? What did we learn?"    │
│     → Loop back to Step 3 with new knowledge               │
└─────────────────────────────────────────────────────────────┘
```

**Coaching Kata** — five questions leaders ask daily to develop scientific thinking in others.

#### The Five Coaching Questions

```
1. What is the target condition?
   (Specific, measurable next step)

2. What is the actual condition now?
   (Facts, data, observation — not opinion)

3. What obstacles are preventing you from reaching the target?
   (List them, prioritize, pick one to address)

4. What is your next step? (next experiment / PDCA cycle)
   (Concrete action, expected outcome, prediction)

5. When can we see what we've learned from taking that step?
   (Timebox: hours, days — not weeks)
```

#### Applying Kata to Software Retrospectives

| Kata Step | Retrospective Application |
|-----------|--------------------------|
| Understand direction | OKRs, team mission, DORA elite targets |
| Grasp current condition | Current DORA metrics, flow metrics, incident data, team health scores |
| Next target condition | One specific metric improvement: "Lead time from 5 days to 3 days" |
| Experiment toward target | Sprint-level experiments: "Add automated staging deploy" |
| Coaching questions | Manager asks 5 questions in 1-on-1s, not just in retro |

#### Kata vs. Standard Retro

| Aspect | Standard Retro | Kata-Infused Retro |
|--------|---------------|-------------------|
| Frequency | End of sprint | Daily coaching + sprint retro |
| Focus | What happened | What did we learn from experiments |
| Data | Sprint-level | Daily observable conditions |
| Improvement | Ad-hoc actions | Systematic target conditions |
| Coaching | Facilitator-driven | Leader-led with 5 questions |
| Outcome | Action items | Scientific thinking habit |

#### Kata Board (Visual Management)

```
┌────────────────────┬────────────────────┬────────────────────┐
│   TARGET CONDITION │    CURRENT STATE   │    OBSTACLES       │
│                    │                    │                    │
│ Lead time < 3 days│ Lead time = 5 days │ Slow env provision │
│                    │                    │ Manual QA gates    │
│                    │                    │ Long PR reviews    │
├────────────────────┴────────────────────┴────────────────────┤
│ EXPERIMENT LOG                                               │
│ ┌──────────────┬──────────────┬────────────┬───────────────┐ │
│ │  Experiment   │  Prediction  │  Result    │  What Learned │ │
│ ├──────────────┼──────────────┼────────────┼───────────────┤ │
│ │ Auto staging │ Lead time -1d│ Lead time  │ Env issue     │ │
│ │ deploy       │              │ -0.5d only │ = bottleneck  │ │
│ │              │              │            │               │ │
│ │ PR review    │ Lead time -1d│ [pending]  │ [pending]     │ │
│ │ pair syst.   │              │            │               │ │
│ └──────────────┴──────────────┴────────────┴───────────────┘ │
└──────────────────────────────────────────────────────────────┘
```

#### Integrating Kata into Sprint Rhythm

| When | Activity | Duration |
|------|----------|----------|
| Daily standup | Coaching question #2-3 (current condition, obstacles) | 2 min/person |
| Mid-sprint | Check experiment results, adjust | 15 min |
| Sprint retro | Review target condition progress, update obstacle list, plan next experiments | 30 min |
| Quarterly | Review direction (challenge), set new target conditions | 1-2 hours |

Source: https://www-personal.umich.edu/~mrother/Kata_Explained.html

### In Software Context
- Sprint retros = kaizen events
- Blameless postmortems after incidents
- DORA metrics as improvement indicators
- Value stream mapping for flow optimization

## Step 4: Blameless Postmortems

### Template
```markdown
# Postmortem: [Incident Title]

## Summary
- **Date:** YYYY-MM-DD
- **Duration:** X hours
- **Impact:** [description of user impact]
- **Severity:** P1/P2/P3

## Timeline
| Time | Event |
|------|-------|
| HH:MM | [What happened] |
| HH:MM | [Detection] |
| HH:MM | [Response] |
| HH:MM | [Resolution] |

## Root Cause
[5 Whys analysis or fishbone diagram]

## What Went Well
- [Thing that helped resolve faster]

## What Went Wrong
- [Thing that slowed resolution]

## Where We Got Lucky
- [Near-misses that could have been worse]

## Action Items
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| [Preventive action] | [Name] | [Date] | Open |
| [Detective action] | [Name] | [Date] | Open |
```

### Blameless Culture Rules
- Focus on systems, not individuals
- Assume everyone did their best given context
- Ask "what allowed this to happen?" not "who caused this?"
- Share learnings widely
- Track action items to completion

Source: https://sre.google/sre-book/postmortem-culture/

## Step 5: Team Health Check

### Spotify Squad Health Check
Source: https://labs.spotify.com/2014/09/16/squad-health-check-model/

| Indicator | Awesome (😊) | Crappy (😢) |
|-----------|-------------|-------------|
| Easy to release | Releasing is easy, no drama | Releasing is painful |
| Suitability | Right tools for the job | Wrong tools/frameworks |
| Tech quality | Clean code, good tests | Tech debt everywhere |
| Speed | Fast, no blockers | Slow, waiting for others |
| Mission | Clear purpose, aligned | Confused, no direction |
| Fun | Enjoy working here | Not fun |
| Learning | Growing skills | Stagnant |
| Support | Team helps each other | Everyone for themselves |
| Pawns or players | Empowered to decide | Told what to do |
| Health of codebase | Easy to change | Scary to touch |

**How to use:**
1. Each team member votes anonymously (😊/😢/meh)
2. Discuss results as team
3. Pick 1-2 areas to improve
4. Track improvement in next retro

## Step 13: SPACE Framework

Source: Forsgren et al., "The SPACE of Developer Productivity" (2021)
Paper: https://queue.acm.org/detail.cfm?id=3454124

SPACE = Satisfaction, Performance, Activity, Communication, Efficiency. Multi-dimensional productivity measurement that avoids single-metric traps. Use in retro "Gather Data" phase alongside DORA.

### The Five Dimensions

| Dimension | What It Measures | Example Metrics | Data Source |
|-----------|-----------------|-----------------|-------------|
| **Satisfaction** | Well-being, fulfillment, happiness | Developer satisfaction survey, eNPS, burnout indicators, tool satisfaction | Surveys, 1-on-1s |
| **Performance** | Quality and correctness of work | Code review thoroughness, test pass rate, defect escape rate, uptime/SLO adherence | CI/CD, monitoring |
| **Activity** | Volume of outputs and actions | Commits, PRs merged, deploys, tickets closed, builds triggered | Git, CI/CD, Jira |
| **Communication** | Collaboration effectiveness | PR review response time, knowledge sharing frequency, documentation updates, async sync ratio | Git, Slack, Confluence |
| **Efficiency** | Ability to complete work with minimal interruptions | Flow efficiency, interruption count, context-switch frequency, time in meetings vs. deep work | Calendar, flow tools |

### Anti-Pattern: Single-Metric Fixation

> Measuring only Activity (commits, PRs) rewards volume over quality.
> Measuring only Performance (zero defects) rewards risk-aversion.
> SPACE requires balance across dimensions. No single metric captures productivity.

### SPACE Survey Template (Quarterly)

Rate 1 (Strongly Disagree) to 7 (Strongly Agree):

| # | Statement | Dimension |
|---|-----------|-----------|
| 1 | I am satisfied with my ability to get work done efficiently | Satisfaction |
| 2 | I have the tools and resources I need | Satisfaction |
| 3 | I am able to do my best work | Performance |
| 4 | The code I produce is high quality | Performance |
| 5 | I make meaningful contributions regularly | Activity |
| 6 | My work output is valued by the team | Activity |
| 7 | I can easily get help when needed | Communication |
| 8 | Information flows effectively on my team | Communication |
| 9 | I have enough uninterrupted time for deep work | Efficiency |
| 10 | I can complete tasks without excessive context switching | Efficiency |

### Integrating SPACE into Retros

1. Administer SPACE survey quarterly (separate from psychological safety survey)
2. In retro "Gather Data," present SPACE radar chart alongside DORA metrics
3. Identify lowest-scoring dimension → root cause analysis in "Generate Insights"
4. Create action items targeting weakest dimension
5. Track dimension trends over time (radar chart shifts indicate improvement)

### SPACE Radar Chart Example

```
        Satisfaction (4.5)
             ★
            /|\
           / | \
  Efficiency/  |  \Performance
    (3.2) ★   |   ★ (5.1)
         / \  |  / \
        /   \ | /   \
       /     \|/     \
      ★-------+-------★
Communication  Activity
   (4.0)       (5.8)
```

Interpretation: Activity high (team busy), Efficiency low (lots of interruptions/waiting). Retro focus: reduce blockers, protect deep work time.

### Combining SPACE + DORA

| Insight | SPACE Signal | DORA Signal | Retro Action |
|---------|-------------|-------------|--------------|
| "Busy but slow" | High Activity, Low Efficiency | Low deploy frequency, long lead time | Reduce WIP, automate pipelines |
| "Fast but fragile" | High Activity, Low Performance | High change failure rate | Better testing, smaller batches |
| "Careful but demoralized" | High Performance, Low Satisfaction | Low change failure rate, slow MTTR | Celebrate wins, reduce toil |
| "Quiet quitting" | Low Activity, Low Satisfaction | Declining metrics across board | Address burnout, workload, purpose |

## Step 14: DORA Capability Assessment Checklist

Source: https://dora.dev/capabilities/

DORA's research identifies 30+ capabilities that predict elite performance. Use this checklist in retro to assess team maturity and identify capability gaps.

### How to Use

1. In retro "Gather Data" phase, rate each capability: **Not Started / Emerging / Growing / Mastered**
2. Focus discussion on capabilities rated "Emerging" that correlate with weakest DORA metric
3. Select 1-2 capabilities to advance one level as sprint improvement experiments (align with Kata target conditions)
4. Re-assess quarterly

### Software Delivery & Operational Performance Capabilities

#### Continuous Delivery

| Capability | Description | Level |
|-----------|-------------|-------|
| Version control | All artifacts in version control (code, config, infra-as-code) | |
| Trunk-based development | Short-lived branches, frequent merges to main | |
| CI/CD pipeline | Automated build, test, deploy pipeline | |
| Test automation | Comprehensive automated test suite (unit, integration, e2e) | |
| Trunk-based deploys | Deploy on merge, feature flags for incomplete work | |
| Deployment automation | One-click, fully automated deployments | |
| Shift-left security | Security integrated into pipeline (SAST, DAST, SCA) | |
| Database change management | Automated, version-controlled database migrations | |

#### Architecture

| Capability | Description | Level |
|-----------|-------------|-------|
| Loosely coupled architecture | Teams can deploy independently | |
| Architecture enables scaling | Can scale components independently | |
| Empowered team chooses tools | Teams select own tools (not mandated from above) | |
| Cloud infrastructure | Elastic, on-demand compute/storage | |

#### Product & Process

| Capability | Description | Level |
|-----------|-------------|-------|
| Working in small batches | Small PRs, incremental releases, MVP approach | |
| Limiting WIP | Explicit WIP limits on work items | |
| Customer feedback loops | Regular user feedback integrated into planning | |
| Team experimentation | Team can A/B test, prototype, experiment without approval | |
| Visibility of work | Work in progress visible to all (Kanban board) | |

#### Management & Culture

| Capability | Description | Level |
|-----------|-------------|-------|
| Lean management | WIP limits, batch size reduction, flow visualization | |
| Culture of psychological safety | See Step 11 measurement | |
| Cross-functional collaboration | Dev, QA, Ops, Product work together daily | |
| Generative culture (Westrum) | Information flows freely, messengers not punished | |
| Transformational leadership | Leaders set vision, provide tools, remove obstacles | |
| Investment in developer experience | Internal tooling, platform teams, documentation | |

#### Monitoring & Observability

| Capability | Description | Level |
|-----------|-------------|-------|
| Proactive monitoring | Systems monitored for anomalies, not just failures | |
| Observability | Distributed tracing, structured logging, metrics | |
| A/B testing capability | Can deploy variants and measure impact | |

### Capability Maturity Levels

```
Not Started (0)  →  No capability present, not planned
Emerging  (1)    →  Awareness, some ad-hoc practice
Growing   (2)    →  Defined process, team follows it consistently
Mastered  (3)    →  Optimizing, measuring, continuously improving
```

### Capability-to-Metric Mapping

| If DORA Metric Is Weak... | Focus On These Capabilities |
|--------------------------|----------------------------|
| Low Deployment Frequency | Trunk-based dev, deployment automation, CI/CD pipeline, small batches |
| Long Lead Time for Changes | CI/CD pipeline, test automation, database change management, small batches |
| High Change Failure Rate | Test automation, shift-left security, trunk-based dev, architecture |
| Slow MTTR | Proactive monitoring, observability, loosely coupled architecture, generative culture |

## Step 15: Incident Retrospective Deep-Dive

For P1/P2 incidents requiring deeper analysis than standard postmortem template (Step 4).

### Timeline Analysis Protocol

Reconstruct detailed incident timeline using multiple data sources.

#### Data Sources for Timeline

| Source | What It Provides | Tool Examples |
|--------|-----------------|---------------|
| Monitoring dashboards | Metric anomalies, timestamps | Datadog, Grafana, New Relic |
| Log aggregation | Error patterns, stack traces | ELK, Splunk, Loki |
| Chat logs | Human communication timeline | Slack, Teams (search by keyword + time) |
| Deployment records | Code/config changes | CI/CD logs, ArgoCD, Spinnaker |
| Incident channel | Command, decisions, coordination | Slack/Teams incident channel |
| PagerDuty/on-call records | Alert timeline, escalation | PagerDuty, OpsGenie |
| Customer reports | External impact detection | Zendesk, Statuspage |

#### Timeline Construction

```
T+0:00  [TRIGGER]     What changed? Deploy, config, traffic spike, dependency failure?
T+0:XX  [ONSET]       First customer/system impact begins
T+0:XX  [DETECTION]   Who/what detected? Monitoring alert? Customer report?
T+0:XX  [DIAGNOSIS]   Team begins investigating
T+0:XX  [COMMUNICATE] Internal: incident channel opened
T+0:XX  [ESCALATION]  Escalated to: who? Why?
T+0:XX  [MITIGATE]    First mitigation action: rollback, feature flag, scale up
T+0:XX  [COMMUNICATE] External: status page updated
T+0:XX  [ROOT CAUSE]  Root cause identified
T+0:XX  [RESOLVE]     Service restored, metrics normal
T+0:XX  [POST-REVIEW] Verify, monitoring confirms stability
```

#### Key Time Intervals to Measure

| Interval | Definition | Target | Elite |
|----------|-----------|--------|-------|
| Time to Detect (TTD) | Onset → Detection | < 5 min | < 1 min |
| Time to Diagnose (TTDx) | Detection → Root cause identified | < 30 min | < 10 min |
| Time to Mitigate (TTM) | Detection → First mitigation | < 15 min | < 5 min |
| Time to Resolve (TTR) | Detection → Full resolution | < 1 hr | < 15 min |
| Time to Communicate (TTC) | Onset → External communication | < 15 min | < 5 min |

### Contributing Factors Analysis

Incidents have multiple contributing factors, never single root cause. Use structured analysis to find all factors.

#### Fishbone Diagram (Ishikawa)

```
    People        Process       Technology       Environment
      │              │              │                │
      ├─ Training    ├─ Procedure   ├─ Monitoring    ├─ Traffic
      ├─ Fatigue     ├─ Review      ├─ Testing       ├─ Dependency
      ├─ Handoff     ├─ Deploy      ├─ Config        ├─ Seasonal
      ├─ Expertise   ├─ Rollback    ├─ Capacity      ├─ External
      │              │              │                │
      └──────────────┴──────────────┴────────────────┘
                         │
                    [INCIDENT]
```

#### 5 Whys (Iterative)

```
Why did the service go down?    →  Database connection pool exhausted
Why was pool exhausted?         →  New feature made N+1 queries
Why did N+1 queries get merged?  →  PR review didn't catch it
Why didn't review catch it?     →  No integration test for that endpoint
Why no integration test?        →  Team lacks testing convention for DB queries

Root cause: Missing testing convention (process gap)
Contributing: Code review checklist (didn't include DB query pattern)
```

### Swiss Cheese Model (James Reason)

Source: Reason, J. (1990). "Human Error." Cambridge University Press.

Multiple defense layers exist in any system. Each layer has holes (like Swiss cheese). Incident occurs when holes in all layers align.

```
┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐
│ Layer 1 │  │ Layer 2 │  │ Layer 3 │  │ Layer 4 │  │ Layer 5 │
│         │  │         │  │         │  │         │  │         │
│  Code   │  │  Review │  │  Test   │  │ Canary  │  │Monitor/ │
│ Quality │  │  Process │  │  Suite  │  │ Deploy  │  │ Alert   │
│         │  │    ○     │  │         │  │    ○    │  │         │
│    ○────│──│─────────│──│────○────│──│─────────│──│────○────│──→ INCIDENT
│         │  │         │  │         │  │         │  │         │
│         │  │         │  │    ○    │  │         │  │    ○    │
└─────────┘  └─────────┘  └─────────┘  └─────────┘  └─────────┘
  ○ = hole in defense layer
  ─→ = path of incident through aligned holes
```

#### Defense Layers in Software Systems

| Layer | Purpose | Holes (Vulnerabilities) |
|-------|---------|------------------------|
| **Code quality** | Prevent defects at source | Linting gaps, no type safety, missing input validation |
| **Code review** | Catch defects before merge | Rubber-stamp reviews, large PRs, fatigue |
| **Automated testing** | Catch regression/defects | Missing test coverage, flaky tests, slow suites |
| **Deployment controls** | Limit blast radius | No canary, no feature flags, full-rollout deploys |
| **Monitoring & alerting** | Detect issues quickly | Missing alerts, alert fatigue, no SLO alerts |
| **Incident response** | Rapid mitigation | No runbooks, unclear ownership, missing escalation |
| **Architecture** | Contain failures | Tightly coupled services, shared databases, single points of failure |

#### Applying Swiss Cheese to Incident Retro

For each incident, map which holes aligned:

```markdown
## Step 16: Team Cognitive Load Measurement

Source: Skelton & Pais, "Team Topologies" (2019) | https://teamtopologies.com/

Cognitive load = total mental effort required for a team to do its work. Excessive cognitive load → mistakes, slow delivery, burnout.

### Three Types of Cognitive Load (Sweller)

| Type | Definition | Can Reduce? | Example |
|------|-----------|-------------|---------|
| **Intrinsic** | Core domain complexity | No — inherent to problem | Understanding financial trading rules |
| **Extraneous** | Unnecessary complexity from environment | Yes — remove it | Poor documentation, unclear APIs, manual processes |
| **Germane** | Learning/domain understanding worth investing in | Increase it | Understanding business domain, system architecture |

### Cognitive Load Assessment Survey

Rate 1 (No load) to 7 (Extreme load):

| # | Statement | Load Type |
|---|-----------|----------|
| 1 | I understand all the domains I need to work in | Intrinsic |
| 2 | The systems I work on are well-documented | Extraneous |
| 3 | I can easily find information when I need it | Extraneous |
| 4 | Our tooling helps rather than hinders me | Extraneous |
| 5 | I understand why our systems are designed the way they are | Germane |
| 6 | I have time to learn new things relevant to my work | Germane |
| 7 | I am learning valuable domain knowledge | Germane |
| 8 | I work on too many different domains/services at once | Intrinsic |
| 9 | I spend significant time on tasks unrelated to my core mission | Extraneous |
| 10 | Our APIs and interfaces are easy to understand | Extraneous |

### Scoring

```
Intrinsic Load  = avg(items 1R, 8)  — reverse-score item 1
Extraneous Load = avg(items 2R, 3R, 4R, 9, 10R)  — reverse-score items 2,3,4,10
Germane Load    = avg(items 5, 6, 7)  — higher = better investment

Total Cognitive Load = Intrinsic + Extraneous (lower = better)
Germane Investment   = Germane (higher = better)

Interpretation:
  Total Load < 3.0  →  Manageable — team has capacity
  Total Load 3.0–4.5 → Moderate — watch for overload indicators
  Total Load > 4.5  →  Overloaded — reduce scope, split domain, or add help
  Germane < 3.0     →  Under-investing in learning — schedule learning time
```

### Applying Cognitive Load to Team Design

| Signal | Problem | Topologies Remedy |
|--------|---------|-------------------|
| High intrinsic load | Team owns too many domains | Split stream-aligned team by domain boundary |
| High extraneous load | Poor tooling/docs/process | Platform team provides self-service; enabling team helps |
| Low germane load | No learning time | Protect learning time; pair programming; mob programming |
| Team does everything | "Full-stack" overload | Identify 1-2 core domains, externalize rest via platform teams |

### Cognitive Load Reduction Patterns

| Pattern | What It Does | Applied By |
|---------|-------------|------------|
| Self-service platform | Eliminates need to understand infrastructure | Platform team |
| Clear team APIs | Reduces inter-team coordination cost | Stream-aligned + platform teams |
| Documentation-as-code | Makes docs findable, versioned | Any team |
| Domain-driven team boundaries | Aligns teams to bounded contexts | Architecture + org design |
| WIP limits | Reduces context-switching cognitive load | Team itself |
| On-call rotation limits | Prevents burnout from dual cognitive load | Team itself |

### Integration with Retros

1. Measure cognitive load quarterly (alongside SPACE survey and psychological safety)
2. In retro, present cognitive load scores alongside team health check
3. If total load > 4.5: immediate retro focus on reducing scope or improving tooling
4. If germane < 3.0: schedule dedicated learning/review time in next sprint
5. Track load trends — increasing load = warning sign even if metrics look OK

## Step 17: Flow Metrics

Source: Kanban, Lean Software Development, Accelerate (Forsgren et al.)

Flow metrics measure how work moves through the system. Use alongside DORA for complete delivery picture.

### Core Flow Metrics

#### Lead Time

```
Definition: Clock time from request created to value delivered to customer.
             Includes all wait time.

  [Customer Request] ──────────────────────────────→ [Value Delivered]
                    |←         Lead Time           →|

Measurement: Track from issue creation (or commit) to production deploy.
Sources: Jira (created → done), Git (first commit → merge to prod).
```

#### Cycle Time

```
Definition: Clock time from work started to work completed.
             Excludes backlog wait time.

                    [Work Started] ─────────────→ [Work Done]
                                  |← Cycle Time →|

Measurement: Track from "In Progress" to "Done" on Kanban board.
Sources: Jira (status change), Git (branch created → merge).
```

#### Lead Time vs. Cycle Time

```
Lead Time = Wait Time + Cycle Time

  [Request] ~~~~wait~~~~ [Start] ~~~~work~~~~ [Done] ~~~~wait~~~~ [Deploy]
            |←  Wait  →|       |← Cycle →|              |← Wait →|
            |←                    Lead Time                    →|

Typical software team:
  Lead time:  5-30 days
  Cycle time: 2-10 days
  Wait time:  3-20 days (often the biggest waste)
```

#### Flow Efficiency

```
Definition: Percentage of lead time spent on value-adding work.

Flow Efficiency = (Active Work Time / Lead Time) × 100

Active Work Time = time item is being actively worked on (not waiting in queue)

Typical values:
  Software industry average:  5-15%
  Good:                       25-40%
  Excellent:                  40%+
  Manufacturing benchmark:    25-40%

If flow efficiency < 15%, focus on reducing wait times and handoffs.
```

#### Work In Progress (WIP) Limits

```
Definition: Maximum number of items allowed in a workflow stage simultaneously.

Purpose: Prevent context-switching, surface bottlenecks, improve flow.

WIP Limit Formula (starting point):
  WIP limit per person = 1-2 items
  Team WIP limit = (Team size × 1.5) for Kanban
  Adjust based on flow efficiency data
```

### WIP Limit Patterns

| Pattern | Description | When to Use |
|---------|-------------|-------------|
| **Per-person WIP** | Each person max 2 items | Small teams (< 6), new to WIP limits |
| **Per-column WIP** | Max items per Kanban column | Kanban boards, larger teams |
| **Per-class WIP** | Different limits per work type (bug vs. feature) | Mixed workload teams |
| **Expedite lane** | 1 item allowed to bypass WIP limit | Emergency/production fixes only |

#### WIP Limit Effects

```
Before WIP limits:
  Person A: Item 1, Item 2, Item 3, Item 4  → context switch every 30 min
  Cycle time: 8 days
  Quality: moderate

After WIP limits (max 2):
  Person A: Item 1 (focus), Item 2 (blocked/waiting)  → deep work
  Cycle time: 3 days
  Quality: high
  Blocked items surface immediately → team swarms to unblock
```

### Flow Metrics Dashboard

| Metric | How to Measure | Target | Elite |
|--------|---------------|--------|-------|
| Lead time P50 | Median request-to-delivery | < 5 days | < 2 days |
| Lead time P85 | 85th percentile (captures outliers) | < 10 days | < 5 days |
| Cycle time P50 | Median start-to-done | < 3 days | < 1 day |
| Cycle time P85 | 85th percentile | < 5 days | < 2 days |
| Flow efficiency | Active time / lead time | > 25% | > 40% |
| WIP (current) | Items in progress now | ≤ WIP limit | ≤ WIP limit |
| Throughput | Items completed per week | Stable or increasing | Increasing |
| Aging WIP | Items in progress > cycle time P85 | 0 | 0 |

### Little's Law

```
L = λ × W

Where:
  L = Average WIP (items in system)
  λ = Throughput (items completed per unit time)
  W = Average cycle time

Rearranged: Cycle Time = WIP / Throughput

Implication: Reducing WIP directly reduces cycle time (if throughput stays constant).
To reduce cycle time from 5 days to 3 days:
  - Reduce WIP from 10 to 6 items (if throughput = 2 items/day)
  - Or increase throughput from 2 to 3.3 items/day (harder)

Reducing WIP is usually the faster lever.
```

### Integrating Flow Metrics into Retros

1. Display flow metrics dashboard in retro "Gather Data" phase (alongside DORA)
2. Calculate flow efficiency — if < 15%, investigate where time is lost
3. Review aging WIP — items stuck in progress are highest priority
4. If cycle time increasing: tighten WIP limits by 1-2 items
5. Map flow metrics to value stream map (Step 8) for visual root cause analysis

### Flow Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|-------------|---------|-----|
| No WIP limits | Items pile up in "In Progress" | Set WIP limit = team size × 1.5, adjust down |
| Large batch sizes | PRs > 200 lines, features > 2 weeks | Break down, enforce small batch culture |
| Handoff queues | Items wait days in "Ready for QA" | Cross-functional team, automate testing |
| Multi-tasking | Every person has 4+ items | Enforce per-person WIP = 2 max |
| No aging tracking | Old items invisible | Add age indicator to Kanban board |
| Ignoring wait time | Only measuring "work time" | Track lead time and cycle time separately |

## Step 18: Team Topologies Deep-Dive

Source: Skelton & Pais, "Team Topologies" (2019) | https://teamtopologies.com/

Team Topologies provides a model for organizing engineering teams to optimize for fast flow of value. Use in retros to assess whether team structure is enabling or hindering delivery.

### Conway's Law

> "Any organization that designs a system will produce a design whose structure is a copy of the organization's communication structure." — Melvin Conway (1967)

**Implication for retros:** If delivery is slow, don't just examine processes — examine team boundaries. Misaligned team structures create architectural friction no process fix can overcome.

### Four Team Types

#### 1. Stream-Aligned Team
```
Purpose: Aligned to a single value stream (product, feature set, user journey).
         Primary team type — most teams should be stream-aligned.

Characteristics:
  - End-to-end ownership of a flow of work
  - Cross-functional (dev, QA, UX, product)
  - Empowered to deploy independently
  - Receives fast feedback from production

Cognitive Load: Manage one or two domains max. If overloaded, split.
Retro Focus: Flow efficiency, deployment frequency, customer feedback loops.
```

#### 2. Platform Team
```
Purpose: Provide internal services that reduce cognitive load for stream-aligned teams.
         Makes infrastructure, tooling, and common capabilities self-service.

Characteristics:
  - Treats internal teams as customers
  - Provides APIs, CLI tools, dashboards, golden paths
  - Measures adoption and developer satisfaction
  - Reduces extraneous cognitive load for stream-aligned teams

Retro Focus: Self-service adoption rate, API stability, onboarding time, DX scores.
```

#### 3. Enabling Team
```
Purpose: Help stream-aligned teams acquire new capabilities.
         Temporary collaboration — goal is to transfer knowledge, then step back.

Characteristics:
  - Deep expertise in specific domain (e.g., testing, security, observability)
  - Works WITH teams, not FOR them
  - Success = team no longer needs them
  - Rotates through teams on a cadence

Retro Focus: Knowledge transfer effectiveness, capability adoption rate, time-to-independence.
```

#### 4. Complicated-Subsystem Team
```
Purpose: Own a subsystem requiring deep specialist expertise.
         Exists when component complexity is too high for stream-aligned team.

Characteristics:
  - Owns specific technical subsystem (e.g., video codec, ML model, financial engine)
  - Deep domain expertise required
  - Provides clear API/interface to consuming teams
  - Should be rare — most organizations need 0-1

Retro Focus: API documentation completeness, integration test coverage, interface stability.
```

### Three Interaction Modes

| Mode | Description | When to Use | Duration | Retro Question |
|------|-------------|-------------|----------|---------------|
| **Collaboration** | Two teams work closely together on shared problem | Discovery phase, novel problems, new tech | Timeboxed (weeks, not months) | "Are we learning fast enough to transition to XaaS or Facilitating?" |
| **X-as-a-Service** | One team provides well-defined API/tool/service to another | Known interface, clear boundary, stable domain | Ongoing | "Is the service meeting consumers' needs? Is the interface clear?" |
| **Facilitating** | Enabling team helps another team learn/adopt capability | Knowledge transfer, new practices, capability building | Temporary (weeks to months) | "Has the receiving team gained the capability? Can we step back?" |

### Team Topology Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|-------------|---------|-----|
| **Spaghetti teams** | Every team depends on every other team | Map value streams, restructure to stream-aligned |
| **Platform without customers** | Platform team builds what nobody uses | Treat internal teams as customers, measure adoption |
| **Permanent collaboration** | Two teams "collaborating" for 6+ months | Timebox collaboration, transition to XaaS or merge |
| **Enabling team becomes gatekeeper** | Enabling team blocks stream-aligned team | Enabling team coaches, doesn't do the work |
| **Too many complicated-subsystem teams** | Specialist silos everywhere | Invest in self-service docs, simplify interfaces |
| **One team, all domains** | Single team owns 5+ business domains | Split by domain boundary, reduce intrinsic cognitive load |

### Applying Team Topologies in Retros

1. **Map current team types** — classify each team as stream-aligned, platform, enabling, or complicated-subsystem
2. **Map interaction modes** — how do teams actually interact? Collaboration? XaaS? Facilitating?
3. **Identify mismatches** — "We're collaborating but should be XaaS" or "We're stream-aligned but acting like a platform team"
4. **Assess cognitive load** — use Step 16 survey to validate team boundaries
5. **Create topology change actions** — restructure teams, change interaction modes, split/merge teams
6. **Re-evaluate quarterly** — topology should evolve as product and org evolve

Source: https://teamtopologies.com/key-concepts

## Step 22: Developer Experience (DevEx)

Source: DevEx: A New Paradigm for Developer Productivity (GitHub, 2023)
Paper: https://queue.acm.org/detail.cfm?id=3595878

Developer Experience (DevEx) measures how developers perceive their work environment, tools, and processes. Poor DevEx = slow delivery, high attrition, low quality.

### Three Dimensions of DevEx

#### 1. Cognitive Load
```
Definition: Mental effort required to do work, including understanding systems,
            navigating tooling, and managing context switches.

Key question: "How much of my mental energy goes to the WORK vs. the TOOLING?"

Components:
  - Intrinsic load: core domain complexity (can't eliminate)
  - Extraneous load: unnecessary complexity from tools, docs, processes (eliminate this)
  - Germane load: learning that builds expertise (invest in this)

Signs of high cognitive load:
  - Developers can't explain how their system works
  - Frequent "I don't know who owns this" moments
  - Long onboarding time (> 2 weeks to first meaningful commit)
  - Developers avoid certain parts of the codebase
  - Context switching between many unrelated tasks
```

#### 2. Flow State
```
Definition: Deep, focused, uninterrupted work where developers do their best work.
            Csikszentmihalyi's "flow" applied to software development.

Key question: "Can I get into and stay in flow state?"

Requirements for flow:
  - Clear goals (know what to do next)
  - Immediate feedback (see results of actions quickly)
  - Challenge-skill balance (not too easy, not too hard)
  - Uninterrupted time (minimum 2-hour blocks)

Flow blockers:
  - Meetings scattered throughout the day
  - Slack/Teams notifications every few minutes
  - Unclear requirements ("figure it out")
  - Slow build/test cycles (> 10 min)
  - Waiting for approvals, reviews, environments
  - Context switching between projects
```

#### 3. Feedback Loops
```
Definition: Speed and quality of information developers receive about their work.

Key question: "How quickly do I know if my work is correct?"

Feedback loops in software:
  - Code → Test result: seconds (unit tests) to minutes (integration)
  - Code → Review: hours to days (PR review turnaround)
  - Code → Production: minutes (CI/CD) to weeks (release cycles)
  - Code → User impact: days (analytics) to weeks (customer feedback)
  - Idea → Validation: weeks (A/B test) to months (product metrics)

Fast feedback = faster learning = better decisions = better code.
Slow feedback = stale context = higher error rate = more rework.
```

### Measurement Approach

| Dimension | Method | Frequency | Example Instruments |
|-----------|--------|-----------|-------------------|
| **Cognitive Load** | Survey + system metrics | Quarterly | Step 16 cognitive load survey, onboarding time tracking |
| **Flow State** | Survey + calendar analysis | Monthly | "How often can you get 2+ hours uninterrupted?" Calendar: meeting-free blocks |
| **Feedback Loops** | System metrics | Weekly (automated) | CI/CD build times, PR review turnaround, deploy frequency |

### DevEx Survey Template

Rate 1 (Strongly Disagree) to 7 (Strongly Agree):

| # | Statement | Dimension |
|---|-----------|-----------|
| 1 | I can easily understand the systems I work on | Cognitive Load |
| 2 | Documentation helps me do my job effectively | Cognitive Load |
| 3 | Our tooling is intuitive and well-integrated | Cognitive Load |
| 4 | I can onboard onto a new codebase quickly | Cognitive Load |
| 5 | I have enough uninterrupted time for deep work | Flow State |
| 6 | I can focus on one task at a time | Flow State |
| 7 | I know what I need to work on and why | Flow State |
| 8 | Our build and test cycles are fast enough | Feedback Loops |
| 9 | I get timely feedback on my code (reviews, tests) | Feedback Loops |
| 10 | I know quickly if something I deployed is working | Feedback Loops |
| 11 | I can deploy my changes to production easily | Feedback Loops |
| 12 | Overall, I am satisfied with my developer experience | Overall |

### Key DevEx Metrics

| Metric | What It Measures | Target | Elite |
|--------|-----------------|--------|-------|
| **Time to first commit** | Onboarding effectiveness | < 1 week | < 1 day |
| **CI build time** | Feedback loop speed | < 10 min | < 5 min |
| **PR review turnaround** | Collaboration speed | < 4 hours | < 1 hour |
| **Deploy-to-production time** | Delivery speed | < 30 min | < 15 min |
| **Uninterrupted work hours/day** | Flow state availability | > 4 hours | > 6 hours |
| **Meeting hours/week** | Context switch load | < 10 hours | < 6 hours |
| **Doc freshness** | Cognitive load reduction | > 80% updated in 90 days | > 90% |
| **Developer satisfaction score** | Overall DevEx | > 5.0/7 | > 6.0/7 |

### DevEx Improvement Patterns

| Pattern | Targets | How |
|---------|---------|-----|
| **Golden paths** | Cognitive Load | Provide opinionated, supported ways to build common things |
| **Internal developer portal** | Cognitive Load | Single place for docs, APIs, ownership, runbooks |
| **Meeting-free blocks** | Flow State | Designate 2+ hours/day with no meetings allowed |
| **Fast CI** | Feedback Loops | Invest in build speed (< 10 min), parallelize tests |
| **PR review SLA** | Feedback Loops | Commit to < 4 hour first review response |
| **Automated environments** | Cognitive Load + Feedback | One-click dev environment setup, ephemeral preview envs |
| **Platform team investment** | All | Dedicated team to improve DevEx as product |

### Integrating DevEx into Retros

1. Measure DevEx quarterly (survey + automated metrics)
2. In retro "Gather Data," present DevEx scores alongside SPACE and DORA
3. Identify weakest dimension (Cognitive Load, Flow State, or Feedback Loops)
4. Root cause analysis: what specific tools/processes/practices are causing friction?
5. Create 1-2 action items targeting weakest dimension
6. Track DevEx metrics trend over time
7. Correlate DevEx improvements with DORA metric improvements

Source: https://queue.acm.org/detail.cfm?id=3595878

## Step 23: Westrum Culture Model

Source: Ron Westrum, "A Typology of Organisational Cultures" (2004)
Paper: https://qualitysafety.bmj.com/content/13/suppl_2/ii22

Ron Westrum's model classifies organizational culture into three types based on how information flows. DORA research shows generative culture is the strongest predictor of software delivery performance.

### Three Culture Types

```
PATHOLOGICAL (Power-Oriented)
──────────────────────────────
  - Information is used as political weapon
  - Messengers are punished ("shoot the messenger")
  - Responsibilities are compartmentalized, siloed
  - Failure leads to scapegoating
  - Bridging between teams is discouraged or punished
  - Novelty is crushed ("not invented here")

  Information flow: Top-down only, hoarded, distorted
  Typical org: Traditional command-and-control hierarchies
  DORA impact: Low performance across all metrics

BUREAUCRATIC (Rule-Oriented)
──────────────────────────────
  - Information is guarded by departments
  - Messengers are tolerated but ignored ("that's not my department")
  - Responsibilities are departmental, not shared
  - Failure leads to investigation and blame assignment
  - Bridging between teams requires formal approval
  - Novelty creates discomfort ("that's not how we do things")

  Information flow: Departmental, formal channels only
  Typical org: Large enterprises, government, regulated industries
  DORA impact: Medium performance, slow but stable

GENERATIVE (Performance-Oriented)
──────────────────────────────────
  - Information is actively sought and shared
  - Messengers are trained and protected
  - Responsibilities are shared across teams
  - Failure leads to inquiry and learning
  - Bridging between teams is encouraged and rewarded
  - Novelty is welcomed ("let's try it")

  Information flow: Free, fast, multi-directional
  Typical org: High-performing tech companies, elite engineering orgs
  DORA impact: High/Elite performance across all metrics
```

### Culture Characteristics Comparison

| Characteristic | Pathological | Bureaucratic | Generative |
|---------------|-------------|-------------|-----------|
| **Power** | Based on fear | Based on rules | Based on respect |
| **Information flow** | Weaponized, hoarded | Guarded, siloed | Shared, sought |
| **Messenger treatment** | Punished | Tolerated | Protected |
| **Failure response** | Scapegoating | Blame assignment | Learning inquiry |
| **Responsibility** | Compartmentalized | Departmental | Shared |
| **Collaboration** | Discouraged | Requires approval | Encouraged |
| **Innovation** | Crushed | Creates discomfort | Welcomed |
| **Trust** | Low | Contractual | High |
| **Blame** | Personal | Process | Systemic |

### Assessment Questions

Rate each statement 1 (Strongly Disagree) to 5 (Strongly Agree):

#### Information Flow
1. Information flows freely across team boundaries
2. Bad news travels fast (up and across)
3. I have access to information I need without asking permission
4. Failures are openly discussed, not hidden
5. Knowledge sharing is rewarded, not hoarded

#### Messenger Treatment
6. People who report problems are thanked, not blamed
7. Raising concerns about decisions is safe
8. I can challenge leadership decisions without career risk
9. "I don't know" is an acceptable answer
10. Admitting mistakes is seen as strength, not weakness

#### Failure Response
11. Postmortems focus on systems, not individuals
12. We learn from failures and share those learnings
13. Retrying after failure is encouraged
14. "Best failure" awards or celebrations exist
15. Failure leads to process improvement, not punishment

#### Responsibility & Collaboration
16. Teams collaborate across boundaries without formal approval
17. Helping another team is valued, not seen as distraction
18. I feel responsible for outcomes beyond my immediate role
19. Cross-functional work is the norm, not the exception
20. Innovation and experimentation are encouraged

#### Scoring
```
Total Score: Sum of all 20 items (max 100)

80-100  →  GENERATIVE — healthy culture, optimize for improvement
60-79   →  BUREAUCRATIC — functional but slow, focus on information flow
< 60    →  PATHOLOGICAL — culture is blocking improvement, address immediately
```

### Westrum in Retrospectives

| Culture Type | Retro Behavior | Intervention |
|-------------|---------------|-------------|
| **Pathological** | People afraid to speak. Only surface issues. Blame dominant. | Fix culture FIRST before deep retros. Leader vulnerability. Anonymous-only. External facilitator. |
| **Bureaucratic** | People share but nothing changes. Actions lost in bureaucracy. | Focus on small, team-owned actions. Reduce approval chains. Empower team to fix their own process. |
| **Generative** | Open, honest, productive retros. People volunteer failures. | Maintain and deepen. Experiment with formats. Share learnings across org. |

### Moving Toward Generative Culture

| From | To | Actions |
|------|----|---------|
| Punishing messengers | Protecting messengers | Leader publicly thanks people who raise problems |
| Hiding failures | Sharing failures | Blameless postmortems (Step 21), failure celebrations |
| Siloed information | Open information | Internal wikis, public dashboards, open Slack channels |
| Approval-heavy | Trust-based | Reduce approval gates, empower team decisions |
| Individual blame | Systemic thinking | "What in our process allowed this?" not "Who caused this?" |
| Novelty rejected | Novelty welcomed | Innovation time, hack days, experiment budgets |

### Correlating Westrum with Other Models

| Model | Westrum Connection |
|-------|-------------------|
| **Psychological Safety (Step 20)** | Generative culture requires high psychological safety |
| **DORA Metrics (Step 6)** | Generative culture → Elite DORA performance |
| **Team Topologies (Step 18)** | Stream-aligned teams need generative culture to work |
| **SPACE (Step 19)** | Generative culture → higher Satisfaction dimension |
| **DevEx (Step 22)** | Generative culture → better feedback loops, less extraneous load |
| **Blameless Postmortems (Step 21)** | Only work in bureaucratic/generative cultures |

Source: https://qualitysafety.bmj.com/content/13/suppl_2/ii22
DORA reference: https://dora.dev/capabilities/generative-organizational-culture/

## Step 27: Toyota Kata Practice

Source: https://www.amazon.com/Toyota-Kata-Managing-Improvement-Adaptiveness/dp/0071635238 | https://www-personal.umich.edu/~mrother/Homepage.html

Toyota Kata is behavioral practice, not a tool or methodology. Two kata (routines) form the system: Improvement Kata and Coaching Kata. Goal: develop scientific thinking as daily habit, not periodic events.

### Improvement Kata (4 Steps)

```
┌─────────────────────────────────────────────────────────────────┐
│  IMPROVEMENT KATA                                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Step 1: Understand the Direction                               │
│  ├── What is the long-term vision / challenge?                  │
│  ├── What business or organizational goal drives this?          │
│  └── How does this team's work contribute?                      │
│                                                                 │
│  Step 2: Grasp the Current Condition                           │
│  ├── What is happening now? (data, facts, not opinions)         │
│  ├── What are the actual process conditions?                    │
│  ├── Where is the process in relation to the target?            │
│  └── Map current value stream, identify obstacles               │
│                                                                 │
│  Step 3: Establish the Next Target Condition                    │
│  ├── What should the process look like next?                    │
│  ├── Define specific measurable target (not the final goal)     │
│  ├── What is the next step toward the direction?                │
│  └── Target must be beyond current capability (stretch)         │
│                                                                 │
│  Step 4: Experiment Toward the Target                           │
│  ├── What obstacles prevent reaching target condition?           │
│  ├── Which obstacle are we addressing now?                      │
│  ├── What is our hypothesis?                                    │
│  ├── What do we expect?                                         │
│  ├── How will we test it? (short PDCA cycle)                    │
│  └── What did we learn?                                         │
│                                                                 │
│  → Then repeat from Step 4 (next obstacle, next experiment)     │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Coaching Kata (5 Questions)

Manager asks these 5 questions in regular coaching cycles (daily or every few days):

| # | Question | Purpose |
|---|----------|---------|
| 1 | What is the target condition? | Clarity on next goal |
| 2 | What is the actual condition now? | Fact-based awareness |
| 3 | What obstacles are you working on now? | Focus on one at a time |
| 4 | What is your next experiment? | Scientific thinking |
| 5 | When can we see what we learned? | Commitment to PDCA cycle |

**Key rule:** Coach does NOT give answers. Coach asks questions. Learner experiments and learns.

### PDCA Experiments in Kata Context

Each experiment is a single PDCA cycle:

```
Plan:   Predict outcome of one specific change
  ↓
Do:     Run experiment (short, safe-to-fail)
  ↓
Check:  Compare actual result to prediction
  ↓
Act:    Decide next step based on what learned
  ↓
  (back to Plan for next experiment)
```

**Experiment design rules:**
- One change at a time (scientific method)
- Predict before you act (forces learning)
- Short cycles (hours to 1-2 days, not weeks)
- Document prediction vs actual (surfacing mental models)
- Failure is information, not blame

### Behavioral Routine vs Tool

**Critical distinction:** Toyota Kata is NOT a tool you apply. It is a behavioral routine you practice daily.

| Aspect | Tool Mindset (wrong) | Kata Mindset (correct) |
|--------|----------------------|------------------------|
| When to use | When problems arise | Every day, routine practice |
| Who does it | Improvement specialists | Everyone, especially leaders |
| Goal | Solve this problem | Develop scientific thinking |
| How long | One-off event | Continuous routine |
| Failure | Avoid it | Learn from it |
| Coaching | Give advice | Ask questions |
| Measurement | Outcome only | Process adherence + outcome |

**Implementing Kata in retrospectives:**
1. Use retro to set next target condition (Step 3)
2. Between retros, run daily coaching cycles (5 questions)
3. Each retro: report on experiments run, what learned
4. Track: how many PDCA cycles this sprint? (aim for 5-10+)
5. Retrospective reviews the practice, not just the results

### Kata Practice Board

```
┌─────────────────────────────────────────────────────────────────┐
│  KATA BOARD                                                     │
├────────────────┬──────────────┬──────────────┬─────────────────┤
│  Direction     │ Current      │ Target       │ Experiments     │
│  (Challenge)   │ Condition    │ Condition    │                 │
├────────────────┼──────────────┼──────────────┼─────────────────┤
│ Long-term      │ Where we     │ Where we     │ Obstacle 1      │
│ challenge      │ are now      │ want to be   │ → Hypothesis    │
│                │ (data/facts) │ next         │ → Test          │
│                │              │              │ → Result        │
│                │              │              │ → Learning      │
│                │              │              │                 │
│                │              │              │ Obstacle 2      │
│                │              │              │ → ...           │
└────────────────┴──────────────┴──────────────┴─────────────────┘
```

### Kata Maturity Levels

| Level | Description | Indicators |
|-------|-------------|------------|
| **1: Awareness** | Team knows Kata exists | Has read the book, tried once |
| **2: Practice** | Regular coaching cycles | 5 questions used weekly, experiments documented |
| **3: Routine** | Kata is how we work | Daily coaching, >5 experiments/sprint, prediction tracking |
| **4: Culture** | Scientific thinking is default | Kata language natural, self-coaching emerging |

Source: Toyota Kata by Mike Rother, 2009 | https://www-personal.umich.edu/~mrother/Homepage.html

## Step 28: Lean Software Development (7 Wastes)

Source: https://www.amazon.com/Lean-Software-Development-Agile-Toolkit/dp/0321150783 | Mary & Tom Poppendieck

Adapted from Toyota Production System's 7 wastes (muda) to software context. Use in retrospectives to identify systemic waste and improve flow.

### 7 Wastes of Software Development

| # | Waste | Definition | Software Examples | Detection Signal |
|---|-------|-----------|-------------------|------------------|
| 1 | **Overproduction** | Building features no one uses | Gold plating, unused features, speculative features | Low feature adoption, low usage analytics |
| 2 | **Extra Processing** | Doing more work than needed | Over-engineering, unnecessary approvals, excessive documentation, redundant testing | Long cycle times for simple changes |
| 3 | **Waiting** | Idle time between steps | Waiting for review, waiting for deploy, waiting for decisions, blocked PRs | Queue buildup, aging tickets |
| 4 | **Motion** | Unnecessary movement of people/context | Context switching, excessive meetings, knowledge silos requiring handoffs | Low flow efficiency, frequent interruptions |
| 5 | **Inventory** | Partially done work (WIP) | Unmerged branches, unfinished stories, accumulated backlogs | High WIP count, stale PRs |
| 6 | **Transportation** | Unnecessary handoffs/movement of work | Multi-team approvals, ticket bouncing, passing work between silos | Long lead times, handoff delays |
| 7 | **Defects** | Work that must be redone | Bugs in production, rework, escaped defects, regressions | Defect rate, rework percentage |

### Waste Detection in Retrospectives

**Gather Data phase waste audit:**
```
For each waste type, collect data:

1. OVERPRODUCTION
   - Features shipped last quarter with <10% adoption
   - Stories completed but never deployed
   - Metrics: feature usage rate, shelf-ware ratio

2. EXTRA PROCESSING
   - Changes that took >5x expected time
   - Documents/reviews that added no value
   - Metrics: cycle time vs complexity scatter plot

3. WAITING
   - Average PR review wait time
   - Average time waiting for environments
   - Metrics: queue age distribution, wait:work ratio

4. MOTION
   - Context switches per day per developer
   - Time spent in meetings vs coding
   - Metrics: flow efficiency = value-add time / total lead time

5. INVENTORY
   - Current WIP count vs WIP limit
   - Age of oldest unmerged branch
   - Metrics: WIP aging chart, inventory carrying cost

6. TRANSPORTATION
   - Number of handoffs from idea to production
   - Teams involved in a single feature delivery
   - Metrics: handoff count, cross-team dependency count

7. DEFECTS
   - Defect escape rate (production bugs / total changes)
   - Rework percentage (time fixing vs building)
   - Metrics: defect density, MTTR, rework ratio
```

### Value Stream Mapping (VSM) for Waste Identification

Map the entire flow from idea to production, marking:
- **Process steps** (what happens)
- **Wait times** (how long between steps)
- **Value-add time** (time actually creating value)
- **Waste type** at each wait/inefficiency

```
┌─────────────────────────────────────────────────────────────────┐
│  SOFTWARE VALUE STREAM MAP                                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Idea → [Backlog] → [Design] → [Code] → [Review] → [Test]     │
│   VA:  0  VA: 0h   VA: 2h    VA: 4h    VA: 1h    VA: 2h       │
│   Wait: 5d  Wait: 2d  Wait: 0.5d  Wait: 1d  Wait: 2d          │
│                                                                 │
│  → [Staging] → [Approve] → [Deploy] → [Monitor]               │
│   VA: 0.5h    VA: 0.5h    VA: 0.25h  VA: 0.25h                │
│   Wait: 3d    Wait: 2d    Wait: 0.5d  Wait: 0d                │
│                                                                 │
│  Total Lead Time: ~16 days                                      │
│  Total Value-Add Time: ~10.5 hours                              │
│  Flow Efficiency: 10.5h / (16 × 8h) = 8.2%                    │
│                                                                 │
│  Major wastes:                                                  │
│  - Backlog wait (5d): inventory waste                           │
│  - Approve wait (2d): extra processing (unnecessary gate)       │
│  - Test wait (2d): waiting (environment contention)             │
│  - Staging wait (3d): transportation (handoff to QA team)       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Flow Efficiency Calculation

```
Flow Efficiency = (Value-Add Time) / (Total Lead Time) × 100%

Typical software teams: 5-15% flow efficiency
Good: 25-40%
Excellent: >40%
Manufacturing benchmark: 50-80%

Improvement lever: Reduce wait times (wastes 3, 5, 6)
Not: Work faster at each step (diminishing returns)
```

### Waste Prioritization Matrix

| Waste | Impact on Lead Time | Fix Difficulty | Priority |
|-------|-------------------|----------------|----------|
| Waiting (PR reviews) | High | Low (set SLAs) | **Fix first** |
| Inventory (high WIP) | High | Medium (WIP limits) | **Fix second** |
| Transportation (handoffs) | High | High (org change) | Plan long-term |
| Defects (rework) | Medium | Medium (test automation) | **Fix third** |
| Extra Processing | Medium | Low (remove gates) | Quick win |
| Overproduction | Low (hidden cost) | High (product discipline) | Plan long-term |
| Motion | Low-Medium | Medium (tooling/automation) | Opportunistic |

### Lean Principles Applied to Retrospectives

| Lean Principle | Retro Application |
|---------------|-------------------|
| **Eliminate waste** | Retro identifies the 7 wastes in current process |
| **Amplify learning** | Retro is itself a learning mechanism (short feedback loops) |
| **Decide late** | Keep options open, avoid premature commitment |
| **Deliver fast** | Short iterations, frequent delivery, reduce batch size |
| **Empower team** | Team identifies and fixes own waste, not management-imposed |
| **Build integrity in** | Quality built into process, not inspected in |
| **Optimize the whole** | Look at entire value stream, not local optimizations |

Source: Lean Software Development: An Agile Toolkit by Mary & Tom Poppendieck, 2003
Implementing Lean Software Development: From Concept to Cash by Mary & Tom Poppendieck, 2006

## Step 30: DORA Transformation Patterns

Source: https://dora.dev/ | Accelerate by Nicole Forsgren, Jez Humble, Gene Kim | https://dora.dev/research/

DORA research identifies 24 capabilities that drive software delivery performance. Teams progress through 4 performance tiers. Transformation follows predictable patterns: foundation → acceleration → optimization. Technical and culture practices co-evolve.

### DORA Performance Tiers

| Tier | Deployment Frequency | Lead Time | Change Failure Rate | Time to Restore Service |
|------|---------------------|-----------|--------------------|-----------------------|
| **Elite** | On-demand (multiple/day) | < 1 hour | 0-15% | < 1 hour |
| **High** | Weekly to monthly | 1 week to 1 month | 16-30% | < 1 day |
| **Medium** | Monthly to quarterly | 1 month to 6 months | 16-30% | 1 week to 1 month |
| **Low** | Less than quarterly | > 6 months | 16-30% | > 6 months |

**Distribution (approximate, DORA 2023):** Elite 19%, High 37%, Medium 29%, Low 15%

### DORA 24 Capabilities

#### Technical Capabilities

| # | Capability | Description | Key Practices |
|---|-----------|-------------|---------------|
| 1 | **Version control** | Everything in version control | Code, config, infrastructure, tests, scripts |
| 2 | **Deployment automation** | Automated deployment process | One-click deploy, CI/CD pipeline |
| 3 | **Continuous integration** | Merge to trunk daily, automated build + test | Trunk-based dev, feature branches < 1 day |
| 4 | **Trunk-based development** | Short-lived branches, merge to main frequently | Branch lifetime < 1 day |
| 5 | **Test automation** | Automated tests at multiple levels | Unit, integration, acceptance tests |
| 6 | **Test data management** | Easy to get test data | On-demand test data, synthetic data |
| 7 | **Shift left on security** | Security integrated early in dev | SAST, DAST, dependency scanning in CI |
| 8 | **Continuous delivery** | Code always in deployable state | Automated deploy pipeline, feature flags |
| 9 | **Database change management** | Database changes version-controlled | Migration scripts, automated schema updates |
| 10 | **Deployment automation (infra)** | Infrastructure changes automated | IaC, automated provisioning |
| 11 | **Loosely coupled architecture** | Teams can deploy independently | Microservices, decoupled services, API contracts |
| 12 | **Empowered teams** | Teams choose own tools and approaches | Autonomy in tooling decisions |
| 13 | **Monitoring and observability** | Production monitoring + alerting | Metrics, logs, traces, dashboards |
| 14 | **Proactive failure notification** | Detect issues before users report | Synthetic monitoring, anomaly detection |
| 15 | **Telemetry in CI/CD** | Pipeline metrics visible | Build times, deploy frequency, failure rates |

#### Management Capabilities

| # | Capability | Description | Key Practices |
|---|-----------|-------------|---------------|
| 16 | **Change approval processes** | Lightweight, peer-based review | Peer review, automated checks vs CAB |
| 17 | **Release management** | Frequent, low-risk releases | Feature flags, canary, blue-green |
| 18 | **Product management** | Small batch, outcome-focused | MVP, continuous discovery, A/B testing |
| 19 | **Lean product management** | Limit WIP, work in small batches | WIP limits, flow efficiency focus |

#### Cultural Capabilities

| # | Capability | Description | Key Practices |
|---|-----------|-------------|---------------|
| 20 | **Westrum organizational culture** | Generative culture (learning-focused) | Blameless postmortems, psychological safety |
| 21 | **Job satisfaction** | Team members satisfied with work | Autonomy, mastery, purpose |
| 22 | **Learning culture** | Invest in learning, allow time for it | 20% time, learning days, conference budgets |
| 23 | **Transformational leadership** | Leaders who inspire and support change | Vision, intellectual stimulation, supportive |
| 24 | **Psychological safety** | Safe to take risks, fail, learn | No blame, experimentation encouraged |

### Transformation Path

Teams don't jump from Low to Elite. They follow a predictable path:

```
Phase 1: FOUNDATION (Low → Medium)
├── Version control for everything
├── Basic CI (automated build)
├── Automated unit tests
├── Deployment automation (at least staging)
├── Monitoring basics (error tracking, uptime)
├── Postmortem practice (blameless)
├── Change approval: lightweight peer review
└── Cultural: shift from blame to learning

Phase 2: ACCELERATION (Medium → High)
├── Trunk-based development
├── Full CI/CD pipeline
├── Comprehensive test automation
├── Infrastructure as Code
├── Feature flags for release management
├── Observability (metrics, logs, traces)
├── Loosely coupled architecture
├── Security shift-left
├── Cultural: psychological safety established
└── Cultural: learning culture (dedicated time)

Phase 3: OPTIMIZATION (High → Elite)
├── On-demand deployment
├── Sub-hour lead time
├── Change failure rate < 15%
├── Sub-hour recovery time
├── Empowered teams (choose own tools)
├── Telemetry in CI/CD (pipeline analytics)
├── Proactive failure detection
├── Database change management automated
├── Cultural: transformational leadership
└── Cultural: continuous improvement routine
```

### Technical + Culture Co-Evolution

**Key finding:** Technical practices and cultural practices must co-evolve. Neither alone drives high performance.

```
Technical Alone (fails):
├── Implement CI/CD but still have blame culture
├── Result: Faster delivery of broken code, fear of deploying
└── Plateaus at Medium tier

Culture Alone (fails):
├── Great psychological safety but manual processes
├── Result: Happy team delivering slowly with high error rate
└── Plateaus at Medium tier

Co-Evolution (succeeds):
├── CI/CD + blameless postmortems → fast recovery, continuous improvement
├── Trunk-based dev + psychological safety → safe to merge frequently
├── Feature flags + empowered teams → team controls release timing
└── Achieves High → Elite performance
```

**Co-evolution pairs:**

| Technical Practice | Cultural Pair | Why They Co-Evolve |
|-------------------|--------------|-------------------|
| Trunk-based dev | Psychological safety | Safe to merge small changes frequently |
| Feature flags | Empowered teams | Team decides when to enable features |
| CI/CD | Blameless postmortems | Fast recovery requires learning from failure |
| Monitoring | Learning culture | Data drives continuous improvement |
| Loosely coupled | Team autonomy | Independent deploy = independent decisions |
| Shift-left security | Shared responsibility | Everyone owns security, not just security team |

### Assessing Current Tier and Next Steps

```
Current State Assessment:

1. Deployment frequency:  _________ → maps to tier: _________
2. Lead time:            _________ → maps to tier: _________
3. Change failure rate:  _________ → maps to tier: _________
4. Time to restore:      _________ → maps to tier: _________

Overall tier = lowest of 4 metrics (bottleneck thinking)

Next improvement:
├── Identify which metric is holding back tier advancement
├── Find the capability gap that affects that metric
├── Check: is it technical or cultural gap?
├── If cultural: address first (culture enables technical)
├── If technical: implement with cultural support
└── Reassess in 4-8 weeks
```

### DORA Capability Priority by Phase

| Phase | Priority 1 | Priority 2 | Priority 3 |
|-------|------------|------------|------------|
| **Foundation** | Version control | CI (automated build) | Blameless postmortems |
| **Acceleration** | Trunk-based dev | Test automation | Psychological safety |
| **Optimization** | Telemetry in CI/CD | Proactive monitoring | Transformational leadership |

### Retrospective Integration

| Retro Phase | DORA Activity |
|-------------|--------------|
| Set the Stage | State current DORA tier and target tier |
| Gather Data | DORA metrics (4 key metrics), capability checklist scores |
| Generate Insights | Identify which capability gap limits tier advancement |
| Decide What to Do | One capability improvement, technical or cultural (check co-evolution pair) |
| Close | Reassess target tier, celebrate progress |

**Retro question:** "Are we improving technical capability and cultural capability together, or neglecting one?"

Source: Accelerate by Nicole Forsgren, Jez Humble, Gene Kim, 2018 | https://dora.dev/research/

## Step 33: Technical Debt Management

Source: Martin Fowler's Technical Debt Quadrant: https://martinfowler.com/bliki/TechnicalDebt.html | Ward Cunningham's original metaphor: https://wiki.c2.com/?WardExplainsDebtMetaphor | Strangler Fig: https://martinfowler.com/bliki/StranglerFigApplication.html

Technical debt is deliberate or inadvertent suboptimal technical choices that incur ongoing cost. Ward Cunningham's original metaphor: shipping first-time code is like going into debt, with interest on that debt being the extra effort required to extend the code in the future. Martin Fowler categorized debt into a 2×2 quadrant. Strangler Fig pattern enables incremental debt reduction.

### Cunningham's Original Metaphor

> "Shipping first time code is like going into debt. A little debt speeds development so long as it is paid back promptly with a rewrite... The danger occurs when the debt is not repaid. Every minute spent on not-quite-right code counts as interest on that debt."
> — Ward Cunningham, 1992

Key insight: debt is not inherently bad. Deliberate, controlled debt can accelerate delivery. The problem is **unmanaged** debt where interest compounds.

### Fowler's Technical Debt Quadrant

Source: https://martinfowler.com/bliki/TechnicalDebt.html

Two axes: **Reckless vs Prudent** × **Deliberate vs Inadvertent**

```
                    Deliberate              Inadvertent
                ┌─────────────────────┬─────────────────────┐
   Prudent      │                     │                     │
                │  "We don't have     │  "Now we know how   │
                │   time to design"   │   we should have    │
                │                     │   done it"          │
                │  Acceptable debt:   │  Learning debt:     │
                │  known tradeoff,    │  discovered through  │
                │  plan to repay      │  experience          │
                │                     │                     │
                ├─────────────────────┼─────────────────────┤
   Reckless     │                     │                     │
                │  "We don't have     │  "What's layered    │
                │   time for design"  │   design?"          │
                │                     │                     │
                │  Dangerous debt:    │  Dangerous debt:    │
                │  knowingly creating │  incompetence creating│
                │  mess               │  mess unknowingly    │
                │                     │                     │
                └─────────────────────┴─────────────────────┘
```

| Quadrant | Type | Example | Retro Response |
|----------|------|---------|---------------|
| **Prudent + Deliberate** | Strategic debt | "Ship now, refactor next sprint" | Track it, schedule repayment, timebox |
| **Prudent + Inadvertent** | Learning debt | "After building it, we see a better design" | Refactor when discovered, share learning |
| **Reckless + Deliberate** | Negligent debt | "No time for tests, just ship it" | Stop. This is organizational dysfunction. Escalate. |
| **Reckless + Inadvertent** | Accidental debt | Developer doesn't know design patterns | Training, pair programming, code review, mentoring |

### Strangler Fig Pattern for Debt

Source: https://martinfowler.com/bliki/StranglerFigApplication.html

Named after strangler fig trees that grow around host trees until they replace them. Apply incrementally to technical debt:

```
Phase 1: Wrap (add facade/API around legacy code)
├── New code calls legacy through clean interface
├── Legacy still runs, but new consumers use new interface
└── Zero risk: legacy untouched

Phase 2: Redirect (route traffic to new implementation)
├── Build new implementation behind the interface
├── Route percentage of traffic to new code (canary)
├── Compare behavior: new vs legacy
└── Low risk: can revert traffic instantly

Phase 3: Replace (remove legacy code)
├── All traffic on new implementation
├── Legacy code no longer called
├── Remove legacy code
└── Debt eliminated incrementally

Phase 4: Clean up
├── Remove facade if no longer needed
├── Update documentation
└── Retrospective: what did we learn?
```

**Strangler Fig applied to common debt:**

| Debt Type | Wrap | Redirect | Replace |
|-----------|------|----------|---------|
| Monolith → Microservices | Extract one service behind API | Route traffic to new service | Decommission monolith module |
| Old library → New library | Adapter pattern around old lib | New consumers use new lib | Remove old lib dependency |
| Manual process → Automated | Document manual steps | Run automation alongside manual | Remove manual process |
| Legacy database → New database | Read from new, write to both | Read/write from new | Decommission old DB |

### Quantifying Technical Debt

**Technical Debt Ratio (TDR):**

```
TDR = (cost to fix debt) / (total codebase cost to develop from scratch)

Example:
  Cost to fix all known debt:  800 person-hours
  Cost to rewrite from scratch: 10,000 person-hours
  TDR = 800 / 10,000 = 8%

Industry benchmark: TDR < 5% is healthy, 5-10% needs attention, > 10% is critical
```

**SQALE (Software Quality Assessment based on Lifecycle Expectations):**

Source: https://www.sonarsource.com/

SQALE method measures debt as remediation cost per quality characteristic:

| SQALE Characteristic | What It Measures | Measurement |
|---------------------|-----------------|-------------|
| Reliability | Bugs, potential failures | Hours to fix all reliability issues |
| Maintainability | Code smells, complexity | Hours to fix all maintainability issues |
| Testability | Test coverage, coupling | Hours to achieve adequate test coverage |
| Portability | Platform dependencies | Hours to remove platform lock-in |
| Security | Vulnerabilities, CVEs | Hours to fix all security issues |
| Efficiency | Performance issues | Hours to fix all performance issues |
| Changeability | Modularity, duplication | Hours to reduce coupling/duplication |

**Cost of Delay for debt:**

```
Cost of Delay = (debt impact per sprint) × (number of sprints until fixed)

Example:
  Tech debt in payment module adds 2 days/sprint to feature work
  Sprint cost: $50,000
  2 days = $25,000/sprint
  If fix is delayed 6 sprints: Cost of Delay = $25,000 × 6 = $150,000
  If fix costs $40,000 now: ROI = ($150,000 - $40,000) / $40,000 = 275%

Fix now. Waiting is expensive.
```

**Debt tracking template:**

```
Technical Debt Register:

| ID | Description | Quadrant | TDR Impact | Cost of Delay | Fix Effort | Priority |
|----|-------------|----------|------------|---------------|------------|----------|
| TD-1 | Payment module has no tests | Reckless+Deliberate | +2% | $25k/sprint | 3 sprints | P1 |
| TD-2 | Old auth library, known better | Prudent+Inadvertent | +1% | $10k/sprint | 1 sprint | P2 |
| TD-3 | Hardcoded config values | Prudent+Deliberate | +0.5% | $5k/sprint | 2 days | P3 |
```

### Retrospective Integration

| Retro Phase | Debt Activity |
|-------------|--------------|
| Set the Stage | State current TDR, list top 3 debt items |
| Gather Data | Measure cost-of-delay for top debt items, track new debt created this sprint |
| Generate Insights | Categorize debt (Fowler quadrant), identify root causes |
| Decide What to Do | Plan one debt repayment (Strangler Fig if large), timebox it |
| Close | Update debt register, celebrate debt reduction |

**Retro questions:**
- "What debt did we create this sprint? Was it deliberate or inadvertent?"
- "Which quadrant is our debt in? Is it strategic or negligent?"
- "What is the cost of delaying our top debt item another sprint?"
- "Can we apply Strangler Fig to our biggest debt item?"
- "Is our TDR going up or down trend?"

Source: Ward Cunningham, 1992 | Martin Fowler, 2009 | Strangler Fig: Fowler, 2004 | SQALE: SonarSource

## Pitfalls

1. **Don't skip the Prime Directive** — sets blameless tone
2. **Don't allow more than 3 action items** — focus beats breadth
3. **Don't assign actions to "the team"** — assign to named person
4. **Don't skip following up previous actions** — accountability matters
5. **Don't use same format every time** — rotate formats to stay fresh
6. **Don't skip anonymous input** — people self-censor in groups
7. **Don't run retros longer than 90 min** — fatigue kills quality
8. **Don't skip postmortems for incidents** — every P1/P2 gets one
9. **Don't blame individuals** — focus on systems and processes
10. **Don't skip data** — DORA metrics, incident counts, team health scores

## Step 6: DORA Metrics as Retro Input

Source: https://dora.dev/guides/dora-metrics-four-key-metrics/

Use DORA 4+1 metrics as objective data input for retrospective "Gather Data" phase.

### The 4+1 Metrics

| Metric | What It Measures | Elite | High | Medium | Low |
|--------|-----------------|-------|------|--------|-----|
| **Deployment Frequency** | How often code deploys to production | Multiple/day | Weekly–Daily | Monthly–Weekly | Monthly+ |
| **Lead Time for Changes** | Commit to production time | < 1 hour | 1 day–1 week | 1 week–1 month | 1 month+ |
| **Change Failure Rate** | % deployments causing failures | < 5% | 5–10% | 10–15% | 15%+ |
| **Mean Time to Recovery** (MTTR) | Time to restore service after failure | < 1 hour | < 1 day | 1 day–1 week | 1 week+ |
| **Reliability** (+1) | Operational performance targets met | Meets SLOs | Near SLOs | Misses occasionally | Misses often |

### Integrating DORA into Retro Agenda

During **Gather Data** phase (Step 2 of retro agenda):
1. Display current sprint's DORA metrics dashboard
2. Compare against previous sprint and industry benchmarks
3. Identify which metric degraded or improved
4. Correlate metric changes with sprint events (incidents, tooling changes, team changes)
5. Use metric trends as input for "Generate Insights" discussion

### DORA-to-Action Mapping

| Metric Problem | Likely Root Area | Retro Focus |
|---------------|-----------------|-------------|
| Low deploy frequency | Batch size too large, manual gates | Automation, trunk-based development |
| Long lead time | Slow reviews, env provisioning | Code review process, CI/CD pipeline |
| High change failure rate | Insufficient testing, risky changes | Test coverage, feature flags, canary deploys |
| Slow MTTR | Poor observability, unclear runbooks | Monitoring, alerting, incident response |
| Low reliability | SLO not defined, capacity issues | SLO review, load testing, architecture |

Source: https://dora.dev/guides/

## Step 7: Team Topologies Awareness

Source: https://teamtopologies.com/

Retrospective format and focus areas should adapt to team type (Team Topologies model by Skelton & Pais).

### Four Team Types

| Team Type | Purpose | Retro Focus |
|-----------|---------|-------------|
| **Stream-aligned** | Aligned to single value stream | Flow efficiency, fast feedback, reducing cognitive load |
| **Enabling** | Helps stream-aligned teams acquire new capabilities | Knowledge transfer effectiveness, adoption rate |
| **Complicated-subsystem** | Owns subsystem requiring deep expertise | Technical debt, integration APIs, documentation |
| **Platform** | Provides internal services to reduce cognitive load | Self-service quality, API stability, onboarding time |

### Interaction Modes

| Mode | When | Retro Question |
|------|------|---------------|
| **Collaboration** | Two teams working closely for discovery | "Are we learning fast enough to split back?" |
| **X-as-a-Service** | One team provides API/tool to another | "Is the service meeting consumers' needs?" |
| **Facilitating** | Enabling team helps another learn | "Has the receiving team gained the capability?" |

### Retro Adaptation by Team Type

- **Stream-aligned:** Emphasize value delivery speed, customer feedback loops, feature lead time
- **Enabling:** Track capability transfer success, team satisfaction scores from served teams
- **Complicated-subsystem:** Focus on API stability, documentation completeness, integration test coverage
- **Platform:** Measure developer experience (DX), self-service adoption, platform reliability

## Step 8: Value Stream Mapping for Flow Optimization

Source: https://www.atlassian.com/team-playbook/plays/value-stream-mapping

Value Stream Mapping (VSM) visualizes end-to-end flow of work from request to delivery. Use in retro to identify waste and bottlenecks.

### VSM Process

```
[Customer Request] → [Analysis] → [Development] → [Review] → [Testing] → [Deploy] → [Customer Value]
    │                  │            │              │          │           │
    ▼                  ▼            ▼              ▼          ▼           ▼
  2 days wait      1 day        3 days         2 days wait  1 day      0.5 days
                                                (queue)
```

### Metrics to Capture

| Metric | Definition | Calculation |
|--------|-----------|-------------|
| **Process Time** | Actual work time | Sum of active work steps |
| **Lead Time** | Request to delivery | Wall clock from start to end |
| **Wait Time** | Time in queues | Lead Time minus Process Time |
| **Flow Efficiency** | % of time adding value | (Process Time / Lead Time) × 100 |
| **% Complete & Accurate** | Work passing through without rework | (Units without rework / Total units) × 100 |

### Retro Integration

1. **Map current state** — team draws their actual workflow with times
2. **Identify waste** — queues, handoffs, rework loops, waiting
3. **Calculate flow efficiency** — typical software teams: 5–15%
4. **Design future state** — target 25%+ flow efficiency
5. **Create improvement actions** — pick biggest queue/waste to attack

### Common Wastes (Muda) in Software

| Waste | Example | Countermeasure |
|-------|---------|---------------|
| Waiting | PR review queue | Review WIP limits, pair programming |
| Handoffs | Dev → QA → Ops | Cross-functional teams, automation |
| Partially done work | Half-merged branches | Smaller batch sizes, trunk-based dev |
| Extra features | Unvalidated requirements | Lean UX, A/B testing |
| Task switching | Context-switching between projects | WIP limits, team focus |
| Defects | Bugs found in production | Shift-left testing, CI/CD |
| Motion | Manual deployment steps | Automation, self-service platforms |

Source: https://kanbanize.com/lean-management/value-waste/value-stream-mapping

## Step 9: Retrospective Anti-Patterns

Anti-patterns that destroy retrospective value. Watch for these and actively counter them.

### Always Same Format
**Symptom:** Start/Stop/Continue every sprint for months.
**Impact:** Stale thinking, participants disengage, same answers recycled.
**Fix:** Rotate formats: 4Ls, Sailboat, Timeline, Fishbone, Lean Coffee. Never repeat same format two sprints in row.

### No Follow-Up on Action Items
**Symptom:** Actions from last retro never discussed. Nobody tracks completion.
**Impact:** Team learns retro is performative. Trust erodes. Participation drops.
**Fix:** Start every retro reviewing previous action items. Use tracking board (Kanban, Jira). Assign owners. Report status.

### Blame Game
**Symptom:** "If Dev A hadn't pushed that bug..." or "QA missed this."
**Impact:** Psychological safety destroyed. People hide problems. Blameless culture fails.
**Fix:** Read Prime Directive aloud. Redirect: "What in our process allowed this?" Use "we" language. Facilitator enforces.

### Skipping Data
**Symptom:** Pure opinion-based discussion. No metrics, no timeline, no evidence.
**Impact:** Arguments based on feelings. Recency bias dominates. Real issues missed.
**Fix:** Present DORA metrics, incident data, sprint burndown, team health scores before discussion. Data anchors insights.

### No Action Items
**Symptom:** Good discussion, no commitments at end. "Let's keep talking about this."
**Impact:** Same problems recur next sprint. Improvement stalls.
**Fix:** Force decision: 1-3 concrete actions with owner and due date. "If no action, what was the point?"

### Manager Dominance
**Symptom:** Manager speaks first, longest, and most. Others defer.
**Impact:** Junior members self-censor. Political dynamics override honest reflection.
**Fix:** Manager speaks last. Use silent brainstorming (sticky notes) before discussion. Facilitator enforces equal airtime.

### Skipping Retros Entirely
**Symptom:** "We're too busy to retro this sprint." Canceled under deadline pressure.
**Impact:** Continuous improvement stops. Technical/process debt accumulates silently.
**Fix:** Treat retro as sprint ceremony equal to planning. Never cancel. If time-crunched, do 30-min focused retro.

### Meeting Without Psychological Safety
**Symptom:** People afraid to raise real issues. Surface-level "everything's fine."
**Impact:** Hidden problems fester. Only cosmetic improvements discussed.
**Fix:** Measure psychological safety (see Step 11). Anonymous input. Leader vulnerability modeling.

## Step 10: Remote Retrospective Patterns

Remote retros require different facilitation than co-located. Tools and patterns to compensate for lack of physical presence.

### Tool Selection

| Tool | Best For | Key Feature |
|------|----------|-------------|
| **Miro** — https://miro.com | Complex visual retros | Infinite canvas, templates, voting |
| **MURAL** — https://mural.co | Design-thinking retros | Facilitation Superpowers, timer |
| **EasyRetro** — https://easyretro.io | Simple column-based | Anonymous boards, CSV export |
| **Parabol** — https://www.parabol.co | Sprint-integrated retro | Jira/Linear integration, async mode |
| **Retrium** — https://retrium.com | Structured facilitation | Guided format selection, action tracking |
| **FunRetro** — https://funretro.io | Quick lightweight | Simple drag-drop, voting |
| **Geekbot** — https://geekbot.com | Async standup/retro | Slack-native, no meeting needed |
| **TeamRetro** — https://www.teamretro.com | Enterprise teams | Multi-team aggregation, analytics |

### Remote Facilitation Patterns

**Silent brainstorming (always do first)**
- Everyone writes sticky notes simultaneously (3-5 min)
- Timebox strictly, use timer visible to all

**Dot voting with limits**
- Each person gets 3-5 votes max
- Forces prioritization, prevents diffuse focus

**Breakout rooms for deep dives**
- Split into pairs/triads for 10-min focused discussion
- Come back and share top insight

**Async retrospective**
- For distributed teams across >3 time zones
- Use Parabol or Geekbot for threaded async input
- 48-hour window for input, then 30-min sync for action selection
- Structure: Day 1-2 async input → Day 3 sync discussion → Day 3 async action items

**Camera-on culture (but not mandated)**
- Encourage but don't require
- Use reaction emojis for engagement signals
- Check-in round: one word for how you're feeling

**Digital whiteboard etiquette**
- Use consistent color coding (e.g., green=good, red=bad, yellow=idea)
- Name your stickies
- Lock background elements
- Use frames/sections to organize

### Remote Anti-Patterns

- **Multitasking:** Camera off + no engagement = disengaged. Use polls, direct questions.
- **Time zone exclusion:** Same meeting time that always excludes APAC. Rotate times.
- **No warm-up:** Jump straight to data. Use 2-min check-in question first.
- **Screen sharing only:** One person controls board. Everyone should edit simultaneously.

## Step 11: Psychological Safety Measurement

Source: Amy Edmondson, "The Fearless Organization" (2018)

Psychological safety = team climate where people feel safe to take interpersonal risks. Measurable, improvable, prerequisite for honest retrospectives.

### Edmondson's 7-Item Survey

Rate 1 (Strongly Disagree) to 7 (Strongly Agree):

| # | Statement | Dimension |
|---|-----------|-----------|
| 1 | If you make a mistake on this team, it is often held against you. (R) | Mistake tolerance |
| 2 | Members of this team are able to bring up problems and tough issues. | Open communication |
| 3 | People on this team sometimes reject others for being different. (R) | Inclusion |
| 4 | It is safe to take a risk on this team. | Risk tolerance |
| 5 | It is difficult to ask other members of this team for help. (R) | Help-seeking |
| 6 | No one on this team would deliberately act in a way that undermines my efforts. | Mutual respect |
| 7 | Working with members of this team, my unique skills and talents are valued and utilized. | Value recognition |

(R) = Reverse-scored items.

### Scoring

```
Team Score = Average of all 7 items (reverse-score where marked)

Interpretation:
  6.0-7.0  →  High psychological safety (healthy retro environment)
  4.5-5.9  →  Moderate (some topics avoided, improvements needed)
  3.0-4.4  →  Low (significant trust issues, address before deep retros)
  1.0-2.9  →  Very low (retros will be performative, focus on safety first)
```

### When to Measure

- **Quarterly** at minimum — track trend over time
- **After major changes** — new manager, team restructuring, post-incident
- **Before investing in retro depth** — if safety is low, fix safety first

### Improving Low Scores

| Score Range | Intervention |
|-------------|-------------|
| < 3.0 | Leader vulnerability modeling (share own mistakes). 1-on-1s, not group retro. |
| 3.0–4.4 | Anonymous-only retros. Facilitator from outside team. Focus on small wins. |
| 4.5–5.9 | Standard retros with anonymous input option. Celebrate risk-taking. |
| 6.0+ | Full open retros. Team facilitates own sessions. Experiment with formats. |

### Integration with Retro

1. Administer survey quarterly (Google Form, SurveyMonkey, Parabol)
2. Share aggregated score (never individual) in retro "Gather Data" phase
3. Discuss: "What would help us feel safer to raise tough issues?"
4. Track score trend over time on team dashboard
5. Correlate with DORA metrics — high safety teams ship faster

Source: https://www.fearlessorganization.com/

## Step 12: Action Item Tracking Patterns

Retro action items without tracking are waste. Patterns to ensure follow-through.

### Kanban Board for Retro Actions

```
┌──────────┬──────────────┬──────────┬────────────┐
│  BACKLOG │  IN PROGRESS │ BLOCKED  │    DONE    │
├──────────┼──────────────┼──────────┼────────────┤
│ Action 4 │  Action 2    │ Action 5 │  Action 1  │
│ (new)    │  (Owner: JD) │ (needs   │  (Owner:   │
│          │              │  infra)  │   AS)      │
│ Action 6 │              │          │  Action 3  │
│ (new)    │              │          │  (Owner:   │
│          │              │          │   MK)      │
└──────────┴──────────────┴──────────┴────────────┘
```

**Board rules:**
- Visible to entire team (physical board or digital: Jira, Trello, Linear, GitHub Projects)
- Review board start of every retro
- WIP limit: max 3 items in "In Progress"
- Items stuck >2 sprints escalate to management
- "Done" requires evidence: PR merged, metric improved, process changed

### OKR Alignment

Align retro actions to team/company OKRs for strategic relevance.

| OKR | Key Result | Retro Action Example |
-----|------------|---------------------|
| O: Improve delivery speed | KR: Lead time < 2 days | Automate staging deployment |
| O: Improve delivery speed | KR: Deploy frequency ≥ 1/day | Implement feature flags |
| O: Reduce incidents | KR: MTTR < 1 hour | Create runbook for top 5 failure modes |
| O: Reduce incidents | KR: Change failure rate < 5% | Add integration test suite |
| O: Team satisfaction | KR: Health check avg > 4.0 | Reduce WIP limit from 5 to 3 |

**Alignment process:**
1. Retro generates candidate actions
2. Map each to relevant OKR/key result
3. Prioritize actions that advance OKRs
4. Drop actions with no OKR alignment (or create new OKR if gap exists)
5. Track OKR contribution in retro dashboard

### Tracking Formats

| Format | Best For | Tool Examples |
--------|----------|---------------|
| **Jira tickets** | Teams already using Jira | Jira, with sprint backlog |
| **GitHub Issues** | Open-source or GitHub-native teams | GitHub Projects |
| **Trello board** | Simple, visual tracking | Trello with "Retro Actions" board |
| **Notion database** | Documentation-heavy teams | Notion with status property |
| **Spreadsheet** | Low-tech, quick start | Google Sheets, shared |
| **Dedicated retro tool** | Integrated with retro flow | Parabol, Retrium built-in tracking |

### Escalation Protocol

```
Sprint N:   Action created → assigned to owner → "Backlog"
Sprint N+1: Owner started → "In Progress"
Sprint N+2: Still in progress → flag in retro
Sprint N+3: Still open → escalate to team lead
Sprint N+4: Still open → escalate to management, re-evaluate if action is valid
```

### Retrospective Dashboard

Track retro health metrics over time:

| Metric | Target | How to Measure |
|--------|--------|---------------|
| Action completion rate | > 80% | Done / Total actions per sprint |
| Time to complete | < 2 sprints | Avg sprints from creation to Done |
| Recurring themes | Decreasing | Tag actions, count repeated tags |
| Team satisfaction with retro | > 4/5 | End-of-retro rating |
| Psychological safety score | > 5.0 | Quarterly survey (Step 11) |
| DORA metrics trend | Improving | Dashboard overlay |

