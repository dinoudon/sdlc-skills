---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest, mypy), JS/TS (pnpm, Vitest, Biome, Playwright), Go (golangci-lint, go test -race), Rust (cargo). Cross-cutting: just, mise, Docker Compose, Dev Containers. LSP/DAP, AI-assisted dev, conventional commits, semver, monorepo tooling, CI/CD patterns, build caching, artifact management, DX metrics."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, tooling, developer-experience, python, typescript, go, rust, docker, lsp, monorepo, conventional-commits, semver, dx]
    related_skills: [sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-testing-qa, sdlc-adversarial-review]
---

# Developer Tooling & DX (Slim)

Modern tooling for Python, JS/TS, Go, Rust. Task runners, version managers, local dev, Dev Containers, LSP/DAP, AI-assisted dev, monorepo patterns, DX metrics.

## When to Use

Trigger when user:
- Sets up dev environment, configures linters/formatters/type checkers
- Chooses package manager or runtime version
- Sets up Docker Compose, Dev Containers, or justfile
- Configures monorepo tooling, release automation, conventional commits
- Measures developer experience

---

## Step 1: Python Tooling

### Package Manager: uv
Source: https://docs.astral.sh/uv/ — 10-100x faster than pip. Replaces pip/pip-tools/pipx/poetry/pyenv.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv init myproject && cd myproject
uv add fastapi uvicorn pytest ruff mypy pre-commit
uv sync && uv run pytest

# Lockfile
uv pip compile pyproject.toml -o requirements.lock
uv pip sync requirements.lock

# Python version management
uv python install 3.12 && uv python pin 3.12

# Dependency tree
uv tree --depth 1 && uv tree --invert

# Inline script deps (PEP 723)
# /// script
# requires-python = ">=3.12"
# dependencies = ["requests", "rich"]
# ///
uv run script.py
```

### Linter + Formatter: Ruff
Source: https://docs.astral.sh/ruff/ — Replaces flake8, isort, black, pylint, pyupgrade. 800+ rules.

```toml
# pyproject.toml
[tool.ruff]
target-version = "py312"
line-length = 100
[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "B", "A", "SIM", "TCH"]
[tool.ruff.format]
quote-style = "double"
```
```bash
uv run ruff check --fix . && uv run ruff format .
```

### Type Checker: mypy
```toml
# pyproject.toml
[tool.mypy]
strict = true
python_version = "3.12"
plugins = ["pydantic.mypy"]
```
```bash
uv run mypy src/
```
**Alternative:** pyright/pylance (faster, VS Code native)

### Testing: pytest
```bash
uv run pytest --tb=short -x                    # Fast fail
uv run pytest --cov=src --cov-report=html       # Coverage
uv run pytest -n auto                           # Parallel (pytest-xdist)
```

### Pre-commit
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
  - repo: https://github.com/pre-commit/mirrors-mypy
    hooks:
      - id: mypy
```

### Recommended Python Stack
```bash
uv init myproject && cd myproject
uv add --dev ruff pytest pytest-cov pytest-xdist mypy pre-commit
uv run ruff check --fix . && uv run pytest && uv run mypy src/
```

---

## Step 2: JavaScript/TypeScript Tooling

### Package Manager: pnpm
Source: https://pnpm.io/ — Fast, disk-efficient. Content-addressable store. Strict: no phantom deps.

```bash
pnpm create vite myapp --template react-ts
cd myapp
pnpm add -D vitest @vitest/coverage-v8 biome playwright
pnpm dlx biome init && pnpm dlx playwright install
```

### pnpm Catalog Protocol (Monorepo)
```yaml
# pnpm-workspace.yaml
packages: ["packages/*"]
catalog:
  react: ^18.3.1
  typescript: ^5.5.0
```
```json
// packages/app/package.json
{ "dependencies": { "react": "catalog:" } }
```
Named catalogs for migration: `"react": "catalog:react17"`. Single source of truth.

### Linter + Formatter: Biome
Source: https://biomejs.dev/ — Replaces ESLint + Prettier. 30-50x faster.

```bash
pnpm dlx biome init
pnpm dlx biome check --write .  # Lint + format + import sort
```
```json
// biome.json
{ "organizeImports": { "enabled": true }, "linter": { "enabled": true, "rules": { "recommended": true } } }
```

### Testing: Vitest
Source: https://vitest.dev/ — Vite-native. Drop-in Jest replacement. Native ESM/TS/JSX without config.

```typescript
import { describe, it, expect } from 'vitest';
describe('calculateDiscount', () => {
  it('applies percentage discount', () => {
    expect(calculateDiscount(100, 0.1)).toBe(90.0);
  });
});
```
```bash
pnpm vitest              # Watch mode
pnpm vitest run          # Single run
pnpm vitest --coverage
```

### E2E: Playwright
```bash
pnpm dlx playwright install
pnpm dlx playwright test
pnpm dlx playwright codegen http://localhost:3000  # Record tests
```

### Type Checking
```bash
pnpm tsc --noEmit
```
```json
// tsconfig.json
{ "compilerOptions": { "strict": true, "module": "NodeNext", "moduleResolution": "NodeNext" } }
```

---

## Step 3: Go Tooling

### Testing
```bash
go test ./...                    # Run all tests
go test -cover ./...             # Coverage
go test -race ./...              # Race detector (ALWAYS in CI)
go test -bench=. ./...           # Benchmarks
```

**Table-driven tests (idiomatic):**
```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 1, 2, 3},
        {"negative", -1, -2, -3},
        {"zero", 0, 0, 0},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            assert.Equal(t, tt.expected, Add(tt.a, tt.b))
        })
    }
}
```

### Linting: golangci-lint
Source: https://golangci-lint.run/

```yaml
# .golangci.yml
linters:
  enable:
    - errcheck - govet - staticcheck - gosec - revive
    - gocritic - bodyclose - nilerr - exhaustive
```
```bash
golangci-lint run ./...
golangci-lint run --fix ./...
```

### Recommended Go CI
```yaml
steps:
  - run: go test -race -cover ./...
  - uses: golangci/golangci-lint-action@v6
  - run: govulncheck ./...
```

---

## Step 4: Rust Tooling

```bash
cargo build && cargo test
cargo clippy          # Lint
cargo fmt             # Format
cargo audit           # Security
cargo deny check      # License + security + bans
```

---

## Step 5: Cross-Cutting Tools

### Task Runner: just
Source: https://just.systems/

```justfile
set shell := ["bash", "-euo", "pipefail", "-c"]
default:
    @just --list
test:
    pytest --tb=short -x
lint:
    ruff check --fix . && ruff format .
deploy env="staging":
    ./deploy.sh {{env}}
```

### Version Manager: mise
Source: https://mise.jdx.dev/ — Polyglot version manager + task runner. Replaces asdf, nvm, pyenv, rbenv.

```toml
# .mise.toml
[tools]
python = "3.12"
node = "20"
go = "1.22"
rust = "latest"
[tasks]
lint = "ruff check --fix . && ruff format ."
test = "pytest --tb=short -x"
deploy = { run = "./deploy.sh {{arg(name='env', default='staging')}}", depends = ["lint", "test"] }
[env]
DATABASE_URL = "postgres://localhost/myapp_dev"
_.path = ["./node_modules/.bin"]
```
```bash
mise install && mise use python=3.12
mise tasks && mise run lint
```

### Environment:***@db:5432/myapp
    volumes: [".:/app", "/app/node_modules"]
    depends_on:
      db:
        condition: service_healthy
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: password
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5
    volumes:
      - pgdata:/var/lib/postgresql/data
volumes:
  pgdata:
```

### Docker Compose Watch Mode
```yaml
services:
  web:
    build: .
    develop:
      watch:
        - action: sync
          path: ./src
          target: /app/src
        - action: rebuild
          path: package.json
```
```bash
docker compose watch            # Start + live-reload on file changes
```
**Watch actions:** `sync` (copy file), `rebuild` (rebuild image), `sync+restart` (copy + restart).

---

## Step 6: Dev Containers
Source: https://containers.dev/

```json
// .devcontainer/devcontainer.json
{
  "name": "My Project",
  "image": "mcr.microsoft.com/devcontainers/python:3.12",
  "features": {
    "ghcr.io/devcontainers/features/node:1": { "version": "20" },
    "ghcr.io/devcontainers/features/docker-in-docker:1": {}
  },
  "postCreateCommand": "uv sync && pnpm install",
  "customizations": {
    "vscode": {
      "extensions": ["ms-python.python", "ms-python.vscode-pylance", "charliermarsh.ruff"]
    }
  }
}
```
Works in VS Code, GitHub Codespaces, JetBrains.

### Expanded Dev Container with Compose
```jsonc
{
  "name": "fullstack",
  "dockerComposeFile": "docker-compose.devcontainer.yml",
  "service": "app",
  "workspaceFolder": "/workspace",
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {},
    "ghcr.io/devcontainers/features/node:1": { "version": "20" }
  },
  "customizations": {
    "vscode": {
      "extensions": ["ms-python.python", "charliermarsh.ruff", "eamodio.gitlens"],
      "settings": { "editor.formatOnSave": true }
    }
  },
  "postCreateCommand": "uv sync && pnpm install",
  "forwardPorts": [3000, 5432]
}
```

---

## Step 7: LSP & DAP Patterns

### LSP Language Servers
| Language | Server | Install |
|----------|--------|---------|
| Python | pyright | `npm i -g pyright` |
| Go | gopls | `go install golang.org/x/tools/gopls@latest` |
| TypeScript | typescript-language-server | `npm i -g typescript-language-server` |
| Rust | rust-analyzer | `rustup component add rust-analyzer` |

### DAP Adapters
| Language | Adapter | Install |
|----------|---------|---------|
| Python | debugpy | `pip install debugpy` |
| Go | delve | `go install github.com/go-delve/delve/cmd/dlv@latest` |
| TypeScript | js-debug | Built into VS Code |

### Neovim LSP Config
```lua
local lspconfig = require('lspconfig')
local capabilities = require('cmp_nvim_lsp').default_capabilities()
local on_attach = function(_, bufnr)
  local map = function(keys, func, desc)
    vim.keymap.set('n', keys, func, { buffer = bufnr, desc = 'LSP: ' .. desc })
  end
  map('gd', vim.lsp.buf.definition, 'Go to definition')
  map('gr', vim.lsp.buf.references, 'References')
  map('K', vim.lsp.buf.hover, 'Hover')
  map('<leader>ca', vim.lsp.buf.code_action, 'Code action')
  map('<leader>rn', vim.lsp.buf.rename, 'Rename')
end
local servers = { 'pyright', 'gopls', 'ts_ls', 'rust_analyzer', 'ruff' }
for _, server in ipairs(servers) do
  lspconfig[server].setup({ on_attach = on_attach, capabilities = capabilities })
end
```

**Lazy.nvim plugin spec:**
```lua
return {
  { 'neovim/nvim-lspconfig' },
  { 'hrsh7th/nvim-cmp', dependencies = { 'hrsh7th/cmp-nvim-lsp', 'hrsh7th/cmp-buffer' } },
  { 'williamboman/mason.nvim', config = true },
  { 'williamboman/mason-lspconfig.nvim', ensure_installed = { 'pyright', 'gopls', 'ts_ls', 'rust_analyzer', 'ruff' } },
}
```

---

## Step 8: Testcontainers

Throwaway Docker containers for integration tests. No shared state.

```python
# Python
import pytest
from testcontainers.postgres import PostgresContainer
@pytest.fixture(scope="session")
def postgres():
    with PostgresContainer("postgres:16") as pg:
        yield pg
def test_user_query(postgres):
    engine = create_engine(postgres.get_connection_url())
```
```typescript
// TypeScript
import { PostgreSqlContainer } from '@testcontainers/postgresql';
const container = await new PostgreSqlContainer('postgres:16').start();
const pool = new Pool({ connectionString: container.getConnectionUri() });
await container.stop();
```

---

## Step 9: Structured Logging

```python
# Python — structlog
import structlog
structlog.configure(processors=[
    structlog.contextvars.merge_contextvars,
    structlog.processors.add_log_level,
    structlog.processors.TimeStamper(fmt="iso"),
    structlog.dev.ConsoleRenderer() if __debug__ else structlog.processors.JSONRenderer(),
])
log = structlog.get_logger()
log.info("user_login", user_id=123, method="oauth", latency_ms=42)
```
```typescript
// TypeScript — pino
import pino from 'pino';
const logger = pino({ transport: { target: 'pino-pretty' } });
logger.info({ userId: 123, action: 'login' }, 'User logged in');
```
```go
// Go — slog (stdlib)
import "log/slog"
logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
logger.Info("user_login", "user_id", 123, "method", "oauth")
```
**Key:** JSON in production, pretty-print in dev. Single library, two renderers via env var.

---

## Pitfalls

1. **Don't use pip** — use uv (10-100x faster)
2. **Don't use ESLint + Prettier** — use Biome (30-50x faster)
3. **Don't use Jest** — use Vitest (Vite-native, faster)
4. **Don't skip `go test -race`** — always in CI
5. **Don't use nvm/pyenv separately** — use mise (polyglot)
6. **Don't skip pre-commit hooks** — catch issues before CI
7. **Don't skip Dev Containers** — reproducible env for all devs
8. **Don't use Makefile for tasks** — use just (simpler syntax)
9. **Don't skip type checking** — mypy/pyright/tsc in CI
10. **Don't skip structured logging** — JSON logs from day 1

---

## Step 10: AI-Assisted Development Tools

### AI Coding Assistants Comparison
| Feature | GitHub Copilot | Cursor | Sourcegraph Cody | Codeium / Windsurf |
|---------|---------------|--------|------------------|---------------------|
| **Type** | Extension | Standalone editor | Extension | Extension + standalone |
| **Codebase indexing** | `@workspace` in chat | Automatic | Deep indexing | Workspace-aware |
| **Agentic mode** | Copilot Workspace | Composer (multi-file) | Limited | Cascade (multi-step) |
| **IDE support** | VS Code, JetBrains, Neovim | Own editor only | VS Code, JetBrains | VS Code, JetBrains, own |
| **Self-hosted** | No | No | Yes (Enterprise) | No |
| **Pricing** | $10/mo individual | $20/mo Pro | Free tier, $9/mo Pro | Free tier, $10/mo Pro |

**Selection:** VS Code + GitHub → **Copilot** | Best AI-native editor → **Cursor** | Enterprise, self-hosted → **Cody** | Budget-constrained → **Windsurf**

### Prompt Engineering Patterns

**Role Assignment:**
```
You are a senior Python developer specializing in FastAPI and async programming.
Write code following PEP 8, using type hints, and preferring composition over inheritance.
```

**Few-Shot Examples:**
```
Convert function signatures to Result types:
Input:  def divide(a: int, b: int) -> float:
Output: def divide(a: int, b: int) -> Result[float, str]:
Now convert:
def fetch_user(user_id: int) -> User:
```

**Chain-of-Thought:**
```
Refactor this monolith into microservices. Think step by step:
1. Identify bounded contexts
2. Map dependencies between contexts
3. Propose service boundaries
4. List shared vs per-service data stores
5. Draft migration plan with rollback strategy
```

**Output Format Control:**
```
Review this function. Output format:
- BUGS: [list with line numbers]
- PERFORMANCE: [optimization suggestions]
- STYLE: [naming/formatting issues]
- IMPROVED: [rewritten function]
```

### AI Pair Programming Best Practices

**Boilerplate, Not Architecture:** Use AI for CRUD endpoints, data models, test fixtures. Do NOT let AI design system architecture or make security decisions.

**Review All AI Code:** Treat every AI-generated block like a PR from an unknown contributor:
- Edge cases handled? (null, empty, overflow)
- Error messages user-safe? (no stack traces, no secrets)
- Types correct? (semantically, not just no errors)
- Hallucinated API? (verify imports, function signatures)

```bash
# Always verify AI suggestions
uv run ruff check --fix . && uv run mypy src/ && uv run pytest
```

**Break Tasks Small:** Large prompts produce wrong outputs. Incremental:
```
1. "Create User model with SQLAlchemy 2.0 mapped_column syntax"
2. "Add Pydantic schemas for User create/read/update"
3. "Write FastAPI router with CRUD endpoints"
4. "Add cursor-based pagination"
5. "Add JWT auth dependency"
```

### AI Anti-Patterns
1. **Blindly Accepting** — Enable "preview before accept". Read every line.
2. **Compensating for Lack of Understanding** — Study AI-generated code. AI accelerates competent devs, doesn't replace learning.
3. **Context Pollution** — Paste only relevant function/section, not entire files.
4. **Prompt-Driven Without Tests** — After every AI interaction, run tests. Code that hasn't been executed doesn't exist.

### LLM-Powered Test Generation

**Patterns:** Scaffold-and-Refine, Property-Based (Hypothesis), Test Migration (unittest → pytest), Negative Tests (adversarial inputs), Coverage Gap (feed uncovered functions to AI).

```bash
uv run pytest --cov=src --cov-report=json
# Then feed uncovered functions to AI
```

**Rules:** AI-generated tests catch ~60% of real bugs. Always verify expected values. Never trust AI for security-critical tests (auth, crypto). Run tests immediately after generation.

---

## Step 11: Conventional Commits & Semver

### Format
```
<type>[optional scope]: <description>

feat(auth): add OAuth2 PKCE flow
fix(api): prevent race condition on order creation
feat!: change user API response format    # BREAKING CHANGE

Version bumps:
  fix:       → PATCH (1.0.0 → 1.0.1)
  feat:      → MINOR (1.0.0 → 1.1.0)
  feat!:     → MAJOR (1.0.0 → 2.0.0)

No bump: docs, style, refactor, perf, test, build, ci, chore
```

### commitlint
```bash
npm install -D @commitlint/cli @commitlint/config-conventional husky
echo "module.exports = { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js
npx husky init
echo 'npx --no -- commitlint --edit $1' > .husky/commit-msg
```

### Semver
```
MAJOR: incompatible API changes (feat!:, BREAKING CHANGE: footer)
MINOR: backward-compatible new functionality (feat:)
PATCH: backward-compatible bug fixes (fix:, perf:)

Version ranges (npm):
  ^1.2.3  → >=1.2.3 <2.0.0  (caret)
  ~1.2.3  → >=1.2.3 <1.3.0  (tilde)
  1.2.3   → exactly 1.2.3    (pinned)
```

---

## Step 12: Release Automation

### release-please (Google)
```yaml
# .github/workflows/release.yml
name: Release
on:
  push:
    branches: [main]
permissions:
  contents: write
  pull-requests: write
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: googleapis/release-please-action@v4
        with:
          release-type: node  # or python, go, rust, java, etc.
```

### Changesets (Monorepo-Friendly)
```bash
pnpm add -D @changesets/cli && npx changeset init

# Developer workflow:
# 1. Make changes
# 2. npx changeset → select packages + bump type + summary
# 3. Commit changeset file
# 4. PR merged → bot creates "Version Packages" PR
# 5. Merge version PR → publishes updated packages
```

### release-please vs semantic-release
| Aspect | release-please | semantic-release |
|--------|---------------|------------------|
| Creator | Google | open-source community |
| Approach | Creates PR with changelog | Auto-commits + tags on merge |
| Visibility | PR lets humans review release | Fully automated |
| Multi-language | Yes (20+ release types) | Node.js native, plugins for others |

### Pre-release Workflows
```bash
npx changeset pre enter alpha   # enter alpha mode
npx changeset version           # creates 1.0.0-alpha.0
npx changeset publish           # publishes to npm with alpha tag
npx changeset pre exit          # back to normal mode
npm publish --tag next          # publish as @next
```

---

## Step 13: Monorepo vs Polyrepo

### When to Use Which
```
Monorepo wins when:
  - Shared code/libraries across services
  - Atomic commits across projects needed
  - Consistent tooling/linting/formatting
  - Single CI/CD pipeline per change
  - You have/will invest in build tooling

Polyrepo wins when:
  - Teams are autonomous, different tech stacks
  - Different release cadences
  - Strong service boundaries
  - Teams can't agree on tooling
  - You don't have build tooling budget
```

### Build Tooling Comparison
| Tool | Ecosystem | Incremental | Remote Cache | Remote Exec | Language |
|------|-----------|-------------|--------------|-------------|----------|
| Nx | JS/TS primary | Yes (affected) | Yes (Nx Cloud) | No | JS/TS, some polyglot |
| Turborepo | JS/TS | Yes | Yes (Vercel) | No | JS/TS only |
| Bazel | Polyglot | Yes (fine-grained) | Yes | Yes | Any (via rules) |
| Pants | Polyglot | Yes | Yes | Yes | Python, Go, Java |
| Moon | Polyglot | Yes | Yes | No | JS/TS, Rust, Go |

---

## Step 14: Monorepo Tooling

### Turborepo
```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "globalDependencies": ["**/.env.*local"],
  "globalEnv": ["CI"],
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**", ".next/**"], "env": ["NODE_ENV"] },
    "test": { "dependsOn": ["build"], "outputs": [], "cache": true },
    "lint": { "outputs": [] },
    "dev": { "cache": false, "persistent": true }
  }
}
```
```bash
turbo run build test lint          # Build graph-aware pipeline
turbo run build --filter=web       # Scope to package
turbo run build --dry-run          # Preview task graph
turbo login && turbo link          # Enable remote cache
```
**Key patterns:** `^build` = run upstream deps first. `outputs` = files to cache. `env` = env vars baked into hash. `cache: false` = disable for dev/watch tasks.

### Nx Affected
```bash
npx create-nx-workspace@latest myorg --preset=ts
nx affected -t lint test build    # only builds changed + dependents
nx graph                          # interactive dep graph in browser
```
```json
// nx.json
{
  "namedInputs": {
    "default": ["{projectRoot}/**/*", "sharedGlobals"],
    "production": ["default", "!{projectRoot}/**/*.spec.ts"]
  },
  "targetDefaults": {
    "test": { "inputs": ["default", "^production"], "cache": true },
    "build": { "inputs": ["production"], "cache": true }
  }
}
```
**Nx Cloud distributed execution:** `npx nx-cloud start-ci-run --distribute-on="5 linux-medium-js"`

### Bazel
```bash
# .bazelrc
build --remote_cache=grpcs://cache.example.com:443
build --remote_download_minimal
```
```python
# BUILD file
go_library(name = "order_lib", srcs = ["order.go"], deps = ["//proto/order:order_proto", "//shared/errors"])
go_test(name = "order_test", deps = [":order_lib"])
```
```bash
bazel test //services/order:order_test    # only rebuilds changed deps
```
**Key principle:** Content-addressable storage. Same inputs → same hash → cache hit. Reproducibility mandatory.
**REAPI servers:** BuildBuddy, EngFlow, Buildfarm (open source), NativeLink.

### Pants Build System
Source: https://www.pantsbuild.org/ — Python-first. Automatic dependency inference.

```toml
# pants.toml
[GLOBAL]
pants_version = "2.22.0"
backend_packages = ["pants.backend.python", "pants.backend.python.lint.ruff", "pants.backend.python.typecheck.mypy"]
```
```python
# BUILD file — deps inferred from imports
python_sources()
python_tests()
pex_binary(name="server", entry_point="server.py")
```
```bash
pants dependencies src/app/server.py  # shows inferred deps
pants --changed-since=origin/main test
```

---

## Step 15: Monorepo Pitfalls & Solutions

### Dependency Hell
```bash
# pnpm: single lockfile at root, strict isolation
# .npmrc
shamefully-hoist=false
auto-install-peers=true
strict-peer-dependencies=true

# Find phantom dependencies
npx depcheck  # unused + missing deps
npx knip      # unused exports, types, files

# Find version conflicts
pnpm why lodash && pnpm dedupe

# Enforce version alignment
npx syncpack list-mismatches && npx syncpack fix-mismatches
```

### CI Scaling for Monorepos
```yaml
# GitHub Actions: Nx affected
steps:
  - uses: actions/checkout@v4
    with:
      fetch-depth: 0  # full history for affected detection
  - uses: nrwl/nx-set-shas@v4
  - run: npx nx affected -t lint test build --base=${{ env.NX_BASE }} --head=${{ env.NX_HEAD }}

# Turborepo affected
  - run: npx turbo run build test lint --filter=...[origin/main]
```

### Build Time Growth Mitigations (priority order)
```
1. Affected-only CI         → 10-50x speedup for small PRs
2. Remote caching            → near-instant for cache hits
3. Distributed execution     → parallelize across agents
4. Incremental TypeScript    → tsc --build with project references
5. Module federation         → don't rebuild unchanged micro-frontends
6. Bazel/Pants hermetic      → precise invalidation
7. Docker layer optimization → COPY package*.json first, then COPY src/
```

### Code Ownership
```gitignore
# .github/CODEOWNERS
*                       @org/platform-team
/packages/frontend/     @org/frontend-team
/packages/api/          @org/backend-team
*.tf                    @org/infra-team
Dockerfile*             @org/platform-team
pnpm-lock.yaml          @org/platform-team
* @techlead-alice @techlead-bob  # fallback
```

---

## Step 16: Monorepo Directory Structure

```
monorepo/
├── apps/                    # deployable applications
│   ├── web/                 # frontend app
│   ├── api/                 # backend service
│   └── worker/              # background job processor
├── packages/                # shared libraries
│   ├── ui/                  # UI component library
│   ├── utils/               # shared utilities
│   ├── config/              # shared configs (eslint, tsconfig)
│   └── tsconfig/            # base TypeScript configs
├── tools/                   # build scripts, generators
├── pnpm-workspace.yaml
├── package.json             # root: scripts, devDeps
├── turbo.json               # or nx.json
├── tsconfig.json            # root references
└── .github/
    └── CODEOWNERS
```

```yaml
# pnpm-workspace.yaml
packages:
  - "apps/*"
  - "packages/*"
  - "tools/*"
```

```json
// Root package.json
{
  "name": "@myorg/monorepo",
  "private": true,
  "scripts": {
    "build": "turbo run build",
    "test": "turbo run test",
    "lint": "turbo run lint",
    "dev": "turbo run dev",
    "changeset": "changeset",
    "version-packages": "changeset version"
  },
  "devDependencies": { "turbo": "^2.0.0", "@changesets/cli": "^2.27.0" },
  "packageManager": "pnpm@9.0.0"
}
```

---

## Step 17: GitHub Actions Advanced

### Reusable Workflows
```yaml
# .github/workflows/reusable-deploy.yml
name: Reusable Deploy
on:
  workflow_call:
    inputs:
      environment: { required: true, type: string }
      image_tag: { required: true, type: string }
    secrets:
      DEPLOY_TOKEN: { required: true }
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ inputs.environment }}
    steps:
      - uses: actions/checkout@v4
      - run: echo "Deploying ${{ inputs.image_tag }} to ${{ inputs.environment }}"
```
**Caller:**
```yaml
jobs:
  deploy-staging:
    uses: ./.github/workflows/reusable-deploy.yml
    with:
      environment: staging
      image_tag: ${{ needs.build.outputs.tag }}
    secrets:
      DEPLOY_TOKEN: ${{ secrets.STAGING_DEPLOY_TOKEN }}
```
Cross-repo: `uses: org/repo/.github/workflows/deploy.yml@main`

### Composite Actions
```yaml
# .github/actions/setup-project/action.yml
name: Setup Project
inputs:
  node-version: { default: '20' }
  python-version: { default: '3.12' }
runs:
  using: composite
  steps:
    - uses: actions/setup-node@v4
      with: { node-version: '${{ inputs.node-version }}' }
    - uses: actions/setup-python@v5
      with: { python-version: '${{ inputs.python-version }}' }
    - shell: bash
      run: npm ci
```
Usage: `- uses: ./.github/actions/setup-project`

### Dynamic Matrix with fromJSON
```yaml
jobs:
  prepare:
    runs-on: ubuntu-latest
    outputs:
      matrix: ${{ steps.set-matrix.outputs.matrix }}
    steps:
      - id: set-matrix
        run: echo 'matrix={"service":["api","web","worker"]}' >> "$GITHUB_OUTPUT"
  build:
    needs: prepare
    strategy:
      matrix: ${{ fromJSON(needs.prepare.outputs.matrix) }}
      fail-fast: false
    steps:
      - run: echo "Building ${{ matrix.service }}"
```

### Concurrency Groups
```yaml
# Cancel in-progress runs on same branch/PR
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

# Environment-level (max 1 deploy at a time)
concurrency:
  group: deploy-${{ github.event.inputs.environment }}
  cancel-in-progress: false
```

---

## Step 18: GitLab CI Advanced

### Child Pipelines
```yaml
generate:
  stage: build
  script: python generate_pipeline.py > child-pipeline.yml
  artifacts: { paths: [child-pipeline.yml] }
child:
  stage: test
  trigger:
    include:
      - artifact: child-pipeline.yml
        job: generate
    strategy: depend  # parent waits for child
```

### DAG with needs Keyword
```yaml
build-api:
  stage: build
  script: make build-api
test-api:
  stage: test
  needs: [build-api]  # runs as soon as build-api done
  script: make test-api
deploy:
  stage: deploy
  needs: [test-api, test-web]  # runs when both tests pass
  script: make deploy
```
Minimal DAG: `needs: []` runs immediately.

### Resource Groups
```yaml
deploy-production:
  stage: deploy
  resource_group: production
  script: make deploy-prod
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

---

## Step 19: Build Caching Deep Dive

### Docker Layer Caching with buildx
```bash
# Cache to registry
docker buildx build \
  --cache-from type=registry,ref=ghcr.io/org/cache:buildcache \
  --cache-to type=registry,ref=ghcr.io/org/cache:buildcache,mode=max \
  -t myapp:latest .

# GitHub Actions inline cache
docker buildx build \
  --cache-from type=gha \
  --cache-to type=gha,mode=max \
  -t myapp:latest .
```
**mode=max** caches all layers (including intermediate). **mode=min** only final layers.

### Gradle Build Cache
```kotlin
// settings.gradle.kts
buildCache {
    local { enabled = true }
    remote<HttpBuildCache> {
        url = uri("https://cache.example.com/cache/")
        push = System.getenv("CI") != null  // CI pushes, local pulls only
    }
}
```
```bash
# gradle.properties
org.gradle.caching=true
org.gradle.configuration-cache=true
```

### Cache Invalidation Strategies
```
1. Content-hash: Hash lockfile. Invalidate on dependency change.
2. Time-based: TTL expiry (7 days). Prevents stale accumulation.
3. Version-bump: Bump cache version prefix when build infra changes.
4. Selective: Cache per-component (monorepo). Invalidate only affected.
```

**Anti-patterns:**
- Caching node_modules directly (cache .npm or pnpm store instead)
- One global cache key for entire repo
- Caching build output that depends on env vars

---

## Step 20: Artifact Management

### OCI Artifacts with oras
```bash
oras push ghcr.io/org/artifacts:v1.0 \
  --artifact-type application/vnd.example.config.v1 \
  config.json:application/json
oras pull ghcr.io/org/artifacts:v1.0
oras copy ghcr.io/org/app:v1 ghcr.io/backup/app:v1
```

### Sigstore Signing with Cosign (Keyless)
```bash
# Keyless sign (in CI, uses OIDC token automatically)
COSIGN_EXPERIMENTAL=1 cosign sign ghcr.io/org/app@sha256:abc123...

# Verify
COSIGN_EXPERIMENTAL=1 cosign verify \
  --certificate-identity-regexp="https://github.com/org/app" \
  --certificate-oidc-issuer="https://token.actions.githubusercontent.com" \
  ghcr.io/org/app@sha256:abc123...

# Sign a file
cosign sign-blob --bundle sig.bundle ./artifact.tar.gz
cosign verify-blob --bundle sig.bundle ./artifact.tar.gz
```

### SBOM Generation
```bash
# Syft (Anchore)
syft ghcr.io/org/app:latest -o cyclonedx-json > sbom.cdx.json
syft ghcr.io/org/app:latest -o spdx-json > sbom.spdx.json

# Attach SBOM to OCI image
oras attach --artifact-type application/spdx+json \
  ghcr.io/org/app:v1 sbom.spdx.json:application/spdx+json

# Grype (vulnerability scanner)
grype sbom:sbom.cdx.json
grype sbom:sbom.cdx.json --fail-on critical
```

**CycloneDX vs SPDX:**
```
CycloneDX (OWASP): JSON/XML, security/vulnerability focus. Preferred for app deps.
SPDX (Linux Foundation): ISO standard, legal/license focus. Preferred for license auditing.
```

---

## Step 21: Code Review Best Practices

### Key Metrics (Google Research)
- Review speed: ~400 LOC/hour (diminishing returns after 60 min)
- Optimal review size: < 400 LOC
- Response time: < 1 hour for initial response
- Multiple small reviews > one large review

### Comment Prefixes
```
nit:        Style/formatting. Non-blocking.
suggestion: Alternative approach. Optional.
question:   Clarification only.
issue:      Bug or correctness problem. Must fix.
todo:       Follow-up. Can be deferred.
praise:     Positive feedback. Use generously.
thought:    Thinking out loud. Neither block nor unblock.
```

### Review Checklist
```
Correctness:  Does it solve the problem? Edge cases? Error handling? Concurrency safe?
Design:       Right abstraction? Follows patterns? Single responsibility?
Testing:      Happy path + edge cases? Deterministic? Error paths tested?
Security:     Input validated? Auth checked? No secrets? SQL injection/XSS prevented?
Performance:  No N+1 queries? Reasonable memory? Right data structures?
Maintainability: Readable without comments? Clear naming? Magic numbers extracted?
```

---

## Step 22: Git Internals

### Object Model
```
blob:    File content (bytes, no filename)
tree:    Directory listing (maps filenames to blobs/trees)
commit:  Snapshot pointer (tree + parent + author + message)
tag:     Annotated tag (points to commit + tagger + message)
```
```bash
git cat-file -t <hash>     # show type (blob/tree/commit/tag)
git cat-file -p <hash>     # pretty-print contents
```

### Reflog (Local History)
```bash
git reflog                    # HEAD history
# Rescue: undo rebase disaster
git reflog                    # find pre-rebase SHA
git reset --hard HEAD@{5}     # go back 5 HEAD movements
```

### Cherry-Pick vs Rebase
```
cherry-pick: Copies specific commits. Creates NEW SHAs. Use: backport fix.
  git cherry-pick abc123
  git cherry-pick abc123..def456   # range (exclusive start)

rebase: Replays entire branch onto new base. Creates NEW SHAs. Linearizes history.
  git rebase main
  git rebase -i HEAD~5             # interactive: squash, reorder, edit
```

**Interactive rebase commands:** pick, reword, edit, squash, fixup, drop

**Safety:** Never rebase pushed commits on shared branches. Use `git push --force-with-lease` if you must.

---

## Step 23: Developer Onboarding

### Time-to-First-Commit Benchmarks
```
Elite:    < 1 day    — pre-configured dev env, clear docs, good first issues
Good:     1-3 days   — setup script exists, docs current, buddy assigned
Average:  3-7 days   — manual setup, docs partially outdated
Below:    > 7 days   — broken setup, missing docs, no process
```

### Tiered Onboarding Checklist

**Week 1 — Environment & First Contribution:**
- [ ] Dev environment setup via `make setup` or Dev Container
- [ ] Clone repo, run tests locally, verify green
- [ ] Read ARCHITECTURE.md, CONTRIBUTING.md, ADRs
- [ ] Shadow a code review
- [ ] Complete "Day 1 Pull Request"
- [ ] Meet onboarding buddy
- [ ] Access: source control, CI, monitoring, incident tooling

**Weeks 2-4 — Domain & Ownership:**
- [ ] Pick up 2-3 "good first issues"
- [ ] Attend 1 design review
- [ ] Write or update 1 piece of documentation
- [ ] Deploy to staging independently
- [ ] Pair with team member on non-trivial feature
- [ ] Shadow 1 on-call shift

**Month 2 — Autonomy:**
- [ ] Own and ship a small feature end-to-end
- [ ] Review 3+ PRs from peers
- [ ] Write an ADR or RFC
- [ ] Present tech talk to team (5-10 min)

**Month 3 — Full Productivity:**
- [ ] On-call rotation (with backup)
- [ ] Mentor next new hire
- [ ] Contribute to platform/tooling improvement

### Day 1 Pull Request
Every new developer merges a PR on day 1. Purpose: learn full workflow before real work.

**Good Day 1 PR tasks:** Fix typo in README, add yourself to CODEOWNERS, update stale comment, add missing test, improve error message, add type annotation.

### Onboarding Buddy System
**Buddy responsibilities:** 30 min daily check-in for first 2 weeks, answer "dumb questions", review Day 1 PR + first 3-5 PRs, introduce to key people, share tribal knowledge.

**Selection:** Same team, 6+ months tenure, good communicator, not on-call, volunteer.

---

## Step 24: API Developer Experience

### OpenAPI Best Practices
```yaml
paths:
  /users/{id}:
    get:
      responses:
        '200':
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
        '404':
          content:
            application/problem+json:  # RFC 9457
              schema:
                $ref: '#/components/schemas/ProblemDetail'
```

**problem+json (RFC 9457):** Use for all error responses. Consistent shape: `type` (URI to error docs), `title`, `status`, `detail`, `instance`.

**API versioning:** URL path (`/v1/users`) for public APIs. Major versions only. Never break v1 — deprecate with Sunset header (RFC 8594).

**Pagination:** Cursor-based preferred. Stable under inserts/deletes. Offset OK for admin UIs.

### SDK Generation
| Tool | Type | Languages | Quality |
|------|------|-----------|---------|
| OpenAPI Generator | Free, open-source | 50+ | Decent, requires cleanup |
| Speakeasy | Commercial | 10+ | Excellent, retry/pagination built-in |
| Stainless | Commercial | Limited | Premium (like OpenAI's SDK) |

```bash
openapi-generator-cli generate -i openapi.yaml -g typescript-fetch -o ./sdk/typescript
openapi-generator-cli generate -i openapi.yaml -g python -o ./sdk/python
```

### API Documentation Tools
| Tool | Type | Strength |
|------|------|----------|
| Swagger UI | Free | Ubiquitous, try-it-out. Looks dated. |
| Redoc | Free | Clean three-panel layout. |
| Stoplight Elements | Free tier | Modern UI, three-panel + try-it-out. |
| ReadMe | Paid SaaS | Analytics + developer portal. |

---

## Step 25: Developer Experience Metrics

### SPACE Framework
| Dimension | Key Metrics | Target |
|-----------|-------------|--------|
| **S**atisfaction | Dev satisfaction score (1-10), eNPS, burnout indicators | ≥ 7/10, eNPS ≥ 30 |
| **P**erformance | Change failure rate, MTTR, code review coverage | Failure < 15%, MTTR < 1h |
| **A**ctivity | Commits/week, PRs/week, deploys/service/day | Stable or improving trends |
| **C**ommunication | PR review turnaround, cross-team contributions, bus factor | Review < 4h, cross-team ≥ 10% |
| **E**fficiency | Lead time, cycle time, deploy frequency, wait time | Lead < 1 day, deploy ≥ 1/day |

### Flow Dashboard
| Metric | Target | Measure from |
|--------|--------|--------------|
| Lead time | < 1 day | Git commit → deploy |
| Cycle time | < 2 days | PR open → merge |
| Deploy frequency | ≥ 1/day | CI/CD pipeline logs |
| Change failure rate | < 15% | Deploy → incident |
| MTTR | < 1 hour | Incident → resolution |
| Flow efficiency | > 25% | Active work / total time |
| Review turnaround | < 4 hours | PR first review time |
| CI duration | < 10 minutes | Pipeline metrics |
| Cognitive load | ≥ 3.5/5 | Quarterly survey |
| Satisfaction | ≥ 7/10 | Quarterly survey |

### Friction Categories
```
1. Environment Setup    6. Deployment
2. Build & Compile      7. Documentation
3. Testing              8. Tooling
4. Code Review          9. Access & Permissions
5. CI/CD Pipeline      10. Knowledge Sharing
```

**Frequency x Impact matrix:**
```
              Low Impact          High Impact
High Frequency  Monitor             FIX FIRST
Low Frequency   Ignore              INVESTIGATE
```

### Developer Satisfaction Survey (Quarterly)
```
1. I can get my dev environment set up in reasonable time.
2. Our CI/CD pipeline is reliable and fast enough.
3. I can find documentation I need when I need it.
4. Code review is timely and constructive.
5. I spend most time on value-creating work.
6. I understand the architecture of systems I work on.
7. I have the tools I need to do my job effectively.
8. Our deployment process is smooth and low-stress.
9. I feel productive in my current work environment.
10. Technical debt does not significantly slow me down.

Open-ended:
- Biggest friction point in daily workflow?
- Tool/process improvement with most impact?
- What's working well that we should keep?
```

---

## Appendix: Decision Matrix
| Scenario | Recommendation |
|----------|---------------|
| Python project setup | uv + Ruff + pytest + mypy |
| JS/TS monorepo | pnpm + Turborepo + Vitest + Biome |
| Large polyglot monorepo | Bazel or Pants + remote cache |
| Microservices (5-20 repos) | Independent repos + shared libraries via registry |
| Monorepo CI optimization | Affected-only + remote cache + distributed execution |
| Contract management | buf (protobuf) + oasdiff (OpenAPI) + Pact |
| Release automation | release-please (simple) or Changesets (monorepo) |
| Developer onboarding | Dev Containers + justfile + Backstage catalog |
| Green software | Kepler + Scaphandre + cloud carbon tools |
| Build performance | Turborepo cache → Nx Cloud → Bazel (escalating complexity) |
