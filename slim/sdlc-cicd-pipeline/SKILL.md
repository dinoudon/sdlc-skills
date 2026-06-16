---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, trunk-based development, supply chain security (SLSA/Sigstore/SBOM), serverless CI/CD, preview environments, multi-platform builds, progressive delivery, database CI/CD, pipeline governance."
version: 4.8.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, ci-cd, github-actions, gitlab-ci, docker, devops, pipeline, gitops, dora, slsa, sbom, supply-chain, security-hardening, serverless, preview-environments, progressive-delivery, database-cicd]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-deployment, github-pr-workflow]
---

---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, trunk-based development, supply chain security (SLSA/Sigstore/SBOM), serverless CI/CD, preview environments, multi-platform builds, progressive delivery, database CI/CD, pipeline governance."
version: 4.8.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  
## When to Use
Trigger when user:
- Designs or debugs CI/CD pipelines
- Sets up GitHub Actions or GitLab CI
- Creates Dockerfile or container builds
- Configures caching, matrix builds, test sharding
- Implements GitOps (ArgoCD, Flux)
- Measures DORA metrics
## Step 1: Pipeline Architecture
```
lint → build → unit-test → integration-test → security-scan →
package → deploy-staging → e2e-test → deploy-production
```
## Step 2: GitHub Actions
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
```
## Step 3: GitLab CI
- **Branch pipelines:** run on every push (default)
- **Merge request pipelines:** run only on MRs
- **Parent-child pipelines:** parent triggers child via `trigger:` keyword
- **Multi-project pipelines:** cross-repo triggers
```yaml
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

```
## Step 4: Docker Multi-Stage Builds
```dockerfile
FROM node:20-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN --mount=type=cache,target=/root/.npm npm ci
COPY . .
RUN npm run build

FROM node:20-slim AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package*.json ./
EXPOSE 3000
USER node
```
## Step 5: GitOps
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
```
## Step 6: Trunk-Based Development
Source: https://trunkbaseddevelopment.com/
- All developers commit to one branch (main/trunk)
- Feature branches are SHORT: 1-2 days max, merged or deleted
- Use feature flags for incomplete work behind the trunk
- Release branches (if needed) are cut from trunk, never merge back
- CI must pass on every commit to trunk. Broken trunk = top priority fix.
- Short-lived branches: < 1 day ideally, < 2 days max.
- No "code freeze" periods — trunk is always deployable.
- Branch by abstraction (feature flags, interfaces) instead of branching.
## Step 7: CI/CD Anti-Patterns
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
## Step 8: DORA Metrics
Source: https://dora.dev/research/
1. **Lead Time for Changes** — commit to production
2. **Deployment Frequency** — how often code reaches production
3. **Change Failure Rate** — % of deployments causing failures
4. **Time to Restore Service** — recovery time from failures
| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| Lead Time | < 1 hour | 1 day-1 week | 1 week-1 month | > 6 months |
| Deploy Frequency | On demand | Daily-weekly | Monthly | < 1/year |
| Change Failure Rate | 0-15% | 16-30% | 16-30% | 16-30% |
| Time to Restore | < 1 hour | < 1 day | < 1 week | > 6 months |
## Step 9: Security Scanning in CI
```yaml
- run: semgrep --config=auto --severity=ERROR .
- run: trivy fs --scanners vuln,secret,misconfig .
- uses: github/codeql-action/analyze@v3
- uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: high
```
## Step 10: Supply Chain Security
Keyless artifact signing. No long-lived keys.
```yaml
- uses: sigstore/cosign-installer@v3
- run: cosign sign --yes ${{ env.REGISTRY }}/${{ env.IMAGE }}@${{ steps.build.outputs.digest }}
  env:
    COSIGN_EXPERIMENTAL: "1"
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
## Step 11: DORA 2024 — AI in CI/CD
Source: https://dora.dev/research/2024-dora-report/
**Key finding:** AI that augments developers improves throughput. AI that replaces review gates without human oversight increases change failure rate.
```yaml
docs:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - run: pip install mkdocs-material
    - run: mkdocs build --strict
    - uses: actions/upload-pages-artifact@v3
      with:
        path: site/
```
## Step 12: Green CI/CD Patterns
**Green CI checklist:**
- [ ] Cancel stale workflow runs (concurrency groups)
- [ ] Cache aggressively (deps, Docker layers, test fixtures)
- [ ] Use `paths:` filters — skip CI for docs-only changes
- [ ] Avoid macOS runners when Linux suffices (10x energy cost)
- [ ] Schedule nightly heavy jobs during green grid windows
## Step 13: Test Sharding
```yaml
test:
  strategy:
    matrix:
      shard: [1/4, 2/4, 3/4, 4/4]
  steps:
    - run: npx playwright test --shard=${{ matrix.shard }}
```
## Step 14: GitHub Actions Security Hardening
```yaml
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11  # v4.1.1
```
## Step 15: Serverless CI/CD
```yaml
- uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsRole
    aws-region: us-east-1
- uses: aws-actions/setup-sam@v2
- run: sam build --cached --parallel
- run: sam deploy --stack-name myapp --resolve-s3 --capabilities CAPABILITY_IAM
```
## Step 16: Preview Environments
```yaml
name: Preview Environment
on:
  pull_request:
    types: [opened, synchronize, reopened]

concurrency:
  group: preview-${{ github.event.pull_request.number }}
  cancel-in-progress: true

jobs:
  deploy-preview:
    runs-on: ubuntu-latest
    environment:
      name: pr-${{ github.event.pull_request.number }}
      url: https://pr-${{ github.event.pull_request.number }}.preview.example.com
```
## Step 17: AI in CI/CD
```yaml
- uses: coderabbitai/ai-pr-reviewer@v1
  with:
    review-type: auto
    path-instructions: |
      src/api/**: Security and input validation focus
      src/db/**: SQL injection and performance focus
```
## Step 18: Multi-Platform Builds
```yaml
- uses: docker/setup-qemu-action@v3
  with:
    platforms: linux/amd64,linux/arm64
- uses: docker/setup-buildx-action@v3
- uses: docker/build-push-action@v5
  with:
    push: true
    platforms: linux/amd64,linux/arm64
    tags: ghcr.io/org/myapp:latest
    cache-from: type=gha
    cache-to: type=gha,mode=max
```
## Step 19: Advanced Dependency Caching
**Go:** `actions/setup-go@v5` with `cache: true`
**Rust:** `Swatinem/rust-cache@v2`
**Python:** `actions/setup-python@v5` with `cache: 'pip'` or uv cache
**JVM:** `actions/setup-java@v4` with `cache: 'gradle'` or `'maven'`
**Guidelines:**
- Lockfile hash in key for deterministic invalidation
- Always set `restore-keys` for partial cache hits
- Don't cache `node_modules` in npm — use `~/.npm` + `npm ci`
- Monitor cache size — GitHub limits 10GB per repo
```yaml
- uses: pnpm/action-setup@v4
  with:
    version: 9
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'pnpm'  # Auto-caches pnpm store
```
## Step 20: Pipeline Security Hardening
```yaml
# AWS
- uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsRole
    aws-region: us-east-1

# GCP
- uses: google-github-actions/auth@v2
  with:
    workload_identity_provider: projects/123/providers/github
    service-account: deploy@project.iam.gserviceaccount.com

# Azure
- uses: azure/login@v2
  with:
```
## Step 21: Build Reproducibility
```dockerfile
FROM node:20.11.0-slim@sha256:abc123... AS builder
COPY package.json package-lock.json ./
RUN npm ci --ignore-scripts
ENV SOURCE_DATE_EPOCH=1
RUN npm run build
RUN find dist -exec touch -t 197001010000.00 {} +
```
## Step 23: Monorepo CI Patterns
**Git-based affected detection (manual):**
```yaml
- name: Detect affected packages
  id: affected
  run: |
    CHANGED=$(git diff --name-only origin/main...HEAD)
    PACKAGES=$(echo "$CHANGED" | cut -d/ -f1-2 | sort -u)
    echo "packages=$PACKAGES" >> $GITHUB_OUTPUT

- name: Build affected only
  run: |
    for pkg in ${{ steps.affected.outputs.packages }}; do
      if [ -f "$pkg/package.json" ]; then
        cd "$pkg" && npm ci && npm run build && cd -
      fi
    done
```
## Step 24: Pipeline Cost Optimization
```yaml
# Save cache only on main (PRs restore only)
- uses: actions/cache/save@v4
  if: github.ref == 'refs/heads/main'
  with:
    path: node_modules
    key: deps-${{ runner.os }}-${{ hashFiles('**/package-lock.json') }}

- uses: actions/cache/restore@v4
  with:
    path: node_modules
    key: deps-${{ runner.os }}-${{ hashFiles('**/package-lock.json') }}
    restore-keys: deps-${{ runner.os }}-

# Conditional cache restore (skip expensive step on hit)
- id: cache
```
## Step 25: Pipeline Governance
```yaml
- run: conftest test --policy policies/ manifests/
- run: kyverno apply policies/ --resource manifests/
```
## Step 26: Deployment at Scale
| Feature | Spinnaker | Argo Rollouts | Flagger |
|---------|-----------|---------------|---------|
| Scope | Full CD platform | K8s-native controller | Progressive delivery |
| Overhead | Heavy | Lightweight | Lightweight |
| Best For | Large orgs, multi-cloud | ArgoCD teams | Metric-driven promotion |
```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: myapp
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  progressDeadlineSeconds: 600
  service:
    port: 80
    targetPort: 8080
    gateways:
      - public-gateway.istio-system.svc.cluster.local
```
## Step 27: Multi-Environment Management
```yaml
# base/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - deployment.yaml
  - service.yaml
commonLabels:
  app: myapp

# overlays/dev/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
namePrefix: dev-
```
## Step 28: Database CI/CD
```yaml
services:
  postgres:
    image: postgres:16
    ports: ['5432:5432']
    options: --health-cmd pg_isready
steps:
  - run: npx prisma migrate deploy
  - run: npx prisma db seed
  - run: npm test -- --testPathPattern=integration
```
## Step 29: Progressive Delivery
```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: success-rate
spec:
  metrics:
    - name: success-rate
      interval: 1m
      count: 5
      successCondition: result[0] >= 0.99
      failureLimit: 2
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
```
## Step 30: SLSA Detailed
```yaml
provenance:
  needs: build
  permissions:
    actions: read
    id-token: write
    packages: write
  uses: slsa-framework/slsa-github-generator/.github/workflows/generator_generic_slsa3.yml@v2.1.0
  with:
    base64-subjects: "${{ needs.build.outputs.hashes }}"
    provenance-name: "myapp.intoto.jsonl"
    upload-assets: true
```
## Step 31: Sigstore
```
OIDC Provider → Fulcio (CA) → Cosign (Sign) → Rekor (Transparency Log)
```
## Step 32: Dependency Security
| Feature | Dependabot | Renovate | Socket.dev | Snyk |
|---------|-----------|----------|------------|------|
| Platforms | GitHub only | GitHub/GitLab/Bitbucket | GitHub App | Multi-platform |
| Package managers | 15 | 80+ | npm/pip/Go/Maven | 10+ + containers |
| Malicious detection | ❌ | ❌ | ✅ | Partial |
| SAST | ❌ | ❌ | ❌ | ✅ |
**Recommended:** Renovate (updates) + Socket.dev (malicious detection) + Snyk (SCA+SAST)
```jsonc
// renovate.json
{
  "extends": ["config:recommended", ":semanticCommits"],
  "packageRules": [
    {
      "matchUpdateTypes": ["patch"],
      "automerge": true,
      "automergeType": "pr",
      "automergeStrategy": "squash"
    },
    {
      "matchDepTypes": ["devDependencies"],
      "matchUpdateTypes": ["minor", "patch"],
      "groupName": "dev dependencies (non-major)",
      "automerge": true
```
## Step 33: GitHub Actions Hardening (Complete)
```bash
pinact run .github/workflows/*.yml
```
## Related Skills
  - [sdlc-platform-engineering](sdlc-platform-engineering): Platform engineering: internal developer portals (IDP), Backstage, golden paths, service catalog, se
  - [sdlc-deployment](sdlc-deployment): Deployment strategies: canary, blue-green, rolling, progressive delivery, feature flags, rollback, d
  - [sdlc-testing-qa](sdlc-testing-qa): Test pyramid (unit/integration/e2e), TDD/BDD, property-based testing, mutation testing, contract tes