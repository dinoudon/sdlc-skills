---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest, mypy), JS/TS (pnpm, Bun, Vitest, Biome, Playwright), Go (golangci-lint, go test -race), Rust (cargo). Cross-cutting: just, mise, direnv, Docker Compose, Dev Containers, Nix. Includes LSP/DAP patterns, AI-assisted dev, green software tooling, CI/CD advanced patterns, build caching, artifact management, monorepo tooling (Nx, Turborepo, Bazel, Pants), polyrepo patterns, repository structure, conventional commits, semver automation."
version: 4.8.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, tooling, developer-experience, python, typescript, go, rust, docker, devex, lsp, dap, nix, bun, ai-assisted, green-software, mise, uv, ci-cd, caching, artifacts, sbom, monorepo, polyrepo, conventional-commits, semver]
    related_skills: [sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-testing-qa, sdlc-adversarial-review]
---

# Developer Tooling & DX

Modern tooling for Python, JS/TS, Go, Rust. Cross-cutting: task runners, runtime managers, local dev, Dev Containers. Includes LSP/DAP patterns, AI-assisted dev tools, and green software practices.

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
Source: https://docs.astral.sh/uv/

Rust-based Python package/project manager. 10-100x faster than pip. Drop-in replacement for pip/pip-tools/pipx/poetry/pyenv.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv init myproject && cd myproject
uv add fastapi uvicorn pytest ruff mypy pre-commit
uv sync
uv run pytest
```

### uv: Lockfile, Python Version, Deps
```bash
# Lockfile
uv pip compile pyproject.toml -o requirements.lock
uv pip compile pyproject.toml --extra dev -o requirements-dev.lock
uv pip sync requirements.lock

# Python version management (replaces pyenv)
uv python install 3.12
uv python pin 3.12              # writes .python-version

# Dependency tree
uv tree                          # full tree
uv tree --depth 1                # direct deps only
uv tree --invert                 # what depends on X

# Inline script deps (PEP 723)
# /// script
# requires-python = ">=3.12"
# dependencies = ["requests", "rich"]
# ///
uv run script.py
uv run --with httpie http GET example.com  # ad-hoc dependency
```

### Linter + Formatter: Ruff
Source: https://docs.astral.sh/ruff/

Replaces flake8, isort, black, pylint, pyupgrade. 800+ rules.

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
uv run ruff check --fix .    # Lint + auto-fix
uv run ruff format .         # Format
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
uv run pytest --tb=short -x          # Fast fail
uv run pytest --cov=src --cov-report=html  # Coverage
uv run pytest -n auto                 # Parallel (pytest-xdist)
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
uv run ruff check --fix .
uv run pytest
uv run mypy src/
uv tree
```

## Step 2: JavaScript/TypeScript Tooling

### Package Manager: pnpm
Source: https://pnpm.io/

Fast, disk-efficient. Content-addressable store. Strict: no phantom deps.

```bash
pnpm create vite myapp --template react-ts
cd myapp
pnpm add -D vitest @vitest/coverage-v8 biome playwright
pnpm dlx biome init
pnpm dlx playwright install
```

### pnpm: Catalog Protocol (Monorepo)
```yaml
# pnpm-workspace.yaml
packages:
  - "packages/*"

catalog:
  react: ^18.3.1
  typescript: ^5.5.0
  vitest: ^2.0.0
```
```json
// packages/app/package.json
{ "dependencies": { "react": "catalog:", "zod": "catalog:" } }
```
Named catalogs for migration: `"react": "catalog:react17"`. Single source of truth.

### Linter + Formatter: Biome
Source: https://biomejs.dev/

Replaces ESLint + Prettier. 30-50x faster.

```bash
pnpm dlx biome init
pnpm dlx biome check --write .  # Lint + format + import sort
```
```json
// biome.json
{
  "organizeImports": { "enabled": true },
  "linter": { "enabled": true, "rules": { "recommended": true } }
}
```

### Testing: Vitest
Source: https://vitest.dev/

Vite-native. Drop-in Jest replacement. Native ESM/TS/JSX without config.

```typescript
import { describe, it, expect } from 'vitest';

describe('calculateDiscount', () => {
  it('applies percentage discount', () => {
    expect(calculateDiscount(100, 0.1)).toBe(90.0);
  });
});
```
```bash
pnpm vitest          # Watch mode
pnpm vitest run      # Single run
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
{
  "compilerOptions": {
    "strict": true,
    "module": "NodeNext",
    "moduleResolution": "NodeNext"
  }
}
```

## Step 3: Go Tooling

### Testing
```bash
go test ./...                    # Run all tests
go test -cover ./...             # Coverage
go test -race ./...              # Race detector (ALWAYS in CI)
go test -bench=. ./...           # Benchmarks
go test -run TestFoo -v ./...    # Verbose, specific test
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
    - errcheck
    - govet
    - staticcheck
    - gosec
    - revive
    - gocritic
    - bodyclose
    - nilerr
    - exhaustive
```
```bash
golangci-lint run ./...
golangci-lint run --fix ./...
```

### Code Generation & CI
```bash
go generate ./...    # Run all //go:generate directives
```

**Common generators:** mockgen, stringer, ent, sqlc

**Recommended Go CI:**
```yaml
steps:
  - run: go test -race -cover ./...
  - uses: golangci/golangci-lint-action@v6
  - run: govulncheck ./...
```

## Step 4: Rust Tooling

```bash
cargo build
cargo test
cargo clippy          # Lint
cargo fmt             # Format
cargo audit           # Security
cargo deny check      # License + security + bans
```

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
    ruff check --fix .
    ruff format .

deploy env="staging":
    ./deploy.sh {{env}}
```

### Version Manager: mise
Source: https://mise.jdx.dev/

Polyglot version manager + task runner. Replaces asdf, nvm, pyenv, rbenv.

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
_