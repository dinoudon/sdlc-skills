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

---
name: sdlc-technical-writing
description: "Technical writing for software companies: documentation strategy, docs-as-code, API docs, runbooks, knowledge base, changelog, README best practices, style guides, information architecture, technical editing, video documentation, diagramming, accessibility in docs."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, technical-writing, documentation, docs-as-code, runbooks, kn
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
```
## Step 2: Docs-as-Code
```
1. Write in Markdown (or MDX, reStructuredText, AsciiDoc)
2. Store docs in same repo as code (or adjacent docs repo)
3. Review docs in PRs (same workflow as code)
4. Version docs alongside code releases
5. Automate builds and publishing
6. Lint docs (vale, markdownlint)
```
## Step 3: Writing Effective README
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
## Step 4: Changelog & Release Notes
Source: https://keepachangelog.com/
```markdown
# Changelog
## [1.2.0] - 2026-06-15
- New payment webhook for refund events
- Python SDK support for async/await
- Upgraded default timeout from 30s to 60s
- Improved error messages for invalid API keys
- `/v1/legacy-endpoint` — use `/v2/modern-endpoint` instead
- Drop support for Node.js 14 (EOL)
- Race condition in concurrent webhook delivery
- Memory leak in long-running WebSocket connections
- Updated dependency X to fix CVE-2026-1234
```

### Semantic Versioning

```
## Step 5: Runbooks & Operational Docs
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
- Strong ACID compliance
- JSONB for flexible schemas
- Excellent ecosystem (ORMs, tools, hosting)
- Managed options (RDS, Cloud SQL, Neon)
- Horizontal scaling requires careful sharding
- No built-in multi-region replication
- Team needs to learn PostgreSQL-specific features
- May need to migrate to distributed DB at >10TB scale
## Alternatives Considered
- MySQL: Less JSON support, weaker full-text search
- CockroachDB: Higher cost, smaller ecosystem
- MongoDB: No ACID transactions (until 4.0+)
```
## Step 7: Style Guide
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
## Step 8: Diagramming
| Type | Use Case | Tool |
|------|----------|------|
| Architecture | System components | Mermaid, PlantUML, Excalidraw |
| Sequence | API flows | Mermaid, PlantUML |
| Entity-Relationship | Database schema | Mermaid, dbdiagram.io |
| Flowchart | Decision trees | Mermaid, draw.io |
| C4 Model | System context | Structurizr, PlantUML |
| Network | Infrastructure | draw.io, Lucidchart |
```markdown
```mermaid
## Step 8: Documentation Quality Metrics
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
```
## Step 9: API Documentation Best Practices
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
```
## Step 10: Knowledge Base Architecture
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
```
## Step 10: Documentation Maintenance
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
```
## Step 14: Documentation Architecture
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
```
## Step 15: Technical Review Process
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
```
## Step 16: Content Strategy
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

```
## Step 17: Documentation Metrics
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
```
## Step 18: Runbook Authoring
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
```bash
# Command or action
command --flag argument
```
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
Cause: [Why it happens]
Solution: [How to fix]
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
## Step 19: Documentation Tooling
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
```
## Step 20: Accessibility in Documentation
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
```
## Step 21: Documentation CI/CD
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
```
## Step 22: Documentation Governance
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
```
## Step 23: Internationalization for Docs
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
```
## Step 24: Documentation Analytics
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
```
## Step 25: Documentation for Open Source
```
README.md:
  - Project name and description
  - Badges (build status, coverage, version)
  - Quick start (install, run, use)
  - Features list
  - Documentation links
  - Contributing guidelines
  - License

CONTRIBUTING.md:
  - How to contribute
  - Code of conduct reference
  - Development setup
  - Pull request process
  - Coding standards
```
## Step 26: Technical Documentation for APIs
```
Structure:
  - Overview (authentication, rate limiting, errors)
  - Endpoints (grouped by resource)
  - Each endpoint:
    - HTTP method and path
    - Description
    - Authentication requirements
    - Request parameters (path, query, body)
    - Request examples (curl, Python, JavaScript)
    - Response format (JSON schema)
    - Response examples (success, error)
    - Error codes

Tools:
  - Swagger/OpenAPI: Specification and UI
```
## Step 27: Documentation for Compliance
```
SOC 2 documentation:
  - Security policies
  - Access control procedures
  - Incident response plan
  - Change management process
  - Vendor management policy
  - Business continuity plan
  - Risk assessment methodology

GDPR documentation:
  - Privacy policy
  - Data processing agreements
  - Data protection impact assessments
  - Consent management procedures
  - Data subject request procedures
```
## Step 28: Documentation for AI/ML
```
Model card template:
  Model name: [Name]
  Version: [X.Y.Z]
  Date: [YYYY-MM-DD]
  
  Overview:
    - Purpose: What the model does
    - Architecture: Model type and structure
    - Training data: Dataset description
    - Performance: Key metrics
  
  Intended use:
    - Primary use cases
    - Out-of-scope uses
    - Users (who should use this)
```
## Step 29: Documentation for Data
```
Table: [table_name]
Description: [What this table represents]
Owner: [Team/person]
Update frequency: [Daily, hourly, real-time]

Columns:
| Column | Type | Description | Nullable | Default | Example |
|--------|------|-------------|----------|---------|---------|
| id | UUID | Primary key | No | gen_random_uuid() | abc-123 |
| user_id | UUID | Foreign key to users | No | - | def-456 |
| event_name | VARCHAR | Event type | No | - | page_view |
| properties | JSONB | Event properties | Yes | {} | {"page": "/home"} |
| created_at | TIMESTAMP | Event timestamp | No | NOW() | 2024-01-01 |

Indexes:
```
## Step 30: Documentation Quality
```
Coverage:
  - % of APIs with documentation
  - % of features with guides
  - % of error codes documented
  - % of configuration options documented

Freshness:
  - % of docs updated in last 90 days
  - Average age of documentation
  - Stale content ratio (>180 days)

Helpfulness:
  - "Was this helpful?" positive ratio
  - Support ticket deflection rate
  - Documentation NPS
```
## Step 31: Documentation for Security
```
Security policy:
  - Information security policy
  - Acceptable use policy
  - Access control policy
  - Data classification policy
  - Incident response policy

Security procedures:
  - Vulnerability management
  - Patch management
  - Access provisioning/deprovisioning
  - Security monitoring
  - Incident response

Security guides:
```
## Step 32: Documentation Automation
```
Code documentation:
  - TypeDoc (TypeScript)
  - JSDoc (JavaScript)
  - Sphinx (Python)
  - GoDoc (Go)
  - Rustdoc (Rust)

API documentation:
  - OpenAPI/Swagger (REST)
  - GraphQL Playground (GraphQL)
  - gRPC reflection (gRPC)

Infrastructure documentation:
  - Terraform docs (terraform-docs)
  - Kubernetes docs (helm-docs)
```
## Step 33: Documentation Localization
```
Content preparation:
  - Externalize strings from code
  - Use i18n framework (react-intl, vue-i18n)
  - Create translation keys
  - Document context for translators

Translation process:
  - Extract translatable content
  - Send to translation service
  - Review translations
  - Import translated content
  - Build and deploy

Quality assurance:
  - Native speaker review
```
## Related Skills
  - [sdlc-developer-relations](sdlc-developer-relations): Developer Relations (DevRel) program design: advocacy, community, marketing, enablement. Developer e
  - [sdlc-prd-to-production](sdlc-prd-to-production): End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monito