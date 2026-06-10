---
name: sdlc-throwaway-projects
description: "Planning throwaway/prototype projects, vibe coding workflows, MVP planning, spike solutions, and decision frameworks for keep vs discard. Speed over perfection. Learn fast, decide fast."
version: 1.0.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, throwaway, prototype, mvp, spike, vibe-coding, hackathon, proof-of-concept, lean, rapid-iteration]
    related_skills: [sdlc-spike, sdlc-prd-to-production, sdlc-architecture-design]
---

# Throwaway Projects

Planning throwaway/prototype projects, vibe coding workflows, MVP planning, spike solutions, and decision frameworks for keep vs discard.

## When to Use

Trigger when user:
- Needs to validate idea before committing resources
- Wants to explore technical feasibility quickly
- Plans hackathon or demo project
- Asks "what if we tried X?"
- Needs proof-of-concept for stakeholder buy-in
- Wants to learn new tech stack rapidly
- Requests spike solution for unknown problem
- Building MVP to test market fit

## Throwaway Project Types

| Type | Purpose | Duration | Fidelity | Keep? |
|------|---------|----------|----------|-------|
| **Prototype** | Test UX/concept | Hours–days | Visual only | Usually no |
| **Spike** | Answer technical question | Hours–1 day | Minimal | No |
| **Proof-of-Concept** | Prove feasibility | Days | Working demo | Sometimes |
| **MVP** | Test market fit | Weeks | Functional | Maybe |
| **Demo** | Show stakeholders | Hours | Polished surface | No |
| **Hackathon** | Explore/innovate | 24–48h | Whatever works | Rarely |

### Prototype

Goal: answer "should we build this?" — not "how do we build this?"

```
Fidelity levels:
- Paper sketch    → 10 min, test basic flow
- Wireframe       → 1 hour, test layout/navigation
- Clickable mock  → 4 hours, test interaction
- Interactive HTML → 1 day, test with real users
```

### Spike

Goal: reduce uncertainty. Time-boxed investigation.

```
Spike output = decision, not code.

Examples:
- "Can library X handle 10k req/s?" → benchmark, report numbers
- "Does API Y support feature Z?"   → proof with code snippet
- "What's migration path for DB?"   → documented approach
```

### Proof-of-Concept (PoC)

Goal: prove something works. End-to-end slice.

```
PoC scope: one happy path, no error handling, no auth, no polish.
If PoC works → proceed. If PoC fails → pivot or abandon.
```

### MVP

Goal: minimum viable product to test market hypothesis.

```
MVP ≠ prototype. MVP ships to real users.

MVP rules:
- One core feature, done well
- Real users, real feedback
- Instrumented for learning
- Predefined success criteria

MVP types:

| Type | Goal | Example |
|------|------|---------|
| **Landing page MVP** | Test demand | Fake door test, waitlist |
| **Wizard of Oz** | Manual backend | Humans do what code would |
| **Concierge** | High-touch service | Manual delivery of value |
| **Single-feature** | One thing well | Stripe's original checkout |
| **Email MVP** | Test engagement | Manual email sequences |
| **Piecemeal** | Combine existing tools | Zapier + Google Sheets |
```

### Demo

Goal: communicate vision. No real functionality required.

```
Demo tricks:
- Hardcoded data looks real
- Smoke and mirrors acceptable
- Focus on story, not code
- Record video backup
```

### Hackathon

Goal: explore, learn, build something in compressed time.

```
Hackathon prep:
- Team formed, idea scoped
- Dev environment ready before start
- One-pager with goal + demo plan
- 20% time for polish/presentation
```

## Planning Framework

### Lean Canvas (10 min max)

```
+-------------------+-------------------+-------------------+
| PROBLEM           | SOLUTION          | UNIQUE VALUE PROP |
| Top 3 problems    | Top 3 features    | Single, clear msg |
+-------------------+-------------------+-------------------+
| KEY METRICS       | UNFAIR ADVANTAGE  | CHANNELS          |
| What you measure  | Can't be copied   | How you reach     |
+-------------------+-------------------+-------------------+
| COST STRUCTURE    | REVENUE STREAMS   |
| Fixed + variable  | How you make $    |
+-------------------+-------------------+
```

### One-Pager Template

```markdown
# [Project Name]

## Hypothesis
What we believe: [specific, testable statement]

## Experiment
What we'll build: [smallest thing to test hypothesis]

## Success Criteria
Pass: [metric] [target]
Fail: [metric] [target]
Kill: [conditions to abandon]

## Scope
In: [2-3 things]
Out: [everything else]

## Timeline
Start: [date]
Decision point: [date + N days]
Max duration: [hard stop]
```

### Scope Boundaries

```
IN SCOPE (explicit):
- [Thing 1]
- [Thing 2]
- [Thing 3]

OUT OF SCOPE (explicit):
- Auth/accounts
- Error handling
- Mobile responsive
- Performance optimization
- Production deployment
- Monitoring/alerting
- Documentation

TRUTH: every "out of scope" item is a decision to revisit IF project graduates.
```

## Speed-Optimized Workflow

### Vibe Coding Principles

```
1. Ship > perfect
2. Working > elegant
3. Copy > rewrite
4. Shortcut > best practice
5. Delete > refactor
```

### Vibe Coding Workflow

```
1. Start with working example (tutorial, GitHub repo, AI scaffold)
2. Modify to fit your case
3. When stuck: search > debug > ask AI > read docs
4. Copy-paste solutions from StackOverflow/GitHub without shame
5. Comment out broken code, don't fix it
6. Ship partial working version early
7. Iterate based on what breaks
```

### Code Quality Rules for Throwaway

```
KEEP:
- Working code (even ugly)
- Clear variable names for core logic
- Comments on non-obvious decisions

SKIP:
- Tests (unless testing is the hypothesis)
- Error handling beyond basic try/catch
- Type safety beyond basic types
- Code formatting consistency
- Performance optimization
- Security hardening
- Documentation

GREY AREA (depends on duration):
- Logging (skip for < 1 day, basic for > 1 day)
- Config management (skip for < 3 days, env vars for > 3 days)
- Basic auth (skip for demo, add for user testing)
```

### Time Allocation

```
For 1-day project:
- 10 min  → Define hypothesis + success criteria
- 20 min  → Choose stack + scaffold
- 4 hours → Build core
- 1 hour  → Polish demo path
- 30 min  → Document decision

For 1-week project:
- 2 hours → Plan + scope
- 1 day   → Scaffold + core
- 2 days  → Build + iterate
- 1 day   → Polish + test + document
```

### AI-Assisted Workflow

```
1. Describe what you want in plain language
2. AI generates scaffold/boilerplate
3. You modify for your specific case
4. AI helps with integration issues
5. You test the core hypothesis
6. Document what you learned
```

**AI prompt template:**
```
Build a [type] app that does [one thing].
Tech stack: [chosen stack].
No auth, no tests, no deployment config.
Focus on [specific feature].
Include: [specific requirements].
```

### No-Setup Tools

| Category | Tools | Setup Time |
|----------|-------|------------|
| **Frontend** | Codepen, CodeSandbox, StackBlitz | 0 min |
| **Backend** | Replit, Railway, Render | < 5 min |
| **Database** | Supabase, PlanetScale, Turso | < 5 min |
| **Full-stack** | Vercel, Netlify, Fly.io | < 10 min |
| **Mobile** | Expo, FlutterFlow, Retool | < 15 min |
| **API mock** | Mockoon, Beeceptor, json-server | 0 min |
| **Design** | Figma, Excalidraw, tldraw | 0 min |

## Tech Stack Selection

### Decision Matrix

| Factor | Weight | Options |
|--------|--------|---------|
| **Speed to hello world** | High | Choose what you know |
| **Ecosystem/libraries** | Medium | More libs = faster |
| **AI code generation** | Medium | Python/JS best supported |
| **Deployment ease** | Medium | Vercel/Railway = zero config |
| **Team familiarity** | High | Never learn new lang in throwaway |

### Recommended Stacks

```
Web app fast:     Next.js + Vercel + Supabase
API prototype:    FastAPI + SQLite + Railway
Mobile demo:      Expo + React Native
Data exploration: Python + Jupyter + Pandas
CLI tool:         Python or Go
Bot/automation:   Python + specific API
Desktop app:      Electron + React (if needed)
```

### Stack Decision Tree

```
Need web UI?
├─ Yes → Do you know React?
│  ├─ Yes → Next.js + Vercel
│  └─ No → SvelteKit or plain HTML
└─ No → Need API?
   ├─ Yes → Do you know Python?
   │  ├─ Yes → FastAPI + SQLite
   │  └─ No → Express + SQLite
   └─ No → Need data processing?
      ├─ Yes → Python + Jupyter
      └─ No → CLI? Python or Go
```

### Stack-Specific Quick Starts

**Next.js + Supabase:**
```bash
npx create-next-app@latest my-app --ts --tailwind
cd my-app
npx supabase init
# Get URL + anon key from supabase.com
# Add to .env.local
```

**FastAPI + SQLite:**
```bash
mkdir my-api && cd my-api
python -m venv venv && source venv/bin/activate
pip install fastapi uvicorn sqlalchemy
# Create main.py with single file app
uvicorn main:app --reload
```

**Expo React Native:**
```bash
npx create-expo-app my-app
cd my-app
npx expo start
# Scan QR with Expo Go app
```

### Anti-patterns

```
DON'T:
- Set up custom CI/CD for throwaway
- Write comprehensive tests first
- Design perfect database schema
- Implement auth before core feature works
- Configure logging/monitoring
- Write documentation during build

DO:
- Use managed services
- Hardcode config values
- Copy working patterns from tutorials
- Ship with console.log
- Use default error handling
```

## Time-boxing and Exit Criteria

### Time-box Rules

```
Hard limits (non-negotiable):
- Spike:        4 hours max
- Prototype:    1 day max
- PoC:          3 days max
- MVP:          2 weeks max
- Hackathon:    Event duration

Extension policy:
- Must define new success criteria
- Must get stakeholder approval
- Maximum one extension
- Extension = half original duration
```

### Exit Criteria Template

```
At [date/time], evaluate:

PASS (graduate):
- Success criteria met
- Path forward clear
- Resources available for production

FAIL (learn and kill):
- Success criteria not met
- No viable path forward
- Opportunity cost too high

PIVOT (new hypothesis):
- Partial success
- New information changes direction
- Different approach needed

INCONCLUSIVE (extend once):
- Need more data
- Close to threshold
- Clear path to resolution
```

### Daily Check-in (for multi-day projects)

```
Morning (5 min):
- What's the goal today?
- What's the biggest risk?
- What's blocking?

Evening (5 min):
- What did we learn?
- Are we on track for success criteria?
- Continue/pivot/kill?
```

## Decision Framework: Keep vs Discard vs Graduate

### Decision Matrix

| Signal | Keep | Discard | Graduate |
|--------|------|---------|----------|
| **Hypothesis validated** | - | - | ✓ |
| **Users want it** | ✓ | - | ✓ |
| **Technically feasible** | ✓ | - | ✓ |
| **Worth investment** | Maybe | - | ✓ |
| **Hypothesis failed** | - | ✓ | - |
| **No user interest** | - | ✓ | - |
| **Technical dead end** | - | ✓ | - |
| **Opportunity cost high** | - | ✓ | - |

### Graduation Process

```
Throwaway → Production requires:

1. Tech debt audit
   - List all shortcuts taken
   - Estimate effort to fix
   - Prioritize: must-fix, should-fix, nice-to-have

Tech debt categories:

| Category | Examples | Fix Effort |
|----------|----------|------------|
| **Architecture** | Monolith, wrong patterns | High |
| **Data** | No migrations, hardcoded schema | Medium |
| **Security** | No auth, hardcoded secrets | High |
| **Testing** | No tests, no CI | Medium |
| **Error handling** | Silent failures, no validation | Low |
| **Performance** | N+1 queries, no caching | Medium |
| **Config** | Hardcoded values, no env vars | Low |
| **Logging** | console.log only | Low |

2. Architecture review
   - What scales? What doesn't?
   - Database: schema migrations needed
   - Security: auth, input validation, secrets
   - Performance: bottlenecks identified

3. Rewrite vs refactor decision
   - If < 20% reusable → rewrite
   - If > 60% reusable → refactor
   - Between → case-by-case

4. Production checklist
   □ Proper error handling
   □ Input validation
   □ Authentication/authorization
   □ Database migrations
   □ Environment config
   □ Logging/monitoring
   □ Tests (unit + integration)
   □ CI/CD pipeline
   □ Documentation
   □ Security review
```

### Archive Process

```
When discarding:

1. Document learnings
   - What worked
   - What didn't
   - What you'd do differently

2. Extract reusable pieces
   - Code snippets
   - Design patterns
   - API knowledge
   - User research data

3. Archive artifacts
   - Push to archive repo/branch
   - Tag with [throwaway-YYYY-MM-DD]
   - Note in team wiki

4. Communicate decision
   - Why killed
   - What learned
   - What's next
```

## Common Pitfalls

1. **Scope creep** — "while we're here, let's add..." kills throwaway projects
2. **Premature optimization** — don't optimize what you'll delete
3. **Attachment to code** — sunk cost fallacy; code is cheap, learning is expensive
4. **No exit criteria** — without kill criteria, zombie projects live forever
5. **Wrong fidelity** — too polished wastes time, too rough doesn't test hypothesis
6. **Missing documentation** — if you don't record learnings, the project was wasted
7. **Team mismatch** — throwaway needs builders, not architects
8. **No time-box** — open-ended throwaway becomes permanent technical debt
9. **Skipping validation** — building without testing hypothesis wastes effort
10. **Graduating too early** — throwaway code isn't production-ready; budget for rewrite

## Quick Reference Templates

### Spike Ticket Template

```markdown
## Spike: [Question]

**Question:** [Specific technical question]
**Time-box:** [X hours/days]
**Owner:** [name]
**Due:** [date]

### Approach
1. [Step 1]
2. [Step 2]
3. [Step 3]

### Success
Answer: [clear yes/no/measurement]

### Output
- [ ] Decision documented
- [ ] Code/proof archived
- [ ] Team notified
```

### Prototype Review Template

```markdown
## Prototype Review: [Name]

**Hypothesis:** [what we tested]
**Duration:** [time spent]
**Fidelity:** [sketch/wireframe/mock/interactive]

### Findings
- [Finding 1]
- [Finding 2]

### User Feedback
- [Feedback 1]
- [Feedback 2]

### Decision
☐ Graduate to production
☐ Pivot (new hypothesis: [X])
☐ Kill (reason: [X])

### Learnings
- [What we'd reuse]
- [What we'd do differently]
```

### MVP Success Tracker

```markdown
## MVP: [Name]

### Hypothesis
We believe [target users] will [behavior] because [reason].

### Metrics
| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| [Metric 1] | [target] | [actual] | ✓/✗ |
| [Metric 2] | [target] | [actual] | ✓/✗ |

### User Feedback
| User | Feedback | Action |
|------|----------|--------|
| [name] | [feedback] | [action] |

### Decision
☐ Scale up
☐ Iterate
☐ Pivot
☐ Kill
```

### Throwaway Project Charter

```markdown
# [Project Name] — Throwaway Charter

## Purpose
[One sentence: why this exists]

## Hypothesis
[What we believe]

## Success Criteria
- Pass: [condition]
- Fail: [condition]

## Time-box
Start: [date]
End: [date]
Duration: [X days]

## Scope
IN: [list]
OUT: [list]

## Team
- [Role]: [Name]

## Tech Stack
- [Choice 1]: [reason]
- [Choice 2]: [reason]

## Decision Point
At [date], evaluate and decide: graduate / pivot / kill
```

### Hackathon Prep Checklist

```
Before event:
- [ ] Team formed (2-4 people ideal)
- [ ] Idea scoped (one-pager written)
- [ ] Tech stack chosen (familiar tools)
- [ ] Dev environments set up
- [ ] Accounts created (hosting, APIs, DB)
- [ ] Design sketches done
- [ ] Presentation template ready

During event:
- [ ] Hour 0-1: Finalize scope, assign tasks
- [ ] Hour 2-8: Build core functionality
- [ ] Hour 8-12: Integrate pieces, fix critical bugs
- [ ] Hour 12-16: Polish demo path only
- [ ] Hour 16-20: Prepare presentation
- [ ] Hour 20-24: Practice demo, backup plan

Presentation structure:
1. Problem (1 min)
2. Demo (2 min)
3. How we built it (1 min)
4. What we learned (1 min)
5. What's next (1 min)
```

### User Testing Script (for prototypes/MVPs)

```markdown
## User Test: [Prototype Name]

### Setup (2 min)
- "We're testing the idea, not you"
- "Think out loud as you go"
- "There are no wrong answers"

### Tasks (10 min)
1. [Primary task] — observe, don't help
2. [Secondary task] — if time allows

### Questions (5 min)
- What was confusing?
- What would make you use this?
- What's missing?
- Would you pay for this? (if applicable)

### Notes
- [Observation 1]
- [Observation 2]
- [Quote 1]
```

### Speed Hacks Cheat Sheet

```
Scaffolding:
- npx create-next-app@latest --ts
- npx create-expo-app
- pip install fastapi uvicorn
- go mod init && go mod tidy

Quick DB:
- SQLite for local (zero config)
- Supabase for hosted (free tier)
- PlanetScale for MySQL (branching)
- Turso for edge SQLite

Quick deploy:
- Vercel: git push, done
- Railway: connect repo, done
- Fly.io: fly launch, done
- Render: connect repo, done

Skip for throwaway:
✗ Custom auth → use Supabase Auth or Clerk
✗ Custom ORM → raw SQL or Prisma
✗ Custom CI → Vercel/GitHub Actions defaults
✗ Custom monitoring → console.log + Vercel Analytics
✗ Custom error handling → try/catch + default page
✗ Custom logging → console.log
✗ Custom validation → TypeScript types + basic checks
```

## Pitfalls

1. **Don't over-plan** — 10 min planning, then build
2. **Don't skip exit criteria** — know when to stop
3. **Don't optimize prematurely** — delete, don't refactor
4. **Don't forget to document learnings** — code dies, knowledge lives
5. **Don't attach to code** — sunk cost is a trap
6. **Don't skip validation** — test hypothesis before building more
7. **Don't use production patterns** — throwaway ≠ production
8. **Don't extend without approval** — time-box is sacred
9. **Don't graduate without tech debt audit** — shortcuts have costs
10. **Don't communicate vaguely** — be clear: this is throwaway, here's why
