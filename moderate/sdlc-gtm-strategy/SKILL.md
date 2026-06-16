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

## Sources

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
