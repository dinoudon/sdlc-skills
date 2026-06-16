---
name: sdlc-finance-ops
description: "Software company finance and operations: unit economics, SaaS metrics, fundraising (seed to IPO), financial planning, burn rate, runway, budgeting, cap table, equity, stock options, board management, vendor management, procurement, insurance."
version: 6.0.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, finance, operations, unit-economics, fundraising, saas-metrics, burn-rate, runway, cap-table, equity, board-management]
    related_skills: [sdlc-product-growth, sdlc-gtm-strategy, sdlc-hiring-talent, sdlc-legal-compliance]
---## When to Use

Trigger when user:
- Calculates unit economics (LTV, CAC, payback period)
- Plans fundraising (seed, Series A/B/C, IPO)
- Creates financial model or projections
- Manages burn rate and runway
- Designs cap table or equity plan
- Prepares board meeting materials
- Evaluates vendor contracts or procurement
- Plans budget or headcount

## Step 1: SaaS Unit Economics

### Core Metrics

```
MRR (Monthly Recurring Revenue)
  = Σ (customers × monthly subscription)

ARR (Annual Recurring Revenue)
  = MRR × 12

ARPU (Average Revenue Per User)
  = MRR / paying customers

LTV (Customer Lifetime Value)
  = ARPU × gross margin × (1 / monthly churn rate)

CAC (Customer Acquisition Cost)
  = Total sales & marketing spend / new customers acquired

LTV:CAC Ratio
  = LTV / CAC (target: >3:1)

CAC Payback Period
  = CAC / (ARPU × gross margin) (target: <18 months)

NRR (Net Revenue Retention)
  = (start MRR + expansion - contraction - churn) / start MRR × 100

Gross Margin
  = (Revenue - COGS) / Revenue (target: >70% for SaaS)

Burn Multiple
  = Net Burn / Net New ARR (target: <2x)

Rule of 40
  = Revenue growth rate (%) + profit margin (%) > 40%
```

### Unit Economics Example

```
Scenario: B2B SaaS company

ARPU: $500/month
Gross margin: 80%
Monthly churn: 3%
LTV: $500 × 0.80 / 0.03 = $13,333

CAC: $3,000
LTV:CAC = $13,333 / $3,000 = 4.4:1 (healthy)

CAC Payback = $3,000 / ($500 × 0.80) = 7.5 months (great)

NRR: 115% (expansion outpaces churn)
```

### Cohort Analysis

```
Revenue cohorts show if unit economics improve over time:

Cohort Q1 2025: 100 customers, $50K MRR
  Q2 2025: $52K MRR (+4% expansion)
  Q3 2025: $54K MRR (+8% from start)
  Q4 2025: $56K MRR (+12% from start)
  → Healthy expansion revenue

Cohort Q1 2025: 100 customers, $50K MRR
  Q2 2025: $45K MRR (-10% churn)
  Q3 2025: $40K MRR (-20% from start)
  → Churn problem, investigate onboarding
```

## Step 2: Fundraising

### Funding Stages

| Stage | Amount | Traction | Valuation | Dilution |
|-------|--------|----------|-----------|----------|
| **Pre-seed** | $100K-$1M | Idea/prototype | $2-5M | 15-25% |
| **Seed** | $1M-$5M | PMF signal, early revenue | $5-20M | 15-25% |
| **Series A** | $5M-$20M | $1-5M ARR, proven GTM | $20-80M | 15-25% |
| **Series B** | $20M-$50M | $5-20M ARR, scaling | $80-300M | 10-20% |
| **Series C+** | $50M-$200M+ | $20M+ ARR, market leader | $300M+ | 10-15% |
| **IPO** | Public offering | $100M+ ARR, profitable path | Market decides | Varies |

### Fundraising Process

```
1. Preparation (2-4 weeks)
   □ Update pitch deck (10-12 slides)
   □ Financial model (3-year projections)
   □ Data room (metrics, contracts, legal docs)
   □ Target investor list (50-100 investors)
   □ Warm intros lined up

2. Outreach (1-2 weeks)
   □ Send intro emails (warm intros preferred)
   □ Schedule first meetings (aim for 30+ in 2 weeks)
   □ Track pipeline in CRM

3. First Meetings (2-3 weeks)
   □ 30-min pitch meeting
   □ Share deck + financial model
   □ Get feedback and interest level

4. Partner Meetings (1-2 weeks)
   □ Present to full partnership
   □ Deep dive on metrics, market, team

5. Due Diligence (2-4 weeks)
   □ Customer references
   □ Technical diligence
   □ Financial audit
   □ Legal review

6. Term Sheet (1 week)
   □ Negotiate terms (valuation, board seats, liquidation prefs)
   □ Sign term sheet
   □ Exclusivity period (30-60 days)

7. Close (4-8 weeks)
   □ Legal documentation
   □ Money wires
   □ Announcement
```

### Pitch Deck Structure

```
1. Title: Company name, one-line description, raise amount
2. Problem: What's broken? Who feels the pain?
3. Solution: How you fix it. Demo screenshot.
4. Market: TAM/SAM/SOM with sources
5. Business model: How you make money
6. Traction: Revenue, growth, key metrics (chart)
7. Competition: Positioning map (not feature matrix)
8. Team: Founders + key hires, relevant experience
9. Financials: Revenue projections, unit economics
10. Ask: How much, what you'll do with it, milestones
```

## Step 3: Financial Planning

### Financial Model Components

```
Revenue Model:
  - New customers/month (from sales pipeline)
  - ARPU (average revenue per user)
  - Expansion revenue (upsells, cross-sells)
  - Churn (lost MRR)

Cost Model:
  - COGS (hosting, support, payment processing)
  - R&D (engineering headcount, tools)
  - S&M (marketing, sales team, events)
  - G&A (legal, finance, office, insurance)

Key Outputs:
  - Monthly burn rate
  - Runway (months of cash remaining)
  - Break-even date
  - Revenue at break-even
```

### Runway Calculation

```
Runway = Cash in bank / Monthly net burn

Example:
Cash: $5M
Monthly revenue: $200K
Monthly expenses: $400K
Net burn: $200K/month
Runway: $5M / $200K = 25 months

Target: Always have 18-24 months of runway
Raise when: 9-12 months remaining (to allow 3-6 months for fundraising)
```

### Headcount Planning

```
Engineering team:
  Engineers: 60% of headcount
  Managers: 10% of headcount
  Product/Design: 15% of headcount
  QA/SDET: 10% of headcount
  DevOps/SRE: 5% of headcount

Revenue per employee target:
  Seed: $100K-$200K per employee
  Series A: $200K-$300K per employee
  Series B+: $300K-$500K per employee
  Public: $500K+ per employee
```


## Pitfalls

1. **Vanity metrics** — "10,000 signups" means nothing without activation and retention data.
2. **Raising too much** — More money = more dilution + pressure to grow faster than healthy.
3. **Raising too little** — Running out of money mid-raise is a death sentence. Raise 18-24 months.
4. **No financial model** — Investors expect a bottoms-up model, not a hockey stick in a slide deck.
5. **Ignoring unit economics** — Growing revenue while losing money on every customer is a Ponzi scheme.

## Sources

- Bessemer Cloud Atlas: https://www.bvp.com/atlas
- OpenView SaaS Benchmarks: https://openviewpartners.com/blog/
- a16z Startup School: https://a16z.com/startup-school/
- Carta (cap table): https://carta.com/
- Holloway Equity Guide: https://www.holloway.com/g/equity-compensation
- SaaStr (SaaS metrics): https://www.saastr.com/
- Christoph Janz (SaaS metrics): https://christophjanz.blogspot.com/
- Tomasz Tunguz (SaaS): https://tomtunguz.com/
- YC Fundraising Guide: https://www.ycombinator.com/library
- Brad Feld, Venture Deals: https://www.feld.com/archives/2019/06/venture-deals-4th-edition.html
