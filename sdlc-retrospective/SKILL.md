---
name: sdlc-retrospective
description: "Retrospective formats: Start/Stop/Continue, 4Ls, Mad/Sad/Glad, Sailboat, Kaizen PDCA cycle, blameless postmortems, continuous improvement patterns. DORA metrics integration, Team Topologies awareness, Value Stream Mapping, anti-patterns, remote retro patterns, psychological safety measurement, action item tracking."
version: 3.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, retrospective, kaizen, continuous-improvement, postmortem, agile, dora-metrics, team-topologies, value-stream-mapping, psychological-safety]
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
Source: https://miketherother.com/

1. Understand direction (challenge)
2. Grasp current condition
3. Establish next target condition
4. PDCA toward target

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