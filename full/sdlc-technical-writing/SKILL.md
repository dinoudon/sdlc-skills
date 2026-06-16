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

## Step 9: Documentation Localization

### Localization Workflow

```
1. Extract translatable content
   - Separate content from code (i18n files)
   - Use .po/.pot files (gettext) or JSON/YAML
   - Mark non-translatable strings (variables, code)
   
2. Translation
   - Professional translators (preferred)
   - Machine translation + human review
   - Community translation (open source)
   
3. Tools
   - Crowdin: Cloud-based, GitHub integration
   - Lokalise: Mobile + web, API-first
   - Transifex: Enterprise, community features
   - POEditor: Simple, affordable
   
4. Quality assurance
   - Native speaker review
   - Context screenshots for translators
   - Terminology glossary
   - Consistency checking
   
5. Deployment
   - Language switcher in UI
   - URL structure: /en/docs, /ja/docs
   - hreflang tags for SEO
   - Fallback language (English)
```

### Documentation Style Guide

```
Voice and tone:
  - Professional but approachable
  - Active voice preferred
  - Second person ("you") for instructions
  - Present tense for descriptions
  
Formatting:
  - Sentence case for headings
  - Oxford comma usage
  - Serial comma in lists
  - One space after periods
  
Technical writing rules:
  - Acronyms: Define on first use
  - Numbers: Spell out 1-9, use digits for 10+
  - Code: Use inline code for commands, variables
  - Links: Descriptive text, not "click here"
  
Accessibility:
  - Alt text for all images
  - Descriptive link text
  - Proper heading hierarchy (H1 → H2 → H3)
  - Color not sole indicator
  - Screen reader testing
```

## Step 10: Documentation Maintenance

### Docs-as-Code Pipeline

```
Version control:
  - Docs in same repo as code
  - Branch strategy: docs/feature-name
  - PR review required for docs changes
  - Automated checks in CI

CI/CD for docs:
  - Lint: markdownlint, vale (prose linting)
  - Build: Static site generator (Docusaurus, MkDocs)
  - Test: Link checker, screenshot comparison
  - Deploy: Preview on PR, production on merge

Tools:
  - Markdown: MDX (React components in docs)
  - Static site: Docusaurus, MkDocs, VitePress, Astro
  - API docs: Swagger/OpenAPI, Redoc, Stoplight
  - Search: Algolia DocSearch, MeiliSearch
  - Analytics: Plausible, PostHog

Maintenance schedule:
  - Weekly: Review incoming issues/PRs
  - Monthly: Audit broken links, outdated screenshots
  - Quarterly: Review all docs for accuracy
  - Annually: Full documentation audit


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


## Step 14: Documentation Architecture

### Information Architecture

```
Diataxis framework (4 quadrants):

                    | Learning          | Working
  ──────────────────┼───────────────────┼───────────────────
  Orientation       | Tutorials         | How-to guides
  (study)           | (hands-on lessons)| (goal-oriented)
  ──────────────────┼───────────────────┼───────────────────
  Reference         | Explanation       | Reference docs
  (work)            | (understanding)   | (information)
  
Tutorial:
  - Learning-oriented
  - Beginner-friendly
  - Step-by-step with expected outcomes
  - Example: "Build your first API"

How-to guide:
  - Task-oriented
  - Assumes knowledge
  - Practical steps
  - Example: "Deploy to production"

Reference:
  - Information-oriented
  - Comprehensive, accurate
  - API docs, config options
  - Example: "REST API reference"

Explanation:
  - Understanding-oriented
  - Context and background
  - Design decisions, architecture
  - Example: "Why we chose GraphQL"
```

### Documentation Site Structure

```
/docs
  /tutorials          # Getting started, quickstarts
    /getting-started
    /quickstart
    /your-first-app
  /how-to             # Task-based guides
    /deployment
    /authentication
    /data-migration
  /reference           # Technical reference
    /api
    /cli
    /config
    /sdk
  /explanation         # Concepts and architecture
    /architecture
    /security
    /performance
  /changelog           # Version history
  /community           # Contributing, support
  /search              # Site search
```

## Step 15: Technical Review Process

### Documentation Review Checklist

```
Technical accuracy:
  □ Code examples tested and working
  □ API endpoints verified
  □ Configuration options correct
  □ Version-specific information accurate
  □ Dependencies and prerequisites listed

Clarity:
  □ Target audience defined
  □ Prerequisites stated
  □ Steps numbered and sequential
  □ Expected outcomes specified
  □ Error messages and troubleshooting included

Style:
  □ Follows style guide
  □ Consistent terminology
  □ Proper heading hierarchy
  □ Code blocks properly formatted
  □ Links working

Completeness:
  □ All features documented
  □ Edge cases covered
  □ Migration guides for breaking changes
  □ Examples cover common use cases
  □ FAQ addresses known issues


## Step 16: Content Strategy

### Content Calendar

```
Planning:
  - Monthly content planning session
  - Align with product roadmap
  - SEO keyword research
  - Audience persona mapping

Content types:
  - Blog posts (weekly)
  - Tutorials (bi-weekly)
  - Case studies (monthly)
  - Whitepapers (quarterly)
  - Videos (monthly)
  - Webinars (monthly)
  - Newsletter (weekly)

Production workflow:
  1. Ideation (keyword research, customer questions)
  2. Outline (review with stakeholders)
  3. Draft (writer)
  4. Technical review (subject matter expert)
  5. Editorial review (style, grammar)
  6. Approval (content lead)
  7. Publish (CMS)
  8. Promote (social, email, communities)

Tools:
  - Content calendar: Notion, Airtable, CoSchedule
  - SEO: Ahrefs, SEMrush, Clearscope
  - Writing: Grammarly, Hemingway, ProWritingAid
  - CMS: WordPress, Ghost, Contentful
  - Analytics: Google Analytics, Plausible
```

### SEO Content Playbook

```
Keyword research:
  1. Seed keywords from product features
  2. Competitor keyword analysis
  3. Customer question mining (support tickets, forums)
  4. Long-tail keyword identification
  5. Search intent mapping (informational, navigational, transactional)

Content optimization:
  - Title: Primary keyword, <60 characters
  - Meta description: Compelling, <160 characters
  - H1: One per page, includes keyword
  - H2/H3: Supporting keywords, logical hierarchy
  - Body: Natural keyword density (1-2%)
  - Internal links: 3-5 per post
  - External links: 2-3 authoritative sources
  - Images: Alt text, compressed, descriptive filenames
  - URL: Short, keyword-rich, hyphen-separated

Content clusters:
  Pillar page: Comprehensive guide on main topic
  Cluster pages: Supporting articles on subtopics
  Internal linking: Cluster to Pillar (and vice versa)
  Example: "CI/CD Guide" (pillar) then "GitHub Actions", "Jenkins", "GitLab CI" (clusters)
```

## Step 17: Documentation Metrics

### Content Performance Dashboard

```
Engagement metrics:
  - Page views (total, unique)
  - Average time on page
  - Scroll depth (%)
  - Bounce rate (%)
  - Pages per session

Search metrics:
  - Organic traffic (% of total)
  - Keyword rankings
  - Click-through rate (CTR)
  - Featured snippet wins

Conversion metrics:
  - Documentation to Signup conversion
  - Tutorial to Activation rate
  - API docs to Developer engagement
  - Support ticket deflection rate

Quality metrics:
  - "Was this helpful?" score
  - Feedback comments
  - Broken links count
  - Outdated content (>90 days)

Reporting:
  - Weekly: Top pages, search queries, feedback
  - Monthly: Content performance, SEO trends
  - Quarterly: ROI analysis, content audit
  - Annually: Strategy review, planning
```


## Step 18: Runbook Authoring

### Runbook Template

```
# Runbook: [Procedure Name]

## Overview
- Purpose: What this runbook accomplishes
- When to use: Trigger conditions
- Time estimate: How long it takes
- Prerequisites: Access, tools, permissions needed

## Pre-conditions
- [ ] Access to [system] confirmed
- [ ] Backup completed
- [ ] Stakeholders notified
- [ ] Maintenance window scheduled

## Procedure

### Step 1: [Action]
```bash
# Command or action
command --flag argument
```
Expected output: [What you should see]
If error: [What to do]

### Step 2: [Action]
```bash
# Command or action
command --flag argument
```
Expected output: [What you should see]
If error: [What to do]

## Rollback
If something goes wrong:
1. [Rollback step 1]
2. [Rollback step 2]
3. [Verification]

## Verification
- [ ] [Check 1]
- [ ] [Check 2]
- [ ] [Check 3]

## Troubleshooting

### Problem: [Common issue]
Cause: [Why it happens]
Solution: [How to fix]

### Problem: [Another issue]
Cause: [Why it happens]
Solution: [How to fix]

## Escalation
If unable to resolve:
- Primary: [Name/Team] via [Channel]
- Secondary: [Name/Team] via [Channel]
- Emergency: [PagerDuty/Opsgenie escalation]

## References
- [Link to related docs]
- [Link to architecture diagram]
- [Link to monitoring dashboard]
```

### Runbook Best Practices

```
Writing:
  - Assume reader has no context
  - Use imperative mood ("Run this command")
  - Include expected output for each step
  - Document failure modes and recovery
  - Keep commands copy-pasteable

Maintenance:
  - Review quarterly
  - Update after every incident use
  - Track runbook usage (which ones are used most)
  - Retire unused runbooks
  - Version control all runbooks

Testing:
  - Dry-run in staging first
  - Have someone unfamiliar follow the runbook
  - Time the execution
  - Document gaps found during testing
  - Update based on test feedback
```

## Step 19: Documentation Tooling

### Static Site Generators

```
Docusaurus (React-based):
  - Best for: Large documentation sites
  - Features: Versioning, i18n, search, MDX
  - Used by: React, Jest, Supabase
  - Deploy: Vercel, Netlify, GitHub Pages

MkDocs (Python-based):
  - Best for: Technical documentation
  - Features: Material theme, search, tags
  - Used by: FastAPI, Pydantic, Kubernetes
  - Deploy: GitHub Pages, Read the Docs

VitePress (Vue-based):
  - Best for: Fast, lightweight docs
  - Features: Vue components, search, sidebar
  - Used by: Vue, Vite, Rollup
  - Deploy: Vercel, Netlify, GitHub Pages

Astro (Framework-agnostic):
  - Best for: Content-heavy sites
  - Features: Island architecture, MDX, collections
  - Used by: Google, Microsoft, NASA
  - Deploy: Any static host

Selection criteria:
  - Framework alignment (React team = Docusaurus)
  - Scale (thousands of pages = Docusaurus)
  - Speed (fast builds = VitePress)
  - Flexibility (custom components = Astro)
```

### API Documentation Tools

```
Swagger/OpenAPI:
  - Auto-generate from code annotations
  - Interactive "Try it" explorer
  - Client SDK generation
  - Used by: Most REST APIs

Redoc:
  - Beautiful OpenAPI rendering
  - Three-panel layout
  - Search and navigation
  - Used by: Stripe, GitHub

Stoplight:
  - Visual API design
  - Mock servers
  - Testing and monitoring
  - Used by: enterprises

GraphQL:
  - GraphQL Playground (interactive)
  - GraphQL Voyager (visual schema)
  - Apollo Studio (management)
  - Used by: GitHub, Shopify, Airbnb

Tools for generating docs:
  - TypeDoc (TypeScript)
  - JSDoc (JavaScript)
  - Sphinx (Python)
  - GoDoc (Go)
  - Rustdoc (Rust)
```

## Step 20: Accessibility in Documentation

### WCAG Compliance for Docs

```
Level A (minimum):
  - All images have alt text
  - Videos have captions
  - Color is not sole indicator
  - Keyboard navigation works
  - Focus indicators visible

Level AA (target):
  - Contrast ratio 4.5:1 (text), 3:1 (large text)
  - Text resizable to 200%
  - Multiple ways to find content
  - Consistent navigation
  - Error identification and suggestions

Level AAA (ideal):
  - Contrast ratio 7:1
  - Sign language for videos
  - Reading level appropriate
  - Pronunciation guides for jargon

Implementation:
  - Use semantic HTML (headings, lists, tables)
  - Add ARIA labels where needed
  - Test with screen readers (VoiceOver, NVDA)
  - Automated testing (axe, Lighthouse)
  - Manual testing with keyboard only
```

### Inclusive Language Guide

```
Do use:
  - "Allowlist" instead of "whitelist"
  - "Blocklist" instead of "blacklist"
  - "Primary/secondary" instead of "master/slave"
  - "They" as singular pronoun
  - "Users" or "people" instead of "guys"

Avoid:
  - Ableist language ("crazy", "insane", "lame")
  - Gendered language ("he/she", "guys", "manpower")
  - Racial/ethnic references in examples
  - Cultural assumptions (holidays, names)

Code examples:
  - Use diverse names in examples
  - Use universal date formats (ISO 8601)
  - Use 24-hour time or specify AM/PM
  - Use metric and imperial (or specify which)
```

## Step 21: Documentation CI/CD

### Docs Pipeline

```
Trigger: Push to main or PR merge

Stage 1: Lint
  - markdownlint (formatting rules)
  - vale (prose linting, style guide)
  - link checker (broken links)
  - spell checker (custom dictionary)

Stage 2: Build
  - Static site generator (Docusaurus, MkDocs)
  - API docs generation (Swagger, TypeDoc)
  - Search index (Algolia, MeiliSearch)

Stage 3: Test
  - Link validation (all internal links work)
  - Screenshot comparison (visual regression)
  - Accessibility scan (axe-core)
  - Performance check (Lighthouse)

Stage 4: Deploy
  - Preview (PR gets preview URL)
  - Production (merge to main deploys)
  - CDN invalidation (clear cache)
  - Monitoring (uptime, error tracking)

Tools:
  - GitHub Actions / GitLab CI
  - Netlify / Vercel (preview deploys)
  - Algolia DocSearch (search)
  - Sentry (error tracking)
```

### Versioned Documentation

```
Versioning strategy:
  - /docs/latest/ (current release)
  - /docs/v2.0/ (previous major)
  - /docs/v1.0/ (legacy)
  - /docs/canary/ (unreleased features)

Implementation:
  - Branch per version (main = latest, v2.0 branch)
  - Version selector dropdown
  - "You are viewing docs for vX.Y" banner
  - Link to latest from older versions
  - Redirect old URLs to archived versions

Maintenance:
  - Keep 2-3 major versions
  - Archive versions older than 2 years
  - Update security docs for all supported versions
  - Deprecation notices on old versions


## Step 22: Documentation Governance

### Documentation Standards

```
Ownership model:
  - Product teams own their feature docs
  - Docs team owns style guide and tooling
  - Engineering owns API and architecture docs
  - Support owns troubleshooting and FAQ

Review process:
  - All docs changes go through PR review
  - Technical accuracy: SME approval required
  - Style compliance: Docs team approval required
  - Code examples: Must be tested in CI

Quality gates:
  - No broken links (automated check)
  - All code examples pass tests
  - Style guide compliance (vale linting)
  - Accessibility check (automated)
  - SEO check (meta tags, structure)

Metrics:
  - Documentation coverage (% of features documented)
  - Documentation freshness (% updated in last 90 days)
  - Documentation quality (feedback scores)
  - Documentation usage (page views, search queries)
```

### Documentation Audits

```
Quarterly audit checklist:
  □ Review all pages for accuracy
  □ Check all links (internal and external)
  □ Verify code examples still work
  □ Update screenshots if UI changed
  □ Review feedback and address issues
  □ Archive outdated content
  □ Update changelog
  □ Review SEO performance
  □ Check accessibility compliance
  □ Update style guide if needed

Annual audit:
  □ Full content inventory
  □ Gap analysis (what is missing)
  □ User journey mapping
  □ Information architecture review
  □ Competitive analysis
  □ Technology stack evaluation
  □ Resource planning
```

## Step 23: Internationalization for Docs

### i18n Documentation Workflow

```
Content extraction:
  - Separate translatable content from code
  - Use .po/.pot files (gettext) or JSON/YAML
  - Mark non-translatable strings (variables, code)
  - Create glossary of technical terms

Translation process:
  1. Extract new/changed content
  2. Send to translation team/service
  3. Review translations for accuracy
  4. Import translated content
  5. Build and deploy localized docs

Tools:
  - Crowdin: Cloud-based, GitHub integration
  - Lokalise: Mobile + web, API-first
  - Transifex: Enterprise, community features
  - POEditor: Simple, affordable
  - Weglot: Website translation

Quality assurance:
  - Native speaker review
  - Context screenshots for translators
  - Terminology glossary
  - Consistency checking
  - User testing in target language
```

### Localized Documentation Structure

```
URL structure:
  /docs/en/ (English - default)
  /docs/ja/ (Japanese)
  /docs/de/ (German)
  /docs/pt-br/ (Brazilian Portuguese)

Implementation:
  - Subdirectories per language
  - hreflang tags for SEO
  - Language switcher in UI
  - Fallback to English for missing translations
  - Separate sitemap per language

Content strategy:
  - English first (always)
  - Priority languages based on user base
  - Community translations for less common languages
  - Machine translation + human review for speed
  - Professional translation for critical content
```

## Step 24: Documentation Analytics

### Metrics Dashboard

```
Engagement metrics:
  - Page views (total, unique)
  - Average time on page
  - Scroll depth (%)
  - Bounce rate (%)
  - Pages per session

Search metrics:
  - Top search queries
  - Searches with no results
  - Search-to-click ratio
  - Search refinement rate

Helpfulness metrics:
  - "Was this helpful?" vote ratio
  - Feedback comments
  - Support ticket deflection rate
  - Documentation NPS

Quality metrics:
  - Broken links count
  - Outdated pages (>90 days since update)
  - Missing pages (404s)
  - Accessibility issues

Conversion metrics:
  - Docs to signup conversion
  - Docs to trial conversion
  - Docs to paid conversion
  - API key generation from docs

Tools:
  - Google Analytics 4 (page views, engagement)
  - Algolia Analytics (search metrics)
  - Hotjar (heatmaps, session recordings)
  - Custom feedback widgets
  - Sentry (error tracking)
```

### Content Performance Analysis

```
Top performing content:
  - Identify pages with highest traffic
  - Analyze what makes them successful
  - Replicate patterns in other content
  - Update and improve top pages regularly

Underperforming content:
  - High bounce rate: Improve intro, add TOC
  - Low time on page: Add more depth, examples
  - No search traffic: Improve SEO, add keywords
  - Negative feedback: Address specific issues

Content gap analysis:
  - Search queries with no results → Write new content
  - Support tickets for undocumented features → Document
  - Competitor content we lack → Create equivalent
  - User requests for specific topics → Prioritize
