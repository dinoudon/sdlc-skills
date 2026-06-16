---
name: sdlc-gtm-strategy
description: "Go-to-market strategy: market positioning, pricing, packaging, sales enablement, competitive analysis, launch planning, distribution channels, PLG vs SLG, developer marketing, enterprise sales, partnership strategy, category creation."
version: 6.0.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, gtm, go-to-market, pricing, sales, marketing, launch, distribution, enterprise-sales, partnerships]
    related_skills: [sdlc-product-growth, sdlc-developer-relations, sdlc-prd-to-production, sdlc-finance-ops]
---## When to Use

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


## Pitfalls

1. **Launching without positioning** — If you can't explain what you do in one sentence, you're not ready to launch.
2. **PLG without product-market fit** — PLG amplifies a good product. It can't save a bad one.
3. **Enterprise sales too early** — Enterprise deals take 6+ months. Don't go enterprise before you have $1M ARR from self-serve.
4. **Pricing by gut feel** — Use Van Westendorp, Gabor-Granger, or A/B testing. Don't guess.
5. **Ignoring competitors** — "We have no competitors" means you haven't looked hard enough or you're in a dead market.

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
