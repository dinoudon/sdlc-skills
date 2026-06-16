---
name: sdlc-developer-relations
description: "Developer Relations (DevRel) program design: advocacy, community, marketing, enablement. Developer experience (DX), developer journey mapping, technical content strategy, community building, SDK strategy, developer onboarding, API playground, developer feedback loops, developer marketing, developer funnel, measuring DevRel."
version: 6.0.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, developer-relations, devrel, developer-experience, dx, community, advocacy, sdk, developer-marketing, technical-content]
    related_skills: [sdlc-product-growth, sdlc-api-documentation, sdlc-prd-to-production, sdlc-developer-tooling, sdlc-gtm-strategy]
---## When to Use

Trigger when user:
- Plans a DevRel program or developer advocacy team
- Designs developer onboarding or first-run experience
- Creates technical content strategy (blogs, tutorials, videos)
- Builds community (Discord, GitHub, forums)
- Plans SDK/client library strategy
- Designs API playground or sandbox
- Measures developer satisfaction or DevRel ROI
- Asks "how do Stripe/Twilio/MongoDB do DevRel?"

## Step 1: The Four Pillars of DevRel
Source: developerrelations.com

```
┌──────────────────────────────────────────────┐
│              Developer Relations              │
├────────────┬────────────┬──────────┬─────────┤
│  ADVOCACY  │  COMMUNITY │MARKETING │ENABLEMENT│
│            │            │          │          │
│ • Talks    │ • Forums   │ • Content│ • Docs   │
│ • Workshops│ • Discord  │ • SEO    │ • SDKs   │
│ • Feedback │ • Events   │ • Social │ • Tools  │
│ • Evangelism│ • OSS     │ • DevRel │ • Sandboxes│
│            │            │  website │          │
└────────────┴────────────┴──────────┴─────────┘
```

### Pillar 1: Developer Advocacy
- Represent developer voice inside the company
- Speak at conferences (KubeCon, re:Invent, local meetups)
- Build sample apps and reference implementations
- Collect developer feedback → product roadmap
- Create educational content (tutorials, courses)

### Pillar 2: Community Building
- Manage Discord/Slack communities
- Triage GitHub issues and discussions
- Organize meetups and hackathons
- Recognize and empower community champions
- Monitor Stack Overflow, Reddit, HN

### Pillar 3: Developer Marketing
- Developer-focused blog posts and case studies
- SEO-optimized documentation
- Social media presence (Twitter/X, YouTube, LinkedIn)
- Launch announcements and changelog
- Developer newsletter

### Pillar 4: Developer Enablement
- API documentation and guides
- SDK/client libraries in popular languages
- Quickstart guides and code samples
- API playground and sandbox environments
- Migration guides and compatibility matrices

## Step 2: Developer Journey Map

```
AWARENESS → INTEREST → EVALUATION → ADOPTION → ADVOCACY
    │           │           │            │           │
 Blog post   Docs page   Sandbox     Integration   Share with
 Talk        Tutorial    API key     Production    peers
 Tweet       GitHub      Quickstart  Team rollout  Conference
 SEO         Sample app  Trial       Enterprise    talk
```

### Journey Stage Details

| Stage | Touchpoints | Metrics | Content |
|-------|------------|---------|---------|
| **Awareness** | Blog, social, conference, SEO | Impressions, visits, search rank | Thought leadership, announcements |
| **Interest** | Docs, tutorials, GitHub | Doc page views, GitHub stars | Getting started, overview docs |
| **Evaluation** | Sandbox, quickstart, pricing | API key signups, sandbox usage | Code samples, comparison guides |
| **Adoption** | SDK, integration guides, support | Active API users, SDK installs | Migration guides, best practices |
| **Advocacy** | Community, events, referral | NPS, community posts, referrals | Ambassador programs, case studies |

## Step 3: Technical Content Strategy

### Content Types & Purpose

| Type | Purpose | Frequency | Owner |
|------|---------|-----------|-------|
| **Blog post** | Deep dives, announcements | 2-4/month | DevRel + Eng |
| **Tutorial** | Step-by-step how-to | 2-4/month | DevRel |
| **Quickstart** | First API call in <5 min | Per product | Docs team |
| **Video** | Visual learners, YouTube SEO | 2-4/month | DevRel |
| **Sample app** | Reference implementation | Per use case | DevRel + Eng |
| **Changelog** | What's new | Every release | Eng |
| **Newsletter** | Community digest | Monthly | DevRel |
| **Case study** | Social proof | Quarterly | Marketing + DevRel |

### Content Calendar Template

```
Week 1: Blog post (new feature) + Tutorial (how to use it)
Week 2: Video walkthrough + Community spotlight
Week 3: Technical deep dive + Sample app update
Week 4: Newsletter + Changelog + Next month planning
```

### Writing Technical Content

Structure for tutorials:
```
1. What you'll build (outcome, not steps)
2. Prerequisites (tools, accounts, versions)
3. Step-by-step instructions (numbered, copy-pasteable)
4. Code blocks with explanations
5. Troubleshooting (common errors)
6. Next steps (what to learn after)
```


## Pitfalls

1. **DevRel as marketing only** — If DevRel only does content and events, product feedback loop breaks. Must be bidirectional.
2. **Measuring activity not impact** — "Published 50 blog posts" means nothing if no one reads them. Track engagement and conversion.
3. **Ignoring community health** — Toxic communities drive developers away. Moderate actively, set clear codes of conduct.
4. **SDK rot** — SDKs that aren't maintained become liabilities. Either maintain or don't ship.
5. **Docs are never done** — Documentation needs continuous updates with every release. Assign owners.

## Sources

- Developer Relations: https://developerrelations.com/
- DevRel Collective: https://devrelcollective.fun/
- Stripe Developer Experience: https://stripe.com/blog/engineering
- Twilio Developer Marketing: https://www.twilio.com/en-us/blog
- MongoDB Community: https://www.mongodb.com/community
- ReadMe (API docs): https://readme.com/
- Mintlify (docs platform): https://www.mintlify.com/
- Fern (SDK generation): https://buildwithfern.com/
- DevRelCon: https://devrelcon.dev/
- Developer Experience paper: https://queue.acm.org/detail.cfm?id=3595878
