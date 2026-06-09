---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, trunk-based development, supply chain security (SLSA/Sigstore/SBOM), serverless CI/CD, preview environments, multi-platform builds, progressive delivery, database CI/CD, pipeline governance."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, ci-cd, github-actions, gitlab-ci, docker, devops, pipeline, gitops, dora, slsa, sbom, supply-chain, security-hardening, serverless, preview-environments, progressive-delivery, database-cicd]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-deployment, github-pr-workflow]
---

# CI/CD Pipeline Design

Pipeline architecture, GitHub Actions, GitLab CI, Docker builds, caching, security scanning, GitOps, DORA metrics, trunk-based development, supply chain, progressive delivery.

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
      - run: npm run lint && npm run typecheck

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
        options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5
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

### Caching
```yaml
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

### Matrix Builds
```yaml
strategy:
  fail-fast: false
  matrix:
    os: [ubuntu-latest, macos-latest]
    node: [18, 20, 22]
    exclude:
      - os: macos-latest
        node: 18
    include:
      - os: ubuntu-latest
        node: 22
        experimental: true
```

## Step 3: GitLab CI

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

cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths: [node_modules/, .npm/]
  policy: pull-push

include:
  - local: ci/build.yml
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml

# Parent-child pipeline
generate-child:
  stage: deploy
  trigger:
    include: child-pipeline.yml
    strategy: depend

deploy:
  script: [./deploy.sh]
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      when: never
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
CMD ["node", "dist/server.js"]
```

**Container scanning:** `trivy image myapp:latest` / `grype myapp:latest` / `snyk container test myapp:latest`

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

### Core Principles
- All developers commit to one branch (main/trunk)
- Feature branches SHORT: 1-2 days max, merged or deleted
- Use feature flags for incomplete work behind the trunk
- Release branches cut from trunk, never merge back

### Key Rules
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

### Four Key Metrics
1. **Lead Time for Changes** — commit to production
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

### Sigstore / Cosign — Artifact Signing
```yaml
- uses: sigstore/cosign-installer@v3
- run: cosign sign --yes ${{ env.REGISTRY }}/${{ env.IMAGE }}@${{ steps.build.outputs.digest }}
  env:
    COSIGN_EXPERIMENTAL: "1"
```

### SLSA Framework
| Level | Guarantees |
|-------|-----------|
| L0 | No guarantees |
| L1 | Provenance exists |
| L2 | Hosted build platform, provenance is signed |
| L3 | Hardened build platform, non-falsifiable provenance |

### SBOM Generation
```yaml
- uses: anchore/sbom-action@v0
  with:
    image: ${{ env.IMAGE }}@${{ steps.build.outputs.digest }}
    format: spdx-json
    output-file: sbom.spdx.json
```

### Secret Management in CI
```yaml
- uses: hashicorp/vault-action@v3
  with:
    url: https://vault.example.com
    method: jwt
    role: ci-deployer
    secrets: |
      secret/data/ci token | CI_TOKEN
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

## Step 11: Test Sharding

```yaml
# Playwright
- run: npx playwright test --shard=${{ matrix.shard }}

# Vitest / Jest
- run: npx vitest --shard=${{ matrix.shard }}/4
- run: npx jest --shard=${{ matrix.shard }}/4 --coverage

# pytest-xdist
- run: pytest -n auto --dist loadgroup --timeout=120
```

**Anti-patterns:** Time-based and alphabetical sharding are uneven. Use file-hash or count-based.

## Step 12: GitHub Actions Security Hardening

### Pin Actions to Full SHA
```yaml
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11  # v4.1.1
```

```bash
go install github.com/suzuki-shunsuke/pinact/cmd/pinact@latest
pinact run
```

### Minimal Permissions
```yaml
permissions:
  contents: read

jobs:
  deploy:
    permissions:
      id-token: write
      contents: read
```

**Supply chain hardening checklist:**
- [ ] All actions pinned to full commit SHA
- [ ] `permissions:` set at workflow level (read-only default)
- [ ] CODEOWNERS protects workflow files
- [ ] Branch protection: require PR reviews for workflow changes

## Step 13: Serverless CI/CD

### AWS SAM
```yaml
- uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsRole
    aws-region: us-east-1
- uses: aws-actions/setup-sam@v2
- run: sam build --cached --parallel
- run: sam deploy --stack-name myapp --resolve-s3 --capabilities CAPABILITY_IAM
```

### CDK Pipeline
```typescript
const pipeline = new CodePipeline(this, 'Pipeline', {
  pipelineName: 'MyAppPipeline',
  synth: new ShellStep('Synth', {
    input: CodePipelineSource.gitHub('org/repo', 'main'),
    commands: ['npm ci', 'npx cdk synth'],
  }),
});
```

### Serverless Framework
```yaml
- uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    aws-region: us-east-1
- run: npx serverless deploy --stage production
```

## Step 14: Preview Environments

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
    steps:
      - uses: actions/checkout@v4
      - run: |
          STACK="myapp-pr-${{ github.event.pull_request.number }}"
          sam deploy --stack-name "$STACK" --resolve-s3 --capabilities CAPABILITY_IAM

  destroy-preview:
    if: github.event.action == 'closed'
    runs-on: ubuntu-latest
    steps:
      - run: aws cloudformation delete-stack --stack-name "myapp-pr-${{ github.event.pull_request.number }}"
```

**Best practices:** concurrency groups, comment URL on PR, auto-destroy on close, TTL 72h max, monitor costs.

## Step 15: Multi-Platform Builds

### Docker Buildx Multi-Platform
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

### Go Multi-Platform (No QEMU)
```dockerfile
FROM --platform=$BUILDPLATFORM golang:1.22 AS builder
ARG TARGETOS TARGETARCH
WORKDIR /app
COPY go.* ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -o /app/server .

FROM gcr.io/distroless/static:nonroot
COPY --from=builder /app/server /server
CMD ["/server"]
```

**Multi-platform checklist:**
- [ ] Use `$BUILDPLATFORM` for build stage (native speed)
- [ ] Go: `CGO_ENABLED=0 GOOS/$GOARCH` — no QEMU needed
- [ ] Rust: cross-compile with `--target`
- [ ] Node/Python: QEMU required unless native ARM runners

## Step 16: Dependency Caching

**Go:** `actions/setup-go@v5` with `cache: true`
**Rust:** `Swatinem/rust-cache@v2`
**Python:** `actions/setup-python@v5` with `cache: 'pip'`
**JVM:** `actions/setup-java@v4` with `cache: 'gradle'`

### Composite Cache Pattern (Monorepo)
```yaml
- uses: actions/cache@v4
  with:
    path: |
      ~/go/pkg/mod
      ~/.cache/go-build
      node_modules
      ~/.npm
      ~/.cache/pip
    key: all-deps-${{ runner.os }}-${{ hashFiles('go.sum', 'package-lock.json', 'requirements.txt') }}
    restore-keys: all-deps-${{ runner.os }}-
```

**Guidelines:** lockfile hash in key, set `restore-keys`, don't cache `node_modules` in npm (use `~/.npm` + `npm ci`), GitHub 10GB limit per repo.

## Step 17: OIDC for Cloud Auth & Security Hardening

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
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
```

**OIDC hardening:** one IAM role per repo, condition on branch/environment, short session duration (15-60 min), separate read-only CI vs write deploy roles.

**Secrets hygiene:**
- [ ] Environment-scoped secrets (not repo-wide)
- [ ] `pull_request` trigger never receives production secrets
- [ ] Rotate all CI secrets quarterly minimum
- [ ] Scan for leaked secrets in every pipeline

### Audit Logging
```yaml
- name: Log pipeline event
  if: always()
  run: |
    curl -X POST "${{ secrets.SIEM_WEBHOOK }}" -H "Content-Type: application/json" -d "{
      \"event\": \"workflow_run\",
      \"repo\": \"${{ github.repository }}\",
      \"workflow\": \"${{ github.workflow }}\",
      \"run_id\": \"${{ github.run_id }}\",
      \"actor\": \"${{ github.actor }}\",
      \"ref\": \"${{ github.ref }}\",
      \"sha\": \"${{ github.sha }}\",
      \"conclusion\": \"${{ github.event.workflow_run.conclusion }}\"
    }"
```

**Audit requirements:**
- [ ] All workflow runs logged (who, what, when, outcome)
- [ ] Secret access logged (which secrets, by which workflow)
- [ ] Retention: minimum 90 days, 1 year for compliance (SOC2/ISO27001)
- [ ] Alert on: first-time secret usage, unusual actor, off-hours deploy

## Step 18: Build Reproducibility

```dockerfile
# Deterministic builds
FROM node:20.11.0-slim@sha256:abc123... AS builder
COPY package.json package-lock.json ./
RUN npm ci --ignore-scripts
ENV SOURCE_DATE_EPOCH=1
RUN npm run build
RUN find dist -exec touch -t 197001010000.00 {} +
```

```yaml
# Hermetic builds
- run: docker buildx build --network=none --output type=local,dest=./output .

# Build provenance
- uses: actions/attest-build-provenance@v2
  with:
    subject-name: ghcr.io/org/myapp
    subject-digest: ${{ steps.build.outputs.digest }}
    push-to-registry: true
```

**Hermetic build checklist:**
- [ ] All deps vendored or cached before build starts
- [ ] Build tool pinned to exact version + hash
- [ ] No `curl`/`wget`/`pip install` during build phase
- [ ] `--network=none` for Docker builds

## Step 19: Pipeline Observability

```yaml
# Build metrics
- name: Emit build metrics
  if: always()
  run: |
    DURATION=$(($(date +%s) - ${{ github.event.workflow_run.created_at }}))
    curl -X POST "https://api.datadoghq.com/api/v2/series" \
      -H "DD-API-KEY: ${{ secrets.DD_API_KEY }}" \
      -H "Content-Type: application/json" \
      -d "{\"series\": [{\"metric\": \"ci.build.duration\", \"points\": [[\"$(date +%s)\", $DURATION]], \"tags\": [\"repo:${{ github.repository }}\", \"workflow:${{ github.workflow }}\"]}]}"

# Test reporting
- uses: dorny/test-reporter@v1
  with:
    path: '**/test-results.xml'
    reporter: java-junit
- uses: codecov/codecov-action@v4
  with:
    token: ${{ secrets.CODECOV_TOKEN }}
```

**Key metrics:** Build duration (p50/p95/p99), cache hit rate, queue time, failure rate, flaky test rate.

## Step 20: Monorepo CI Patterns

### Affected-Only Builds
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

### Incremental Testing & Shared Caches
```yaml
# Nx
- run: npx nx affected -t test --base=origin/main --parallel=3
- run: npx nx affected -t build --cloud
  env:
    NX_CLOUD_AUTH_TOKEN: ${{ secrets.NX_CLOUD_TOKEN }}

# Turborepo
- run: npx turbo run build --cache-dir=.turbo
  env:
    TURBO_TOKEN: ${{ secrets.TURBO_TOKEN }}
    TURBO_TEAM: org-name
```

**Monorepo checklist:**
- [ ] Use Nx/Turborepo/Bazel for dependency graph awareness
- [ ] Affected-only builds on PRs, full builds on main
- [ ] Remote/distributed cache for build artifacts
- [ ] Nightly full test suite (catch drift from incremental)

## Step 21: Pipeline Cost Optimization

### Cache Strategies
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
```

### Runner Cost Tiers
| Runner | Cost | Use For |
|--------|------|---------|
| ubuntu-latest | 1x | Linux builds, Docker |
| ubuntu-24.04-arm | 1x | ARM builds (native) |
| macos-13 | 10x | macOS/iOS only |
| macos-13-xlarge (M1) | 20x | Apple Silicon only |
| windows-latest | 2x | Windows only |

**Cost optimization checklist:**
- [ ] Tag all CI resources with team/cost-center/project
- [ ] Generate monthly showback reports
- [ ] Set per-team CI minutes budgets with alerts
- [ ] Default to cheapest runner tier, escalate only on failure
- [ ] Use spot/preemptible for self-hosted runners (non-release)
- [ ] Monitor cost per build metric — track over time

**Green CI checklist:** cancel stale runs, cache aggressively, `paths:` filters for docs, avoid macOS when Linux suffices, schedule heavy jobs during green grid windows.

## Step 22: Pipeline Governance

```yaml
# Policy enforcement
- run: conftest test --policy policies/ manifests/
- run: kyverno apply policies/ --resource manifests/

# Compliance gate
- run: |
    CRITICAL=$(trivy image --format json myapp:${{ github.sha }} | jq '[.Results[].Vulnerabilities[] | select(.Severity=="CRITICAL")] | length')
    if [ "$CRITICAL" -gt 0 ]; then exit 1; fi
- run: license-checker --production --failOn 'GPL-3.0;AGPL-3.0'
```

## Step 23: Progressive Delivery

### Comparison & Decision
| Feature | Spinnaker | Argo Rollouts | Flagger |
|---------|-----------|---------------|---------|
| Scope | Full CD platform | K8s-native controller | Progressive delivery |
| Overhead | Heavy | Lightweight | Lightweight |
| Best For | Large orgs, multi-cloud | ArgoCD teams | Metric-driven promotion |

```
Need multi-cloud CD platform?  YES → Spinnaker
Already using ArgoCD?          YES → Argo Rollouts
Have service mesh?             YES → Flagger
Default                        → Argo Rollouts (CRD-only, no mesh required)
```

### Argo Rollouts Canary + AnalysisTemplate
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  strategy:
    canary:
      steps:
        - setWeight: 20
        - pause: { duration: 5m }
        - setWeight: 40
        - pause: { duration: 5m }
      analysis:
        templates:
          - templateName: success-rate
---
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
            sum(rate(http_requests_total{service="{{args.service-name}}",status=~"2.."}[5m]))
            / sum(rate(http_requests_total{service="{{args.service-name}}"}[5m]))
```

**Progressive delivery checklist:**
- [ ] AnalysisTemplate for success rate, latency, error rate, pod restarts
- [ ] failureLimit: 0 for critical errors, 1-2 for performance
- [ ] Header-based routing for internal testing before weight shift
- [ ] Traffic mirroring to validate canary with real traffic
- [ ] Automated rollback on analysis failure
- [ ] Alerting on rollback events (Slack/PagerDuty)

## Step 24: Multi-Environment Management

### Kustomize Overlays
```yaml
# overlays/dev/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources: [../../base]
namePrefix: dev-
namespace: dev
patches:
  - path: patch-replicas.yaml
```

### ArgoCD ApplicationSets
```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: myapp
spec:
  generators:
    - list:
        elements:
          - { env: dev, cluster: https://dev-cluster, namespace: dev }
          - { env: staging, cluster: https://staging-cluster, namespace: staging }
          - { env: production, cluster: https://prod-cluster, namespace: production }
  template:
    spec:
      source:
        path: 'apps/myapp/overlays/{{env}}'
      destination:
        server: '{{cluster}}'
        namespace: '{{namespace}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

### Dev/Staging/Prod Parity
| Aspect | Dev | Staging | Prod |
|--------|-----|---------|------|
| Container image | Same | Same | Same |
| Resource limits | Minimal | Medium | Full |
| Replicas | 1 | 2 | 3+ |
| Database | Local | Prod-like + anonymized | Real |

## Step 25: Database CI/CD

### Ephemeral Database
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

### Expand-Contract Pattern (Zero-Downtime)
```
Phase 1: EXPAND — Add new column (nullable), dual-write
Phase 2: MIGRATE — Backfill, read from new column
Phase 3: CONTRACT — Drop old column
```

```sql
-- Phase 1: Expand
ALTER TABLE users ADD COLUMN email_address TEXT;
UPDATE users SET email_address = email WHERE email_address IS NULL;
-- Code: write to both columns, read from email_address

-- Phase 3: Contract
ALTER TABLE users DROP COLUMN email;
```

### Migration Linting
```yaml
- run: atlas schema diff --from "postgres://..." --to "file://schema.hcl" --dev-url "docker://postgres/16"
- run: atlas migrate lint --dir "file://migrations" --dev-url "docker://postgres/16" --latest 1
- run: npm install -g squawk-cli && squawk --pg-version 16 migrations/*.sql
```

**Dangerous patterns:** `ALTER TABLE ... ADD COLUMN ... NOT NULL` without DEFAULT (locks table), `DROP TABLE` without backup, `CREATE INDEX` without `CONCURRENTLY` (locks table), missing index on FK column.

### Compensating Migrations
```sql
-- Forward: add column / Rollback: drop column
ALTER TABLE users ADD COLUMN display_name TEXT;
ALTER TABLE users DROP COLUMN IF EXISTS display_name;

-- Forward: create index / Rollback: drop index
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
DROP INDEX CONCURRENTLY IF EXISTS idx_users_email;
```

**Database CI/CD checklist:**
- [ ] Every migration runs against ephemeral DB in CI
- [ ] Migrations linted for dangerous operations (table locks, missing defaults)
- [ ] Expand-contract for zero-downtime column/table changes
- [ ] Compensating migrations for every forward migration
- [ ] Staging runs production migration before production
- [ ] Backup verified before production migration

## Step 26: SLSA & Sigstore (Detailed)

### SLSA L3 Provenance Generator
```yaml
provenance:
  needs: build
  permissions:
    actions: read
    ### Migration Linting
    ```yaml
    - run: atlas migrate lint --dir "file://migrations" --dev-url "docker://postgres/16" --latest 1
    - run: squawk --pg-version 16 migrations/*.sql
    ```

    **Dangerous patterns detected:**
    - `ALTER TABLE ... ADD COLUMN ... NOT NULL` without DEFAULT (locks table)
    - `CREATE INDEX` without `CONCURRENTLY` (locks table)
    - Missing index on foreign key column

    **Database CI/CD checklist:**
    - [ ] Every migration runs against ephemeral DB in CI
    - [ ] Migrations linted for dangerous operations
    - [ ] Expand-contract for zero-downtime changes
    - [ ] Compensating migrations for every forward migration
    - [ ] Staging runs production migration before production
    - [ ] Backup verified before production migration

    ## Step 26: SLSA & Sigstore

    ### SLSA L3 Provenance Generator
    ```yaml
    provenance:
      needs: build
      permissions:
        actions: read
        id-token: write
        packages: write
      uses: slsa-framework/slsa-github-generator/.github/workflows/generator_container_slsa3.yml@v2.1.0
      with:
        image: ${{ env.IMAGE }}
        digest: ${{ needs.build.outputs.digest }}
        registry-username: ${{ github.actor }}
      secrets:
        registry-password: ${{ secrets.GITHUB_TOKEN }}
    ```

    | Level | Provenance | Isolation | Non-falsifiable |
    |-------|-----------|-----------|-----------------|
    | L2 | Required | Optional | Optional |
    | L3 | Required | Required | Required |
    | L4 | Required | Required + Hermetic | Required + Two-party |

    ### Cosign Keyless Signing & Verification
    ```bash
    cosign sign --yes ghcr.io/org/myapp@sha256:abc123...

    cosign verify \
      --certificate-identity='https://github.com/org/myapp/.github/workflows/build.yml@refs/tags/v*' \
      --certificate-oidc-issuer='https://token.actions.githubusercontent.com' \
      ghcr.io/org/myapp:latest
    ```

    **SLSA + Sigstore checklist:**
    - [ ] L2 minimum: hosted CI with signed provenance
    - [ ] L3: slsa-github-generator delegator workflow
    - [ ] Verify provenance on deploy: `slsa-verifier verify-artifact`
    - [ ] Cosign installed (sigstore/cosign-installer action)
    - [ ] Strict verification: pin `--certificate-identity` and `--certificate-oidc-issuer`
    - [ ] Sign both container images and build artifacts (blobs)

    ## Step 27: Dependency Security

| Feature | Dependabot | Renovate | Socket.dev | Snyk |
|---------|-----------|----------|------------|------|
| Platforms | GitHub only | GitHub/GitLab/Bitbucket | GitHub App | Multi-platform |
| Package managers | 15 | 80+ | npm/pip/Go/Maven | 10+ + containers |
| Malicious detection | ❌ | ❌ | ✅ | Partial |
| SAST | ❌ | ❌ | ❌ | ✅ |

**Recommended:** Renovate (updates) + Socket.dev (malicious detection) + Snyk (SCA+SAST)

### Renovate Config
```jsonc
{
  "extends": ["config:recommended", ":semanticCommits"],
  "packageRules": [
    { "matchUpdateTypes": ["patch"], "automerge": true, "automergeType": "pr", "automergeStrategy": "squash" },
    { "matchDepTypes": ["devDependencies"], "matchUpdateTypes": ["minor", "patch"], "groupName": "dev deps (non-major)", "automerge": true }
  ],
  "vulnerabilityAlerts": { "enabled": true, "labels": ["security"] },
  "pinDigests": true
}
```

**Dependency security checklist:**
- [ ] Automated dependency updates (Dependabot or Renovate)
- [ ] Digest pinning enabled (`pinDigests: true`)
- [ ] Security-only alerts configured (Snyk or GitHub Security tab)
- [ ] Malicious package detection (Socket.dev)
- [ ] Auto-merge patch updates of dev dependencies
- [ ] Vulnerability PRs merge within SLA (critical: 24h, high: 7d, medium: 30d)

## Step 28: Environment Protection

```yaml
jobs:
  deploy-staging:
    environment:
      name: staging
      url: https://staging.myapp.com
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ./deploy.sh staging

  deploy-production:
    needs: deploy-staging
    environment:
      name: production
      url: https://myapp.com
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ./deploy.sh production
```

| Setting | Staging | Production |
|---------|---------|------------|
| Required reviewers | 0 | 2 |
| Wait timer | 0 min | 15 min |
| Deployment branches | `main` | `main` + `v*` tags |

### Least Privilege
```yaml
permissions: {}

jobs:
  deploy:
    permissions:
      contents: read
      id-token: write
      deployments: write
```

| Permission | Read | Write |
|-----------|------|-------|
| contents | checkout | releases |
| packages | pull images | push images |
| id-token | N/A | OIDC token |
| attestations | N/A | build provenance |

**Complete hardening checklist:**
- [ ] Top-level `permissions: {}`
- [ ] OIDC for AWS/GCP/Azure
- [ ] All actions SHA-pinned
- [ ] CODEOWNERS protects workflows
- [ ] Environment protection: reviewers + wait timer
- [ ] Environment-scoped secrets
- [ ] Deployment branches restricted
- [ ] Dependabot/Renovate for action updates
