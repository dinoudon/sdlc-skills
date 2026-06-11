# SDLC Skills

Comprehensive SDLC skill library for AI coding agents. Works with any IDE or CLI that loads context from files.

## Tiers

| Tier | Lines | Tokens | Use Case |
|------|-------|--------|----------|
| **slim/** | ~11K total (~730/skill) | ~7-10K/skill | Quick reference, daily use |
| **moderate/** | ~25K total (~1.7K/skill) | ~17-25K/skill | Planning, deep dives |
| **full/** | ~55K total (~3.7K/skill) | ~37-55K/skill | Complete reference |

## Skills (15)

| Skill | Focus |
|-------|-------|
| sdlc-adversarial-review | Security review, threat modeling, OWASP SAMM, compliance |
| sdlc-api-documentation | OpenAPI 3.1, Redoc/Swagger, SDK generation, docs-as-code, NestJS |
| sdlc-architecture-design | System design, patterns, DDD, distributed systems, hexagonal arch |
| sdlc-cicd-pipeline | CI/CD, build systems, pipeline security, GitOps |
| sdlc-database-design | Schema design, migrations, NoSQL, query optimization, scaling |
| sdlc-deployment | Deployment strategies, infrastructure, multi-tenancy, FinOps |
| sdlc-developer-tooling | Dev environments, monorepo, platform engineering, DX |
| sdlc-incident-management | On-call, severity levels, runbooks, blameless postmortems, chaos eng |
| sdlc-ml-engineering | MLOps, feature stores, model serving, drift monitoring, LLMOps |
| sdlc-observability | Monitoring, tracing, SRE, SLOs, eBPF, LGTM stack |
| sdlc-prd-to-production | Product lifecycle, stakeholder mgmt, launch, High Growth Handbook |
| sdlc-requirements-engineering | Requirements, DDD, BDD, user research, domain modeling |
| sdlc-retrospective | Retrospectives, team health, org design, Team Topologies |
| sdlc-testing-qa | Testing strategies, automation, quality, contract testing |
| sdlc-throwaway-projects | Prototypes, MVPs, spikes, vibe coding, validation |

## Install

### Hermes Agent

```bash
# Slim (default, lightweight)
cp -r slim/sdlc-* ~/.hermes/skills/software-development/

# Moderate (balanced)
cp -r moderate/sdlc-* ~/.hermes/skills/software-development/

# Full (comprehensive reference)
cp -r full/sdlc-* ~/.hermes/skills/software-development/
```

### Claude Code

```bash
# Copy skills to project context
cp -r slim/sdlc-* .claude/skills/

# Or as markdown reference in project root
cp -r slim/sdlc-* docs/sdlc-skills/
```

Claude Code loads `.claude/` context automatically. Skills become available as reference material.

### Cursor

```bash
# Option 1: Copy to project .cursorrules context
mkdir -p .cursor/skills
cp -r slim/sdlc-* .cursor/skills/

# Option 2: Add as workspace context
cp -r slim/sdlc-* docs/sdlc-skills/
```

In Cursor Settings → Features → Codebase Indexing, add the skills directory as context.

### Windsurf

```bash
# Copy to project context
mkdir -p .windsurf/skills
cp -r slim/sdlc-* .windsurf/skills/

# Or reference from docs
cp -r slim/sdlc-* docs/sdlc-skills/
```

Windsurf loads `.windsurf/` context. Add skills as project knowledge.

### VS Code + Continue

```bash
# Copy to project context directory
mkdir -p .continue/skills
cp -r slim/sdlc-* .continue/skills/
```

In Continue config (`~/.continue/config.json`), add context provider:
```json
{
  "contextProviders": [
    {
      "name": "file",
      "params": {
        "directories": [".continue/skills"]
      }
    }
  ]
}
```

### VS Code + Cline

```bash
# Copy to project docs
cp -r slim/sdlc-* docs/sdlc-skills/
```

Cline loads project files automatically. Reference skills with `@file` in prompts.

### VS Code + GitHub Copilot

```bash
# Copy to .github/copilot-instructions.md or project docs
cp -r slim/sdlc-* docs/sdlc-skills/
```

Add to `.github/copilot-instructions.md`:
```markdown
## SDLC Skills
Reference skills in docs/sdlc-skills/ for development guidance.
```

### Aider

```bash
# Copy to project docs
cp -r slim/sdlc-* docs/sdlc-skills/

# Or add as read-only context
aider --read docs/sdlc-skills/sdlc-testing-qa/SKILL.md
```

Aider loads `--read` files as context. Use for targeted skill loading.

### Codex (OpenAI)

```bash
# Copy to project docs
cp -r slim/sdlc-* docs/sdlc-skills/
```

Codex loads project files. Reference skills in AGENTS.md:
```markdown
## SDLC Skills
Reference docs/sdlc-skills/ for development guidance.
```

### OpenCode

```bash
# Copy to project context
cp -r slim/sdlc-* docs/sdlc-skills/
```

OpenCode loads project context automatically.

### Generic (Any AI Agent)

```bash
# Copy to any context directory your agent loads
cp -r slim/sdlc-* <your-agent-context-dir>/

# Or reference as documentation
cp -r slim/sdlc-* docs/sdlc-skills/
```

Most AI agents load `.md` files from project directories. Copy skills wherever your agent looks for context.

## Tier Selection Guide

| Use Case | Recommended Tier |
|----------|-----------------|
| Daily coding, quick questions | slim |
| Planning features, code review | moderate |
| Architecture decisions, deep research | full |
| Token-constrained agents | slim |
| Large context window (100K+) | moderate or full |
| Reference documentation | full (don't load into context) |

## Content Sources

See [SOURCES.md](SOURCES.md) for complete source list (160+ sources across 15 categories).

Key sources:
- DORA / State of DevOps reports
- Google SRE book & workbook
- OWASP guidelines & SAMM
- Team Topologies (Skelton & Pais)
- Accelerate (Forsgren, Humble, Kim)
- CNCF best practices
- Industry case studies (Netflix, Google, Stripe, Shopify)
- NestJS, PostgreSQL, Redis, MongoDB official docs
- MLflow, Kubeflow, Feast, BentoML docs
- PagerDuty, Gremlin, incident.io docs

## Author

Dinoudon

## Version

v5.2.1 - Three-tier structure (slim/moderate/full) with 15 skills
