# SDLC Skills for AI Agents

Skills covering the Software Development Life Cycle, designed for AI coding agents (Hermes Agent, Claude Code, Cursor, Copilot, etc.).

Each skill has: trigger conditions, step-by-step workflows, CLI commands, code examples, tool comparisons, and pitfalls.

## Skills

| Skill | Description |
|-------|-------------|
| [sdlc-requirements-engineering](sdlc-requirements-engineering/) | User stories, BDD/Gherkin, estimation, backlog prioritization, sprint planning |
| [sdlc-architecture-design](sdlc-architecture-design/) | C4 diagrams, API design (REST/GraphQL/gRPC), DB schema, Clean/DDD, ADRs |
| [sdlc-cicd-pipeline](sdlc-cicd-pipeline/) | GitHub Actions, GitLab CI, Docker, GitOps, DORA metrics |
| [sdlc-testing-qa](sdlc-testing-qa/) | Test pyramid, TDD/BDD, property-based testing, k6, SAST/DAST, accessibility |
| [sdlc-deployment](sdlc-deployment/) | Canary, blue-green, rolling deployments, feature flags, chaos engineering |
| [sdlc-developer-tooling](sdlc-developer-tooling/) | Python/JS/Go/Rust tooling, DevEx, Team Topologies |
| [sdlc-observability](sdlc-observability/) | Logging, metrics, tracing, SRE (SLIs/SLOs), post-mortems |
| [sdlc-adversarial-review](sdlc-adversarial-review/) | Multi-agent PR review, code review culture |
| [sdlc-prd-to-production](sdlc-prd-to-production/) | PRD → spec → plan → implement → review → ship → deploy pipeline |
| [sdlc-retrospective](sdlc-retrospective/) | Retrospective formats, blameless post-mortems, team culture |

## Usage

```bash
# Hermes Agent
cp -r sdlc-* ~/.hermes/skills/software-development/

# Claude Code
cp -r sdlc-* .claude/skills/

# Cursor
cp -r sdlc-* .cursor/skills/
```

Skills auto-load based on context, or load manually.

## Based On

Patterns from Accelerate, Team Topologies, Staff Engineer, DDIA, Phoenix Project, Google SRE, Software Engineering at Google, and engineering practices at Netflix, Stripe, Meta, Amazon.

## License

MIT
