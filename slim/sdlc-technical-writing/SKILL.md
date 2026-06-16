---
name: sdlc-technical-writing
description: "Technical writing for software companies: documentation strategy, docs-as-code, API docs, runbooks, knowledge base, changelog, README best practices, style guides, information architecture, technical editing, video documentation, diagramming, accessibility in docs."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, technical-writing, documentation, docs-as-code, runbooks, knowledge-base, changelog, style-guide, information-architecture]
    related_skills: [sdlc-api-documentation, sdlc-developer-relations, sdlc-developer-tooling, sdlc-prd-to-production]
---

# Technical Writing

Documentation strategy, docs-as-code, technical writing craft, and knowledge management. How Stripe, ReadMe, and Google write docs.

## When to Use

Trigger when user:
- Creates or improves project documentation
- Writes README, CONTRIBUTING, or ARCHITECTURE docs
- Designs documentation site or information architecture
- Creates runbooks or operational guides
- Writes changelogs or release notes
- Establishes style guide or writing standards
- Plans docs-as-code pipeline
- Asks "how should I document X?"

## Step 1: Documentation Strategy

### The Four Types of Documentation
Source: Divio documentation system

```
┌────────────────┬────────────────┐
│   TUTORIALS    │   HOW-TO       │
│   (Learning)   │   (Goals)      │
│                │                │
│ • Step-by-step │ • Task-focused │
│ • Beginner     │ • Intermediate │
│ • Guided       │ • Problem-solving│
│ • Safe env     │ • Real scenarios│
├────────────────┼────────────────┤
│   EXPLANATION  │   REFERENCE    │
│   (Understand) │   (Information)│
│                │                │
│ • Conceptual   │ • Accurate     │
│ • Background   │ • Complete     │
│ • Why things   │ • API specs    │
│   work         │ • Config opts  │
└────────────────┴────────────────┘
```

### Documentation Pyramid

```
                    ┌─────────┐
                    │ Guides  │  ← Deep dives, best practices
                    │ & Deep  │     (fewer, maintained by experts)
                    │ Dives   │
                ┌───┴─────────┴───┐
                │   How-To Guides  │  ← Task-based, actionable
                │   & Tutorials    │     (many, maintained by teams)
            ┌───┴──────────────────┴───┐
            │     API Reference         │  ← Auto-generated, complete
            │     & Reference Docs      │     (generated from code/OpenAPI)
        ┌───┴──────────────────────────┴───┐
        │          README & Quickstart      │  ← Entry point, always current
        │          & Getting Started        │     (maintained by everyone)
        └──────────────────────────────────┘
```

## Step 2: Docs-as-Code

### Docs-as-Code Principles

```
1. Write in Markdown (or MDX, reStructuredText, AsciiDoc)
2. Store docs in same repo as code (or adjacent docs repo)
3. Review docs in PRs (same workflow as code)
4. Version docs alongside code releases
5. Automate builds and publishing
6. Lint docs (vale, markdownlint)
```

### Docs Pipeline

```
Source (Markdown) → Lint (vale) → Build (MkDocs/Docusaurus/Hugo)
    → Preview (PR preview) → Publish (GitHub Pages/S3/Vercel)
```

### Documentation Tools

| Tool | Type | Language | Best For |
|------|------|----------|----------|
| **MkDocs** + Material | Static site | Python | Technical docs |
| **Docusaurus** | Static site | React | Community docs |
| **Hugo** | Static site | Go | Fast builds |
| **VitePress** | Static site | Vue | Vue ecosystem |
| **Starlight** | Static site | Astro | Modern docs |
| **GitBook** | Hosted | — | Non-technical teams |
| **ReadMe** | Hosted | — | API documentation |
| **Mintlify** | Hosted | — | API-first docs |
| **Redocly** | Hosted | — | OpenAPI docs |
| **Notion** | Wiki | — | Internal knowledge |

### MkDocs Material Example

```yaml
# mkdocs.yml
site_name: My Project
theme:
  name: material
  palette:
    primary: indigo
  features:
    - navigation.tabs
    - navigation.sections
    - content.code.copy
    - content.tabs.link
    - search.suggest
    - search.highlight
plugins:
  - search
  - tags
markdown_extensions:
  - admonition
  - pymdownx.details
  - pymdownx.superfences
  - pymdownx.tabbed
  - pymdownx.highlight
  - toc:
      permalink: true
nav:
  - Home: index.md
  - Getting Started:
    - Quickstart: getting-started/quickstart.md
    - Installation: getting-started/installation.md
  - Guides:
    - Authentication: guides/authentication.md
    - API Usage: guides/api-usage.md
  - Reference:
    - API: reference/api.md
    - Configuration: reference/config.md
  - Changelog: changelog.md
```

## Step 3: Writing Effective README

### README Template

```markdown
# Project Name

One-line description of what this does.

## Quick Start

```bash
# Install
npm install my-project

# Run
npx my-project --help
```

## Features

- Feature 1: brief description
- Feature 2: brief description
- Feature 3: brief description

## Documentation

- [Getting Started](docs/getting-started.md)
- [API Reference](docs/api.md)
- [Examples](docs/examples/)

## Development

```bash
# Setup
git clone https://github.com/org/repo
cd repo
npm install

# Test
npm test

# Build
npm run build
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

[MIT](LICENSE)
```

### README Anti-Patterns

```
BAD:
- 10-page README with every detail
- Installation instructions that don't work
- Badge spam (20 badges nobody clicks)
- No quick start section
- Marketing copy instead of technical info

GOOD:
- Quick start in <30 seconds
- Link to full docs for details
- Working code examples
- Clear description of what it does and why
- Contributing guide for open source
```

## Step 4: Changelog & Release Notes

### Keep a Changelog Format
Source: https://keepachangelog.com/

```markdown
# Changelog

## [1.2.0] - 2026-06-15

### Added
- New payment webhook for refund events
- Python SDK support for async/await

### Changed
- Upgraded default timeout from 30s to 60s
- Improved error messages for invalid API keys

### Deprecated
- `/v1/legacy-endpoint` — use `/v2/modern-endpoint` instead

### Removed
- Drop support for Node.js 14 (EOL)

### Fixed
- Race condition in concurrent webhook delivery
- Memory leak in long-running WebSocket connections

### Security
- Updated dependency X to fix CVE-2026-1234
```

### Semantic Versioning

```
MAJOR.MINOR.PATCH

MAJOR: Breaking changes (API contract changes, removed features)
MINOR: New features (backward compatible)
PATCH: Bug fixes (backward compatible)

Examples:
1.0.0 → 1.0.1  (bug fix)
1.0.1 → 1.1.0  (new feature)
1.1.0 → 2.0.0  (breaking change)
```

## Step 5: Runbooks & Operational Docs

### Runbook Template

```markdown
# Runbook: [Service Name] [Alert/Scenario]

## Overview
What this alert means and why it fires.

## Impact
Who/what is affected, severity level.

## Diagnosis
1. Check dashboard: [link]
2. Check logs: `kubectl logs -f deploy/service-name`
3. Check metrics: query `rate(requests_total{status="5xx"}[5m])`

## Mitigation
1. If [condition A]: [action]
2. If [condition B]: [action]
3. If unsure: page on-call lead at [contact]

## Escalation
- L1: On-call engineer (15 min)
- L2: Team lead (30 min)
- L3: Engineering manager (1 hour)

## Resolution
Steps to permanently fix (link to postmortem if applicable).

## Related
- Dashboard: [link]
- Runbook: [link]
- Postmortem: [link]
```

## Step 6: Architecture Decision Records (ADR)

### ADR Template

```markdown
# ADR-001: Use PostgreSQL as primary database

## Status
Accepted (2026-01-15)

## Context
We need a relational database for our core application.
Requirements: ACID transactions, JSON support, full-text search,
horizontal scaling, managed hosting options.

## Decision
We will use PostgreSQL 16 as our primary database.

## Consequences
### Positive
- Strong ACID compliance
- JSONB for flexible schemas
- Excellent ecosystem (ORMs, tools, hosting)