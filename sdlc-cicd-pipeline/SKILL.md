---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, trunk-based development, anti-patterns. SLSA L3 supply chain, SBOM generation, Green CI/CD, AI in pipelines, GitHub Actions hardening. Serverless CI/CD (SAM/CDK/Serverless Framework), preview environments, multi-platform builds, advanced dependency caching. Pipeline security hardening, build reproducibility, pipeline observability, monorepo CI patterns, pipeline cost optimization."
version: 4.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, ci-cd, github-actions, gitlab-ci, docker, devops, pipeline, gitops, dora, accelerate, trunk-based, slsa, sbom, supply-chain, green-ci, security-hardening, serverless, preview-environments, multi-platform, ai-cicd, oidc, secrets-rotation, audit-logging, reproducible-builds, hermetic-builds, build-provenance, build-observability, monorepo-ci, cost-optimization]
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

## Step 15: Serverless CI/CD Patterns

Source: https://docs.aws.amazon.com/serverless-developer-guide/, https://www.serverless.com/framework/docs

### AWS SAM Build/Deploy Pipeline

```yaml
# .github/workflows/sam-deploy.yml
name: SAM Deploy
on:
  push:
    branches: [main]

permissions:
  id-token: write
  contents: read

jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsRole
          aws-region: us-east-1

      - uses: aws-actions/setup-sam@v2
        with:
          use-installer: true

      # Build (cached in .aws-sam/)
      - run: sam build --cached --parallel

      # Deploy with changeset for review
      - run: >
          sam deploy
          --stack-name myapp
          --resolve-s3
          --capabilities CAPABILITY_IAM
          --confirm-changeset false
          --no-fail-on-empty-changeset
          --parameter-overrides Env=production

      # Output API endpoint
      - run: sam list stack-outputs --stack-name myapp
```

**SAM with layers (shared deps):**
```yaml
- run: sam build --cached --parallel --use-container  # Build in container for Linux compat
```

**SAM local testing in CI:**
```yaml
# Integration test against local Lambda runtime
- run: sam local invoke MyFunction --event events/test.json
- run: sam local start-api &
- run: sleep 5 && curl http://localhost:3000/health
```

### CDK Pipeline (Self-Mutating)

Source: https://docs.aws.amazon.com/cdk/v2/guide/cdk_pipeline.html

```typescript
// lib/pipeline-stack.ts
import { CodePipeline, CodePipelineSource, ShellStep } from 'aws-cdk-lib/pipelines';

export class PipelineStack extends Stack {
  constructor(scope: Construct, id: string) {
    super(scope, id);

    const pipeline = new CodePipeline(this, 'Pipeline', {
      pipelineName: 'MyAppPipeline',
      crossAccountKeys: true,
      synth: new ShellStep('Synth', {
        input: CodePipelineSource.gitHub('org/repo', 'main'),
        commands: ['npm ci', 'npx cdk synth'],
      }),
      dockerEnabledForSynth: true,  // If synth needs Docker
    });

    // Self-mutating: pipeline updates itself when stack changes
    const stage = pipeline.addStage(new MyAppStage(this, 'Production', {
      env: { account: '123456789012', region: 'us-east-1' },
    }));

    stage.addPost(
      new ShellStep('IntegrationTest', {
        commands: ['npm run test:integration'],
      }),
    );
  }
}
```

**CDK Pipeline with GitHub source (via CodeStar Connection):**
```yaml
# CDK CLI deploy (simpler alternative)
- run: npx cdk deploy --require-approval never --exclusively
  env:
    CDK_DEFAULT_ACCOUNT: ${{ secrets.AWS_ACCOUNT }}
    CDK_DEFAULT_REGION: us-east-1
```

### Serverless Framework

```yaml
# .github/workflows/serverless-deploy.yml
name: Serverless Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '20' }
      - run: npm ci

      # Deploy all stages
      - run: npx serverless deploy --stage production --region us-east-1
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

      # Or with OIDC (preferred)
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1
      - run: npx serverless deploy --stage production
```

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
    - 'node_modules/aws-sdk/**'  # Only if not in Lambda runtime
```

## Step 16: Preview Environments (Ephemeral per PR)

Source: https://docs.github.com/en/actions/managing-issues-and-pull-requests/adding-labels-to-issues

Preview environments spin up isolated infrastructure per PR for visual review and integration testing, auto-destroyed on merge/close.

### GitHub Actions Preview Environment

```yaml
# .github/workflows/preview.yml
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

      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1

      # Deploy unique stack per PR
      - run: |
          STACK="myapp-pr-${{ github.event.pull_request.number }}"
          sam deploy \
            --stack-name "$STACK" \
            --resolve-s3 \
            --capabilities CAPABILITY_IAM \
            --parameter-overrides \
              PrNumber=${{ github.event.pull_request.number }} \
              Environment=preview

      - run: |
          ENDPOINT=$(aws cloudformation describe-stacks \
            --stack-name "myapp-pr-${{ github.event.pull_request.number }}" \
            --query 'Stacks[0].Outputs[?OutputKey==`ApiUrl`].OutputValue' \
            --output text)
          echo "### Preview URL" >> $GITHUB_STEP_SUMMARY
          echo "$ENDPOINT" >> $GITHUB_STEP_SUMMARY

  # Comment URL on PR
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
              body: `🚀 Preview deployed: https://pr-${context.issue.number}.preview.example.com`
            })

  # Destroy on PR close/merge
  destroy-preview:
    if: github.event.action == 'closed'
    runs-on: ubuntu-latest
    steps:
      - uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1
      - run: aws cloudformation delete-stack --stack-name "myapp-pr-${{ github.event.pull_request.number }}"
```

### Kubernetes Preview (Namespace per PR)

```yaml
deploy-preview:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - run: |
        NS="pr-${{ github.event.pull_request.number }}"
        kubectl create namespace "$NS" --dry-run=client -o yaml | kubectl apply -f -
        helm upgrade --install myapp ./chart \
          --namespace "$NS" \
          --set ingress.host="pr-${{ github.event.pull_request.number }}.preview.example.com" \
          --set image.tag="${{ github.sha }}"
```

**Cleanup with TTL controller:**
```yaml
# Add TTL to auto-delete stale preview namespaces
apiVersion: v1
kind: Namespace
metadata:
  name: pr-123
  annotations:
    expiry: "72h"  # Controller deletes after 72h
```

### Vercel/Netlify Preview (Simpler SaaS)

```yaml
# Vercel auto-deploys PRs — just configure Vercel GitHub App
# For custom domains per PR:
- run: npx vercel deploy --yes --token=${{ secrets.VERCEL_TOKEN }}
  env:
    VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
    VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

**Preview env best practices:**
- Use concurrency groups — cancel stale preview deploys on new push
- Always comment URL on PR for easy access
- Auto-destroy on PR close/merge (prevent resource leaks)
- Use lightweight infra (single container, SQLite) for preview — not full production stack
- Set TTL (72h max) for safety net if webhook misses close event
- Monitor costs — cap preview envs per org

## Step 17: AI in CI/CD

Source: https://dora.dev/research/2024-dora-report/

### Automated Test Generation in Pipeline

```yaml
# Generate tests for changed files, post as PR suggestion
- name: AI test generation
  uses: coderabbitai/ai-pr-reviewer@v1
  with:
    generate-tests: true
    test-framework: jest
    review-draft: true  # Also review draft PRs
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```

**Standalone AI test gen (aider in CI):**
```yaml
- name: Generate missing tests
  run: |
    pip install aider-chat
    CHANGED=$(git diff --name-only origin/main...HEAD -- '*.py' '*.ts' '*.go')
    for file in $CHANGED; do
      test_file=$(echo "$file" | sed 's/\.\(py\|ts\|go\)$/.test.\1/')
      if [ ! -f "$test_file" ]; then
        aider --yes --message "Write comprehensive tests for $file" "$file"
      fi
    done
```

### AI Code Review in Pipeline

```yaml
# CodeRabbit — full AI review
- uses: coderabbitai/ai-pr-reviewer@v1
  with:
    review-type: auto
    language: en
    # Focus areas
    path-instructions: |
      src/api/**: Security and input validation focus
      src/db/**: SQL injection and performance focus
      **/Dockerfile: Security best practices
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

# Sourcery — code quality + suggestions
- uses: sourcery-ai/sourcery-action@v1
  with:
    token: ${{ secrets.SOURCERY_TOKEN }}
    review: true
```

**Custom AI review with LLM API:**
```yaml
- name: AI security review
  run: |
    DIFF=$(git diff origin/main...HEAD)
    RESPONSE=$(curl -s https://api.openai.com/v1/chat/completions \
      -H "Authorization: Bearer ${{ secrets.OPENAI_API_KEY }}" \
      -H "Content-Type: application/json" \
      -d "{
        \"model\": \"gpt-4o\",
        \"messages\": [{
          \"role\": \"system\",
          \"content\": \"Review this diff for security vulnerabilities. Output JSON: {\\\"issues\\\": [{\\\"file\\\": ..., \\\"line\\\": ..., \\\"severity\\\": ..., \\\"description\\\": ...}]}\"},
          {\"role\": \"user\", \"content\": $(echo "$DIFF" | jq -Rs .)}
        ]
      }")
    ISSUES=$(echo "$RESPONSE" | jq -r '.choices[0].message.content' | jq '.issues | length')
    if [ "$ISSUES" -gt 0 ]; then
      echo "::warning::AI found $ISSUES potential issues"
      echo "$RESPONSE" | jq -r '.choices[0].message.content'
    fi
```

### AI-Powered Flaky Test Detection

```yaml
- name: Detect flaky tests
  run: |
    # Run tests 3 times, flag inconsistency
    for i in 1 2 3; do
      npm test -- --json --outputFile=results-$i.json || true
    done
    npx ai-flaky-detector compare results-*.json > flaky-report.json
    FLAKY_COUNT=$(jq '.flaky | length' flaky-report.json)
    if [ "$FLAKY_COUNT" -gt 0 ]; then
      echo "::warning::Found $FLAKY_COUNT flaky tests"
      jq -r '.flaky[] | "- \(.name): \(.reason)"' flaky-report.json
    fi
```

**DORA 2024 key findings on AI in CI/CD:**
- AI that *suggests* (test gen, review hints) improves throughput
- AI that *replaces* human review gates increases change failure rate
- Best pattern: AI generates suggestions, human approves before merge
- AI review catches 30-40% of issues missed by static analysis alone
- AI test gen increases coverage but needs human curation

## Step 18: Multi-Platform Builds (ARM64 + x86_64)

Source: https://docs.docker.com/build/building/multi-platform/

### Docker Buildx Multi-Platform

```yaml
# Build for both ARM64 and x86_64
- uses: docker/setup-qemu-action@v3
  with:
    platforms: linux/amd64,linux/arm64

- uses: docker/setup-buildx-action@v3

- uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}

- uses: docker/build-push-action@v5
  with:
    push: true
    platforms: linux/amd64,linux/arm64
    tags: |
      ghcr.io/org/myapp:latest
      ghcr.io/org/myapp:${{ github.sha }}
    cache-from: type=gha
    cache-to: type=gha,mode=max
```

**Multi-platform Dockerfile (platform-aware):**
```dockerfile
FROM --platform=$BUILDPLATFORM node:20-slim AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-slim AS runner
# $TARGETPLATFORM is set automatically by buildx
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
CMD ["node", "dist/server.js"]
```

**Go multi-platform (native cross-compile, no QEMU):**
```dockerfile
FROM --platform=$BUILDPLATFORM golang:1.22 AS builder
ARG TARGETOS TARGETARCH
WORKDIR /app
COPY go.* .
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -o /app/server .

FROM gcr.io/distroless/static:nonroot
COPY --from=builder /app/server /server
CMD ["/server"]
```

```yaml
# Go: cross-compile natively (fast, no QEMU overhead)
- uses: docker/build-push-action@v5
  with:
    platforms: linux/amd64,linux/arm64
    # Buildx uses $BUILDPLATFORM for build stage, $TARGETPLATFORM for final
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

### Matrix Build for Native Tests

```yaml
# Test natively on each platform (faster than QEMU)
test:
  strategy:
    matrix:
      include:
        - os: ubuntu-latest
          platform: linux/amd64
        - os: ubuntu-24.04-arm  # GitHub ARM runners (preview)
          platform: linux/arm64
  runs-on: ${{ matrix.os }}
  steps:
    - uses: actions/checkout@v4
    - run: make test
```

**Multi-platform checklist:**
- [ ] Use `$BUILDPLATFORM` for build stage (native speed)
- [ ] Use `$TARGETPLATFORM` only for final runtime stage
- [ ] Go: use `CGO_ENABLED=0 GOOS/$GOARCH` — no QEMU needed
- [ ] Rust: cross-compile with `--target`, avoid QEMU for compilation
- [ ] Node/Python: QEMU required unless using native ARM runners
- [ ] Test on both platforms — use matrix with ARM runners or QEMU
- [ ] Manifest list auto-created when pushing multi-platform

## Step 19: Advanced Dependency Caching

### Go Module Cache

```yaml
- uses: actions/setup-go@v5
  with:
    go-version: '1.22'
    cache: true  # Auto-caches ~/go/pkg/mod and ~/.cache/go-build
```

**Manual Go cache (more control):**
```yaml
- uses: actions/cache@v4
  with:
    path: |
      ~/go/pkg/mod
      ~/.cache/go-build
    key: go-${{ runner.os }}-${{ hashFiles('**/go.sum') }}
    restore-keys: go-${{ runner.os }}-
```

### Cargo Registry Cache (Rust)

```yaml
- uses: actions/cache@v4
  with:
    path: |
      ~/.cargo/registry/index
      ~/.cargo/registry/cache
      ~/.cargo/git/db
      target/
    key: cargo-${{ runner.os }}-${{ hashFiles('**/Cargo.lock') }}
    restore-keys: cargo-${{ runner.os }}-
```

**Cargo with Swatinem/rust-cache (simpler):**
```yaml
- uses: Swatinem/rust-cache@v2
  with:
    workspaces: "." -> target
    cache-on-failure: true  # Cache even if build fails
```

### pnpm Store Cache

```yaml
- uses: pnpm/action-setup@v4
  with:
    version: 9

- uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'pnpm'  # Auto-caches pnpm store

# Or manual:
- uses: actions/cache@v4
  with:
    path: |
      ~/.local/share/pnpm/store
      node_modules/.pnpm
    key: pnpm-${{ runner.os }}-${{ hashFiles('**/pnpm-lock.yaml') }}
    restore-keys: pnpm-${{ runner.os }}-
```

### pip Cache (Python)

```yaml
- uses: actions/setup-python@v5
  with:
    python-version: '3.12'
    cache: 'pip'  # Auto-caches ~/.cache/pip

# Or with poetry:
- uses: actions/setup-python@v5
  with:
    python-version: '3.12'
    cache: 'poetry'

# Or with uv (fast Python package manager):
- run: pip install uv
- uses: actions/cache@v4
  with:
    path: ~/.cache/uv
    key: uv-${{ runner.os }}-${{ hashFiles('**/uv.lock') }}
```

### Gradle Cache (JVM)

```yaml
- uses: actions/setup-java@v4
  with:
    distribution: 'temurin'
    java-version: '21'
    cache: 'gradle'  # Auto-caches ~/.gradle/caches and ~/.gradle/wrapper
```

### Maven Cache (JVM)

```yaml
- uses: actions/setup-java@v4
  with:
    distribution: 'temurin'
    java-version: '21'
    cache: 'maven'  # Auto-caches ~/.m2/repository
```

### Composite Cache Pattern (Multi-Deps)

```yaml
# Monorepo with Go + Node + Python
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

**Cache strategy guidelines:**
- Use lockfile hash in key — deterministic invalidation
- Always set `restore-keys` — partial cache hit > no cache
- Cache `target/` for Rust (huge build time savings)
- Don't cache `node_modules` in npm projects — use `~/.npm` + `npm ci`
- DO cache `node_modules` for pnpm/yarn (faster than `pnpm install`)
- Monitor cache size — GitHub limits 10GB per repo
- Use `actions/cache/save` and `actions/cache/restore` separately for fine-grained control

## Step 20: Pipeline Security Hardening

Source: https://docs.github.com/en/actions/security-for-github-actions/security-hardening-for-github-actions

### OIDC for Cloud Authentication

Replace long-lived static credentials with short-lived OIDC tokens. No secrets to rotate or leak.

**AWS:**
```yaml
permissions:
  id-token: write
  contents: read

steps:
  - uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: arn:aws:iam::123456789012:role/GitHubActionsRole
      aws-region: us-east-1
      # Optional: restrict to specific repo/branch
      audience: sts.amazonaws.com
```

AWS IAM role trust policy — limit to repo + environment:
```json
{
  "Condition": {
    "StringEquals": {
      "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
    },
    "StringLike": {
      "token.actions.githubusercontent.com:sub": "repo:org/repo:ref:refs/heads/main"
    }
  }
}
```

**GCP Workload Identity Federation:**
```yaml
- uses: google-github-actions/auth@v2
  with:
    workload_identity_provider: projects/123/locations/global/workloadIdentityPools/github/providers/github
    service-account: deploy@project.iam.gserviceaccount.com
```

**Azure Federated Credentials:**
```yaml
- uses: azure/login@v2
  with:
    client-id: ${{ secrets.AZURE_CLIENT_ID }}
    tenant-id: ${{ secrets.AZURE_TENANT_ID }}
    subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
```

**HashiCorp Vault via OIDC:**
```yaml
- uses: hashicorp/vault-action@v3
  with:
    url: https://vault.example.com
    method: jwt
    role: ci-deployer
    secrets: |
      secret/data/ci/deploy token | DEPLOY_TOKEN
```

**OIDC hardening checklist:**
- [ ] One IAM role per repo (not shared across repos)
- [ ] Condition on branch/environment (not just repo)
- [ ] Short session duration (15-60 min, not 12h)
- [ ] Separate roles: read-only for CI, write for deploy
- [ ] Audit OIDC token issuance in cloud trail

### Secrets Rotation

```yaml
# GitHub: use environment-level secrets (rotatable per environment)
# Rotate on schedule via API or Terraform

# AWS Secrets Manager rotation (Lambda-based)
# GCP Secret Manager with automatic rotation
# Azure Key Vault rotation policies

# Rotation automation script
- name: Rotate secrets
  if: github.event.schedule  # Cron-triggered
  run: |
    # Generate new secret
    NEW_TOKEN=$(openssl rand -hex 32)
    # Update via CLI
    gh secret set DEPLOY_TOKEN --body "$NEW_TOKEN"
    # Update in cloud vault
    aws secretsmanager rotate-secret --secret-id ci/deploy-token
```

**Secrets hygiene:**
- [ ] No secrets in env vars for logging steps — use `::add-mask::`
- [ ] Environment-scoped secrets (not repo-wide)
- [ ] `pull_request` trigger never receives production secrets
- [ ] Dependabot secrets separate from CI secrets
- [ ] Rotate all CI secrets quarterly minimum
- [ ] Scan for leaked secrets in every pipeline (trivy/gitleaks)
- [ ] Use ephemeral secrets (OIDC tokens) where possible

### Audit Logging

```yaml
# GitHub Actions audit events (enterprise/org level)
# Enable: Settings > Security > Audit log > Actions events

# Log all workflow runs to external SIEM
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
      \"conclusion\": \"${{ github.event.workflow_run.conclusion }}\",
      \"timestamp\": \"$(date -u +%Y-%m-%dT%H:%M:%SZ)\"
    }"
```

**Audit requirements:**
- [ ] All workflow runs logged (who, what, when, outcome)
- [ ] Secret access logged (which secrets, by which workflow)
- [ ] Environment protection rule events logged
- [ ] Artifact uploads/downloads logged
- [ ] Retention: minimum 90 days, 1 year for compliance (SOC2/ISO27001)
- [ ] Alert on: first-time secret usage, unusual actor, off-hours deploy

**GitHub enterprise audit log API:**
```bash
gh api /organizations/ORG_ID/audit-log \
  --jq '.[] | select(.action | startswith("actions."))' \
  --paginate
```

## Step 21: Build Reproducibility

Source: https://reproducible-builds.org/, https://slsa.dev/

### Deterministic Builds

Same source + same inputs = identical binary. Every time.

**Principles:**
- Pin ALL dependency versions (lockfiles, no ranges)
- Pin toolchain versions (compiler, runtime, OS packages)
- Eliminate timestamps from builds
- Use `SOURCE_DATE_EPOCH` for reproducible dates
- Disable network during build phase (hermetic)
- Sort file lists (directory traversal order varies)

```dockerfile
# Deterministic Node.js build
FROM node:20.11.0-slim@sha256:abc123... AS builder  # Pin image + digest
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --ignore-scripts  # Exact install, no scripts
COPY . .
ENV SOURCE_DATE_EPOCH=1
RUN npm run build
# Normalize timestamps
RUN find dist -exec touch -t 197001010000.00 {} +
```

```dockerfile
# Deterministic Go build
FROM golang:1.22.0@sha256:def456... AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build \
    -trimpath \              # Remove local paths
    -buildvcs=false \        # Remove VCS info
    -ldflags="-s -w -buildid=" \  # Strip build ID
    -o /app/server .
```

### Hermetic Builds

No network access during build. All inputs declared upfront.

```yaml
# Nix-based hermetic build
- uses: cachix/install-nix-action@v25
- run: nix build --option sandbox true  # Network isolated in sandbox

# Bazel hermetic build
- run: bazel build //... --remote_download_minimal --incompatible_strict_action_env

# Docker hermetic build (--network=none)
- run: |
    docker buildx build \
      --network=none \           # No network during build
      --output type=local,dest=./output \
      .
```

**Hermetic build checklist:**
- [ ] All deps vendored or cached before build starts
- [ ] Build tool pinned to exact version + hash
- [ ] No `curl`/`wget`/`pip install` during build phase
- [ ] `--network=none` for Docker builds
- [ ] Build sandbox enabled (Bazel, Nix)
- [ ] Environment variables sanitized (`--incompatible_strict_action_env`)

### Build Provenance

Prove who built what, how, from which source.

```yaml
# SLSA provenance (covered in Step 10, advanced patterns below)
# GitHub attestation (built-in)
- uses: actions/attest-build-provenance@v2
  with:
    subject-name: ghcr.io/org/myapp
    subject-digest: ${{ steps.build.outputs.digest }}
    push-to-registry: true

# Verify provenance
# gh attestation verify oci://ghcr.io/org/myapp@sha256:... --owner org
```

**In-toto attestation (custom predicates):**
```yaml
- uses: actions/attest-build-provenance@v2
  with:
    subject-path: './dist/**'
    predicate-type: https://example.com/custom/v1
    predicate: |
      {
        "builder": "github-actions",
        "tests_passed": true,
        "vulnerability_scan": "clean",
        "compliance_checks": ["soc2", "hipaa"]
      }
```

**Provenance verification in deploy pipeline:**
```yaml
deploy:
  steps:
    - name: Verify provenance
      run: |
        gh attestation verify oci://${IMAGE}@${DIGEST} \
          --owner org \
          --signer-repo org/repo \
          --predicate-type https://slsa.dev/provenance/v0.2
    - name: Deploy only if verified
      run: kubectl apply -f k8s/
```

## Step 22: Pipeline Observability

### Build Metrics

Track CI performance over time. Identify regressions.

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
          \"tags\": [\"repo:${{ github.repository }}\", \"workflow:${{ github.workflow }}\", \"conclusion:${{ github.event.workflow_run.conclusion }}\"]
        }]
      }"
```

**Key build metrics to track:**
- Build duration (p50, p95, p99) per workflow
- Cache hit rate (actions/cache)
- Queue time (time waiting for runner)
- Failure rate per workflow/job
- Flaky test rate
- Resource usage (runner CPU/memory)

**GitHub Actions metrics API:**
```bash
# Workflow run timing
gh api /repos/{owner}/{repo}/actions/runs \
  --jq '.workflow_runs[] | {name: .name, duration: (.updated_at | fromdateiso8601) - (.run_started_at | fromdateiso8601), conclusion: .conclusion}'
```

### Test Analytics

```yaml
# Upload test results for analysis
- name: Upload test results
  if: always()
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: test-results/
    retention-days: 30

# Playwright test reporter
- uses: dorny/test-reporter@v1
  if: always()
  with:
    name: Test Results
    path: '**/test-results.xml'
    reporter: java-junit

# Codecov for coverage trends
- uses: codecov/codecov-action@v4
  with:
    token: ${{ secrets.CODECOV_TOKEN }}
    fail_ci_if_error: true
    flags: unittests
```

**Test analytics dashboard metrics:**
- Test pass/fail rate over time
- Slowest tests (optimization targets)
- Flaky test detection (pass → fail → pass within 24h)
- Coverage trend (line, branch, function)
- Test duration regression alerts

### Deployment Tracking

```yaml
# Track every deployment
- name: Record deployment
  if: success() && github.ref == 'refs/heads/main'
  run: |
    curl -X POST "${{ secrets.DEPLOYMENT_TRACKER_URL }}" \
      -H "Content-Type: application/json" \
      -d "{
        \"environment\": \"production\",
        \"version\": \"${{ github.sha }}\",
        \"tag\": \"${{ github.ref_name }}\",
        \"deployer\": \"${{ github.actor }}\",
        \"commit_message\": \"$(git log -1 --pretty=%B | head -1 | sed 's/\"/\\\\"/g')\",
        \"pipeline_url\": \"${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}\",
        \"timestamp\": \"$(date -u +%Y-%m-%dT%H:%M:%SZ)\"
      }"
```

**Deployment tracking checklist:**
- [ ] Every deployment recorded (env, version, who, when)
- [ ] DORA metrics auto-calculated from deployment records
- [ ] Rollback events tracked separately
- [ ] Correlation: deployment → error rate spike → rollback
- [ ] ChatOps notification (Slack/Teams) on every deploy
- [ ] Deployment frequency dashboard
- [ ] Lead time: commit timestamp → deploy timestamp

## Step 23: Monorepo CI Patterns

Source: https://bazel.build/, https://nx.dev/, https://turbo.build/

### Affected-Only Builds

Only build/test what changed. Critical for monorepo performance.

**Nx (JavaScript/TypeScript):**
```yaml
# .github/workflows/monorepo-ci.yml
- uses: nrwl/nx-set-shas@v4
  with:
    main-branch-name: main

- run: npx nx affected -t lint test build --base=${{ env.NX_BASE }} --head=${{ env.NX_HEAD }}
```

**Turborepo:**
```yaml
- run: npx turbo run lint test build --filter=...[origin/main]
```

**Bazel (any language):**
```yaml
- run: |
    bazel test \
      --keep_going \
      --test_output=errors \
      $(bazel query 'kind("test", rdeps(//..., set($(git diff --name-only origin/main HEAD))))')
```

**Git-based affected detection (manual):**
```yaml
- name: Detect affected packages
  id: affected
  run: |
    CHANGED=$(git diff --name-only origin/main...HEAD)
    PACKAGES=$(echo "$CHANGED" | cut -d/ -f1-2 | sort -u)
    echo "packages=$PACKAGES" >> $GITHUB_OUTPUT
    echo "Changed packages: $PACKAGES"

- name: Build affected only
  run: |
    for pkg in ${{ steps.affected.outputs.packages }}; do
      if [ -f "$pkg/package.json" ]; then
        echo "Building $pkg"
        cd "$pkg" && npm ci && npm run build && cd -
      fi
    done
```

### Incremental Testing

Only run tests for changed code + its dependents.

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

**Incremental test strategies:**
- [ ] Dependency graph aware (Nx, Bazel, Turborepo)
- [ ] Test impact analysis (only run tests covering changed lines)
- [ ] Parallel execution (Nx `--parallel`, `pytest -n auto`)
- [ ] Skip unchanged packages entirely
- [ ] Always run full suite on main (nightly or post-merge)

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

# GitHub Actions cache for monorepo (scoped)
- uses: actions/cache@v4
  with:
    path: |
      apps/web/node_modules
      apps/api/node_modules
      packages/shared/node_modules
    key: monorepo-${{ runner.os }}-${{ hashFiles('pnpm-lock.yaml') }}
```

**Monorepo CI checklist:**
- [ ] Use Nx/Turborepo/Bazel for dependency graph awareness
- [ ] Affected-only builds on PRs, full builds on main
- [ ] Remote/distributed cache for build artifacts (Nx Cloud, Turbo cache)
- [ ] Parallel execution across affected packages
- [ ] CODEOWNERS for package-level review
- [ ] Path filters per workflow (e.g., `apps/web/**` triggers frontend CI only)
- [ ] Nightly full test suite (catch drift from incremental)

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

## Step 24: Pipeline Cost Optimization

Source: https://docs.github.com/en/billing/managing-billing-for-github-actions

### Runner Selection

Choose runners by actual need, not habit.

| Runner | Cost (GitHub Actions) | Use For |
|--------|----------------------|---------|
| ubuntu-latest | 1x (baseline) | Linux builds, Docker, most CI |
| ubuntu-24.04-arm | 1x | ARM builds (native, no QEMU) |
| macos-13 | 10x | macOS/iOS builds only |
| macos-13-xlarge (M1) | 20x | Apple Silicon builds only |
| windows-latest | 2x | Windows builds only |
| Larger runners | 2-10x | Memory-heavy builds, many parallel jobs |

**Cost-saving rules:**
- [ ] Default to `ubuntu-latest` — macOS/Windows only when required
- [ ] Use ARM runners (same price as x86) for ARM builds instead of QEMU
- [ ] Self-hosted runners for heavy workloads (owned hardware, zero per-minute)
- [ ] Larger runners only if build OOMs on standard — test first
- [ ] macOS: use `macos-13` (Intel) when Apple Silicon not needed

### Cache Strategies

```yaml
# Cache aggressively, restore cheaply
# 1. Save cache only on main (PRs restore only)
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

# 2. Docker BuildKit cache (GHA backend)
- uses: docker/build-push-action@v5
  with:
    cache-from: type=gha
    cache-to: type=gha,mode=max  # Cache ALL layers

# 3. Conditional cache restore (skip on cache hit)
- id: cache
  uses: actions/cache@v4
  with:
    path: ~/.cache/go-build
    key: go-${{ hashFiles('go.sum') }}

- run: go build ./...
  if: steps.cache.outputs.cache-hit != 'true'
```

**Cache cost rules:**
- [ ] GitHub cache limit: 10GB per repo — monitor usage
- [ ] Eviction policy: LRU — unused caches auto-deleted after 7 days
- [ ] `cache-hit` output to skip expensive steps when cache warm
- [ ] Save cache on main only — PRs inherit via `restore-keys`
- [ ] Scope caches per OS + architecture (`${{ runner.os }}-${{ runner.arch }}`)
- [ ] Docker BuildKit cache (`type=gha`) — saves pull time on every build

### Parallelism Tuning

```yaml
# Matrix parallelism — balance speed vs cost
strategy:
  matrix:
    shard: [1, 2, 3, 4]  # 4x cost, ~4x speed
  fail-fast: false

# Job-level parallelism — split expensive stages
jobs:
  lint:
    runs-on: ubuntu-latest        # Fast, cheap
  unit-test:
    needs: lint
    runs-on: ubuntu-latest
    strategy:
      matrix:
        shard: [1, 2]
  integration-test:
    needs: lint
    runs-on: ubuntu-latest        # Parallel with unit tests
    services:
      postgres:
        image: postgres:16
  build:
    needs: [unit-test, integration-test]
    runs-on: ubuntu-latest
```

**Parallelism guidelines:**
- [ ] Default: 2-4 shards for tests (diminishing returns beyond 8)
- [ ] Use `fail-fast: false` — don't cancel on first failure (wastes the partial run)
- [ ] Concurrency groups — cancel stale runs (saves compute)
- [ ] Estimate: 4 shards costs 4x but finishes ~3.5x faster (scheduling overhead)
- [ ] Nightly/weekly heavy jobs — don't run full matrix on every PR

```yaml
# Concurrency: cancel stale PR runs
concurrency:
  group: ${{ github.workflow }}-${{ github.event.pull_request.number || github.ref }}
  cancel-in-progress: true

# Schedule heavy jobs off-peak
on:
  schedule:
    - cron: '0 3 * * 1'  # Weekly Monday 3am UTC
```

**Cost optimization checklist:**
- [ ] Monitor Actions minutes usage (org billing page)
- [ ] Set per-repo minutes limit (org settings)
- [ ] Use `paths:` filters — skip CI for docs/config-only changes
- [ ] Schedule nightly full suite, skip on PRs
- [ ] Reuse artifacts between jobs (`actions/upload-artifact` + `download`)
- [ ] Self-hosted runners for high-volume repos
- [ ] Audit expensive workflows monthly (sort by minutes consumed)
