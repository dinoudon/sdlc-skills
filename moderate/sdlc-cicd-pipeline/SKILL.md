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

### Key Patterns

### Pipeline Architectures
- **Branch pipelines:** run on every push (default)
- **Merge request pipelines:** run only on MRs
- **Parent-child pipelines:** parent triggers child via `trigger:` keyword
- **Multi-project pipelines:** cross-repo triggers

### Key Patterns
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

test:
  needs: [build]
  script: [pytest]

cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .npm/
  policy: pull-push

include:
  - local: ci/build.yml
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Secret-Detection.gitlab-ci.yml
```

### Rules (replaces only/except)
```yaml
# Parent-child pipeline
generate-child:
  stage: deploy
  trigger:
    include: child-pipeline.yml
    strategy: depend  # Parent waits for child
```

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

### Container Security Scanning
```bash
trivy image myapp:latest
grype myapp:latest
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
Keyless artifact signing. No long-lived keys.

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

### SLSA L3 Complete Workflow

Full end-to-end SLSA L3 container build + provenance:

```yaml
# .github/workflows/release-slsa3.yml
name: SLSA L3 Release
on:
  push:
    tags: ['v*']

permissions:
  contents: read

env:
  IMAGE: ghcr.io/${{ github.repository }}

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      digest: ${{ steps.build.outputs.digest }}
    permissions:
      contents: read
      packages: write
      id-token: write
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/setup-buildx-action@v3
      - id: build
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: ${{ env.IMAGE }}:${{ github.ref_name }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

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

**Verify provenance:**
```bash
cosign verify-attestation \
  --type slsaprovenance \
  --certificate-identity-regexp 'https://github.com/slsa-framework/slsa-github-generator' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  ${{ env.IMAGE }}@${{ digest }}
```

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

### Playwright Sharding
```yaml
test:
  strategy:
    matrix:
      shard: [1/4, 2/4, 3/4, 4/4]
  steps:
    - run: npx playwright test --shard=${{ matrix.shard }}
```

### pytest-xdist
```yaml
- run: pytest -n auto --dist loadgroup --timeout=120
```

### Vitest / Jest
```yaml
- run: npx vitest --shard=${{ matrix.shard }}/4
- run: npx jest --shard=${{ matrix.shard }}/4 --coverage
```

**Anti-patterns:** Time-based and alphabetical sharding are uneven. Use file-hash or count-based.

## Step 14: GitHub Actions Security Hardening

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

## Step 15: Serverless CI/CD

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

### CDK Pipeline (Self-Mutating)

**SAM local testing in CI:**
```yaml
# Integration test against local Lambda runtime
- run: sam local invoke MyFunction --event events/test.json
- run: sam local start-api &
- run: sleep 5 && curl http://localhost:3000/health
```

**SAM with layers (shared deps):**
```yaml
- run: sam build --cached --parallel --use-container  # Build in container for Linux compat
```

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

**Serverless offline testing in CI:**
```yaml
- run: npx serverless offline start &
- run: sleep 10 && curl http://localhost:3000/dev/health
- run: npx jest tests/integration
```

**Serverless packaging optimization:**
```yaml
# serverless.yml — exclude dev deps and tests
package:
  individually: true
  patterns:
    - '!tests/**'
    - '!**/*.test.*'
    - '!node_modules/.cache/**'
```

```yaml
- uses: aws-actions/configure-aws-credentials@v4
  with:
    role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
    aws-region: us-east-1
- run: npx serverless deploy --stage production
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

### Kubernetes Preview

**Comment URL on PR:**
```yaml
comment:
  needs: deploy-preview
  runs-on: ubuntu-latest
  steps:
    - uses: actions/github-script@v7
      with:
        script: |
          github.rest.issues.createComment({
            issue_number: context.issue.number,
            owner: context.repo.owner,
            repo: context.repo.repo,
            body: `🚀 Preview: https://pr-${context.issue.number}.preview.example.com`
          })
```

**Vercel/Netlify Preview (SaaS):**
```yaml
- run: npx vercel deploy --yes --token=${{ secrets.VERCEL_TOKEN }}
  env:
    VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
    VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

**Preview env best practices:**
- Use concurrency groups — cancel stale preview deploys
- Always comment URL on PR for easy access
- Auto-destroy on PR close/merge (prevent resource leaks)
- Set TTL (72h max) for safety net if webhook misses close event
- Monitor costs — cap preview envs per org

```yaml
- run: |
    NS="pr-${{ github.event.pull_request.number }}"
    kubectl create namespace "$NS" --dry-run=client -o yaml | kubectl apply -f -
    helm upgrade --install myapp ./chart --namespace "$NS"       --set ingress.host="pr-${{ github.event.pull_request.number }}.preview.example.com"
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

### Rust Multi-Platform
```dockerfile
FROM --platform=$BUILDPLATFORM rust:1.77 AS builder
ARG TARGETPLATFORM
RUN case "$TARGETPLATFORM" in \
      "linux/arm64") echo "aarch64-unknown-linux-gnu" > /tmp/target ;; \
      *) echo "x86_64-unknown-linux-gnu" > /tmp/target ;; \
    esac && \
    rustup target add $(cat /tmp/target)
WORKDIR /app
COPY Cargo.* ./
RUN cargo fetch
COPY . .
RUN cargo build --release --target $(cat /tmp/target) && \
    cp target/$(cat /tmp/target)/release/server /app/server

FROM gcr.io/distroless/static:nonroot
COPY --from=builder /app/server /server
CMD ["/server"]
```

**Checklist:**
- [ ] Use `$BUILDPLATFORM` for build stage (native speed)
- [ ] Go: `CGO_ENABLED=0 GOOS/$GOARCH` — no QEMU needed
- [ ] Rust: cross-compile with `--target`
- [ ] Node/Python: QEMU required unless native ARM runners

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

### pnpm Store Cache
```yaml
- uses: pnpm/action-setup@v4
  with:
    version: 9
- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'pnpm'  # Auto-caches pnpm store
```

### uv Cache (Fast Python)
```yaml
- run: pip install uv
- uses: actions/cache@v4
  with:
    path: ~/.cache/uv
    key: uv-${{ runner.os }}-${{ hashFiles('**/uv.lock') }}
```

### Composite Cache Pattern (Multi-Deps Monorepo)
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

## Step 20: Pipeline Security Hardening

### OIDC for Cloud Authentication
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

**OIDC hardening:**

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

- [ ] One IAM role per repo
- [ ] Condition on branch/environment
- [ ] Short session duration (15-60 min)
- [ ] Separate roles: read-only for CI, write for deploy

### Secrets Hygiene
- [ ] Environment-scoped secrets (not repo-wide)
- [ ] `pull_request` trigger never receives production secrets
- [ ] Rotate all CI secrets quarterly minimum
- [ ] Scan for leaked secrets in every pipeline

## Step 21: Build Reproducibility

### Deterministic Builds
```dockerfile
FROM node:20.11.0-slim@sha256:abc123... AS builder
COPY package.json package-lock.json ./
RUN npm ci --ignore-scripts
ENV SOURCE_DATE_EPOCH=1
RUN npm run build
RUN find dist -exec touch -t 197001010000.00 {} +
```

### Hermetic Builds
```yaml
- run: docker buildx build --network=none --output type=local,dest=./output .
```

**Hermetic build checklist:**
- [ ] All deps vendored or cached before build starts
- [ ] Build tool pinned to exact version + hash
- [ ] No `curl`/`wget`/`pip install` during build phase
- [ ] `--network=none` for Docker builds
- [ ] Build sandbox enabled (Bazel, Nix)

### Build Provenance
```yaml
- uses: actions/attest-build-provenance@v2
  with:
    subject-name: ghcr.io/org/myapp
    subject-digest: ${{ steps.build.outputs.digest }}
    push-to-registry: true
```

**Verify provenance in deploy pipeline:**
```yaml
deploy:
  steps:
    - name: Verify provenance
      run: |
        gh attestation verify oci://${IMAGE}@${DIGEST} \
          --owner org \
          --signer-repo org/repo
    - name: Deploy only if verified
      run: kubectl apply -f k8s/
```

### Build Provenance
```yaml
- ## Step 22: Pipeline Observability

### Build Metrics
```yaml
# Emit build metrics to Datadog/Prometheus/Grafana
- name: Emit build metrics
  if: always()
  run: |
    DURATION=$(($(date +%s) - ${{ github.event.workflow_run.created_at }}))
    curl -X POST "https://api.datadoghq.com/api/v2/series" \
      -H "DD-API-KEY: ${{ secrets.DD_API_KEY }}" \
      -H "Content-Type: application/json" \
      -d "{
        \"series\": [{
          \"metric\": \"ci.build.duration\",
          \"points\": [[\"$(date +%s)\", $DURATION]],
          \"tags\": [\"repo:${{ github.repository }}\", \"workflow:${{ github.workflow }}\"]
        }]
      }"
```

**Key build metrics to track:**
- Build duration (p50, p95, p99) per workflow
- Cache hit rate (actions/cache)
- Queue time (time waiting for runner)
- Failure rate per workflow/job
- Flaky test rate

### Deployment Tracking
```yaml
- name: Record deployment
  if: success() && github.ref == 'refs/heads/main'
  run: |
    curl -X POST "${{ secrets.DEPLOYMENT_TRACKER_URL }}" \
      -H "Content-Type: application/json" \
      -d "{
        \"environment\": \"production\",
        \"version\": \"${{ github.sha }}\",
        \"deployer\": \"${{ github.actor }}\",
        \"pipeline_url\": \"${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}\"
      }"
```

**Deployment tracking checklist:**
- [ ] Every deployment recorded (env, version, who, when)
- [ ] DORA metrics auto-calculated from deployment records
- [ ] Rollback events tracked separately
- [ ] ChatOps notification (Slack/Teams) on every deploy

**Key metrics:** Build duration (p50/p95/p99), cache hit rate, queue time, failure rate, flaky test rate.

```yaml
- uses: dorny/test-reporter@v1
  with:
    path: '**/test-results.xml'
    reporter: java-junit
- uses: codecov/codecov-action@v4
  with:
    token: ${{ secrets.CODECOV_TOKEN }}
```

## Step 23: Monorepo CI Patterns

### Affected-Only Builds

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

### Incremental Testing
```yaml
# Nx incremental test
- run: npx nx affected -t test --base=origin/main --parallel=3

# pytest with testmon (incremental Python)
- run: |
    pip install pytest-testmon
    pytest --testmon --testmon-cov=.testmondata

# Jest incremental (via changedSince)
- run: npx jest --changedSince=origin/main --passWithNoTests

# Go incremental tests (package-level)
- run: |
    CHANGED_PKGS=$(git diff --name-only origin/main...HEAD -- '*.go' | \
      xargs -I{} dirname {} | sort -u | sed 's|^|./|')
    go test $CHANGED_PKGS -v -count=1
```

### Shared Caches in Monorepos
```yaml
# Nx Cloud (distributed cache)
- run: npx nx affected -t build --cloud
  env:
    NX_CLOUD_AUTH_TOKEN: ${{ secrets.NX_CLOUD_TOKEN }}

# Turborepo remote cache
- run: npx turbo run build --cache-dir=.turbo
  env:
    TURBO_TOKEN: ${{ secrets.TURBO_TOKEN }}
    TURBO_TEAM: org-name

# Bazel remote cache
- run: bazel build //... --remote_cache=grpcs://cache.example.com
```

```yaml
# Path-based workflow splitting
name: Frontend CI
on:
  pull_request:
    paths:
      - 'apps/web/**'
      - 'packages/shared/**'
      - 'pnpm-lock.yaml'
```

```yaml
# Nx
- uses: nrwl/nx-set-shas@v4
- run: npx nx affected -t lint test build --base=${{ env.NX_BASE }}

# Turborepo
- run: npx turbo run lint test build --filter=...[origin/main]

# Bazel
- run: bazel test $(bazel query 'kind("test", rdeps(//..., set($(git diff --name-only origin/main HEAD))))')
```

**Checklist:**
- [ ] Use Nx/Turborepo/Bazel for dependency graph awareness
- [ ] Affected-only builds on PRs, full builds on main
- [ ] Remote/distributed cache for build artifacts
- [ ] Nightly full test suite (catch drift from incremental)

## Step 24: Pipeline Cost Optimization

### Cache Strategies (Cost-Saving)
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
  uses: actions/cache@v4
  with:
    path: ~/.cache/go-build
    key: go-${{ hashFiles('go.sum') }}

- run: go build ./...
  if: steps.cache.outputs.cache-hit != 'true'
```

### FinOps for CI/CD

Source: https://www.finops.org/framework/

FinOps applies financial accountability to cloud spend. CI/CD is often 10-30% of cloud compute costs.

**Three phases:**
- **Inform:** Visibility into CI/CD spend by team, repo, workflow. Benchmark cost per build/deployment.
- **Optimize:** Right-size runners, eliminate waste, autoscale self-hosted to zero, spot instances for non-critical jobs.
- **Operate:** Budget alerts per team/repo, chargeback model, continuous cost review in retrospectives.

**Cost allocation tagging:**
```yaml
env:
  COST_TEAM: platform-engineering
  COST_PROJECT: payment-service
  COST_CENTER: CC-1042
```

**Tiered runner strategy:**
- Tier 1 (default): ubuntu-latest, ubuntu-24.04-arm (1x cost)
- Tier 2 (when needed): windows-latest (2x), larger runners (2-4x)
- Tier 3 (minimize): macos-13 (10x), macos-13-xlarge M1 (20x)

**Cost optimization checklist:**
- [ ] Tag all CI resources with team/cost-center/project
- [ ] Generate monthly showback reports
- [ ] Set per-team CI minutes budgets with alerts
- [ ] Default to cheapest runner tier, escalate only on failure
- [ ] Use spot/preemptible for self-hosted runners (non-release)
- [ ] Monitor cost per build metric — track over time
- [ ] Review and eliminate unused workflows monthly

| Runner | Cost | Use For |
|--------|------|---------|
| ubuntu-latest | 1x | Linux builds, Docker |
| ubuntu-24.04-arm | 1x | ARM builds (native) |
| macos-13 | 10x | macOS/iOS only |
| macos-13-xlarge (M1) | 20x | Apple Silicon only |
| windows-latest | 2x | Windows only |

**Rules:**
- [ ] Default to `ubuntu-latest`
- [ ] Self-hosted runners for heavy workloads
- [ ] Concurrency groups — cancel stale runs
- [ ] Use `paths:` filters — skip CI for docs changes

## Step 25: Pipeline Governance

### Policy Enforcement
```yaml
- run: conftest test --policy policies/ manifests/
- run: kyverno apply policies/ --resource manifests/
```

### Compliance Gates
```yaml
jobs:
  compliance-gate:
    needs: build
    steps:
      - run: |
          CRITICAL=$(trivy image --format json myapp:${{ github.sha }} | jq '[.Results[].Vulnerabilities[] | select(.Severity=="CRITICAL")] | length')
          if [ "$CRITICAL" -gt 0 ]; then exit 1; fi
      - run: license-checker --production --failOn 'GPL-3.0;AGPL-3.0'
      - run: cosign verify ${{ env.REGISTRY }}/${{ env.IMAGE }}@${{ github.sha }}
```

## Step 26: Deployment at Scale

### Spinnaker vs Argo Rollouts vs Flagger

| Feature | Spinnaker | Argo Rollouts | Flagger |
|---------|-----------|---------------|---------|
| Scope | Full CD platform | K8s-native controller | Progressive delivery |
| Overhead | Heavy | Lightweight | Lightweight |
| Best For | Large orgs, multi-cloud | ArgoCD teams | Metric-driven promotion |

### Argo Rollouts Quick Start
### Flagger Canary with Istio
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
    hosts:
      - myapp.example.com
  analysis:
    interval: 1m
    threshold: 5
    maxWeight: 50
    stepWeight: 10
    metrics:
      - name: request-success-rate
        thresholdRange:
          min: 99
      - name: request-duration
        thresholdRange:
          max: 500
```

### Decision Guide
```
Need multi-cloud CD platform?
  YES → Spinnaker
  NO  ↓
Already using ArgoCD?
  YES → Argo Rollouts
  NO  ↓
Have service mesh (Istio/Linkerd)?
  YES → Flagger
  NO  → Argo Rollouts (CRD-only, no mesh required)
```

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
```

## Step 27: Multi-Environment Management

### ArgoCD ApplicationSets
### Kustomize Overlays
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
namespace: dev
patches:
  - path: patch-replicas.yaml

# overlays/dev/patch-replicas.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 1
  template:
    spec:
      containers:
        - name: myapp
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 200m
              memory: 256Mi
```

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: myapp
spec:
  generators:
    - list:
        elements:
          - env: dev
            cluster: https://dev-cluster
            namespace: dev
          - env: staging
            cluster: https://staging-cluster
            namespace: staging
          - env: production
            cluster: https://prod-cluster
            namespace: production
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

## Step 28: Database CI/CD

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

### Expand-Contract Pattern
```
Phase 1: EXPAND — Add new column (nullable), dual-write
Phase 2: MIGRATE — Backfill, read from new column
Phase 3: CONTRACT — Drop old column
```

### Migration Linting

#### Shadow Database (migration validation)

Separate database used to diff current schema against desired state.

```yaml
# Prisma shadow DB pattern
- run: |
    PGPASSWORD=test psql -h localhost -U postgres -c "CREATE DATABASE myapp_shadow;"
    npx prisma migrate diff \
      --from-schema-datamodel prisma/schema.prisma \
      --to-schema-datamodel prisma/schema.prisma \
      --shadow-database-url "postgresql://postgres:test@localhost:5432/myapp_shadow" \
      --script > migration.sql
    cat migration.sql
    npx prisma migrate deploy
```

**Atlas (declarative schema diffing):**
```yaml
- run: |
    atlas schema diff \
      --from "postgres://postgres:test@localhost:5432/myapp_test" \
      --to "file://schema.hcl" \
      --dev-url "docker://postgres/16"
```

#### Migration Linting Tools
```yaml
- run: atlas migrate lint --dir "file://migrations" --dev-url "docker://postgres/16" --latest 1
```

```yaml
# Squawk — PostgreSQL linting (detects unsafe ALTER TABLE)
- run: npm install -g squawk-cli && squawk --pg-version 16 migrations/*.sql

# SQLFluff — general SQL linting
- run: pip install sqlfluff && sqlfluff lint migrations/ --dialect postgres
```

**Dangerous patterns detected:**
- `ALTER TABLE ... ADD COLUMN ... NOT NULL` without DEFAULT (locks table)
- `DROP TABLE` without backup confirmation
- `CREATE INDEX` without `CONCURRENTLY` (locks table)
- Missing index on foreign key column

#### Rollback Strategies

**Expand-Contract Pattern (Zero-Downtime):**
```
Phase 1: EXPAND — Add new column (nullable), dual-write
Phase 2: MIGRATE — Backfill, read from new column
Phase 3: CONTRACT — Drop old column
```

**Example: Rename column safely**
```sql
-- Phase 1: Expand
ALTER TABLE users ADD COLUMN email_address TEXT;
UPDATE users SET email_address = email WHERE email_address IS NULL;
-- Code: write to both columns, read from email_address

-- Phase 3: Contract
ALTER TABLE users DROP COLUMN email;
```

**Compensating migrations:**
```sql
-- Forward: add column
ALTER TABLE users ADD COLUMN display_name TEXT;
-- Rollback: drop column
ALTER TABLE users DROP COLUMN IF EXISTS display_name;

-- Forward: create index
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
-- Rollback: drop index
DROP INDEX CONCURRENTLY IF EXISTS idx_users_email;
```

**Database CI/CD checklist:**
- [ ] Every migration runs against ephemeral DB in CI
- [ ] Migrations linted for dangerous operations (table locks, missing defaults)
- [ ] Expand-contract for zero-downtime column/table changes
- [ ] Compensating migrations for every forward migration
- [ ] Staging runs production migration before production
- [ ] Backup verified before production migration

```yaml

## Step 29: Progressive Delivery

### AnalysisTemplate
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
            sum(rate(http_requests_total{service="{{args.service-name}}",status=~"2.."}[5m]))
            / sum(rate(http_requests_total{service="{{args.service-name}}"}[5m]))
```

### Traffic Mirroring

### Header-Based Routing (Internal Testing)
```yaml
# Route specific headers to canary (testing with internal users)
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
    - myapp.example.com
  http:
    - match:
        - headers:
            x-canary:
              exact: "true"
      route:
        - destination:
            host: myapp
            subset: canary
    - route:
        - destination:
            host: myapp
            subset: stable
          weight: 100
```

### Automated Rollback Triggers
```yaml
# AnalysisTemplate with strict failure conditions
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: strict-analysis
spec:
  metrics:
    # Immediate abort on critical error rate spike
    - name: critical-error-rate
      interval: 30s
      count: 2
      successCondition: result[0] <= 0.001
      failureLimit: 0           # Zero tolerance — immediate rollback
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
            sum(rate(http_requests_total{
              service="{{args.service-name}}",
              status=~"5.."
            }[1m])) / sum(rate(http_requests_total{
              service="{{args.service-name}}"
            }[1m]))

    # Abort on pod crashes
    - name: crash-loop
      interval: 30s
      count: 3
      successCondition: result[0] == 0
      failureLimit: 0
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
            sum(kube_pod_container_status_waiting_reason{
              namespace="{{args.namespace}}",
              reason="CrashLoopBackOff"
            })
```

### Full Rollout (All 7 Phases)
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  replicas: 5
  revisionHistoryLimit: 3
  strategy:
    canary:
      canaryService: myapp-canary
      stableService: myapp-stable
      trafficRouting:
        istio:
          virtualServices:
            - name: myapp-vsvc
              routes:
                - primary
      steps:
        # Step 1-2: Deploy canary, no traffic
        - setWeight: 0
        - pause: { duration: 30s }

        # Step 3: Mirror traffic to canary
        - mirror:
            percentage: 100
            duration: 5m

        # Step 4: Analyze mirrored results
        - analysis:
            templates:
              - templateName: success-rate

        # Step 5: Progressive weight shifting with analysis
        - setWeight: 10
        - pause: { duration: 5m }
        - analysis:
            templates:
              - templateName: strict-analysis

        - setWeight: 25
        - pause: { duration: 5m }

        - setWeight: 50
        - pause: { duration: 10m }

        - setWeight: 75
        - pause: { duration: 5m }

        # Step 6: Full promotion (100% handled automatically)

        # Step 7: Post-deploy monitoring via background analysis
      analysis:
        templates:
          - templateName: success-rate
        startingStep: 1
        args:
          - name: service-name
            value: myapp
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
        - name: myapp
          image: myapp:canary
```

**Progressive delivery checklist:**
- [ ] AnalysisTemplate defined for success rate, latency, error rate, pod restarts
- [ ] failureLimit set per metric (0 for critical errors, 1-2 for performance)
- [ ] Header-based routing for internal testing before weight shift
- [ ] Traffic mirroring to validate canary with real traffic patterns
- [ ] Automated rollback triggers on any analysis failure
- [ ] Alerting configured (Slack/PagerDuty) on rollback events

### Lifecycle
```
BUILD → DEPLOY CANARY → MIRROR → ANALYZE → SHIFT WEIGHT → PROMOTE → OBSERVE
```

## Step 30: SLSA Detailed
### SLSA L3 Provenance Generator
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

### in-toto Attestation Format
```json
{
  "_type": "https://in-toto.io/Statement/v1",
  "subject": [{ "name": "pkg:github.com/org/myapp@v1.2.3", "digest": { "sha256": "abc123..." } }],
  "predicateType": "https://slsa.dev/provenance/v1",
  "predicate": {
    "buildDefinition": {
      "buildType": "https://actions.github.io/buildtypes/workflow/v1",
      "externalParameters": {
        "workflow": { "ref": "refs/tags/v1.2.3", "repository": "https://github.com/org/myapp" }
      }
    },
    "runDetails": {
      "builder": { "id": "https://github.com/actions/runner" },
      "metadata": {
        "invocationId": "https://github.com/org/myapp/actions/runs/12345",
        "startedOn": "2026-01-15T10:30:00Z"
      }
    }
  }
}
```

**SLSA checklist:**
- [ ] L2 minimum: use hosted CI with signed provenance
- [ ] L3: slsa-github-generator delegator workflow
- [ ] Provenance attestation attached to release artifacts
- [ ] Verify provenance on deploy: `slsa-verifier verify-artifact`


| Level | Provenance | Isolation | Non-falsifiable |
|-------|-----------|-----------|-----------------|
| L2 | Required | Optional | Optional |
| L3 | Required | Required | Required |
| L4 | Required | Required + Hermetic | Required + Two-party |

```yaml
- uses: actions/attest-build-provenance@v2
  with:
    subject-path: './build/myapp'
```

## Step 31: Sigstore

### Architecture
```
OIDC Provider → Fulcio (CA) → Cosign (Sign) → Rekor (Transparency Log)
```

### Cosign — Keyless Signing and Verification
```bash
# Install cosign
go install github.com/sigstore/cosign/v2/cmd/cosign@latest
# or: brew install cosign

# --- Keyless signing (OIDC → Fulcio → sign) ---
# In GitHub Actions: cosign detects OIDC automatically
cosign sign --yes \
  ghcr.io/org/myapp@sha256:abc123...

# Sign a file (not OCI image)
cosign sign-blob --yes \
  --output-signature mysig.sig \
  --output-certificate mycert.pem \
  ./build/myapp

# Sign with annotations
cosign sign --yes \
  -a "commit=${{ github.sha }}" \
  -a "workflow=build" \
  ghcr.io/org/myapp:latest

# --- Verification ---
# Verify OCI image signature (checks Rekor entry)
cosign verify \
  --certificate-identity-regexp='.*' \
  --certificate-oidc-issuer-regexp='.*' \
  ghcr.io/org/myapp:latest

# Verify with strict identity pinning
cosign verify \
  --certificate-identity='https://github.com/org/myapp/.github/workflows/build.yml@refs/tags/v*' \
  --certificate-oidc-issuer='https://token.actions.githubusercontent.com' \
  ghcr.io/org/myapp:latest

# Verify blob
cosign verify-blob \
  --cert mycert.pem \
  --signature mysig.sig \
  --certificate-identity='...' \
  --certificate-oidc-issuer='...' \
  ./build/myapp
```

### Full GitHub Actions Sigstore Workflow
```yaml
name: Build and Sign
on:
  push:
    tags: ['v*']

jobs:
  build-and-sign:
    runs-on: ubuntu-latest
    permissions:
      id-token: write    # OIDC → Fulcio
      packages: write    # Push to GHCR
      contents: read
    steps:
      - uses: actions/checkout@v4

      - name: Build and push image
        id: build
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.ref_name }}

      - name: Install cosign
        uses: sigstore/cosign-installer@v3

      - name: Sign image (keyless — OIDC → Fulcio → Rekor)
        run: |
          cosign sign --yes \
            -a "commit=${{ github.sha }}" \
            -a "version=${{ github.ref_name }}" \
            ghcr.io/${{ github.repository }}:${{ github.ref_name }}

      - name: Verify signature
        run: |
          cosign verify \
            --certificate-identity='https://github.com/${{ github.repository }}/.github/workflows/build-sign.yml@refs/tags/${{ github.ref_name }}' \
            --certificate-oidc-issuer='https://token.actions.githubusercontent.com' \
            ghcr.io/${{ github.repository }}:${{ github.ref_name }}
```

**Sigstore checklist:**
- [ ] Cosign installed in CI (sigstore/cosign-installer action)
- [ ] Keyless signing enabled (`--yes` flag for non-interactive OIDC)
- [ ] Strict verification: pin `--certificate-identity` and `--certificate-oidc-issuer`
- [ ] Rekor transparency enabled (default)
- [ ] Sign both container images and build artifacts (blobs)

## Step 32: Dependency Security

| Feature | Dependabot | Renovate | Socket.dev | Snyk |
|---------|-----------|----------|------------|------|
| Platforms | GitHub only | GitHub/GitLab/Bitbucket | GitHub App | Multi-platform |
| Package managers | 15 | 80+ | npm/pip/Go/Maven | 10+ + containers |
| Malicious detection | ❌ | ❌ | ✅ | Partial |
| SAST | ❌ | ❌ | ❌ | ✅ |

**Recommended:** Renovate (updates) + Socket.dev (malicious detection) + Snyk (SCA+SAST)

### Renovate Config
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
    }
  ],
  "vulnerabilityAlerts": { "enabled": true, "labels": ["security"] },
  "pinDigests": true
}
```

**Socket.dev catches (examples):**
- Package with `postinstall` script accessing `~/.ssh/`
- Typosquat: `lod-ash` vs `lodash`
- Dependency confusion: internal package name in public registry

**Dependency security checklist:**
- [ ] Automated dependency updates enabled (Dependabot or Renovate)
- [ ] Digest pinning enabled (Renovate `pinDigests: true`)
- [ ] Security-only alerts configured (Snyk or GitHub Security tab)
- [ ] Malicious package detection active (Socket.dev)
- [ ] Auto-merge for patch updates of dev dependencies
- [ ] Vulnerability PRs merge within SLA (critical: 24h, high: 7d, medium: 30d)

## Step 33: GitHub Actions Hardening (Complete)

### SHA-Pinned Actions
```bash
pinact run .github/workflows/*.yml
```

### CODEOWNERS
```
/.github/workflows/    @org/platform-security
/terraform/            @org/infrastructure
```

### Environment Protection

```yaml
# GitHub Environments: Settings → Environments
# Staging: auto-deploy on main merge
# Production: gated by reviewers + wait timer + branch filter

# In workflow, reference environment:
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

**Environment secrets scoping:**
```yaml
# Secrets scoped to environment (not repo-wide)
# deploy-production only has prod AWS role, staging has staging role
# Limits blast radius — compromised staging secret ≠ production access
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