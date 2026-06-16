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

---
name: sdlc-developer-tooling
description: "Modern dev tooling: Python (uv, Ruff, pytest, mypy), JS/TS (pnpm, Bun, Vitest, Biome, Playwright), Go (golangci-lint, go test -race), Rust (cargo). Cross-cutting: just, mise, direnv, Docker Compose, Dev Containers, Nix. Includes LSP/DAP patterns, AI-assisted dev, green software tooling, CI/CD advanced patterns, build caching, artifact management, monorepo tooling (Nx, Turborepo, Bazel, Pants), polyrepo patterns, repository structure, conventional co
## When to Use
Trigger when user:
- Sets up dev environment for a project
- Configures linters, formatters, type checkers
- Chooses package manager or runtime version
- Sets up Docker Compose for local dev
- Creates Dev Containers or justfile
- Measures developer experience
## Step 1: Python Tooling
Source: https://docs.astral.sh/uv/
Rust-based Python package/project manager. 10-100x faster than pip. Drop-in replacement for pip/pip-tools/pipx/poetry/pyenv.
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
uv init myproject && cd myproject
uv add fastapi uvicorn pytest ruff mypy pre-commit
uv sync
uv run pytest
```
## Step 2: JavaScript/TypeScript Tooling
Source: https://pnpm.io/
Fast, disk-efficient. Content-addressable store. Strict: no phantom deps.
```bash
pnpm create vite myapp --template react-ts
cd myapp
pnpm add -D vitest @vitest/coverage-v8 biome playwright
pnpm dlx biome init
pnpm dlx playwright install
```
## Step 3: Go Tooling
```bash
go test ./...                    # Run all tests
go test -cover ./...             # Coverage
go test -race ./...              # Race detector (ALWAYS in CI)
go test -bench=. ./...           # Benchmarks
go test -run TestFoo -v ./...    # Verbose, specific test
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
## Step 6: Docker Compose for Local Dev
```yaml
# compose.yaml
services:
  app:
    build: .
    ports: ["3000:3000"]
    environment:
      DATABASE_URL: postgres://postgres:***@db:5432/myapp
    volumes: [".:/app", "/app/node_modules"]
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres:16
    environment:
```
## Step 7: Dev Containers
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
```
## Step 8: Nix + devenv
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
```
## Step 9: LSP & DAP Patterns
| Language | Server | Install |
|----------|--------|---------|
| Python | pyright | `npm i -g pyright` |
| Go | gopls | `go install golang.org/x/tools/gopls@latest` |
| TypeScript | typescript-language-server | `npm i -g typescript-language-server` |
| Rust | rust-analyzer | `rustup component add rust-analyzer` |
| Language | Adapter | Install |
|----------|---------|---------|
| Python | debugpy | `pip install debugpy` |
| Go | delve | `go install github.com/go-delve/delve/cmd/dlv@latest` |
| TypeScript | js-debug | Built into VS Code |
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
  map('[d', vim.diagnostic.goto_prev, 'Prev diagnostic')
  map(']d', vim.diagnostic.goto_next, 'Next diagnostic')
end
```
## Step 10: Bun Runtime
Source: https://bun.sh/
All-in-one JS/TS runtime, bundler, test runner, package manager. Built on JavaScriptCore (Safari engine).
```bash
curl -fsSL https://bun.sh/install | bash
bun init                     # Scaffold project
bun install                  # Install deps (faster than npm/pnpm)
bun add express zod
bun run index.ts             # Run TS directly — no compile step
bun test                     # Built-in test runner (Jest-compatible API)
bun build ./src/index.ts --outdir ./dist --target bun  # Bundle
```
## Step 11: Git Worktrees
Work on multiple branches simultaneously without stashing.
```bash
git worktree add ../myproject-feature-x feature/x
git worktree add ../myproject-hotfix hotfix/critical
git worktree list
git worktree remove ../myproject-feature-x
git worktree prune
```
## Step 12: Documentation Tooling
| Tool | Language | Output | Strength |
|------|----------|--------|----------|
| MkDocs Material | Python | Static site | Beautiful, fast setup |
| Docusaurus | React | Static site | Versioning, i18n, blog |
| Starlight | Astro | Static site | Zero JS, fast builds |
| TypeDoc | TypeScript | HTML/MD | API reference from TS |
| Sphinx | Python | HTML/PDF | Autodoc, enterprise |
```yaml
# mkdocs.yml
site_name: My Project
theme:
  name: material
  features:
    - content.code.copy
    - navigation.tabs
    - search.suggest
plugins: [search, tags, git-revision-date-localized]
markdown_extensions:
  - pymdownx.superfences
  - pymdownx.tabbed:
      alternate_style: true
  - admonition
```
## Step 13: Observability for Developers
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
## Step 14: Testcontainers
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
## Step 15: AI-Assisted Development Tools
| Feature | GitHub Copilot | Cursor | Sourcegraph Cody | Codeium / Windsurf |
|---------|---------------|--------|------------------|---------------------|
| **Type** | Extension | Standalone editor | Extension | Extension + standalone |
| **Codebase indexing** | `@workspace` in chat | Automatic | Deep indexing | Workspace-aware |
| **Agentic mode** | Copilot Workspace (preview) | Composer (multi-file edits) | Limited | Cascade (multi-step) |
| **IDE support** | VS Code, JetBrains, Neovim | Own editor only | VS Code, JetBrains | VS Code, JetBrains, own |
| **Self-hosted** | No | No | Yes (Enterprise) | No |
| **Pricing** | $10/mo individual | $20/mo Pro | Free tier, $9/mo Pro | Free tier, $10/mo Pro |
| **Strengths** | Broadest IDE support, CLI | Best multi-file editing | Best codebase search | Free tier, agentic Cascade |
| **Weaknesses** | Less context-aware | Editor lock-in | Weaker completions | Newer ecosystem |
**Selection guide:**
- VS Code + GitHub → **Copilot** (lowest friction)
- Best AI-native editor → **Cursor** (Composer, multi-file edits)
- Enterprise, self-hosted → **Cody**
- Budget-constrained / agentic → **Windsurf**
**Role Assignment:**
```
You are a senior Python developer specializing in FastAPI and async programming.
Write code following PEP 8, using type hints, and preferring composition over inheritance.
```
## Step 16: Green Software Tooling
```yaml
# .github/workflows/carbon-aware.yml
name: Carbon-Aware Tests
on:
  schedule:
    - cron: "0 2 * * *"  # Fallback: 2am (typically low carbon)
```
## Step 17: Platform Engineering
```
CRAWL:
  - Centralized docs (wiki/confluence)
  - Shared CI templates (GitHub Actions reusable workflows)
  - Manual provisioning with runbooks
  - Onboarding time: 2-4 weeks

WALK:
  - Self-service portal (Backstage/Port)
  - Golden paths: opinionated starter templates
  - Automated environment provisioning
  - Onboarding time: 2-3 days

RUN:
  - Score/Humanitec workload spec for env-agnostic deploys
  - Platform APIs (create service, provision DB, rotate secrets)
```
## Step 18: GitOps
```
1. Declarative: Desired state described declaratively (YAML/HCL)
2. Versioned & Immutable: State stored in Git (single source of truth)
3. Pulled Automatically: Agents pull desired state, not pushed to
4. Continuously Reconciled: Agent ensures actual = desired state
```
## Step 19: Trunk-Based Development
Source: https://trunkbaseddevelopment.com/
```
- All work happens on main/trunk (or very-lived branches)
- Branch lifetime: < 1 day (ideally hours)
- No long-lived release branches
- Integration happens continuously, not at end of sprint
- Rebase over merge commits for clean history
```
## Step 20: Conventional Commits & Semver
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
## Step 21: Release Automation
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
## Step 22: Monorepo vs Polyrepo
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
## Step 23: Monorepo Tooling Deep Dive
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
## Step 24: Monorepo Pitfalls & Solutions
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
pnpm why lodash
pnpm dedupe

# Enforce version alignment
```
## Step 25: Polyrepo Patterns
```bash
# npm: publish to private registry
# .npmrc
@myorg:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${NPM_TOKEN}

# Tag + publish workflow
git tag v2.3.1
git push origin v2.3.1
# GitHub Action: on tag push → npm publish
```
## Step 26: Monorepo Directory Structure
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
```
## Step 27: GitHub Actions Advanced
```yaml
# .github/workflows/reusable-deploy.yml
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
```
## Step 28: GitLab CI Advanced
```yaml
generate:
  stage: build
  script: python generate_pipeline.py > child-pipeline.yml
  artifacts:
    paths: [child-pipeline.yml]

child:
  stage: test
  trigger:
    include:
      - artifact: child-pipeline.yml
        job: generate
    strategy: depend  # parent waits for child
```
## Step 29: Build Caching Deep Dive
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
## Step 30: Artifact Management
```bash
# Push arbitrary file as OCI artifact
oras push ghcr.io/org/artifacts:v1.0 \
  --artifact-type application/vnd.example.config.v1 \
  config.json:application/json

# Pull
oras pull ghcr.io/org/artifacts:v1.0

# Copy between registries
oras copy ghcr.io/org/app:v1 ghcr.io/backup/app:v1
```
## Step 31: Code Review Best Practices
- Review speed: ~400 LOC/hour (diminishing returns after 60 min)
- Optimal review size: < 400 LOC
- Response time: < 1 hour for initial response
- Multiple small reviews > one large review
```
nit:        Style/formatting. Non-blocking.
suggestion: Alternative approach. Optional.
question:   Clarification only.
issue:      Bug or correctness problem. Must fix.
todo:       Follow-up. Can be deferred.
praise:     Positive feedback. Use generously.
thought:    Thinking out loud. Neither block nor unblock.
```
## Step 32: Git Internals
```
blob:    File content (bytes, no filename)
tree:    Directory listing (maps filenames to blobs/trees)
commit:  Snapshot pointer (tree + parent + author + message)
tag:     Annotated tag (points to commit + tagger + message)
```
## Step 33: Developer Onboarding
```
Elite:    < 1 day    — pre-configured dev env, clear docs, good first issues
Good:     1-3 days   — setup script exists, docs current, buddy assigned
Average:  3-7 days   — manual setup, docs partially outdated
Below:    > 7 days   — broken setup, missing docs, no process
```
## Step 34: API Developer Experience
```yaml
# Use $ref for DRY schemas
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
## Step 35: Developer Experience Metrics
| Dimension | Key Metrics | Target |
|-----------|-------------|--------|
| **S**atisfaction | Dev satisfaction score (1-10), eNPS, burnout indicators | ≥ 7/10, eNPS ≥ 30 |
| **P**erformance | Change failure rate, MTTR, code review coverage | Failure < 15%, MTTR < 1h |
| **A**ctivity | Commits/week, PRs/week, deploys/service/day | Stable or improving trends |
| **C**ommunication | PR review turnaround, cross-team contributions, bus factor | Review < 4h, cross-team ≥ 10% |
| **E**fficiency | Lead time, cycle time, deploy frequency, wait time | Lead < 1 day, deploy ≥ 1/day |
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
```
1. Environment Setup    6. Deployment
2. Build & Compile      7. Documentation
3. Testing              8. Tooling
4. Code Review          9. Access & Permissions
5. CI/CD Pipeline      10. Knowledge Sharing
```
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