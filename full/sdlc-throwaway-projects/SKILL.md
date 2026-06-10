---
name: sdlc-throwaway-projects
description: "Planning throwaway/prototype projects: rapid prototyping, vibe coding workflows, MVP planning, spike solutions, PoC validation, hackathon prep, AI-assisted development, lean canvas, assumption mapping, time-boxing, graduation path (throwaway → production), decision frameworks for keep vs discard vs graduate, tech stack selection for speed, common pitfalls, and integration with 2026 AI coding tools (Cursor, Lovable, Bolt, Replit, v0, Claude Code, Gemini CLI). Includes pretotyping, Wizard-of-Oz, concierge MVP, landing page MVP, fake door tests, smoke tests, Mom Test, customer development, lean startup validation, design sprints, trystorming, disposable code patterns, assumption tracking, risk-first ordering, time-boxed sprints, progress tracking, graduation checklists, tech debt management, and comprehensive templates."
version: 1.0.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, throwaway, prototype, mvp, spike, poc, vibe-coding, rapid-prototyping, hackathon, proof-of-concept, ai-assisted, lean-startup, validation, experiment, design-sprint, pretotyping, wizard-of-oz, concierge-mvp, landing-page-mvp, paper-prototype, fake-door, smoke-test, mom-test, customer-development, rapid-iteration, disposable-code, trystorming, lean-canvas, assumption-mapping, risk-first, time-boxing, graduation-path, scope-creep, sunk-cost, ai-coding-tools, cursor, lovable, bolt, replit, v0, claude-code, gemini-cli, copilot, prompt-engineering, prompt-iteration]
    related_skills: [sdlc-spike, sdlc-prd-to-production, sdlc-architecture-design, sdlc-testing-qa, sdlc-requirements-engineering]
---

# Throwaway Projects

Comprehensive guide to planning, executing, and deciding the fate of throwaway/prototype projects. Covers the full lifecycle from idea validation through graduation to production or graceful kill.

## When to Use

Trigger when user:
- Needs to validate an idea quickly
- Mentions prototype, MVP, spike, PoC, demo, hackathon
- Wants to explore a new tech stack
- Needs proof-of-concept for stakeholder buy-off
- Asks "should we build this?"
- Wants to learn a technology by building something real
- Preparing for hackathon or demo day
- Needs to test market demand before investing
- Wants to compare technical approaches
- Has a "weekend project" idea
- Mentions vibe coding or AI-assisted prototyping

### When NOT to Use

```
DON'T use throwaway approach when:
- Building safety-critical systems (medical, aviation, financial)
- Requirements are already validated and well-understood
- The project has compliance/regulatory requirements
- You need production-grade code from day one
- The cost of rewriting exceeds the cost of doing it right first
- Team has no capacity for the graduation path if successful
```

## Step 1: Throwaway Project Taxonomy

### Complete Taxonomy

| Type | Purpose | Lifespan | Fidelity | End State | Example |
|------|---------|----------|----------|-----------|---------|
| **Spike** | Answer technical question | Hours–2 days | Low | Knowledge, discard | "Can we use WebRTC for this?" |
| **Prototype** | Explore UX/interaction | Days–2 weeks | Medium | Feedback, discard | "How would users navigate this flow?" |
| **PoC** | Prove technical feasibility | 1–2 weeks | Medium | Decision, discard | "Can we process 10k events/sec?" |
| **MVP** | Test market demand | 2–4 weeks | High | Metrics, iterate or kill | "Will people pay for this?" |
| **Demo** | Show possibility to stakeholders | Hours–2 days | Surface | Impress, maybe continue | "Show leadership what's possible" |
| **Hackathon** | Build in extreme time-box | 24–48h | Variable | Pitch, maybe continue | "Build something cool this weekend" |
| **Pretotype** | Test if people WANT it | Days | Very low | Interest signal | "Would you sign up for this?" |
| **Wizard of Oz** | Fake automation with humans | 1–2 weeks | Medium | Demand signal | "Manual backend, automated frontend" |
| **Concierge MVP** | Manual service delivery | 2–4 weeks | Medium | Service validation | "Deliver the value manually first" |
| **Landing Page MVP** | Test signup intent | Days | Low | Conversion rate | "Landing page + waitlist" |
| **Paper Prototype** | Test UI concepts | Hours | Very low | Design feedback | "Paper sketches of the app" |
| **Fake Door Test** | Test feature demand | Days | Low | Click-through rate | "Button that says 'coming soon'" |
| **Smoke Test** | Test market signal | Days–1 week | Low | Demand metrics | "Ad campaign for non-existent product" |

### Decision Tree: Which Type to Use

```
START
│
├─ Do you have a technical question?
│  └─ YES → SPIKE (1-2 days)
│
├─ Do you need to test UX/interaction?
│  └─ YES → PAPER PROTOTYPE (hours) or PROTOTYPE (days)
│
├─ Do you need to prove feasibility?
│  └─ YES → PoC (1-2 weeks)
│
├─ Do you need to test if people WANT it?
│  ├─ Haven't talked to users → PRETOTYPE or LANDING PAGE
│  ├─ Can fake the automation → WIZARD OF OZ
│  ├─ Can deliver manually → CONCIERGE MVP
│  └─ Need real usage data → MVP
│
├─ Do you need to impress stakeholders?
│  └─ YES → DEMO (1-2 days)
│
└─ Is it a time-boxed event?
   └─ YES → HACKATHON (24-48h)
```

### Detailed Type Descriptions

#### Spike
```
Purpose: Answer a specific technical question
Duration: 4 hours to 2 days (HARD limit)
Output: Written answer + recommendation
Example questions:
  - "Can we use WebSocket for real-time sync?"
  - "Does library X support our use case?"
  - "What's the performance of approach A vs B?"

Spike rules:
1. ONE question per spike
2. Time-box is sacred — no extensions
3. Document findings immediately
4. Share with team before moving on
5. Code is throwaway — keep only the knowledge
```

#### Prototype
```
Purpose: Explore how something should work
Duration: 3 days to 2 weeks
Output: Interactive artifact + user feedback
Fidelity levels:
  - Low: wireframes, paper sketches (hours)
  - Medium: clickable mockup (days)
  - High: functional but incomplete (1-2 weeks)

Prototype rules:
1. Focus on the EXPERIENCE, not the code
2. Use the fastest tool available
3. Test with 5+ real users
4. Document feedback systematically
5. Decide: iterate, graduate, or kill
```

#### MVP (Minimum Viable Product)
```
Purpose: Test if people will use/pay for it
Duration: 2-4 weeks (HARD limit: 6 weeks)
Output: Live product + metrics
Success metrics:
  - 10+ users tried it
  - Core action completed by >30%
  - Retention signal (users come back)
  - Willingness to pay (even 1 person)

MVP rules:
1. ONE core feature only
2. Ship before it's "ready"
3. Measure everything
4. Talk to every user
5. Kill criteria defined upfront
```

#### Pretotype
```
Purpose: Test if people WANT it before building
Duration: 1-3 days
Output: Interest signal (signups, clicks, conversations)

Pretotype methods:
  - Landing page with email signup
  - Social media post describing the product
  - "Fake door" button in existing app
  - Manual promise: "I'll build X if you want it"
  - Crowdfunding campaign (ultimate pretotype)

Pretotype rules:
1. Zero code if possible
2. Measure genuine interest, not politeness
3. Ask for commitment (email, money, time)
4. 100 signups = green light for prototype
5. <10 signups = kill or radically change positioning
```

#### Wizard of Oz
```
Purpose: Fake automation to test user behavior
Duration: 1-2 weeks
Output: User behavior data

How it works:
1. Build the frontend (what user sees)
2. Human does the backend manually (what user doesn't see)
3. User thinks it's automated
4. Collect behavior data
5. Decide if automation is worth building

Example:
  - User uploads photo → human does image processing → result appears
  - User asks question → human writes response → chatbot displays it
  - User sets preferences → human curates results → personalized feed

Wizard of Oz rules:
1. User MUST believe it's automated
2. Log everything the human does (defines automation spec)
3. Time the manual process (defines cost)
4. Keep it small (<20 users)
5. Transition to real automation or kill
```

#### Concierge MVP
```
Purpose: Deliver value manually before automating
Duration: 2-4 weeks
Output: Service validation + process knowledge

How it works:
1. Identify the value proposition
2. Deliver it manually to 5-10 customers
3. Document every step
4. Identify what's automatable vs what requires humans
5. Decide: automate, stay manual, or kill

Example:
  - "AI meal planning" → human plans meals for customers
  - "Automated investing" → human makes investment decisions
  - "Smart scheduling" → human schedules meetings

Concierge rules:
1. Charge money from day one (validates willingness to pay)
2. Document every step (defines product spec)
3. Keep customer count small (personal attention)
4. Ask: "Would you use this if it were automated?"
5. The manual process IS the product spec
```

#### Landing Page MVP
```
Purpose: Test if people will sign up
Duration: 1-3 days
Output: Conversion rate

Components:
  - Headline (value proposition)
  - 3-5 bullet points (benefits)
  - CTA (email signup, waitlist, preorder)
  - Optional: pricing test (show price, see if they click buy)

Tools:
  - Carrd.co ($19/year)
  - v0 by Vercel (free, AI-generated)
  - Bolt.new (free tier)
  - Typedream, Framer, Webflow

Landing page rules:
1. One page, one message, one CTA
2. Drive traffic: social media, Reddit, Hacker News
3. Measure: visits, signups, conversion rate
4. 5%+ conversion = strong signal
5. <1% conversion = weak signal, change positioning
```

#### Fake Door Test
```
Purpose: Test feature demand before building
Duration: Hours to days
Output: Click-through rate

How it works:
1. Add a button/link for the new feature
2. User clicks it
3. Show "Coming soon" or "Sign up for early access"
4. Measure click rate

Fake door rules:
1. Don't mislead — be transparent it's not built yet
2. Capture email for "early access"
3. High click rate = build it
4. Low click rate = don't build it
5. Works best in existing products with traffic
```

#### Smoke Test
```
Purpose: Test market demand with minimal investment
Duration: 3-7 days
Output: Demand metrics

How it works:
1. Create ad campaign (Google, Facebook, Reddit)
2. Ads describe the product (that doesn't exist yet)
3. Click → landing page → signup
4. Measure: CTR, signup rate, cost per signup

Smoke test rules:
1. Budget: $50-200 max
2. Target: specific audience, not broad
3. Metrics: CTR > 2%, signup rate > 5%
4. Don't run longer than 1 week
5. Data > opinions
```

## Step 2: Planning Framework

### Lean Canvas (Detailed)

```
+------------------+------------------+------------------+
| PROBLEM          | SOLUTION         | UNIQUE VALUE      |
|                  |                  |                   |
| Top 3 problems   | Top 3 features   | Single, clear     |
| for target       | that solve the   | compelling        |
| customer         | problems         | message           |
|                  |                  |                   |
| Existing alt:    |                  | High-level        |
| How they solve   |                  | concept (visual)  |
| it now           |                  |                   |
+------------------+------------------+
| KEY METRICS      | UNFAIR ADVANTAGE |
|                  |                  |
| Key activities   | Can't be easily  |
| you measure to   | copied or        |
| know if it's     | bought           |
| working          |                  |
+------------------+------------------+
| CHANNELS         | CUSTOMER SEGMENTS|
|                  |                  |
| Path to          | Target audience  |
| customers        | Early adopters   |
| (first 10,       | (who specifically?)|
| first 100)       |                  |
+------------------+------------------+
| COST STRUCTURE                  | REVENUE STREAMS           |
|                                 |                           |
| Fixed costs: hosting, tools     | Revenue model:            |
| Variable costs: per-user        | subscription, freemium,   |
| Time investment: hours/week     | one-time, usage-based     |
+---------------------------------+---------------------------+
```

### Lean Canvas Example: AI Meal Planner

```
+------------------+------------------+------------------+
| PROBLEM          | SOLUTION         | UNIQUE VALUE      |
|                  |                  |                   |
| 1. Don't know    | 1. AI generates  | "Personalized     |
|    what to cook   |    weekly meal   |  meals planned    |
| 2. Grocery       |    plans         |  in 30 seconds"   |
|    shopping is   | 2. Auto grocery  |                   |
|    tedious       |    lists         | Saves 3 hours/week|
| 3. Food waste    | 3. Uses what you | + reduces waste   |
|    from bad      |    already have  |                   |
|    planning      |                  |                   |
+------------------+------------------+
| KEY METRICS      | UNFAIR ADVANTAGE |
|                  |                  |
| - Weekly active  | - First to use   |
|   users          |   LLM for meal   |
| - Meals planned  |   planning       |
| - Groceries      | - Dietary pref   |
|   purchased      |   learning       |
| - Retention (W4) |                  |
+------------------+------------------+
| CHANNELS         | CUSTOMER SEGMENTS|
|                  |                  |
| - Reddit (r/meal | - Busy           |
|   prep, r/cooking)|  professionals  |
| - TikTok food    | - Parents with   |
|   content        |   picky eaters   |
| - Instagram      | - Health-        |
| - Word of mouth  |   conscious      |
+------------------+------------------+
| COST: $20/mo hosting | REVENUE: $9.99/mo subscription |
+------------------------------------+-------------------+
```

### Assumption Mapping (Detailed)

#### Step-by-Step Process

```
1. BRAINSTORM (15 min)
   - List ALL assumptions about:
     * The problem (is it real?)
     * The customer (do they exist?)
     * The solution (will it work?)
     * The business (can we make money?)
   - Aim for 15-30 assumptions

2. CLASSIFY each assumption:
   - PROBLEM: "Users struggle with meal planning"
   - CUSTOMER: "Working parents are the target"
   - SOLUTION: "AI-generated plans save time"
   - BUSINESS: "Users will pay $9.99/mo"

3. MAP on 2x2 matrix:
   - X-axis: Evidence (low → high)
   - Y-axis: Importance (low → high)

4. PRIORITIZE:
   - High importance + low evidence = VALIDATE FIRST
   - High importance + high evidence = MONITOR
   - Low importance + low evidence = IGNORE
   - Low importance + high evidence = NICE TO HAVE

5. For top 3 assumptions, design experiments
```

#### Assumption Mapping Template

```markdown
## Assumption Map: [Project Name]

### Problem Assumptions
| # | Assumption | Evidence | Importance | Priority |
|---|-----------|----------|------------|----------|
| P1 | Users struggle with meal planning | Low (anecdotal) | High | VALIDATE |
| P2 | Current solutions are too complex | Medium (reviews) | High | MONITOR |
| P3 | Users want AI recommendations | Low | Medium | VALIDATE |

### Customer Assumptions
| # | Assumption | Evidence | Importance | Priority |
|---|-----------|----------|------------|----------|
| C1 | Working parents are the target | Low | High | VALIDATE |
| C2 | They'll pay $9.99/mo | None | High | VALIDATE |
| C3 | They use mobile > desktop | Medium | Low | IGNORE |

### Solution Assumptions
| # | Assumption | Evidence | Importance | Priority |
|---|-----------|----------|------------|----------|
| S1 | AI can generate good meal plans | Medium | High | SPIKE |
| S2 | Grocery list integration is key | Low | Medium | VALIDATE |
| S3 | Users want dietary customization | Medium | Medium | MONITOR |

### Validation Plan
| Assumption | Experiment | Success Criteria | Timeline |
|-----------|-----------|-----------------|----------|
| P1 | Landing page + survey | 100 signups in 1 week | Day 1-7 |
| C2 | Pricing page test | 5% click "buy" | Day 1-7 |
| S1 | Technical spike | AI generates coherent plan | Day 1-2 |
```

### Risk-First Ordering

```
PRINCIPLE: Test the assumption that can KILL the project first.

Process:
1. List all assumptions
2. For each, ask: "If this is wrong, does the project die?"
3. If YES → highest priority
4. If NO → lower priority
5. Among equal priority: test easiest to validate first

Example:
  Assumption: "Users will pay $10/mo" → If wrong, project dies
  Assumption: "AI can generate plans" → If wrong, can use humans
  Assumption: "Grocery integration needed" → If wrong, can add later

  Order: Pricing (killing) → AI capability (technical) → Integration (nice-to-have)

Red flags (validate immediately):
  - "Users have this problem" (they might not)
  - "Users will switch from existing solution" (switching cost)
  - "Users will pay $X" (they might not)
  - "This technology works for our use case" (it might not)
```

### Customer Discovery: The Mom Test

```
RULES (from Rob Fitzpatrick's "The Mom Test"):

1. Talk about THEIR life, not your idea
   BAD:  "Would you use an AI meal planner?"
   GOOD: "Tell me about how you plan meals currently."

2. Ask about SPECIFIC past behavior, not hypothetical
   BAD:  "Would you pay for this?"
   GOOD: "Have you ever paid for a meal planning service?"

3. Ask about their problems, not your solution
   BAD:  "Wouldn't it be great if AI planned your meals?"
   GOOD: "What's the hardest part about meal planning?"

4. Shut up and listen
   - Ask open questions
   - Let them talk
   - Don't defend your idea

5. Get commitment, not compliments
   BAD:  "That sounds cool!" (compliment)
   GOOD: "Can I sign up now?" (commitment)
   GOOD: "Here's my email, let me know when it's ready" (commitment)
   GOOD: "I'll introduce you to my friend who needs this" (commitment)

GOOD QUESTIONS:
  - "Tell me about the last time you [did the thing]"
  - "What's the hardest part about [problem area]?"
  - "How do you currently solve [problem]?"
  - "What have you tried that didn't work?"
   - "How much time/money do you spend on [activity]?"

BAD QUESTIONS (leading):
  - "Don't you think X is a problem?"
  - "Wouldn't it be nice if Y?"
  - "How much would you pay for Z?"
```

## Step 3: Speed-Optimized Workflow

### Vibe Coding Deep Dive

#### What is Vibe Coding?

```
Definition: Software development assisted by AI where you describe 
what you want in natural language and the AI generates code.

Term coined: February 2025 by Andrej Karpathy
Key quote: "Fully give in to the vibes, embrace exponentials, 
and forget that the code even exists."

Key distinction:
  - Using AI as typing assistant = NOT vibe coding
  - Accepting AI output without review = vibe coding
  - The spectrum: review everything → review nothing
  - For throwaway projects: lean toward less review
  - For production: lean toward more review
```

#### Prompt Engineering for Prototypes

```
PATTERN 1: Scaffold Prompt
"Create a [type] app using [framework]. Features:
- [Feature 1]: [brief description]
- [Feature 2]: [brief description]
Use [data source: mock/localStorage/API]. 
Skip: auth, tests, error handling, loading states.
Make it functional, not pretty."

PATTERN 2: Iteration Prompt
"The [component] should [specific change]. 
Keep everything else exactly the same.
Don't refactor anything."

PATTERN 3: Debug Prompt
"Error: [paste error]
Expected: [what should happen]
Actual: [what happens]
Fix just this. Don't change anything else."

PATTERN 4: Feature Addition Prompt
"Add [feature] to the existing code.
Requirements: [list]
Don't modify existing features.
Minimal changes only."

PATTERN 5: Architecture Prompt
"I'm building [description]. 
Suggest 2-3 technical approaches with pros/cons.
Consider: [constraints: time, skill level, hosting]"

PATTERN 6: Planning Prompt
"I want to validate [idea] in [timeframe].
Help me:
1. Write a one-pager
2. Identify riskiest assumption
3. Design a validation experiment
4. Pick the fastest tech stack"

PATTERN 7: Refactor for Understanding
"Explain what this code does, step by step:
[paste code]
Then suggest the minimal changes to [goal]."
```

#### Iteration Loop (Optimized)

```
CYCLE TIME: 2-15 minutes per iteration

1. PROMPT (1-2 min)
   - Clear, specific instruction
   - Include constraints
   - Reference existing code if relevant

2. GENERATE (10-60 sec)
   - Wait for AI output
   - Don't interrupt

3. SCAN (30 sec)
   - Does it look right?
   - Any obvious errors?
   - Does it match what I asked?

4. RUN (30 sec)
   - Execute the code
   - Check for runtime errors
   - Test the specific feature

5. DECIDE (30 sec)
   - Works → COMMIT
   - Close → ADJUST (small fix)
   - Wrong → RE-PROMPT (different approach)
   - Stuck → PIVOT (simpler approach)

6. COMMIT (10 sec)
   - git add -A && git commit -m "working: [feature]"
   - Every working state = a save point

ITERATIONS PER HOUR: 4-30
FEATURES PER DAY: 5-15 (depending on complexity)
```

#### When to Accept vs Reject AI Output

```
ACCEPT when:
✓ It works (runs without errors)
✓ Core logic is correct
✓ It does what you asked
✓ You can see what it does (even if not every line)

REJECT when:
✗ It doesn't run
✗ It does something different than asked
✗ It adds complexity you didn't ask for
✗ It uses patterns you don't understand AND can't test

ADJUST when:
△ It mostly works but has a small issue
△ It works but uses an approach you don't like
△ It works but is overly complex for a prototype

For throwaway projects: bias toward ACCEPT
For production: bias toward REVIEW
```

### AI Tool Selection Matrix (2026)

```
┌─────────────────┬────────────┬──────────┬────────────┬─────────────┐
│ Tool            │ Best For   │ Skill    │ Cost       │ Speed       │
│                 │            │ Level    │            │             │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ Lovable         │ Full-stack │ Beginner │ Free tier  │ Fastest     │
│                 │ web apps   │          │ + paid     │ (0 setup)   │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ Bolt.new        │ Quick web  │ Beginner │ Free tier  │ Fast        │
│                 │ prototypes │          │ + paid     │ (0 setup)   │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ Replit          │ All-in-one │ Beginner │ Free tier  │ Fast        │
│                 │ (web+API)  │          │ + paid     │ (2 min)     │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ v0 by Vercel    │ UI         │ Some     │ Free tier  │ Fast        │
│                 │ components │ frontend │ + paid     │ (0 setup)   │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ Cursor          │ Existing   │ Developer│ $20/mo     │ Medium      │
│                 │ codebases  │          │            │ (local)     │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ Claude Code     │ Complex    │ Advanced │ API costs  │ Medium      │
│                 │ projects   │          │            │ (terminal)  │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ Gemini CLI      │ Google     │ Developer│ Free tier  │ Medium      │
│                 │ ecosystem  │          │            │ (terminal)  │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ GitHub Copilot  │ IDE        │ Developer│ $10-19/mo  │ Inline      │
│                 │ integration│          │            │ (in editor) │
├─────────────────┼────────────┼──────────┼────────────┼─────────────┤
│ Windsurf        │ Teams      │ Developer│ Fixed/mo   │ Medium      │
│                 │            │          │            │ (local)     │
└─────────────────┴────────────┴──────────┴────────────┴─────────────┘

SELECTION GUIDE:
- Non-coder, need web app → Lovable or Bolt.new
- Developer, existing project → Cursor
- Need UI components → v0
- Need backend + frontend → Replit or Lovable
- Complex multi-file → Claude Code
- Budget-conscious → Gemini CLI (free tier)
- Team workflow → Windsurf or Copilot
```

### No-Setup Tools for Maximum Speed

```
ZERO SETUP (browser-based):
├── Lovable      → Full-stack web app
├── Bolt.new     → Web app prototyping
├── v0           → UI components
├── Replit       → Any code (cloud IDE)
├── CodeSandbox  → Web experiments
└── StackBlitz   → Node.js projects

MINIMAL SETUP (install + run):
├── Cursor       → Download, open, prompt
├── Windsurf     → Download, open, prompt
└── Claude Code  → npm install, run

DEPLOYMENT (one-click):
├── Vercel       → Frontend (GitHub push)
├── Netlify      → Frontend (drag & drop)
├── Railway      → Backend + DB
├── Render       → Full stack
├── Fly.io       → Docker containers
└── Cloudflare   → Workers + Pages
```

## Step 4: Tech Stack Selection

### Decision Matrix

```
CRITERIA (weighted):
├── Setup time (30%)      → Can I start coding in <5 min?
├── Disposability (25%)   → Can I throw it away easily?
├── Learning curve (20%)  → Do I already know this?
├── Deployment (15%)      → Can I ship in <10 min?
└── Cost (10%)            → Free tier available?

SCORING (1-5):
5 = Perfect for this criterion
3 = Acceptable
1 = Poor
```

### Stack Recommendations by Project Type

#### Web Application
```
FASTEST (non-coder):
  Stack: Lovable or Bolt.new
  Setup: 0 min
  Deploy: Instant
  Best for: MVP, demo, prototype

FASTEST (developer):
  Stack: Next.js + Vercel + Cursor
  Setup: 5 min (npx create-next-app)
  Deploy: git push
  Best for: MVP, prototype, graduation path

ALTERNATIVE:
  Stack: Vite + React + Tailwind + Railway
  Setup: 3 min (npm create vite)
  Deploy: 10 min
  Best for: When you need more control
```

#### Mobile Application
```
FASTEST:
  Stack: Lovable + Capacitor (web → mobile)
  Setup: 0 min
  Best for: Prototype, MVP

ALTERNATIVE:
  Stack: React Native + Cursor
  Setup: 10 min (npx react-native init)
  Best for: When you need native features

NATIVE:
  Stack: Flutter + Cursor
  Setup: 15 min
  Best for: When you know Dart
```

#### Backend API
```
FASTEST:
  Stack: Replit (built-in hosting)
  Setup: 2 min
  Best for: Spike, prototype

FASTEST (local):
  Stack: Hono (TypeScript) or FastAPI (Python) + Cursor
  Setup: 3 min
  Deploy: Railway or Render
  Best for: When you need more control

ALTERNATIVE:
  Stack: Express.js + Cursor
  Setup: 5 min
  Deploy: Railway
  Best for: When you know Node.js
```

#### Data/ML Prototype
```
FASTEST:
  Stack: Google Colab + Gemini
  Setup: 0 min
  Best for: Spike, data exploration

LOCAL:
  Stack: Jupyter + Python + Cursor
  Setup: 5 min (pip install jupyter)
  Best for: When you need local data

PRODUCTION-BOUND:
  Stack: Python + FastAPI + Cursor
  Setup: 5 min
  Deploy: Railway
  Best for: When prototype might graduate
```

#### CLI Tool
```
FASTEST:
  Stack: Node.js or Python script + Claude Code
  Setup: 0 min
  Best for: Spike, personal tools

ALTERNATIVE:
  Stack: Go + Cursor
  Setup: 5 min
  Best for: When you need single binary
```

#### Landing Page
```
FASTEST:
  Stack: v0 by Vercel or Bolt.new
  Setup: 0 min
  Deploy: Instant
  Best for: Landing page MVP, smoke test

ALTERNATIVE:
  Stack: Carrd.co ($19/year)
  Setup: 5 min
  Best for: Simple pages, no code at all
```

### Anti-Patterns in Stack Selection

```
DON'T:
├── Use unfamiliar framework "to learn" during time-box
├── Choose based on resume value
├── Over-engineer (microservices for a prototype)
├── Pick slow setup tools (Kubernetes for a demo)
├── Use paid tools when free tier works
└── Choose based on what's "cool" vs what's fast

DO:
├── Use what you know
├── Use what's fastest to deploy
├── Use what's easiest to throw away
├── Use what has best AI support
├── Mock everything non-essential
└── Ship before it's "ready"
```

## Step 5: Time-Boxing Strategies

### Hard Limits by Project Type

| Type | Max Duration | Extension | Justification Required |
|------|-------------|-----------|----------------------|
| Spike | 2 days | None | — |
| Paper Prototype | 4 hours | None | — |
| Prototype | 1 week | +3 days | User feedback shows promise |
| PoC | 2 weeks | +1 week | Stakeholder approval |
| MVP | 4 weeks | +2 weeks | Metrics show traction |
| Demo | 2 days | None | — |
| Hackathon | 48 hours | None | — |
| Pretotype | 3 days | None | — |
| Landing Page | 2 days | None | — |
| Wizard of Oz | 2 weeks | +1 week | User demand validated |

### Daily Time-Box Structure

```
MORNING PLANNING (10 min):
├── What's the ONE thing that matters today?
├── What's blocking it?
├── Am I still on scope?
└── Time allocation:
    ├── Building: 60%
    ├── Testing: 20%
    ├── Planning: 10%
    └── Buffer: 10%

WORK BLOCKS:
├── Block 1 (2h): Core feature development
├── Break (15 min)
├── Block 2 (1.5h): Iteration + testing
├── Break (15 min)
├── Block 3 (1.5h): Secondary features or polish
├── Break (15 min)
└── Block 4 (1h): Documentation + planning

EVENING REVIEW (10 min):
├── Did I hit today's goal?
├── What did I learn?
├── Scope creep check: added anything not in plan?
├── Progress update (checklist)
└── Tomorrow's priority
```

### Weekly Sprint Structure (for MVPs)

```
WEEK 1: Core Feature
├── Day 1-2: Scaffold + core feature v1
├── Day 3-4: Iterate based on self-testing
├── Day 5: Show to 3+ users, collect feedback
└── Weekend: Process feedback, plan Week 2

WEEK 2: Polish + Expand
├── Day 1-2: Address top feedback items
├── Day 3-4: Add secondary feature (if validated)
├── Day 5: Second round of user testing
└── Weekend: Process feedback, plan Week 3

WEEK 3: Validate
├── Day 1-2: Launch to wider audience (20+ users)
├── Day 3-4: Measure core metrics
├── Day 5: Analyze data, prep decision
└── Weekend: Decision meeting

WEEK 4: Decide
├── Day 1-2: Final user interviews
├── Day 3: Decision: graduate / pivot / kill
├── Day 4: Execute decision
└── Day 5: Retrospective
```

### Progress Tracking

```markdown
## Progress: [Project Name]

### Day 1 (Date)
**Goal:** Scaffold + core feature
- [x] Set up project (tool: Lovable)
- [x] Core feature v1: user can [action]
- [ ] Test with mock data
- **Notes:** [learnings, blockers, surprises]
- **Time:** 4h planned, 3.5h actual

### Day 2 (Date)
**Goal:** Iterate + secondary feature
- [ ] Address Day 1 feedback
- [ ] Add [secondary feature]
- [ ] Show to first user
- **Notes:** 
- **Time:** 

### Day 3 (Date)
**Goal:** User testing
- [ ] Recruit 3 testers
- [ ] Run user tests
- [ ] Document feedback
- **Notes:** 
- **Time:** 
```

## Step 6: Quality Gates

### "Good Enough" by Project Type

```
SPIKE quality gate:
✓ Question answered definitively (yes/no/maybe)
✓ Approach documented
✓ Risks identified
✓ Recommendation clear
✗ No code quality needed (it's throwaway)

PROTOTYPE quality gate:
✓ Core interaction works
✓ User can complete primary task
✓ Feedback collected from 3+ users
✓ Key insights documented
✗ No error handling needed
✗ No edge cases covered
✗ No performance optimization

PoC quality gate:
✓ Technical feasibility proven
✓ Performance acceptable for demo
✓ Integration points identified
✓ Risks and limitations documented
✗ No production-grade code
✗ No comprehensive testing

MVP quality gate:
✓ 10+ real users tried it
✓ Core metric measured
✓ Retention signal (or clear lack thereof)
✓ At least 1 user willing to pay/commit
✓ Key feedback incorporated
✗ Not feature-complete
✗ Not optimized for scale

DEMO quality gate:
✓ Impressive to target audience
✓ Core flow works (even if fragile)
✓ Looks good enough for presentation
✓ Backup plan if demo fails
✗ No production readiness needed
```

### What to Skip vs What to Keep

```
SKIP (for all throwaway projects):
├── Comprehensive error handling
├── Loading states and spinners
├── Accessibility (unless testing with disabled users)
├── Performance optimization
├── Code documentation
├── CI/CD pipeline
├── Monitoring and alerting
├── Multi-environment setup
├── Database migrations
├── API versioning
├── Rate limiting
├── Caching strategies
├── Logging infrastructure
├── Feature flags
├── A/B testing framework
└── Internationalization

DON'T SKIP:
├── Basic input validation (prevents crashes)
├── Core user flow (the thing you're testing)
├── Data persistence (if testing over multiple days)
├── Basic security (don't expose secrets, use HTTPS)
├── Error messages for users (at least "something went wrong")
└── Basic responsive design (if testing on mobile)
```

## Step 7: Decision Framework

### Keep vs Discord vs Graduate

#### Scoring Rubric

```
After validation, score each dimension (1-5):

IMPACT (did users care?):
  5 = Users actively asking for more
  4 = Users completed tasks, positive feedback
  3 = Users completed tasks, neutral feedback
  2 = Users struggled, mixed feedback
  1 = Users didn't care or couldn't use it

FEASIBILITY (was it buildable?):
  5 = Easy to build, no technical blockers
  4 = Built with minor challenges
  3 = Built with significant effort
  2 = Major technical challenges remain
  1 = Fundamental technical blockers

STRATEGIC (does it fit?):
  5 = Perfect alignment with goals
  4 = Good alignment, minor gaps
  3 = Partial alignment
  2 = Weak alignment
  1 = No alignment

COST (can we maintain it?):
  5 = Minimal ongoing cost
  4 = Manageable cost
  3 = Moderate cost, needs resources
  2 = High cost, significant investment
  1 = Prohibitive cost

TOTAL: Sum of all four scores

DECISION MATRIX:
  16-20: GRADUATE → Plan production path
  11-15: PIVOT    → Adjust hypothesis, re-test
  6-10:  KILL     → Archive learnings, move on
  4-5:   ABORT    → Stop immediately
```

#### Decision Tree

```
START
│
├─ Did users care? (Impact ≥ 3?)
│  ├─ NO → KILL
│  └─ YES ↓
│
├─ Is it feasible? (Feasibility ≥ 3?)
│  ├─ NO → PIVOT (simpler approach) or KILL
│  └─ YES ↓
│
├─ Does it fit strategy? (Strategic ≥ 3?)
│  ├─ NO → PIVOT (different angle) or KILL
│  └─ YES ↓
│
├─ Can we afford it? (Cost ≥ 3?)
│  ├─ NO → PIVOT (cheaper approach) or KILL
│  └─ YES ↓
│
└─ GRADUATE → Production path
```

#### Stakeholder Alignment

```
BEFORE the project, agree on:
1. Decision criteria (what does success look like?)
2. Decision makers (who has final say?)
3. Decision timeline (when do we decide?)
4. Kill criteria (what makes us stop?)

AFTER validation, present:
1. Data (not opinions)
2. User quotes (real feedback)
3. Metrics vs targets
4. Recommendation (with reasoning)
5. Next steps (if any)

TEMPLATE for decision meeting:
  "We built [X] to test [hypothesis].
   We tested with [N] users over [timeframe].
   Key findings: [bullet points]
   Metrics: [numbers vs targets]
   Our recommendation: [graduate/pivot/kill]
   Reasoning: [why]
   Next steps: [if any]"
```

### Graduation Path

#### When Throwaway Becomes Real

```
TRIGGERS for graduation:
  ✓ Impact score ≥ 4
  ✓ Users asking "when can I use this?"
  ✓ Stakeholders want to invest
  ✓ Metrics show real demand
  ✓ Technical feasibility proven

SIGNS it's NOT ready:
  ✗ "I think it could work" (not validated)
  ✗ "Users said it was cool" (compliment ≠ commitment)
  ✗ "We already built it" (sunk cost)
  ✗ "It's almost done" (famous last words)
```

#### Graduation Checklist

```markdown
## Graduation Checklist: [Project Name]

### Phase 1: Foundation (Week 1-2)
- [ ] Code review by another developer
- [ ] Add proper error handling
- [ ] Add input validation
- [ ] Security review (OWASP basics)
  - [ ] No secrets in code
  - [ ] SQL injection prevention
  - [ ] XSS prevention
  - [ ] CSRF protection
  - [ ] Proper auth (not hardcoded)
- [ ] Set up proper database (not localStorage)
- [ ] Database migrations
- [ ] Environment variables

### Phase 2: Quality (Week 3-4)
- [ ] Write tests for core flows
  - [ ] Unit tests (critical functions)
  - [ ] Integration tests (API endpoints)
  - [ ] E2E tests (main user flows)
- [ ] Set up CI/CD pipeline
  - [ ] Lint on commit
  - [ ] Tests on PR
  - [ ] Auto-deploy to staging
- [ ] Code documentation
  - [ ] README with setup instructions
  - [ ] API documentation
  - [ ] Architecture overview

### Phase 3: Operations (Week 5-6)
- [ ] Logging and monitoring
  - [ ] Error tracking (Sentry)
  - [ ] Performance monitoring
  - [ ] Uptime monitoring
- [ ] Deployment strategy
  - [ ] Staging environment
  - [ ] Production environment
  - [ ] Rollback plan
- [ ] Performance baseline
  - [ ] Load testing
  - [ ] Response time benchmarks
  - [ ] Database query optimization

### Phase 4: Launch (Week 7-8)
- [ ] User documentation
- [ ] Onboarding flow
- [ ] Support process
- [ ] Feedback mechanism
- [ ] Analytics setup
- [ ] Launch plan
- [ ] Rollback plan tested
```

#### Refactoring Strategy

```
PRIORITY ORDER for refactoring:
1. Security issues (fix immediately)
2. Data integrity (fix before launch)
3. Core logic (refactor for maintainability)
4. Error handling (add before scaling)
5. Performance (optimize when needed)
6. Code style (last priority)

REFACTORING RULES:
- Refactor in small, testable increments
- Write tests BEFORE refactoring
- One refactor per commit
- Verify behavior doesn't change
- Don't refactor what you'll delete soon
```

### Archive Process (When Killing)

```markdown
## Project Archive: [Name]

### Metadata
- **Date:** [start date] — [end date]
- **Duration:** [X days/weeks]
- **Team:** [names]
- **Tool:** [Lovable/Cursor/etc.]

### What We Built
[Brief description of what was created]

### Hypothesis
[Original hypothesis]

### Validation Results
| Metric | Target | Actual | Verdict |
|--------|--------|--------|---------|
| [metric] | [target] | [actual] | ✓/✗ |

### Key Learnings
1. [Learning 1 — what we now know]
2. [Learning 2]
3. [Learning 3]

### User Feedback (Key Quotes)
> "[quote]"
> "[quote]"

### Why We Killed It
[Data-backed reasons]

### Reusable Assets
- [Code snippet / pattern worth keeping]
- [User research / interview notes]
- [Design assets / mockups]
- [API knowledge / integration learnings]
- [Contact list of interested users]

### Impact on Future Projects
[How this changes our approach]

### Decision Makers
[names + roles]

### Date Archived: [date]
```

## Step 8: Common Pitfalls

### 1. Scope Creep
```
Symptom: "While I'm at it, let me also add..."
Cause: No clear scope definition
Fix: 
  - Write scope in one-pager BEFORE starting
  - New ideas go to "future ideas" list
  - Daily scope check: "Am I building what I planned?"
  - Ask: "Does this feature test our hypothesis?"
  - If NO → don't build it
```

### 2. Sunk Cost Fallacy
```
Symptom: "I've spent 2 weeks on this, can't stop now."
Cause: Emotional attachment to invested time
Fix:
  - Time-box is sacred — no extensions without data
  - Kill criteria defined BEFORE starting
  - Ask: "If I were starting fresh today, would I build this?"
  - Archive learnings — the time wasn't wasted
  - Remember: killing early saves time for better ideas
```

### 3. Premature Optimization
```
Symptom: "Let me refactor this for performance."
Cause: Developer instinct to write "good" code
Fix:
  - It's throwaway. If it works, ship it.
  - Ask: "Will users notice this optimization?"
  - If NO → don't do it
  - Save optimization for graduation path
  - "Make it work, then make it good" — not for prototypes
  - For prototypes: "Make it work. Ship it."
```

### 4. Ignoring Security Basics
```
Symptom: Hardcoded API keys, no input validation, SQL injection
Cause: "It's just a prototype"
Fix:
  - NEVER commit secrets (use .env)
  - ALWAYS validate user input (basic)
  - ALWAYS use HTTPS
  - If handling user data: basic auth
  - "Just a prototype" doesn't mean "just a vulnerability"
```

### 5. Not Testing with Real Users
```
Symptom: "I think it works well."
Cause: Developers are not users
Fix:
  - Show to 3+ target users before deciding
  - Watch them use it (don't explain)
  - Ask: "What would you do next?" (not "do you like it?")
  - Record sessions (with permission)
  - User feedback > developer opinion
```

### 6. Over-Engineering Prototypes
```
Symptom: Microservices, event buses, design patterns for a prototype
Cause: Architecture astronaut / resume-driven development
Fix:
  - Monolith. Single file if possible.
  - Mock everything that's not the core hypothesis
  - No abstraction until you need it twice
  - No design patterns until the design emerges
  - The goal is VALIDATION, not architecture
```

### 7. AI Code Blindness
```
Symptom: Accepting all AI output without any review
Cause: Trust in AI / laziness / time pressure
Fix:
  - At minimum: run the code
  - Understand the core logic (even if not every line)
  - Check for obvious issues (secrets, SQL injection)
  - If you can't explain what it does, you can't debug it
  - For throwaway: lighter review. For graduation: full review.
```

### 8. Choosing Wrong Fidelity Level
```
Symptom: Pixel-perfect UI for a PoC, or ugly MVP for user testing
Cause: Mismatch between project type and effort
Fix:
  - PoC/Spike: functional is enough, ugly is fine
  - Prototype: must be usable, not pretty
  - MVP: must look professional enough to take seriously
  - Demo: must be impressive (polish matters)
  - Match effort to purpose
```

### 9. Ignoring the Hypothesis
```
Symptom: Building features instead of testing assumptions
Cause: Feature-driven development instead of hypothesis-driven
Fix:
  - Every feature maps to an assumption
  - No assumption = no feature
  - Ask: "What does this feature teach us?"
  - If answer is "nothing" → don't build it
  - Stay focused on the question, not the code
```

### 10. Not Documenting Learnings
```
Symptom: Kill project, move on, forget everything
Cause: No documentation habit
Fix:
  - Document as you go (daily notes)
  - Archive template filled at project end
  - Share learnings with team
  - "What did we learn?" is the most valuable output
  - Even failed projects produce valuable knowledge
```

## Step 9: Templates

### Project Brief

```markdown
# Project Brief: [Name]

## One-Line Description
[What this project does in one sentence]

## Hypothesis
We believe [target users] have [problem] and will [desired action] 
if we build [solution].

## Project Type
□ Spike  □ Prototype  □ PoC  □ MVP  □ Demo  □ Hackathon

## Timeline
- Start: [date]
- End: [date]
- Duration: [X days/weeks]

## Validation Criteria
| Metric | Target | How to Measure |
|--------|--------|---------------|
| [metric 1] | [target] | [method] |
| [metric 2] | [target] | [method] |
| [metric 3] | [target] | [method] |

## Scope
### In Scope
- [Feature 1]
- [Feature 2]
- [Feature 3]

### Out of Scope
- [NOT building 1]
- [NOT building 2]

## Tech Stack
- Tool: [Lovable/Cursor/etc.]
- Framework: [if applicable]
- Hosting: [if applicable]

## Riskiest Assumptions
1. [Assumption 1] — Test: [how]
2. [Assumption 2] — Test: [how]

## Decision Criteria
- SUCCESS: [what success looks like]
- PIVOT: [what pivot looks like]
- KILL: [what failure looks like]

## Team
- [Name]: [role]
- [Name]: [role]
```

### Assumption Tracker

```markdown
## Assumption Tracker: [Project Name]

### Active Assumptions
| # | Assumption | Priority | Experiment | Status | Result |
|---|-----------|----------|-----------|--------|--------|
| A1 | [assumption] | HIGH | [experiment] | Testing | — |
| A2 | [assumption] | MED | [experiment] | Validated | ✓ |
| A3 | [assumption] | LOW | [experiment] | Not started | — |

### Validated Assumptions
| # | Assumption | Evidence | Date |
|---|-----------|----------|------|
| A2 | [assumption] | [evidence] | [date] |

### Invalidated Assumptions
| # | Assumption | Evidence | Impact | Pivot/Kill? |
|---|-----------|----------|--------|-------------|
| A4 | [assumption] | [evidence] | [impact] | [decision] |

### Key Learnings
1. [Learning from assumption testing]
2. [Learning]
```

### Graduation Decision Matrix

```markdown
## Graduation Decision: [Project Name]

### Date: [date]

### Scores
| Dimension | Score (1-5) | Evidence |
|-----------|-------------|----------|
| Impact | [score] | [evidence] |
| Feasibility | [score] | [evidence] |
| Strategic Fit | [score] | [evidence] |
| Cost | [score] | [evidence] |
| **TOTAL** | **[sum]** | |

### Decision
□ GRADUATE (16-20) → Production path
□ PIVOT (11-15) → [pivot description]
□ KILL (6-10) → Archive learnings
□ ABORT (4-5) → Stop immediately

### Rationale
[Why this decision]

### Next Steps
[Action items with owners and dates]
```

### Retrospective Template

```markdown
## Retrospective: [Project Name]

### Project Info
- **Type:** [Spike/Prototype/PoC/MVP/etc.]
- **Duration:** Planned [X] / Actual [Y]
- **Tool:** [Lovable/Cursor/etc.]
- **Team:** [names]

### What Went Well
1. [positive 1]
2. [positive 2]
3. [positive 3]

### What Could Improve
1. [improvement 1]
2. [improvement 2]
3. [improvement 3]

### Key Learnings
1. [learning 1 — applicable to future projects]
2. [learning 2]
3. [learning 3]

### Tool Feedback
- **[Tool name]:** [what worked, what didn't]
- **AI assistance:** [quality of prompts, iteration speed]
- **Time-boxing:** [did it work? what to adjust?]

### Metrics
| Metric | Target | Actual |
|--------|--------|--------|
| Timeline | [X days] | [Y days] |
| Features planned | [N] | [M] |
| Users tested with | [N] | [M] |
| Key assumption validated? | Yes/No | [result] |

### Reusable for Future
- [Pattern/template/prompt that worked well]
- [Tool configuration worth reusing]
- [Contact/resource discovered]

### Would We Do It Again?
[Yes/No + why + what would change]

### Action Items
- [ ] [Action 1 — owner — date]
- [ ] [Action 2 — owner — date]
```

### Hackathon Prep Checklist

```markdown
## Hackathon Prep: [Event Name]

### Before the Event
- [ ] Choose project idea (1-2 options)
- [ ] Write one-pager for top idea
- [ ] Set up tool accounts (Lovable, Replit, etc.)
- [ ] Prepare dev environment
- [ ] Identify riskiest assumption
- [ ] Plan first 2 hours

### Team Roles (if team)
- [ ] Builder: [name] — core development
- [ ] Designer: [name] — UI/UX
- [ ] Presenter: [name] — demo prep
- [ ] Researcher: [name] — user feedback

### Time Plan (48h)
```
Hour 0-2:   Planning + scaffold
Hour 2-8:   Core feature
Hour 8-12:  Iterate + test
Hour 12-20: Secondary features
Hour 20-24: Polish + fix bugs
Hour 24-30: User testing + iteration
Hour 30-40: Final features + polish
Hour 40-44: Demo prep + practice
Hour 44-48: Buffer + presentation
```

### Demo Script
1. Problem (30 sec)
2. Solution (30 sec)
3. Live demo (2 min)
4. Traction/metrics (30 sec)
5. Ask (30 sec)

### Backup Plan
- If demo breaks: [screenshots/video]
- If API goes down: [mock data]
- If internet fails: [local demo]
```

### User Testing Script

```markdown
## User Testing Script: [Project Name]

### Setup (2 min)
"Hi [name], thanks for trying this out. 
I'm testing [what], not testing you. 
There are no wrong answers.
Please think out loud as you use it.
I'll ask questions along the way."

### Tasks (10-15 min)
1. "[First task instruction]"
   - Observe: [what to watch for]
   - Ask: "What are you thinking?"

2. "[Second task instruction]"
   - Observe: [what to watch for]
   - Ask: "What would you do next?"

3. "[Third task instruction]"
   - Observe: [what to watch for]
   - Ask: "Is this what you expected?"

### Questions (5 min)
1. "What was confusing?"
2. "What would you change?"
3. "Would you use this? Why/why not?"
4. "Would you pay for this? How much?"
5. "Who else should I talk to?"

### Wrap-up (1 min)
"Thanks! This was really helpful. 
Can I follow up with you if I have more questions?"

### Notes Template
| Task | Completed? | Time | Issues | Quotes |
|------|-----------|------|--------|--------|
| [task] | Y/N | [sec] | [issues] | "[quote]" |
```

### Speed Hacks Cheat Sheet

```
SPEED HACKS FOR THROWAWAY PROJECTS:

1. Use AI for EVERYTHING
   - Planning: "Help me write a one-pager for [idea]"
   - Scaffolding: "Create a [type] app with [features]"
   - Debugging: "Fix this error: [paste]"
   - Iteration: "Change X to Y"

2. Mock everything
   - Hardcode data instead of APIs
   - Use localStorage instead of databases
   - Skip auth (or use simple password)
   - Use placeholder images

3. Skip what doesn't matter
   - No tests (for throwaway)
   - No CI/CD
   - No error handling (basic try/catch only)
   - No loading states
   - No responsive design (unless testing mobile)

4. Use opinionated tools
   - Lovable/Bolt: pre-configured stack
   - Replit: zero setup
   - v0: pre-built components

5. Commit frequently
   - Every working feature = commit
   - Easy rollback if something breaks
   - Never lose working state

6. Time-box ruthlessly
   - Set timer for each feature
   - If stuck > 30 min → simplify or skip
   - "Done" > "perfect"

7. Test with users early
   - Day 1: show wireframe
   - Day 2: show prototype
   - Day 3: show working feature
   - Don't wait until it's "ready"

8. Use templates
   - One-pager template
   - Lean canvas template
   - User testing script
   - Don't reinvent the planning process
```

## Integration with Vibe Coding Best Practices

### 2026 Landscape

```
WHAT CHANGED (2024 → 2026):
├── AI tools went from suggestions to full generation
├── Non-coders can now build functional apps
├── "Vibe coding" became mainstream term
├── Y Combinator: 25% of startups 95% AI-generated
├── Tools: Cursor, Lovable, Bolt, Replit, v0 all matured
└── Risk: code quality and security concerns remain

WHAT IT MEANS FOR THROWAWAY PROJECTS:
├── Faster than ever to prototype
├── Non-technical founders can self-serve
├── Validation speed increased 10x
├── But: graduation path still requires engineering
└── And: security review is MORE important (AI code has vulns)
```

### Vibe Coding Best Practices for Throwaway Projects

```
DO:
├── Use AI for scaffolding (saves hours)
├── Iterate with prompts (not manual coding)
├── Accept "good enough" output
├── Ship before it's ready
├── Test with real users ASAP
└── Save often (commit working states)

DON'T:
├── Blindly accept all AI output (run it first)
├── Skip security basics (secrets, input validation)
├── Over-engineer (no patterns for prototypes)
├── Spend hours on prompts (if stuck, simplify)
├── Use unfamiliar stack (use what you know)
└── Forget the hypothesis (features must test assumptions)
```

### Prompt Iteration Pattern

```
ROUND 1: Scaffold
  "Create a [type] with [features]. Skip auth and tests."
  → Get basic structure

ROUND 2: Core Feature
  "Add [feature]. Requirements: [details]."
  → Get working feature

ROUND 3: Iterate
  "The [component] should [change]. Keep everything else."
  → Refine based on testing

ROUND 4: Fix
  "Error: [description]. Fix just this."
  → Fix what broke

ROUND 5: Polish
  "Make it look [adjective]. [specific changes]."
  → Make it presentable

RULES:
- One change per prompt
- Be specific about scope
- Reference existing code
- Ask for minimal changes
- Commit after each working change
```
