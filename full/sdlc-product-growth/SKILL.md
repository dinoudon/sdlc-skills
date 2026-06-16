---
name: sdlc-product-growth
description: "Product-led growth (PLG), developer-led growth, growth loops, activation funnels, A/B testing, SaaS metrics (MRR/ARR/LTV/CAC/NRR), unit economics, pricing strategy, monetization, onboarding optimization, feature gating, competitive analysis, growth hacking, referral programs, paywall design."
version: 6.0.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, product-growth, plg, growth-engineering, saas-metrics, pricing, monetization, experimentation, ab-testing, funnels, activation, retention, unit-economics, developer-led-growth]
    related_skills: [sdlc-prd-to-production, sdlc-requirements-engineering, sdlc-testing-qa, sdlc-developer-relations, sdlc-gtm-strategy, sdlc-finance-ops]
---

# Product Growth Engineering

Product-led growth, growth loops, experimentation, SaaS metrics, pricing strategy, and monetization. How unicorn companies drive revenue from product.

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

### The Spectrum

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

### Developer-Led Growth (DLG)
Source: Stripe, Twilio, SendGrid

Developer-led growth is a subset of PLG where developers are the primary adopters. The developer discovers, evaluates, integrates, and champions the product internally.

```
Developer discovers API → Reads docs → Gets API key → Builds integration
    → Shows team → Team adopts → Company buys enterprise plan
```

Key principles (from Stripe):
1. **7 lines of code** to first value — minimize time-to-integration
2. **Docs are the product** — API reference is the landing page
3. **Sandboxes for everyone** — test without talking to sales
4. **SDKs in every language** — meet developers where they are
5. **Transparent pricing** — no "contact sales" for basic features

### Product-Led Growth Flywheel
Source: OpenView Partners

```
                    ┌─────────────┐
                    │  EVALUATE   │
                    │ (Free trial  │
                    │  or freemium)│
                    └──────┬──────┘
                           │
           ┌───────────────┼───────────────┐
           ▼                               │
    ┌─────────────┐                 ┌──────┴──────┐
    │   ACTIVATE  │                 │   EXPAND    │
    │ (Time-to-   │                 │ (Upsell,    │
    │  value <5m) │                 │  cross-sell) │
    └──────┬──────┘                 └──────┬──────┘
           │                               │
           └───────────────┬───────────────┘
                           ▼
                    ┌─────────────┐
                    │   ADVOCATE  │
                    │ (Referral,  │
                    │  word-of-   │
                    │  mouth)     │
                    └─────────────┘
```

## Step 2: SaaS Metrics Framework

### Pirate Metrics (AARRR)
Source: Dave McClure, 500 Startups

| Stage | Metric | Definition | Target |
|-------|--------|------------|--------|
| **Acquisition** | Visitor → Signup rate | % of visitors who create account | 5-15% |
| **Activation** | Time-to-value | Time from signup to first success | <5 min |
| **Retention** | D1/D7/D30 retention | % returning after N days | D1: 40%+, D7: 20%+, D30: 10%+ |
| **Revenue** | MRR, ARPU | Monthly recurring revenue per user | Varies |
| **Referral** | Viral coefficient (k-factor) | Invites sent × conversion rate | k > 1 = viral |

### Core SaaS Metrics

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

### Healthy SaaS Benchmarks
Source: OpenView Partners, Bessemer Venture Partners

| Metric | Good | Great | Elite |
|--------|------|-------|-------|
| NRR | 100-110% | 110-130% | >130% |
| LTV:CAC | 3:1 | 5:1 | >7:1 |
| CAC Payback | 18 months | 12 months | <6 months |
| Gross Margin | 70% | 80% | >85% |
| Logo Churn (annual) | <10% | <5% | <3% |
| Rule of 40 | 40% | 50% | >60% |

**Rule of 40**: Growth rate (%) + Profit margin (%) should exceed 40%.

### Cohort Analysis Template

```
Cohort: January 2026 signups (1000 users)

Month 0: 1000 active (100%)
Month 1: 600 active (60%)   ← D30 retention
Month 2: 450 active (45%)
Month 3: 380 active (38%)
Month 6: 300 active (30%)   ← Long-term retention
Month 12: 250 active (25%)  ← Plateau

Revenue cohort:
Month 0: $5,000 MRR
Month 3: $7,200 MRR  ← expansion revenue kicks in
Month 6: $9,800 MRR  ← upgrades + seat expansion
Month 12: $15,000 MRR ← enterprise deals close
```

## Step 3: Pricing Strategy

### Pricing Models Compared

| Model | How It Works | Best For | Examples |
|-------|-------------|----------|----------|
| **Freemium** | Free tier + paid upgrades | PLG, viral products | Slack, Notion, Figma |
| **Free Trial** | Time-limited full access | Complex products | Salesforce, HubSpot |
| **Usage-Based** | Pay per API call/compute/GB | APIs, infrastructure | Stripe, AWS, Twilio |
| **Seat-Based** | Per-user per-month | Collaboration tools | Atlassian, GitHub |
| **Hybrid** | Base + usage overage | Mixed workloads | Datadog, Snowflake |
| **Tiered** | Good/Better/Best packages | Multiple segments | Zoom, Mailchimp |

### Stripe's Pricing Model (Case Study)

```
Starter:  2.9% + 30¢ per transaction  (no monthly fee)
Scale:    2.9% + 30¢ + volume discounts
Enterprise: Custom pricing + dedicated support

Key insight: Pricing aligns with customer success.
When customer processes more, Stripe earns more.
No upfront cost → zero friction to start.
```

### Pricing Page Template

```
┌─────────────────────────────────────────────────────────┐
│                    Pricing Plans                         │
├──────────────┬──────────────┬──────────────┬────────────┤
│   Free       │   Pro        │   Business   │ Enterprise │
│   $0/mo      │   $29/mo     │   $99/mo     │  Custom    │
├──────────────┼──────────────┼──────────────┼────────────┤
│ 3 users      │ 10 users     │ Unlimited    │ Unlimited  │
│ 1 project    │ 10 projects  │ Unlimited    │ Unlimited  │
│ Community    │ Email support│ Priority     │ Dedicated  │
│ Basic feat.  │ All features │ Advanced     │ Custom     │
│              │              │ SSO/SAML     │ SLA/On-prem│
├──────────────┼──────────────┼──────────────┼────────────┤
│ [Get Started]│ [Start Trial]│ [Start Trial]│ [Contact]  │
└──────────────┴──────────────┴──────────────┴────────────┘
```

### Feature Gating Framework

```
Tier 1 (Free):     Core functionality, single user, limited volume
Tier 2 (Pro):      Team features, integrations, higher limits
Tier 3 (Business): Admin controls, SSO, audit logs, compliance
Tier 4 (Enterprise): Custom, SLA, dedicated infra, white-glove

Gating rules:
- NEVER gate core value prop (that's the hook)
- Gate collaboration (teams need to pay)
- Gate compliance (enterprises expect to pay)
- Gate scale (volume needs to pay)
- Gate support (premium support = premium price)
```

## Step 4: Growth Loops & Viral Mechanics

### Growth Loops vs Funnels

Funnels are linear (acquire → activate → retain). Growth loops are circular — output of one cycle becomes input of next.

### Examples of Growth Loops

**Content Loop (Pinterest, Stack Overflow)**:
```
User creates content → Content indexed by SEO → New user discovers content
→ New user creates content → Loop repeats
```

**Viral Loop (Slack, Dropbox)**:
```
User invites teammate → Teammate signs up → Teammate invites others
→ Network effect increases value → More invites
```

**Collaboration Loop (Figma, Notion)**:
```
User shares doc → Recipient opens it → Recipient edits → Shared with more people
→ More people see value → More signups
```

**Payment Loop (Stripe, Square)**:
```
Merchant integrates Stripe → Customer pays → Merchant sees revenue
→ Merchant tells other merchants → More integrations
```

### Viral Coefficient (k-factor)

```
k = invites_per_user × conversion_rate_of_invites

k > 1.0 = exponential growth (viral)
k = 0.5-1.0 = significant boost to organic growth
k < 0.5 = limited viral effect

Example:
- Average user sends 3 invites
- 20% of invites convert to signups
- k = 3 × 0.20 = 0.6 (good but not viral)

To make viral:
- Increase invites (better sharing UX, incentives)
- Increase conversion (better invite content, social proof)
```

### Referral Program Design

```
Components:
1. Incentive: What does referrer get? (credits, features, cash)
2. Trigger: When to prompt? (after success moment, not onboarding)
3. Friction: How easy? (one-click share, pre-filled message)
4. Tracking: Attribution (referral codes, UTM, invite links)
5. Reward timing: Immediate or after referee activates?

Examples:
Dropbox: 500MB extra storage per referral (both sides)
Slack: Credit toward bill (referrer only)
Stripe: No formal referral (word-of-mouth from DX)
PayPal: $10 cash per referral (both sides) — early growth hack
```

## Step 5: Activation & Onboarding

### Time-to-Value (TTV) Framework

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

### Onboarding Patterns

```
1. Checklist: Show progress (5/7 steps complete)
2. Empty states: Guide to first action instead of blank page
3. Progressive disclosure: Don't show everything at once
4. Templates: Pre-built starting points
5. Tooltips: Contextual help at point of need
6. Interactive tutorial: Walk through core flow
7. Milestone emails: Triggered by behavior, not time
```

### Activation Funnel Template

```
Signup (100%)
  → Email verified (70%)
    → Profile completed (50%)
      → First action (core feature used) (35%)
        → Second session (D1 retention) (25%)
          → Teammate invited (15%)
            → Converted to paid (5%)
```

## Step 6: Experimentation & A/B Testing

### Experiment Design

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

### Experimentation Platforms

| Platform | Type | Best For |
|----------|------|----------|
| LaunchDarkly | Feature flags + experiments | Engineering-led |
| Optimizely | A/B testing | Marketing-led |
| Statsig | Feature gates + analytics | Product-led |
| GrowthBook | Open-source A/B | Budget-conscious |
| Amplitude Experiment | Analytics + testing | Data teams |
| PostHog | Open-source analytics | Self-hosted |

### Common A/B Testing Pitfalls

```
1. Peeking: Checking results before reaching sample size
2. Multiple testing: Running 20 variants, one "wins" by chance
3. Novelty effect: Users engage with change briefly, then revert
4. Sample ratio mismatch: 50/50 split becomes 48/52
5. Survivorship bias: Only measuring users who didn't churn
6. Network effects: Users in treatment affect control users
7. Segment dilution: Overall no effect, but huge effect for subset
```

## Step 7: Competitive Analysis

### Competitive Intelligence Framework

```
1. Feature comparison matrix
2. Pricing comparison
3. Market positioning map
4. SWOT analysis per competitor
5. Customer win/loss analysis
6. Technical architecture comparison
7. Developer experience comparison (docs, SDKs, sandbox)
```

### Positioning Map Template

```
                    ENTERPRISE
                        │
           Salesforce   │   ServiceNow
                        │
    ────────────────────┼────────────────────
    SELF-SERVE          │          SALES-ASSISTED
                        │
           Notion       │   Stripe
           Slack        │   Datadog
                        │
                    STARTUP/SMB
```

## Step 8: Unicorn Growth Playbooks

### Stripe: Developer-Led Growth
- API-first: 7 lines of code to first payment
- Docs-as-product: Best-in-class API documentation
- Sandbox: Full test environment, no signup required
- Transparent pricing: No hidden fees, no sales calls
- Expand: Start with payments → billing → connect → radar → atlas
- Result: $107B valuation, processes $1T+/year

### Slack: Viral Team Adoption
- Freemium with generous limits (10K messages)
- Bottom-up: Individual → team → company
- Integrations: 2400+ apps create switching costs
- Fun UX: Custom emoji, bots, playful design
- Growth loop: User invites → team grows → needs paid plan
- Result: Acquired by Salesforce for $27.7B

### Notion: PLG + Community
- Templates: Community-created templates drive discovery
- Power users become advocates (YouTube, Twitter)
- Flexible: Notes → docs → wiki → database → project management
- Education: Notion Academy, certification program
- Result: $10B valuation

### Figma: Collaboration as Growth
- Browser-based: Zero install, instant sharing
- Real-time collab: Like Google Docs for design
- Sharing loop: Designer shares link → stakeholder views → signs up
- Community: Figma Community for plugins and templates
- Result: Acquired by Adobe for $20B

### Zoom: Frictionless Experience
- One-click join: No account needed to join a meeting
- Reliability: "It just works" — superior video quality
- Free tier: 40-minute meetings (generous enough to hook)
- Viral: Every meeting invite is a product demo
- Result: IPO, peak $150B market cap

## Step 9: Growth Engineering (Technical Implementation)

### Event Tracking Architecture

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
    "referrer": "/onboarding",
    "device": "desktop"
  }
}
```

### Analytics Stack Options

| Layer | Tool | Type | Best For |
|-------|------|------|----------|
| Collection | Segment | SaaS | Multi-destination routing |
| Collection | RudderStack | Open-source | Self-hosted, cost control |
| Collection | PostHog | Open-source | All-in-one analytics+flags |
| Warehouse | BigQuery | Managed | Large-scale analytics |
| Warehouse | Snowflake | Managed | Enterprise data platform |
| Warehouse | ClickHouse | Open-source | Real-time analytics |
| BI | Metabase | Open-source | Self-serve dashboards |
| BI | Looker | Enterprise | Governed metrics layer |
| BI | Hex | Collaborative | Data team notebooks |

### Key Growth Queries (SQL)

```sql
-- Activation rate (users who performed key action within 7 days)
SELECT 
  DATE_TRUNC('week', u.created_at) AS cohort_week,
  COUNT(DISTINCT u.id) AS signups,
  COUNT(DISTINCT CASE WHEN a.event = 'key_action' 
    AND a.created_at < u.created_at + INTERVAL '7 days' THEN u.id END) AS activated,
  ROUND(activated * 100.0 / signups, 1) AS activation_rate
FROM users u
LEFT JOIN events a ON u.id = a.user_id
GROUP BY 1 ORDER BY 1;

-- Retention cohort (D1, D7, D30)
SELECT 
  DATE_TRUNC('month', u.created_at) AS cohort,
  COUNT(DISTINCT u.id) AS users,
  COUNT(DISTINCT CASE WHEN e.created_at::date = (u.created_at + 1)::date THEN u.id END) AS d1,
  COUNT(DISTINCT CASE WHEN e.created_at::date = (u.created_at + 7)::date THEN u.id END) AS d7,
  COUNT(DISTINCT CASE WHEN e.created_at::date = (u.created_at + 30)::date THEN u.id END) AS d30
FROM users u
LEFT JOIN events e ON u.id = e.user_id
GROUP BY 1 ORDER BY 1;

-- Revenue expansion per cohort
SELECT 
  DATE_TRUNC('month', p.started_at) AS cohort_month,
  SUM(p.mrr_at_start) AS starting_mrr,
  SUM(p.mrr_current) AS current_mrr,
  ROUND(SUM(p.mrr_current) * 100.0 / SUM(p.mrr_at_start), 1) AS nrr_pct
FROM subscriptions p
WHERE p.started_at >= NOW() - INTERVAL '12 months'
GROUP BY 1 ORDER BY 1;
```

### Feature Flagging for Growth

```
Use feature flags for:
1. Gradual rollouts (1% → 10% → 50% → 100%)
2. A/B testing (50/50 split, measure impact)
3. Kill switches (instantly disable broken features)
4. Segment targeting (beta users, enterprise, specific cohorts)

Tools: LaunchDarkly, Unleash, GrowthBook, PostHog, Statsig

Example:
  flag: new_onboarding_flow
  rollout: 20% of new signups
  metric: activation_rate (key_action within 7 days)
  guardrail: signup_completion_rate must not drop >5%
  duration: 14 days minimum
  decision: ship if activation +10% with p < 0.05
```

## Step 10: Retention & Engagement Deep Dive

### Retention Framework

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
  - "What's new" notifications for churned users
  - Surveys for churned users (why did you leave?)
  - Special offers or extended trials
```

### Engagement Scoring

```
Engagement Score = weighted sum of key actions

Actions and weights:
  - Login (1x)
  - Core feature used (5x)
  - Collaboration action (3x)
  - Settings/config changed (2x)
  - Integration connected (4x)
  - Teammate invited (6x)

Segments:
  Highly engaged:  score > 80  (power users, upsell targets)
  Engaged:         score 40-80 (healthy, maintain)
  At risk:         score 10-40 (intervention needed)
  Dormant:         score < 10  (win-back campaign)
```

### Churn Prediction Signals

```
Leading indicators of churn (2-4 weeks before):
1. Login frequency drops >50%
2. Core feature usage stops
3. Support tickets spike (frustration)
4. Team admin removes integrations
5. Billing page visited (price shopping)
6. Export/download activity (data portability)
7. Cancellation page visited
8. NPS score drops below 6

Interventions:
- In-app nudge: "Need help with [feature]?"
- Email: Personal outreach from CSM
- Offer: Discount, extended trial, plan downgrade option
- Product: Simplify workflow, offer concierge onboarding
```

## Step 11: Growth Team Structure

### Growth Team Roles

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

### Growth Team Charter

```
Mission: Increase the rate at which users discover and experience core value.

Scope:
- Signup flow optimization
- Onboarding and activation
- Feature adoption and engagement
- Pricing and packaging experiments
- Viral and referral mechanics

NOT in scope:
- Core product features (product team)
- Enterprise sales (sales team)
- Brand marketing (marketing team)
- Infrastructure (platform team)
```

### Growth Operating Cadence

```
Monday:    Experiment review (last week's results)
Tuesday:   Experiment planning (this week's experiments)
Wednesday: Implementation (build experiments)
Thursday:  Analysis (review running experiments)
Friday:    Documentation (record learnings, update playbook)

Monthly:   Growth review with leadership
Quarterly: Strategy review (what bets to place next)
```

## Step 12: Metrics Dashboard Templates

### Executive Growth Dashboard

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
└─────────────────────────────────────────────────────────┘
```

### Retention Cohort Heatmap

```
         Month 0  Month 1  Month 2  Month 3  Month 6  Month 12
Jan '26  100%     62%      48%      42%      35%      28%
Feb '26  100%     65%      51%      44%      —        —
Mar '26  100%     68%      54%      —        —        —
Apr '26  100%     70%      —        —        —        —

Trend: D1 retention improving (62% → 70%) ← onboarding changes working
```

## Step 13: Growth Playbook Templates

### Experiment Backlog Template

| ID | Hypothesis | Metric | MDE | Sample | Duration | Status |
|----|------------|--------|-----|--------|----------|--------|
| EXP-001 | New onboarding increases activation | D7 activation | 10% | 5K users | 14d | Running |
| EXP-002 | Social proof on pricing page increases conversion | Signup→paid | 15% | 3K visitors | 14d | Planned |
| EXP-003 | In-app referral prompt increases invites | K-factor | 20% | 2K users | 21d | Planned |
| EXP-004 | Usage-based pricing reduces churn | Monthly churn | 2pts | 1K subs | 30d | Backlog |

### Growth Experiment Template

```
## Experiment: [Name]

### Hypothesis
If we [change], then [metric] will [improve/decrease] by [amount]
because [reasoning].

### Design
- Variant A (control): [current state]
- Variant B (treatment): [proposed change]
- Split: 50/50, user-level randomization
- Duration: [X days minimum]
- Sample size: [N per variant]

### Primary Metric
[Name] — [definition] — [how measured]

### Guardrail Metrics
- [Metric that must not degrade]
- [Metric that must not degrade]

### Results
- Variant A: [value] (n=[N])
- Variant B: [value] (n=[N])
- Lift: [X%] (p-value: [Y])
- Decision: [Ship / Iterate / Kill]

### Learnings
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

## Sources

- Reforge: https://www.reforge.com/blog
- Teresa Torres, Continuous Discovery Habits: https://www.producttalk.org/
- Lenny Rachitsky Newsletter: https://www.lennysnewsletter.com/
- OpenView Partners PLG: https://openviewpartners.com/blog/
- Bessemer Cloud Index: https://www.bvp.com/atlas
- a16z Growth: https://a16z.com/growth/
- Dave McClure Pirate Metrics: https://500.co/
- Stripe engineering: https://stripe.com/blog/engineering
- Slack growth story: https://slack.com/blog
- Notion growth: https://www.notion.so/blog
