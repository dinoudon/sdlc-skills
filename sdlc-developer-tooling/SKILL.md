---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest, mypy), JS/TS (pnpm, Vitest, Biome, Playwright), Go (golangci-lint, go test -race), Rust (cargo). Cross-cutting: just, mise, direnv, Docker Compose, Dev Containers, Nix. Includes LSP/DAP patterns and DevEx."
version: 2.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, tooling, developer-experience, python, typescript, go, rust, docker, devex, lsp, dap, nix]
    related_skills: [sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-testing-qa]
---

# Developer Tooling & DX

Modern tooling for Python, JS/TS, Go, Rust. Cross-cutting: task runners, runtime managers, local dev, Dev Containers. Includes LSP/DAP patterns and DevEx.

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

Rust-based Python package/project manager by Astral (creators of Ruff). Drop-in replacement for pip/pip-tools/pipx/poetry/pyenv. 10-100x faster than pip.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv init myproject && cd myproject
uv add fastapi uvicorn pytest ruff mypy pre-commit
uv sync
uv run pytest
```

**Key features:**
- `uv pip install`, `uv venv`, `uv pip compile` (lockfile generation)
- `uv run` executes commands in project env without activation
- `uv python install 3.12` manages Python versions (replaces pyenv)
- pyproject.toml-based project management (`uv init`, `uv add`, `uv remove`)

### Linter + Formatter: Ruff
Source: https://docs.astral.sh/ruff/

Rust-based linter + formatter. Replaces flake8, isort, black, pylint, pyupgrade, and 800+ rules.

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
Source: https://mypy-lang.org/

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
```

## Step 2: JavaScript/TypeScript Tooling

### Package Manager: pnpm
Source: https://pnpm.io/

Fast, disk-efficient package manager. Content-addressable store. Strict: no phantom deps by default.

```bash
pnpm create vite myapp --template react-ts
cd myapp
pnpm add -D vitest @vitest/coverage-v8 biome playwright
pnpm dlx biome init
pnpm dlx playwright install
```

**Key features:**
- `pnpm install`, `pnpm add`, `pnpm dlx` (like npx)
- Workspace support: `pnpm-workspace.yaml`
- `pnpm patch`, `pnpm overrides` for dep management

### Linter + Formatter: Biome
Source: https://biomejs.dev/

Rust-based linter + formatter. Replaces ESLint + Prettier. 30-50x faster.

```bash
pnpm dlx biome init
pnpm dlx biome check --write .  # Lint + format + import sort
```

```json
// biome.json
{
  "organizeImports": { "enabled": true },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  }
}
```

### Testing: Vitest
Source: https://vitest.dev/

Vite-native test framework. Drop-in Jest replacement. Native ESM, TypeScript, JSX without config.

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
Source: https://playwright.dev/

```bash
pnpm dlx playwright install
pnpm dlx playwright test
pnpm dlx playwright codegen http://localhost:3000  # Record tests
```

### Type Checking
```bash
pnpm tsc --noEmit    # Type-check only (use bundler for emit)
```

**tsconfig.json:**
```json
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

Meta-linter aggregating 100+ Go linters.

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

linters-settings:
  revive:
    rules:
      - name: unexported-return
        disabled: true
```

```bash
golangci-lint run ./...
golangci-lint run --fix ./...
```

### Code Generation
```bash
go generate ./...    # Run all //go:generate directives
```

**Common generators:** mockgen, stringer, ent, sqlc

### Recommended Go CI
```yaml
# .github/workflows/go.yml
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
# justfile
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

Polyglot version manager. Replaces asdf, nvm, pyenv, rbenv.

```toml
# .mise.toml
[tools]
python = "3.12"
node = "20"
go = "1.22"
```

```bash
mise install     # Install all versions
mise use python=3.12  # Switch version
```

### Auto-Env: direnv
Source: https://direnv.net/

```bash
# .envrc
export DATABASE_URL=postgres://localhost/myapp_dev
export REDIS_URL=redis://localhost:6379
layout python3  # Auto-create venv
```

```bash
direnv allow
```

### Docker Compose
```yaml
# docker-compose.yml
services:
  app:
    build: .
    ports: ["3000:3000"]
    volumes: ["./src:/app/src"]
    environment:
      DATABASE_URL: postgres://postgres:password@db:5432/myapp
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
      "extensions": [
        "ms-python.python",
        "ms-python.vscode-pylance",
        "charliermarsh.ruff"
      ]
    }
  }
}
```

Works in VS Code, GitHub Codespaces, JetBrains.

## Step 7: Nix + devenv

Source: https://nixos.org/, https://devenv.sh/

```nix
# devenv.nix
{ pkgs, ... }:
{
  packages = [ pkgs.git pkgs.gh ];

  languages.python = {
    enable = true;
    version = "3.12";
    venv.enable = true;
    venv.requirements = ./requirements.txt;
  };

  languages.javascript = {
    enable = true;
    package = pkgs.nodejs_20;
  };

  services.postgres = {
    enable = true;
    initialDatabases = [{ name = "myapp"; }];
  };
}
```

```bash
devenv shell   # Drop into dev shell
devenv up      # Start services
```

## Step 8: LSP & DAP Patterns

### LSP (Language Server Protocol)
Source: https://microsoft.github.io/language-server-protocol/

Editor-agnostic language intelligence. Autocomplete, go-to-definition, hover, refactoring, diagnostics.

**Key language servers:**
| Language | Server | Install |
|----------|--------|---------|
| Python | pyright | `npm i -g pyright` |
| Go | gopls | `go install golang.org/x/tools/gopls@latest` |
| TypeScript | typescript-language-server | `npm i -g typescript-language-server` |
| Rust | rust-analyzer | `rustup component add rust-analyzer` |

### DAP (Debug Adapter Protocol)
Source: https://microsoft.github.io/debug-adapter-protocol/

Editor-agnostic debugging. Same pattern as LSP.

**Key adapters:**
| Language | Adapter | Install |
|----------|---------|---------|
| Python | debugpy | `pip install debugpy` |
| Go | delve | `go install github.com/go-delve/delve/cmd/dlv@latest` |
| TypeScript | js-debug | Built into VS Code |

### Neovim LSP Config
```lua
-- init.lua
require('lspconfig').pyright.setup{}
require('lspconfig').gopls.setup{}
require('lspconfig').ts_ls.setup{}
require('lspconfig').rust_analyzer.setup{}
```

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
