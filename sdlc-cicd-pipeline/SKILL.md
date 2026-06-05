---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, WIP limits."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, ci-cd, github-actions, gitlab-ci, docker, devops, pipeline, gitops, dora, accelerate]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-deployment, github-pr-workflow]
---

# CI/CD Pipeline Design

Pipeline architecture, GitHub Actions, GitLab CI, Docker builds, caching, security scanning, GitOps, DORA metrics, WIP limits. Includes Accelerate and Phoenix Project patterns.

## When to Use

Trigger when user:
- Designs or debugs CI/CD pipelines
- Sets up GitHub Actions or GitLab CI
- Creates Dockerfile or container builds
- Configures caching, matrix builds, test sharding
- Implements GitOps (ArgoCD, Flux)
- Measures DORA metrics

## Step 1: Pipeline Architecture

### Standard Stages
```
lint → build → unit-test → integration-test → security-scan →
package → deploy-staging → e2e-test → deploy-production
```

### Principles
- **Cache aggressively** — actions/cache, Docker --mount=type=cache
- **Parallelize** — matrix builds, test sharding
- **Fast feedback** — lint first (< 30s), unit tests (< 2min)
- **Ephemeral environments** — preview deploys per PR
- **Artifact signing** — Sigstore/cosign for container images

## Step 2: GitHub Actions

### Full Pipeline Template
```yaml
name: CI/CD Pipeline
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run typecheck

  test:
    needs: lint
    strategy:
      matrix:
        shard: [1, 2, 3, 4]
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_PASSWORD: test
        ports: ['5432:5432']
        options: >-
          --health-cmd pg_isready
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm test -- --shard=${{ matrix.shard }}/4

  security:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          severity: 'HIGH,CRITICAL'

  deploy:
    if: github.ref == 'refs/heads/main'
    needs: [test, security]
    environment: production
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t app:${{ github.sha }} .
      - run: argocd app sync myapp
```

### Caching
```yaml
# Node.js (built-in)
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'

# Python
- uses: actions/cache@v4
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}

# Docker layer
- uses: docker/build-push-action@v5
  with:
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

### Matrix Builds
```yaml
strategy:
  fail-fast: false
  matrix:
    os: [ubuntu-latest, macos-latest]
    node: [18, 20, 22]
```

## Step 3: GitLab CI

```yaml
stages: [lint, test, build, deploy]

lint:
  stage: lint
  image: node:20
  script: npm ci && npm run lint

test:
  stage: test
  services: [postgres:16]
  script: npm ci && npm test
  coverage: '/All files[^|]*\|[^|]*\s+([\d.]+)/'

build:
  stage: build
  image: docker:24
  services: [docker:24-dind]
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
```

## Step 4: Docker Multi-Stage Builds

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci --production=false
COPY . .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
RUN addgroup -g 1001 -S nodejs && adduser -S nextjs -u 1001
COPY --from=build --chown=nextjs:nodejs /app/dist ./dist
COPY --from=build --chown=nextjs:nodejs /app/node_modules ./node_modules
COPY package*.json ./
USER nextjs
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/main.js"]
```

### Dockerfile Best Practices
- Multi-stage: build in first stage, copy to minimal runtime
- `.dockerignore`: exclude `node_modules`, `.git`, `tests/`
- Non-root `USER` for security
- Pin base image versions

## Step 5: GitOps

### ArgoCD Application
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
  namespace: argocd
spec:
  source:
    repoURL: https://github.com/org/k8s-manifests.git
    targetRevision: main
    path: apps/myapp/overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: myapp
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### ArgoCD CLI
```bash
argocd app sync myapp
argocd app rollback myapp REVISION
```

## Step 6: Infrastructure as Code

### Terraform + Terragrunt
```bash
terragrunt plan --terragrunt-source /modules//vpc
terragrunt apply
```

### Tools
| Tool | Language | Best For |
|------|----------|----------|
| Terraform | HCL | Multi-cloud |
| Pulumi | TS/Python/Go | Code-native |
| CDK | TypeScript | AWS-specific |
| OpenTofu | HCL | Open-source fork |

## Step 7: CI Gates (from earp-kit)

### Gate Structure
```
lint        → < 30s — fast feedback
build       → < 2min — compilation
unit-test   → < 2min — core logic
integration → < 5min — boundaries
security    → < 3min — SAST + secrets
e2e         → < 10min — critical paths (optional)
```

### Gate Rules
- **Gate tests** (CI default, blocks merge) — safety, functional
- **Periodic tests** (weekly cron) — benchmarks, non-deterministic
- Each test declares file dependencies for diff-based selection

## Step 8: DORA Metrics (from Accelerate)

### Four Key Metrics
| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| Deployment Frequency | On-demand | Daily-weekly | Weekly-monthly | Monthly-never |
| Lead Time for Changes | < 1 hour | 1 day-1 week | 1 week-1 month | > 1 month |
| Change Failure Rate | < 5% | 5-10% | 10-15% | 15-30% |
| Time to Restore | < 1 hour | < 1 day | < 1 week | > 1 week |

### Key Insight
**Throughput and stability are NOT a tradeoff.** Elite performers have BOTH high deployment frequency AND low change failure rate.

### DORA Capabilities
1. Trunk-based development
2. Test automation
3. Deployment automation
4. Infrastructure as code
5. Shift left on security
6. Monitoring and observability
7. Lightweight change approval

## Step 9: WIP Limits (from Phoenix Project)

### Theory of Constraints
1. **Visualize** all work (Kanban board)
2. **Limit WIP** at every stage
3. **Find bottleneck** — everything else subordinated
4. **Automate** the bottleneck away
5. **Repeat** — find next bottleneck

### WIP Limits
```
In Progress: 2-3 per developer (strict)
In Review: 1-2 per reviewer
Deploying: limited by pipeline capacity
```

### Four Types of Work
1. **Business projects** — new features
2. **Internal projects** — tech debt, tooling
3. **Changes** — operational changes
4. **Unplanned work** — incidents (the silent killer)

Track unplanned work ratio. If > 30%, systemic problem.

## Pitfalls

1. **Don't use `@main` for actions** — pin to `@v4`
2. **Don't cache without lockfile hash** — stale cache = phantom bugs
3. **Don't deploy without health checks**
4. **Don't skip `concurrency` in GitHub Actions**
5. **Don't store secrets in CI config** — use vault
6. **Don't automate deploy without rollback plan**
7. **Don't ignore DORA metrics** — they predict org performance
8. **Don't overload teams with WIP** — limit work in progress
