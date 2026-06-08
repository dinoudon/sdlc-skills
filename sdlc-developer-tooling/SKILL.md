---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest, mypy), JS/TS (pnpm, Bun, Vitest, Biome, Playwright), Go (golangci-lint, go test -race), Rust (cargo). Cross-cutting: just, mise, direnv, Docker Compose, Dev Containers, Nix. Includes LSP/DAP patterns, AI-assisted dev, green software tooling."
version: 3.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, tooling, developer-experience, python, typescript, go, rust, docker, devex, lsp, dap, nix, bun, ai-assisted, green-software, mise, uv]
    related_skills: [sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-testing-qa, sdlc-security-compliance]
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

Rust-based Python package/project manager by Astral (creators of Ruff). Drop-in replacement for pip/pip-tools/pipx/poetry/pyenv. 10-100x faster than pip.

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv init myproject && cd myproject
uv add fastapi uvicorn pytest ruff mypy pre-commit
uv sync
uv run pytest
```

**Key features:**
- `uv pip install`, `uv venv` — fast package operations
- `uv run` — execute commands in project env without activation
- pyproject.toml-based project management (`uv init`, `uv add`, `uv remove`)

### uv: Lockfile & Dependency Resolution
```bash
# Compile lockfile from pyproject.toml (replaces pip-compile)
uv pip compile pyproject.toml -o requirements.lock
# Compile with extras
uv pip compile pyproject.toml --extra dev -o requirements-dev.lock
# Install from lockfile
uv pip sync requirements.lock
```

### uv: Python Version Management
```bash
uv python install 3.12          # Install specific Python version
uv python install 3.11 3.12     # Install multiple versions
uv python list                   # List available versions
uv python pin 3.12              # Pin project to version (writes .python-version)
uv python find 3.12             # Find installed path
```
Replaces pyenv. Downloads pre-built CPython builds from python.org.

### uv: Dependency Visualization
```bash
uv tree                          # Show dependency tree
uv tree --depth 1                # Direct deps only
uv tree --prune fastapi          # Exclude subtree
uv tree --invert                 # Reverse: what depends on X
uv tree --package mypkg          # Tree for specific package
```

### uv: Script Runner
```bash
# Inline script dependencies (PEP 723)
# /// script
# requires-python = ">=3.12"
# dependencies = ["requests", "rich"]
# ///
uv run script.py                 # Auto-installs deps, runs script
uv run --with httpie http GET example.com  # Ad-hoc dependency
```

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
uv tree                          # Verify dependency graph
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

### pnpm: Catalog Protocol (Monorepo Version Management)
Catalog protocol centralizes shared dependency versions across monorepo packages.

```yaml
# pnpm-workspace.yaml
packages:
  - "packages/*"

catalog:
  react: ^18.3.1
  typescript: ^5.5.0
  vitest: ^2.0.0
  zod: ^3.23.0

catalogs:
  react17:
    react: ^17.0.2
    react-dom: ^17.0.2
```

```json
// packages/app/package.json
{
  "dependencies": {
    "react": "catalog:",
    "zod": "catalog:"
  },
  "devDependencies": {
    "vitest": "catalog:",
    "typescript": "catalog:"
  }
}
```

Use named catalogs for migration: `"react": "catalog:react17"`. Single source of truth for versions — update once in `pnpm-workspace.yaml`, propagates to all packages.

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

Polyglot version manager + task runner. Replaces asdf, nvm, pyenv, rbenv. Manages runtimes and project tasks in one tool.

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
ci = { depends = ["lint", "test"] }

[env]
DATABASE_URL = "postgres://localhost/myapp_dev"
_.path = ["./node_modules/.bin"]
```

```bash
mise install              # Install all versions
mise use python=3.12      # Switch version
mise tasks                # List tasks
mise run lint             # Run task
mise run deploy --env prod
```

#### mise: Hooks
Run commands on directory enter, tool install, or env change:

```toml
# .mise.toml
[hooks]
enter = "echo 'Welcome to myproject'"
leave = "echo 'Leaving myproject'"

[hooks.postinstall]
python = "pip install -e .[dev]"
node = "pnpm install"
```

#### mise: Trust System
Prevent malicious `.mise.toml` in untrusted repos:

```bash
mise trust                # Trust current dir .mise.toml
mise trust --all          # Trust all configs
mise trust --untrust      # Revoke trust
mise ls-trusted           # List trusted configs
```

Untrusted configs show warning and don't auto-activate. Trust is per-file, tracked in `~/.config/mise/trusted-configs`.

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

## Step 9: Bun Runtime
Source: https://bun.sh/

All-in-one JS/TS runtime, bundler, test runner, package manager. Built on JavaScriptCore (Safari engine), not V8. Significantly faster cold start than Node.

```bash
curl -fsSL https://bun.sh/install | bash
bun init                     # Scaffold project
bun install                  # Install deps (faster than npm/pnpm)
bun add express zod          # Add dependency
bun run index.ts             # Run TS directly — no compile step
bun test                     # Built-in test runner (Jest-compatible API)
bun build ./src/index.ts --outdir ./dist --target bun  # Bundle
```

### Bun: Test Runner
```typescript
import { describe, it, expect } from "bun:test";

describe("math", () => {
  it("adds", () => {
    expect(1 + 1).toBe(2);
  });
});
```
```bash
bun test                     # Run all *.test.ts
bun test --watch             # Watch mode
bun test --coverage          # Coverage
```

### Bun: Bundler
```bash
bun build ./src/index.ts --outdir ./dist --minify --sourcemap
bun build ./src/index.ts --outdir ./dist --target node  # Node-compatible
```

### When to Use Bun vs Node
| Scenario | Choice |
|----------|--------|
| New project, TS-first | Bun |
| Existing Node ecosystem deps | Node |
| Script execution / automation | Bun |
| Edge/serverless cold start | Bun |
| Needs native Node addons | Node |

## Step 10: AI-Assisted Development Tools

Patterns for integrating AI coding assistants into SDLC workflows.

### GitHub Copilot
- Inline completions + chat in VS Code, JetBrains, Neovim
- `@workspace` chat context: indexes full repo for Q&A
- Copilot CLI: shell command suggestions (`??` prefix)
- Use `.github/copilot-instructions.md` for project-specific guidance

```markdown
# .github/copilot-instructions.md
- Use Ruff for linting, never flake8
- Prefer uv over pip
- All functions need docstrings
- Use pytest, not unittest
```

### Sourcegraph Cody
- Context-aware code chat with full codebase indexing
- Works with private repos, self-hosted option

### Cursor Patterns
- AI-native editor (VS Code fork) with codebase-aware chat
- `.cursorrules` file for project conventions:

```
# .cursorrules
Use TypeScript strict mode. Prefer Vitest over Jest.
Use pnpm catalog protocol for shared deps in monorepos.
Format with Biome. Use uv for Python deps.
```

### General AI-Assisted Dev Patterns
1. **AI in PR reviews**: Copilot/Cody review bots catch style + logic issues
2. **Test generation**: AI generates test skeletons, human refines assertions
3. **Refactoring assistance**: Large-scale renames, API migrations
4. **Documentation**: AI drafts, human verifies accuracy
5. **Onboarding**: New devs use AI to explore unfamiliar codebases

**Guardrails:**
- Never paste secrets/credentials into AI tools
- Review all AI-generated code — treat like junior dev PRs
- Use `.cursorrules` / copilot-instructions to enforce team conventions
- Disable AI suggestions for sensitive/proprietary code paths

## Step 11: Green Software Tooling

Integrate carbon awareness into development workflows.

### Carbon-Aware CI/CD
Run compute-heavy jobs when grid carbon intensity is lowest:

```yaml
# .github/workflows/carbon-aware.yml
name: Carbon-Aware Tests
on:
  schedule:
    - cron: "0 2 * * *"  # Fallback: run at 2am (typically low carbon)

# Use green-coding-hub/eco-ci-energy-estimation for measurement
```

### Green Software Tools
| Tool | Purpose |
|------|---------|
| [Green Metrics Tool](https://www.green-coding.io/projects/green-metrics-tool/) | Measure energy/carbon of software |
| [Eco-CI](https://github.com/green-coding-hub/eco-ci-energy-estimation) | CI pipeline energy estimation |
| [Boa Vista](https://github.com/green-coding-hub/boa-vista) | Website carbon footprint |
| [cloud-carbon-footprint](https://www.cloudcarbonfootprint.org/) | Cloud infrastructure emissions |
| [scaphandre](https://github.com/hubblo-org/scaphandre) | Host-level energy monitoring |
| [Greenframe](https://greenframe.io/) | Frontend carbon footprint analysis |

### Carbon-Aware Development Practices
1. **Measure first**: Profile energy before optimizing — don't guess
2. **Cache aggressively**: Reduce redundant compute (CI caches, CDN, memoization)
3. **Choose efficient runtimes**: Bun/Rust/Go over interpreted languages for hot paths
4. **Reduce bundle size**: Smaller payloads = less network energy
5. **Scheduled batch jobs**: Run at low-carbon grid hours (use [electricitymaps.org](https://electricitymaps.org/) API)
6. **Green hosting**: Prefer providers with renewable energy commitments

```python
# Example: carbon-aware job scheduling
import requests

def get_carbon_intensity(zone: str = "DE") -> float:
    """Get current grid carbon intensity (gCO2/kWh)."""
    resp = requests.get(f"https://api.electricitymaps.com/v1/carbon-intensity/latest?zone={zone}")
    return resp.json()["carbonIntensity"]

def is_low_carbon(threshold: int = 200) -> bool:
    return get_carbon_intensity() < threshold
```

### Green Software Principles (SCI Standard)
Source: https://sci-guide.greensoftware.foundation/

Software Carbon Intensity (SCI) = `(E * I) + M per R`
- **E**: Energy consumed by software
- **I**: Location-based carbon intensity of grid
- **M**: Embodied carbon of hardware
- **R**: Functional unit (request, user, transaction)

Target: reduce SCI score over time. Measure, optimize, repeat.