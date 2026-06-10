# SDLC Skills

Comprehensive SDLC skill library for AI agents. Three tiers for different needs.

## Tiers

| Tier | Lines | Tokens | Use Case |
|------|-------|--------|----------|
| **slim/** | ~8K total (~800/skill) | ~8-10K/skill | Quick reference, daily use |
| **moderate/** | ~20K total (~2K/skill) | ~20-25K/skill | Planning, deep dives |
| **full/** | ~50K total (~5K/skill) | ~40-50K/skill | Complete reference |

## Install

```bash
# Slim (default, lightweight)
cp -r slim/sdlc-* ~/.hermes/skills/software-development/

# Moderate (balanced)
cp -r moderate/sdlc-* ~/.hermes/skills/software-development/

# Full (comprehensive reference)
cp -r full/sdlc-* ~/.hermes/skills/software-development/
```

## Skills (11)

| Skill | Focus |
|-------|-------|
| sdlc-adversarial-review | Security review, threat modeling, compliance |
| sdlc-architecture-design | System design, patterns, distributed systems |
| sdlc-cicd-pipeline | CI/CD, build systems, pipeline security |
| sdlc-deployment | Deployment strategies, infrastructure, DR |
| sdlc-developer-tooling | Dev environments, monorepo, platform eng |
| sdlc-observability | Monitoring, tracing, SRE, SLOs |
| sdlc-prd-to-production | Product lifecycle, incident mgmt |
| sdlc-requirements-engineering | Requirements, DDD, BDD, user research |
| sdlc-retrospective | Retrospectives, team health, org design |
| sdlc-testing-qa | Testing strategies, automation, quality |
| sdlc-throwaway-projects | Prototypes, MVPs, spikes, vibe coding, validation |

## Content Sources

- DORA / State of DevOps reports
- Google SRE book
- OWASP guidelines
- Team Topologies (Skelton & Pais)
- Accelerate (Forsgren, Humble, Kim)
- CNCF best practices
- Industry case studies (Netflix, Google, Stripe, Shopify)

## Author

Dinoudon

## Version

v5.0.0 - Three-tier structure (slim/moderate/full)
