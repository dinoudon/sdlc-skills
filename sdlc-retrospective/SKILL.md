---
name: sdlc-retrospective
description: "Retrospective formats: Start/Stop/Continue, 4Ls, Mad/Sad/Glad, Sailboat, Kaizen PDCA cycle, Toyota Kata (Mike Rother), blameless postmortems, incident deep-dive (Swiss cheese model), continuous improvement patterns. DORA metrics integration, DORA capability assessment, SPACE framework productivity metrics, Team Topologies awareness, team cognitive load measurement, Value Stream Mapping, flow metrics (lead time, cycle time, flow efficiency, WIP limits), anti-patterns, remote retro patterns, psychological safety measurement, action item tracking."
version: 3.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, retrospective, kaizen, continuous-improvement, postmortem, agile, dora-metrics, team-topologies, value-stream-mapping, psychological-safety, space-framework, toyota-kata, flow-metrics, cognitive-load, incident-deepdive, dora-capabilities]
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
## Swiss Cheese Analysis — [Incident Name]

| Layer | Did it hold? | What was the hole? | Improvement |
|-------|-------------|-------------------|-------------|
| Code quality | ✓ Held | N/A | N/A |
| Code review | ✗ Failed | 400-line PR, reviewer rubber-stamped | Smaller PRs, review checklist |
| Automated testing | ✗ Failed | No integration test for DB path | Add DB integration test |
| Deployment controls | ✓ Held | Canary detected anomaly | N/A |
| Monitoring | ✗ Delayed | Alert fired 15 min after onset | Tighten alert threshold |
| Incident response | ✓ Held | Team mobilized in 5 min | N/A |

Holes aligned: Code review + Testing + Monitoring delay = path to incident
```

### Deep-Dive Retro Agenda (90 min)

| Phase | Time | Activity |
|-------|------|----------|
| Timeline review | 15 min | Walk through reconstructed timeline, verify timestamps |
| Contributing factors | 20 min | Fishbone or 5 Whys exercise |
| Swiss cheese mapping | 15 min | Map defense layers, identify which holes aligned |
| Detection analysis | 10 min | Why did we detect at X time? How to detect sooner? |
| Mitigation analysis | 10 min | Was mitigation effective? How to mitigate faster? |
| Action items | 15 min | 1-3 actions: close specific holes in specific layers |
| Close | 5 min | Appreciation, rate the retro |

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