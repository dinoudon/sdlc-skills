---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest, mypy), JS/TS (pnpm, Bun, Vitest, Biome, Playwright), Go (golangci-lint, go test -race), Rust (cargo). Cross-cutting: just, mise, direnv, Docker Compose, Dev Containers, Nix. Includes LSP/DAP patterns, AI-assisted dev, green software tooling, CI/CD advanced patterns, build caching, artifact management."
version: 4.6.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, tooling, developer-experience, python, typescript, go, rust, docker, devex, lsp, dap, nix, bun, ai-assisted, green-software, mise, uv, ci-cd, caching, artifacts, sbom]
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
## Step 12: Platform Engineering & Internal Developer Platforms

### Platform as Product
Shift from ticket-based ops to self-service. Platform teams build products developers consume.

**Mindset principles:**
- Treat developers as customers, not subordinates
- Measure adoption, not compliance
- Product roadmap driven by developer surveys + usage data
- Dedicated PM for platform team (yes, really)
- Platform team owns SLO for internal services

### Platform Maturity Model

```
CRAWL:
  - Centralized docs (wiki/confluence)
  - Shared CI templates (GitHub Actions reusable workflows)
  - Service catalog spreadsheet
  - Manual provisioning with runbooks
  - Onboarding time: 2-4 weeks

WALK:
  - Self-service portal (Backstage/Port)
  - Golden paths: opinionated starter templates
  - Automated environment provisioning
  - TechDocs integrated in portal
  - Onboarding time: 2-3 days

RUN:
  - Score/Humanitec workload spec for env-agnostic deploys
  - Dynamic configuration management (no YAML hand-editing)
  - Platform APIs (create service, provision DB, rotate secrets)
  - Paved roads with escape hatches
  - Developer NPS tracked quarterly
  - Onboarding time: < 1 day
```

### Backstage (CNCF)

Open-source developer portal by Spotify.

**Core plugins:**

| Plugin | Purpose |
|--------|---------|
| Catalog | Service/component registry. `catalog-info.yaml` in each repo |
| Scaffolder | Cookiecutter templates. `template.yaml` defines parameters + steps |
| TechDocs | Docs-as-code. MkDocs in Backstage. Write in repo, render in portal |
| Kubernetes | Pod/deployment status per service |
| ArgoCD | Deployment status from ArgoCD |
| GitHub Actions | CI status per component |
| Lighthouse | Automated audits (perf, a11y, SEO) |

```yaml
# catalog-info.yaml (in each repo)
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: order-service
  description: Handles order lifecycle
  annotations:
    github.com/project-slug: org/order-service
    argocd/app-name: order-service
  tags: [go, orders, production]
spec:
  type: service
  lifecycle: production
  owner: team-orders
  system: e-commerce
  dependsOn:
    - resource:postgres-orders
  providesApis:
    - order-api
```

```yaml
# template.yaml (Scaffolder)
apiVersion: scaffolder.backstage.io/v1beta3
kind: Template
metadata:
  name: create-go-service
  title: Go Microservice
  description: Scaffold new Go service with CI/CD
spec:
  owner: platform-team
  type: service
  parameters:
    - title: Service Info
      properties:
        name:
          title: Service name
          type: string
          pattern: "^[a-z][a-z0-9-]+$"
    - title: Infrastructure
      properties:
        database:
          title: Database
          type: string
          enum: [postgres, mysql, none]
  steps:
    - id: fetch-base
      action: fetch:template
      input:
        url: ./skeleton
        values:
          name: ${{ parameters.name }}
          database: ${{ parameters.database }}
    - id: publish
      action: github:repo:create
      input:
        repoUrl: github.com?repo=${{ parameters.name }}&owner=org
    - id: register
      action: catalog:register
      input:
        repoContentsUrl: ${{ steps.publish.output.repoContentsUrl }}
        catalogInfoPath: /catalog-info.yaml
```

### Humanitec & Score

**Score**: open-source workload spec. Declarative, environment-agnostic.

```yaml
# score.yaml
apiVersion: score.dev/v1b1
metadata:
  name: order-service
  annotations:
    score.dev/description: "Order processing service"

containers:
  main:
    image: .
    variables:
      DB_HOST: ${resources.db.host}
      DB_PORT: ${resources.db.port}
      DB_NAME: ${resources.db.name}
    resources:
      limits:
        memory: "256Mi"
        cpu: "500m"

resources:
  db:
    type: postgres
  cache:
    type: redis
    params:
      maxmemory: 128mb
```

Humanitec orchestrator resolves `${resources.db.host}` differently per env:
- Dev: local Docker Postgres
- Staging: small RDS instance
- Prod: multi-AZ RDS cluster

**Value**: same `score.yaml` deploys to any environment. No env-specific YAML.

### Port vs Cortex

| Aspect | Port | Cortex |
|--------|------|--------|
| Focus | Service catalog + self-service actions | Service catalog + scorecards |
| Scorecards | Yes | Advanced (GPA-style scoring) |
| Self-service | UI + API forms, webhook actions | Limited |
| Integrations | 50+ (K8s, ArgoCD, GitHub, PagerDuty) | 40+ |
| Pricing | Free tier + enterprise | Enterprise only |
| Config as code | Yes (JSON blueprints) | YAML-based |
| Open source | No | No |
| Best for | Self-service portal | Operational excellence scorecards |

### Golden Paths vs Paved Roads vs Guardrails

```
Golden Paths:
  - Single recommended way to do something
  - Opinionated: "use this template, this CI pipeline, this deploy method"
  - Example: Spring Initializr for Java, Create React App for React
  - Trade-off: may not fit edge cases

Paved Roads:
  - Multiple supported paths, all well-maintained
  - Less opinionated than golden paths
  - Example: "We support both Go and Java, both have full CI/CD pipelines"
  - Trade-off: more maintenance burden

Guardrails:
  - Automated policy enforcement
  - Prevent bad outcomes without prescribing solutions
  - Examples: OPA/Kyverno policies, branch protection, required checks
  - Usually combine with golden paths or paved roads
```

**Practical mix**: Start with guardrails (cheap). Add golden paths for most common workflows. Graduate to paved roads as org scales.

---

## Step 13: Trunk-Based Development

Source: https://trunkbaseddevelopment.com/

### Core Principles

```
- All work happens on main/trunk (or very short-lived branches)
- Branch lifetime: < 1 day (ideally hours)
- Feature branches merged same day they're created
- No long-lived release branches
- Integration happens continuously, not at end of sprint
- Rebase over merge commits for clean history
```

### Why Short-Lived Branches

```
Long-lived branches (> 1 week):
  - Merge conflicts increase exponentially with age
  - Integration risk accumulates
  - Code review becomes "review 2000 LOC wall"
  - Psychological: harder to merge, more fear
  - Blocks other developers from using your changes

Short-lived branches (< 1 day):
  - Small, focused changes
  - Merge conflicts are trivial
  - Integration is continuous
  - Code review is fast (200-400 LOC)
  - Can always release main
```

### Feature Flags

**Types:**

```
Release flags:
  - Hide incomplete features in production
  - Short-lived: removed after feature ships
  - Toggle: FEATURE_CHECKOUT_V2=true

Experiment flags (A/B):
  - Serve different variants to user segments
  - Medium-lived: run for experiment duration
  - Tools: LaunchDarkly, Unleash, Flipt, Flagsmith

Ops flags:
  - Kill switches for degraded performance
  - Long-lived: stay forever
  - Example: DISABLE_RECOMMENDATIONS=true (circuit breaker)

Permission flags:
  - Feature access per tenant/user
  - Long-lived: business logic
  - Example: PREMIUM_ANALYTICS=true
```

**Flag lifecycle:**

```
1. Create flag (specify type, owner, expiry date)
2. Use flag in code (guard new code path)
3. Roll out (1% -> 10% -> 50% -> 100%)
4. Verify (metrics, error rates, user feedback)
5. Remove flag (delete old code path, remove flag definition)
   - NEVER leave dead flags. Track flag debt.
```

**Tools:**

| Tool | Type | Notes |
|------|------|-------|
| LaunchDarkly | SaaS | Enterprise, expensive, most features |
| Unleash | Open source | Self-hosted or SaaS, good default |
| Flipt | Open source | Lightweight, fast, Go-based |
| Flagsmith | Open source | Good free tier, REST API |
| OpenFeature | Standard | CNCF standard for flag evaluation APIs |

**OpenFeature SDK example:**

```typescript
import { OpenFeature } from '@openfeature/web-sdk';

const provider = new LaunchDarklyProvider(ldClient);
OpenFeature.setProvider(provider);
const client = OpenFeature.getClient();

const showNewCheckout = await client.getBooleanValue(
  'checkout-v2',
  false,  // default value
  { targetingKey: user.id }
);

if (showNewCheckout) {
  render(CheckoutV2);
} else {
  render(CheckoutV1);
}
```

### Branch by Abstraction

When feature flag is impractical (large refactor spanning many files):

```
1. Identify the code that needs to change
2. Introduce abstraction (interface/strategy pattern)
3. Implement new behavior behind abstraction
4. Swap implementations at runtime (flag or config)
5. Verify new implementation works
6. Remove old implementation
7. Remove abstraction if no longer needed
```

```python
# Step 1-2: Abstract
class PaymentProcessor(ABC):
    @abstractmethod
    def charge(self, amount: Decimal) -> Receipt: ...

class StripeProcessor(PaymentProcessor):  # existing
    def charge(self, amount: Decimal) -> Receipt: ...

# Step 3: New implementation behind abstraction
class AdyenProcessor(PaymentProcessor):  # new
    def charge(self, amount: Decimal) -> Receipt: ...

# Step 4: Swap via config
processor = AdyenProcessor() if config.use_adyen else StripeProcessor()

# Step 6-7: After full migration, remove StripeProcessor + flag
```

---

## Step 14: GitOps

### Principles (OpenGitOps - CNCF)

```
1. Declarative: Desired state described declaratively (YAML/HCL)
2. Versioned & Immutable: State stored in Git (single source of truth)
3. Pulled Automatically: Agents pull desired state, not pushed to
4. Continuously Reconciled: Agent ensures actual = desired state
```

### Reconciliation Loop

```
+-----------------+
|   Git Repo      |  (desired state: YAML manifests)
|   (source)      |
+--------+--------+
         | watch/poll for changes
         v
+-----------------+
|  GitOps Agent   |  (ArgoCD / Flux)
|  (reconciler)   |
+--------+--------+
         | compare desired vs actual
         v
+-----------------+
|  Kubernetes     |  (actual state)
|  Cluster        |
+--------+--------+
         | drift detected?
         v
    apply desired state (kubectl apply)
```

**Drift detection**: If someone runs `kubectl edit` manually, agent detects drift and reverts. Manual changes are anti-pattern.

### ArgoCD vs Flux

| Aspect | ArgoCD | Flux |
|--------|--------|------|
| CNCF status | Graduated | Graduated |
| UI | Rich web UI (built-in) | No built-in UI (use Weave GitOps) |
| Architecture | Centralized (single control plane) | Distributed (per-cluster agents) |
| App model | Application CRD | Kustomization + HelmRelease CRDs |
| Multi-tenancy | Projects + RBAC | Namespace-scoped, native K8s RBAC |
| Notifications | Built-in (Slack, Discord, webhook) | Notification controller (separate) |
| Helm support | Native (values from UI) | HelmRelease CRD (more native Helm) |
| Kustomize support | Native | Kustomization CRD (first-class) |
| Image automation | No (needs external) | Built-in (image-reflector-controller) |
| Progressive delivery | Via Argo Rollouts (separate) | Via Flagger (separate) |
| Secret management | External Secrets, Sealed Secrets, Vault | SOPS, External Secrets, Sealed Secrets |
| Config | YAML/CLI/UI | YAML only (GitOps-native) |
| Best for | Teams wanting UI + visualization | Teams wanting pure GitOps + automation |

**ArgoCD example:**

```yaml
# application.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: order-service
  namespace: argocd
spec:
  project: production
  source:
    repoURL: https://github.com/org/k8s-manifests.git
    targetRevision: main
    path: apps/order-service/overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: order-service
  syncPolicy:
    automated:
      prune: true        # delete resources removed from Git
      selfHeal: true      # revert manual changes
    syncOptions:
      - CreateNamespace=true
```

**Flux example:**

```yaml
# gitrepository.yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: k8s-manifests
  namespace: flux-system
spec:
  interval: 1m
  url: https://github.com/org/k8s-manifests.git
  ref:
    branch: main

# kustomization.yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: order-service
  namespace: flux-system
spec:
  interval: 5m
  path: ./apps/order-service/overlays/production
  prune: true
  sourceRef:
    kind: GitRepository
    name: k8s-manifests
```

---

## Step 15: Conventional Commits & Semver Automation

### Conventional Commits Format

Source: https://www.conventionalcommits.org/

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]

# Examples:
feat(auth): add OAuth2 PKCE flow
fix(api): prevent race condition on order creation
docs(readme): update installation steps
feat!: change user API response format    # BREAKING CHANGE
refactor(core): extract validation logic  # no version bump

# Types that trigger version bumps:
#   fix:       -> PATCH (1.0.0 -> 1.0.1)
#   feat:      -> MINOR (1.0.0 -> 1.1.0)
#   feat!:     -> MAJOR (1.0.0 -> 2.0.0)
#   BREAKING CHANGE footer: -> MAJOR

# Types that DON'T bump:
#   docs, style, refactor, perf, test, build, ci, chore
```

### commitlint

```bash
npm install -D @commitlint/cli @commitlint/config-conventional

# commitlint.config.js
echo "module.exports = { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js

# As pre-commit hook (Husky)
npx husky add .husky/commit-msg 'npx commitlint --edit "$1"'
```

**Custom rules example:**

```javascript
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'docs', 'style', 'refactor',
      'perf', 'test', 'build', 'ci', 'chore', 'revert'
    ]],
    'scope-enum': [2, 'always', [
      'api', 'auth', 'db', 'ui', 'ci', 'deps', 'infra'
    ]],
    'subject-max-length': [2, 'always', 72],
    'body-max-line-length': [2, 'always', 100],
  }
};
```

### semantic-release (Node.js)

```bash
npm install -D semantic-release

# .releaserc.json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    "@semantic-release/github",
    "@semantic-release/git"
  ]
}
```

**What it does:**
1. Analyzes commits since last tag
2. Determines version bump (patch/minor/major)
3. Generates CHANGELOG
4. Publishes to npm
5. Creates GitHub release with notes
6. Commits changelog + version bump back to repo

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
          changelog-types: |
            [
              {"type":"feat","section":"Features","hidden":false},
              {"type":"fix","section":"Bug Fixes","hidden":false},
              {"type":"perf","section":"Performance","hidden":false},
              {"type":"docs","section":"Documentation","hidden":false}
            ]
```

**release-please vs semantic-release:**

| Aspect | release-please | semantic-release |
|--------|---------------|------------------|
| Creator | Google | open-source community |
| Approach | Creates PR with changelog | Auto-commits + tags on merge |
| Visibility | PR lets humans review release | Fully automated (no human gate) |
| Multi-language | Yes (20+ release types) | Node.js native, plugins for others |
| Commit format | Conventional Commits | Conventional Commits |
| GitHub integration | PRs + releases | Releases only |

---

## Step 16: Monorepo vs Polyrepo

### Approaches by Company

```
Google (Monorepo):
  - Single repo, 2B+ lines of code
  - Custom tools: Bazel (build), Piper (VCS), Critique (code review)
  - Trunk-based, no branches
  - Every change reviewed by owner of affected code
  - Massive CI: only rebuild affected targets

Meta (Monorepo):
  - Single repo for all products (Facebook, Instagram, WhatsApp)
  - Mercurial initially, now custom Git (Sapling)
  - Buck2 build system
  - Ship-of-Theseus: continuous deployment, no releases

Uber (Polyrepo -> Monorepo -> Polyrepo):
  - Started polyrepo, migrated to monorepo
  - Found monorepo hard at scale without Google tooling
  - Settled on polyrepo with strong service templates
  - Lesson: monorepo benefits require investment in tooling

Spotify (Polyrepo with Golden Path):
  - Each team owns repos
  - Backstage for service discovery
  - Strong internal templates for consistency
  - Good for team autonomy

Airbnb (Monorepo):
  - Single repo, millions of lines
  - Nx for JS/TS build orchestration
  - Shared libraries published internally
```

### When to Use Which

```
Monorepo wins when:
  - Shared code/libraries across services
  - Atomic commits across projects needed
  - Consistent tooling/linting/formatting
  - Single CI/CD pipeline per change
  - Code reuse is high priority
  - You have/will invest in build tooling

Polyrepo wins when:
  - Teams are autonomous, different tech stacks
  - Different release cadences
  - Strong service boundaries
  - Open-source repos alongside internal
  - Teams can't agree on tooling
  - You don't have build tooling budget
```

### Build Tooling Comparison

| Tool | Ecosystem | Incremental | Remote Cache | Remote Exec | Language |
|------|-----------|-------------|--------------|-------------|----------|
| Nx | JS/TS primary | Yes (affected) | Yes (Nx Cloud) | No | JS/TS, some polyglot |
| Turborepo | JS/TS | Yes | Yes (Vercel) | No | JS/TS only |
| Bazel | Polyglot | Yes (fine-grained) | Yes | Yes | Any (via rules) |
| Pants | Polyglot | Yes | Yes | Yes | Python, Go, Java, Shell |
| Lage | JS/TS | Yes | No | No | JS/TS only |
| Moon | Polyglot | Yes | Yes | No | JS/TS, Rust, Go |

**Nx example (monorepo):**

```bash
npx create-nx-workspace@latest myorg --preset=ts

# Generate apps/libs
nx g @nx/node:application api
nx g @nx/react:application web
nx g @nx/js:library shared-utils

# Run affected only (incremental)
nx affected -t lint test build    # only builds changed + dependents

# Dependency graph
nx graph                          # interactive dep graph in browser
```

**Turborepo example:**

```bash
npx create-turbo@latest

# turbo.json
{
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"]
    },
    "lint": {}
  }
}

# Run with caching
turbo run build test lint         # caches outputs, skips unchanged
turbo run build --filter=web      # only web package + deps
```

**Bazel example:**

```python
# BUILD file
go_library(
    name = "order_lib",
    srcs = ["order.go"],
    deps = [
        "//proto/order:order_proto",
        "//shared/errors",
    ],
)

go_test(
    name = "order_test",
    deps = [":order_lib"],
)
```

```bash
bazel test //services/order:order_test    # only rebuilds changed deps
bazel test //...                           # all tests (incremental)
```

---

## Step 17: Git Internals

### Object Model

Git stores everything as 4 object types in `.git/objects/`:

```
blob:    File content (just bytes, no filename)
tree:    Directory listing (maps filenames to blobs/trees)
commit:  Snapshot pointer (tree + parent + author + message)
tag:     Annotated tag (points to commit + tagger + message)
```

```bash
# Inspect objects
git cat-file -t <hash>     # show type (blob/tree/commit/tag)
git cat-file -p <hash>     # pretty-print contents

# Example: trace a commit
git cat-file -p HEAD
# tree 4b825dc642cb6eb9a060e54bf899d69f3e2e1b
# parent abc123...
# author Name <email> timestamp +0000
# committer Name <email> timestamp +0000
#
# Commit message

git cat-file -p 4b825dc642cb6eb9a060e54bf899d69f3e2e1b
# 100644 blob def123...    README.md
# 040000 tree abc456...    src

git cat-file -p def123...  # actual file contents
```

**SHA-1 hash**: content-addressable. Same content = same hash. Hash computed from object header + content.

### Refs

```
refs/heads/main          -> local branch (pointer to commit SHA)
refs/heads/feature-x     -> local branch
refs/remotes/origin/main -> remote tracking branch
refs/tags/v1.0.0         -> tag (lightweight: commit SHA, annotated: tag object)
HEAD                     -> symbolic ref: points to branch ref
```

```bash
cat .git/HEAD                     # ref: refs/heads/main
cat .git/refs/heads/main          # commit SHA
git symbolic-ref HEAD             # read HEAD
git update-ref refs/heads/main <sha>  # move branch (dangerous)
```

### Reflog

Local history of where HEAD and branch tips have pointed. Not shared. Default retention: 90 days.

```bash
git reflog                    # HEAD history
git reflog show main          # branch tip history
git reflog show --all         # all refs

# Rescue: undo rebase disaster
git reflog                    # find pre-rebase SHA
git reset --hard HEAD@{5}     # go back 5 HEAD movements

# Cherry-pick lost commit
git reflog | grep "commit msg"
git cherry-pick <sha>
```

### Cherry-Pick vs Rebase

```
cherry-pick:
  - Copies specific commits onto current branch
  - Creates NEW commit objects (different SHAs)
  - Use case: backport fix to release branch
  - Original commits untouched
  
  git cherry-pick abc123           # apply one commit
  git cherry-pick abc123..def456   # range (exclusive start)
  git cherry-pick -m 1 merge-sha   # cherry-pick merge commit (first parent)

rebase:
  - Replays commits from current branch onto new base
  - Creates NEW commit objects (different SHAs)
  - Linearizes history (no merge commits)
  - Use case: update feature branch with latest main
  
  git rebase main                  # replay current branch onto main
  git rebase -i HEAD~5             # interactive: squash, reorder, edit
  git rebase --onto main old-base feature  # surgical rebase
```

**Key difference**: cherry-pick selects specific commits. Rebase replays entire branch.

**Interactive rebase commands:**

```
pick   = use commit as-is
reword = use commit, edit message
edit   = use commit, stop for amending
squash = use commit, meld into previous
fixup  = use commit, meld into previous (discard message)
drop   = remove commit

# Typical: squash WIP commits before merge
git rebase -i main
# pick abc123 feat: add user model
# squash def456 fix typo
# squash ghi789 WIP tests
# Result: single clean commit
```

**Safety**: `git rebase` rewrites history. Never rebase commits that are pushed to shared branches. Use `git push --force-with-lease` if you must force-push after rebase.

---

## Step 18: Code Review Best Practices

### Google Engineering Practices

Source: https://google.github.io/eng-practices/review/

**Metrics from Google research:**
- Review speed: ~400 LOC/hour (diminishing returns after 60 min)
- Optimal review size: < 400 LOC per review
- Response time: < 1 hour for initial response (not full review)
- Multiple small reviews > one large review

**Reviewer responsibilities:**
```
1. Correctness: Does code do what it claims?
2. Design: Is code well-designed? Right abstractions?
3. Complexity: Can code be simpler? Over-engineered?
4. Tests: Are there tests? Do they test the right things?
5. Naming: Are names clear and consistent?
6. Comments: Are comments useful (why, not what)?
7. Style: Does it follow style guide?
8. Documentation: Do public APIs have docs?
```

**Author responsibilities:**
```
1. Self-review first: read your own diff before requesting review
2. Write good description: what, why, how, testing done
3. Small CLs: < 400 LOC, single responsibility
4. Respond to every comment: resolve or discuss
5. Don't take it personally: feedback is about code, not you
```

### SmartBear Code Review Findings

Source: https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/

**Key findings from studying 2500+ reviews:**
- Review no more than 200-400 LOC at a time
- Take breaks after 60 minutes (inspection rate drops)
- Expect ~15 defects per hour in dense code
- Author should annotate code before review (explain non-obvious parts)
- Checklists improve defect detection by 30-90%
- Tool-assisted review catches more than unstructured review

### Comment Prefixes

Standardize review comment tone with prefixes:

```
nit:       Style/formatting preference. Non-blocking. Author decides.
suggestion: Alternative approach. Optional. Author weighs trade-offs.
question:  "I don't understand this. Can you explain?" Clarification only.
issue:     Bug or correctness problem. Must fix before merge.
todo:      Something that should be done but can be in follow-up.
praise:    "Nice approach!" Positive feedback. Use generously.
thought:   Thinking out loud. Neither block nor unblock.
```

**Examples:**
```
nit: Extra blank line here.

suggestion: Consider using a Map instead of array lookup for O(1) access.

question: Why do we need the retry here? Is the upstream unreliable?

issue: This will panic on nil if user has no address. Need nil check.

praise: Clever use of the builder pattern here. Very clean.
```

### Review Checklist Template

```markdown
## Code Review Checklist

### Correctness
- [ ] Does it solve the stated problem?
- [ ] Edge cases handled? (nil, empty, boundary values)
- [ ] Error handling present and correct?
- [ ] Concurrency safe? (if applicable)

### Design
- [ ] Right level of abstraction?
- [ ] Follows existing patterns in codebase?
- [ ] Single responsibility?
- [ ] Dependencies reasonable?

### Testing
- [ ] Tests cover happy path + edge cases?
- [ ] Tests are deterministic (no flakes)?
- [ ] Error paths tested?
- [ ] Test names describe behavior?

### Security
- [ ] Input validated/sanitized?
- [ ] Auth/permissions checked?
- [ ] No secrets in code/logs?
- [ ] SQL injection / XSS prevented?

### Performance
- [ ] No N+1 queries?
- [ ] Reasonable memory usage?
- [ ] Appropriate data structures?

### Maintainability
- [ ] Code is readable without comments?
- [ ] Naming is clear and consistent?
- [ ] Magic numbers extracted?
- [ ] Public APIs documented?
```

## Step 19: GitHub Actions Advanced

### Reusable Workflows

Call workflows from other repos/workflows. Define at top level with `workflow_call` trigger.

```yaml
# .github/workflows/reusable-deploy.yml (called workflow)
name: Reusable Deploy
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
      image_tag:
        required: true
        type: string
    secrets:
      DEPLOY_TOKEN:
        required: true
    outputs:
      deploy_url:
        description: "Deployed URL"
        value: ${{ jobs.deploy.outputs.url }}

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ inputs.environment }}
    outputs:
      url: ${{ steps.deploy.outputs.url }}
    steps:
      - uses: actions/checkout@v4
      - id: deploy
        run: |
          echo "Deploying ${{ inputs.image_tag }} to ${{ inputs.environment }}"
          echo "url=https://${{ inputs.environment }}.example.com" >> "$GITHUB_OUTPUT"
```

```yaml
# Caller workflow
jobs:
  deploy-staging:
    uses: ./.github/workflows/reusable-deploy.yml
    with:
      environment: staging
      image_tag: ${{ needs.build.outputs.tag }}
    secrets:
      DEPLOY_TOKEN: ${{ secrets.STAGING_DEPLOY_TOKEN }}

  deploy-prod:
    needs: deploy-staging
    uses: ./.github/workflows/reusable-deploy.yml
    with:
      environment: production
      image_tag: ${{ needs.build.outputs.tag }}
    secrets:
      DEPLOY_TOKEN: ${{ secrets.PROD_DEPLOY_TOKEN }}
```

Cross-repo: `uses: org/repo/.github/workflows/deploy.yml@main`

### Composite Actions

Bundle multiple steps into one reusable action with `action.yml`.

```yaml
# .github/actions/setup-project/action.yml
name: Setup Project
description: Install deps and configure environment
inputs:
  node-version:
    default: '20'
  python-version:
    default: '3.12'
runs:
  using: composite
  steps:
    - uses: actions/setup-node@v4
      with:
        node-version: ${{ inputs.node-version }}
    - uses: actions/setup-python@v5
      with:
        python-version: ${{ inputs.python-version }}
    - shell: bash
      run: npm ci
    - shell: bash
      run: pip install -r requirements.txt
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
        run: |
          # Generate matrix dynamically (e.g., from changed files)
          echo 'matrix={"service":["api","web","worker"],"include":[{"service":"api","port":8080},{"service":"web","port":3000},{"service":"worker","port":9090}]}' >> "$GITHUB_OUTPUT"

  build:
    needs: prepare
    runs-on: ubuntu-latest
    strategy:
      matrix: ${{ fromJSON(needs.prepare.outputs.matrix) }}
      fail-fast: false
    steps:
      - run: echo "Building ${{ matrix.service }} on port ${{ matrix.port }}"
```

### Concurrency Groups

Prevent duplicate runs, cancel in-progress when new push arrives.

```yaml
# Cancel in-progress runs on same branch/PR
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

# Environment-level concurrency (max 1 deploy at a time)
concurrency:
  group: deploy-${{ github.event.inputs.environment }}
  cancel-in-progress: false

# Dynamic group for PR comments
concurrency:
  group: pr-${{ github.event.pull_request.number }}
  cancel-in-progress: true
```

## Step 20: GitLab CI Advanced

### Child Pipelines

Trigger child pipelines from main pipeline for modularization.

```yaml
# Generate child pipeline config dynamically
generate:
  stage: build
  script:
    - python generate_pipeline.py > child-pipeline.yml
  artifacts:
    paths:
      - child-pipeline.yml

child:
  stage: test
  trigger:
    include:
      - artifact: child-pipeline.yml
        job: generate
    strategy: depend  # parent waits for child
```

### Multi-Project Pipelines

Trigger pipeline in another project and wait for result.

```yaml
deploy-infra:
  stage: deploy
  trigger:
    project: infra/terraform
    branch: main
    strategy: depend
  variables:
    UPSTREAM_COMMIT: $CI_COMMIT_SHA
    ENVIRONMENT: staging
```

### DAG with needs Keyword

`needs` defines explicit job dependencies instead of stage-based ordering. Jobs run as soon as dependencies complete.

```yaml
stages:
  - build
  - test
  - deploy

build-api:
  stage: build
  script: make build-api

build-web:
  stage: build
  script: make build-web

test-api:
  stage: test
  needs: [build-api]  # runs as soon as build-api done, ignores stage order
  script: make test-api

test-web:
  stage: test
  needs: [build-web]
  script: make test-web

deploy:
  stage: deploy
  needs: [test-api, test-web]  # runs when both tests pass
  script: make deploy
```

Minimal DAG: `needs: []` runs immediately with no dependencies.

### Resource Groups

Limit concurrent access to a shared resource (e.g., only one deploy to prod at a time).

```yaml
deploy-production:
  stage: deploy
  resource_group: production
  script: make deploy-prod
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

Queue behavior: second job waits until first finishes. Combine with `environment` for auto-locking.

```yaml
deploy:
  resource_group: deploy/$CI_ENVIRONMENT_NAME
  environment:
    name: $CI_ENVIRONMENT_NAME
    on_stop: stop-deploy
```

## Step 21: Build Caching Deep Dive

### Bazel Remote Cache

Bazel separates analysis (loading, analysis) from execution. Remote cache stores action outputs.

```bash
# .bazelrc -- use remote cache
build --remote_cache=grpcs://cache.example.com:443
build --remote_header=Authorization="Bearer $CACHE_TOKEN"
# Read-only for CI (no cache pollution)
build --remote_upload_local_results=false
# Accept cached results
build --noremote_accept_cached
```

```yaml
# GitHub Actions: cache Bazel with GitHub cache backend
- uses: actions/cache@v4
  with:
    path: ~/.cache/bazel
    key: bazel-${{ runner.os }}-${{ hashFiles('.bazelversion', 'MODULE.bazel') }}
    restore-keys: bazel-${{ runner.os }}-
```

**Cache key strategy:** Hash lockfile (MODULE.bazel, WORKSPACE) + OS. Invalidate on dependency change, not on source change (Bazel handles source-level caching internally).

### Gradle Build Cache

Gradle caches task outputs. Local cache is on by default; remote cache requires setup.

```kotlin
// settings.gradle.kts
buildCache {
    local {
        enabled = true
        directory = File(rootDir, ".gradle/build-cache")
    }
    remote<HttpBuildCache> {
        url = uri("https://cache.example.com/cache/")
        credentials {
            username = "ci"
            password = System.getenv("CACHE_PASSWORD") ?: ""
        }
        push = System.getenv("CI") != null  // CI pushes, local pulls only
    }
}
```

```bash
# gradle.properties
org.gradle.caching=true
org.gradle.configuration-cache=true   # caches configuration phase
```

**Debug cache hits:** `./gradlew build --info | grep -i "cache"`

### Docker Layer Caching with buildx

```bash
# GitHub Actions: cache registry
docker buildx build \
  --cache-from type=registry,ref=ghcr.io/org/cache:buildcache \
  --cache-to type=registry,ref=ghcr.io/org/cache:buildcache,mode=max \
  -t myapp:latest .

# Local: cache to local directory
docker buildx build \
  --cache-from type=local,src=/tmp/buildcache \
  --cache-to type=local,dest=/tmp/buildcache,mode=max \
  -t myapp:latest .
```

**mode=max** caches all layers (including intermediate). **mode=min** only caches final layers (default).

```yaml
# GitHub Actions: inline cache (simpler but less effective)
- uses: docker/build-push-action@v5
  with:
    push: true
    tags: ghcr.io/org/app:latest
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

### Cache Invalidation Strategies

**Key-based invalidation:**
```
hash(files) -> cache key
restore-keys: [prefix-]  # fallback to partial match
```

**Strategies:**
1. **Content-hash:** Hash lockfile/manifest. Invalidate on dependency change.
2. **Time-based:** TTL expiry (e.g., 7 days). Prevents stale cache accumulation.
3. **Version-bump:** Bump cache version prefix when build infra changes.
4. **Selective invalidation:** Cache per-component (monorepo). Invalidate only affected.

```yaml
# Monorepo: per-service cache keys
- uses: actions/cache@v4
  with:
    path: services/api/node_modules
    key: api-nm-${{ hashFiles('services/api/package-lock.json') }}
    restore-keys: api-nm-
```

**Anti-patterns:**
- Caching node_modules directly (cache .npm or pnpm store instead)
- One global cache key for entire repo (invalidates too broadly)
- Caching build output that depends on env vars (stale secrets)

## Step 22: Artifact Management

### OCI Artifacts with oras

Push/pull non-Docker artifacts to OCI registries (Helm charts, WASM, SBOMs, signatures, policies).

```bash
# Install oras
curl -LO https://github.com/oras-project/oras/releases/download/v1.2.0/oras_1.2.0_linux_amd64.tar.gz
tar xzf oras_1.2.0_linux_amd64.tar.gz

# Push arbitrary file as OCI artifact
oras push ghcr.io/org/artifacts:v1.0 \
  --artifact-type application/vnd.example.config.v1 \
  config.json:application/json

# Push multiple files with annotations
oras push ghcr.io/org/release:v1.2.0 \
  ./dist/app-linux:application/vnd.oci.image.layer.v1.tar \
  ./dist/app-darwin:application/vnd.oci.image.layer.v1.tar \
  --annotation "org.opencontainers.image.version=1.2.0"

# Pull
oras pull ghcr.io/org/artifacts:v1.0

# Copy between registries
oras copy ghcr.io/org/app:v1 ghcr.io/backup/app:v1
```

### Sigstore Signing with Cosign (Keyless)

Sign artifacts using OIDC identity (GitHub Actions, GitLab CI) — no long-lived keys.

```bash
# Install cosign
go install github.com/sigstore/cosign/v2/cmd/cosign@latest

# Keyless sign (in CI, uses OIDC token automatically)
COSIGN_EXPERIMENTAL=1 cosign sign ghcr.io/org/app@sha256:abc123...

# Sign with annotations
cosign sign \
  --annotations "repo=https://github.com/org/app" \
  --annotations "ref=$GITHUB_REF" \
  ghcr.io/org/app@sha256:abc123...

# Verify
COSIGN_EXPERIMENTAL=1 cosign verify \
  --certificate-identity-regexp="https://github.com/org/app" \
  --certificate-oidc-issuer="https://token.actions.githubusercontent.com" \
  ghcr.io/org/app@sha256:abc123...

# Sign a file (not OCI image)
cosign sign-blob --bundle sig.bundle ./artifact.tar.gz
cosign verify-blob --bundle sig.bundle ./artifact.tar.gz
```

**Keyless flow:** CI runner gets OIDC token → cosign gets short-lived cert from Fulcio → signs artifact → stores in Rekor transparency log.

### SBOM Generation

Generate Software Bill of Materials for supply chain transparency.

**Syft** (Anchore):
```bash
# Install
curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s

# Generate SBOM from container image
syft ghcr.io/org/app:latest -o cyclonedx-json > sbom.cdx.json
syft ghcr.io/org/app:latest -o spdx-json > sbom.spdx.json
syft ghcr.io/org/app:latest -o spdx-tag-value > sbom.spdx

# Generate from directory
syft dir:. -o cyclonedx-json > sbom.cdx.json

# Attach SBOM to OCI image (with oras)
oras attach \
  --artifact-type application/spdx+json \
  ghcr.io/org/app:v1 \
  sbom.spdx.json:application/spdx+json
```

**CycloneDX vs SPDX:**
```
CycloneDX (OWASP): JSON/XML, designed for security use cases,
  vulnerability analysis, license compliance. Preferred for app deps.

SPDX (Linux Foundation): JSON/YAML/RDF, ISO standard (5962:2021),
  legal/license compliance focus. Preferred for license auditing.
```

**GitHub Actions: generate and attest SBOM:**
```yaml
- name: Generate SBOM
  run: syft ghcr.io/org/app@${{ steps.build.outputs.digest }} -o cyclonedx-json > sbom.cdx.json

- name: Attest SBOM
  run: |
    gh attestation add sbom.cdx.json \
      --repo ${{ github.repository }} \
      --bundle-from-oci

- name: Sign SBOM
  run: cosign sign-blob --bundle sbom.bundle sbom.cdx.json
```

**Grype** (vulnerability scanner that consumes SBOMs):
```bash
grype sbom:sbom.cdx.json
# Exit non-zero if critical/high vulns found
grype sbom:sbom.cdx.json --fail-on critical
```

## Step 22: Backstage Plugins Deep Dive

### Plugin Architecture

Backstage plugins follow a dual architecture: **frontend plugins** (React/TypeScript) and **backend plugins** (Node.js/TypeScript). Frontend plugins render UI in the Backstage app shell; backend plugins expose REST APIs consumed by frontend or external clients.

```typescript
// Frontend plugin structure (plugin-my-feature)
import { createPlugin, createRouteRef, createRoutableExtension } from '@backstage/core-plugin-api';

export const rootRouteRef = createRouteRef({ id: 'my-feature' });

export const myFeaturePlugin = createPlugin({
  id: 'my-feature',
  routes: { root: rootRouteRef },
});

export const MyFeaturePage = myFeaturePlugin.provide(
  createRoutableExtension({
    name: 'MyFeaturePage',
    component: () => import('./components/MyFeaturePage').then(m => m.MyFeaturePage),
    mountPoint: rootRouteRef,
  }),
);
```

```typescript
// Backend plugin structure (plugin-my-feature-backend)
import { createRouter } from '@backstage/backend-common';
import express from 'express';
import { Logger } from 'winston';

export interface RouterOptions {
  logger: Logger;
  config: Config;
}

export async function createRouter(options: RouterOptions): Promise<express.Router> {
  const { logger, config } = options;
  const router = Router();
  router.get('/health', (_, res) => res.json({ status: 'ok' }));
  return router;
}
```

**Plugin communication:** Frontend plugins use `@backstage/core-plugin-api` for routing and shared state. Backend plugins register via `createBackendModule` in the new backend system. Cross-plugin data sharing uses `EntityProvider` interfaces and the Software Catalog API.

### Key Plugin Categories

**CI/CD:**
- `@backstage/plugin-github-actions` — GitHub workflow status, triggers
- `@backstage/plugin-jenkins` — Jenkins build history, stages
- `@backstage/plugin-argo-cd` — Argo CD sync status, rollback
- `@backstage/plugin-tekton` — Tekton pipeline visualization

**Monitoring & Observability:**
- `@backstage/plugin-prometheus` — embedded Prometheus graphs per entity
- `@backstage/plugin-grafana` — Grafana dashboard links per entity
- `@backstage/plugin-pagerduty` — on-call schedules, incidents
- `@backstage/plugin-sentry` — error tracking per service

**Security:**
- `@backstage/plugin-security-insights` — GitHub Dependabot alerts
- `@backstage/plugin-sonarqube` — code quality gates per entity
- `@backstage/plugin-snyk` — vulnerability dashboard per entity
- `roadiehq/backstage-plugin-security-hub` — aggregated security posture

**Infrastructure:**
- `@backstage/plugin-kubernetes` — pod status, deployments per entity
- `@backstage/plugin-terraform` — Terraform Cloud run status
- `@backstage/plugin-aws-lambda` — Lambda function metrics
- `@backstage/plugin-cost-insights` — cloud cost per team/service

### Custom Plugin Pattern

```bash
# Generate scaffold
npx @backstage/create-app@latest --template-path ./my-plugin-template
# Or use the CLI
npx @backstage/cli new --select plugin
```

```typescript
// Custom plugin with catalog entity integration
import { useEntity } from '@backstage/plugin-catalog-react';
import { Entity } from '@backstage/catalog-model';

export const MyCustomCard = () => {
  const { entity } = useEntity();
  const myAnnotation = entity.metadata.annotations?.['my-org.com/custom-data'];

  return (
    <InfoCard title="Custom Data">
      {myAnnotation ? <Content data={myAnnotation} /> : <MissingAnnotationEmptyState annotation="my-org.com/custom-data" />}
    </InfoCard>
  );
};
```

**Best practices:**
- Keep plugins focused: one concern per plugin
- Use `@backstage/plugin-catalog-react` hooks (`useEntity`, `useAsyncEntity`) for entity data
- Prefer backend-for-frontend pattern: frontend plugin calls its own backend plugin, backend plugin calls external APIs
- Use `@backstage/backend-plugin-api` (new backend system) for backend plugins
- Export a `createPlugin` or `createBackendModule` — no side effects at import time

### Catalog Best Practices

**Entity hierarchy (4 levels):**

```yaml
# Domain — groups related systems
apiVersion: backstage.io/v1alpha1
kind: Domain
metadata:
  name: payments
  description: Payment processing domain
spec:
  owner: team-payments

---
# System — cohesive set of components
apiVersion: backstage.io/v1alpha1
kind: System
metadata:
  name: payment-processing
spec:
  owner: team-payments
  domain: payments

---
# Component — deployable unit
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: payment-api
  annotations:
    github.com/project-slug: org/payment-api
    jenkins.io/job-name: payment-api/main
    backstage.io/techdocs-ref: dir:.
spec:
  type: service
  lifecycle: production
  owner: team-payments
  system: payment-processing
  providesApis: [payment-api]
  dependsOn: [component:payment-db]

---
# Resource — infrastructure dependency
apiVersion: backstage.io/v1alpha1
kind: Resource
metadata:
  name: payment-db
spec:
  type: database
  owner: team-payments
  system: payment-processing
```

**Auto-discovery:**

```yaml
# app-config.yaml — entity providers for auto-discovery
catalog:
  providers:
    github:
      providerId:
        organization: 'my-org'
        catalogPath: '/catalog-info.yaml'
        filters:
          branch: 'main'
          repository: '.*'  # regex
    gitlab:
      providerId:
        host: gitlab.com
        group: my-org
        catalogPath: '/.backstage/catalog-info.yaml'
    kubernetes:
      providerId:
        cluster: production
```

**Annotations that unlock plugin integrations:**
```yaml
annotations:
  github.com/project-slug: org/repo          # GitHub Actions plugin
  jenkins.io/job-name: my-job                 # Jenkins plugin
  argocd/app-name: my-app                     # Argo CD plugin
  grafana/dashboard-url: https://grafana/...  # Grafana plugin
  pagerduty.com/service-id: P1234             # PagerDuty plugin
  sonarqube.org/project-key: my-project       # SonarQube plugin
  kubernetes.io/app-name: my-deploy           # K8s plugin
```

### Software Templates (Golden Path Pattern)

Templates scaffold new projects or infrastructure via `cookiecutter` or `nunjucks` templating.

```yaml
# template.yaml
apiVersion: scaffolder.backstage.io/v1beta3
kind: Template
metadata:
  name: create-node-service
  title: Create Node.js Service
  description: Golden path template for Node.js microservices
  tags: [node, typescript, microservice]
spec:
  owner: platform-team
  type: service
  parameters:
    - title: Service Details
      required: [name, owner, description]
      properties:
        name:
          title: Service Name
          type: string
          pattern: '^[a-z0-9-]+$'
        owner:
          title: Owner
          type: string
          ui:field: OwnerPicker
        description:
          title: Description
          type: string
        useTypescript:
          title: Use TypeScript
          type: boolean
          default: true
  steps:
    - id: fetch
      name: Fetch Template
      action: fetch:template
      input:
        url: ./skeleton  # cookiecutter or nunjucks template dir
        targetPath: ${{ parameters.name }}
        values:
          name: ${{ parameters.name }}
          owner: ${{ parameters.owner }}
          description: ${{ parameters.description }}
          useTypescript: ${{ parameters.useTypescript }}
    - id: publish
      name: Publish to GitHub
      action: github:repo:create
      input:
        repoUrl: github.com?owner=my-org&repo=${{ parameters.name }}
        defaultBranch: main
    - id: register
      name: Register in Catalog
      action: catalog:register
      input:
        repoContentsUrl: ${{ steps.publish.output.repoContentsUrl }}
        catalogInfoPath: /catalog-info.yaml
```

**Cookiecutter vs Nunjucks:**
```
Cookiecutter: Python-based, uses {{ cookiecutter.var }} syntax,
  cookiecutter.json for defaults. Better for Python projects.

Nunjucks: JS-based, uses {{ parameters.var }} syntax,
  native to Backstage scaffolder. Better for Node/TS projects.
  Supports conditionals: {% if parameters.useTypescript %}...{% endif %}
```

## Step 23: Developer Onboarding

### Time-to-First-Commit Benchmarks

Time-to-first-commit (TTFC) measures how quickly a new developer can make a meaningful code contribution.

```
Elite:    < 1 day    — pre-configured dev env, clear docs, good first issues tagged
Good:     1-3 days   — setup script exists, onboarding docs current, buddy assigned
Average:  3-7 days   — manual setup, docs partially outdated, some tribal knowledge
Below:    > 7 days   — broken setup, missing docs, no onboarding process
```

**Measuring TTFC:**
```bash
# GitHub: find first commit date per author
gh api graphql -f query='
{
  repository(owner: "org", name: "repo") {
    ref(qualifiedName: "main") {
      target {
        ... on Commit {
          history(first: 1, author: {email: "newdev@org.com"}) {
            nodes { committedDate }
          }
        }
      }
    }
  }
}'

# Git: first commit by author after start date
git log --author="newdev@org.com" --after="2025-01-15" --format="%H %ai" --reverse | head -1
```

### Tiered Onboarding Checklist

**Week 1 — Environment & First Contribution:**
- [ ] Dev environment setup via `make setup` or Dev Container
- [ ] Clone repo, run tests locally, verify green
- [ ] Read ARCHITECTURE.md, CONTRIBUTING.md, ADRs
- [ ] Shadow a code review (observe, don't review)
- [ ] Complete "Day 1 Pull Request" (see below)
- [ ] Meet onboarding buddy (30 min daily standup)
- [ ] Access: source control, CI, monitoring, incident tooling
- [ ] Join team channels, sprint ceremonies

**Weeks 2-4 — Domain & Ownership:**
- [ ] Pick up 2-3 "good first issues" from backlog
- [ ] Attend at least 1 design review
- [ ] Write or update 1 piece of documentation
- [ ] Deploy to staging independently
- [ ] Pair with team member on a non-trivial feature
- [ ] Understand team's on-call rotation, shadow 1 shift
- [ ] Review team's tech radar / tech debt backlog

**Month 2 — Autonomy:**
- [ ] Own and ship a small feature end-to-end
- [ ] Review 3+ PRs from peers
- [ ] Participate in retro, propose 1 improvement
- [ ] Write an ADR or RFC for a design decision
- [ ] Present a tech talk to the team (5-10 min)

**Month 3 — Full Productivity:**
- [ ] On-call rotation (with backup)
- [ ] Mentor next new hire (reverse mentoring)
- [ ] Contribute to platform/tooling improvement
- [ ] Own a service or subsystem

### Day 1 Pull Request Pattern

Every new developer should merge a PR on their first day. Purpose: learn the full workflow (branch, code, test, review, merge, deploy) before tackling real work.

**Good Day 1 PR tasks:**
```markdown
# Example Day 1 PR ideas
- Fix a typo in README.md
- Add yourself to CODEOWNERS or team.md
- Update a stale comment in code
- Add a missing test for a simple function
- Improve an error message
- Add a missing type annotation
- Update a dependency in a lockfile
```

**Template issue for Day 1 PR:**
```markdown
## Welcome to the team! 🎉

Your first task: make a small improvement to any repository.

### Steps
1. Fork/branch from `main`
2. Make a change (typo fix, doc update, test improvement)
3. Run `make check` locally
4. Open a PR, request review from your buddy
5. Address feedback, merge

### Success criteria
- PR merged within first day
- You ran CI locally and it passed
- You experienced the full PR lifecycle
```

### Onboarding Buddy System

Each new hire gets an onboarding buddy (not their manager, not their interviewer).

**Buddy responsibilities:**
- 30 min daily check-in for first 2 weeks
- Answer "dumb questions" without judgment
- Review Day 1 PR and first 3-5 PRs
- Introduce to key people across teams
- Share tribal knowledge (deployment quirks, naming conventions, "why we do X this way")
- Escalate blockers to tech lead if onboarding stalls

**Buddy selection criteria:**
- Same team, 6+ months tenure
- Good communicator, patient
- Not currently on-call or in incident response rotation
- Volunteer, not assigned by default

### Reverse Mentoring

Pair new hires with senior engineers where the new hire teaches, not learns. New hires bring fresh perspectives on:
- Tooling friction (what's broken in the dev setup?)
- Documentation gaps (what's unclear or missing?)
- Process inefficiencies (what feels unnecessary?)
- Modern practices from previous role (what worked well elsewhere?)

**Structured reverse mentoring:**
```markdown
## Reverse Mentoring Session (30 min, month 2)

Agenda:
1. New hire demos their "fresh eyes" findings (10 min)
2. Categorized: friction, confusion, delight (5 min)
3. Discussion: which findings warrant action? (10 min)
4. Assign follow-ups to platform/team (5 min)
```

## Step 24: API Developer Experience

### OpenAPI Best Practices

**Use `$ref` for DRY schemas:**
```yaml
# openapi.yaml
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
            application/problem+json:
              schema:
                $ref: '#/components/schemas/ProblemDetail'

components:
  schemas:
    User:
      type: object
      required: [id, email, createdAt]
      properties:
        id:
          type: string
          format: uuid
        email:
          type: string
          format: email
        createdAt:
          type: string
          format: date-time
        links:
          $ref: '#/components/schemas/Links'

    ProblemDetail:  # RFC 9457
      type: object
      required: [type, title, status]
      properties:
        type:
          type: string
          format: uri
          example: https://api.example.com/errors/not-found
        title:
          type: string
          example: Resource Not Found
        status:
          type: integer
          example: 404
       detail:
          type: string
          example: User with id 'abc' not found
        instance:
          type: string
          format: uri
```

**problem+json (RFC 9457):**
```
Use application/problem+json for all error responses.
Consistent error shape across all endpoints.
Clients parse errors uniformly — no guessing field names.

Key fields: type (URI to error docs), title (human summary),
  status (HTTP code), detail (specific instance info),
  instance (URI to this specific error occurrence).
```

**API versioning strategies:**
```
URL path:    /v1/users  — most visible, easy to route
Header:      Accept: application/vnd.myapi.v2+json — cleaner URLs
Query param: /users?version=2 — least preferred, easy to ignore

Recommendation: URL path versioning for public APIs.
  Major versions only (/v1, /v2). Minor changes are backward-compatible.
  Never break v1 — deprecate with Sunset header (RFC 8594).
```

**Pagination (cursor-based preferred):**
```yaml
# Cursor-based pagination schema
PaginationParams:
  type: object
  properties:
    cursor:
      type: string
      description: Opaque cursor from previous response
    limit:
      type: integer
      minimum: 1
      maximum: 100
      default: 20

PaginatedResponse:
  type: object
  properties:
    data:
      type: array
      items:
        $ref: '#/components/schemas/User'
    pagination:
      type: object
      properties:
        nextCursor:
          type: string
          nullable: true
        prevCursor:
          type: string
          nullable: true
        hasMore:
          type: boolean
```

```
Cursor vs offset pagination:
Cursor: stable under inserts/deletes, no skip performance issues.
  Best for infinite scroll, mobile, high-write APIs.
Offset: simpler, supports "go to page N". OK for admin UIs.
  Breaks if rows inserted/deleted between pages.
```

### SDK Generation

**OpenAPI Generator (open-source):**
```bash
# Install
npm install @openapitools/openapi-generator-cli -g

# Generate TypeScript SDK
openapi-generator-cli generate \
  -i openapi.yaml \
  -g typescript-fetch \
  -o ./sdk/typescript \
  --additional-properties=supportsES6=true,npmName=my-api-sdk

# Generate Python SDK
openapi-generator-cli generate \
  -i openapi.yaml \
  -g python \
  -o ./sdk/python \
  --package-name=my_api_sdk

# Generate Go client
openapi-generator-cli generate \
  -i openapi.yaml \
  -g go \
  -o ./sdk/go \
  --package-name=myapi
```

**Speakeasy (commercial, high-quality):**
```bash
# Install
brew install speakeasy-api/tap/speakeasy

# Generate SDK with retry logic, pagination helpers, type-safe models
speakeasy generate sdk \
  --schema openapi.yaml \
  --lang typescript \
  --out ./sdk/typescript

# Features: automatic retries, OAuth token refresh, pagination helpers,
# idiomatic code per language, GitHub Action for auto-regeneration
```

**Stainless (commercial, API-company focused):**
```bash
# Generates polished SDKs like OpenAI, Anthropic, Vercel use
# Define SDK config in stainless.yml
# Auto-generates on OpenAPI spec change via GitHub App
```

**SDK generation comparison:**
```
OpenAPI Generator:  Free, 50+ languages, decent quality.
  Requires manual cleanup. Good for internal APIs.

Speakeasy:          Commercial, 10+ languages, excellent quality.
  Retry/pagination built-in. Best for public APIs.

Stainless:          Commercial, premium quality, limited languages.
  Generates polished SDKs like OpenAI's. Best for API-first companies.
```

### Interactive API Documentation

**Swagger UI:**
```html
<!-- Embed Swagger UI -->
<div id="swagger-ui"></div>
<script src="https://unpkg.com/swagger-ui-dist/swagger-ui-bundle.js"></script>
<script>
  SwaggerUIBundle({
    url: '/openapi.yaml',
    dom_id: '#swagger-ui',
    presets: [SwaggerUIBundle.presets.apis, SwaggerUIBundle.SwaggerUIStandalonePreset],
    layout: 'StandaloneLayout',
    tryItOutEnabled: true,
  });
</script>
```

**Redoc:**
```html
<!-- Redoc: cleaner rendering, better for reference docs -->
<redoc spec-url="/openapi.yaml"></redoc>
<script src="https://cdn.redoc.ly/redoc/latest/bundled/redoc.standalone.js"></script>
```

**Stoplight Elements:**
```html
<!-- Stoplight: modern, three-panel layout -->
<script src="https://unpkg.com/@stoplight/elements/web-components.min.js"></script>
<link rel="stylesheet" href="https://unpkg.com/@stoplight/elements/styles.min.css">
<elements-api apiDescriptionUrl="/openapi.yaml" router="hash" layout="sidebar" />
```

**ReadMe (commercial):**
```
SaaS platform. Auto-generates docs from OpenAPI spec.
Adds: API explorer with auth, usage analytics, changelog,
developer dashboard, interactive tutorials. Best for public APIs.
```

**Tool comparison:**
```
Swagger UI:  Free, ubiquitous, try-it-out. Looks dated.
Redoc:       Free, clean three-panel layout. No try-it-out in OSS version.
Stoplight:   Free tier, modern UI, three-panel + try-it-out.
ReadMe:      Paid, SaaS, analytics + developer portal features.
```

## Step 25: Developer Experience (DevEx) Metrics

### SPACE Framework Applied

SPACE measures developer productivity across 5 dimensions. No single metric captures productivity — use a balanced set.

**S — Satisfaction & Well-Being:**
```
Metrics:
- Developer satisfaction score (1-10 survey, quarterly)
- eNPS (employee Net Promoter Score) for engineering org
- Burnout indicators (after-hours commits, PTO usage)
- Tool satisfaction rating (per tool: IDE, CI, deploy pipeline)

Target: satisfaction >= 7/10, eNPS >= 30, no sustained after-hours work
```

**P — Performance:**
```
Metrics:
- Change failure rate (% of deploys causing incidents)
- Mean time to recovery (MTTR) from deployment failures
- Code review coverage (% of PRs reviewed before merge)
- Automated test coverage (line + branch, trending)

Target: change failure rate < 15%, MTTR < 1h, review coverage 100%
```

**A — Activity:**
```
Metrics:
- Commits per developer per week (trend, not absolute)
- PRs opened/merged per developer per week
- Deploys per service per day/week
- Incidents resolved per developer per week

Target: activity trends stable or improving. Decline signals friction.
Activity metrics alone are misleading — pair with satisfaction/flow.
```

**C — Communication & Collaboration:**
```
Metrics:
- PR review turnaround time (time to first review)
- PR review depth (comments per PR, substantive vs nit)
- Cross-team contribution rate (% of PRs from outside team)
- Documentation freshness (age of last edit per doc)
- Knowledge silos index (bus factor per repo/service)

Target: review turnaround < 4h, cross-team contributions >= 10%
```

**E — Efficiency & Flow:**
```
Metrics:
- Lead time for changes (commit to production)
- Cycle time (PR open to merge)
- Deploy frequency (per service, per team)
- Wait time (% of time PRs spend waiting for review/CI/deploy)
- Context switches per day (IDE/app switches, meeting interruptions)

Target: lead time < 1 day, deploy frequency >= 1/day, wait time < 30%
```

### Developer Satisfaction Surveys

**Survey design (quarterly cadence):**
```
Frequency: quarterly (monthly is too frequent, causes survey fatigue)
             (semi-annually misses problems for too long)
Length: 5-10 minutes max (20-30 questions)
Format: Likert scale (1-5 or 1-7) + 2-3 open-ended questions
Anonymity: mandatory. Aggregate results only. No individual tracking.
```

**Core questions (adapt from DX company research):**
```
1. I can get my development environment set up in a reasonable time.
2. Our CI/CD pipeline is reliable and fast enough.
3. I can find the documentation I need when I need it.
4. Code review is timely and constructive.
5. I spend most of my time on value-creating work (not fighting tools).
6. I understand the architecture of the systems I work on.
7. I have the tools I need to do my job effectively.
8. Our deployment process is smooth and low-stress.
9. I feel productive in my current work environment.
10. Technical debt does not significantly slow me down.

Open-ended:
- What is the biggest friction point in your daily workflow?
- What tool or process improvement would have the most impact?
- What's working well that we should keep doing?
```

**DX company methodology:**
```
Framework by Abi Noda (DX) for measuring developer experience.
Core insight: DevEx = f(perception, workflows, environment).
Three dimensions: feedback loops, cognitive load, flow state.
Survey + quantitative data = complete picture.
Link: https://getdx.com
```

### Friction Logging

Systematic process for identifying and tracking developer friction points.

**Friction categories:**
```
1. Environment Setup    — dev env provisioning, dependency issues
2. Build & Compile      — slow builds, flaky compilation
3. Testing              — slow tests, flaky tests, hard to write tests
4. Code Review          — slow reviews, nitpicking, unclear standards
5. CI/CD Pipeline       — slow pipeline, opaque failures, flaky jobs
6. Deployment           — complex deploy process, rollback difficulty
7. Documentation        — missing, stale, hard to find docs
8. Tooling              — IDE issues, CLI friction, tool fragmentation
9. Access & Permissions — slow provisioning, too many systems
10. Knowledge Sharing   — siloed knowledge, missing runbooks
```

**Friction log template:**
```markdown
## Friction Log Entry

**Date:** 2025-01-15
**Category:** CI/CD Pipeline
**Reporter:** dev@example.com
**Frequency:** Daily
**Impact:** High (blocks work for 15+ min each occurrence)

### Description
CI pipeline takes 25 minutes for PR checks. Developers context-switch
while waiting, losing flow state.

### Current workaround
Run subset of tests locally before pushing. Skip full check.

### Suggested fix
Implement test splitting across parallel runners. Target: < 10 min.

### Evidence
- 50+ PRs/month affected
- Average wait: 25 min (p95: 40 min)
- Developer survey: CI speed rated 2.1/5
```

**Frequency x Impact matrix:**
```
              Low Impact          High Impact
High          Monitor             FIX FIRST
Frequency     (log for trends)    (invest this sprint)

Low           Ignore              INVESTIGATE
              (noise)             (may become high frequency)
```

### Flow Metrics

**Flow time:** Total elapsed time from work start to production deploy.
```
Flow time = ideation + development + review + testing + deployment

Breakdown (example healthy org):
  Ideation/requirements:  10% of flow time
  Development:            30% of flow time
  Code review:            15% of flow time
  Testing (CI):           15% of flow time
  Deployment:             10% of flow time
  Waiting/blocked:        20% of flow time  ← minimize this

Target: total flow time < 7 days for features, < 1 day for bugfixes
```

**Flow efficiency:**
```
Flow efficiency = active work time / total flow time

Formula:
  efficiency = (time_coding + time_reviewing + time_testing) / total_elapsed_time

Benchmarks:
  Elite:    > 40%
  Good:     25-40%
  Average:  15-25%
  Below:    < 15%

Improving efficiency: reduce wait states (PR review queue, CI queue,
  deploy approval gates, environment provisioning).
```

**Cognitive load:**
```
Types (from Skelton & Pais, "Team Topologies"):
  Intrinsic:     unavoidable complexity of the problem domain
  Extraneous:    unnecessary complexity from tools/processes/debt
  Germane:       productive learning, understanding the domain

Measuring cognitive load:
  Survey: "I can hold the relevant system context in my head" (1-5)
  Proxy metrics:
    - Number of repos a developer touches per week
    - Number of distinct services owned per team
    - Lines of config vs lines of product code
    - On-call pages per shift (alert fatigue)
    - Time spent in meetings vs coding

Target: team owns 2-3 services max. Cognitive load survey >= 3.5/5.
Red flags: developers can't explain system architecture, high bus factor,
  frequent "I don't know who owns that" moments.
```

**Combined flow dashboard metrics:**
```
| Metric              | Target        | Measure from          |
|---------------------|---------------|-----------------------|
| Lead time           | < 1 day       | Git commit → deploy   |
| Cycle time          | < 2 days      | PR open → merge       |
| Deploy frequency    | ≥ 1/day       | CI/CD pipeline logs   |
| Change failure rate | < 15%         | Deploy → incident     |
| MTTR                | < 1 hour      | Incident → resolution |
| Flow efficiency     | > 25%         | Time tracking / PRs   |
| Review turnaround   | < 4 hours     | PR first review time  |
| CI duration         | < 10 minutes  | Pipeline metrics      |
| Cognitive load      | ≥ 3.5/5       | Quarterly survey      |
| Satisfaction        | ≥ 7/10        | Quarterly survey      |
```