---
name: sdlc-hiring-talent
description: "Technical hiring and team building: recruiting, interview design, coding assessments, system design interviews, culture fit, engineering levels, onboarding, retention, compensation, remote teams, diversity, employer branding, hiring pipeline optimization."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, hiring, recruiting, interviews, team-building, engineering-levels, onboarding, retention, compensation, employer-branding]
    related_skills: [sdlc-retrospective, sdlc-prd-to-production, sdlc-finance-ops]
---

# Hiring & Talent

Building engineering teams. Recruiting, interviewing, onboarding, and retaining top talent.

## When to Use

Trigger when user:
- Designs interview process or coding assessments
- Creates engineering career ladder or levels
- Plans hiring pipeline or recruiting strategy
- Onboards new engineers
- Addresses retention or attrition
- Builds remote or distributed teams
- Plans compensation bands or equity
- Creates employer brand or job descriptions

## Step 1: Hiring Pipeline

### Pipeline Stages

```
Source → Screen → Interview → Offer → Close → Onboard

Sourcing:
• Job boards (LinkedIn, Indeed, Hacker News Who's Hiring)
• Referrals (highest quality, lowest cost)
• Inbound (careers page, blog, open source)
• Agencies (for hard-to-fill roles)
• Events (meetups, conferences, hackathons)

Screening:
• Resume review (2-3 min per resume)
• Recruiter call (15-30 min, culture + logistics)
• Technical phone screen (45-60 min)
• Take-home assignment (optional, 2-4 hours max)

Interview:
• Coding interview (1-2 rounds, 45-60 min each)
• System design (1 round, 60 min)
• Behavioral (1 round, 45 min)
• Team fit / hiring manager (1 round, 30-45 min)

Offer:
• Verbal offer (hiring manager calls)
• Written offer (compensation, equity, benefits)
• Negotiation (expected, budget for it)
• Close (reference checks, start date)
```

### Hiring Funnel Metrics

```
1000 applicants → 200 screened → 50 phone screens → 20 onsites → 8 offers → 5 hires

Conversion rates:
• Application → Screen: 20%
• Screen → Phone: 25%
• Phone → Onsite: 40%
• Onsite → Offer: 40%
• Offer → Accept: 62%

Key metrics:
• Time to hire: 30-45 days (good), <30 days (great)
• Cost per hire: $4,000-$15,000 (varies by role)
• Offer acceptance rate: >70%
• Quality of hire (6-month performance review)
```

## Step 2: Interview Design

### Interview Loop Structure

```
Round 1: Technical Phone Screen (45 min)
  • 5 min: introductions
  • 35 min: coding problem (easy-medium, LeetCode style)
  • 5 min: candidate questions

Round 2: Onsite / Virtual Onsite (4-5 hours)
  • Coding 1 (60 min): data structures + algorithms
  • Coding 2 (60 min): practical problem (build something)
  • System Design (60 min): design a system at scale
  • Behavioral (45 min): STAR-format questions
  • Hiring Manager (30 min): fit + sell the role

Alternative (for senior+ roles):
  • Take-home (2-4 hours) replaces coding rounds
  • Architecture review of their solution
  • Technical deep dive on past projects
```

### Coding Interview Best Practices

```
DO:
• Use problems relevant to actual work
• Allow candidate to use their preferred language
• Provide a collaborative coding environment (CoderPad, HackerRank)
• Give hints when stuck (see how they learn)
• Evaluate problem-solving process, not just solution
• Test edge cases and error handling

DON'T:
• Ask brainteasers or trick questions
• Require memorized algorithms (allow looking up syntax)
• Make it adversarial (you're evaluating, not interrogating)
• Ignore communication skills
• Penalize for nervousness
• Ask the same question to every candidate (rotate pool)
```

### System Design Interview

```
Structure (60 min):
1. Requirements gathering (10 min)
   • Functional: what does it do?
   • Non-functional: scale, latency, availability
   
2. High-level design (15 min)
   • Major components, data flow, APIs
   
3. Deep dive (25 min)
   • Database schema, caching strategy, scaling
   • Trade-offs, bottlenecks, failure modes
   
4. Wrap-up (10 min)
   • Monitoring, deployment, operational concerns

Example problems:
• Design a URL shortener (1B URLs, 100B reads/day)
• Design Twitter/X (500M DAU, real-time feed)
• Design a payment system (Stripe-like)
• Design a chat system (WhatsApp-like)
• Design a video streaming service (Netflix-like)
```

### Behavioral Interview Questions (STAR Format)

```
Situation → Task → Action → Result

Questions:
• "Tell me about a time you disagreed with a technical decision."
• "Describe a project that failed. What did you learn?"
• "How did you handle a situation with ambiguous requirements?"
• "Tell me about a time you mentored someone."
• "Describe your most complex debugging session."
• "How do you prioritize when everything is urgent?"
• "Tell me about a time you had to push back on scope creep."

Red flags:
• Blames others, no self-reflection
• Can't articulate their specific contribution
• No lessons learned from failures
• Dismissive of non-technical stakeholders
```

## Step 3: Engineering Levels

### Career Ladder Template

```
Level | Title              | Scope          | Impact
------|--------------------|----------------|------------------
IC1   | Junior Engineer    | Task           | Within team
IC2   | Engineer           | Feature        | Within team
IC3   | Senior Engineer    | Project        | Across team
IC4   | Staff Engineer     | Multiple teams | Org-wide
IC5   | Principal Engineer | Company        | Industry
IC6   | Distinguished Eng  | Industry       | Industry-wide

M1    | Engineering Manager| People         | Team
M2    | Senior EM / Dir    | Multiple teams | Org
M3    | VP Engineering     | Org            | Company
M4    | CTO                | Company        | Industry
```

### Level Expectations Matrix

| Dimension | IC3 Senior | IC4 Staff | IC5 Principal |
|-----------|------------|-----------|---------------|
| **Scope** | Project (3-6 months) | Multiple projects | Org-wide strategy |
| **Influence** | Team of 5-10 | 3-5 teams (30-50 people) | Entire engineering org |
| **Technical** | Deep expertise in area | Cross-domain expertise | Industry-level expertise |
| **Leadership** | Mentors juniors | Sets technical direction | Defines engineering vision |
| **Ambiguity** | Resolves for team | Resolves for org | Defines for company |
| **Communication** | Team-level docs | Org-level RFCs | Company-wide talks |

### Promotion Criteria

```
To promote IC2 → IC3 (Senior):
□ Independently delivers complex projects (3+ months)
□ Writes design docs reviewed by peers
□ Mentors at least 1 junior engineer
□ On-call for production systems
□ Contributes to team processes and standards
□ Positive feedback from cross-functional partners
□ Demonstrates technical judgment (trade-off decisions)
```

## Step 4: Onboarding

### 30-60-90 Day Plan

```
Day 1-7: Setup
  □ Laptop + tools configured
  □ Access to repos, CI, dashboards
  □ Meet the team (1:1 with each member)
  □ Read team docs, architecture diagrams
  □ First PR merged (typo fix, small bug)

Day 8-30: Contribute
  □ Complete 2-3 small features/bug fixes
  □ Attend all team ceremonies
  □ Understand on-call rotation
  □ Shadow a senior engineer's project
  □ 30-day check-in with manager

Day 31-60: Own
  □ Lead a medium-sized feature
  □ Write design doc for a project
  □ Participate in code reviews
  □ Identify and fix a process gap
  □ 60-day check-in with manager

Day 61-90: Scale
  □ Own a project end-to-end
  □ Mentor a newer hire
  □ Propose an improvement to team processes
  □ 90-day review with manager
  □ Onboard into on-call rotation
```

### Onboarding Buddy System

```
Each new hire gets an onboarding buddy (peer, not manager):
• Same team, different project
• Available for quick questions (Slack, in-person)
• Meets 2x/week for first month
• Answers "how do we actually do X?" questions
• Introduces to people outside the team
• Provides safe space for feedback
```

## Step 5: Retention & Compensation

### Why Engineers Leave

```
Top reasons (from exit surveys):
1. Limited growth opportunities (no career path)
2. Compensation below market
3. Poor management (micromanagement, no autonomy)
4. Boring work (maintenance only, no greenfield)
5. Burnout (overwork, on-call, tight deadlines)
6. Culture (toxic, political, no psychological safety)
7. Better opportunity (more impact, better title, cooler tech)
```

### Retention Strategies

```
1. Growth: Clear career ladder, training budget, conference attendance
2. Compensation: Annual market adjustments, equity refreshers
3. Autonomy: Let engineers choose how to solve problems
4. Impact: Connect work to business outcomes
5. Recognition: Public praise, spot bonuses, promotions
6. Flexibility: Remote work, flexible hours, unlimited PTO
7. Culture: Blameless postmortems, psychological safety, team events
```

### Compensation Bands

```
Component          | Typical Range
-------------------|------------------
Base salary        | Market rate (P50-P75)
Equity (RSUs)      | 10-50% of base (4yr vest, 1yr cliff)
Bonus              | 0-20% of base
Sign-on bonus      | $10K-$50K (to offset unvested equity)
Benefits           | Health, dental, vision, 401k match
Perks              | Meals, gym, transit, learning budget

Benchmarking sources:
• Levels.fyi
• Glassdoor
• Blind
• Radford (enterprise)
• Comptryx (enterprise)
```

## Step 6: Remote & Distributed Teams

### Remote Team Best Practices

```
Communication:
• Default to async (write it down, don't schedule a meeting)
• Document decisions (ADRs, meeting notes)
• Over-communicate context (assume nothing)
• Use video for relationship-building, not status updates

Collaboration:
• Overlap hours (4 hours of shared time)
• Pair programming via screen share
• Virtual coffee chats (random 1:1 pairing)
• Annual or semi-annual in-person offsites

Tools:
• Slack/Teams for async communication
• Notion/Confluence for documentation
• Loom for async video updates
• GitHub/GitLab for code collaboration
• Miro/FigJam for visual collaboration
```

## Step 7: Employer Branding

### Building Engineering Brand

```
1. Engineering blog (2-4 posts/month)
2. Open source contributions
3. Conference talks and sponsorships
4. GitHub presence (stars, contributions)
5. Social media (Twitter/X, LinkedIn)
6. "Best Places to Work" awards
7. Glassdoor reviews (respond to all)
8. University partnerships and internships
9. Hackathon sponsorships
10. Technical content (tutorials, courses)
```

### Job Description Template

```markdown
## About [Company]
[2-3 sentences about mission and product]

## What You'll Do
- [Specific responsibility 1]
- [Specific responsibility 2]
- [Specific responsibility 3]

## What We're Looking For
### Must Have
- [X] years of experience with [technology]
- Experience with [specific skill]
- [Education/certification if required]

### Nice to Have
- Experience with [bonus technology]
- Contributions to open source
- [Other bonus qualifications]

## What We Offer
- Competitive salary ($X-$Y range, transparent)
- Equity package
- Health, dental, vision
- [Other benefits]
- Remote-friendly / [location]

## How to Apply
[Clear instructions — link, email, what to include]
```

## Step 8: Technical Assessment Design

### Take-Home Assignment Best Practices

```
Design principles:
1. Real-world problem (not algorithmic puzzles)
2. Time-boxed (2-4 hours max, respect candidates' time)
3. Clear requirements and evaluation criteria
4. Allow any language/framework
5. Provide starter code if applicable
6. Evaluate: code quality, testing, documentation, trade-offs

Example take-home:
"Build a URL shortener service with:
 - POST /urls → create short URL
 - GET /{code} → redirect to original
 - GET /{code}/stats → click count, referrers
 - Handle concurrent requests
 - Include tests
 
Deliverable: GitHub repo with README explaining your design decisions.
Time limit: 3 hours. We value clarity over cleverness."
```

### System Design Interview Rubric

```
Score 1-4 per dimension:

Requirements Gathering (weight: 15%):
  1: Jumped to solution without asking questions
  2: Asked some questions but missed key requirements
  3: Clarified functional and non-functional requirements
  4: Deep understanding, asked about edge cases and constraints

High-Level Design (weight: 25%):
  1: No clear architecture
  2: Basic components but unclear data flow
  3: Clear components, data flow, API design
  4: Elegant architecture with clear trade-off discussion

Deep Dive (weight: 25%):
  1: Couldn't go deeper when asked
  2: Surface-level knowledge of components
  3: Good depth on 2-3 areas, understands trade-offs
  4: Expert-level depth, considers failure modes

Scalability (weight: 20%):
  1: No consideration of scale
  2: Mentions caching but no systematic approach
  3: Discusses sharding, caching, CDN, queue-based processing
  4: Quantitative analysis, capacity planning, bottleneck identification

Communication (weight: 15%):
  1: Monologuing, not responsive to hints
  2: Explains thinking but hard to follow
  3: Clear explanation, responsive to feedback
  4: Collaborative, thinks out loud, incorporates feedback naturally
```

### Coding Interview Evaluation Matrix

```
Dimension          | Weight | 1 (Below) | 2 (Meets) | 3 (Exceeds)
-------------------|--------|-----------|-----------|------------
Problem solving    | 30%    | Needs    | Solves    | Elegant
                   |        | heavy    | with      | solution,
                   |        | hints    | guidance  | multiple
                   |        |          |           | approaches
Code quality       | 25%    | Messy,   | Clean,    | Production-
                   |        | no       | readable  | ready,
                   |        | structure|           | idiomatic
Testing            | 15%    | No tests | Basic     | Edge cases,
                   |        |          | happy path| error cases
Communication      | 15%    | Silent   | Explains  | Collaborative
                   |        |          | approach  | thinking
Optimization       | 15%    | Brute    | Discusses | Implements
                   |        | force    | trade-offs| optimal
```

## Step 9: Diversity & Inclusion in Hiring

### Bias Mitigation Strategies

```
Sourcing:
  □ Partner with diversity-focused organizations (Code2040, /dev/color, Out in Tech)
  □ Post on diverse job boards (Jopwell, PowerToFly, Diversify Tech)
  □ Use gender-neutral job descriptions (Textio, Gender Decoder)
  □ Remove degree requirements when skills matter more

Screening:
  □ Blind resume review (remove names, schools, photos)
  □ Structured scorecards (same criteria for all candidates)
  □ Diverse interview panels (at least 1 underrepresented interviewer)
  □ Calibrate interviewers regularly (reduce subjective bias)

Evaluation:
  □ Standardized rubrics (1-4 scale with clear definitions)
  □ Independent scoring before debrief (prevent anchoring)
  □ Separate "culture fit" from "culture add" (fit = homogeneity risk)
  □ Track diversity metrics in pipeline (identify drop-off points)
```

### Inclusive Job Description Template

```
Avoid:
  ❌ "Ninja", "rockstar", "guru" (gendered, exclusionary)
  ❌ "Must have 10+ years experience" (arbitrary, discourages diverse candidates)
  ❌ "Fast-paced, high-pressure environment" (signals burnout culture)
  ❌ Long lists of "requirements" (women apply when meeting 100%, men at 60%)

Use:
  ✅ Clear, specific role description
  ✅ "Requirements" vs "Nice to have" (separate clearly)
  ✅ Mention flexibility (remote, hours, parental leave)
  ✅ Include salary range (transparency)
  ✅ EEO statement (not performative, genuine commitment)
```

## Pitfalls

1. **Hiring for pedigree** — "Must have FAANG experience" excludes great talent. Hire for skills, not logos.
2. **LeetCode grind culture** — Memorizing 500 problems doesn't predict job performance. Use practical assessments.
3. **No structured interview** — Unstructured interviews are 2x more biased. Use scorecards and consistent rubrics.
4. **Slow hiring process** — Top candidates have offers in 2 weeks. Move fast or lose them.
5. **Ghosting candidates** — Always send rejection emails. Reputation matters.
6. **No diversity effort** — Homogeneous teams build homogeneous products. Actively source diverse candidates.
7. **Equity confusion** — Explain equity clearly (vesting, cliff, dilution, strike price). Don't be vague.
8. **Burnout as culture** — "We work hard and play hard" often means "we overwork you." Sustainable pace wins.
9. **No growth path** — Engineers without promotion paths leave. Have clear, achievable criteria.
10. **Hiring above your bar** — Desperation hires create more work. Better to leave a role open than hire wrong.

## Sources

- Will Larson, Staff Engineer: https://staffeng.com/
- Will Larson, An Elegant Puzzle: https://lethain.com/elegant-puzzle/
- Camille Fournier, Manager's Path: https://www.themanagerspath.org/
- First Round Review (hiring): https://review.firstround.com/
- Levels.fyi (compensation): https://www.levels.fyi/
- Hacker News Who's Hiring: https://news.ycombinator.com/item?id=35930664
- Key Values (culture fit): https://www.keyvalues.com/
- Holloway Guide to Equity: https://www.holloway.com/g/equity-compensation
- Pragmatic Engineer (hiring): https://blog.pragmaticengineer.com/
- Structuring Teams (Team Topologies): https://teamtopologies.com/
