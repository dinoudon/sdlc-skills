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

## Step 12: Employer Branding

### Careers Page Anatomy

```
Above the fold:
  - Hero video: Team culture montage (60-90s)
  - Mission statement: 1-2 sentences
  - Open positions CTA
  
Culture section:
  - Values (3-5 core values with examples)
  - Benefits summary (health, equity, flexibility)
  - Team photos (authentic, not stock)
  - Employee testimonials (video preferred)
  
Engineering blog:
  - Technical challenges
  - Architecture decisions
  - Open source contributions
  - Conference talks

Office/Remote section:
  - Workspace photos
  - Remote work policy
  - Office locations
  - Co-working stipend info
```

### Glassdoor/LinkedIn Reputation Management

```
Monitoring:
  - Weekly check for new reviews
  - Track rating trends over time
  - Compare against competitors

Response strategy:
  Positive reviews:
    - Thank the reviewer
    - Mention specific things they praised
    - Invite them to refer friends
    
  Negative reviews:
    - Acknowledge the feedback
    - Explain what you're doing to improve
    - Offer to discuss offline
    - Never argue or dismiss
    
Improvement loop:
  - Aggregate feedback themes quarterly
  - Share with leadership
  - Implement changes
  - Update Glassdoor response to reflect changes
```

## Step 13: Remote Team Management

### Remote Work Policy Template

```
1. Eligibility
   - All full-time employees
   - Contractors on 6+ month engagements
   
2. Work hours
   - Core hours: 10am-3pm local time
   - Flexible outside core hours
   - Meeting-free Tuesdays and Thursdays
   
3. Communication
   - Slack for async communication
   - Zoom for video meetings
   - Notion for documentation
   - Email for external communication only
   
4. Equipment
   - $2,500 stipend for home office setup
   - Company laptop + monitors
   - $100/month internet/phone reimbursement
   
5. Expenses
   - Co-working space: up to $500/month
   - Coffee shop meals: up to $20/day
   - Travel to HQ: 2x per year (company-paid)
   
6. Performance
   - Output-based evaluation (not hours)
   - Weekly 1:1 with manager
   - Quarterly OKR reviews
   - Annual performance review
```

### Remote Meeting Best Practices

```
Before the meeting:
  - Agenda shared 24h in advance
  - Pre-read materials attached
  - Clear objective stated
  - Required vs optional attendees identified

During the meeting:
  - Start on time, end 5 min early
  - Camera on (builds trust)
  - Designated note-taker
  - Use "round robin" for input
  - Park off-topic items in "parking lot"

After the meeting:
  - Notes shared within 1 hour
  - Action items with owners + deadlines
  - Recording (if applicable)
  - Follow-up async if needed

Meeting-free zones:
  - Tuesdays: Deep work day
  - Thursdays: Deep work day
  - Fridays: No meetings after 2pm


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


## Step 16: Diversity & Inclusion

### D&I Metrics

```
Representation:
  - Gender ratio (overall, engineering, leadership)
  - Ethnicity ratio (overall, engineering, leadership)
  - Age distribution
  - Disability disclosure rate
  - Veteran status

Pipeline:
  - Diverse candidate % at each stage
  - Pass-through rates by demographic
  - Offer acceptance rates by demographic
  - Source diversity (job boards, referrals, agencies)

Retention:
  - Turnover by demographic
  - Promotion rates by demographic
  - Engagement scores by demographic
  - Pay equity ratios

Accountability:
  - D&I goals in leadership OKRs
  - Regular D&I reporting to board
  - ERG (Employee Resource Group) participation
  - D&I training completion rates
```

### Bias Reduction Strategies

```
Job descriptions:
  - Use gender-neutral language (Textio, Gender Decoder)
  - Remove unnecessary requirements (research shows women apply 
    only when meeting 100% of requirements, men at 60%)
  - Highlight inclusive benefits (parental leave, flexibility)
  - Avoid jargon and insider language

Resume screening:
  - Blind resume review (remove names, photos, schools)
  - Structured scoring rubric
  - Diverse review panels
  - Calibration sessions

Interviews:
  - Standardized questions per role
  - Scorecards with defined criteria
  - Diverse interview panels
  - Separate "culture fit" from "culture add"
  - Take-home assignments over whiteboard coding

Compensation:
  - Transparent salary bands
  - Annual pay equity audits
  - No salary history questions
  - Standardized offer process
```

## Step 17: Contractor & Vendor Management

### Contractor Onboarding

```
Pre-engagement:
  □ MSA and SOW executed
  □ Background check completed
  □ NDA signed
  □ Equipment/access requirements defined
  □ Manager and team introductions

Day 1:
  □ System access provisioned (email, Slack, tools)
  □ Security training completed
  □ Project briefing with manager
  □ Documentation access granted
  □ Communication norms established

Ongoing:
  □ Weekly check-ins with manager
  □ Access reviews (quarterly)
  □ Performance feedback (monthly)
  □ Contract renewal/termination planning

Offboarding:
  □ Access revoked immediately
  □ Equipment returned
  □ Knowledge transfer completed
  □ Exit interview (optional)
  □ IP assignment confirmed
```

### Vendor Evaluation Matrix

```
Criteria (weighted):
  - Technical capability (30%)
  - Cost (25%)
  - Security/compliance (20%)
  - References/reputation (15%)
  - Support/responsiveness (10%)

Scoring:
  1 = Does not meet requirements
  2 = Partially meets requirements
  3 = Meets requirements
  4 = Exceeds requirements
  5 = Significantly exceeds requirements

Vendor scorecard template:
  | Criteria | Weight | Vendor A | Vendor B | Vendor C |
  |----------|--------|----------|----------|----------|
  | Technical| 30%    | 4 (1.2) | 3 (0.9) | 5 (1.5) |
  | Cost     | 25%    | 3 (0.75)| 5 (1.25)| 2 (0.5) |
  | Security | 20%    | 4 (0.8) | 4 (0.8) | 4 (0.8) |
  | Refs     | 15%    | 4 (0.6) | 3 (0.45)| 4 (0.6) |
  | Support  | 10%    | 3 (0.3) | 4 (0.4) | 3 (0.3) |
  | TOTAL    | 100%   | 3.65    | 3.80    | 3.70     |


## Step 18: Employer Value Proposition

### EVP Framework

```
Components:
  1. Compensation: Competitive pay, equity, bonuses
  2. Benefits: Health, retirement, perks
  3. Career: Growth, learning, promotion paths
  4. Culture: Values, team, work environment
  5. Work: Impact, autonomy, meaningful projects

EVP statement template:
  "At [Company], you will [unique opportunity] while [key benefit]. 
  We [culture differentiator] and [career differentiator]."

Examples:
  Stripe: "Move money globally, build infrastructure at scale, 
  work with the smartest people in fintech."
  
  Notion: "Shape how teams collaborate, work on tools you would use 
  every day, in a thoughtful, design-driven culture."
  
  Vercel: "Build the future of web development, ship features 
  used by millions, in a remote-first, developer-obsessed team."

EVP communication:
  - Careers page (primary)
  - Job descriptions (every posting)
  - Social media (employee stories)
  - Interview process (reinforce throughout)
  - Onboarding (deliver on promises)
```

### Candidate Experience

```
Application process:
  - Simple application form (<5 minutes)
  - Mobile-friendly
  - Auto-acknowledgment (within 1 hour)
  - Status updates (weekly minimum)

Interview process:
  - Clear timeline communicated upfront
  - Respectful scheduling (minimal rounds)
  - Interviewer preparation (review resume)
  - Candidate prep materials provided
  - Decision within 1 week of final interview

Communication:
  - Rejection: Personalized, constructive feedback
  - Offer: Enthusiastic, clear terms, time to decide
  - Pre-boarding: Welcome package, team intros
  - Post-rejection: Talent pool for future roles

Metrics:
  - Application completion rate
  - Time to fill
  - Candidate NPS (survey after process)
  - Offer acceptance rate
  - Glassdoor interview experience rating
```

## Step 19: Learning and Development

### L&D Framework

```
Learning modalities:
  1. Formal training: Courses, certifications, conferences
  2. Social learning: Mentoring, peer learning, communities
  3. Experiential: Stretch assignments, job rotations, projects
  4. Self-directed: Books, podcasts, online courses

Budget allocation:
  - Engineering: $3,000-5,000/person/year
  - Product: $2,000-3,000/person/year
  - Sales: $2,000-4,000/person/year
  - Leadership: $5,000-10,000/person/year

Popular programs:
  - Engineering: AWS/GCP certs, conferences, Pluralsight
  - Product: Reforge, Product School, Mind the Product
  - Sales: MEDDIC, Sandler, Command of the Message
  - Leadership: Executive coaching, MBA sponsorship
  - All: LinkedIn Learning, Coursera, internal workshops

Measurement:
  - Course completion rates
  - Skill assessments (before/after)
  - Promotion rates for L&D participants
  - Manager feedback on skill development
  - Employee satisfaction with growth opportunities
```

## Step 20: Workforce Planning

### Headcount Planning

```
Inputs:
  - Revenue targets then Sales headcount
  - Product roadmap then Engineering headcount
  - Customer growth then Support/CS headcount
  - Strategic initiatives then Specialized roles

Planning model:
  Revenue per employee target: $200K-$400K (SaaS)
  Engineering % of total: 40-60%
  Sales % of total: 15-25%
  G&A % of total: 10-15%
  Marketing % of total: 5-10%
  Support % of total: 5-10%

Timeline:
  - Q4: Annual headcount plan
  - Q1-Q4: Quarterly hiring sprints
  - Monthly: Pipeline review and adjustments
  - Weekly: Offer approvals and start dates

Ramp assumptions:
  - Engineering: 3 months to full productivity
  - Sales: 4-6 months to quota
  - Customer Success: 2-3 months
  - Marketing: 2-3 months
```

## Step 21: Remote Culture

### Remote Culture Playbook

```
Communication norms:
  - Async-first: Documentation over meetings
  - Default to public: Slack channels, Notion pages
  - Written decisions: Decision logs, RFCs
  - Meeting-free time: Blocks for deep work

Connection rituals:
  - Daily standup (async or sync)
  - Weekly all-hands (recorded for time zones)
  - Monthly virtual social events
  - Quarterly in-person offsites
  - Annual company retreat

Tools:
  - Communication: Slack, Zoom, Loom
  - Documentation: Notion, Confluence
  - Collaboration: Figma, Miro, Google Workspace
  - Social: Donut (random coffee), Gather.town

Anti-patterns:
  - Always-on culture (no boundaries)
  - Meeting overload (no deep work time)
  - Proximity bias (favoring in-office)
  - Isolation (no social connection)
  - Timezone insensitivity
```


## Step 22: Technical Hiring

### Engineering Interview Process

```
Stage 1: Resume screen (15 min)
  - Relevant experience (3+ years for mid-level)
  - Technology stack match
  - Company/role progression
  - Open source / side projects (bonus)

Stage 2: Recruiter screen (30 min)
  - Motivation and career goals
  - Salary expectations
  - Logistics (location, visa, start date)
  - Culture fit (quick assessment)

Stage 3: Technical phone screen (60 min)
  - Coding problem (medium difficulty)
  - Language/framework proficiency
  - Problem-solving approach
  - Communication clarity

Stage 4: On-site / Virtual loop (4-6 hours)
  - Coding interview (2x 60 min): Algorithms, data structures
  - System design (60 min): Architecture, scalability
  - Behavioral (60 min): STAR format, culture fit
  - Hiring manager (30 min): Role fit, team dynamics

Stage 5: Debrief (30-60 min)
  - Each interviewer presents assessment
  - Discuss disagreements
  - Vote: Strong hire / Hire / No hire / Strong no hire
  - Hiring manager makes final decision
```

### System Design Interview Rubric

```
Evaluation criteria (1-4 scale):

Problem understanding:
  1: Jumps to solution without clarifying requirements
  2: Asks some clarifying questions
  3: Thoroughly understands requirements and constraints
  4: Identifies edge cases and non-functional requirements

High-level design:
  1: No clear architecture
  2: Basic architecture, missing key components
  3: Complete architecture with appropriate components
  4: Elegant design with clear trade-off analysis

Deep dive:
  1: Cannot explain component details
  2: Surface-level understanding
  3: Detailed understanding of key components
  4: Expert-level knowledge with optimization strategies

Trade-offs:
  1: No awareness of trade-offs
  2: Mentions trade-offs but cannot analyze
  3: Clear analysis of trade-offs with justification
  4: Nuanced analysis with real-world examples

Scoring:
  4: Exceptional (clear hire signal)
  3: Meets bar (positive signal)
  2: Below bar (negative signal)
  1: Significant concerns (strong no hire)
  
  Hire if: Majority of criteria score 3+
  Strong hire if: Multiple criteria score 4
```

## Step 23: Sourcing Strategies

### Active Sourcing Playbook

```
Channels:
  LinkedIn Recruiter:
    - Boolean search: "software engineer" AND "React" AND "Series B"
    - InMail: Personalized, short, specific
    - Response rate: 15-25% with good messaging
  
  GitHub:
    - Search by language, location, contribution activity
    - Open source contributors = demonstrated skill
    - Look for README quality, project complexity
  
  Technical communities:
    - Stack Overflow (high reputation = strong engineer)
    - Hacker News (technical discussions)
    - Dev.to, Hashnode (technical writing)
    - Discord/Slack communities (by technology)
  
  Referrals:
    - Employee referral program ($2K-$10K bonus)
    - Referral rate target: 30-40% of hires
    - Referred candidates: 2x retention rate
  
  Events:
    - Tech meetups (local communities)
    - Conferences (sourcing at booths)
    - Hackathons (observe skills in action)
    - University recruiting (intern pipeline)

Outreach templates:
  Cold outreach (LinkedIn):
    "Hi [Name], I saw your work on [specific project]. 
    We're building [compelling problem] at [Company] and 
    looking for [role]. Would you be open to a quick chat?"
  
  Follow-up (3 days later):
    "Hi [Name], just bumping this up. We're working on 
    [specific technical challenge] and your background in 
    [their expertise] would be perfect. 15 min this week?"
```

### Employer Branding Content

```
Engineering blog topics:
  - Technical architecture decisions (why we chose X over Y)
  - Scaling stories (how we handled 10x growth)
  - Open source contributions (what we built and why)
  - Engineering culture (how we work, values, rituals)
  - Day-in-the-life profiles (meet the team)
  - Conference talks (share knowledge)
  - Post-mortems (learn from failures, transparently)

Content calendar:
  - 2 blog posts per month
  - 1 video per month (team or technical)
  - 1 conference talk per quarter
  - 1 meetup hosting per quarter
  - Daily social media (team wins, culture)

Measurement:
  - Blog traffic and engagement
  - Social media followers and engagement
  - Inbound applications (from content)
  - Source attribution (blog → application)
  - Quality of inbound candidates
```

## Step 24: Onboarding Programs

### 30-60-90 Day Plan

```
Day 1-30 (Learn):
  Week 1:
    - Company orientation (mission, values, org chart)
    - Team introductions (1:1s with each team member)
    - Tool setup (laptop, accounts, access)
    - First task: Fix a small bug or docs typo
  
  Week 2-3:
    - Product deep dive (use the product as a customer)
    - Architecture overview (system design walkthrough)
    - Codebase tour (key repos, patterns, conventions)
    - First PR review (learn code review culture)
  
  Week 4:
    - First feature task (small, well-scoped)
    - 1:1 with manager (feedback, adjust plan)
    - Onboarding survey (feedback on experience)
    - Milestone: First PR merged

Day 31-60 (Contribute):
  - Own and deliver a medium-sized feature
  - Participate in on-call rotation (shadowing)
  - Lead a technical discussion or design review
  - Contribute to sprint planning
  - Build relationships cross-functionally

Day 61-90 (Lead):
  - Own a significant project or initiative
  - Mentor newer team members
  - Propose process improvements
  - Present at team meeting or all-hands
  - 90-day performance review with manager
```

### Buddy System

```
Buddy assignment:
  - Same team, similar level
  - Not the direct manager
  - Willing volunteer (not forced)
  - 3-month commitment

Buddy responsibilities:
  Week 1: Daily check-ins (15 min)
  - Answer questions about tools, processes, culture
  - Introduce to key people
  - Help with logistics (badge, parking, lunch)
  
  Week 2-4: 2x per week check-ins
  - Code review guidance
  - Process questions
  - Social integration (lunch, coffee)
  
  Month 2-3: Weekly check-ins
  - Career advice
  - Team dynamics
  - Feedback channel

Buddy training:
  - 1-hour orientation on buddy role
  - Conversation guide (what to cover each week)
  - Escalation path (if onboarding issues arise)
  - Recognition (buddy program in performance review)


## Step 25: Diversity Hiring

### Inclusive Job Descriptions

```
Language audit:
  - Remove gendered language (use Textio, Gender Decoder)
  - Avoid jargon and insider terms
  - Keep requirements to must-haves (research shows women 
    apply only when meeting 100% of requirements)
  - Highlight inclusive benefits (parental leave, flexibility)

Template:
  About [Company]:
  We are [mission statement]. Our team of [X] people is 
  building [what] for [who]. We value [2-3 core values].

  About the role:
  As a [Title], you will [key responsibility]. You will work 
  with [team] to [outcome].

  What you will do:
  - [Responsibility 1]
  - [Responsibility 2]
  - [Responsibility 3]

  What we are looking for:
  - [Must-have skill 1]
  - [Must-have skill 2]
  - [Must-have skill 3]

  Nice to have:
  - [Bonus skill 1]
  - [Bonus skill 2]

  What we offer:
  - Competitive salary ($X-$Y, based on experience)
  - Equity (0.X-0.Y%)
  - Health, dental, vision insurance
  - $X learning budget
  - Flexible PTO
  - Remote-friendly

  We encourage applications from candidates of all 
  backgrounds. If you do not meet every requirement but 
  are excited about this role, we want to hear from you.
```

### Diverse Sourcing Channels

```
Job boards:
  - Jopwell: Black, Latinx, Native American professionals
  - PowerToFly: Women in tech
  - Diversify Tech: Underrepresented groups
  - Out in Tech: LGBTQ+ tech professionals
  - AbilityJobs: People with disabilities
  - Hire Heroes USA: Veterans

Communities:
  - /dev/color: Black software engineers
  - Lesbians Who Tech: LGBTQ+ women in tech
  - Tech Ladies: Women in tech
  - Code2040: Black and Latinx technologists
  - /include: Diversity and inclusion

University partnerships:
  - HBCUs (Historically Black Colleges and Universities)
  - HSIs (Hispanic-Serving Institutions)
  - Women in CS programs
  - Disability inclusion programs

Events:
  - Grace Hopper Celebration (women in computing)
  - AfroTech (Black tech professionals)
  - Lesbians Who Tech Summit
  - Tech Inclusion Conference
```

## Step 26: Interview Bias Reduction

### Structured Interview Design

```
Standardized questions:
  - Same questions for all candidates (same role)
  - Questions tied to job requirements
  - Behavioral questions (STAR format)
  - Technical questions (role-specific)

Scorecard:
  | Criterion | 1 (Poor) | 2 (Fair) | 3 (Good) | 4 (Excellent) |
  |-----------|----------|----------|----------|---------------|
  | Technical skill | Cannot solve | Partial solution | Correct solution | Optimal solution |
  | Communication | Unclear, disorganized | Somewhat clear | Clear, structured | Exceptional clarity |
  | Problem-solving | Gives up quickly | Needs hints | Independent | Creative approaches |
  | Collaboration | Poor listener | Adequate | Good team player | Elevates team |

Calibration:
  - Interviewers discuss before interview
  - Align on what "good" looks like
  - Practice scoring with sample answers
  - Regular calibration sessions (monthly)

Debiasing techniques:
  - Blind resume review (remove names, schools)
  - Diverse interview panels (gender, ethnicity, role)
  - Separate "culture fit" from "culture add"
  - Document decision rationale
  - Review decisions for patterns (demographic bias)
```

### Interview Training Program

```
Module 1: Legal compliance (30 min)
  - What you can and cannot ask
  - Protected characteristics
  - Reasonable accommodations

Module 2: Bias awareness (60 min)
  - Common biases (affinity, halo, confirmation)
  - Impact on hiring decisions
  - Mitigation strategies

Module 3: Structured interviewing (60 min)
  - Question design
  - Scorecard usage
  - STAR format for behavioral questions

Module 4: Technical interviewing (60 min)
  - Problem design (relevant, fair, calibrated)
  - Evaluation criteria
  - Providing good candidate experience

Module 5: Practical exercise (60 min)
  - Mock interview with feedback
  - Score calibration exercise
  - Debrief practice

Certification:
  - Complete all modules
  - Shadow 2 interviews
  - Conduct 2 interviews with observer
  - Pass calibration test
  - Renewed annually
```

## Step 27: Compensation Benchmarking

### Benchmarking Process

```
Data sources:
  - Levels.fyi: Crowdsourced compensation data
  - Glassdoor: Employee-reported salaries
  - Payscale: Compensation data and analytics
  - Radford: Enterprise compensation surveys
  - Comptryx: Tech compensation benchmarks
  - Carta: Startup equity benchmarks

Benchmarking methodology:
  1. Define job families (Engineering, Product, Sales, etc.)
  2. Map internal levels to market levels
  3. Collect data from 3+ sources
  4. Calculate percentile (25th, 50th, 75th)
  5. Adjust for geography (cost of labor)
  6. Set target percentile per level

Target percentiles:
  - Junior (L1-L2): 50th percentile (competitive for talent)
  - Mid (L3-L4): 60th percentile (retain top performers)
  - Senior (L5-L6): 70th percentile (scarce talent)
  - Staff+ (L7+): 75th percentile (exceptional talent)
  - Executive: 75th-90th percentile (retention critical)

Adjustment factors:
  - Geography: SF=100%, NYC=95%, Austin=85%, Remote=90%
  - Industry: FinTech=110%, Enterprise=105%, Consumer=95%
  - Stage: Startup=90% base + equity, Growth=100%, Public=105%
```

## Step 28: Termination Best Practices

### Termination Checklist

```
Pre-termination:
  □ Document performance issues (PIP, written warnings)
  □ HR review of legal risks
  □ Manager training on termination conversation
  □ Prepare separation agreement
  □ Calculate final pay (salary, PTO, bonus)
  □ Plan for benefits continuation (COBRA)
  □ Arrange equipment return
  □ Plan access revocation timing

Day of termination:
  □ Private meeting room (in-person) or video call
  □ Manager + HR present
  □ Brief, respectful conversation (15-20 min)
  □ Provide written separation agreement
  □ Explain benefits and final pay
  □ Collect equipment (or arrange return)
  □ Escort from building (if in-person)

Post-termination:
  □ Revoke all system access (same day)
  □ Update org chart and team communications
  □ Redirect email and work to team
  □ Announce to team (brief, respectful)
  □ Process COBRA election notice (within 14 days)
  □ Final paycheck (timing varies by state)
  □ File unemployment (if applicable)
```

### Severance Policy

```
Standard severance:
  - Non-executive: 2-4 weeks per year of service
  - Executive: 6-12 months
  - Minimum: 2 weeks (goodwill)

Severance agreement includes:
  - Release of claims (standard)
  - Non-disparagement (mutual)
  - Confidentiality (surviving)
  - Non-compete (if applicable, varies by state)
  - Reference agreement (what will be said)
  - Outplacement services (optional)

Legal considerations:
  - 21-day review period (45 days if group termination)
  - 7-day revocation period (after signing)
  - ADEA compliance (age 40+)
  - OWBPA compliance (group terminations)
  - State-specific requirements (WARN Act for 100+ employees)


## Step 29: International Hiring

### Global Employment Options

```
Option 1: Entity setup
  - Establish legal entity in country
  - Full control over employment
  - High cost and complexity
  - Timeline: 3-6 months
  - Best for: Large teams (10+), long-term commitment

Option 2: Employer of Record (EOR)
  - Third party employs on your behalf
  - Handles payroll, compliance, benefits
  - Lower cost, faster setup
  - Timeline: 1-2 weeks
  - Best for: Small teams (1-5), testing market

Option 3: Contractor
  - Independent contractor agreement
  - No employment obligations
  - Risk of misclassification
  - Timeline: Immediate
  - Best for: Short-term, project-based

Option 4: PEO (Professional Employer Organization)
  - Co-employment arrangement
  - Shared employer responsibilities
  - Available in some countries (mainly US)
  - Best for: SMBs wanting HR outsourcing

EOR providers:
  - Deel: Global payroll and compliance
  - Remote: International employment
  - Oyster: Global employment platform
  - Papaya Global: Global payroll management
  - Velocity Global: International expansion
```

### Global Compensation

```
Benchmarking:
  - Use local salary surveys (Radford, Mercer)
  - Adjust for cost of living
  - Consider local market rates
  - Factor in benefits expectations

Components:
  Base salary: Competitive locally
  Equity: Stock options or RSUs (tax implications vary)
  Benefits: Local requirements + enhancements
  Bonus: Performance-based (10-20% target)

Country-specific considerations:
  - 13th month salary (Latin America, Asia)
  - Mandatory bonuses (India, China)
  - Provident fund (India, Singapore)
  - Social security contributions (varies)
  - Vacation minimums (EU: 20-30 days)
  - Parental leave (varies significantly)

Currency:
  - Pay in local currency
  - Budget in USD (FX risk management)
  - Review rates quarterly
  - Adjust for significant movements
```

## Step 30: Talent Analytics

### HR Metrics Dashboard

```
Recruitment metrics:
  - Time to fill: Days from requisition to offer acceptance
  - Time to hire: Days from application to offer acceptance
  - Cost per hire: Total recruiting cost / hires made
  - Source of hire: Which channels produce best candidates
  - Offer acceptance rate: Offers accepted / offers made
  - Quality of hire: Performance rating of new hires

Retention metrics:
  - Turnover rate: Departures / average headcount
  - Voluntary turnover: Resignations / average headcount
  - Involuntary turnover: Terminations / average headcount
  - Retention rate by cohort: 90-day, 1-year, 2-year
  - Regrettable turnover: High performers who left

Engagement metrics:
  - Employee NPS: "Would you recommend as workplace?"
  - Engagement score: Survey-based
  - Participation rate: Survey completion %
  - Absenteeism: Unplanned absences per employee

Diversity metrics:
  - Representation: % by gender, ethnicity, level
  - Hiring diversity: % diverse candidates at each stage
  - Promotion equity: Promotion rates by demographic
  - Pay equity: Compensation ratios by demographic
```

### Predictive Analytics

```
Turnover prediction:
  Features:
    - Tenure
    - Time since last promotion
    - Salary vs market rate
    - Manager rating
    - Engagement score
    - Commute time/remote status
    - Team turnover rate
  
  Model: Logistic regression or random forest
  Output: Probability of leaving in next 6 months
  Action: Proactive retention for high-risk employees

Hiring success prediction:
  Features:
    - Interview scores
    - Assessment results
    - Source of hire
    - Years of experience
    - Education
    - Cultural fit score
  
  Model: Gradient boosting
  Output: Probability of high performance
  Action: Improve hiring decisions

Tools:
  - Visier: People analytics
  - One Model: HR analytics platform
  - ChartHop: People analytics
  - Custom: Python/R for advanced modeling
```

## Step 31: Employer Branding

### Careers Page Optimization

```
Above the fold:
  - Hero video: Team culture montage (60-90s)
  - Mission statement: 1-2 sentences
  - Open positions CTA

Content sections:
  - Culture: Values, team photos, office/workspace
  - Benefits: Comprehensive benefits overview
  - Testimonials: Employee stories (video preferred)
  - Engineering blog: Technical content
  - Perks: Unique offerings (learning, wellness)

Conversion optimization:
  - Clear job search/filter
  - Easy application process (<5 minutes)
  - Mobile-responsive design
  - Fast page load
  - SEO-optimized job postings

Analytics:
  - Page views and time on page
  - Application conversion rate
  - Source attribution
  - Bounce rate
  - Mobile vs desktop
```

### Glassdoor Strategy

```
Profile optimization:
  - Complete company profile
  - Respond to reviews (positive and negative)
  - Update photos and videos
  - Highlight awards and recognition

Review management:
  - Monitor reviews weekly
  - Respond to all reviews within 48 hours
  - Thank positive reviewers
  - Address negative feedback constructively
  - Never argue or dismiss concerns

Improvement process:
  - Aggregate feedback themes
  - Share with leadership
  - Implement changes
  - Update Glassdoor profile to reflect improvements
  - Track rating trends over time


## Step 32: Employee Relations

### Conflict Resolution

```
Types of workplace conflict:
  - Interpersonal: Personality clashes, communication styles
  - Task: Disagreements about work approach or priorities
  - Process: Disputes about workflows or responsibilities
  - Status: Power dynamics, recognition, advancement

Resolution process:
  1. Early intervention (address before escalation)
  2. Private conversations (understand each perspective)
  3. Mediation (neutral third party if needed)
  4. Agreement (document resolution)
  5. Follow-up (ensure resolution holds)

Manager training:
  - Active listening skills
  - De-escalation techniques
  - Bias awareness
  - Documentation requirements
  - When to involve HR

Escalation path:
  1. Direct conversation (employee to employee)
  2. Manager involvement
  3. HR mediation
  4. Formal investigation
  5. Executive decision
```

### Employee Complaints

```
Complaint channels:
  - Direct manager (first option)
  - HR representative
  - Anonymous hotline
  - Ethics email
  - Skip-level manager

Investigation process:
  1. Receive complaint (document details)
  2. Assess severity (immediate action needed?)
  3. Investigate (interviews, evidence review)
  4. Determine findings (facts, not opinions)
  5. Take action (disciplinary, training, policy change)
  6. Communicate outcome (to complainant, respondent)
  7. Follow-up (ensure no retaliation)

Documentation:
  - Complaint details (who, what, when, where)
  - Investigation steps taken
  - Evidence collected
  - Findings and conclusions
  - Action taken
  - Follow-up plan

Legal considerations:
  - Non-retaliation policy
  - Confidentiality (as much as possible)
  - Timely response (within 48 hours)
  - Attorney involvement (if serious)
```

## Step 33: HR Technology

### HR Tech Stack

```
Core HRIS:
  - BambooHR: SMB HRIS
  - Rippling: HR + IT + Finance
  - Workday: Enterprise HCM
  - ADP: Payroll + HR

Recruiting:
  - Greenhouse: ATS
  - Lever: ATS + CRM
  - Ashby: Modern ATS
  - Gem: Recruiting CRM

Performance:
  - Lattice: Performance + engagement
  - Culture Amp: Employee experience
  - 15Five: Performance management
  - Betterworks: OKR + performance

Learning:
  - LinkedIn Learning: Online courses
  - Pluralsight: Tech skills
  - Udemy Business: Diverse courses
  - Lessonly: Team learning

Benefits:
  - Gusto: SMB benefits
  - Zenefits: Benefits administration
  - Benefitfocus: Enterprise benefits
  - Alight: Benefits outsourcing

Compensation:
  - Payscale: Compensation data
  - Carta: Equity management
  - Comp Analytics: Comp planning
  - Salary.com: Benchmarking
```

### HR Data and Analytics

```
Key HR metrics:
  Headcount:
    - Total employees
    - By department, level, location
    - New hires, terminations
    - Open positions
  
  Retention:
    - Turnover rate (voluntary, involuntary)
    - Retention by cohort (90-day, 1-year, 2-year)
    - Regrettable turnover rate
    - Exit interview themes
  
  Engagement:
    - Employee NPS
    - Engagement survey scores
    - Participation rates
    - eNPS trends
  
  Compensation:
    - Pay equity ratios
    - Comp-ratio (actual vs market)
    - Total cost of workforce
    - Benefits utilization

Analytics tools:
  - Visier: People analytics
  - One Model: HR data platform
  - ChartHop: Org chart + analytics
  - BambooHR reports: Basic analytics
```

## Step 34: Employee Wellness

### Wellness Programs

```
Mental health:
  - Employee Assistance Program (EAP)
  - Therapy/counseling benefit ($1-5K/year)
  - Mental health days (separate from PTO)
  - Stress management workshops
  - Mindfulness/meditation apps (Calm, Headspace)

Physical health:
  - Gym membership subsidy ($50-150/month)
  - Ergonomic equipment (standing desk, chair)
  - Health screenings (annual)
  - Vaccination clinics (flu, COVID)
  - Wellness challenges (steps, sleep)

Financial wellness:
  - Financial planning services
  - Student loan repayment assistance
  - 401k education
  - Emergency savings programs
  - Financial literacy workshops

Social wellness:
  - Team building activities
  - Employee resource groups (ERGs)
  - Volunteer days (1-2 per year)
  - Social events (virtual and in-person)
  - Community service opportunities

Measurement:
  - Program utilization rates
  - Employee satisfaction surveys
  - Health insurance claims trends
  - Absenteeism rates
  - Productivity metrics
