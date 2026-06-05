---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest), JS/TS (Bun, Vitest, Biome), Go (golangci-lint), Rust (cargo). Cross-cutting: just, mise, direnv, Docker Compose, Dev Containers. Includes DevEx and Team Topologies."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, tooling, developer-experience, python, typescript, go, rust, docker, devex, team-topologies]
    related_skills: [sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-testing-qa]
---

# Developer Tooling & DX

Modern tooling for Python, JS/TS, Go, Rust. Cross-cutting: task runners, runtime managers, local dev, Dev Containers. Includes DevEx and Team Topologies.

## When to Use

Trigger when user:
- Sets up dev environment for a project
- Configures linters, formatters, type checkers
- Chooses package manager or runtime version
- Sets up Docker Compose for local dev
- Creates Dev Containers or justfile
- Measures developer experience

## Step 1: Python Tooling

### Package Manager: uv
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv init myproject
uv add fastapi uvicorn pytest ruff
uv sync
uv run pytest
```

### Linter + Formatter: Ruff
```toml
# pyproject.toml
[tool.ruff]
target-version = "py312"
line-length = 100

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "B", "A", "SIM", "TCH"]
```

```bash
ruff check src/
ruff check --fix src/
ruff format src/
```

### Type Checking: Pyright
```bash
pyright src/
```

### Testing: pytest
```bash
pytest                          # Run all
pytest -x                       # Stop on first failure
pytest -k "test_login"          # Pattern match
pytest --cov=src --cov-report=html
pytest -n auto                  # Parallel (pytest-xdist)
```

## Step 2: JavaScript/TypeScript Tooling

### Runtime: Bun
```bash
curl -fsSL https://bun.sh/install | bash
bun init
bun add express
bun add -d typescript vitest
bun run src/index.ts
```

### Linter + Formatter: Biome
```bash
bun add -d @biomejs/biome
bunx @biomejs/biome init
bunx @biomejs/biome check --write src/
```

### Test Runner: Vitest
```typescript
import { describe, it, expect } from 'vitest';

describe('add', () => {
  it('adds two numbers', () => {
    expect(add(1, 2)).toBe(3);
  });
});
```

```bash
bun test
bun test --coverage
```

### Type Checking
```bash
tsc --noEmit
```

## Step 3: Go Tooling

### Linter: golangci-lint
```yaml
# .golangci.yml
linters:
  enable:
    - errcheck
    - gosimple
    - govet
    - staticcheck
    - unused
    - gocritic
    - gofmt
    - goimports
    - revive
```

```bash
golangci-lint run ./...
golangci-lint run --fix ./...
```

### Formatter: gofumpt
```bash
gofumpt -w .
```

### Mock Generation: mockery
```bash
mockery --all --inpackage
```

### Vulnerability Scanner
```bash
govulncheck ./...
```

## Step 4: Rust Tooling

```bash
cargo clippy                  # Lint
cargo clippy -- -D warnings   # Strict
cargo fmt                     # Format
cargo deny check              # License + vulnerability audit
cargo audit                   # Security
cargo test
cargo bench
```

## Step 5: Cross-Cutting Tools

### Task Runner: just
```justfile
dev:
    docker compose up -d
    air  # hot reload

test *args:
    go test -race -cover ./... {{args}}

lint:
    golangci-lint run ./...

deploy env:
    terraform -chdir=infra/{{env}} apply
```

### Runtime Manager: mise
```bash
curl https://mise.run | sh
mise use node@20
mise use python@3.12
mise use go@1.22
```

### Environment: direnv
```bash
# .envrc
export DATABASE_URL="postgresql://localhost:5432/myapp"
export REDIS_URL="redis://localhost:6379"
```

### Docker Compose
```yaml
services:
  app:
    build: .
    ports: ["3000:3000"]
    environment:
      DATABASE_URL: postgresql://postgres:***@db:5432/myapp
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: myapp
      POSTGRES_PASSWORD: dev
    ports: ["5432:5432"]
    healthcheck:
      test: pg_isready -U postgres
      interval: 5s
```

### Dev Containers
```json
// .devcontainer/devcontainer.json
{
  "name": "My Project",
  "dockerComposeFile": "../docker-compose.yml",
  "service": "app",
  "customizations": {
    "vscode": {
      "extensions": ["ms-python.python", "charliermarsh.ruff"]
    }
  },
  "postCreateCommand": "uv sync"
}
```

## Step 6: Developer Experience (from Stripe + Google)

### DevEx Principles
- **Fast feedback** — PR feedback < 10 min
- **Self-service** — engineers provision infra without tickets
- **Paved roads** — blessed paths over hard mandates
- **Measure DevEx** — surveys, build times, deploy frequency

### Internal Developer Platform (IDP)
- CI/CD pipeline — one-click deploy
- Preview environments — per-PR staging
- Self-service infra — databases, queues, caches
- Observability — logs, metrics, traces in one place
- Documentation — searchable, versioned

### Build Speed Targets
| Metric | Target |
|--------|--------|
| Full build | < 10 min |
| Incremental build | < 30s |
| Test feedback | < 5 min |
| Deploy to staging | < 10 min |

### DevEx Survey
```markdown
## Monthly DevEx Survey (1-5 scale)

### Flow State
- How often are you interrupted during focused work?
- How easy is it to get a PR reviewed?
- How fast is your local dev environment?

### Cognitive Load
- How complex is the codebase to navigate?
- How clear are team boundaries?

### Feedback Loops
- How fast is CI feedback?
- How quickly do you get production metrics?
```

## Step 7: Team Topologies (from Skelton & Pais)

### 4 Team Types
1. **Stream-aligned** (~80%) — aligned to business flow
2. **Platform** — provides internal services
3. **Enabling** — helps teams acquire capabilities
4. **Complicated-subsystem** — owns complex subsystem

### 3 Interaction Modes
1. **Collaboration** — close working, temporary
2. **X-as-a-Service** — provider/consumer, clear API
3. **Facilitating** — helping, then stepping back

### Conway's Law
> "Organizations which design systems produce designs that mirror their communication structures."

**Reverse Conway Maneuver**: Design org structure to get desired architecture.

### Cognitive Load
- **Load-bearing** — core domain, must stay
- **Contextual** — supporting, can be shared
- **Extraneous** — toil, eliminate

## Step 8: AI-Assisted Development

### Repo Preparation
- Create AGENTS.md / CLAUDE.md with tech stack, conventions
- Define boundary rules: always do, needs approval, never touch
- Add ADRs for architecture decisions
- Document build/test/lint commands as copy-pasteable

### Prompt Best Practices (from katapultlabs)
- Be concise — cut 50-70% filler tokens
- Include only what's being modified
- Use diff-based editing
- Debug progressively (error → function → file)
- Prefer single-turn interactions
- Treat tests as statement of facts

## Pitfalls

1. **Don't use pip when uv exists** — uv is 10-100x faster
2. **Don't run ESLint + Prettier when Biome exists**
3. **Don't use npm scripts for complex tasks** — justfile is better
4. **Don't manage runtime versions manually** — mise auto-switches
5. **Don't share .env files** — use direnv + .envrc
6. **Don't use `docker compose` without healthchecks**
7. **Don't skip type checking in CI**
8. **Don't ignore DevEx surveys** — measure and improve
