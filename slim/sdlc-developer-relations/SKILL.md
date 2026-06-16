---
name: sdlc-developer-relations
description: "Developer Relations (DevRel) program design: advocacy, community, marketing, enablement. Developer experience (DX), developer journey mapping, technical content strategy, community building, SDK strategy, developer onboarding, API playground, developer feedback loops, developer marketing, developer funnel, measuring DevRel."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, developer-relations, devrel, developer-experience, dx, community, advocacy, sdk, developer-marketing, technical-content]
    related_skills: [sdlc-product-growth, sdlc-api-documentation, sdlc-prd-to-production, sdlc-developer-tooling, sdlc-gtm-strategy]
---

# Developer Relations (DevRel)

Building and nurturing developer communities. The bridge between product and developers.

## When to Use

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

## Step 4: SDK & Client Library Strategy

### SDK Design Principles
Source: Stripe, Twilio, AWS SDK

```
1. Idiomatic: Follow language conventions (Pythonic, Go-idiomatic)
2. Typed: Type hints, generics where supported
3. Consistent: Same patterns across languages
4. Minimal: Few dependencies, small footprint
5. Documented: Inline docs, generated reference
6. Tested: 90%+ coverage, integration tests
7. Versioned: Semantic versioning, deprecation warnings
8. Auto-generated: OpenAPI → SDK via openapi-generator or Fern
```

### SDK Matrix Template

```
Language  | SDK       | Status    | Coverage | Maintainer
----------|-----------|-----------|----------|------------
Python    | pip pkg   | Stable    | 100%     | @team
Node.js   | npm pkg   | Stable    | 100%     | @team
Go        | module    | Stable    | 95%      | @community
Java      | Maven     | Beta      | 90%      | @team
Ruby      | gem       | Beta      | 85%      | @community
PHP       | Composer  | Alpha     | 70%      | @community
Rust      | crate     | Community | 60%      | @external
Swift     | SPM       | Planned   | 0%       | —
```

### SDK Generation Tools

| Tool | Approach | Best For |
|------|----------|----------|
| **openapi-generator** | OpenAPI → multi-language | Broad coverage |
| **Fern** | OpenAPI → polished SDKs | Quality SDKs |
| **Kiota** | OpenAPI → typed clients | Microsoft ecosystem |
| **Stainless** | OpenAPI → SDKs (Stripe-backed) | API companies |
| **Progenitor** | OpenAPI → Rust clients | Rust SDKs |

## Step 5: Community Building

### Community Platforms

| Platform | Best For | Pros | Cons |
|----------|----------|------|------|
| **Discord** | Real-time chat, gaming/dev | Rich features, bots | Can get noisy |
| **Slack** | Professional communities | Familiar UI | Free tier limits |
| **GitHub Discussions** | Open source projects | Near code, searchable | Limited formatting |
| **Stack Overflow** | Q&A, SEO | High authority | No community feel |
| **Reddit** | Broad communities | Large audience | Less control |
| **Forum** (Discourse) | Long-form, searchable | Full control, SEO | Higher barrier |

### Community Health Metrics

```
1. Active members (monthly active posters)
2. Response time (median time to first reply)
3. Resolution rate (% questions answered)
4. Sentiment (positive/neutral/negative ratio)
5. Champion count (most active helpers)
6. Community NPS (would you recommend?)
7. Content creation (user-generated posts/month)
8. Event attendance (meetup/webinar participants)
```

### Champion/Ambassador Program

```
Levels:
1. Contributor: Occasional PRs, answers questions
2. Champion: Regular contributor, writes content, helps others
3. Ambassador: Represents community at events, mentors others

Benefits by level:
Contributor: S