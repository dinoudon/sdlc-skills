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

---
name: sdlc-throwaway-projects
description: "Planning throwaway/prototype projects: rapid prototyping, vibe coding workflows, MVP planning, spike solutions, PoC validation, hackathon prep, AI-assisted development, lean canvas, assumption mapping, time-boxing, graduation path (throwaway → production), decision frameworks for keep vs discard vs graduate, tech stack selection for speed, common pitfalls, and integration with 2026 AI coding tools (Cursor, Lovable, Bolt, Replit, v0, Claude Code, Ge
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
| Type | Purpose | Lifespan | Fidelity | End State |
|------|---------|----------|----------|-----------|
| **Spike** | Answer technical question | Hours–days | Low | Knowledge, discard |
| **Prototype** | Explore UX/interaction | Days–weeks | Medium | Feedback, discard |
| **PoC** | Prove feasibility | Days–weeks | Medium | Decision, discard |
| **MVP** | Test market demand | Weeks–months | High | Metrics, iterate or kill |
| **Demo** | Show possibility | Hours–days | Surface-level | Impress, discard |
| **Hackathon** | Build in time-box | 24–48h | Variable | Pitch, maybe continue |
```
Need to answer "can we do X?" → Spike
Need to answer "should we do X?" → Prototype
Need to prove "X works" → PoC
Need to test "will people pay for X?" → MVP
Need to show "X is possible" → Demo
Need to build "something in 48h" → Hackathon project
```
## Step 2: Planning Framework
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
```
## Hypothesis
We believe [target users] have [problem] and will [desired action] if we build [solution].
## Validation Criteria
- [Metric 1]: [target] — proves problem is real
- [Metric 2]: [target] — proves solution works
- [Metric 3]: [target] — proves willingness to pay/engage
## Scope
- [Feature 1]
- [Feature 2]
- [Feature 3]
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
## Step 3: Speed-Optimized Workflow
```
1. Describe, don't code — natural language prompts
2. Iterate fast — prompt → review → adjust → repeat
3. Accept imperfection — "good enough" beats "perfect"
4. Ship and test — get user feedback ASAP
5. Save often — commit after every working change
```
## Step 4: Tech Stack Selection
| Criterion | Weight | Questions |
|-----------|--------|-----------|
| Setup time | HIGH | How fast from zero to running? |
| Disposability | HIGH | Can I throw it away easily? |
| Learning curve | MEDIUM | Do I already know this? |
| Deployment speed | MEDIUM | How fast to get it live? |
| Cost | LOW-MED | Free tier available? |
| Fidelity | LOW | Does it look "real enough"? |
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
```
## Step 5: Time-Boxing
| Project Type | Max Duration | Extension Policy |
|-------------|-------------|-----------------|
| Spike | 2 days | None — answer or abandon |
| Prototype | 1 week | +3 days with justification |
| PoC | 2 weeks | +1 week with stakeholder OK |
| MVP | 4 weeks | +2 weeks with metrics |
| Demo | 2 days | None |
| Hackathon | 48 hours | None |
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

```
## Step 7: Decision Framework
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
## Project Archive: [Name]
[Brief description]
- [Learning 1]
- [Learning 2]
[Reasons with data]
- [Code snippet / pattern]
- [User research]
- [Design assets]
```
## Step 8: Common Pitfalls
```
Symptom: "While I'm at it, let me also add..."
Fix: Stick to one-pager. New ideas go to "future ideas" list.
```
## Step 9: Templates
```markdown
## Spike: [Question]
[Specific technical question to answer]
[2 days max]
1. [Step 1]
2. [Step 2]
- Clear yes/no answer
- Approach documented
- Risks identified
[Fill after spike: answer, recommendation, open questions]
```

### Prototype Review

```markdown
## Prototype Review: [Name]
| Theme | Frequency | Severity | Action |
|-------|-----------|----------|--------|
| [issue] | [how many] | [high/med/low] | [fix/pivot/ignore] |
> "[user quote]"
> "[user quote]"
| Metric | Target | Actual | Pass? |
|--------|--------|--------|-------|
| [metric] | [target] | [actual] | ✓/✗ |
□ Graduate  □ Pivot  □ Kill
[action items]
```

### MVP Tracker

```markdown
## MVP: [Name]
[What we believe]
- [ ] Core feature live
- [ ] 5 users recruited
- [ ] Tracking setup
- [ ] 10 users tried it
- [ ] Feedback collected
- [ ] Iterate on top issue
- [ ] 20+ users
- [ ] Core metric measured
- [ ] Retention check
- [ ] Decision: scale / pivot / kill
- [ ] If scale: graduation plan
- [ ] If kill: archive learnings
```

### Retrospective Template

```markdown
## Retrospective: [Project Name]
- [positive 1]
- [positive 2]
- [improvement 1]
- [improvement 2]
- [learning 1]
- [learning 2]
- Estimated: [X days]
- Actual: [Y days]
- Variance: [+/- Z days]
- Reason: [why]
[Yes/No + why]
- [pattern/template/tool]
```
## Integration with Vibe Coding
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
## Step 10: Validation Methods
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
```
## Step 11: Graduation Deep Dive
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
## Step 12: Case Studies
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
```
## Step 13: Metrics
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
```
## Step 14: Spike Methodology
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
```
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

```
## Step 16: MVP Frameworks
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
```
## Step 17: Throwaway-to-Production Pipeline
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
```
## Step 18: Experimentation Culture
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
```
## Step 19: Risk Management
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
```
## Step 22: Validation Experiments
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

```
## Step 23: Throwaway Project Anti-Patterns
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
```
## Step 24: Throwaway Project Tools
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
```
## Step 25: Post-Mortem Template
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