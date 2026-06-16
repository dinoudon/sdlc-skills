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

---
name: sdlc-retrospective
description: "Retrospective formats: Start/Stop/Continue, 4Ls, Mad/Sad/Glad, Sailboat, Kaizen PDCA cycle, Toyota Kata (Mike Rother), blameless postmortems, incident deep-dive (Swiss cheese model), continuous improvement patterns. DORA metrics integration, DORA capability assessment, SPACE framework productivity metrics, Team Topologies awareness, team cognitive load measurement, Value Stream Mapping, flow metrics (lead time, cycle time, flow efficiency, WIP limits), 
## When to Use
Trigger when user:
- Runs sprint retrospective
- Plans continuous improvement
- Conducts blameless postmortem
- Measures team health or improvement
## Step 1: Retrospective Formats
Source: https://www.atlassian.com/team-playbook/plays/retrospective
Simplest format. Three columns on board.
| Start | Stop | Continue |
|-------|------|----------|
| Things team should begin doing | Things not adding value | Things working well |
| New practices to try | Wastes to eliminate | Keep doing these |
**Good for:** new teams, first retros, quick sessions.
| Liked | Learned | Lacked | Longed For |
|-------|---------|--------|------------|
| What went well | New insights | Missing resources/skills | Things wished existed |
| What enjoyed | Key takeaways | Support gaps | Ideal state |
**Good for:** cross-functional teams, product+engineering mix.
| Mad | Sad | Glad |
|-----|-----|------|
| Frustrations, blockers | Disappointments, missed opportunities | Wins, celebrations |
| Waste that caused anger | Things that didn't happen | Things that worked |
**Good for:** surfacing hidden frustrations, psychological safety.
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
## Step 2: Running a Retrospective
Source: https://www.atlassian.com/team-playbook/plays/retrospective
1. **Timebox:** 60-90 min for 2-week sprint
2. **Facilitator rotates** (not always SM)
3. **Anonymous input** via sticky notes or digital tools
4. **Limit action items** to 1-3 max. Assign owners.
5. **Follow up previous retro actions** first
6. **Prime Directive:** "Regardless of what we discover, we understand and truly believe that everyone did the best job they could, given what they knew at the time, their skills and abilities, the resources available, and the situation at hand." — Norm Kerth
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
```
## Step 3: Kaizen (Continuous Improvement)
Source: https://www.kaizen.com/what-is-kaizen.html
Japanese "change for better." Philosophy of ongoing incremental improvement.
- Good processes bring good results
- Go see for yourself (genchi genbutsu)
- Speak with data, manage by facts
- Take action to contain and correct root causes
- Work as team
- Kaizen is everybody's business
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
```
## Step 4: Blameless Postmortems
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
| Dimension | What It Measures | Example Metrics | Data Source |
|-----------|-----------------|-----------------|-------------|
| **Satisfaction** | Well-being, fulfillment, happiness | Developer satisfaction survey, eNPS, burnout indicators, tool satisfaction | Surveys, 1-on-1s |
| **Performance** | Quality and correctness of work | Code review thoroughness, test pass rate, defect escape rate, uptime/SLO adherence | CI/CD, monitoring |
| **Activity** | Volume of outputs and actions | Commits, PRs merged, deploys, tickets closed, builds triggered | Git, CI/CD, Jira |
| **Communication** | Collaboration effectiveness | PR review response time, knowledge sharing frequency, documentation updates, async sync ratio | Git, Slack, Confluence |
| **Efficiency** | Ability to complete work with minimal interruptions | Flow efficiency, interruption count, context-switch frequency, time in meetings vs. deep work | Calendar, flow tools |
> Measuring only Activity (commits, PRs) rewards volume over quality.
> Measuring only Performance (zero defects) rewards risk-aversion.
> SPACE requires balance across dimensions. No single metric captures productivity.
Rate 1 (Strongly Disagree) to 7 (Strongly Agree):
| # | Statement | Dimension |
|---|-----------|-----------|
| 1 | I am satisfied with my ability to get work done efficiently | Satisfaction |
| 2 | I have the tools and resources I need | Satisfaction |
| 3 | I am able to do my best work | Performance |
| 4 | The code I produce is high quality | Performance |
| 5 | I make meaningful contributions regularly | Activity |
## Step 14: DORA Capability Assessment Checklist
Source: https://dora.dev/capabilities/
DORA's research identifies 30+ capabilities that predict elite performance. Use this checklist in retro to assess team maturity and identify capability gaps.
1. In retro "Gather Data" phase, rate each capability: **Not Started / Emerging / Growing / Mastered**
2. Focus discussion on capabilities rated "Emerging" that correlate with weakest DORA metric
3. Select 1-2 capabilities to advance one level as sprint improvement experiments (align with Kata target conditions)
4. Re-assess quarterly
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
| Capability | Description | Level |
|-----------|-------------|-------|
| Loosely coupled architecture | Teams can deploy independently | |
| Architecture enables scaling | Can scale components independently | |
| Empowered team chooses tools | Teams select own tools (not mandated from above) | |
## Step 15: Incident Retrospective Deep-Dive
For P1/P2 incidents requiring deeper analysis than standard postmortem template (Step 4).
Reconstruct detailed incident timeline using multiple data sources.
| Source | What It Provides | Tool Examples |
|--------|-----------------|---------------|
| Monitoring dashboards | Metric anomalies, timestamps | Datadog, Grafana, New Relic |
| Log aggregation | Error patterns, stack traces | ELK, Splunk, Loki |
| Chat logs | Human communication timeline | Slack, Teams (search by keyword + time) |
| Deployment records | Code/config changes | CI/CD logs, ArgoCD, Spinnaker |
| Incident channel | Command, decisions, coordination | Slack/Teams incident channel |
| PagerDuty/on-call records | Alert timeline, escalation | PagerDuty, OpsGenie |
| Customer reports | External impact detection | Zendesk, Statuspage |
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
## Step 16: Team Cognitive Load Measurement
Source: Skelton & Pais, "Team Topologies" (2019) | https://teamtopologies.com/
Cognitive load = total mental effort required for a team to do its work. Excessive cognitive load → mistakes, slow delivery, burnout.
| Type | Definition | Can Reduce? | Example |
|------|-----------|-------------|---------|
| **Intrinsic** | Core domain complexity | No — inherent to problem | Understanding financial trading rules |
| **Extraneous** | Unnecessary complexity from environment | Yes — remove it | Poor documentation, unclear APIs, manual processes |
| **Germane** | Learning/domain understanding worth investing in | Increase it | Understanding business domain, system architecture |
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
## Step 17: Flow Metrics
Source: Kanban, Lean Software Development, Accelerate (Forsgren et al.)
Flow metrics measure how work moves through the system. Use alongside DORA for complete delivery picture.
```
Definition: Clock time from request created to value delivered to customer.
             Includes all wait time.

  [Customer Request] ──────────────────────────────→ [Value Delivered]
                    |←         Lead Time           →|

Measurement: Track from issue creation (or commit) to production deploy.
Sources: Jira (created → done), Git (first commit → merge to prod).
```
## Step 18: Team Topologies Deep-Dive
Source: Skelton & Pais, "Team Topologies" (2019) | https://teamtopologies.com/
Team Topologies provides a model for organizing engineering teams to optimize for fast flow of value. Use in retros to assess whether team structure is enabling or hindering delivery.
> "Any organization that designs a system will produce a design whose structure is a copy of the organization's communication structure." — Melvin Conway (1967)
**Implication for retros:** If delivery is slow, don't just examine processes — examine team boundaries. Misaligned team structures create architectural friction no process fix can overcome.
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
## Step 22: Developer Experience (DevEx)
Source: DevEx: A New Paradigm for Developer Productivity (GitHub, 2023)
Paper: https://queue.acm.org/detail.cfm?id=3595878
Developer Experience (DevEx) measures how developers perceive their work environment, tools, and processes. Poor DevEx = slow delivery, high attrition, low quality.
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
```
## Step 23: Westrum Culture Model
Source: Ron Westrum, "A Typology of Organisational Cultures" (2004)
Paper: https://qualitysafety.bmj.com/content/13/suppl_2/ii22
Ron Westrum's model classifies organizational culture into three types based on how information flows. DORA research shows generative culture is the strongest predictor of software delivery performance.
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
```
## Step 27: Toyota Kata Practice
Source: https://www.amazon.com/Toyota-Kata-Managing-Improvement-Adaptiveness/dp/0071635238 | https://www-personal.umich.edu/~mrother/Homepage.html
Toyota Kata is behavioral practice, not a tool or methodology. Two kata (routines) form the system: Improvement Kata and Coaching Kata. Goal: develop scientific thinking as daily habit, not periodic events.
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
```
## Step 28: Lean Software Development (7 Wastes)
Source: https://www.amazon.com/Lean-Software-Development-Agile-Toolkit/dp/0321150783 | Mary & Tom Poppendieck
Adapted from Toyota Production System's 7 wastes (muda) to software context. Use in retrospectives to identify systemic waste and improve flow.
| # | Waste | Definition | Software Examples | Detection Signal |
|---|-------|-----------|-------------------|------------------|
| 1 | **Overproduction** | Building features no one uses | Gold plating, unused features, speculative features | Low feature adoption, low usage analytics |
| 2 | **Extra Processing** | Doing more work than needed | Over-engineering, unnecessary approvals, excessive documentation, redundant testing | Long cycle times for simple changes |
| 3 | **Waiting** | Idle time between steps | Waiting for review, waiting for deploy, waiting for decisions, blocked PRs | Queue buildup, aging tickets |
| 4 | **Motion** | Unnecessary movement of people/context | Context switching, excessive meetings, knowledge silos requiring handoffs | Low flow efficiency, frequent interruptions |
| 5 | **Inventory** | Partially done work (WIP) | Unmerged branches, unfinished stories, accumulated backlogs | High WIP count, stale PRs |
| 6 | **Transportation** | Unnecessary handoffs/movement of work | Multi-team approvals, ticket bouncing, passing work between silos | Long lead times, handoff delays |
| 7 | **Defects** | Work that must be redone | Bugs in production, rework, escaped defects, regressions | Defect rate, rework percentage |
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
```
## Step 30: DORA Transformation Patterns
Source: https://dora.dev/ | Accelerate by Nicole Forsgren, Jez Humble, Gene Kim | https://dora.dev/research/
DORA research identifies 24 capabilities that drive software delivery performance. Teams progress through 4 performance tiers. Transformation follows predictable patterns: foundation → acceleration → optimization. Technical and culture practices co-evolve.
| Tier | Deployment Frequency | Lead Time | Change Failure Rate | Time to Restore Service |
|------|---------------------|-----------|--------------------|-----------------------|
| **Elite** | On-demand (multiple/day) | < 1 hour | 0-15% | < 1 hour |
| **High** | Weekly to monthly | 1 week to 1 month | 16-30% | < 1 day |
| **Medium** | Monthly to quarterly | 1 month to 6 months | 16-30% | 1 week to 1 month |
| **Low** | Less than quarterly | > 6 months | 16-30% | > 6 months |
**Distribution (approximate, DORA 2023):** Elite 19%, High 37%, Medium 29%, Low 15%
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
## Step 33: Technical Debt Management
Source: Martin Fowler's Technical Debt Quadrant: https://martinfowler.com/bliki/TechnicalDebt.html | Ward Cunningham's original metaphor: https://wiki.c2.com/?WardExplainsDebtMetaphor | Strangler Fig: https://martinfowler.com/bliki/StranglerFigApplication.html
Technical debt is deliberate or inadvertent suboptimal technical choices that incur ongoing cost. Ward Cunningham's original metaphor: shipping first-time code is like going into debt, with interest on that debt being the extra effort required to extend the code in the future. Martin Fowler categorized debt into a 2×2 quadrant. Strangler Fig pattern enables incremental debt reduction.
> "Shipping first time code is like going into debt. A little debt speeds development so long as it is paid back promptly with a rewrite... The danger occurs when the debt is not repaid. Every minute spent on not-quite-right code counts as interest on that debt."
> — Ward Cunningham, 1992
Key insight: debt is not inherently bad. Deliberate, controlled debt can accelerate delivery. The problem is **unmanaged** debt where interest compounds.
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
```
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
| Metric | What It Measures | Elite | High | Medium | Low |
|--------|-----------------|-------|------|--------|-----|
| **Deployment Frequency** | How often code deploys to production | Multiple/day | Weekly–Daily | Monthly–Weekly | Monthly+ |
| **Lead Time for Changes** | Commit to production time | < 1 hour | 1 day–1 week | 1 week–1 month | 1 month+ |
| **Change Failure Rate** | % deployments causing failures | < 5% | 5–10% | 10–15% | 15%+ |
| **Mean Time to Recovery** (MTTR) | Time to restore service after failure | < 1 hour | < 1 day | 1 day–1 week | 1 week+ |
| **Reliability** (+1) | Operational performance targets met | Meets SLOs | Near SLOs | Misses occasionally | Misses often |
During **Gather Data** phase (Step 2 of retro agenda):
1. Display current sprint's DORA metrics dashboard
2. Compare against previous sprint and industry benchmarks
3. Identify which metric degraded or improved
4. Correlate metric changes with sprint events (incidents, tooling changes, team changes)
5. Use metric trends as input for "Generate Insights" discussion
| Metric Problem | Likely Root Area | Retro Focus |
|---------------|-----------------|-------------|
| Low deploy frequency | Batch size too large, manual gates | Automation, trunk-based development |
| Long lead time | Slow reviews, env provisioning | Code review process, CI/CD pipeline |
| High change failure rate | Insufficient testing, risky changes | Test coverage, feature flags, canary deploys |
| Slow MTTR | Poor observability, unclear runbooks | Monitoring, alerting, incident response |
## Step 7: Team Topologies Awareness
Source: https://teamtopologies.com/
Retrospective format and focus areas should adapt to team type (Team Topologies model by Skelton & Pais).
| Team Type | Purpose | Retro Focus |
|-----------|---------|-------------|
| **Stream-aligned** | Aligned to single value stream | Flow efficiency, fast feedback, reducing cognitive load |
| **Enabling** | Helps stream-aligned teams acquire new capabilities | Knowledge transfer effectiveness, adoption rate |
| **Complicated-subsystem** | Owns subsystem requiring deep expertise | Technical debt, integration APIs, documentation |
| **Platform** | Provides internal services to reduce cognitive load | Self-service quality, API stability, onboarding time |
| Mode | When | Retro Question |
|------|------|---------------|
| **Collaboration** | Two teams working closely for discovery | "Are we learning fast enough to split back?" |
| **X-as-a-Service** | One team provides API/tool to another | "Is the service meeting consumers' needs?" |
| **Facilitating** | Enabling team helps another learn | "Has the receiving team gained the capability?" |
- **Stream-aligned:** Emphasize value delivery speed, customer feedback loops, feature lead time
- **Enabling:** Track capability transfer success, team satisfaction scores from served teams
- **Complicated-subsystem:** Focus on API stability, documentation completeness, integration test coverage
- **Platform:** Measure developer experience (DX), self-service adoption, platform reliability
## Step 8: Value Stream Mapping for Flow Optimization
Source: https://www.atlassian.com/team-playbook/plays/value-stream-mapping
Value Stream Mapping (VSM) visualizes end-to-end flow of work from request to delivery. Use in retro to identify waste and bottlenecks.
```
[Customer Request] → [Analysis] → [Development] → [Review] → [Testing] → [Deploy] → [Customer Value]
    │                  │            │              │          │           │
    ▼                  ▼            ▼              ▼          ▼           ▼
  2 days wait      1 day        3 days         2 days wait  1 day      0.5 days
                                                (queue)
```
## Step 9: Retrospective Anti-Patterns
Anti-patterns that destroy retrospective value. Watch for these and actively counter them.
**Symptom:** Start/Stop/Continue every sprint for months.
**Impact:** Stale thinking, participants disengage, same answers recycled.
**Fix:** Rotate formats: 4Ls, Sailboat, Timeline, Fishbone, Lean Coffee. Never repeat same format two sprints in row.
**Symptom:** Actions from last retro never discussed. Nobody tracks completion.
**Impact:** Team learns retro is performative. Trust erodes. Participation drops.
**Fix:** Start every retro reviewing previous action items. Use tracking board (Kanban, Jira). Assign owners. Report status.
**Symptom:** "If Dev A hadn't pushed that bug..." or "QA missed this."
**Impact:** Psychological safety destroyed. People hide problems. Blameless culture fails.
**Fix:** Read Prime Directive aloud. Redirect: "What in our process allowed this?" Use "we" language. Facilitator enforces.
**Symptom:** Pure opinion-based discussion. No metrics, no timeline, no evidence.
**Impact:** Arguments based on feelings. Recency bias dominates. Real issues missed.
**Fix:** Present DORA metrics, incident data, sprint burndown, team health scores before discussion. Data anchors insights.
**Symptom:** Good discussion, no commitments at end. "Let's keep talking about this."
**Impact:** Same problems recur next sprint. Improvement stalls.
**Fix:** Force decision: 1-3 concrete actions with owner and due date. "If no action, what was the point?"
**Symptom:** Manager speaks first, longest, and most. Others defer.
**Impact:** Junior members self-censor. Political dynamics override honest reflection.
**Fix:** Manager speaks last. Use silent brainstorming (sticky notes) before discussion. Facilitator enforces equal airtime.
**Symptom:** "We're too busy to retro this sprint." Canceled under deadline pressure.
**Impact:** Continuous improvement stops. Technical/process debt accumulates silently.
## Step 10: Remote Retrospective Patterns
Remote retros require different facilitation than co-located. Tools and patterns to compensate for lack of physical presence.
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
## Step 11: Psychological Safety Measurement
Source: Amy Edmondson, "The Fearless Organization" (2018)
Psychological safety = team climate where people feel safe to take interpersonal risks. Measurable, improvable, prerequisite for honest retrospectives.
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
```
Team Score = Average of all 7 items (reverse-score where marked)

Interpretation:
  6.0-7.0  →  High psychological safety (healthy retro environment)
  4.5-5.9  →  Moderate (some topics avoided, improvements needed)
  3.0-4.4  →  Low (significant trust issues, address before deep retros)
  1.0-2.9  →  Very low (retros will be performative, focus on safety first)
```
## Step 12: Action Item Tracking Patterns
Retro action items without tracking are waste. Patterns to ensure follow-through.
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
## Unicorn Engineering Culture Patterns
Source: https://engineering.atspotify.com/
SQUAD (6-12 people): Autonomous, cross-functional team. Owns a feature area end-to-end. Has mission, long-term mission, and metrics. Like a mini-startup within the company.
TRIBE (40-150 people): Collection of related squads. Squads in same product area. Tribe lead provides vision and alignment.
CHAPTER (5-10 people): Same skill across squads. All backend engineers in a tribe. Chapter lead = line manager. Meets weekly to share practices.
GUILD (company-wide): Community of interest. Anyone interested in a topic (e.g., testing, security). Cross-tribe knowledge sharing. Voluntary participation.
Source: Reed Hastings, Netflix Culture
1. **Freedom and Responsibility**: High alignment, high autonomy
2. **Context, not Control**: Leaders set context, teams decide how
3. **Highly Aligned, Loosely Coupled**: Strategy aligned, execution autonomous
4. **Lead with Context**: Share business metrics, strategy, constraints openly
5. **Blameless Postmortems**: Focus on systems, not individuals
Source: Google SRE, Site Reliability Engineering
1. **Error budgets**: 50% of engineering time on toil reduction
2. **Blameless postmortems**: Focus on systemic improvements
3. **Toil tracking**: Measure and reduce manual operational work
4. **Production readiness review**: Before launch, not after incident
Source: Jeff Bezos
- Teams small enough to be fed by two pizzas (6-10 people)
- Each team owns a service/API end-to-end
- Teams are autonomous: choose their own tools, processes
- Communication via APIs, not meetings
## Related Skills
  - [sdlc-hiring-talent](sdlc-hiring-talent): Technical hiring and team building: recruiting, interview design, coding assessments, system design 
  - [sdlc-prd-to-production](sdlc-prd-to-production): End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monito