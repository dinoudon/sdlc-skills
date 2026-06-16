---
name: sdlc-finance-ops
description: "Software company finance and operations: unit economics, SaaS metrics, fundraising (seed to IPO), financial planning, burn rate, runway, budgeting, cap table, equity, stock options, board management, vendor management, procurement, insurance."
version: 6.0.0
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

## Step 6: Vendor & Operations

### Vendor Evaluation

```
Criteria for SaaS vendor selection:
1. Security: SOC 2, GDPR compliance, data residency
2. Reliability: SLA, uptime history, incident response
3. Cost: Per-user vs flat rate, annual vs monthly, volume discounts
4. Integration: API quality, SSO, SAML, SCIM
5. Lock-in: Data export, migration support, contract terms
6. Support: Response time, dedicated CSM, escalation path
```

### Insurance Requirements

```
Essential for software companies:
1. General liability ($1M-$2M coverage)
2. Professional liability / E&O ($1M-$5M)
3. Cyber liability ($1M-$10M)
4. D&O (Directors & Officers) — required for board members
5. Workers' compensation — required by law
6. Employment practices liability (EPLI)
7. Key person insurance (for critical founders/employees)
```

## Step 7: Financial Modeling

### SaaS Financial Model Template

```
Revenue Model (Monthly):
┌─────────────────────────────────────────────────────┐
│ Month        │ M1    │ M2    │ M3    │ ... │ M12   │
├──────────────┼───────┼───────┼───────┼─────┼───────┤
│ New customers│ 20    │ 25    │ 30    │ ... │ 50    │
│ Churned      │ -2    │ -3    │ -3    │ ... │ -5    │
│ Net new      │ 18    │ 22    │ 27    │ ... │ 45    │
│ Total cust.  │ 118   │ 140   │ 167   │ ... │ 450   │
│ ARPU         │ $500  │ $500  │ $500  │ ... │ $520  │
│ MRR          │ $59K  │ $70K  │ $84K  │ ... │ $234K │
│ ARR (MRR×12) │ $708K │ $840K │ $1M   │ ... │ $2.8M │
└──────────────┴───────┴───────┴───────┴─────┴───────┘

Cost Model (Monthly):
┌─────────────────────────────────────────────────────┐
│ COGS (30%):     Hosting, support, payment processing│
│ R&D (40%):      Engineering headcount + tools        │
│ S&M (20%):      Marketing + sales team + events      │
│ G&A (10%):      Legal, finance, office, insurance    │
└─────────────────────────────────────────────────────┘

Key Outputs:
  Gross margin: 70%
  Burn rate: $150K/month
  Break-even: Month 18
  Revenue at break-even: $250K MRR
```

### Fundraising Financial Model

```
What investors want to see:

1. Revenue trajectory (3-5 year projection)
   - Bottom-up: customers × ARPU (not top-down "1% of $10B market")
   - Conservative, base, optimistic scenarios

2. Unit economics
   - LTV:CAC ratio (and trend)
   - CAC payback period
   - Gross margin per customer

3. Cost structure
   - Headcount plan by department
   - Infrastructure costs (scaling with usage)
   - Marketing spend efficiency

4. Cash management
   - Current burn rate
   - Runway (months remaining)
   - Use of funds breakdown

5. Key assumptions
   - Growth rate assumptions (and why)
   - Churn assumptions (and why)
   - Pricing assumptions (and why)
```

### Use of Funds Template

```
Series A: $15M raise

Engineering (50% — $7.5M):
  - 15 engineers over 18 months
  - Infrastructure scaling
  - Security and compliance

Sales & Marketing (30% — $4.5M):
  - 5 sales reps
  - Marketing campaigns
  - Events and conferences

Operations (15% — $2.25M):
  - G&A team expansion
  - Legal and compliance
  - Office and tools

Reserve (5% — $750K):
  - Unexpected costs
  - Bridge funding if needed
```

## Step 8: Accounting & Tax Basics

### SaaS Revenue Recognition

```
ASC 606 (Revenue Recognition):

Recognized when:
1. Contract identified
2. Performance obligations identified
3. Transaction price determined
4. Price allocated to obligations
5. Revenue recognized when obligation satisfied

Example:
Annual contract: $12,000/year
Monthly recognition: $1,000/month
Not $12,000 upfront (even if paid in advance)

Deferred revenue: Cash received but not yet recognized
  Balance sheet liability until performance obligation met
```

### Key Tax Considerations

```
1. R&D Tax Credit: 20% of qualifying R&D expenses
   - Engineering salaries
   - Cloud infrastructure for development
   - Contractor costs for R&D
   
2. State Nexus: Sales tax obligations by state
   - Economic nexus thresholds (typically $100K+ revenue or 200+ transactions)
   - SaaS taxability varies by state
   
3. International: VAT/GST for non-US customers
   - EU: Digital services VAT (typically 20%)
   - Use tax automation (Stripe Tax, Avalara, TaxJar)
   
4. Equity: 409A valuation for stock options
   - Required before granting options
   - Updated every 12 months or after material events
   - Cost: $5K-$15K per valuation
```

## Pitfalls

1. **Vanity metrics** — "10,000 signups" means nothing without activation and retention data.
2. **Raising too much** — More money = more dilution + pressure to grow faster than healthy.
3. **Raising too little** — Running out of money mid-raise is a death sentence. Raise 18-24 months.
4. **No financial model** — Investors expect a bottoms-up model, not a hockey stick in a slide deck.
5. **Ignoring unit economics** — Growing revenue while losing money on every customer is a Ponzi scheme.
6. **Premature scaling** — Hiring 50 engineers before PMF burns cash without returns.
7. **Cap table mess** — Clean up cap table before raising. Investors will find every issue.
8. **No board prep** — Board meetings are high-leverage. Prepare materials 1 week in advance.
9. **Equity over-promising** — Running out of option pool before Series B creates hiring problems.
10. **Cash flow blindness** — Profitable on paper but no cash in bank = bankruptcy. Track cash weekly.

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
