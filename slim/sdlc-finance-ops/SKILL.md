---
name: sdlc-finance-ops
description: "Software company finance and operations: unit economics, SaaS metrics, fundraising (seed to IPO), financial planning, burn rate, runway, budgeting, cap table, equity, stock options, board management, vendor management, procurement, insurance."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, finance, operations, unit-economics, fundraising, saas-metrics, burn-rate, runway, cap-table, equity, board-management]
    related_skills: [sdlc-product-growth, sdlc-gtm-strategy, sdlc-hiring-talent, sdlc-legal-compliance]
---

# Finance & Operations

Unit economics, fundraising, financial planning, and operational infrastructure for software companies.

## When to Use

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

## Step 4: Cap Table & Equity

### Cap Table Example

```
Founder A: 40% (4,000,000 shares)
Founder B: 30% (3,000,000 shares)
Employee option pool: 15% (1,500,000 shares)
Seed investors: 10% (1,000,000 shares)
Angel investors: 5% (500,000 shares)
Total: 10,000,000 shares

After Series A (20% dilution):
Founder A: 32% (diluted from 40%)
Founder B: 24% (diluted from 30%)
Employee pool: 12% (refreshed to 15%)
Seed investors: 8%
Angel investors: 4%
Series A investors: 20%
Total: 100%
```

### Equity Best Practices

```
Employee equity grants:
  Junior IC: 0.01-0.05%
  Senior IC: 0.05-0.25%
  Staff IC: 0.1-0.5%
  VP/Director: 0.5-1.5%
  C-level: 1-5%

Vesting: 4 years, 1-year cliff (standard)
Cliff: No equity until 1 year, then 25% vests
Monthly: Remaining 75% vests monthly over 3 years
Acceleration: Single trigger (acquisition) or double trigger (acquisition + termination)
```

## Step 5: Board Management

### Board Meeting Structure

```
Quarterly board meeting (2-3 hours):

1. CEO Update (15 min)
   - Key wins, challenges, strategic decisions
   
2. Financial Review (30 min)
   - Revenue, burn, runway, metrics dashboard
   
3. Product Update (20 min)
   - Roadmap progress, key launches, customer feedback
   
4. GTM Update (20 min)
   - Sales pipeline, marketing, customer success
   
5. People Update (15 min)
   - Headcount, hiring, culture, key departures
   
6. Key Decisions (30 min)
   - Strategic decisions requiring board input
   
7. Executive Session (15 min)
   - Board meets without management
```

### Board Reporting Template

```
Key Metrics Dashboard:
┌───────────────────────────────────────────┐
│  ARR: $2.4M  ↑32% QoQ                    │
│  MRR: $200K  ↑$15K from last month        │
│  Customers: 450  ↑45 net new               │
│  NRR: 112%                                │
│  Burn: $180K/mo  Runway: 22 months        │
│  Headcount: 18  ↑3 this quarter           │
│  NPS: 62                                  │
└───────────────────────────────────────────┘
```

## St