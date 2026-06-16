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