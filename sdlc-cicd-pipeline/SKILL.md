---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, trunk-based development, anti-patterns."
version: 2.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, ci-cd, github-actions, gitlab-ci, docker, devops, pipeline, gitops, dora, accelerate, trunk-based]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-deployment, github-pr-workflow]
---

# CI/CD Pipeline Design

Pipeline architecture, GitHub Actions, GitLab CI, Docker builds, caching, security scanning, GitOps, DORA metrics, trunk-based development, anti-patterns.

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
      fail-fast: false
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
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm test -- --shard=${{ matrix.shard }}/4

  build:
    needs: test
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/build-push-action@v5
        with:
          push: false
          tags: myapp:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ./deploy.sh
```

### Caching Best Practices
```yaml
# Cache key pattern: include lockfile hash for deterministic invalidation
- uses: actions/cache@v4
  with:
    path: |
      node_modules
      ~/.npm
    key: deps-${{ runner.os }}-${{ hashFiles('**/package-lock.json') }}
    restore-keys: deps-${{ runner.os }}-
```

**What to cache:** node_modules/, pip cache, go mod cache, cargo registry
**What NOT to cache:** build artifacts (use actions/upload-artifact)

### Reusable Workflows
```yaml
# .github/workflows/reusable-deploy.yml
name: Reusable Deploy
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
    secrets:
      DEPLOY_KEY:
        required: true

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: ${{ inputs.environment }}
    steps:
      - uses: actions/checkout@v4
      - run: ./deploy.sh
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
```

```yaml
# Caller workflow
jobs:
  deploy-staging:
    uses: ./.github/workflows/reusable-deploy.yml
    with:
      environment: staging
    secrets:
      DEPLOY_KEY: ${{ secrets.STAGING_DEPLOY_KEY }}
```

### Matrix Builds
```yaml
strategy:
  fail-fast: false  # Don't cancel other legs on failure
  matrix:
    os: [ubuntu-latest, macos-latest]
    node: [18, 20, 22]
    exclude:
      - os: macos-latest
        node: 18  # Skip known-bad combo
    include:
      - os: ubuntu-latest
        node: 22
        experimental: true  # Special case
```

## Step 3: GitLab CI

### Pipeline Architectures
- **Branch pipelines:** run on every push (default)
- **Merge request pipelines:** run only on MRs
- **Parent-child pipelines:** parent triggers child via `trigger:` keyword
- **Multi-project pipelines:** cross-repo triggers

### Key Patterns
```yaml
# Hidden jobs as templates (prefix with .)
.base_test:
  stage: test
  image: python:3.12
  script: [pytest]

unit_tests:
  extends: .base_test
  script: [pytest tests/unit]

integration_tests:
  extends: .base_test
  services:
    - postgres:16
  script: [pytest tests/integration]

# DAG with needs: for parallel execution
test:
  needs: [build]  # Only waits for build, not all prior stage jobs
  script: [pytest]

# Cache vs artifacts
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .npm/
  policy: pull-push  # or pull-only for non-build jobs

# Include external configs
include:
  - local: ci/build.yml
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml
```

### Rules (replaces only/except)
```yaml
deploy:
  script: [./deploy.sh]
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      when: never
```

## Step 4: Docker Multi-Stage Builds

```dockerfile
# Build stage
FROM node:20-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN --mount=type=cache,target=/root/.npm npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:20-slim AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
EXPOSE 3000
USER node
CMD ["node", "dist/server.js"]
```

### Container Security Scanning
```bash
# Trivy
trivy image myapp:latest

# Grype
grype myapp:latest

# Snyk
snyk container test myapp:latest
```

## Step 5: GitOps

### ArgoCD
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: myapp
spec:
  project: default
  source:
    repoURL: https://github.com/org/k8s-manifests
    targetRevision: main
    path: apps/myapp/overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

### Flux
```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: myapp
spec:
  interval: 1m
  url: https://github.com/org/k8s-manifests
  ref:
    branch: main
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: myapp
spec:
  interval: 5m
  path: ./apps/myapp/overlays/production
  prune: true
  sourceRef:
    kind: GitRepository
    name: myapp
```

## Step 6: Trunk-Based Development

Source: https://trunkbaseddevelopment.com/

### Core Principles
- All developers commit to one branch (main/trunk)
- Feature branches are SHORT: 1-2 days max, merged or deleted
- Use feature flags for incomplete work behind the trunk
- Release branches (if needed) are cut from trunk, never merge back

### Key Rules
- CI must pass on every commit to trunk. Broken trunk = top priority fix.
- Short-lived branches: < 1 day ideally, < 2 days max.
- No "code freeze" periods — trunk is always deployable.
- Branch by abstraction (feature flags, interfaces) instead of branching.

### Why
- Eliminates merge hell from long-lived branches
- Forces small, incremental changes = easier review, faster feedback
- DORA research correlates trunk-based with high deployment frequency

## Step 7: CI/CD Anti-Patterns

Source: https://dora.dev/capabilities/ (Accelerate research)

| Anti-Pattern | Why It's Bad | Fix |
|--------------|-------------|-----|
| Long-lived feature branches | Merge conflicts, stale code | Trunk-based + feature flags |
| Broken CI ignored | Erodes trust | Branch protection: green before merge |
| Manual approval gates everywhere | Humans become bottlenecks | Automated quality gates |
| Flaky tests | Pass/fail randomly erodes trust | Quarantine and fix immediately |
| No caching | Re-downloading deps every run | Cache with lockfile hash keys |
| CI does everything in one job | Can't parallelize, slow feedback | Split into focused jobs |
| Snowflake environments | Staging != production | IaC (Terraform/Pulumi) |
| Manual deployment | Error-prone, slow | Fully automated deploy pipelines |
| No rollback mechanism | Can't recover from bad deploy | Always have rollback plan |
| Secret sprawl | Credentials in code/CI systems | Centralize (Vault, cloud secrets) |
| Coupled build and deploy | Can't deploy artifact independently | Separate build from deploy stage |

## Step 8: DORA Metrics

Source: https://dora.dev/research/ (State of DevOps Reports)

### Four Key Metrics
1. **Lead Time for Changes** — commit to production (includes review time)
2. **Deployment Frequency** — how often code reaches production
3. **Change Failure Rate** — % of deployments causing failures
4. **Time to Restore Service** — recovery time from failures

### Performance Tiers (2023)
| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| Lead Time | < 1 hour | 1 day-1 week | 1 week-1 month | > 6 months |
| Deploy Frequency | On demand | Daily-weekly | Monthly | < 1/year |
| Change Failure Rate | 0-15% | 16-30% | 16-30% | 16-30% |
| Time to Restore | < 1 hour | < 1 day | < 1 week | > 6 months |

### Key DORA Capabilities
- Version control (trunk-based)
- Continuous integration
- Test automation
- Deployment automation
- Infrastructure as Code
- Progressive delivery (canary/blue-green)
- Observability (SLIs/SLOs)
- Cloud infrastructure

## Step 9: Security Scanning in CI

```yaml
# Semgrep — fast SAST
- run: semgrep --config=auto --severity=ERROR .

# Trivy — dependencies + secrets + IaC
- run: trivy fs --scanners vuln,secret,misconfig .

# CodeQL — deep semantic analysis (GitHub Advanced Security)
- uses: github/codeql-action/analyze@v3

# Dependency review
- uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: high
```

## Pitfalls

1. **Don't run everything in one job** — split lint, test, build, deploy
2. **Don't ignore flaky tests** — quarantine and fix immediately
3. **Don't skip caching** — lockfile hash key for deterministic invalidation
4. **Don't use long-lived branches** — trunk-based + feature flags
5. **Don't skip branch protection** — require green CI before merge
6. **Don't manually deploy** — automate fully
7. **Don't skip security scanning** — SAST + SCA + secrets in every pipeline
8. **Don't use `fail-fast: true`** — one failure shouldn't cancel other matrix legs
9. **Don't forget concurrency groups** — cancel stale runs on same PR
10. **Don't skip artifact signing** — Sigstore/cosign for container images
