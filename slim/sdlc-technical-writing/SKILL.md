---
name: sdlc-technical-writing
description: "Technical writing for software companies: documentation strategy, docs-as-code, API docs, runbooks, knowledge base, changelog, README best practices, style guides, information architecture, technical editing, video documentation, diagramming, accessibility in docs."
version: 6.0.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, technical-writing, documentation, docs-as-code, runbooks, knowledge-base, changelog, style-guide, information-architecture]
    related_skills: [sdlc-api-documentation, sdlc-developer-relations, sdlc-developer-tooling, sdlc-prd-to-production]
---## When to Use

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


## Pitfalls

1. **Docs drift** — Docs that don't match code are worse than no docs. Automate doc generation where possible.
2. **No ownership** — Docs without owners rot. Assign doc owners per area.
3. **Writing for yourself** — Write for the reader, not the author. What do they need to know?
4. **Wall of text** — Use headings, bullets, tables, code blocks. Break up dense text.
5. **No examples** — Abstract descriptions without code examples are useless for developers.

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
