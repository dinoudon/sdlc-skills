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
│                      