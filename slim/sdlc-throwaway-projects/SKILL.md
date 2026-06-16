---
name: sdlc-throwaway-projects
description: "Planning throwaway/prototype projects: rapid prototyping, vibe coding workflows, MVP planning, spike solutions, PoC validation, hackathon prep, AI-assisted development, lean canvas, assumption mapping, time-boxing, graduation path (throwaway → production), decision frameworks for keep vs discard vs graduate, tech stack selection for speed, common pitfalls, and integration with 2026 AI coding tools (Cursor, Lovable, Bolt, Replit, v0, Claude Code, Gemini CLI)."
version: 1.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, throwaway, prototype, mvp, spike, poc, vibe-coding, rapid-prototyping, hackathon, proof-of-concept, ai-assisted, lean-startup, validation, experiment]
    related_skills: [sdlc-spike, sdlc-prd-to-production, sdlc-architecture-design, sdlc-testing-qa]
---

# Throwaway Projects

Planning throwaway/prototype projects: rapid prototyping, vibe coding, MVP planning, spike solutions, decision frameworks for keep vs discard.

## When to Use

Trigger when user:
- Needs to validate an idea quickly
- Mentions prototype, MVP, spike, PoC, demo, hackathon
- Wants to explore a new tech stack
- Needs proof-of-concept for stakeholder buy-off
- Asks "should we build this?"
- Wants to learn a technology by building something real
- Preparing for hackathon or demo day

## Step 1: Throwaway Project Taxonomy

### Types Compared

| Type | Purpose | Lifespan | Fidelity | End State |
|------|---------|----------|----------|-----------|
| **Spike** | Answer technical question | Hours–days | Low | Knowledge, discard |
| **Prototype** | Explore UX/interaction | Days–weeks | Medium | Feedback, discard |
| **PoC** | Prove feasibility | Days–weeks | Medium | Decision, discard |
| **MVP** | Test market demand | Weeks–months | High | Metrics, iterate or kill |
| **Demo** | Show possibility | Hours–days | Surface-level | Impress, discard |
| **Hackathon** | Build in time-box | 24–48h | Variable | Pitch, maybe continue |

### When to Pick Which

```
Need to answer "can we do X?" → Spike
Need to answer "should we do X?" → Prototype
Need to prove "X works" → PoC
Need to test "will people pay for X?" → MVP
Need to show "X is possible" → Demo
Need to build "something in 48h" → Hackathon project
```

## Step 2: Planning Framework

### Lean Canvas (1-page business model)

```
+------------------+------------------+------------------+
| PROBLEM          | SOLUTION         | UNIQUE VALUE      |
| Top 3 problems   | Top 3 features   | Single clear msg  |
|                  |                  |                   |
+------------------+------------------+
| KEY METRICS      | UNFAIR ADVANTAGE |
| Key activities   | Can't be easily  |
| to measure       | copied           |
+------------------+------------------+
| CHANNELS         | CUSTOMER SEGMENTS|
| Path to          | Target audience  |
| customers        | Early adopters   |
+------------------+------------------+
| COST STRUCTURE                  | REVENUE STREAMS           |
| Fixed + variable costs          | Revenue model             |
+---------------------------------+---------------------------+
```

### One-Pager Template

```markdown
# [Project Name]

## Hypothesis
We believe [target users] have [problem] and will [desired action] if we build [solution].

## Validation Criteria
- [Metric 1]: [target] — proves problem is real
- [Metric 2]: [target] — proves solution works
- [Metric 3]: [target] — proves willingness to pay/engage

## Scope
### In Scope
- [Feature 1]
- [Feature 2]
- [Feature 3]

### Out of Scope
- [Explicitly NOT building]
- [Deferred to future]

## Timeline
- Day 1-2: [Phase 1]
- Day 3-4: [Phase 2]
- Day 5: [Testing + decision]

## Exit Criteria
- SUCCESS: [What success looks like]
- PIVOT: [What pivot looks like]
- KILL: [What failure looks like]
```

### Assumption Mapping

Map assumptions on two axes: **certainty** (how sure are we?) and **importance** (how critical?).

```
HIGH IMPORTANCE
     |
     |  [Validate FIRST]    [Danger zone]
     |  High importance,     High importance,
     |  low certainty        high certainty
     |                       (still monitor)
     |
     +----------------------------------------
     |  [Ignore for now]    [Nice to have]
     |  Low importance,     Low importance,
     |  low certainty       high certainty
     |
LOW IMPORTANCE
     
     LOW CERTAINTY --------- HIGH CERTAINTY
```

**Rule:** Validate top-left quadrant first. Those assumptions can kill the project.

### Risk-First Ordering

```
1. List all assumptions
2. Rank by: Impact if wrong × Likelihood of being wrong
3. Build spike/prototype to test #1 assumption FIRST
4. If assumption fails → pivot or kill early
5. If assumption holds → move to #2
```

## Step 3: Speed-Optimized Workflow

### Vibe Coding Principles

```
1. Describe, don't code — natural language prompts
2. Iterate fast — prompt → review → adjust → repeat
3. Accept imperfection — "good enough" beats "perfect"
4. Ship and test — get user feedback ASAP
5. Save often — commit after every working change
```

### AI-Assisted Prototype Workflow

```
Phase 1: PLAN (10% of time)
├── Write one-pager
├── Map assumptions
├── Pick top 1-2 to validate
└── Choose tech stack

Phase 2: BUILD (60% of time)
├── Prompt AI for scaffold
├── Iterate on core feature ONLY
├── Skip auth, error handling, edge cases
├── Commit after each working feature
└── Test manually as you go

Phase 3: VALIDATE (20% of time)
├── Show to 3-5 target users
├── Collect structured feedback
├── Measure against validation criteria
└── Document findings

Phase 4: DECIDE (10% of time)
├── Score: kill / pivot / keep / graduate
├── If keep: plan graduation path
├── If kill: archive learnings
└── Retrospective
```

### Prompt Patterns for Prototypes

```
Scaffold prompt:
"Create a [type] app with [framework]. Include [feature 1], [feature 2]. 
Skip auth, tests, and error handling. Use mock data."

Iteration prompt:
"The [component] should [change]. Keep everything else the same."

Debug prompt:
"It's broken: [error]. Fix just this issue, don't refactor."

Feature prompt:
"Add [feature] to the existing code. Minimal changes only."
```

### No-Setup Tools for Speed

| Need | Tool | Setup Time |
|------|------|------------|
| Web app | Lovable / Bolt.new | 0 min |
| UI components | v0 by Vercel | 0 min |
| Backend API | Replit | 2 min |
| Mobile app | Lovable + Capacitor | 5 min |
| CLI tool | Claude Code | 0 min |
| Data pipeline | Cursor + Python | 5 min |
| Landing page | v0 / Bolt | 0 min |

## Step 4: Tech Stack Selection

### Decision Criteria

| Criterion | Weight | Questions |
|-----------|--------|-----------|
| Setup time | HIGH | How fast from zero to running? |
| Disposability | HIGH | Can I throw it away easily? |
| Learning curve | MEDIUM | Do I already know this? |
| Deployment speed | MEDIUM | How fast to get it live? |
| Cost | LOW-MED | Free tier available? |
| Fidelity | LOW | Does it look "real enough"? |

### Stack Recommendations

```
Web App (non-coder):
  → Lovable or Bolt.new
  → No setup, visual editor, instant deploy

Web App (developer):
  → Cursor + Next.js/React
  → Or Vite + any framework
  → Fast iteration, full control

Mobile App:
  → Lovable + Capacitor (web → mobile)
  → Or Flutter + Cursor (if know Dart)

API/Backend:
  → Replit (instant)
  → Or Hono/Express + Cursor

CLI Tool:
  → Claude Code / Gemini CLI
  → Node.js or Python script

Data/ML:
  → Cursor + Python + Jupyter
  → Google Colab for zero-setup

Landing Page:
  → v0 by Vercel (React/Tailwind)
  → Or Bolt.new (full page)
```

### Anti-Patterns

```
DON'T:
- Use unfamiliar frameworks "to learn" during a time-boxed prototype
- Set up CI/CD for throwaway projects
- Write comprehensive tests before validating the idea
- Pick microservices architecture for a prototype
- Optimize for scale before validating demand
- Spend >20% of time on styling/polish

DO:
- Use what you know
- Use w