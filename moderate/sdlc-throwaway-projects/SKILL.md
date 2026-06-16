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
- Use what's fastest to deploy
- Mock everything that's not the core hypothesis
- Accept ugly if functional
- Ship before it's "ready"
```

## Step 5: Time-Boxing

### Hard Limits

| Project Type | Max Duration | Extension Policy |
|-------------|-------------|-----------------|
| Spike | 2 days | None — answer or abandon |
| Prototype | 1 week | +3 days with justification |
| PoC | 2 weeks | +1 week with stakeholder OK |
| MVP | 4 weeks | +2 weeks with metrics |
| Demo | 2 days | None |
| Hackathon | 48 hours | None |

### Daily Check-In

```
Morning (5 min):
1. What's the ONE thing that matters today?
2. What's blocking it?
3. Am I still on scope?

Evening (5 min):
1. Did I hit today's goal?
2. What did I learn?
3. Scope creep check: did I add anything not in plan?
```

### Progress Tracking

```markdown
## Day 1
- [x] Set up project scaffold
- [x] Core feature v1
- [ ] Test with mock data
- Notes: [learnings]

## Day 2
- [ ] Iterate based on testing
- [ ] Add secondary feature
- [ ] Show to first user
```

## Step 6: Quality Gates

### "Good Enough" Criteria

```
For Spike:
- Question answered definitively
- Approach validated or invalidated
- Decision documented

For Prototype:
- Core interaction works
- User can complete primary task
- Feedback collected from 3+ users

For PoC:
- Technical feasibility proven
- Performance acceptable for demo
- Integration points identified

For MVP:
- 10+ real users tried it
- Core metric measured
- Retention signal (or clear lack thereof)
```

### What to Skip in Throwaway Projects

```
SKIP:
- Comprehensive error handling
- Loading states and edge cases
- Accessibility (unless testing with disabled users)
- Performance optimization
- Code documentation
- CI/CD pipeline
- Monitoring and alerting
- Multi-environment setup
- Database migrations
- API versioning

DON'T SKIP:
- Basic input validation (prevents crashes)
- Core user flow (the thing you're testing)
- Data persistence (if testing over multiple days)
- Basic security (don't expose secrets)
```

## Step 7: Decision Framework

### Keep vs Discard vs Graduate

```
After validation, score each dimension (1-5):

IMPACT:      Did users care? (engagement, feedback)
FEASIBILITY: Was it buildable? (technical challenges)
STRATEGIC:   Does it fit our goals? (alignment)
COST:        Can we maintain it? (ongoing effort)

Score:
- 16-20: GRADUATE → production path
- 11-15: PIVOT   → adjust and re-test
- 6-10:  KILL    → archive learnings
- 4-5:   ABORT   → stop immediately
```

### Graduation Checklist

```
When throwaway becomes real:
□ Add proper error handling
□ Write tests for core flows
□ Security review (OWASP basics)
□ Set up CI/CD
□ Add logging and monitoring
□ Document architecture
□ Refactor hot paths
□ Add proper auth
□ Database migrations
□ Performance baseline
□ Code review by another dev
□ Deployment strategy (staging → prod)
```

### Archive Process (when killing)

```markdown
## Project Archive: [Name]

### What We Built
[Brief description]

### What We Learned
- [Learning 1]
- [Learning 2]

### Why We Killed It
[Reasons with data]

### Reusable Assets
- [Code snippet / pattern]
- [User research]
- [Design assets]

### Date: [date]
### Decision makers: [names]
```

## Step 8: Common Pitfalls

### 1. Scope Creep
```
Symptom: "While I'm at it, let me also add..."
Fix: Stick to one-pager. New ideas go to "future ideas" list.
```

### 2. Sunk Cost Fallacy
```
Symptom: "I've spent 2 weeks on this, can't stop now."
Fix: Time-box is sacred. Kill criteria defined upfront.
```

### 3. Premature Optimization
```
Symptom: "Let me refactor this for performance."
Fix: It's throwaway. If it works, ship it.
```

### 4. Wrong Fidelity Level
```
Symptom: Spending days on pixel-perfect UI for a PoC.
Fix: Match fidelity to project type. PoC ≠ production UI.
```

### 5. Not Testing with Real Users
```
Symptom: "I think it works well."
Fix: Show to 3+ target users before deciding. Always.
```

### 6. Over-Engineering Prototypes
```
Symptom: Setting up microservices, event buses, for a prototype.
Fix: Monolith. Single file if possible. Mock everything.
```

### 7. AI Code Blindness
```
Symptom: Accepting all AI output without review.
Fix: At minimum, understand the core logic. Run the code.
```

### 8. Ignoring the Hypothesis
```
Symptom: Building features instead of testing assumptions.
Fix: Every feature maps to an assumption. No assumption = no feature.
```

## Step 9: Templates

### Spike Ticket

```markdown
## Spike: [Question]

### Question
[Specific technical question to answer]

### Time-box
[2 days max]

### Approach
1. [Step 1]
2. [Step 2]

### Success Criteria
- Clear yes/no answer
- Approach documented
- Risks identified

### Outcome
[Fill after spike: answer, recommendation, open questions]
```

### Prototype Review

```markdown
## Prototype Review: [Name]

### Users Tested: [count]
### Date: [date]

### Feedback Summary
| Theme | Frequency | Severity | Action |
|-------|-----------|----------|--------|
| [issue] | [how many] | [high/med/low] | [fix/pivot/ignore] |

### Key Quotes
> "[user quote]"
> "[user quote]"

### Metrics vs Targets
| Metric | Target | Actual | Pass? |
|--------|--------|--------|-------|
| [metric] | [target] | [actual] | ✓/✗ |

### Decision
□ Graduate  □ Pivot  □ Kill

### Next Steps
[action items]
```

### MVP Tracker

```markdown
## MVP: [Name]

### Hypothesis
[What we believe]

### Week 1
- [ ] Core feature live
- [ ] 5 users recruited
- [ ] Tracking setup

### Week 2
- [ ] 10 users tried it
- [ ] Feedback collected
- [ ] Iterate on top issue

### Week 3
- [ ] 20+ users
- [ ] Core metric measured
- [ ] Retention check

### Week 4
- [ ] Decision: scale / pivot / kill
- [ ] If scale: graduation plan
- [ ] If kill: archive learnings
```

### Retrospective Template

```markdown
## Retrospective: [Project Name]

### What Went Well
- [positive 1]
- [positive 2]

### What Could Improve
- [improvement 1]
- [improvement 2]

### Key Learnings
- [learning 1]
- [learning 2]

### Time Spent vs Estimate
- Estimated: [X days]
- Actual: [Y days]
- Variance: [+/- Z days]
- Reason: [why]

### Would We Do It Again?
[Yes/No + why]

### Reusable for Future
- [pattern/template/tool]
```

## Integration with Vibe Coding

### 2026 AI Tools for Throwaway Projects

```
Best for NON-CODERS building prototypes:
→ Lovable: visual editor, auth, full-stack
→ Bolt.new: fast prototyping, generous free tier
→ Replit: all-in-one, zero setup

Best for DEVELOPERS building prototypes:
→ Cursor: multi-file editing, model flexibility
→ Claude Code: terminal agent, deep codebase context
→ v0 by Vercel: UI components (React/Tailwind)

Best for TEAMS:
→ Windsurf: fixed price, strong agent mode
→ GitHub Copilot: works in most IDEs
```

### Prompt Engineering for Prototypes

```
Rule 1: Be specific about scope
  BAD:  "Build me a todo app"
  GOOD: "Build a todo app with add/delete/complete. 
         Use React + localStorage. No auth, no tests."

Rule 2: Describe the constraint
  BAD:  "Make it fast"
  GOOD: "This is a 2-day prototype. Skip error handling."

Rule 3: Iterate in small steps
  BAD:  "Build the entire dashboard"
  GOOD: "Show a table of 5 mock users with name/email/status"

Rule 4: Ask for alternatives
  "Show me 2 approaches for [feature]. I'll pick one."

Rule 5: Use AI for planning too
  "I want to validate [idea]. Help me write a lean canvas 
   and identify the riskiest assumption."
```

### Iteration Loop

```
1. PROMPT   → Describe what you want
2. REVIEW   → Check output (run it!)
3. ADJUST   → "Change X to Y, keep Z"
4. COMMIT   → git commit -m "working feature"
5. REPEAT   → Next feature or refinement

Time per iteration: 2-15 minutes
Iterations per hour: 4-30
```

## Step 10: Validation Methods

### Customer Discovery (Mom Test)

```
RULES:
1. Talk about THEIR life, not your idea
   BAD:  "Would you use an AI meal planner?"
   GOOD: "Tell me about how you plan meals currently."

2. Ask about SPECIFIC past behavior
   BAD:  "Would you pay for this?"
   GOOD: "Have you ever paid for a meal planning service?"

3. Ask about problems, not your solution
   BAD:  "Wouldn't it be great if AI planned your meals?"
   GOOD: "What's the hardest part about meal planning?"

4. Get commitment, not compliments
   BAD:  "That sounds cool!" (compliment)
   GOOD: "Can I sign up now?" (commitment)
```

### Pretotyping Methods

```
Before building anything, test if people WANT it:

1. LANDING PAGE
   - One page, one message, one CTA (email signup)
   - Drive traffic: social media, Reddit, HN
   - 5%+ conversion = strong signal
   - Tools: Carrd.co, v0, Bolt.new

2. FAKE DOOR TEST
   - Add button for feature that doesn't exist
   - Click → "Coming soon — sign up for early access"
   - High click rate = build it
   - Works best in existing products

3. SMOKE TEST
   - Ad campaign for non-existent product ($50-200 budget)
   - Click → landing page → signup
   - CTR > 2%, signup rate > 5% = green light
   - Run for 3-7 days max

4. SOCIAL MEDIA TEST
   - Post describing the product
   - Measure: likes, comments, DMs, signups
   - "Would you use this?" + link to waitlist
```

### User Testing Script

```
SETUP (2 min):
"Hi [name], thanks for trying this out.
I'm testing the product, not testing you.
Please think out loud as you use it."

TASKS (10-15 min):
1. "[Task instruction]"
   - Observe: what they do, where they struggle
   - Ask: "What are you thinking?"

2. "[Task instruction]"
   - Observe: completion rate
   - Ask: "Is this what you expected?"

QUESTIONS (5 min):
- "What was confusing?"
- "What would you change?"
- "Would you use this? Why/why not?"
- "Would you pay for this? How much?"
- "Who else should I talk to?"

RULES:
- Don't explain — let them figure it out
- Silence is OK — don't fill it
- Record sessions (with permission)
- 5 users find 80% of usability issues
```

## Step 11: Graduation Deep Dive

### When Throwaway Becomes Real

```
GRADUATION TRIGGERS (need 2+):
  ✓ Users actively asking "when can I use this?"
  ✓ At least 1 user willing to pay
  ✓ Core metric shows traction
  ✓ Stakeholders want to invest
  ✓ Technical feasibility proven

ANTI-TRIGGERS (none of these are valid reasons):
  ✗ "We already built it" (sunk cost)
  ✗ "It's almost done" (famous last words)
  ✗ "Users said it was cool" (compliment ≠ commitment)
  ✗ "I think it could work" (not validated)
```

### Graduation Checklist

```markdown
## Phase 1: Foundation (Week 1-2)
- [ ] Code review by another developer
- [ ] Add proper error handling
- [ ] Add input validation
- [ ] Security review (OWASP basics)
  - [ ] No secrets in code
  - [ ] SQL injection prevention
  - [ ] XSS prevention
  - [ ] Proper auth
- [ ] Set up proper database
- [ ] Environment variables

## Phase 2: Quality (Week 3-4)
- [ ] Tests for core flows (unit + integration)
- [ ] CI/CD pipeline (lint, test, deploy)
- [ ] Documentation (README, API docs)
- [ ] Code documentation

## Phase 3: Operations (Week 5-6)
- [ ] Logging and monitoring
- [ ] Staging + production environments
- [ ] Performance baseline
- [ ] Rollback plan

## Phase 4: Launch (Week 7-8)
- [ ] User documentation
- [ ] Onboarding flow
- [ ] Support process
- [ ] Analytics setup
```

### Refactoring Strategy

```
PRIORITY ORDER:
1. Security issues (fix immediately)
2. Data integrity (fix before launch)
3. Core logic (refactor for maintainability)
4. Error handling (add before scaling)
5. Performance (optimize when needed)
6. Code style (last priority)

RULES:
- Refactor in small, testable increments
- Write tests BEFORE refactoring
- One refactor per commit
- Verify behavior doesn't change
- Don't refactor what you'll delete soon
```

## Step 12: Case Studies

### Case Study 1: Weekend Prototype → Real Product

```
CONTEXT: Solo developer, idea for task management app
TIMELINE: 3 days

Day 1 (8h):
- Used Lovable to scaffold app
- Core feature: drag-and-drop task board
- Prompt: "Create a Kanban board with 3 columns. 
  Tasks can be dragged between columns. Use localStorage."
- Result: Working prototype in 4 hours
- Showed to 5 friends, collected feedback

Day 2 (6h):
- Iterated on feedback: added due dates, labels
- Used Lovable prompts for each feature
- Committed after each working feature
- Created landing page with v0

Day 3 (4h):
- Shared landing page on Reddit (r/productivity)
- 50 signups in 24 hours
- 3 users willing to pay $5/mo
- DECISION: Graduate

GRADUATION (4 weeks):
- Moved from Lovable to Cursor + Next.js
- Added proper auth (Clerk)
- Added database (Supabase)
- Added tests (Playwright)
- Launched on Product Hunt
- Result: 200 users, $500 MRR

KEY LEARNINGS:
- Lovable was perfect for validation speed
- Graduation path took 4x longer than prototype
- User feedback shaped the product more than the plan
- Landing page test saved months of building wrong thing
```

### Case Study 2: Spike → Kill Decision

```
CONTEXT: Team of 3, exploring real-time collaboration feature
TIMELINE: 2 days

Day 1 (6h):
- Spike question: "Can we use WebRTC for real-time sync?"
- Used Cursor + Yjs library
- Tested with 2 browsers, basic sync worked
- BUT: conflict resolution was complex

Day 2 (6h):
- Deep dive on conflict resolution
- Tested with 5+ concurrent users
- Result: Works for simple cases, breaks for complex
- Performance: 200ms latency (acceptable)
- BUT: 3 weeks estimated to production-ready

DECISION: Kill
- Reason: 3 weeks effort for feature users hadn't asked for
- Alternative: simpler polling-based sync (2 days)
- Archived: WebRTC learnings, Yjs patterns
- Reused: polling approach in production (2 days vs 3 weeks)

KEY LEARNINGS:
- Spike saved 3 weeks of wasted development
- Technical feasibility ≠ business justification
- Simpler alternative existed
- Spike output: clear recommendation with data
```

### Case Study 3: Wizard of Oz → MVP

```
CONTEXT: AI-powered recipe recommendation app
TIMELINE: 2 weeks

Week 1: Wizard of Oz
- Built frontend with Lovable (photo upload → recipe suggestions)
- Backend: human (developer) manually analyzed photos and wrote recipes
- 10 test users, all believed it was automated
- Result: 8/10 users said "this is amazing"
- Key learning: users cared about speed, not AI accuracy

Week 2: Decision + Transition
- Manual process took 5 min per user (too slow to scale)
- BUT: user demand was strong
- DECISION: Build real AI backend
- Used Claude API for recipe generation
- Launched MVP to same 10 users + 10 new

RESULT:
- 15/20 users completed onboarding
- 8/20 used it 3+ times in first week
- 3/20 willing to pay $4.99/mo
- DECISION: Graduate

KEY LEARNINGS:
- Wizard of Oz proved demand before building AI
- Manual process defined the product spec perfectly
- Users valued speed over accuracy (unexpected)
- Cost of manual process = cost of AI API (roughly)
```

## Step 13: Metrics

### Validation Metrics by Project Type

```
SPIKE:
- Question answered: yes/no
- Time spent vs estimate
- Recommendation clarity

PROTOTYPE:
- Task completion rate (% of users who complete primary task)
- Time to complete (seconds)
- Error rate (mistakes per session)
- User satisfaction (1-5 scale)
- Net Promoter Score (would you recommend?)

MVP:
- Activation rate (% who complete onboarding)
- Retention (D1, D7, D30)
- Core action frequency (actions per user per day)
- Conversion rate (free → paid)
- Customer Acquisition Cost (CAC)
- Willingness to pay (% and amount)

LANDING PAGE:
- Visit → signup conversion rate
- Signup → waitlist conversion
- Traffic source breakdown
- Cost per signup (if paid traffic)

SMOKE TEST:
- Ad click-through rate (CTR)
- Landing page conversion
- Cost per signup
- Signup quality (engagement with follow-up)
```

### Pivot/Kill Criteria

```
PIVOT when:
- Users have the problem but don't like the solution
- Some users love it, most don't (niche opportunity)
- Technical approach doesn't work, but problem is real
- Different segment shows more promise

KILL when:
- Users don't have the problem (or don't know they do)
- Users won't pay (and no viable free model)
- Technical feasibility is proven impossible
- Market is too small or too competitive
- Better alternative exists and is entrenched

METRICS THRESHOLDS (rough guide):
- Activation < 20% → pivot or kill
- D7 retention < 10% → pivot or kill
- Willingness to pay < 5% → pivot or kill
- Task completion < 30% → pivot (UX issue)
- NPS < 0 → pivot or kill
```

## Speed Hacks Cheat Sheet

```
1. AI for EVERYTHING
   - Planning: "Help me write a one-pager for [idea]"
   - Scaffolding: "Create a [type] app with [features]"
   - Debugging: "Fix this error: [paste]"
   - Iteration: "Change X to Y"

2. Mock everything
   - Hardcode data instead of APIs
   - Use localStorage instead of databases
   - Skip auth (or simple password)
   - Use placeholder images

3. Skip what doesn't matter
   - No tests (for throwaway)
   - No CI/CD
   - No error handling (basic try/catch only)
   - No loading states

4. Use opinionated tools
   - Lovable/Bolt: pre-configured stack
   - Replit: zero setup
   - v0: pre-built components

5. Commit frequently
   - Every working feature = commit
   - Easy rollback if breaks
   - Never lose working state

6. Time-box ruthlessly
   - Set timer for each feature
   - Stuck > 30 min → simplify or skip
   - "Done" > "perfect"

7. Test with users early
   - Day 1: show wireframe
   - Day 2: show prototype
   - Day 3: show working feature
```

## Step 14: Spike Methodology

### Spike Types

```
Technical spike:
  Question: "Can we use [technology] for [purpose]?"
  Duration: 1-3 days
  Output: Technical feasibility report
  Example: "Can we use WebAssembly for real-time video processing?"

Research spike:
  Question: "What is the best approach for [problem]?"
  Duration: 2-5 days
  Output: Comparison matrix with recommendation
  Example: "What is the best auth provider for our use case?"

Integration spike:
  Question: "How does [system A] connect to [system B]?"
  Duration: 1-2 days
  Output: Working integration prototype + gotchas doc
  Example: "How does Stripe Connect handle marketplace payments?"

Performance spike:
  Question: "Can we meet [performance requirement]?"
  Duration: 2-3 days
  Output: Benchmarks + bottlenecks + optimization path
  Example: "Can we process 10K events/second with this architecture?"
```

### Spike Report Template

```
# Spike: [Question]

## Context
- Why are we investigating this?
- What decision depends on this?

## Approach
- What did we try?
- What tools/libraries did we evaluate?

## Findings
- [Finding 1 with evidence]
- [Finding 2 with evidence]
- [Finding 3 with evidence]

## Recommendation
- [Clear recommendation with reasoning]
- [Trade-offs acknowledged]

## Proof
- [Link to code/repo]
- [Screenshots/benchmarks]
- [Working demo if applicable]

## Follow-up
- [ ] [Next step 1]
- [ ] [Next step 2]
```

## Step 15: Prototype Patterns

### UI Prototype

```
Goal: Validate user experience before building backend

Tools:
  - Figma: High-fidelity mockups
  - Framer: Interactive prototypes
  - HTML/CSS: Clickable prototype
  - React + static data: Functional prototype

Approach:
  1. Sketch key screens (paper or digital)
  2. Build clickable flow (3-5 screens)
  3. Add realistic data (not lorem ipsum)
  4. Test with 3-5 users
  5. Iterate based on feedback

Time: 1-3 days
Output: Clickable prototype + user feedback
```

### Data Prototype

```
Goal: Validate data model and queries before building app

Tools:
  - Jupyter notebook: Data exploration
  - SQL: Schema design and query testing
  - Python + pandas: Data transformation
  - Sample dataset: Realistic test data

Approach:
  1. Define key entities and relationships
  2. Create sample dataset (100-1000 records)
  3. Write key queries (top 5 use cases)
  4. Test performance (indexes, joins)
  5. Validate data model supports requirements

Time: 1-2 days
Output: Schema + sample queries + performance notes
```

### API Prototype

```
Goal: Validate API design before implementation

Tools:
  - OpenAPI spec: API contract
  - Mock server: Auto-generated from spec
  - Postman: API testing
  - Swagger UI: Interactive documentation

Approach:
  1. Define API contract (OpenAPI/Swagger)
  2. Generate mock server
  3. Build client against mock
  4. Test with frontend team
  5. Iterate on contract

Time: 1-2 days
Output: API spec + mock server + client feedback
```

## Step 16: MVP Frameworks

### Concierge MVP

```
Definition: Deliver value manually before building automation

Example: 
  - Instead of building recommendation engine, manually curate
  - Instead of building chatbot, have humans respond
  - Instead of building matching algorithm, match manually

Benefits:
  - Validate demand without engineering
  - Learn user preferences firsthand
  - Iterate faster (change manual process)
  - Build empathy for the problem

Time: 1-2 weeks
Cost: Human time only
Output: Validated demand + user insights
```

### Wizard of Oz MVP

```
Definition: Appear automated to user, but operate manually behind scenes

Example:
  - User sees AI-powered analysis, human actually does it
  - User sees automated matching, human actually matches
  - User sees real-time processing, batch processing behind scenes

Benefits:
  - Test user experience without building backend
  - Validate willingness to pay
  - Learn edge cases before automating

Time: 2-4 weeks
Cost: Human time + minimal frontend
Output: Validated UX + willingness to pay
```

### Single-Feature MVP

```
Definition: Build ONE feature exceptionally well

Example:
  - Dropbox: File sync (nothing else)
  - Instagram: Photo filter + share (nothing else)
  - Twitter: 140-character posts (nothing else)

Benefits:
  - Faster to market
  - Clear value proposition
  - Easier to iterate
  - Lower maintenance burden

Time: 2-4 weeks
Cost: Minimal engineering
Output: Working feature + user feedback
```

## Step 17: Throwaway-to-Production Pipeline

### Graduation Criteria

```
Technical criteria:
  - Core functionality works end-to-end
  - Performance meets requirements (response time, throughput)
  - Security basics implemented (auth, input validation)
  - Error handling covers known failure modes
  - Data persistence is reliable
  - API contract is stable

Business criteria:
  - User feedback is positive (>80% satisfaction)
  - Key metrics show promise (engagement, retention)
  - Willingness to pay validated (if applicable)
  - Competitive advantage identified
  - Scale requirements understood
  - Go/no-go decision documented

Process criteria:
  - Code reviewed by at least one engineer
  - Test coverage for critical paths (>60%)
  - Documentation exists (README, API docs)
  - Deployment process documented
  - Monitoring and alerting basic setup
  - Rollback plan exists
```

### Refactoring Playbook

```
Phase 1: Assessment (1-2 days)
  - Code review (identify technical debt)
  - Architecture review (identify structural issues)
  - Test coverage analysis
  - Performance profiling
  - Security audit

Phase 2: Foundation (1-2 weeks)
  - Set up proper project structure
  - Add comprehensive tests
  - Implement CI/CD pipeline
  - Set up monitoring and logging
  - Document architecture decisions

Phase 3: Refactoring (2-4 weeks)
  - Extract reusable components
  - Implement proper error handling
  - Add input validation and sanitization
  - Optimize database queries
  - Implement caching strategy

Phase 4: Hardening (1-2 weeks)
  - Security penetration testing
  - Load testing
  - Chaos engineering (failure injection)
  - Documentation completion
  - Runbook creation

Phase 5: Launch (1 week)
  - Staged rollout (1% then 10% then 100%)
  - Monitoring dashboards
  - Alert configuration
  - On-call rotation setup
  - Post-launch review
```

## Step 18: Experimentation Culture

### Experiment Design

```
Hypothesis format:
  "If we [change], then [metric] will [direction] by [amount], 
  because [reasoning]."

Example:
  "If we add a free tier, then signups will increase by 50%, 
  because developers can try before committing budget."

Variables:
  Independent: What we change (free tier)
  Dependent: What we measure (signups)
  Controlled: What stays the same (product, marketing)

Sample size:
  Baseline: Current signups/month
  MDE: Minimum detectable effect (50% increase)
  Significance: 95% confidence
  Power: 80%
  
  Use calculator: Evan Miller A/B test calculator

Duration:
  Minimum: 2 full business cycles (2 weeks)
  Maximum: 6 weeks (diminishing returns)
  Consider: Seasonality, day-of-week effects
```

### Experiment Tracking

```
Experiment log:
  | ID | Hypothesis | Start | End | Result | Learning |
  |----|------------|-------|-----|--------|----------|
  | EXP-001 | Free tier increases signups | Jan 1 | Jan 15 | +62% | Strong signal |
  | EXP-002 | Shorter onboarding improves activation | Jan 15 | Jan 29 | +18% | Moderate signal |
  | EXP-003 | Social proof increases conversion | Feb 1 | Feb 14 | -3% | No effect |

Review cadence:
  - Daily: Monitor for anomalies
  - Weekly: Review running experiments
  - Bi-weekly: Plan next experiments
  - Monthly: Retrospective on experiment velocity
```

## Step 19: Risk Management

### Risk Register

```
| Risk | Probability | Impact | Mitigation | Owner |
|------|-------------|--------|------------|-------|
| Tech does not work | Medium | High | Spike first, validate early | Tech lead |
| No user demand | Medium | High | User interviews, concierge MVP | Product |
| Scope creep | High | Medium | Strict timeboxing, daily standups | PM |
| Key person leaves | Low | High | Document decisions, pair programming | Team |
| Integration fails | Medium | Medium | Spike integrations, have fallbacks | Tech lead |

Risk review:
  - Daily: Check risk indicators
  - Weekly: Review risk register
  - At milestones: Reassess all risks
  - At completion: Final risk assessment
```

## Step 20: Communication Templates

### Project Kickoff Email

```
Subject: [Project Name] Kickoff

Team,

We are starting a throwaway project to validate [hypothesis].

Goal: [One sentence goal]
Timeline: [X days/weeks]
Decision: [What we will decide at the end]

Team:
  - [Name]: Role
  - [Name]: Role

Key dates:
  - Kickoff: [Date]
  - Daily standup: [Time] (15 min)
  - Demo: [Date]
  - Decision: [Date]

Repo: [Link]
Docs: [Link]
Slack: [Channel]

Let us move fast and learn.
```

### Decision Email

```
Subject: [Project Name] Decision

Team,

Based on our throwaway project, here is the decision:

Decision: [GO / NO-GO / PIVOT]

Evidence:
  - [Key finding 1]
  - [Key finding 2]
  - [Key finding 3]

Metrics:
  - [Metric 1]: [Result]
  - [Metric 2]: [Result]

Next steps:
  - [If GO]: [What happens next]
  - [If NO-GO]: [What we learned, what we will try instead]
  - [If PIVOT]: [What we are changing and why]

Lessons learned:
  - [Lesson 1]
  - [Lesson 2]

Repo: [Link] (archived)
Report: [Link]
```

## Step 21: Speed Hacks Cheat Sheet

```
SETUP:
  - Use templates (create-next-app, create-react-app)
  - Use managed services (Supabase, PlanetScale, Vercel)
  - Use component libraries (shadcn/ui, Tailwind UI)
  - Copy-paste from previous projects

DEVELOPMENT:
  - Build vertical slice first (one feature end-to-end)
  - Use hardcoded data before database
  - Use console.log before proper logging
  - Skip authentication initially (add later)
  - Use placeholder UI (canned responses)

TESTING:
  - Manual testing for throwaways
  - Automated tests only for critical paths
  - Smoke tests for deployment
  - User testing over unit testing

DEPLOYMENT:
  - One-click deploy (Vercel, Railway, Render)
  - Managed database (Supabase, PlanetScale)
  - Environment variables in platform
  - No custom CI/CD initially

DOCUMENTATION:
  - README with setup instructions
  - Decision log (why, not what)
  - Screenshot/video of working demo
  - Lessons learned document
```


## Step 22: Validation Experiments

### Landing Page Test

```
Goal: Validate demand before building product

Setup:
  - Create landing page (Carrd, Webflow, Framer)
  - Value proposition (headline, subheadline, CTA)
  - Email capture (signup form)
  - Analytics (Plausible, PostHog)

Traffic sources:
  - Reddit (relevant subreddits)
  - Hacker News (Show HN)
  - Twitter/X (tech community)
  - LinkedIn (target audience)
  - Google Ads (search intent)

Metrics:
  - Visitors: Total unique visitors
  - Conversion: Visitors who signed up
  - Cost per signup: Ad spend / signups
  - Qualitative: Feedback from comments, emails

Decision criteria:
  - >5% conversion: Strong signal (build it)
  - 2-5% conversion: Moderate signal (iterate on messaging)
  - <2% conversion: Weak signal (reconsider or pivot)

Time: 1-2 weeks
Cost: $0-500 (ads optional)
```

### Smoke Test

```
Goal: Test willingness to pay before building

Setup:
  - Landing page with pricing
  - Payment button (Stripe checkout)
  - "Coming soon" page after payment
  - Refund immediately + email explanation

Metrics:
  - Click-through rate on pricing
  - Payment attempts
  - Successful payments
  - Refund requests

Decision criteria:
  - >10% click pricing: Strong interest
  - >2% attempt payment: Strong signal
  - Any successful payment: Build it immediately

Ethics:
  - Be transparent ("We are testing demand")
  - Refund immediately
  - Offer early access when you build
  - Do not charge without delivering value
```

## Step 23: Throwaway Project Anti-Patterns

### Common Mistakes

```
Premature optimization:
  Problem: Building for scale before validating demand
  Solution: Build for 10 users, not 10 million
  
  Signs:
    - Choosing databases for performance
    - Implementing caching before measuring
    - Designing for multi-region before launch
    - Building admin dashboards before users

Gold plating:
  Problem: Adding features beyond the core hypothesis
  Solution: Ruthlessly cut scope
  
  Signs:
    - "While we are at it, let us add..."
    - Building settings/preferences pages
    - Implementing edge case handling
    - Perfecting error messages

Analysis paralysis:
  Problem: Researching instead of building
  Solution: Set a research deadline, then build
  
  Signs:
    - Reading more than 3 blog posts on a topic
    - Comparing more than 5 tools/libraries
    - Creating detailed specs before prototyping
    - Waiting for "perfect" information

Sunk cost fallacy:
  Problem: Continuing a failing project because of time invested
  Solution: Define exit criteria upfront, honor them
  
  Signs:
    - "We have already spent 2 weeks on this"
    - Ignoring negative user feedback
    - Adding features to fix fundamental problems
    - Extending timeline repeatedly
```

## Step 24: Throwaway Project Tools

### Tool Selection Matrix

```
Frontend prototyping:
  Speed-first: HTML + Tailwind CDN + Alpine.js
  React-first: Next.js + shadcn/ui + Tailwind
  No-code: Framer, Webflow, Carrd
  
Backend prototyping:
  Speed-first: Supabase (Auth + DB + API)
  Node-first: Express + Prisma + PostgreSQL
  Python-first: FastAPI + SQLAlchemy + PostgreSQL
  Serverless: AWS Lambda + DynamoDB

Database:
  Speed-first: Supabase (PostgreSQL hosted)
  Local-first: SQLite + Prisma
  Production-ready: PlanetScale (MySQL) or Neon (PostgreSQL)

Deployment:
  Speed-first: Vercel (frontend) + Railway (backend)
  All-in-one: Render (web + DB + workers)
  Self-hosted: Docker + DigitalOcean

Auth:
  Speed-first: Supabase Auth
  Flexible: Auth0, Clerk, Lucia
  Self-hosted: NextAuth.js, Passport.js
```

## Step 25: Post-Mortem Template

### Throwaway Project Retrospective

```
# Retrospective: [Project Name]

## Hypothesis
[What we were testing]

## Timeline
- Start: [Date]
- End: [Date]
- Duration: [X days/weeks]

## Decision
[GO / NO-GO / PIVOT]

## What Worked
- [Thing 1]
- [Thing 2]

## What Did Not Work
- [Thing 1]
- [Thing 2]

## Key Learnings
- [Learning 1]
- [Learning 2]

## Surprises
- [Unexpected finding 1]
- [Unexpected finding 2]

## Next Steps
- [If GO]: [What we will build]
- [If NO-GO]: [What we will try instead]
- [If PIVOT]: [What we will change]

## Archive
- Repo: [Link] (archived)
- Demo: [Link] (if still running)
- Artifacts: [Screenshots, videos, data]

## Related Skills

  - [sdlc-prd-to-production](sdlc-prd-to-production): End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monito
  - [sdlc-product-growth](sdlc-product-growth): Product-led growth (PLG), developer-led growth, growth loops, activation funnels, A/B testing, SaaS 
  - [sdlc-architecture-design](sdlc-architecture-design): System design, C4 diagrams, API design, database schema, code architecture, ADRs, branching, depende


## Step 26: Technical Debt from Throwaways

### Managing Throwaway Code

```
When throwaway becomes production:
  - Identify technical debt immediately
  - Document shortcuts taken
  - Prioritize refactoring items
  - Set timeline for cleanup

Technical debt categories:
  Architecture debt:
    - Monolithic structure (need decomposition)
    - Tight coupling (need abstraction)
    - Missing patterns (need proper design)
  
  Code debt:
    - No tests (need test coverage)
    - Copy-paste code (need DRY refactoring)
    - Hardcoded values (need configuration)
    - Poor error handling (need resilience)
  
  Infrastructure debt:
    - Manual deployment (need CI/CD)
    - No monitoring (need observability)
    - No logging (need structured logging)
    - Single point of failure (need redundancy)

Debt tracking:
  - Create tickets for each debt item
  - Estimate effort (hours/days)
  - Prioritize by risk and impact
  - Schedule 20% of sprint capacity for debt
```

### Code Review for Throwaways

```
Review focus areas:
  - Security basics (no hardcoded secrets, input validation)
  - Data handling (proper encryption, access controls)
  - Error handling (graceful failures, user-friendly messages)
  - Performance (no obvious bottlenecks)
  - Maintainability (clear structure, comments for complex logic)

Review checklist:
  □ No secrets in code
  □ Input validation present
  □ Error handling implemented
  □ Database queries parameterized
  □ Authentication required for sensitive endpoints
  □ Rate limiting on public APIs
  □ Logging for debugging
  □ README with setup instructions
```

## Step 27: Throwaway Project Metrics

### Success Metrics

```
Speed metrics:
  - Time to first working prototype
  - Time to user feedback
  - Time to decision (go/no-go)
  - Time to production (if graduating)

Learning metrics:
  - Hypotheses tested
  - Assumptions validated/invalidated
  - User interviews conducted
  - Experiments run

Quality metrics:
  - User satisfaction (if tested with users)
  - Technical feasibility confirmed
  - Business viability assessed
  - Risk reduction achieved

Efficiency metrics:
  - Cost per experiment
  - Developer hours per hypothesis
  - Iterations before decision
  - Resources consumed vs budget
```

## Step 28: Throwaway Project Library

### Reusable Components

```
Boilerplate templates:
  - Next.js + Supabase (full-stack web)
  - Express + PostgreSQL (API backend)
  - FastAPI + SQLAlchemy (Python API)
  - React Native + Expo (mobile)

UI component libraries:
  - shadcn/ui (copy-paste components)
  - Tailwind UI (pre-built layouts)
  - Radix UI (accessible primitives)
  - Headless UI (unstyled components)

Backend services:
  - Supabase (auth, database, storage)
  - PlanetScale (serverless MySQL)
  - Upstash (serverless Redis)
  - Resend (email API)

Deployment templates:
  - Vercel (frontend + serverless)
  - Railway (full-stack)
  - Fly.io (containers)
  - Render (web services + databases)

Analytics:
  - PostHog (product analytics)
  - Plausible (privacy-friendly web analytics)
  - Mixpanel (event tracking)
  - Amplitude (user analytics)
```

## Step 29: Throwaway vs Prototype vs MVP

### Definitions

```
Throwaway:
  - Purpose: Answer a specific question
  - Duration: Hours to days
  - Quality: Lowest (functional only)
  - Audience: Internal team
  - Outcome: Decision (yes/no/pivot)
  - Example: "Can we process video in the browser?"

Prototype:
  - Purpose: Validate design or experience
  - Duration: Days to weeks
  - Quality: Medium (looks real, limited backend)
  - Audience: Users (for testing)
  - Outcome: User feedback
  - Example: "Is this checkout flow intuitive?"

MVP:
  - Purpose: Validate product-market fit
  - Duration: Weeks to months
  - Quality: Production-ready (minimal features)
  - Audience: Real users (paying customers)
  - Outcome: Market validation
  - Example: "Will people pay for this?"

Decision framework:
  - Unknown technology? → Throwaway (spike)
  - Unknown UX? → Prototype
  - Unknown demand? → MVP
  - Unknown business model? → MVP with pricing test


## Step 30: Throwaway Project Checklist

### Pre-Project Checklist

```
□ Hypothesis clearly defined
□ Success criteria established
□ Time limit set (hours/days)
□ Scope limited to core question
□ Exit criteria defined
□ Team aligned on approach
□ Tools selected (fastest path)
□ Repository created
□ README with hypothesis
```

### During Project Checklist

```
□ Working on core hypothesis only
□ No feature creep
□ Time tracking active
□ Daily standup (if multi-day)
□ Documenting learnings
□ Testing with real data/users
□ Not optimizing prematurely
□ Staying within time limit
```

### Post-Project Checklist

```
□ Decision made (go/no-go/pivot)
□ Learnings documented
□ Code archived (if keeping)
□ Team retrospective done
□ Next steps defined
□ Stakeholders informed
□ Metrics recorded
□ Artifacts saved (screenshots, data)
```

## Step 31: Throwaway Project Communication

### Stakeholder Updates

```
Daily update template:
  Today: [What we did]
  Learnings: [What we discovered]
  Blockers: [What is blocking us]
  Tomorrow: [What we will do next]

Decision update template:
  Hypothesis: [What we tested]
  Result: [What we found]
  Evidence: [Data, feedback, metrics]
  Decision: [Go/No-go/Pivot]
  Next steps: [What happens now]
```

### Documentation Template

```
# Throwaway: [Project Name]

## Hypothesis
[What we are testing]

## Approach
[How we are testing it]

## Timeline
- Start: [Date]
- End: [Date]
- Duration: [X days]

## Results
[What we found]

## Decision
[Go / No-go / Pivot]

## Learnings
- [Learning 1]
- [Learning 2]

## Next Steps
[What happens next]

## Artifacts
- [Screenshots, videos, data]
- [Link to archived code]


## Step 32: Throwaway Project Resources

### Learning Resources

```
Books:
  - The Lean Startup (Eric Ries)
  - Sprint (Jake Knapp)
  - Inspired (Marty Cagan)
  - Continuous Discovery Habits (Teresa Torres)

Courses:
  - Y Combinator Startup School (free)
  -IDEO Design Thinking (online)
  - Google Design Sprint (online)

Tools:
  - Miro (collaboration)
  - Figma (design)
  - Notion (documentation)
  - Loom (video updates)
