# GitHub-Discovered SDLC Patterns

Patterns discovered from community repos during research (2026-06-05). Integrate into workflows when applicable.

## Sources
- `bzellman/earp-kit` — Public agentic SDLC framework for Claude Code skills, commands, workflows
- `katapultlabs/engineering-playbook` — Centralized knowledge base for software engineering best practices
- `jtsang4/efficient-coding` — Curated collection of reusable AI coding skills and engineering playbooks

---

## PRD-to-Production Pipeline (earp-kit)

8-stage orchestration from requirement to deployed code:

```
1. PRD Generation      → Comprehensive PRD with all requirements (no deferred scope)
2. Specification       → Structured implementation plan in plan mode
3. Plan Refinement     → Multi-model counsel (independent review by 2+ models)
4. Implementation      → Multi-agent development (dispatched by change type)
5. Simplification      → Code clarity and consistency pass
6. Adversarial Review  → 4-agent PR review (see below)
7. Feedback Address    → Resolve review findings
8. Self-Healing Deploy → 7-phase merge to production (see below)
```

Key insight: each stage is a discrete skill/command. Pipeline chains them with handoff context.

## Adversarial PR Review (earp-kit)

3 parallel specialized reviewers + 1 orchestrator:

| Agent | Focus |
|-------|-------|
| Architecture Reviewer | Design patterns, separation of concerns, scalability |
| Security Reviewer | OWASP top 10, auth issues, injection vectors |
| Code Quality Reviewer | Readability, test coverage, naming, duplication |
| Orchestrator | Synthesizes findings, deduplicates, prioritizes, applies fixes |

**When to use:** Before merging PRs to main, after significant refactors, security-sensitive changes.
**When NOT:** Trivial changes (typos, version bumps), draft PRs, hotfixes.

Implementation approach for Hermes:
```
delegate_task(tasks=[
    {"goal": "Review PR for architecture concerns...", "role": "leaf"},
    {"goal": "Review PR for security vulnerabilities...", "role": "leaf"},
    {"goal": "Review PR for code quality...", "role": "leaf"},
])
# Synthesize results in parent agent
```

## Self-Healing Deploy Pipeline (earp-kit)

7-phase autonomous deployment:

| Phase | Action |
|-------|--------|
| 0. Recon | Classify changes (backend/infra/ios), determine relevant CI checks |
| 1. Merge | Merge PR, handle conflicts |
| 2. Self-Heal CI | Monitor CI → if fail: read logs → classify → fix → re-push |
| 3. DB Migrations | Run SQL migrations, verify, rollback if fail |
| 4. Terraform Apply | Apply infrastructure changes |
| 5. Dev Verification | Smoke tests against dev endpoint, check logs |
| 6. Promote | Staging → Production promotion |
| 7. Monitor | Watch production metrics, auto-rollback if degradation |

Key insight: Phase 2 auto-diagnoses CI failures by category:
- **Build failure** → fix imports, compilation errors
- **Test failure** → fix assertions, update snapshots
- **Lint failure** → auto-format, fix style
- **Infra failure** → retry transient, fix config

## Standards-Driven Development (earp-kit / Agent OS)

5-command cycle for injecting project standards into agent context:

1. **discover-standards** — Scan codebase for implicit/explicit conventions
2. **index-standards** — Categorize by type (code style, arch, testing, deploy)
3. **inject-standards** — Filter relevant standards for current task
4. **plan-product** — Plan features with standards awareness
5. **shape-spec** — Structure implementation specs using standards

Key insight: agents produce better code when standards are injected as context. Without it, agents guess about architecture, conventions, and boundaries.

## Design-OS Pipeline (earp-kit)

10-command product design lifecycle:

```
Strategy:  product-vision → product-roadmap
Foundation: data-model → design-tokens
Structure:  design-shell → design-screen
Detail:     shape-section → sample-data
Output:     screenshot-design → export-product
```

Product design is inherently sequential — can't design screens without a shell, can't design a shell without tokens.

---

## AI-Assisted Development Best Practices (katapultlabs)

Key rules for AI-assisted SDLC:

### Repository Preparation
- Create AGENTS.md/CLAUDE.md/.cursorrules with tech stack, conventions, boundaries
- Define three-tier boundary rules: **always do**, **human approval required**, **never touch**
- Add SKILL.md files for complex repeatable procedures (deployments, migrations)
- Maintain ADRs (Architecture Decision Records) for "why" behind decisions
- Provide domain glossary mapping business terms to code representations

### Prompt Engineering
- Concise Goal-Oriented (CGO) prompting — emphasize functional objectives, not step-by-step
- Include only files being modified — irrelevant files degrade output quality
- Use diff-based editing — saves 31-98% output tokens vs full file regeneration
- TSV costs half the tokens of JSON; only use JSON when schema validation required
- Debug progressively: error message first → function → file
- Prefer single-turn interactions — multi-turn degrades accuracy by up to 39%

### Testing as Truth
- Tests are the only mechanism that forces AI agents to respect business logic
- AI agents are complacent by default — test suites are the statement of facts
- Document build/test/lint/deploy commands as copy-pasteable with full flags

### Code for Agents
- Explicit naming, small focused files, co-located intent comments
- Add `.aiignore` for .env files, secrets, credentials
- Provide `llms.txt` with machine-readable project summary
