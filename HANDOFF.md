# SDLC Skills — Handoff Document

**Date:** 2026-06-16
**Status:** 23 skills (15 original + 8 new), three-tier structure
**Repository:** /root/sdlc-skills/ (local), `dinoudon/sdlc-skills` (GitHub)

---

## What This Is

Complete software company skill library. 23 skills covering the full lifecycle from idea to IPO:

| Domain | Skills |
|--------|--------|
| **Engineering** | 15 skills (adversarial-review, api-documentation, architecture-design, cicd-pipeline, database-design, deployment, developer-tooling, incident-management, ml-engineering, observability, prd-to-production, requirements-engineering, retrospective, testing-qa, throwaway-projects) |
| **Business & Growth** | 6 skills (product-growth, developer-relations, gtm-strategy, finance-ops, hiring-talent, legal-compliance) |
| **Platform & Operations** | 2 skills (platform-engineering, technical-writing) |

Each skill in 3 tiers:

| Tier | Target Lines | Use Case |
|:-----|:-------------|:---------|
| **slim** | ~800 | Quick reference, token-constrained contexts |
| **moderate** | ~2000 | Balanced — actionable code + essential theory |
| **full** | ~4500-6200 | Deep reference, comprehensive coverage |

## What's New in v6.0.0

### New Skills (8)

| Skill | Focus | Key Sources |
|-------|-------|-------------|
| sdlc-product-growth | PLG, SaaS metrics, pricing, experimentation | Reforge, OpenView, Bessemer |
| sdlc-developer-relations | DevRel, community, SDK strategy | developerrelations.com, Stripe |
| sdlc-platform-engineering | Backstage, golden paths, DORA/SPACE | Spotify, Team Topologies |
| sdlc-technical-writing | Docs-as-code, runbooks, style guides | Divio, Google Style Guide |
| sdlc-gtm-strategy | GTM, positioning, sales enablement | April Dunford, YC |
| sdlc-hiring-talent | Recruiting, interviews, career ladders | Will Larson, First Round |
| sdlc-finance-ops | Unit economics, fundraising, cap table | Bessemer, SaaStr |
| sdlc-legal-compliance | GDPR, SOC 2, IP, licensing | gdpr.eu, AICPA |

### Enhanced Existing Skills

- **prd-to-production**: Added gstack-inspired CEO review, unicorn launch patterns (Stripe, Figma, Notion, Slack)
- **retrospective**: Added Spotify model (Squads/Tribes/Chapters/Guilds), Netflix culture, Google SRE, Amazon Two-Pizza teams, Stripe practices

### Updated SOURCES.md

- 200+ sources (up from 160+)
- New sections: Growth & PLG, Developer Relations, Platform Engineering, Technical Writing, GTM & Sales, Hiring & Talent, Finance & Operations, Legal & Compliance, Unicorn Case Studies, gstack

## Skills Inventory

| Skill | Full | Moderate | Slim |
|:------|:-----|:---------|:-----|
| sdlc-adversarial-review | 4805 | 1999 | 803 |
| sdlc-api-documentation | — | 1566 | — |
| sdlc-architecture-design | 5354 | 1901 | 1565 |
| sdlc-cicd-pipeline | 4420 | 1928 | 1091 |
| sdlc-deployment | 6156 | 1905 | 807 |
| sdlc-developer-tooling | 4747 | 2095 | 1389 |
| sdlc-observability | 5068 | 2021 | 813 |
| sdlc-prd-to-production | 4527 | 1822 | 759 |
| sdlc-requirements-engineering | 4467 | 1965 | 761 |
| sdlc-retrospective | 4790 | 2247 | 652 |
| sdlc-testing-qa | 5030 | 2065 | 798 |
| sdlc-throwaway-projects | 1762 | 1016 | 741 |
| sdlc-database-design | — | — | — |
| sdlc-incident-management | — | — | — |
| sdlc-ml-engineering | — | — | — |
| **NEW** sdlc-product-growth | 459 | ~1800 | 228 |
| **NEW** sdlc-developer-relations | 342 | ~1450 | 147 |
| **NEW** sdlc-platform-engineering | 388 | ~1475 | 248 |
| **NEW** sdlc-technical-writing | 426 | ~1220 | 174 |
| **NEW** sdlc-gtm-strategy | 318 | ~1165 | 171 |
| **NEW** sdlc-hiring-talent | 409 | ~1330 | 231 |
| **NEW** sdlc-finance-ops | 364 | ~1070 | 245 |
| **NEW** sdlc-legal-compliance | 390 | ~1410 | 248 |

## Directory Structure

```
/root/sdlc-skills/
├── README.md
├── HANDOFF.md          ← this file
├── SOURCES.md          ← 200+ sources
├── full/               ← 23 skills
│   ├── sdlc-*/SKILL.md
│   └── sdlc-*/references/*.md
├── moderate/           ← 23 skills
│   └── sdlc-*/SKILL.md
└── slim/               ← 23 skills
    └── sdlc-*/SKILL.md
```

## Version Numbers

- **Full:** `6.0.0`
- **Moderate:** `6.0.0-moderate`
- **Slim:** `6.0.0-slim`

## Key Decisions Made

1. **Moderate = ~2000 lines, not strict 2000** — acceptable range is 1700-2200.
2. **Slim varies more** — 650-1565 lines. Some skills have more essential content.
3. **Frontmatter metadata preserved** — `name`, `author`, `license`, `platforms` unchanged across tiers.
4. **Content strategy:** Keep actionable code/examples/templates/checklists/pitfalls. Remove verbose explanations.
5. **New skills follow same structure** — When to Use, numbered steps, templates, pitfalls, sources.
6. **Unicorn patterns integrated** — Real company case studies (Stripe, Spotify, Netflix, Google, Amazon) embedded in relevant skills.

## Git History

```
(v6.0.0) Complete software company package: 8 new skills, unicorn patterns
(v5.2.1) Three-tier structure with 15 skills
(v5.0.0) Three-tier structure (slim/moderate/full)
(v4.9.0) Architecture patterns, Team Topologies, Multi-tenancy, FinOps
(v4.8.0) Quality review #8 - fix cross-ref, normalize versions
```

## What's NOT Done

- **No automated testing** — skills are markdown, no lint/validation framework exists
- **Full tier for new skills** — Currently copies of moderate with version bump. Need expansion.
- **Sync enhanced skills to slim/full** — prd-to-production and retrospective changes only in moderate tier
- **GitHub push** — changes are local only. Push when ready.

## Session History

This work spanned multiple sessions:
- **Epochs 1-28:** Built full skills incrementally (v1.0 → v4.8.0)
- **Epoch 29-30:** Added architecture patterns, Team Topologies, multi-tenancy, FinOps
- **v5.0.0:** Created three-tier structure (slim/moderate/full)
- **v6.0.0:** Added 8 new skills, enhanced 2 existing, updated SOURCES.md to 200+ sources
