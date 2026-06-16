---
name: sdlc-gtm-strategy
description: "Go-to-market strategy: market positioning, pricing, packaging, sales enablement, competitive analysis, launch planning, distribution channels, PLG vs SLG, developer marketing, enterprise sales, partnership strategy, category creation."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, gtm, go-to-market, pricing, sales, marketing, launch, distribution, enterprise-sales, partnerships]
    related_skills: [sdlc-product-growth, sdlc-developer-relations, sdlc-prd-to-production, sdlc-finance-ops]
---

# Go-to-Market Strategy

How software companies bring products to market. From launch to scale.

## When to Use

Trigger when user:
- Plans product launch or market entry
- Designs pricing or packaging strategy
- Builds sales enablement materials
- Plans distribution channels
- Evaluates PLG vs sales-led motion
- Prepares enterprise sales playbook
- Creates competitive positioning
- Plans partnership or channel strategy

## Step 1: GTM Motion Selection

### Three GTM Motions

```
┌─────────────────────────────────────────────────────────┐
│                  GTM Motion Spectrum                     │
├──────────────┬──────────────┬───────────────────────────┤
│  PRODUCT-LED │  HYBRID      │  SALES-LED                │
│  (PLG)       │  (PLG + SLG) │  (SLG)                    │
│              │              │                           │
│ Self-serve   │ Self-serve   │ Outbound sales            │
│ Freemium     │ + Sales      │ Enterprise deals          │
│ Viral        │ assist       │ Procurement               │
│              │              │                           │
│ $0-50 ACV    │ $5K-100K ACV │ $100K+ ACV                │
│              │              │                           │
│ Slack, Zoom  │ Stripe,      │ Salesforce,               │
│ Notion, Figma│ Datadog,     │ Oracle, Workday           │
│              │ MongoDB      │                           │
└──────────────┴──────────────┴───────────────────────────┘
```

### When to Use Each

| Signal | PLG | Hybrid | Sales-Led |
|--------|-----|--------|-----------|
| Buyer = developer | ✅ | ✅ | ❌ |
| ACV < $10K | ✅ | ✅ | ❌ |
| Long sales cycle OK | ❌ | ✅ | ✅ |
| Complex implementation | ❌ | ✅ | ✅ |
| Network effects | ✅ | ✅ | ❌ |
| Compliance-heavy industry | ❌ | ✅ | ✅ |

## Step 2: Launch Planning

### Launch Timeline

```
T-8 weeks:  Define positioning, messaging, target audience
T-6 weeks:  Create assets (landing page, demo, docs, blog post)
T-4 weeks:  Beta/early access (50-100 users for feedback)
T-2 weeks:  Press/analyst briefings, influencer outreach
T-1 week:   Internal enablement (sales, support, CS)
T-0:        LAUNCH (Product Hunt, HN, Twitter, blog)
T+1 week:   Follow-up content (case studies, tutorials)
T+2 weeks:  Measure results, iterate on messaging
T+4 weeks:  Expand to additional channels
```

### Launch Checklist

```
Product:
□ Feature-complete and tested
□ Performance benchmarks passing
□ Security review complete
□ Documentation complete
□ API reference published

Marketing:
□ Landing page live
□ Blog post written
□ Demo video recorded
□ Social media posts scheduled
□ Email announcement drafted

Sales:
□ Sales deck updated
□ Pricing page live
□ Competitive battlecard ready
□ Demo script prepared
□ FAQ document ready

Support:
□ Knowledge base articles
□ Support team trained
□ Escalation paths defined
□ Monitoring dashboards ready
```

## Step 3: Positioning & Messaging

### Positioning Statement Template
Source: April Dunford, Obviously Awesome

```
For [target customer]
Who [statement of need or opportunity]
The [product name] is a [product category]
That [statement of key benefit / compelling reason to buy]
Unlike [primary competitive alternative]
Our product [statement of primary differentiation]
```

### Example (Stripe)

```
For internet businesses
Who need to accept payments online
Stripe is a payment infrastructure platform
That lets developers integrate payments with 7 lines of code
Unlike PayPal or legacy payment processors
Our product is developer-first with best-in-class APIs and documentation
```

### Messaging Hierarchy

```
Level 1: Category (what you are)
  "Payment infrastructure for the internet"

Level 2: Value prop (why you matter)
  "Increase revenue with a payments platform built for growth"

Level 3: Proof points (how you deliver)
  "7 lines of code to first payment"
  "99.999% uptime"
  "$1T+ processed annually"

Level 4: Feature details (what you built)
  "Support for 135+ currencies"
  "Machine learning fraud detection"
  "Real-time reporting dashboard"
```

## Step 4: Enterprise Sales Playbook

### Enterprise Sales Stages

```
1. Prospecting: Identify target accounts (ICP matching)
2. Outreach: Cold email, LinkedIn, events, referrals
3. Discovery: Understand pain, budget, timeline, decision-makers
4. Demo: Tailored demo showing solution to their specific pain
5. Technical evaluation: POC, security review, architecture review
6. Business case: ROI analysis, cost comparison
7. Negotiation: Pricing, terms, SLA, contract
8. Close: Legal review, procurement, signature
9. Onboarding: Implementation, training, go-live
10. Expansion: Upsell, cross-sell, renewal
```

### Enterprise Sales Metrics

| Metric | Target | World-Class |
|--------|--------|-------------|
| Win rate | 20-25% | 30%+ |
| Sales cycle | 90-180 days | <90 days |
| ACV | $50K-500K | $1M+ |
| Net retention | 110% | 130%+ |
| CAC payback | 18 months | <12 months |
| Quota attainment | 60-70% | 80%+ |

### Sales Enablement Materials

```
1. Sales deck (10-15 slides)
2. Product demo script
3. Competitive battlecard (per competitor)
4. ROI calculator
5. Case studies (3-5 per vertical)
6. Security questionnaire (pre-filled)
7. Technical architecture overview
8. Pricing calculator
9. Contract templates (MSA, DPA, SLA)
10. Objection handling guide
```

## Step 5: Distribution Channels

### Channel Strategy

| Channel | Type | Best For | Examples |
|---------|------|----------|----------|
| **Direct sales** | Owned | Enterprise, high ACV | Salesforce, Workday |
| **Self-serve** | Owned | PLG, developer tools | Stripe, Notion |
| **Marketplace** | Partner | Cloud apps | AWS, GCP, Azure Marketplace |
| **Reseller** | Partner | Regional reach | VARs, SIs, consultancies |
| **Affiliate** | Partner | Content-driven | Review sites, comparison blogs |
| **OEM/embedded** | Partner | White-label | SDK embedded in other products |
| **Open source** | Community | Developer tools | MongoDB, Elastic, GitLab |

### Marketplace Strategy (AWS/GCP/Azure)

```
Benefits:
- Reach: Millions of cloud customers
- Billing: Consolidated through existing cloud bill
- Trust: Cloud provider endorsement
- Co-sell: Joint sales with cloud reps

Requirements:
- Technical integration (listing, metering)
- Security review (AWS Foundational Technical Review)
- Pricing alignment (typically 15-20% marketplace fee)
- Support SLA alignment
```

## Step 6: Competitive Intelligence

### Competitive Battlecard Template

```
┌─────────────────────────────────────────┐
│     BATTLECARD: Us vs [Competitor]       │
├─────────────────────────────────────────┤
│ When They Win:                           │
│ • [Specific scenario]                    │
│ • [Specific scenario]                    │
│                                          │
│ When We Win:                             │
│ • [Specific scenario]                    │
│ • [Specific scenario]                    │
│                                          │
│ Their Pitch:                             │
│ • "[Their main value prop]"              │
│                                          │
│ Our Counter:                             │
│ • "[How we respond]"                     │
│                                          │
│ Trap-Setting Questions:                  │
│ • "Have you considered [our strength]?"  │
│ • "What happens when [their weakness]?"  │
│                                          │
│ Pricing Comparison:                      │
│ • Them: [pricing model]                  │
│ • Us: [pricing model]                    │
│ • Delta: [cost difference]               │
└─────────────────────────────────────────┘
```

## Step 7: Partnership Strategy

### Partnership Types

```
1. Technology: Integrate with complementary tools
   Example: Stripe + Shopify (payment processing)

2. Channel: Resellers, distributors, VARs
   Example: Snowflake + Deloitte (implementation)

3. Strategic: Co-development, co-marketing
   Example: AWS + Anthropic (AI infrastructure)

4. Community: Open source, developer ecosystem
   Example: Vercel + Next.js (framework + hosting)
```

### Partnership Evaluation

```
Criteria:
1. Strategic fit (aligned vision, complementary products)
2. Market reach (new customers, new segments)
3. Technical effort (integration complexity)
4. Revenue potential (direct + indirect)
5. Brand value (association with partner)
6. Exclusivity (open vs exclusive)
7. Resource requirement (team, support, maintenance)
```

## Step 7: GTM Metrics & KPIs

### GTM Funnel Metrics

```
AWARENESS → INTEREST → EVALUATION → PURCHASE → EXPANSION
  │            │           │            │           │
Website      Demo        POC/Trial    Closed     Upsell
visitors     requests    starts       deals      revenue
  │            │           │            │           │
100K/mo      500/mo      200/mo       50/mo      $10K/mo
  ↓            ↓           ↓            ↓           ↓
2% → Demo  40% → POC   25% → Close  20% → Expand
```

### GTM Team Structure

```
Series A (10-30 employees):
├── Head of Sales (or founder-led sales)
├── 2-3 SDRs (outbound prospecting)
├── 2-3 AEs (closing deals)
├── 1 Marketing generalist
└── 1 CS manager

Series B (30-100 employees):
├── VP Sales
├── Sales team (SDRs → AEs → closers)
├── VP Marketing (demand gen + product marketing)
├── CS team (onboarding + retention)
├── Solutions Engineering (technical pre-sales)
└── Rev Ops (pipeline analytics, tooling)

Series C+ (100+ employees):
├── CRO (Chief Revenue Officer)
├── Regional sales teams
├── Enterprise vs SMB split
├── Full marketing org (demand gen, brand, PMM, DevRel)
├── Customer Success org (CSMs, support, training)
├── Revenue Operations (analytics, process, tooling)
└── Partnerships / Channel team
```

### Sales Compensation Models

```
SaaS Sales Compensation:

SDR (Sales Development Rep):
  Base: $50K-$70K
  Variable: $30K-$50K (based on qualified meetings/opportunities created)
  OTE: $80K-$120K

AE (Account Executive):
  Base: $80K-$120K
  Variable: $80K-$120K (based on closed revenue)
  OTE: $160K-$240K
  Quota: $400K-$800K ARR/year

Enterprise AE:
  Base: $120K-$160K
  Variable: $120K-$160K
  OTE: $240K-$320K
  Quota: $800K-$1.5M ARR/year

Solutions Engineer:
  Base: $100K-$140K
  Variable: $30K-$60K (tied to AE quota attainment)
  OTE: $130K-$200K
```

## Step 8: Product Marketing

### Positioning Document Template

```
## Product Positioning

### Target Market
Primary: [Company size, industry, role]
Secondary: [Adjacent segment]

### Problem Statement
[Target customer] struggles with [problem] because [root cause].
This results in [business impact: lost revenue, wasted time, risk].

### Solution
[Product] is a [category] that [key differentiator].
Unlike [alternatives], we [unique value proposition].

### Key Messages
1. [Message 1 — for awareness]
2. [Message 2 — for evaluation]
3. [Message 3 — for decision]

### Proof Points
- [Customer quote or case study]
- [Metric or benchmark]
- [Award or recognition]

### Competitive Position
Against [Competitor A]: We win on [X], they win on [Y]
Against [Competitor B]: We win on [X], they win on [Y]
```

### Content for Each GTM Stage

```
Awareness (top of funnel):
  - Blog posts (SEO-optimized)
  - Industry reports
  - Podcast appearances
  - Social media content
  - Conference talks

Interest (middle of funnel):
  - Webinars
  - Case studies
  - Comparison guides
  - ROI calculators
  - Product demos

Evaluation (bottom of funnel):
  - Free trial / sandbox
  - Technical documentation
  - Security questionnaire
  - Reference customers
  - POC support

Purchase (decision):
  - Proposal templates
  - Contract negotiation
  - Implementation plan
  - Executive sponsor alignment

Expansion (post-sale):
  - Quarterly business reviews
  - New feature announcements
  - Training and certification
  - Community engagement
```

## Pitfalls

1. **Launching without positioning** — If you can't explain what you do in one sentence, you're not ready to launch.
2. **PLG without product-market fit** — PLG amplifies a good product. It can't save a bad one.
3. **Enterprise sales too early** — Enterprise deals take 6+ months. Don't go enterprise before you have $1M ARR from self-serve.
4. **Pricing by gut feel** — Use Van Westendorp, Gabor-Granger, or A/B testing. Don't guess.
5. **Ignoring competitors** — "We have no competitors" means you haven't looked hard enough or you're in a dead market.
6. **Feature-driven positioning** — "We have 50 features" isn't positioning. "We solve X problem" is.
7. **One channel dependence** — Relying only on paid ads or only on organic is risky. Diversify.
8. **No sales-marketing alignment** — Marketing generates leads sales can't close. Align on ICP and qualification criteria.
9. **Premature internationalization** — Get PMF in one market before expanding globally.
10. **Copy-paste GTM** — What worked at Company A won't work at Company B. Adapt to your context.

## Step 12: Channel Strategy

### Distribution Channel Matrix

```
Channel Type        | Best For              | Examples
────────────────────┼───────────────────────┼─────────────────
Direct sales        | Enterprise ($50K+ ACV)| Salesforce, Palantir
Inside sales        | Mid-market ($5-50K)   | HubSpot, Datadog
Self-serve          | SMB/Developer (<$5K)  | Slack, Notion
Marketplace         | Platform ecosystems   | AWS, Salesforce AppExchange
Channel partners    | Geographic expansion  | Regional resellers
OEM/embedded        | White-label           | Embedded analytics
Affiliate           | Content-driven        | Review sites, bloggers
```

### Partner Program Tiers

```
Tier 1: Registered Partner
  Requirements: Sign partner agreement
  Benefits: Logo usage, partner portal access
  Revenue share: 10-15%
  
Tier 2: Silver Partner
  Requirements: 5+ certified sales reps, $50K annual revenue
  Benefits: Co-marketing, deal registration, NFR licenses
  Revenue share: 15-20%
  
Tier 3: Gold Partner
  Requirements: 10+ certified reps, $200K annual revenue
  Benefits: Dedicated partner manager, joint webinars, MDF
  Revenue share: 20-25%
  
Tier 4: Platinum Partner
  Requirements: 20+ certified reps, $1M annual revenue
  Benefits: Executive sponsor, joint product development
  Revenue share: 25-30%
```

## Step 13: Sales Enablement

### Sales Battle Card Template

```
COMPETITOR: [Name]

Overview:
  - Founded: [Year]
  - Funding: [$X series Y]
  - Market position: [Leader/Challenger/Niche]
  
Strengths:
  1. [Strength 1]
  2. [Strength 2]
  3. [Strength 3]
  
Weaknesses:
  1. [Weakness 1] — Our advantage: [How we win]
  2. [Weakness 2] — Our advantage: [How we win]
  3. [Weakness 3] — Our advantage: [How we win]
  
Common objections when they're evaluating us vs them:
  Objection: "[Competitor is cheaper]"
  Response: "Our TCO is lower because [reason]. Here's the ROI calculation..."
  
  Objection: "[Competitor has more features]"
  Response: "We focus on [core use case] where we're 10x better. Here's proof..."
  
Win/loss intelligence:
  - We win when: [Ideal customer profile]
  - We lose when: [When competitor is better fit]
  - Typical deal cycle: [X weeks]
  - Average deal size: [$X]
```

### Demo Script Template

```
Demo structure (30 minutes):

1. Discovery recap (5 min)
   "Based on our conversation, your main challenges are [X, Y, Z]. 
   Let me show you how we solve each one."

2. Problem → Solution (15 min)
   For each pain point:
   - Show the problem (current state)
   - Demonstrate the solution (our product)
   - Quantify the impact (time saved, revenue gained)
   
3. Technical deep dive (5 min)
   - Architecture overview
   - Integration points
   - Security/compliance features
   
4. Next steps (5 min)
   - Recap key takeaways
   - Propose trial/pilot
   - Define timeline and stakeholders


- April Dunford, Obviously Awesome: https://www.aprildunford.com/
- Winning by Design (sales): https://winningbydesign.com/
- Pavilion (revenue leaders): https://joinpavilion.com/
- Cloud marketplaces: https://aws.amazon.com/marketplace/
- Product Hunt launch guide: https://www.producthunt.com/
- Kyle Poyar, OpenView: https://openviewpartners.com/blog/
- a16z go-to-market: https://a16z.com/
- First Round Review: https://review.firstround.com/
- Y Combinator Startup School: https://www.startupschool.org/
- Bessemer Venture Partners: https://www.bvp.com/atlas


## Step 14: Go-to-Market Execution

### Launch Checklist

```
Pre-launch (4 weeks before):
  □ Messaging finalized (positioning doc approved)
  □ Sales enablement complete (battle cards, demo script)
  □ Marketing assets ready (landing page, blog post, emails)
  □ PR outreach started (embargo set, press kit sent)
  □ Product ready (feature complete, QA passed)
  □ Support ready (FAQ, escalation paths, training)
  □ Pricing page updated
  □ Analytics tracking verified

Launch day:
  □ Product feature enabled (feature flag flipped)
  □ Blog post published
  □ Email blast sent (segmented by persona)
  □ Social media posts scheduled
  □ PR embargo lifted
  □ Sales team notified (Slack/email)
  □ Customer success briefed
  □ Monitoring active (errors, performance)

Post-launch (1-4 weeks):
  □ Daily metrics review (signups, activation, revenue)
  □ Customer feedback collection (NPS, support tickets)
  □ Sales feedback loop (objections, competitive intel)
  □ Content amplification (social, communities, forums)
  □ Iteration based on feedback
  □ Retrospective (2 weeks post-launch)
```

### Sales Enablement Package

```
Assets to prepare:
  1. One-pager (PDF, 1 page)
  2. Battle card (per competitor)
  3. Demo script (30-min standard demo)
  4. ROI calculator (spreadsheet or web tool)
  5. Case study library
  6. Email templates (cold, follow-up, re-engagement)
  7. Objection handling guide
```

## Step 15: Marketing Metrics & Attribution

### Attribution Models

```
1. First-touch: Credit to first interaction
2. Last-touch: Credit to final interaction
3. Multi-touch linear: Equal credit to all touches
4. Multi-touch U-shaped: 40% first, 40% last, 20% middle
5. Multi-touch W-shaped: 30% first, 30% lead, 30% opportunity, 10% rest
```

### Marketing Dashboard Metrics

```
Awareness: Traffic, impressions, brand mentions, share of voice
Acquisition: MQLs, CPL, conversion rate, landing page performance
Activation: MQL-to-SQL, sales cycle, demo conversion
Revenue: CAC, CAC payback, pipeline generated, win rate
Retention: NRR, CLV, churn by channel, expansion revenue
```

## Step 16: Competitive Intelligence

### Competitive Monitoring

```
Sources:
  - G2/Capterra reviews (customer sentiment)
  - Glassdoor (employee sentiment, hiring signals)
  - Job postings (technology signals, expansion)
  - Patent filings (innovation direction)
  - Press releases (partnerships, funding)
  - Social media (messaging changes)
  - Product Hunt launches (new features)
  - Conference talks (strategy signals)

Tools:
  - Klue: Competitive intelligence platform
  - Crayon: Market intelligence
  - Kompyte: Competitive tracking
  - Prisync: Pricing intelligence
  - Owler: Company insights
```

## Step 17: Partnership Marketing

### Co-Marketing Playbook

```
Types:
  1. Joint webinar (shared audience, shared effort)
  2. Co-authored content (blog, whitepaper, guide)
  3. Integration announcement (product synergy)
  4. Joint case study (shared customer success)
  5. Cross-promotion (newsletter swaps, social)
  6. Event sponsorship (shared booth, speaking)

Partnership selection criteria:
  - Audience overlap (30-70% ideal)
  - Non-competitive product
  - Similar brand positioning
  - Complementary value prop
  - Active marketing team

ROI measurement:
  - Leads generated (attributed to partner)
  - Pipeline influenced
  - Revenue closed
  - Brand awareness lift
  - Content engagement


## Step 18: Customer Segmentation

### Segmentation Framework

```
Firmographic segmentation:
  Company size: SMB (1-50), Mid-market (51-500), Enterprise (500+)
  Industry: Technology, Healthcare, Finance, Retail, Manufacturing
  Geography: North America, Europe, APAC, LATAM
  Revenue: <$1M, $1-10M, $10-100M, $100M+
  Funding stage: Seed, Series A/B/C, Public

Behavioral segmentation:
  Usage patterns: Power user, regular, occasional, dormant
  Feature adoption: Core only, advanced, enterprise features
  Engagement: High (weekly), Medium (monthly), Low (quarterly)
  Support behavior: Self-serve, mixed, high-touch

Needs-based segmentation:
  Price-sensitive: Value-driven, cost-conscious
  Feature-rich: Need advanced capabilities
  Ease-of-use: Want simplicity, quick setup
  Enterprise: Need compliance, SSO, SLA
  Developer: Want API access, customization

Segment prioritization matrix:
  | Segment | Market Size | Fit | Competition | Priority |
  |---------|-------------|-----|-------------|----------|
  | Dev tools| Large      | High| Medium      | 1        |
  | Enterprise| Large     | Med | High        | 2        |
  | SMB      | Very Large | High| Low         | 3        |
```

### Ideal Customer Profile (ICP)

```
ICP definition:
  Company:
    - B2B SaaS company
    - 50-500 employees
    - $5M-$50M ARR
    - Series A-C funded
    - Technical team >30%
    - Using modern stack (React, Node, Python)
  
  Buyer:
    - Title: VP Engineering, CTO, Head of Platform
    - Pain: Developer productivity, deployment speed
    - Budget: $50K-$200K annual
    - Decision process: Technical evaluation then Business case then Procurement
  
  Timing:
    - Recent funding round
    - Scaling team (hiring 10+ engineers)
    - Migrating to cloud/microservices
    - Compliance requirements (SOC 2, ISO)

ICP scoring model:
  - Company size match: 0-25 points
  - Industry match: 0-25 points
  - Technology fit: 0-25 points
  - Timing signals: 0-25 points
  - Score >70: High priority
  - Score 40-70: Medium priority
  - Score <40: Low priority
```

## Step 19: Product Positioning

### Positioning Statement Template

```
For [target customer]
Who [statement of need or opportunity]
The [product name] is a [product category]
That [statement of key benefit]
Unlike [competitive alternative]
Our product [statement of primary differentiation]

Example:
For engineering teams at B2B SaaS companies
Who struggle with slow deployment cycles and unreliable infrastructure
DeployFast is a continuous deployment platform
That reduces deployment time from hours to minutes with zero-downtime releases
Unlike Jenkins or GitHub Actions
Our product provides intelligent rollback, canary deployments, and production monitoring in one integrated platform
```

### Messaging Hierarchy

```
Level 1: Brand message (company-wide)
  "We help engineering teams ship software faster and more reliably."

Level 2: Product message (per product)
  "DeployFast automates your deployment pipeline with intelligent 
  rollback and production monitoring."

Level 3: Feature message (per feature)
  "Canary deployments let you test changes with 1% of traffic 
  before full rollout, catching issues before they impact users."

Level 4: Persona message (per audience)
  For CTO: "Reduce deployment risk while accelerating delivery."
  For VP Eng: "Give your team confidence to deploy anytime."
  For DevOps: "Automate rollbacks, monitor canaries, sleep better."

Message testing:
  - A/B test landing page headlines
  - Survey existing customers on messaging resonance
  - Test ad copy variations
  - Monitor win/loss reasons for messaging effectiveness
```

## Step 20: Channel Marketing

### Multi-Channel Strategy

```
Owned channels:
  Website: Conversion-optimized, SEO-focused
  Blog: Thought leadership, SEO content
  Email: Nurture sequences, product updates
  Social: LinkedIn (B2B), Twitter (dev community)
  Community: Slack, Discord, forums

Earned channels:
  PR: Industry publications, tech press
  Analysts: Gartner, Forrester, IDC
  Reviews: G2, Capterra, TrustRadius
  Referrals: Customer advocacy program
  Partners: Co-marketing, integrations

Paid channels:
  Search: Google Ads (high intent)
  Social: LinkedIn Ads (B2B targeting)
  Display: Retargeting, brand awareness
  Sponsorships: Newsletters, podcasts, events
  Affiliates: Commission-based partnerships

Channel mix by stage:
  Seed: Content, community, direct outreach
  Series A: +Paid search, events, PR
  Series B: +ABM, analyst relations, partner program
  Series C+: +Brand, TV/video, global expansion
```

---

## Pricing Strategy Deep Dive

### Pricing Models

**Cost-Plus Pricing**
```
Formula: Cost + Markup = Price
Example:
  COGS per unit: $50
  Target margin: 60%
  Price = $50 / (1 - 0.60) = $125

Pros: Simple, guaranteed margin
Cons: Ignores willingness-to-pay, competitive dynamics
Best for: Physical goods, professional services
```

**Value-Based Pricing**
```
Framework:
  1. Quantify customer value (ROI calculator)
  2. Segment by value perception
  3. Set price as % of value delivered (10-30% rule)
  4. Validate with willingness-to-pay research

Value Equation:
  Value = (Gain - Pain) / Risk
  Price ≤ Value × 0.25 (captures 25% of value created)

Example:
  Saves customer $500K/year → Price: $50-125K/year
  Reduces risk by $1M → Price: $100-250K/year
```

**Competitive Pricing**
```
Strategy Matrix:
  Premium: 20-50% above market (differentiated product)
  Parity: ±10% of market (commodity categories)
  Penetration: 20-50% below market (market share grab)
  Freemium: $0 base + paid tiers (PLG motion)

Competitive intel sources:
  - G2/Capterra price filters
  - Competitor pricing pages (Wayback Machine)
  - Sales team win/loss reports
  - Mystery shopping
  - Industry analyst reports
```

**Usage-Based Pricing**
```
Models:
  Per-user: $X/seat/month (SaaS standard)
  Per-transaction: $X/transaction (payments, API)
  Per-unit: $X/GB, $X/compute hour (infrastructure)
  Per-feature: Base + premium features (modular)
  Hybrid: Base fee + usage overage (telecom, cloud)

Metrics that scale:
  Good: Users, transactions, revenue processed
  Bad: CPU cycles, API calls (misaligned incentives)

Slack example (2022):
  Free → Pro ($7.25/user/mo) → Business ($12.50) → Enterprise (custom)
  Usage signals: Message history, integrations, compliance
```

### Packaging Strategies

**Good-Better-Best Framework**
```
Tier Design Principles:
  Good (Starter): 
    - Solve core problem
    - Limited to small teams
    - Self-serve support
    - Price anchor: $29-49/mo

  Better (Professional): [TARGET TIER]
    - Full feature set
    - Team collaboration
    - Priority support
    - Price: 2-3x Starter
    - Target: 60-70% of revenue

  Best (Enterprise):
    - Everything in Pro
    - Advanced security/compliance
    - Dedicated support/CSM
    - Custom integrations
    - Price: 5-10x Starter
    - Target: 20-30% of revenue

Decoy Effect:
  Make "Better" the obvious choice by positioning "Best" as expensive
  Creates anchor that makes "Better" feel like great value
```

**Feature Packaging Matrix**
```
Feature                  | Starter | Pro    | Enterprise
-------------------------|---------|--------|----------
Core functionality       | ✓       | ✓      | ✓
Users                    | 5       | 25     | Unlimited
Storage                  | 10GB    | 100GB  | Unlimited
API access               | ✗       | ✓      | ✓
Custom integrations      | ✗       | 3      | Unlimited
SSO/SAML                 | ✗       | ✗      | ✓
Audit logs               | ✗       | 30d    | 1 year
Dedicated support        | ✗       | Email  | 24/7 phone
SLA                      | ✗       | 99.9%  | 99.99%
Data residency           | ✗       | ✗      | ✓

Rule: Each tier should have 2-3 "must-have" features that force upgrade
```

### Discount Strategy

**Discount Types and Guidelines**
```
Volume Discounts:
  10-49 seats: 0% off (list price)
  50-99 seats: 10% off
  100-249 seats: 15% off
  250-499 seats: 20% off
  500+ seats: 25% off (requires exec approval)

Term Discounts:
  Monthly: List price
  Annual: 15-20% off (standard)
  2-year: 25% off
  3-year: 30% off (requires VP approval)

Strategic Discounts:
  Startup program: 50% off for 1 year (< $5M funding)
  Nonprofit: 25-50% off
  Education: 50-75% off
  Partner/reseller: 20-30% off
  Logo/press: Additional 10% for case study rights

Discount Approval Matrix:
  0-10%: Rep discretion
  11-20%: Sales manager approval
  21-30%: VP Sales approval
  31%+: CEO/CRO approval
```

**Price Testing Methodologies**
```
Van Westendorp Price Sensitivity Meter:
  Ask 4 questions to target market (n ≥ 100):
  1. At what price would this be too cheap you'd doubt quality?
  2. At what price is this a bargain (great value)?
  3. At what price is this getting expensive (but you'd still consider)?
  4. At what price is this too expensive (would never buy)?

  Plot responses → find optimal price range
  PMC (Point of Marginal Cheapness): Below = quality concerns
  PME (Point of Marginal Expensiveness): Above = too costly
  OPP (Optimal Price Point): Where PMC and PME curves cross

Gabor-Granger Method:
  1. Present product at specific price point
  2. Ask: "Would you buy at $X?" (Yes/No)
  3. Test 5-7 price points per respondent
  4. Plot demand curve → find revenue-maximizing price

A/B Price Testing:
  Test variants: Price A vs Price B
  Metrics: Conversion rate, ARPU, LTV, total revenue
  Duration: 2-4 weeks minimum
  Sample: ≥ 1000 visitors per variant
  Tools: Optimizely, VWO, LaunchDarkly
```

---

## Sales Playbook

### Discovery Questions Framework

**BANT Qualification**
```
Budget:
  "What budget have you allocated for solving this?"
  "Is there a budget line item, or would this need approval?"
  "What's your typical investment in tools like this?"

Authority:
  "Who else is involved in this decision?"
  "What does your evaluation/procurement process look like?"
  "Who has final sign-off authority?"

Need:
  "What triggered this initiative now?"
  "What happens if you don't solve this in the next 6 months?"
  "How are you handling this today? What's broken?"

Timeline:
  "When do you need a solution in place?"
  "Is there a hard deadline driving this?"
  "What other priorities compete with this project?"
```

**MEDDPICC Advanced Qualification**
```
Metrics:
  "How will you measure success?"
  "What KPIs are you trying to move?"
  "What's the financial impact of solving this?"

Economic Buyer:
  "Who controls the budget for this initiative?"
  "What does this person care about most?"

Decision Criteria:
  "What are your must-have vs nice-to-have requirements?"
  "How will you compare vendors?"

Decision Process:
  "Walk me through how decisions like this get made."
  "Who reviews, who approves, what's the timeline?"

Paper Process:
  "What does legal/procurement review look like?"
  "Any standard terms or security reviews required?"

Identified Pain:
  "What's the #1 problem you're trying to solve?"
  "How does this pain manifest day-to-day?"

Champion:
  "Who on your team is most passionate about fixing this?"
  "Would they advocate internally for our solution?"

Competition:
  "Who else are you evaluating?"
  "What's your current solution? Why switch now?"
```

### Demo Scripts

**Standard Demo Flow (30 minutes)**
```
[0-5 min] Personalize
  "Based on our conversation, your main challenge is [X]. 
   I'll show you exactly how we solve that. Questions first?"

[5-10 min] Problem Recap
  "You mentioned [pain point]. Here's how that typically 
   costs companies like yours [quantified impact]."

[10-20 min] Solution Demo
  Start with the "wow moment" (most impactful feature)
  Show workflow that matches their use case
  Use their data/terminology when possible
  Pause every 3-5 min: "Does this match what you need?"

[20-25 min] Differentiation
  "Unlike [competitor/status quo], we [unique advantage]."
  Show 2-3 specific differentiators relevant to them

[25-30 min] Next Steps
  "Based on what you've seen, does this address your needs?"
  "What would you need to see to move forward?"
  Propose specific next step (POC, technical review, proposal)
```

**Technical Deep Dive (45 minutes)**
```
[0-5 min] Context
  "What's your current tech stack? Any constraints?"

[5-15 min] Architecture Overview
  System architecture diagram
  Integration points with their stack
  Security/compliance capabilities

[15-30 min] Hands-On
  Live environment walkthrough
  API demonstration
  Custom configuration example

[30-40 min] Q&A / Whiteboarding
  Address specific technical questions
  Draw architecture on whiteboard
  Discuss migration/implementation path

[40-45 min] Technical Next Steps
  "Would a POC/pilot help validate this?"
  "Should we loop in your security team?"
```

### Objection Handling

**Common Objections and Responses**
```
"It's too expensive."
  Response: "I understand price is important. Let's look at the ROI. 
  You said [X problem] costs you [$Y/year]. Our solution is [$Z/year], 
  so you'd see [N]x return in the first year. Does that math work for you?"
  
  Technique: Reframe cost as investment, anchor to value

"We're happy with [competitor]."
  Response: "That's great they're working for you. Curious — 
  if you could change one thing about [competitor], what would it be? 
  [Listen]. That's actually where we differentiate: [specific capability]."
  
  Technique: Find the gap, don't trash competitor

"Send me some information."
  Response: "Happy to. To make sure I send relevant materials, 
  can you tell me what specifically you'd like to learn about? 
  [Then qualify: "What's driving your interest now?"]"
  
  Technique: Don't send blind — qualify the request

"Not the right time."
  Response: "Totally understand. When would be a better time? 
  And what would need to change for this to become a priority?"
  
  Technique: Get specific timeline, uncover real objection

"I need to talk to my team."
  Response: "Of course. What questions do you think they'll have? 
  Would it help if I joined that conversation to address them directly?"
  
  Technique: Multi-thread, offer to help build internal case

"We don't have budget."
  Response: "Budget is often a matter of priority. If we could show 
  [X ROI] within [Y months], would that change how you think about funding?"
  
  Technique: Challenge gently, reframe as ROI conversation
```

### Closing Techniques

**Assumptive Close**
```
"So we'll start with 50 seats on the Pro plan, annual billing.
 I'll send the contract over today — who should I address it to?"
 
When to use: Strong buying signals, verbal agreement on value
```

**Urgency Close**
```
"Our Q2 pricing ends Friday — the annual discount goes from 
 20% to 10% after that. Want to lock in the better rate?"
 
When to use: Real deadline exists, customer is price-sensitive
Warning: Never manufacture fake urgency
```

**Summary Close**
```
"Let me recap what we've agreed on:
 - Solves your [problem A] and [problem B]
 - 100 seats on Enterprise plan
 - 2-year commitment at 25% discount
 - Implementation starts March 1st
 Did I capture everything? Ready to move forward?"
 
When to use: Complex sale, multiple stakeholders, long cycle
```

**Puppy Dog Close (Trial/POC)**
```
"Why don't we set up a 30-day pilot with your team? 
 No commitment — if it doesn't deliver results, you walk away.
 If it does, we convert to annual. Fair?"
 
When to use: Risk-averse buyer, needs proof before committing
```

---

## Account-Based Marketing (ABM)

### ABM Tier Structure

**Tier 1: Strategic (1:1) — Top 10-50 Accounts**
```
Characteristics:
  ACV: $100K+
  Effort: High-touch, personalized
  Team: Named account team (AE + SDR + marketer)
  Timeline: 6-18 month cycles

Tactics:
  Custom research reports for each account
  Personalized landing pages (account-specific URLs)
  Executive dinner invitations
  Bespoke content (industry reports, ROI models)
  Handwritten notes, personalized gifts
  Custom event experiences

Budget: $5K-$25K per account per year
Content: 100% custom per account
```

**Tier 2: Scale (1:Few) — 50-500 Accounts**
```
Characteristics:
  ACV: $25K-$100K
  Effort: Semi-personalized by segment
  Team: Shared ABM marketer across segments
  Timeline: 3-9 month cycles

Tactics:
  Industry-specific content and campaigns
  Cluster-based events (industry roundtables)
  Programmatic ABM ads (industry-targeted)
  Webinars for specific verticals
  Semi-personalized outreach sequences

Budget: $1K-$5K per account per year
Content: Customized by industry/segment
```

**Tier 3: Programmatic (1:Many) — 500-5000 Accounts**
```
Characteristics:
  ACV: $10K-$25K
  Effort: Automated personalization
  Team: Demand gen team manages
  Timeline: 1-6 month cycles

Tactics:
  Programmatic display ads (account-targeted)
  Automated email sequences with merge fields
  Industry-specific nurture tracks
  Retargeting campaigns
  Chatbot with account recognition

Budget: $100-$1K per account per year
Content: Templates with dynamic fields
```

### Personalization Framework

**Personalization Levels**
```
Level 1 — Basic (Tier 3):
  - Company name in subject line
  - Industry-specific content
  - Role-based messaging
  Tools: Marketo, HubSpot, Outreach

Level 2 — Advanced (Tier 2):
  - Reference recent company news/funding
  - Industry-specific case studies
  - Competitor-aware messaging
  - Custom value props by segment
  Tools: Demandbase, 6sense, Terminus

Level 3 — Deep (Tier 1):
  - Reference specific company initiatives
  - Name key stakeholders in content
  - Custom ROI models with their data
  - Personalized video messages
  - Account-specific landing pages
  Tools: Vidyard, PathFactory, custom builds
```

**Account Intelligence Template**
```
Account Name: [Company]
Industry: [Vertical]
Revenue: [$X] | Employees: [N] | Growth: [X%]
Recent News: [Funding, M&A, leadership changes]
Key Initiatives: [Digital transformation, cost reduction, etc.]
Current Stack: [Competitors/integrations]
Decision Makers:
  - Economic Buyer: [Name, Title, LinkedIn]
  - Champion: [Name, Title, LinkedIn]
  - Influencer: [Name, Title, LinkedIn]
  - Blocker: [Name, Title, LinkedIn]
Pain Points:
  1. [Specific pain with business impact]
  2. [Specific pain with business impact]
Value Proposition: [Tailored to their pains]
Competitive Intel: [Who else they're evaluating]
Engagement History: [Previous touchpoints]
Next Actions: [Specific outreach plan]
```

### Multi-Threading Strategy

```
Why Multi-Thread:
  Single-threaded deals have 5x higher risk of stalling
  Average B2B buying committee: 6-10 stakeholders
  Champion leaving = deal dies if single-threaded

Multi-Threading Map:
  Economic Buyer (C-suite/VP)     → Executive briefing, ROI focus
  Champion (Day-to-day user)      → Product deep dive, enablement
  Technical Evaluator (IT/Eng)    → Technical demo, security review
  End Users (Team leads)          → Use case workshops, trials
  Procurement/Legal               → Contract terms, compliance
  Blockers (Incumbent vendor)     → Differentiation, migration ease

Engagement Cadence:
  Week 1-2: Identify all stakeholders via LinkedIn + champion intel
  Week 3-4: Begin parallel outreach (personalized per role)
  Week 5-6: Group meeting (align all on problem/solution)
  Week 7-8: Role-specific follow-ups
  Week 9+:  Executive alignment, proposal, close

Template — Multi-Thread Outreach:
  Subject: [Company] + [Our Company]: [Specific topic]
  
  Hi [Name],
  
  [Champion name] suggested I reach out — we're working with 
  [Company] on [initiative]. As [their role], I thought you'd 
  find this relevant: [specific value prop for their role].
  
  [Link to role-specific content/case study]
  
  Would 20 minutes this week work to discuss?
```

---

## Customer Acquisition Cost (CAC) Optimization

### Channel Mix Optimization

**CAC by Channel Benchmark (B2B SaaS)**
```
Channel                  | Avg CAC  | Sales Cycle | Quality
-------------------------|----------|-------------|--------
Organic/SEO              | $100-200 | Long        | High
Content/Inbound          | $200-400 | Medium      | High
Referral                 | $150-300 | Short       | Highest
Paid Search (Google)     | $300-600 | Short       | Medium
Paid Social (LinkedIn)   | $400-800 | Medium      | Medium
Events/Conferences       | $500-1K  | Medium      | High
Outbound SDR             | $800-2K  | Long        | Variable
ABM                      | $1K-5K   | Long        | Highest
Display/Retargeting      | $200-500 | Long        | Low

Optimal Mix by Stage:
  Early (Seed): 70% outbound, 20% content, 10% paid
  Growth (A-B): 40% inbound, 30% outbound, 20% paid, 10% events
  Scale (C+): 35% inbound, 25% paid, 20% outbound, 20% partners
```

**Channel Efficiency Scoring**
```
Score each channel (1-10) on:
  - Volume potential (can it scale?)
  - CAC efficiency (cost per customer)
  - Time to close (speed)
  - Customer quality (LTV of acquired customers)
  - Predictability (consistent results?)

Weighted Score = Σ (Score × Weight)
Weights: Volume 20%, CAC 30%, Speed 15%, Quality 25%, Predictability 10%

Decision Framework:
  Score ≥ 7.5: Invest aggressively
  Score 5-7.5: Optimize and test
  Score < 5: Reduce or eliminate
```

### Attribution Models

**Attribution Model Comparison**
```
First-Touch:
  Credit: 100% to first interaction
  Use: Understanding awareness drivers
  Tool: Google Analytics, UTM tracking
  Limitation: Ignores nurture/consideration

Last-Touch:
  Credit: 100% to last interaction before conversion
  Use: Understanding conversion drivers
  Tool: CRM source tracking
  Limitation: Ignores awareness/nurture

Linear:
  Credit: Equal split across all touches
  Use: Fair representation of full journey
  Limitation: Treats all touches equally

Time-Decay:
  Credit: More weight to recent touches
  Use: Long sales cycles
  Limitation: Undervalues awareness

U-Shaped:
  Credit: 40% first touch, 40% lead creation, 20% split rest
  Use: Balanced view of awareness + conversion
  Limitation: Complex to implement

Data-Driven (ML):
  Credit: Algorithm assigns based on actual conversion patterns
  Use: Sophisticated orgs with large data sets
  Tools: Google Analytics 4, Bizible, Dreamdata
  Limitation: Requires significant data volume

Implementation:
  1. Define tracking taxonomy (UTM conventions)
  2. Implement CRM + marketing automation integration
  3. Tag all campaigns consistently
  4. Run multi-model comparison quarterly
  5. Use insights to reallocate budget
```

### Budget Allocation Framework

**Demand Generation Budget Template**
```
Total Marketing Budget: $X/month

Allocation:
  Content & SEO:          25%  ($X × 0.25)
    Blog production: $X
    SEO tools/consulting: $X
    Content distribution: $X

  Paid Acquisition:       30%  ($X × 0.30)
    Google Ads: $X
    LinkedIn Ads: $X
    Retargeting: $X
    Display: $X

  Events & Field:         15%  ($X × 0.15)
    Conferences: $X
    Hosted events: $X
    Webinars: $X

  ABM:                    15%  ($X × 0.15)
    Tier 1 accounts: $X
    Tier 2 programs: $X
    ABM platforms: $X

  Brand & PR:             10%  ($X × 0.10)
    PR agency: $X
    Brand campaigns: $X
    Analyst relations: $X

  Tools & Infrastructure:  5%  ($X × 0.05)
    MarTech stack: $X
    Analytics: $X

Monthly Review:
  - CAC by channel
  - Pipeline generated per $ spent
  - Budget vs actual spend
  - Reallocation recommendations
```

**CAC Payback Calculation**
```
CAC Payback Period = CAC / (ARPU × Gross Margin %)

Example:
  CAC: $10,000
  ARPU: $500/month
  Gross Margin: 80%
  Payback = $10,000 / ($500 × 0.80) = 25 months

Benchmarks:
  Excellent: < 12 months
  Good: 12-18 months
  Acceptable: 18-24 months
  Concerning: > 24 months

Improvement Levers:
  1. Reduce CAC (better targeting, channel optimization)
  2. Increase ARPU (upsell, cross-sell, price optimization)
  3. Improve gross margin (infrastructure optimization)
  4. Accelerate time-to-value (reduce onboarding friction)
```

---

## Market Expansion

### International Expansion

**Market Prioritization Matrix**
```
Score each market (1-10):
  Market Size: TAM in that region
  Growth Rate: Market CAGR
  Competition: Fewer competitors = higher score
  Cultural Fit: Product-market fit signals
  Regulatory: Ease of compliance
  Talent: Availability of local team
  Infrastructure: Internet, payments, logistics

Weighted Score = Σ (Score × Weight)
Top 3-5 markets = expansion targets

Entry Models (low to high investment):
  1. Localization only (translate website/docs)
  2. Remote sales (sell from HQ, support remotely)
  3. Distributed team (local sales + support)
  4. Regional office (full local operation)
  5. Subsidiary (separate legal entity)
```

**Localization Checklist**
```
Product:
  □ UI translated to local language
  □ Date/time/currency formats
  □ Local payment methods
  □ Data residency compliance
  □ Right-to-left support (if applicable)
  □ Local phone number/address formats

Marketing:
  □ Website translated + culturally adapted
  □ Local case studies and references
  □ Localized pricing page
  □ Regional PR/analyst relationships
  □ Local social media presence

Legal:
  □ GDPR/local privacy compliance
  □ Local terms of service
  □ Tax registration (VAT, GST)
  □ Employment law compliance
  □ Data processing agreements

Sales:
  □ Local currency invoicing
  □ Regional payment terms
  □ Local sales team or partners
  □ Timezone-appropriate support
  □ Regional sales engineering
```

### Vertical Expansion

**Vertical Go-to-Market Template**
```
Vertical: [Healthcare / Financial Services / Manufacturing / etc.]

Market Assessment:
  TAM: $X billion
  SAM: $X million (our addressable portion)
  Key players: [Top 10 companies]
  Growth drivers: [Regulation, digitization, etc.]
  Pain points: [Industry-specific challenges]

Product Adaptation:
  Required features: [Compliance, integrations, workflows]
  Certifications needed: [HIPAA, SOC2, PCI-DSS, etc.]
  Integrations required: [EHR, core banking, ERP, etc.]
  Custom workflows: [Industry-specific processes]

GTM Plan:
  Messaging: "Built for [vertical]"
  Pricing: [Vertical-specific packaging]
  Partners: [Industry consultants, system integrators]
  Events: [Industry conferences, trade shows]
  Content: [Industry reports, compliance guides, ROI studies]

Success Metrics:
  Pipeline from vertical: $X
  Customers in vertical: N
  Market share: X%
  Average deal size: $X
  Win rate: X%
```

### Adjacent Market Expansion

**Adjacent Market Identification**
```
Framework — Ansoff Matrix:
  Market Penetration: Existing product × Existing market
    → Sell more to current customers
    → Win share from competitors
  
  Market Development: Existing product × New market
    → New geographies
    → New customer segments
    → New channels
  
  Product Development: New product × Existing market
    → Add features/modules
    → New product tiers
    → Complementary products
  
  Diversification: New product × New market
    → Highest risk
    → Consider only with strong core business

Adjacency Scoring:
  Customer overlap: Do existing customers need this? (1-10)
  Capability fit: Can we build/sell this? (1-10)
  Market attractiveness: Size, growth, competition (1-10)
  Brand permission: Will market trust us here? (1-10)
  
  Score ≥ 30: Strong adjacency candidate
  Score 20-29: Investigate further
  Score < 20: Too far from core
```

---

## Product-Led Growth (PLG) Metrics

### Activation Metrics

**Activation Definition**
```
Activation = User completes key action that correlates with retention

Finding your activation metric:
  1. Identify power users (retained 90+ days)
  2. Compare their first-week behavior vs churned users
  3. Find the action with highest correlation to retention
  4. Define as "Activation Event"

Common Activation Events:
  Slack: Team sends 2,000 messages
  Dropbox: User places file in folder on 2+ devices
  Facebook: User adds 7 friends in 10 days
  Zoom: User hosts first meeting
  Notion: User creates 5 notes in first week

Activation Funnel Template:
  Signup → Onboarding Complete → First Value Action → Activation Event
  
  Metrics:
    Signup → Onboarding: X% (target: >70%)
    Onboarding → First Value: X% (target: >50%)
    First Value → Activation: X% (target: >30%)
    Overall: Signup → Activation: X% (target: >10%)
```

**Onboarding Optimization**
```
Time-to-Value (TTV):
  Measure: Time from signup to activation event
  Benchmark: < 1 hour (simple products), < 1 day (medium), < 1 week (complex)
  
  Reduce TTV:
    - Remove unnecessary signup fields
    - Pre-populate templates/data
    - Guided product tours (Appcues, Pendo)
    - Interactive walkthroughs
    - Skip non-essential setup steps

Progressive Disclosure:
  Show only what's needed at each step
  Don't overwhelm new users with features
  Reveal complexity as user becomes proficient

Checklist Strategy:
  □ Create account
  □ Complete profile
  □ Import data
  □ Create first [item]
  □ Invite team member
  □ Set up integration
  □ Complete first workflow
  Progress bar: "5 of 7 complete — you're 71% there!"
```

### Engagement Scoring

**Engagement Score Model**
```
Components:
  Login frequency:     Weight: 20%
    Daily = 10, Weekly = 7, Monthly = 4, Rarely = 1

  Feature adoption:    Weight: 30%
    Core features used / Total core features × 10

  Content creation:    Weight: 20%
    Items created per week (normalized to 1-10)

  Collaboration:       Weight: 15%
    Invites sent, comments, shares (normalized to 1-10)

  Support engagement:  Weight: 15%
    Positive: Feature requests, feedback (high engagement)
    Negative: Bug reports, complaints (may indicate churn risk)

Engagement Score = Σ (Component Score × Weight)

Score Interpretation:
  8-10: Champion (power user, advocate candidate)
  6-8:  Healthy (active, growing usage)
  4-6:  At Risk (declining engagement, intervention needed)
  1-4:  Critical (likely churn, immediate action)
```

**Health Score Dashboard**
```
Account Health Score = w1(Engagement) + w2(Product Usage) + w3(Support) + w4(NPS) + w5(Payment)

Weights (adjust per business):
  Engagement: 25%
  Product Usage: 30%
  Support Tickets: 15%
  NPS/CSAT: 15%
  Payment Health: 15%

Status Thresholds:
  Green (Healthy):  Score ≥ 80
  Yellow (Watch):   Score 60-79
  Red (At Risk):    Score < 60

Automated Triggers:
  Score drops > 10 points → Alert CSM
  Score drops to Red → Alert VP CS + AE
  Score < 40 → Executive intervention
  Score increases > 15 → Upsell signal to Sales
```

### Conversion Funnel

**Free-to-Paid Conversion Funnel**
```
Stages:
  Visitor → Signup → Activated → Engaged → Converted → Expanded

Benchmark Conversion Rates (B2B SaaS PLG):
  Visitor → Signup: 5-15%
  Signup → Activated: 30-50%
  Activated → Engaged: 40-60%
  Engaged → Converted: 3-10% (free to paid)
  Converted → Expanded: 20-40% (upsell/cross-sell)

Overall: Visitor → Paid: 0.2-1.5%

Optimization Levers:
  Visitor → Signup:
    - Simplify signup (SSO, magic links)
    - Remove credit card requirement
    - Strong value proposition on landing page
    - Social proof (logos, testimonials, reviews)

  Signup → Activated:
    - Guided onboarding
    - Quick-win templates
    - Progress indicators
    - Email nurture sequences

  Activated → Engaged:
    - Feature discovery nudges
    - Use case suggestions
    - Team collaboration prompts
    - Integration recommendations

  Engaged → Converted:
    - Usage limit notifications
    - Feature gating (right features)
    - In-app upgrade prompts
    - Time-limited trials of premium features
    - Personalized upgrade offers

  Converted → Expanded:
    - Seat expansion prompts
    - Usage-based overage nudges
    - New module recommendations
    - Success-driven upsell timing
```

**PLG Metrics Dashboard Template**
```
Acquisition:
  - New signups (daily/weekly/monthly)
  - Signup source breakdown
  - Signup-to-activation rate
  - Viral coefficient (invites sent × conversion)

Engagement:
  - DAU/MAU ratio (stickiness, target > 20%)
  - Weekly active users (WAU)
  - Feature adoption rates
  - Session duration and frequency

Conversion:
  - Free-to-paid conversion rate
  - Time to convert (median days)
  - Conversion by cohort
  - Trial-to-paid conversion (if applicable)

Revenue:
  - ARPU (average revenue per user)
  - Expansion revenue %
  - Net revenue retention (NRR)
  - LTV:CAC ratio

Retention:
  - Day 1/7/30 retention curves
  - Logo churn rate
  - Revenue churn rate
  - Reactivation rate

Reporting Cadence:
  Daily: Signups, activation, DAU
  Weekly: Conversion rates, engagement scores, pipeline
  Monthly: Revenue metrics, cohort analysis, churn analysis
  Quarterly: LTV:CAC, market analysis, strategic review
```

---

## GTM Metrics Dashboard

### Key Metrics by Function

**Marketing Metrics**
```
  MQLs generated
  MQL → SQL conversion rate
  Cost per MQL
  Marketing-sourced pipeline ($)
  Marketing-influenced pipeline ($)
  Content engagement (views, downloads, shares)
  Website traffic (organic, paid, referral)
  SEO rankings (target keywords)
```

**Sales Metrics**
```
  SQLs received
  SQL → Opportunity conversion rate
  Average deal size (ACV)
  Win rate
  Sales cycle length (days)
  Pipeline coverage ratio (target: 3-4x)
  Quota attainment %
  New ARR booked
```

**Customer Success Metrics**
```
  NPS / CSAT scores
  Net Revenue Retention (NRR)
  Logo retention rate
  Expansion revenue
  Time to value (days)
  Health score distribution
  Support ticket volume and resolution time
  Customer references generated
```

**Product Metrics (PLG)**
```
  DAU/MAU ratio
  Activation rate
  Free-to-paid conversion
  Feature adoption rates
  Product-qualified leads (PQLs)
  Viral coefficient
  Time in product
  NPS by feature
```

### Quarterly Business Review (QBR) Template

```
Section 1: Performance Summary
  - Revenue vs plan ($ and %)
  - Customer acquisition vs plan
  - Key wins and losses

Section 2: GTM Funnel Analysis
  - Funnel conversion rates (current vs last quarter)
  - Bottleneck identification
  - Pipeline health

Section 3: Channel Performance
  - CAC by channel
  - ROI by channel
  - Channel mix recommendations

Section 4: Customer Insights
  - NPS/CSAT trends
  - Churn analysis (reasons, patterns)
  - Expansion pipeline

Section 5: Competitive Landscape
  - Win/loss analysis
  - Competitive positioning changes
  - Market trends

Section 6: Next Quarter Plan
  - Revenue targets
  - Key initiatives (3-5)
  - Resource needs
  - Risk mitigation
```



## Step 21: Pricing Strategy Deep Dive

### Pricing Models

```
Per-user pricing:
  - $X per user per month
  - Pros: Simple, predictable, scales with adoption
  - Cons: Penalizes large teams, seat-hoarding
  - Examples: Slack, Atlassian, GitHub

Usage-based pricing:
  - $X per API call, per GB, per compute hour
  - Pros: Fair, aligns cost with value, low barrier
  - Cons: Unpredictable, hard to budget
  - Examples: AWS, Twilio, Stripe

Flat-rate pricing:
  - $X per month for all features
  - Pros: Simple, predictable
  - Cons: Leaves money on table, no expansion path
  - Examples: Basecamp, Buffer

Tiered pricing:
  - Free / Pro / Business / Enterprise
  - Pros: Clear upgrade path, segments market
  - Cons: Feature gating decisions, tier confusion
  - Examples: Zoom, Notion, Figma

Hybrid pricing:
  - Base fee + usage overage
  - Pros: Predictable base, fair expansion
  - Cons: Complex to explain
  - Examples: Datadog, Snowflake, MongoDB Atlas
```

### Pricing Page Best Practices

```
Layout:
  - 3-4 tiers side by side
  - Highlight "Most Popular" tier
  - Monthly/Annual toggle (show savings)
  - Feature comparison table below
  - Enterprise tier: "Contact Sales"

Psychology:
  - Anchoring: Show highest price first
  - Decoy: Middle tier makes top tier look reasonable
  - Social proof: "Used by 10,000+ companies"
  - Urgency: "Annual billing saves 20%"
  - Risk reversal: "14-day free trial, no credit card"

Common mistakes:
  - Too many tiers (4+ is confusing)
  - Feature overlap between tiers
  - Hidden pricing for enterprise (frustrating)
  - No free tier (misses developer adoption)
  - Annual-only pricing (kills trial-to-paid)
```

## Step 22: Sales Playbook

### Discovery Questions

```
Situation questions:
  - "Walk me through your current [process/tool]."
  - "How many people on your team use [relevant tool]?"
  - "What does your current stack look like for [area]?"

Problem questions:
  - "What's the biggest challenge with your current setup?"
  - "How much time does your team spend on [manual task]?"
  - "What happens when [bad scenario] occurs?"

Implication questions:
  - "How does that impact your team's productivity?"
  - "What's the cost of [problem] to your business?"
  - "If this continues, what does it mean for [goal]?"

Need-payoff questions:
  - "If you could [solve problem], what would that mean for your team?"
  - "How would it change things if [desired outcome]?"
  - "What would you do with the time saved from [automation]?"
```

### Objection Handling

```
Price objection:
  "It's too expensive."
  Response: "I understand price is a factor. Let's look at the ROI. 
  If this saves your team 10 hours/week at $100/hour, that's 
  $4,000/month in value against our $500/month price. What's the 
  most important value driver for you?"

Timing objection:
  "We're not ready yet."
  Response: "Totally understand. What would need to be true for 
  you to be ready? Often we see teams start with a small pilot 
  to build the case internally. Would a 30-day pilot help?"

Competitor objection:
  "We're already using [competitor]."
  Response: "Great, [competitor] is solid. What's working well 
  with them? What would you change if you could? [Then position 
  differentiated value in their gap areas.]"

Authority objection:
  "I need to check with my boss."
  Response: "Of course. What questions do you think they'll have? 
  Would it help if I joined that conversation? I can prepare a 
  business case summary for them."
```

### Closing Techniques

```
Assumptive close:
  "Should we start with the Pro plan or Business plan?"
  (Assumes decision is which plan, not whether to buy)

Summary close:
  "Based on our conversation, you need [X], [Y], and [Z]. 
  Our [plan] covers all three. Shall we move forward?"

Urgency close:
  "Our current promotion ends Friday. If you sign up today, 
  you'll get [benefit]. After that, it's full price."

Trial close:
  "If we could solve [their main objection], would you be ready 
  to move forward?"
  (Tests commitment before proposing solution)

ROI close:
  "Based on the numbers we discussed, you'll save $X per month. 
  The investment is $Y per month. That's a Z:1 ROI. Makes sense 
  to start, right?"
```

## Step 23: Account-Based Marketing

### ABM Tiers

```
Tier 1: Strategic ABM (1:1)
  - Target: 10-20 dream accounts
  - Approach: Fully customized campaigns
  - Investment: $5K-$20K per account
  - Tactics: Custom content, executive events, direct mail
  - Team: Dedicated marketer + sales rep per account

Tier 2: ABM Lite (1:Few)
  - Target: 50-100 accounts in same industry/segment
  - Approach: Semi-customized campaigns
  - Investment: $500-$2K per account
  - Tactics: Industry-specific content, targeted ads, webinars
  - Team: Shared marketing + sales pods

Tier 3: Programmatic ABM (1:Many)
  - Target: 500-1000 accounts matching ICP
  - Approach: Automated personalization
  - Investment: $50-$200 per account
  - Tactics: Targeted ads, personalized emails, dynamic web content
  - Team: Marketing automation + SDRs
```

### ABM Playbook

```
Account selection:
  1. Define ICP criteria (firmographic + technographic)
  2. Build target account list (CRM + intent data)
  3. Score accounts (fit + intent + engagement)
  4. Assign to tiers based on potential value

Multi-threading:
  - Identify 5-8 stakeholders per account
  - Map buying committee roles:
    - Champion: Internal advocate
    - Decision maker: Budget authority
    - Influencer: Technical evaluator
    - Blocker: Risk-averse stakeholder
  - Tailor message per role
  - Engage across multiple channels

Measurement:
  - Account engagement score
  - Pipeline generated per account
  - Deal velocity (ABM vs non-ABM)
  - Win rate (ABM vs non-ABM)
  - Average contract value (ABM vs non-ABM)
```

## Step 24: Market Expansion

### International Expansion Checklist

```
Market assessment:
  □ Total addressable market (TAM) by country
  □ Competitive landscape (local + global players)
  □ Regulatory requirements (data residency, privacy)
  □ Payment infrastructure (local payment methods)
  □ Language requirements (localization scope)
  □ Cultural considerations (business norms, holidays)

Go-to-market:
  □ Local pricing (PPP adjustment)
  □ Local domain and SEO (ccTLD, hreflang)
  □ Local payment methods (SEPA, iDEAL, UPI, Alipay)
  □ Local support (timezone, language)
  □ Local partnerships (resellers, integrators)
  □ Local legal entity (if required)

Operations:
  □ Tax registration (VAT, GST)
  □ Employment compliance (if hiring locally)
  □ Data residency (local hosting)
  □ Currency management (FX risk)
  □ Customer support timezone coverage
```

### Vertical Market Strategy

```
Vertical selection criteria:
  - Market size: >$1B TAM
  - Pain severity: High willingness to pay
  - Competition: Underserved by existing solutions
  - Adjacency: Leverages existing capabilities
  - References: Can win 2-3 lighthouse customers

Vertical playbook:
  1. Research industry (regulations, workflows, terminology)
  2. Customize product (industry-specific features)
  3. Build integrations (industry-standard tools)
  4. Create content (industry-specific case studies, guides)
  5. Hire industry expertise (sales, marketing, product)
  6. Partner with industry players (consultants, associations)
  7. Win lighthouse customers (reference accounts)
  8. Scale (repeatable playbook)
