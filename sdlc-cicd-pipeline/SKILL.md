---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, trunk-based development, anti-patterns. SLSA L3 supply chain, SBOM generation, Green CI/CD, AI in pipelines, GitHub Actions hardening."
version: 3.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, ci-cd, github-actions, gitlab-ci, docker, devops, pipeline, gitops, dora, accelerate, trunk-based, slsa, sbom, supply-chain, green-ci, security-hardening]
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

## Step 10: Supply Chain Security

### Sigstore / Cosign — Artifact Signing
Source: https://www.sigstore.dev/

Keyless artifact signing. No long-lived keys.

**Components:**
- **cosign:** signs and verifies OCI images, blobs, attestations
- **fulcio:** free CA that issues short-lived certs tied to OIDC identity
- **rekor:** transparency log (append-only, public) for all signatures

```yaml
# GitHub Actions
- uses: sigstore/cosign-installer@v3
- run: cosign sign --yes ${{ env.REGISTRY }}/${{ env.IMAGE }}@${{ steps.build.outputs.digest }}
  env:
    COSIGN_EXPERIMENTAL: "1"
```

### SLSA Framework
Source: https://slsa.dev/

Supply-chain Levels for Software Artifacts. Google-originated, now OpenSSF.

| Level | Guarantees |
|-------|-----------|
| L0 | No guarantees |
| L1 | Provenance exists (who built it, how) |
| L2 | Hosted build platform, provenance is signed |
| L3 | Hardened build platform, non-falsifiable provenance |

```yaml
# SLSA L3 provenance generator
- uses: slsa-framework/slsa-github-generator/generator_container_slsa3.yml@v2
  with:
    image: ghcr.io/org/image
    digest: ${{ steps.build.outputs.digest }}
  permissions:
    id-token: write
```

### SLSA L3 Complete Workflow

Full end-to-end SLSA L3 container build + provenance in GitHub Actions:

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
    # SLSA generator runs as reusable workflow — MUST be called at top level
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

**SLSA L3 non-goals vs L2:** L3 adds hardened build platform (isolation, ephemeral env)
and non-falsifiable provenance (generator is separate from build). L2 only needs signed provenance.

Source: https://slsa.dev/spec/v1.0/levels

### SBOM Generation
Source: https://github.com/anchore/syft

Dedicated SBOM step — run after build, before sign. Produces machine-readable inventory of dependencies.

**Syft (Anchore) — standalone CLI:**
```yaml
# Generate SBOM for built image
- name: Install Syft
  run: curl -sSfL https://raw.githubusercontent.com/anchore/syft/main/install.sh | sh -s -- -b /usr/local/bin

- name: Generate SBOM
  run: syft ${{ env.IMAGE }}@${{ steps.build.outputs.digest }} -o spdx-json=sbom.spdx.json -o cyclonedx-json=sbom.cdx.json

- name: Upload SBOM artifact
  uses: actions/upload-artifact@v4
  with:
    name: sbom
    path: |
      sbom.spdx.json
      sbom.cdx.json
```

**Or use GitHub Action (simpler):**
```yaml
- uses: anchore/sbom-action@v0
  with:
    image: ${{ env.IMAGE }}@${{ steps.build.outputs.digest }}
    format: spdx-json
    output-file: sbom.spdx.json
```

**Vulnerability scan from SBOM (Grype):**
```bash
grype sbom:sbom.spdx.json --fail-on high
```

**Attach SBOM to image:**
```bash
cosign attest --predicate sbom.spdx.json --type spdx IMAGE
```

**SBOM best practices:**
- Generate both SPDX and CycloneDX (different consumers prefer different formats)
- Include transitive dependencies
- Pin Syft version for reproducible output
- Scan SBOM with Grype before attestation — don't attest vulnerable images
- Store SBOMs as build artifacts for audit trail

### Dependency Review
```yaml
# Block PRs on known vulnerabilities
- uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: high
    deny-licenses: GPL-3.0, AGPL-3.0
    comment-summary-in-pr: always
```

### Secret Management in CI

**HashiCorp Vault (OIDC auth):**
```yaml
- uses: hashicorp/vault-action@v3
  with:
    url: https://vault.example.com
    method: jwt
    role: ci-deployer
    secrets: |
      secret/data/ci token | CI_TOKEN
```

**Cloud native:**
- AWS: OIDC provider + IAM role
- GCP: Workload Identity Federation
- Azure: Federated credentials

Source: https://developer.hashicorp.com/vault/docs/github-actions

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

## Step 11: DORA 2024 — AI in CI/CD & Docs-as-Code

Source: https://dora.dev/research/2024-dora-report/

### AI-Assisted CI/CD (DORA 2024 Findings)
DORA 2024 found AI adoption correlates with improved delivery performance
when used *inside* pipelines, not as replacement for engineering judgment.

**AI in test generation:**
```yaml
# Generate tests from changed code, review before merge
- name: Generate tests with AI
  uses: coderabbitai/ai-pr-reviewer@latest
  with:
    generate-tests: true
    review-draft: true
```
Tools: GitHub Copilot test generation, Cody, Codeium, aider

**AI in code review (pipeline-integrated):**
- Automated PR summaries and review comments
- Vulnerability explanation (why is this CVE dangerous in this context?)
- Code smell detection beyond static rules

**Key DORA 2024 finding:** AI that augments developers (test suggestions, review hints)
improves throughput. AI that replaces review gates without human oversight increases
change failure rate.

### Documentation-as-Code in Pipelines
DORA 2024 found **documentation quality** is a strong predictor of delivery performance.

```yaml
# Docs build + deploy in CI pipeline
docs:
  runs-on: ubuntu-latest
  needs: lint
  steps:
    - uses: actions/checkout@v4
    - run: pip install mkdocs-material
    - run: mkdocs build --strict  # Fail on broken links
    - uses: actions/upload-pages-artifact@v3
      with:
        path: site/

# API docs from OpenAPI spec
    - run: npx @redocly/cli build-docs openapi.yaml -o site/api.html
```

**Docs-as-code practices:**
- Docs live in same repo as code, reviewed in same PRs
- API docs auto-generated from OpenAPI/gRPC proto
- Broken links = CI failure (strict build mode)
- Changelog generated from conventional commits

## Step 12: Green CI/CD Patterns

Source: https://grfrn.org/, https://www.thegreenwebfoundation.org/

### Carbon-Aware Scheduling
Run compute-heavy jobs when/where grid electricity is cleanest.

```yaml
# Use Electricity Maps API to find green windows
- name: Check carbon intensity
  run: |
    INTENSITY=$(curl -s "https://api.electricitymap.org/v3/carbon-intensity/latest?zone=${{ runner.location }}" \
      -H "auth-token: ${{ secrets.ELECTRICITY_MAPS_TOKEN }}" | jq '.carbonIntensity')
    echo "carbon_intensity=$INTENSITY" >> $GITHUB_OUTPUT

# Delay heavy jobs if carbon intensity > threshold
- name: Wait for green window
  if: steps.carbon.outputs.carbon_intensity > 200
  run: sleep 3600  # Retry in 1 hour
```

**Green runners:**
- Use cloud regions with low-carbon grids (Nordics, Quebec, Pacific NW)
- [Green Web Foundation](https://www.thegreenwebfoundation.org/) directory of green hosts
- GitHub Actions runs on Azure — some regions cleaner than others

### Efficient Caching (Reduce Redundant Compute)
```yaml
# Aggressive Docker layer caching
- uses: docker/build-push-action@v5
  with:
    cache-from: type=gha
    cache-to: type=gha,mode=max  # Cache ALL layers, not just final

# Cache test results — skip unchanged test suites
- uses: actions/cache@v4
  with:
    path: .test-cache
    key: tests-${{ runner.os }}-${{ hashFiles('**/*.test.ts') }}-${{ hashFiles('src/**') }}
```

**Green CI checklist:**
- [ ] Cancel stale workflow runs (concurrency groups)
- [ ] Cache aggressively (deps, Docker layers, test fixtures)
- [ ] Use `paths:` filters — skip CI for docs-only changes
- [ ] Avoid macOS runners when Linux suffices (10x energy cost)
- [ ] Schedule nightly heavy jobs during green grid windows
- [ ] Monitor CI minutes as proxy for carbon footprint

## Step 13: Test Sharding Advanced Patterns

### Playwright Sharding (Native)
```yaml
# Playwright built-in sharding
test:
  strategy:
    matrix:
      shard: [1/4, 2/4, 3/4, 4/4]
  steps:
    - run: npx playwright test --shard=${{ matrix.shard }}
    - uses: actions/upload-artifact@v4
      if: always()
      with:
        name: playwright-report-${{ strategy.job-index }}
        path: playwright-report/

# Merge reports after all shards
merge-reports:
  needs: test
  if: always()
  steps:
    - run: npx playwright merge-reports --reporter html ./all-blob-reports
    - uses: actions/upload-artifact@v4
      with:
        name: html-report
        path: playwright-report/
```

### pytest-xdist (Parallel Workers)
```yaml
# Parallel pytest with auto-detected workers
- run: pip install pytest-xdist
- run: pytest -n auto --dist loadgroup --timeout=120

# Shard by file hash for matrix builds
strategy:
  matrix:
    shard: [1, 2, 3, 4]
- run: |
    FILES=$(pytest --collect-only -q | python -c "
    import sys, hashlib
    files = sorted(set(l.split('::')[0] for l in sys.stdin if '::' in l))
    n = int('${{ matrix.shard }}')
    total = 4
    selected = [f for i, f in enumerate(files) if i % total == n - 1]
    print(' '.join(selected))
    ")
    pytest $FILES -v
```

### Test Sharding Anti-Patterns
- **Time-based sharding** — fragile, uneven (tests change duration)
- **Alphabetical sharding** — uneven (test files vary in size)
- **Use file-hash or count-based** — deterministic, even distribution

### Vitest Sharding
```yaml
- run: npx vitest --shard=${{ matrix.shard }}/4 --reporter=default
```

### Jest Shard (via --shard flag, Jest 28+)
```yaml
- run: npx jest --shard=${{ matrix.shard }}/4 --coverage
```

## Step 14: GitHub Actions Security Hardening

Source: https://docs.github.com/en/actions/security-guides

### Pin Actions to Full SHA
```yaml
# BEFORE (vulnerable to tag mutation attack)
- uses: actions/checkout@v4
- uses: docker/build-push-action@v5

# AFTER (immutable, auditable)
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11  # v4.1.1
- uses: docker/build-push-action@0565240e2d4ab88bba5387d7195852808d34b1d1  # v5.1.0
```

**Automation:** use `gh-actions-pinner` or `pinact` CLI:
```bash
go install github.com/suzuki-shunsuke/pinact/cmd/pinact@latest
pinact run  # Pins all actions in .github/workflows/
```

### Minimal Permissions
```yaml
# Top-level: default to read-only
permissions:
  contents: read

# Per-job: elevate only what's needed
jobs:
  build:
    permissions:
      contents: read
      packages: write  # Only if pushing to GHCR
  deploy:
    permissions:
      id-token: write  # OIDC for cloud auth
      contents: read
```

### GITHUB_TOKEN Scoping
```yaml
# NEVER give broad write permissions
# BAD:
permissions: write-all

# GOOD: explicit minimal scope
permissions:
  contents: read
  pull-requests: write  # Only if bot needs to comment

# For OIDC (SLSA, cloud deploys):
permissions:
  id-token: write
  contents: read
```

### Third-Party Action Auditing
```yaml
# Pin to SHA + verify maintainer
- uses: some-org/some-action@a1b2c3d4e5f6  # Pin SHA
  # Check: who maintains this? Is it a verified creator?
  # Use: https://github.com/step-security/secure-repo to audit
```

**Supply chain hardening checklist:**
- [ ] All actions pinned to full commit SHA
- [ ] `permissions:` set at workflow level (read-only default)
- [ ] Per-job elevated permissions are minimal
- [ ] `GITHUB_TOKEN` has no unnecessary scopes
- [ ] Dependabot/Renovate configured for action version updates
- [ ] `CODEOWNERS` protects workflow files
- [ ] Branch protection: require PR reviews for workflow changes
- [ ] Use `step-security/harden-runner` for runtime monitoring

```yaml
# Runtime hardening with StepSecurity
- uses: step-security/harden-runner@v2
  with:
    egress-policy: audit  # or 'block' for strict mode
    allowed-endpoints: >
      github.com:443
      api.github.com:443
```
