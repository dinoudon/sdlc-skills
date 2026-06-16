---
name: sdlc-retrospective
description: "Retrospective formats: Start/Stop/Continue, 4Ls, Mad/Sad/Glad, Sailboat, Kaizen PDCA cycle, Toyota Kata (Mike Rother), blameless postmortems, incident deep-dive (Swiss cheese model), continuous improvement patterns. DORA metrics integration, DORA capability assessment, SPACE framework productivity metrics, Team Topologies awareness, team cognitive load measurement, Value Stream Mapping, flow metrics (lead time, cycle time, flow efficiency, WIP limits), anti-patterns, remote retro patterns, psychological safety measurement, action item tracking, green software retrospective, FinOps retrospective, platform engineering retrospective, Toyota Kata practice, Lean Software Development (7 wastes), Theory of Constraints (5 focusing steps, thinking processes), DORA transformation patterns (24 capabilities, 4 tiers), Platform Engineering Maturity (CNCF maturity model, Gartner predictions), Developer Productivity Research (SPACE applied, Microsoft studies, DORA culture findings), Technical Debt Management (Fowler's quadrant, Strangler Fig, quantification), Inner Source Patterns (InnerSource Commons, trusted committer, 30-day warranty), Staff Engineer Role (Larson's 4 archetypes), Engineering Ladder Design (dual-track IC/management), 1:1 Meeting Patterns (Lara Hogan, SBI, Radical Candor), Technical Decision Making (ADR, RFC, consensus models), Team Topologies (4 team types, 3 interaction modes, cognitive load theory), Inverse Conway Maneuver (org-to-architecture alignment), Value Stream Mapping (flow efficiency, bottleneck elimination), Team API (code API, communication API, work-with-us API)."
version: 4.9.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc-slim, sdlc, retrospective, kaizen, continuous-improvement, postmortem, agile, dora-metrics, team-topologies, value-stream-mapping, psychological-safety, space-framework, toyota-kata, flow-metrics, cognitive-load, incident-deepdive, dora-capabilities, green-software, finops, platform-engineering, sustainable-engineering, cloud-cost-optimization, developer-experience, lean-software-development, theory-of-constraints, dora-transformation, seven-wastes, throughput-accounting, platform-maturity, developer-productivity, technical-debt, inner-source, staff-engineer, engineering-ladder, one-on-ones, adr, rfc, technical-decisions, inverse-conway, team-api, stream-aligned, enabling-team]
    related_skills: [sdlc-prd-to-production, sdlc-requirements-engineering]
---## When to Use

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


## Pitfalls

1. **Don't skip the Prime Directive** — sets blameless tone
2. **Don't allow more than 3 action items** — focus beats breadth
3. **Don't assign actions to "the team"** — assign to named person
4. **Don't skip following up previous actions** — accountability matters
5. **Don't use same format every time** — rotate formats to stay fresh