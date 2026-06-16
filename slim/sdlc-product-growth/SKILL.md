---
name: sdlc-product-growth
description: "Product-led growth (PLG), developer-led growth, growth loops, activation funnels, A/B testing, SaaS metrics (MRR/ARR/LTV/CAC/NRR), unit economics, pricing strategy, monetization, onboarding optimization, feature gating, competitive analysis, growth hacking, referral programs, paywall design."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, product-growth, plg, growth-engineering, saas-metrics, pricing, monetization, experimentation, ab-testing, funnels, activation, retention, unit-economics, developer-led-growth]
    related_skills: [sdlc-prd-to-production, sdlc-requirements-engineering, sdlc-testing-qa, sdlc-developer-relations, sdlc-gtm-strategy, sdlc-finance-ops]
---

---
name: sdlc-product-growth
description: "Product-led growth (PLG), developer-led growth, growth loops, activation funnels, A/B testing, SaaS metrics (MRR/ARR/LTV/CAC/NRR), unit economics, pricing strategy, monetization, onboarding optimization, feature gating, competitive analysis, growth hacking, referral programs, paywall design."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, product-growth, plg, growth-engineer
## When to Use
Trigger when user:
- Designs pricing, plans tiers, or evaluates monetization models
- Asks about growth hacking, viral loops, or referral programs
- Needs SaaS metrics (MRR, ARR, LTV, CAC, NRR, churn)
- Wants A/B testing or experimentation setup
- Designs onboarding, activation funnels, or paywalls
- Asks "how do companies like Stripe/Slack/Notion grow?"
- Plans feature gating or freemium strategy
- Needs unit economics or cohort analysis
## Step 1: Growth Philosophy — Product-Led vs Sales-Led
```
PRODUCT-LED ◄─────────────────────────────► SALES-LED
(Free signup,    (Hybrid:      (Demos,
 self-serve)     free trial +  contracts,
                 sales assist) procurement)

Examples:
PLG:    Notion, Figma, Slack, Zoom, Dropbox, Canva
Hybrid: Stripe, Datadog, Confluent, MongoDB
Sales:  Salesforce, Oracle, Workday, ServiceNow
```
## Step 2: SaaS Metrics Framework
Source: Dave McClure, 500 Startups
| Stage | Metric | Definition | Target |
|-------|--------|------------|--------|
| **Acquisition** | Visitor → Signup rate | % of visitors who create account | 5-15% |
| **Activation** | Time-to-value | Time from signup to first success | <5 min |
| **Retention** | D1/D7/D30 retention | % returning after N days | D1: 40%+, D7: 20%+, D30: 10%+ |
| **Revenue** | MRR, ARPU | Monthly recurring revenue per user | Varies |
| **Referral** | Viral coefficient (k-factor) | Invites sent × conversion rate | k > 1 = viral |
```
MRR  = Σ (paying customers × monthly subscription)
ARR  = MRR × 12
ARPU = MRR / paying customers
NRR  = (MRR start + expansion - contraction - churn) / MRR start × 100
LTV  = ARPU × (1 / monthly churn rate)
CAC  = Total sales & marketing spend / new customers acquired
LTV:CAC ratio = LTV / CAC  (target: >3:1)
CAC Payback = CAC / (ARPU × gross margin)  (target: <18 months)
Churn Rate = Customers lost / Total customers at start
Net Revenue Retention = (start MRR + expansion - contraction - churn) / start MRR
Gross Margin = (Revenue - COGS) / Revenue  (target: >70% for SaaS)
```
## Step 3: Pricing Strategy
| Model | How It Works | Best For | Examples |
|-------|-------------|----------|----------|
| **Freemium** | Free tier + paid upgrades | PLG, viral products | Slack, Notion, Figma |
| **Free Trial** | Time-limited full access | Complex products | Salesforce, HubSpot |
| **Usage-Based** | Pay per API call/compute/GB | APIs, infrastructure | Stripe, AWS, Twilio |
| **Seat-Based** | Per-user per-month | Collaboration tools | Atlassian, GitHub |
| **Hybrid** | Base + usage overage | Mixed workloads | Datadog, Snowflake |
| **Tiered** | Good/Better/Best packages | Multiple segments | Zoom, Mailchimp |
```
Starter:  2.9% + 30¢ per transaction  (no monthly fee)
Scale:    2.9% + 30¢ + volume discounts
Enterprise: Custom pricing + dedicated support

Key insight: Pricing aligns with customer success.
When customer processes more, Stripe earns more.
No upfront cost → zero friction to start.
```
## Step 4: Growth Loops & Viral Mechanics
Funnels are linear (acquire → activate → retain). Growth loops are circular — output of one cycle becomes input of next.
**Content Loop (Pinterest, Stack Overflow)**:
```
User creates content → Content indexed by SEO → New user discovers content
→ New user creates content → Loop repeats
```
## Step 5: Activation & Onboarding
```
Goal: Get user to first success moment as fast as possible.

TTV Components:
1. Signup friction (email+password vs SSO vs magic link)
2. First-run experience (empty state, guided tour)
3. Aha moment (when user first sees value)
4. Setup completion (profile, integrations, first action)

Aha Moments (by company):
Slack:    2,000 messages sent in a workspace
Dropbox:  File saved in Dropbox folder on 2+ devices
Facebook: 7 friends in 10 days
Twitter:  30 followers
Zoom:     First successful video call
Stripe:   First test payment in sandbox
```
## Step 6: Experimentation & A/B Testing
```
1. Hypothesis: "Changing CTA from 'Sign Up' to 'Start Free Trial' 
   will increase signup rate by 10%"
2. Metric: Primary = signup conversion. Secondary = activation rate.
3. Sample size: Use power analysis (α=0.05, β=0.2, MDE=10%)
4. Duration: Minimum 2 full business weeks (avoid novelty effect)
5. Variants: Control (current) vs Treatment (new CTA)
6. Randomization: User-level (not session-level) to avoid contamination
7. Analysis: Intention-to-treat, guard against Simpson's paradox
```
## Step 7: Competitive Analysis
```
1. Feature comparison matrix
2. Pricing comparison
3. Market positioning map
4. SWOT analysis per competitor
5. Customer win/loss analysis
6. Technical architecture comparison
7. Developer experience comparison (docs, SDKs, sandbox)
```
## Step 8: Unicorn Growth Playbooks
- API-first: 7 lines of code to first payment
- Docs-as-product: Best-in-class API documentation
- Sandbox: Full test environment, no signup required
- Transparent pricing: No hidden fees, no sales calls
- Expand: Start with payments → billing → connect → radar → atlas
- Result: $107B valuation, processes $1T+/year
- Freemium with generous limits (10K messages)
- Bottom-up: Individual → team → company
- Integrations: 2400+ apps create switching costs
- Fun UX: Custom emoji, bots, playful design
- Growth loop: User invites → team grows → needs paid plan
- Result: Acquired by Salesforce for $27.7B
- Templates: Community-created templates drive discovery
- Power users become advocates (YouTube, Twitter)
- Flexible: Notes → docs → wiki → database → project management
- Education: Notion Academy, certification program
- Result: $10B valuation
- Browser-based: Zero install, instant sharing
- Real-time collab: Like Google Docs for design
- Sharing loop: Designer shares link → stakeholder views → signs up
- Community: Figma Community for plugins and templates
## Step 9: Growth Engineering (Technical Implementation)
```
Client → Analytics SDK → Event Pipeline → Data Warehouse → BI Tool

Event Schema:
{
  "event": "feature_used",
  "user_id": "usr_123",
  "timestamp": "2026-06-16T10:30:00Z",
  "properties": {
    "feature": "export_csv",
    "plan": "pro",
    "team_size": 5,
    "source": "onboarding_checklist"
  },
  "context": {
    "page": "/dashboard",
```
## Step 10: Retention & Engagement Deep Dive
```
RETENTION = FUNCTION(Activation, Engagement, Resurrection)

Activation (Day 0-7):
  - Get user to first value moment
  - Remove all friction from signup → first success
  - Personalize based on use case / persona

Engagement (Day 7-90):
  - Build habit loops (trigger → action → reward)
  - Progressive feature discovery
  - Social features (collaboration, sharing)
  - Content and community

Resurrection (Day 90+):
  - Win-back campaigns (email, in-app)
```
## Step 11: Growth Team Structure
```
Growth Team (5-8 people):
├── Growth Lead (PM or Engineering Manager)
├── Growth Engineer (full-stack, experimentation)
├── Data Analyst (metrics, experiments, insights)
├── Product Designer (onboarding, activation UX)
├── Marketing Ops (email, campaigns, automation)
└── Content Strategist (in-app messaging, copy)

Steering:
- Weekly growth review (metrics dashboard)
- Bi-weekly experiment planning
- Monthly growth retrospective
```
## Step 12: Metrics Dashboard Templates
```
┌─────────────────────────────────────────────────────────┐
│  GROWTH DASHBOARD — Week of June 16, 2026               │
├────────────────┬────────────────┬───────────────────────┤
│  MRR: $245K    │  ARR: $2.94M   │  NRR: 114%           │
│  ↑8% MoM       │  ↑32% YoY      │  ↑2pts QoQ           │
├────────────────┼────────────────┼───────────────────────┤
│  New MRR: $18K │  Expansion: $5K│  Churn: -$3K         │
│  ↑12% WoW      │  ↑8% WoW       │  ↓2pts from last wk  │
├────────────────┴────────────────┴───────────────────────┤
│  Activation Funnel (this week):                         │
│  Signup (850) → Verified (595, 70%) → Activated (298,   │
│  35%) → Invited Team (127, 15%) → Converted (43, 5%)   │
├─────────────────────────────────────────────────────────┤
│  Experiments Running: 5  |  Completed: 3  |  Won: 1     │
│  Top experiment: +18% activation (new onboarding flow)  │
```
## Step 13: Growth Playbook Templates
| ID | Hypothesis | Metric | MDE | Sample | Duration | Status |
|----|------------|--------|-----|--------|----------|--------|
| EXP-001 | New onboarding increases activation | D7 activation | 10% | 5K users | 14d | Running |
| EXP-002 | Social proof on pricing page increases conversion | Signup→paid | 15% | 3K visitors | 14d | Planned |
| EXP-003 | In-app referral prompt increases invites | K-factor | 20% | 2K users | 21d | Planned |
| EXP-004 | Usage-based pricing reduces churn | Monthly churn | 2pts | 1K subs | 30d | Backlog |
```
## Experiment: [Name]
If we [change], then [metric] will [improve/decrease] by [amount]
because [reasoning].
- Variant A (control): [current state]
- Variant B (treatment): [proposed change]
- Split: 50/50, user-level randomization
- Duration: [X days minimum]
- Sample size: [N per variant]
[Name] — [definition] — [how measured]
- [Metric that must not degrade]
- [Metric that must not degrade]
- Variant A: [value] (n=[N])
- Variant B: [value] (n=[N])
- Lift: [X%] (p-value: [Y])
- Decision: [Ship / Iterate / Kill]
[What did we learn? What's next?]
```
## Pitfalls
1. **Optimizing for vanity metrics** — Page views and signups don't equal revenue. Track activation and retention.
2. **Pricing too low** — Underpricing signals low value. Raise prices until you get pushback.
3. **Premature scaling** — Don't invest in growth before product-market fit. PMF signal: organic retention > 40%.
4. **Ignoring churn** — Acquiring 100 users/month while losing 80 is a leaky bucket. Fix retention first.
5. **A/B testing without traffic** — Need ~10K visitors/week per variant for statistical significance.
6. **Copy-pasting growth tactics** — What works for Slack won't work for enterprise. Adapt to your market.
7. **Free tier too generous** — If free users never need to upgrade, you have a free product, not freemium.
8. **Free tier too stingy** — Users can't experience value → never convert. Find the balance.
9. **Ignoring expansion revenue** — NRR > 100% means existing customers grow faster than churn. Critical for SaaS.
10. **Sales-led when should be PLG** — Developers don't want sales calls. If your users are devs, go PLG.
## Step 14: Growth Accounting & North Star Metric
```
Growth Accounting decomposes user base changes:

Net New Users = New Users + Resurrected Users - Churned Users

Weekly example:
  Start of week: 10,000 users
  New: +500 (signups)
  Resurrected: +100 (returned after 30+ days)
  Churned: -200 (inactive 30+ days)
  End of week: 10,400 users
  Net growth: +4% (healthy)

Growth Rate = (End - Start) / Start × 100

Sustainable growth: growth rate stays constant or increases
```
## Step 15: Network Effects & Moats
```
Direct Network Effects (same-side):
  More users → more value for each user
  Examples: WhatsApp, Telegram, telephone network
  
Indirect Network Effects (cross-side):
  More users on side A → more value for side B
  Examples: Uber (riders ↔ drivers), Airbnb (guests ↔ hosts)
  
Data Network Effects:
  More users → more data → better product → more users
  Examples: Google Search, Waze, recommendation engines
  
Protocol Network Effects:
  More adopters → stronger standard → harder to switch
  Examples: TCP/IP, HTTP, Bitcoin, Ethereum
```
## Step 16: International Growth & Localization
```
Product:
□ i18n framework (react-intl, vue-i18n, next-intl)
□ String externalization (no hardcoded strings)
□ RTL support (Arabic, Hebrew, Farsi)
□ Date/time formatting (moment.js, date-fns, Intl)
□ Number formatting (currencies, decimals)
□ Address formats by country
□ Phone number validation (libphonenumber)

Pricing:
□ Multi-currency support
□ Purchasing power parity (PPP) pricing
□ Tax calculation by jurisdiction (Stripe Tax, Avalara)
□ Payment methods by region (iDEAL, SEPA, Alipay, UPI)

```
## Step 17: Product-Led Sales (PLG + Sales Hybrid)
```
Self-serve journey (PLG):
  Signup → Free/Freemium → Activate → Use → Hit limit → Upgrade

Product-qualified lead (PQL) signals:
  - Team workspace created (5+ users)
  - API calls exceed free tier
  - Admin features accessed
  - Integration connected
  - Export/download activity
  - Billing page visited

Sales touchpoints:
  1. In-app prompt: "Need help scaling? Talk to our team"
  2. PQL scoring: Auto-flag accounts hitting usage thresholds
  3. CSM outreach: Personal email from customer success
```
## Step 18: Viral Growth Engineering
```
Viral loop = action → expose → invite → activate → repeat

Types:
1. Collaboration viral: User invites teammate to shared workspace
   Example: Slack, Notion, Figma, Google Docs
   
2. Content viral: User creates public content that exposes product
   Example: Canva designs, Linktree, Notion templates
   
3. Value viral: User must share to get value (gaming, social)
   Example: Houseparty, Zoom (meeting host invites others)
   
4. Incentive viral: Referral rewards for both parties
   Example: Dropbox (500MB bonus), Uber (ride credits)

```
## Step 19: Product Analytics Deep Dive
```
Naming convention: [noun].[verb]
Examples:
  user.signed_up
  user.invited_team_member
  project.created
  project.deployed
  billing.upgraded
  billing.payment_failed

Event properties (context):
  user.signed_up:
    - signup_method: google | github | email
    - referrer: direct | organic | referral | paid
    - utm_source: twitter | google | newsletter
    - company_size: 1-10 | 11-50 | 51-200 | 200+
```
## Step 20: Pricing Strategy Deep Dive
```
Above the fold:
  - Clear value prop headline
  - 3-4 pricing tiers side by side
  - Monthly/Annual toggle (show savings)
  - Highlighted "Most Popular" tier

Tier structure:
  Free:        Limited features, usage caps, no support
  Pro:         Full features, higher limits, email support
  Business:    Team features, SSO, priority support, SLA
  Enterprise:  Custom pricing, dedicated CSM, custom SLA

Anchoring psychology:
  - Show Enterprise tier first (sets high anchor)
  - Highlight Pro as "Most Popular" (social proof)
```
## Step 21: Experimentation and A/B Testing
```
Hypothesis format:
  "If we [change], then [metric] will [direction] by [amount], 
  because [reasoning]."
  
  Example: "If we add social proof to the pricing page, then 
  conversion rate will increase by 15%, because seeing other 
  companies using the product reduces purchase anxiety."

Sample size calculation:
  Baseline conversion rate: 3%
  Minimum detectable effect (MDE): 15% relative (0.45% absolute)
  Statistical significance: 95% (alpha = 0.05)
  Statistical power: 80% (beta = 0.20)
  
  Formula: n = (Z_alpha/2 + Z_beta)^2 * (p1*(1-p1) + p2*(1-p2)) / (p1-p2)^2
```
## Step 22: Customer Journey Mapping
```
Stage 1: Awareness
  Touchpoints: Google search, social media, blog, ads, referral
  Questions: "What is this? Do I need it?"
  Content: Blog posts, social content, ads
  Metrics: Impressions, clicks, brand awareness
  
Stage 2: Consideration
  Touchpoints: Website, pricing page, docs, reviews, demos
  Questions: "Is this right for me? How does it compare?"
  Content: Comparison pages, case studies, webinars
  Metrics: Time on site, pages per session, demo requests
  
Stage 3: Decision
  Touchpoints: Sales call, trial, proposal, reference calls
  Questions: "Can I justify this? What's the ROI?"
```
## Step 23: Product Analytics SQL
```sql
-- Monthly cohort retention
WITH cohorts AS (
  SELECT 
    user_id,
    DATE_TRUNC('month', MIN(created_at)) AS cohort_month
  FROM users
  GROUP BY 1
),
activity AS (
  SELECT 
    c.cohort_month,
    DATE_TRUNC('month', e.created_at) AS activity_month,
    COUNT(DISTINCT c.user_id) AS active_users
  FROM cohorts c
  JOIN events e ON c.user_id = e.user_id
```
## Step 24: Retention Engineering
```
Features for churn prediction:
  1. Usage frequency (weekly active days)
  2. Feature adoption breadth (# features used)
  3. Support ticket sentiment (negative = churn risk)
  4. Login recency (days since last login)
  5. Contract value trend (declining = risk)
  6. Team size change (shrinking = risk)
  7. Integration activity (less = risk)
  8. NPS score (detractor = risk)

Scoring:
  Low risk (0-30): Regular engagement, expanding usage
  Medium risk (31-60): Declining engagement, flat usage
  High risk (61-100): Minimal usage, negative sentiment, shrinking team

```
## Step 25: Expansion Revenue
```
Usage-based triggers:
  - API calls > 80% of plan limit
  - Storage > 75% of plan limit
  - Team members > included seats
  - Projects > plan limit
  
Feature-based triggers:
  - Attempted to access premium feature
  - Viewed enterprise features page
  - Asked about SSO/SAML in support ticket
  - Requested audit logs or compliance features

Timing triggers:
  - 90 days before contract renewal
  - After successful product milestone (100th deploy)
```
## Step 26: Product-Led Onboarding
```
First-run experience:
  1. Welcome screen (value prop, 3 key benefits)
  2. Account setup (minimal fields, progressive disclosure)
  3. Quick win (achieve something meaningful in <5 min)
  4. Feature discovery (guided tour of key features)
  5. Next steps (clear path to deeper engagement)

Progressive onboarding:
  Day 1: Core feature (create first project)
  Day 3: Collaboration (invite team member)
  Day 7: Advanced feature (set up automation)
  Day 14: Integration (connect to existing tools)
  Day 30: Full adoption (use all key features)

Email sequence:
```
## Step 27: Product Feedback Loops
```
In-product:
  - NPS surveys (quarterly)
  - Feature feedback widgets
  - Bug report button
  - Feature request form
  - Usage analytics (implicit feedback)

External:
  - Customer interviews (monthly)
  - Support ticket analysis
  - Community forums
  - Social media monitoring
  - Review sites (G2, Capterra)
  - Sales team feedback

```
## Step 28: Growth Team Structure
```
Embedded model:
  - Growth engineers embedded in product teams
  - Each team runs own experiments
  - Centralized analytics support
  - Best for: Small companies (<50 engineers)

Centralized model:
  - Dedicated growth team
  - Owns activation, retention, monetization experiments
  - Works across all product areas
  - Best for: Mid-size companies (50-200 engineers)

Hybrid model:
  - Core growth team (strategy, analytics, experimentation)
  - Growth engineers in product teams
```
## Step 29: Product Analytics Tools
```
Data collection:
  - Segment: Event routing (collect once, send everywhere)
  - Rudderstack: Open-source alternative to Segment
  - Snowplow: Event data pipeline
  - Mixpanel SDK: Direct event tracking

Analysis:
  - Mixpanel: Event-based analytics, funnels, retention
  - Amplitude: Product analytics, behavioral cohorts
  - PostHog: Open-source, self-hostable
  - Heap: Auto-capture analytics

Visualization:
  - Looker: Enterprise BI
  - Tableau: Data visualization
```
## Step 30: Growth Experimentation
```
Hypothesis:
  "If we [change], then [metric] will [direction] by [amount], 
  because [reasoning]."

ICE Score (1-10 each):
  Impact: How much will this move the needle?
  Confidence: How sure are we it will work?
  Ease: How easy is it to implement?
  
  Score = (Impact + Confidence + Ease) / 3

Experiment log:
  | ID | Hypothesis | ICE | Status | Result | Learning |
  |----|------------|-----|--------|--------|----------|
  | EXP-001 | Social proof increases conversion | 8.0 | Complete | +12% | Works on pricing page |
```
## Step 31: Product Metrics Hierarchy
```
North Star Metric:
  - Single metric that best captures value delivered
  - Examples:
    - Slack: Weekly active teams sending 2000+ messages
    - Airbnb: Nights booked
    - Stripe: Weekly active merchants

Input metrics (leading indicators):
  - New users (acquisition)
  - Activation rate (onboarding)
  - Feature adoption (engagement)
  - Retention rate (stickiness)
  - Expansion revenue (monetization)

Output metrics (lagging indicators):
```