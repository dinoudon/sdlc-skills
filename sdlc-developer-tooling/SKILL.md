---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest, mypy), JS/TS (pnpm, Bun, Vitest, Biome, Playwright), Go (golangci-lint, go test -race), Rust (cargo). Cross-cutting: just, mise, direnv, Docker Compose, Dev Containers, Nix. Includes LSP/DAP patterns, AI-assisted dev, green software tooling."
version: 4.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, tooling, developer-experience, python, typescript, go, rust, docker, devex, lsp, dap, nix, bun, ai-assisted, green-software, mise, uv]
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
-- init.lua (expanded with keymaps, capabilities, on_attach)
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
  map('[d', vim.diagnostic.goto_prev, 'Prev diagnostic')
  map(']d', vim.diagnostic.goto_next, 'Next diagnostic')
end

local servers = { 'pyright', 'gopls', 'ts_ls', 'rust_analyzer', 'ruff' }
for _, server in ipairs(servers) do
  lspconfig[server].setup({ on_attach = on_attach, capabilities = capabilities })
end

-- Diagnostic display
vim.diagnostic.config({ virtual_text = true, signs = true, float = { border = 'rounded' } })
```

**Lazy.nvim plugin spec (LSP stack):**
```lua
-- lua/plugins/lsp.lua
return {
  { 'neovim/nvim-lspconfig' },
  { 'hrsh7th/nvim-cmp', dependencies = { 'hrsh7th/cmp-nvim-lsp', 'hrsh7th/cmp-buffer' } },
  { 'L3MON4D3/LuaSnip' },
  { 'williamboman/mason.nvim', config = true },
  { 'williamboman/mason-lspconfig.nvim', ensure_installed = { 'pyright', 'gopls', 'ts_ls', 'rust_analyzer', 'ruff' } },
}
```

## Monorepo Tooling (Expanded)

### Turborepo Caching
Source: https://turbo.build/repo/docs

```json
// turbo.json
{
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**", ".next/**"] },
    "test": { "dependsOn": ["build"], "outputs": [] },
    "lint": { "outputs": [] }
  },
  "remoteCache": {
    "enabled": true
  }
}
```
```bash
turbo run build test lint          # Build graph-aware pipeline
turbo run build --filter=web       # Scope to package
turbo run build --dry-run          # Preview task graph
turbo login                        # Enable remote cache (Vercel)
turbo run build --summarize        # Generate run summary JSON
```
**Remote cache:** `TURBO_TOKEN` env var + Vercel Remote Cache or self-hosted (turborepo-remote-cache, S3).

### Nx Affected
Source: https://nx.dev/

```bash
nx affected -t test                # Only test projects changed since last commit
nx affected -t build --base=main   # Compare against main branch
nx affected --graph                # Visualize affected dependency graph
nx show projects --affected        # List affected projects
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
**Nx Cloud:** distributed caching + CI distribution. `nx connect`.

### Bazel Remote Cache
Source: https://bazel.build/

```bash
# .bazelrc -- remote cache with gRPC
build --remote_cache=grpcs://cache.example.com:443
build --remote_header=x-api-key=SECRET
build --remote_timeout=60
build --remote_download_minimal     # Don't download outputs not needed locally
```
**Self-hosted options:** buildbarn, buildfarm, buildgrid, EngFlow, BuildBuddy.
**Key principle:** Content-addressable storage. Same inputs → same hash → cache hit. Reproducibility is mandatory.

### Turborepo vs Nx vs Bazel
| Feature | Turborepo | Nx | Bazel |
|---------|-----------|-----|-------|
| Best for | JS/TS monorepos | JS/TS + polyglot | Large polyglot, enterprise |
| Remote cache | Vercel (built-in) | Nx Cloud | Self-hosted / BuildBuddy |
| Affected detection | `--filter=...[HEAD^]` | `nx affected` | Manual (tag filtering) |
| Learning curve | Low | Medium | High |

## Container Development

### Docker Compose Watch Mode
Source: https://docs.docker.com/compose/how-tos/file-watch/

```yaml
# compose.yaml
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
docker compose up --build       # Rebuild when Dockerfile changes
```
**Watch actions:** `sync` (copy file), `rebuild` (rebuild image), `sync+restart` (copy + restart process).

### Testcontainers for Development
Source: https://testcontainers.com/

Throwaway Docker containers for integration tests. No shared state, no mock DBs.

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
    # Real DB, real queries, real constraints
```
```typescript
// TypeScript (Node)
import { PostgreSqlContainer } from '@testcontainers/postgresql';

const container = await new PostgreSqlContainer('postgres:16').start();
const pool = new Pool({ connectionString: container.getConnectionUri() });
// ... run tests
await container.stop();
```
```go
// Go
func TestMain(m *testing.M) {
    pg, _ := postgres.Run(ctx, "postgres:16",
        postgres.WithDatabase("testdb"),
        postgres.WithUsername("user"),
        postgres.WithPassword("pass"),
    )
    defer pg.Terminate(ctx)
    os.Exit(m.Run())
}
```
**Languages:** Java, .NET, Go, Node.js, Python, Rust. **Containers:** databases, message queues, search engines, web servers.

## Git Worktrees for Parallel Development

Work on multiple branches simultaneously without stashing or cloning multiple repos.

```bash
git worktree add ../myproject-feature-x feature/x    # New worktree for branch
git worktree add ../myproject-hotfix hotfix/critical   # Another branch
git worktree list                                      # List all worktrees
git worktree remove ../myproject-feature-x             # Remove worktree
git worktree prune                                     # Clean up stale worktrees
```
**Use cases:**
- Review PR while working on feature branch
- Test code on `main` without losing feature work
- Run CI-like checks on one branch while editing another

**With Neovim/tmux:**
```bash
# tmux session: one window per worktree
tmux new-session -s dev -n feature 'cd ../myproject-feature && nvim'
tmux new-window -t dev -n main 'cd ../myproject-main && nvim'
```
**git-wt helper:** `git-wt add <branch> --open` opens worktree + editor in one command.

## Editor/IDE Patterns

### VS Code Dev Containers (Expanded)

```jsonc
// .devcontainer/devcontainer.json
{
  "name": "fullstack",
  "dockerComposeFile": "docker-compose.devcontainer.yml",
  "service": "app",
  "workspaceFolder": "/workspace",
  "features": {
    "ghcr.io/devcontainers/features/docker-in-docker:2": {},
    "ghcr.io/devcontainers/features/github-cli:1": {},
    "ghcr.io/devcontainers/features/node:1": { "version": "20" }
  },
  "customizations": {
    "vscode": {
      "extensions": [
        "ms-python.python",
        "charliermarsh.ruff",
        "ms-vscode.live-server",
        "eamodio.gitlens",
        "ms-azuretools.vscode-docker"
      ],
      "settings": {
        "python.defaultInterpreterPath": "/usr/local/bin/python",
        "editor.formatOnSave": true
      }
    }
  },
  "postCreateCommand": "uv sync && pnpm install",
  "forwardPorts": [3000, 5432],
  "remoteEnv": { "DATABASE_URL": "postgres://postgres:password@db:5432/myapp" }
}
```
**Remote SSH:** `code --remote ssh-remote+user@host /path` — edit on remote machine with full VS Code.

### JetBrains Gateway
Source: https://www.jetbrains.com/remote-development/gateway/

Remote dev backend runs on server; thin client on local machine. Code/indexing on server, rendering locally.

```bash
# Install Gateway, connect via SSH
# Or: JetBrains Space dev environments
# Or: JetBrains Toolbox → Remote → SSH → Enter host
```
**Project config (.idea/):** commit `codeStyleConfig.xml`, `inspectionProfiles/`. Gitignore workspace files.
**Gateway with Dev Containers:** Open devcontainer.json in Gateway → container builds, IDE runs inside.

## Documentation Tooling

### MkDocs Material
Source: https://squidfunk.github.io/mkdocs-material/

```yaml
# mkdocs.yml
site_name: My Project
theme:
  name: material
  features:
    - content.code.copy
    - navigation.tabs
    - search.suggest
    - toc.follow
  palette:
    scheme: slate
    primary: indigo
plugins:
  - search
  - tags
  - git-revision-date-localized
markdown_extensions:
  - pymdownx.superfences
  - pymdownx.tabbed:
      alternate_style: true
  - admonition
  - pymdownx.details
```
```bash
pip install mkdocs-material
mkdocs serve              # Live preview
mkdocs build --strict     # Build + fail on warnings
mkdocs gh-deploy          # Deploy to GitHub Pages
```
**Best for:** Technical docs, internal wikis, API references. Python ecosystem.

### Docusaurus
Source: https://docusaurus.io/

```bash
npx create-docusaurus@latest website classic
cd website && npm start
```
```js
// docusaurus.config.js
module.exports = {
  title: 'My Project',
  url: 'https://example.com',
  presets: [
    ['classic', {
      docs: { sidebarPath: './sidebars.js', editUrl: 'https://github.com/org/repo/edit/main/website/' },
      blog: { showReadingTime: true },
      theme: { customCss: './src/css/custom.css' },
    }],
  ],
};
```
**Best for:** React-based docs with blogs, versioning, i18n. OSS project sites.

### Astro Starlight
Source: https://starlight.astro.build/

```bash
npx create-astro@latest --template starlight
```
```yaml
# astro.config.mjs
import starlight from '@astrojs/starlight';
export default {
  integrations: [starlight({
    title: 'My Docs',
    social: { github: 'https://github.com/org/repo' },
    sidebar: [
      { label: 'Start', items: ['intro', 'install'] },
      { label: 'Guides', autogenerate: { directory: 'guides' } },
    ],
  })],
};
```
**Best for:** Fast, Astro-native docs. Islands architecture — zero JS by default.

### TypeDoc
Source: https://typedoc.org/

```bash
npx typedoc --out docs src/index.ts
npx typedoc --plugin typedoc-plugin-markdown --out docs src/index.ts  # Markdown output
```
```json
// tsconfig.json
{
  "typedocOptions": {
    "entryPoints": ["src/index.ts"],
    "out": "docs",
    "excludePrivate": true,
    "excludeInternal": true
  }
}
```
**Best for:** TypeScript API reference docs. Pair with MkDocs/Docusaurus for hosting.

### Sphinx
Source: https://www.sphinx-doc.org/

```bash
pip install sphinx sphinx-rtd-theme
sphinx-quickstart docs
cd docs && make html
```
```python
# docs/conf.py
extensions = [
    'sphinx.ext.autodoc',      # Auto-generate from docstrings
    'sphinx.ext.napoleon',     # Google/NumPy style docstrings
    'sphinx.ext.viewcode',     # Source links
    'myst_parser',             # Markdown support
    'sphinx_autodoc_typehints',# Type hint rendering
]
html_theme = 'sphinx_rtd_theme'
```
**Best for:** Python projects with autodoc. Enterprise/traditional docs.

### Docs Tooling Decision Matrix
| Tool | Language | Output | Strength |
|------|----------|--------|----------|
| MkDocs Material | Python | Static site | Beautiful, fast setup |
| Docusaurus | React | Static site | Versioning, i18n, blog |
| Starlight | Astro | Static site | Zero JS, fast builds |
| TypeDoc | TypeScript | HTML/MD | API reference from TS |
| Sphinx | Python | HTML/PDF | Autodoc, enterprise |

## Observability for Developers

### Local Jaeger (Distributed Tracing)
Source: https://www.jaegertracing.io/

```yaml
# docker-compose.observability.yml
services:
  jaeger:
    image: jaegertracing/all-in-one:1.62
    ports:
      - "16686:16686"  # UI
      - "4317:4317"    # OTLP gRPC
      - "4318:4318"    # OTLP HTTP
    environment:
      COLLECTOR_OTLP_ENABLED: "true"
```
```python
# Python — instrument with OpenTelemetry
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace.export import BatchSpanProcessor

provider = TracerProvider()
provider.add_span_processor(BatchSpanProcessor(OTLPSpanExporter(endpoint="http://localhost:4317")))
trace.set_tracer_provider(provider)
tracer = trace.get_tracer("my-service")

with tracer.start_as_current_span("handle_request"):
    with tracer.start_as_current_span("db_query"):
        # Your code — auto-correlated in Jaeger UI
        pass
```
**View traces:** Open http://localhost:16686 → select service → find traces.

### Grafana Tempo (Traces) + Loki (Logs)
Source: https://grafana.com/docs/tempo/

```yaml
# docker-compose.observability.yml
services:
  tempo:
    image: grafana/tempo:latest
    command: ["-config.file=/etc/tempo.yaml"]
    volumes: ["./tempo.yaml:/etc/tempo.yaml"]
    ports: ["3200:3200", "4317:4317"]

  loki:
    image: grafana/loki:3.0.0
    ports: ["3100:3100"]

  grafana:
    image:grafana/grafana:latest
    ports: ["3000:3000"]
    volumes: ["./grafana-datasources.yaml:/etc/grafana/provisioning/datasources/datasources.yaml"]
```
```yaml
# grafana-datasources.yaml
apiVersion: 1
datasources:
  - name: Tempo
    type: tempo
    url: http://tempo:3200
  - name: Loki
    type: loki
    url: http://loki:3100
```
**Trace-to-logs:** Link traces to logs via TraceID. Loki queries `{trace_id="<TRACE_ID>"}`.

### Structured Logging in Development

```python
# Python — structlog
import structlog
structlog.configure(
    processors=[
        structlog.contextvars.merge_contextvars,
        structlog.processors.add_log_level,
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.dev.ConsoleRenderer() if __debug__ else structlog.processors.JSONRenderer(),
    ],
)
log = structlog.get_logger()
log.info("user_login", user_id=123, method="oauth", latency_ms=42)
# Dev output: [info] user_login  user_id=123 method=oauth latency_ms=42
# Prod output: {"event":"user_login","user_id":123,"method":"oauth","latency_ms":42,"level":"info","timestamp":"2024-01-15T10:30:00Z"}
```
```typescript
// TypeScript — pino
import pino from 'pino';
const logger = pino({ transport: { target: 'pino-pretty' } }); // Dev: pretty, Prod: JSON
logger.info({ userId: 123, action: 'login' }, 'User logged in');
```
```go
// Go — slog (stdlib)
import "log/slog"
logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
logger.Info("user_login", "user_id", 123, "method", "oauth")
```
**Key:** Use JSON in production (machine-parseable), pretty-print in dev (human-readable). Single log library, two renderers via env var.

### Dev Observability Stack (All-in-One)
```yaml
# docker-compose.observability.yml — full local stack
services:
  jaeger:
    image: jaegertracing/all-in-one:1.62
    ports: ["16686:16686", "4317:4317"]
  tempo:
    image: grafana/tempo:latest
    ports: ["3200:3200"]
  loki:
    image: grafana/loki:3.0.0
    ports: ["3100:3100"]
  grafana:
    image: grafana/grafana:latest
    ports: ["3000:3000"]
    environment:
      GF_AUTH_ANONYMOUS_ENABLED: "true"
      GF_AUTH_ANONYMOUS_ORG_ROLE: Admin
```
```bash
docker compose -f docker-compose.observability.yml up -d
# Traces: http://localhost:16686 (Jaeger) or http://localhost:3000 (Grafana + Tempo)
# Logs: http://localhost:3000 (Grafana + Loki)
```

### OTel Auto-Instrumentation
```bash
# Python
pip install opentelemetry-distro opentelemetry-exporter-otlp
opentelemetry-bootstrap -a install  # Auto-detect + install instrumentors
opentelemetry-instrument --service myapp --exporter_otlp_endpoint http://localhost:4317 python app.py

# Node.js
npx @opentelemetry/auto-instrumentations-node
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317 node --require ./tracing.js app.js
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

### AI Coding Assistants Comparison

| Feature | GitHub Copilot | Cursor | Sourcegraph Cody | Codeium / Windsurf |
|---------|---------------|--------|------------------|---------------------|
| **Type** | Extension | Standalone editor (VS Code fork) | Extension | Extension + standalone editor |
| **Inline completions** | Yes (multi-line) | Yes (multi-line, tab-to-accept) | Yes | Yes |
| **Chat** | Yes (`@workspace`, `@terminal`) | Yes (codebase-aware, multi-file edits) | Yes (full codebase index) | Yes (Cascade agentic flow) |
| **Codebase indexing** | `@workspace` in chat | Automatic, `.cursorignore` | Deep indexing, supports private repos | Workspace-aware context |
| **Agentic mode** | Copilot Workspace (preview) | Composer mode (multi-file edits) | Limited | Cascade (Windsurf, multi-step agent) |
| **IDE support** | VS Code, JetBrains, Neovim | Own editor only | VS Code, JetBrains | VS Code, JetBrains, own editor |
| **CLI assistant** | Copilot CLI (`??` prefix) | Terminal chat | No | No |
| **Self-hosted** | No (GitHub Enterprise only) | No | Yes (Enterprise) | No |
| **Pricing** | $10/mo individual, $19/mo business | $20/mo Pro, $40/mo Business | Free tier, $9/mo Pro, Enterprise custom | Free tier, $10/mo Pro, Enterprise custom |
| **Model** | GPT-4o, Claude 3.5 Sonnet | GPT-4o, Claude 3.5 Sonnet, own models | Claude 3.5 Sonnet, Mixtral | GPT-4o, Claude 3.5 Sonnet |
| **Strengths** | Broadest IDE support, GitHub integration, CLI tool | Best multi-file editing, fastest iteration, `.cursorrules` | Best codebase search, enterprise self-host | Free tier generous, agentic Cascade flow |
| **Weaknesses** | Less context-aware than Cursor for large refactors | Lock-in to own editor, no self-hosted | Weaker inline completions, no CLI | Newer ecosystem, less mature |

**Selection guide:**
- Already on VS Code + GitHub → **Copilot** (lowest friction)
- Want best AI-native editor experience → **Cursor** (Composer, multi-file edits)
- Enterprise with private repos, need self-hosted → **Cody**
- Budget-constrained or want agentic flows → **Windsurf** (free tier, Cascade)

### Prompt Engineering for Developers

Effective prompting turns AI from autocomplete into a force multiplier. Apply these patterns in chat, inline comments, `.cursorrules`, and `copilot-instructions.md`.

#### 1. Role Assignment
Set context before asking. AI performs better with a role.

```
You are a senior Python developer specializing in FastAPI and async programming.
Write code following PEP 8, using type hints, and preferring composition over inheritance.
```

#### 2. Few-Shot Examples
Show input/output pairs to anchor the pattern.

```
Convert these function signatures to use Result types:

Input:  def divide(a: int, b: int) -> float:
Output: def divide(a: int, b: int) -> Result[float, str]:

Input:  def parse_json(raw: str) -> dict:
Output: def parse_json(raw: str) -> Result[dict, JSONDecodeError]:

Now convert:
def fetch_user(user_id: int) -> User:
```

#### 3. Chain-of-Thought
Force step-by-step reasoning for complex tasks.

```
I need to refactor this monolith into microservices. Think step by step:
1. Identify bounded contexts in the codebase
2. Map dependencies between contexts
3. Propose service boundaries
4. List shared vs per-service data stores
5. Draft migration plan with rollback strategy
```

#### 4. Output Format Control
Specify exact structure to avoid free-form rambling.

```
Review this function. Output format:
- BUGS: [list of bugs with line numbers]
- PERFORMANCE: [optimization suggestions]
- STYLE: [naming/formatting issues]
- IMPROVED: [rewritten function]
```

#### 5. Negative Examples
Tell AI what NOT to do — reduces bad suggestions.

```
Write a database query function. Do NOT:
- Use raw SQL strings (use SQLAlchemy ORM)
- Use `any` type annotations
- Add comments that restate the code
- Use default mutable arguments
- Catch broad exceptions (catch specific ones)
```

#### 6. Context Window Management
- Paste only relevant code, not entire files
- Use `@file` / `@workspace` references when available
- For large refactors: describe the change, paste the target file, not the whole repo
- Summarize conversation context when switching topics mid-chat

### AI Pair Programming Best Practices

#### Boilerplate, Not Architecture
Use AI for repetitive code (CRUD endpoints, data models, test fixtures, config files). Do NOT let AI design system architecture, choose patterns, or make security decisions. Architecture requires understanding business context, trade-offs, and long-term maintenance — AI lacks all three.

```python
# GOOD: AI generates boilerplate CRUD
# Prompt: "Generate a FastAPI CRUD router for the User model with SQLAlchemy"

# BAD: AI designs your auth system
# "Design an authentication system for my app" — too many business decisions
```

#### Review All AI Code
Treat every AI-generated code block like a PR from an unknown contributor:
- Does it handle edge cases? (null, empty, overflow)
- Are error messages user-safe? (no stack traces, no secrets leaked)
- Are types correct? (not just no errors — semantically correct)
- Does it follow your team's patterns? (not generic "best practices")
- Could it be a hallucinated API? (verify imports, function signatures, library existence)

```bash
# Always verify AI suggestions compile and pass tests
uv run ruff check --fix . && uv run mypy src/ && uv run pytest
```

#### Break Tasks Small
Large prompts produce large, often wrong outputs. Break work into atomic units:

```
# BAD: "Build a full REST API with auth, pagination, and rate limiting"

# GOOD (incremental):
# 1. "Create User model with SQLAlchemy 2.0 mapped_column syntax"
# 2. "Add Pydantic schemas for User create/read/update"
# 3. "Write FastAPI router with CRUD endpoints for User"
# 4. "Add pagination with cursor-based approach"
# 5. "Add JWT auth dependency"
# 6. "Add rate limiting with slowapi"
```

Each step is reviewable, testable, and correctable in isolation.

### AI Anti-Patterns

#### Blindly Accepting
The most dangerous pattern: accepting AI suggestions without reading them. Signs:
- Copilot suggestions accepted with Tab without scanning
- Chat responses pasted directly into codebase
- "It compiles, ship it" mentality

**Fix:** Enable "preview before accept" in your editor. Force yourself to read every line. If you can't explain what the code does, don't merge it.

#### Compensating for Lack of Understanding
Using AI as a crutch instead of learning fundamentals. Symptoms:
- Can't write a basic function without AI assistance
- Don't understand what the AI-generated code does
- Can't debug AI-generated code when it breaks
- Repeatedly asking AI the same category of question

**Fix:** After AI generates code, study it. Understand why it works. Next time, try writing it yourself first. AI should accelerate competent developers, not replace learning.

#### Context Pollution
Dumping entire files or unrelated code into prompts. Results in:
- AI picks up wrong patterns from unrelated code
- Suggestions that "average" across conflicting conventions
- Wasted tokens on irrelevant context

**Fix:** Paste only the function/section being worked on. Use `@file` references for context, not raw pastes.

#### Prompt-Driven Development (Without Tests)
Iterating on prompts until code "looks right" without running it. Common loop:
```
Ask AI → paste code → ask again → paste again → never run tests
```
**Fix:** After every AI interaction, run the relevant test suite. Code that hasn't been executed doesn't exist.

### LLM-Powered Test Generation Patterns

#### Pattern 1: Scaffold-and-Refine
AI generates test structure, human fills business logic.

```python
# Prompt: "Generate pytest tests for calculate_discount. Include:
#   - Happy path with percentage and fixed discounts
#   - Edge cases: zero price, negative discount, 100% discount
#   - Error cases: invalid discount type
# Use parametrize for similar cases."

# AI output gives you structure. Human refines:
#   - Exact expected values (AI guesses, you know)
#   - Business-specific edge cases
#   - Mocking strategy for external deps
```

#### Pattern 2: Property-Based Test Generation
AI suggests Hypothesis strategies you might not think of.

```python
# Prompt: "Write Hypothesis property-based tests for this sorting function.
# Properties: output is sorted, same length as input, same elements,
# idempotent when applied twice."

from hypothesis import given, strategies as st

@given(st.lists(st.integers()))
def test_sort_properties(lst):
    result = my_sort(lst)
    assert result == sorted(lst)
    assert len(result) == len(lst)
    assert set(result) == set(lst)
    assert my_sort(result) == result  # idempotent
```

#### Pattern 3: Test Migration
AI converts tests between frameworks.

```
# Prompt: "Convert these unittest tests to pytest. Use fixtures instead of
# setUp/tearDown. Use parametrize instead of subTest. Remove self.assertEqual
# in favor of assert."
```

#### Pattern 4: Negative Test Generation
AI generates adversarial inputs you'd miss.

```
# Prompt: "Generate negative tests for parse_config(path: str) -> Config.
# Think of inputs that could crash, hang, or produce wrong results:
# empty file, binary file, missing keys, extra keys, wrong types,
# circular references, 100MB file, unicode keys, symlink loops."
```

#### Pattern 5: Coverage Gap Analysis
Use AI to suggest tests for uncovered code paths.

```bash
# First: identify uncovered lines
uv run pytest --cov=src --cov-report=json
# Then: feed uncovered functions to AI
# Prompt: "These functions have 0% test coverage. Generate tests:
#   - src/auth.py: validate_token (lines 45-72)
#   - src/cache.py: evict_lru (lines 12-38)
# Cover all branches shown in the coverage report."
```

#### Test Generation Rules
- AI-generated tests catch ~60% of real bugs. Human-written assertions catch the rest.
- Always verify expected values — AI guesses outputs, you know them.
- Never trust AI to generate security-critical test cases (auth, crypto, permissions).
- Run `pytest --tb=short` immediately after generation — fix import errors and API mismatches.
- Use AI to generate, not to verify. Verification is your job.

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