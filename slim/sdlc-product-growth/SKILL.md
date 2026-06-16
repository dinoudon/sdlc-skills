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
- Gate c