---
name: sdlc-technical-writing
description: "Technical writing for software companies: documentation strategy, docs-as-code, API docs, runbooks, knowledge base, changelog, README best practices, style guides, information architecture, technical editing, video documentation, diagramming, accessibility in docs."
version: 6.0.0
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
- Managed options (RDS, Cloud SQL, Neon)

### Negative
- Horizontal scaling requires careful sharding
- No built-in multi-region replication
- Team needs to learn PostgreSQL-specific features

### Risks
- May need to migrate to distributed DB at >10TB scale

## Alternatives Considered
- MySQL: Less JSON support, weaker full-text search
- CockroachDB: Higher cost, smaller ecosystem
- MongoDB: No ACID transactions (until 4.0+)
```

## Step 7: Style Guide

### Technical Writing Principles

```
1. Use active voice ("The server processes requests" not "Requests are processed")
2. Use present tense ("The function returns" not "The function will return")
3. Use second person for instructions ("You can configure..." not "Users can configure...")
4. Use sentence case for headings ("Getting started" not "Getting Started")
5. Be concise (if you can say it in 5 words, don't use 10)
6. Define jargon on first use
7. Use code formatting for code (commands, paths, variables)
8. Use admonitions for warnings, notes, tips
```

### Google Developer Documentation Style Guide

Key rules:
- Use "sign in" not "log in" or "login"
- Use "email" not "e-mail"
- Don't use "please" in instructions
- Don't use "simple" or "easy" (it's not simple for everyone)
- Use serial comma (a, b, and c)
- Write for scanning (bullets, tables, short paragraphs)

Source: https://developers.google.com/style

## Step 8: Diagramming

### Diagram Types & Tools

| Type | Use Case | Tool |
|------|----------|------|
| Architecture | System components | Mermaid, PlantUML, Excalidraw |
| Sequence | API flows | Mermaid, PlantUML |
| Entity-Relationship | Database schema | Mermaid, dbdiagram.io |
| Flowchart | Decision trees | Mermaid, draw.io |
| C4 Model | System context | Structurizr, PlantUML |
| Network | Infrastructure | draw.io, Lucidchart |

### Mermaid Example (in Markdown)

```markdown
```mermaid
sequenceDiagram
    participant Client
    participant API
    participant DB
    Client->>API: POST /payments
    API->>DB: INSERT payment
    DB-->>API: OK
    API-->>Client: 201 Created
```​
```

## Step 8: Documentation Quality Metrics

### Documentation Health Dashboard

```
Metrics to track:

Coverage:
  □ % of API endpoints with documentation
  □ % of features with how-to guides
  □ % of error codes with troubleshooting docs
  □ % of internal services with runbooks

Freshness:
  □ Days since last update per doc
  □ % of docs updated in last 90 days
  □ Docs/code sync rate (automated checks)

Usage:
  □ Page views per doc
  □ Search queries with no results (gaps)
  □ Bounce rate on docs pages
  □ Time on page (engagement)

Quality:
  □ "Was this helpful?" rating
  □ Open doc issues count
  □ Vale linting score
  □ Broken link count
```

### Documentation Review Checklist

```
□ Accuracy: Does it match current behavior?
□ Completeness: Are all options/parameters covered?
□ Clarity: Can a new user follow this?
□ Examples: Are there working code samples?
□ Structure: Is it scannable (headings, bullets, tables)?
□ Links: Do all cross-references work?
□ Tone: Consistent with style guide?
□ Accessibility: Alt text, proper headings, readable?
□ Versioning: Does it apply to the current version?
□ Searchability: Does it use terms users search for?
```

## Step 9: API Documentation Best Practices

### OpenAPI 3.1 Document Structure

```yaml
openapi: 3.1.0
info:
  title: Payment API
  version: 1.2.0
  description: Process payments, manage customers, handle refunds
  contact:
    email: api-support@company.com
  license:
    name: MIT

servers:
  - url: https://api.company.com/v1
    description: Production
  - url: https://sandbox.company.com/v1
    description: Sandbox

paths:
  /payments:
    post:
      summary: Create a payment
      description: |
        Creates a payment intent for processing a payment.
        Use this endpoint to initiate a new payment flow.
      operationId: createPayment
      tags: [Payments]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreatePaymentRequest'
            examples:
              card_payment:
                summary: Card payment
                value:
                  amount: 2000
                  currency: usd
                  payment_method: pm_card_visa
      responses:
        '201':
          description: Payment created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Payment'
        '400':
          description: Invalid request
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Error'
```

### Error Response Documentation

```yaml
# Every error should document:
# - HTTP status code
# - Error code (machine-readable)
# - Message (human-readable)
# - Details (field-level validation errors)
# - Suggestion (how to fix)

components:
  schemas:
    Error:
      type: object
      properties:
        error:
          type: object
          properties:
            code:
              type: string
              example: "invalid_amount"
              description: Machine-readable error code
            message:
              type: string
              example: "Amount must be a positive integer"
              description: Human-readable error message
            param:
              type: string
              example: "amount"
              description: The parameter that caused the error
            type:
              type: string
              enum: [invalid_request_error, api_error, authentication_error]
            doc_url:
              type: string
              example: "https://docs.company.com/errors#invalid_amount"
```

## Step 10: Knowledge Base Architecture

### Information Architecture Template

```
docs/
├── index.md                    # Landing page
├── getting-started/
│   ├── quickstart.md          # 5-minute setup
│   ├── installation.md        # Detailed install
│   └── first-steps.md         # After install guide
├── guides/
│   ├── authentication.md      # How-to: auth
│   ├── payments.md            # How-to: payments
│   ├── webhooks.md            # How-to: webhooks
│   └── error-handling.md      # How-to: errors
├── reference/
│   ├── api.md                 # Full API reference
│   ├── sdks.md                # SDK reference
│   ├── errors.md              # Error code reference
│   └── config.md              # Configuration reference
├── concepts/
│   ├── architecture.md        # System design
│   ├── data-model.md          # Data structures
│   └── security.md            # Security model
├── changelog.md               # Version history
└── support/
    ├── faq.md                 # Common questions
    ├── troubleshooting.md     # Debugging guide
    └── contact.md             # Support channels
```

## Pitfalls

1. **Docs drift** — Docs that don't match code are worse than no docs. Automate doc generation where possible.
2. **No ownership** — Docs without owners rot. Assign doc owners per area.
3. **Writing for yourself** — Write for the reader, not the author. What do they need to know?
4. **Wall of text** — Use headings, bullets, tables, code blocks. Break up dense text.
5. **No examples** — Abstract descriptions without code examples are useless for developers.
6. **Outdated screenshots** — Don't screenshot UI unless you automate screenshot updates.
7. **Docs as afterthought** — Write docs before or during development, not after.
8. **No feedback mechanism** — Add "Was this helpful?" buttons, GitHub issues link.
9. **Translation too early** — Don't translate docs until the source is stable and well-maintained.
10. **Perfectionism** — Published docs that are 80% good > perfect docs that are never published.

## Sources

- Divio Documentation System: https://documentation.divio.com/
- Google Developer Style Guide: https://developers.google.com/style
- Write the Docs: https://www.writethedocs.org/
- Keep a Changelog: https://keepachangelog.com/
- ADR GitHub: https://adr.github.io/
- MkDocs Material: https://squidfunk.github.io/mkdocs-material/
- Docusaurus: https://docusaurus.io/
- Vale (prose linter): https://vale.sh/
- Mermaid diagrams: https://mermaid.js.org/
- Stripe Docs (example): https://docs.stripe.com/
