# HANDOFF.md — SDLC Skills Project

## Current State

**Version:** v7.0.0
**Last Updated:** 2026-06-16
**Total Skills:** 23
**Total Lines:** ~39K (moderate) + ~13K (slim) + ~40K (full)

## Skills Inventory

All skills have 3 tiers: slim (~600 lines), moderate (~1900 lines), full (same as moderate).

### Engineering SDLC (11 skills)
| Skill | Lines | Description |
|-------|-------|-------------|
| sdlc-adversarial-review | 2005 | Security review, threat modeling, OWASP |
| sdlc-architecture-design | 1907 | System design, patterns, DDD, distributed systems |
| sdlc-cicd-pipeline | 1934 | CI/CD, build systems, GitOps, pipeline security |
| sdlc-deployment | 1911 | Deployment strategies, infrastructure, multi-tenancy |
| sdlc-developer-tooling | 2100 | Dev environments, monorepo, DX, tooling |
| sdlc-observability | 2027 | Monitoring, tracing, SRE, SLOs, LGTM stack |
| sdlc-prd-to-production | 1907 | Product lifecycle, stakeholder mgmt, launch |
| sdlc-requirements-engineering | 1970 | Requirements, DDD, BDD, user research |
| sdlc-retrospective | 2251 | Retrospectives, team health, Spotify model, culture |
| sdlc-testing-qa | 2071 | Testing strategies, automation, quality, contract testing |
| sdlc-throwaway-projects | 1918 | Prototypes, MVPs, spikes, validation |

### Business & Growth (6 skills)
| Skill | Lines | Description |
|-------|-------|-------------|
| sdlc-developer-relations | 1927 | DevRel, community, SDK strategy, DX |
| sdlc-finance-ops | 1911 | Unit economics, fundraising, cap table, SaaS metrics |
| sdlc-gtm-strategy | 2185 | Go-to-market, positioning, sales, partnerships |
| sdlc-hiring-talent | 1981 | Recruiting, interviews, onboarding, retention |
| sdlc-legal-compliance | 1954 | GDPR, SOC 2, IP, privacy, employment law |
| sdlc-product-growth | 1913 | PLG, SaaS metrics, pricing, experimentation |

### Platform & Operations (2 skills)
| Skill | Lines | Description |
|-------|-------|-------------|
| sdlc-platform-engineering | 1907 | Backstage, golden paths, DORA, DORA metrics |
| sdlc-technical-writing | 1916 | Docs-as-code, runbooks, style guides, API docs |

### Emerging Domains (4 skills)
| Skill | Lines | Description |
|-------|-------|-------------|
| sdlc-ai-engineering | 879 | LLMOps, prompt engineering, model integration, AI safety |
| sdlc-data-engineering | 893 | Data pipelines, data quality, data mesh, data lakehouse |
| sdlc-devsecops | 955 | Supply chain security, SBOMs, policy-as-code, zero-trust |
| sdlc-green-software | 873 | Sustainability, carbon-aware computing, energy efficiency |

## Features

- **3-tier structure:** Slim (quick ref), Moderate (planning), Full (reference)
- **Cross-references:** Each skill links to 2-3 related skills
- **Practical focus:** Code examples, templates, checklists, SQL queries
- **Unicorn patterns:** Stripe, Slack, Notion, Figma, Spotify, Netflix, Google, Amazon
- **200+ sources:** DORA, Google SRE, OWASP, Team Topologies, Accelerate

## Installation

```bash
# Hermes Agent
cp -r moderate/sdlc-* ~/.hermes/skills/software-development/

# Claude Code
cp -r slim/sdlc-* .claude/skills/

# Cursor
cp -r slim/sdlc-* .cursor/skills/
```

## Author

Dinoudon
