---
name: sdlc-cicd-pipeline
description: "CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix builds, test sharding, security scanning, GitOps, DORA metrics, trunk-based development, anti-patterns. SLSA L3 supply chain, SBOM generation, Green CI/CD, AI in pipelines, GitHub Actions hardening. Serverless CI/CD (SAM/CDK/Serverless Framework), preview environments, multi-platform builds, advanced dependency caching. Pipeline security hardening, build reproducibility, pipeline observability, monorepo CI patterns, pipeline cost optimization. FinOps for CI/CD, Green CI/CD (SCI), pipeline governance. Deployment at scale (Spinnaker/Argo Rollouts/Flagger), multi-environment management, database CI/CD, progressive delivery."
version: 4.6.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, ci-cd, github-actions, gitlab-ci, docker, devops, pipeline, gitops, dora, accelerate, trunk-based, slsa, sbom, supply-chain, green-ci, security-hardening, serverless, preview-environments, multi-platform, ai-cicd, oidc, secrets-rotation, audit-logging, reproducible-builds, hermetic-builds, build-provenance, build-observability, monorepo-ci, cost-optimization, finops, green-cicd, sci, pipeline-governance, compliance-gates, deployment-at-scale, spinnaker, argo-rollouts, flagger, progressive-delivery, canary-deployments, database-cicd, schema-migration, multi-environment, environment-as-code]
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

## Step 25: FinOps for CI/CD

Source: https://www.finops.org/framework/ | https://focus.finops.org/

FinOps applies financial accountability to cloud spend. CI/CD is often 10-30% of cloud compute costs.

### FinOps Foundation Framework

Three phases, continuous cycle:

**Inform:**
- Visibility into CI/CD spend by team, repo, workflow, environment
- Showback reports (who spends what, no chargeback yet)
- Benchmark cost per build, cost per deployment, cost per test run
- Link CI minutes to business outcomes (cost per feature, cost per release)

**Optimize:**
- Right-size runners (don't use macOS if Linux suffices)
- Eliminate waste: unused caches, redundant workflows, idle self-hosted runners
- Autoscale self-hosted runners to zero when idle
- Spot/preemptible instances for non-critical jobs

**Operate:**
- Budget alerts per team/repo
- Chargeback: teams pay from their cloud budget for CI usage
- Continuous cost review in sprint retrospectives
- Governance policies (max runner size, max matrix width)

### FOCUS Specification

FinOps Open Cost and Usage Specification. Standardized billing data format.

```yaml
# Map CI/CD costs to FOCUS dimensions
# FOCUS columns relevant to CI/CD:
# - BillingAccountId / BillingAccountName
# - ServiceName: "GitHub Actions", "GitLab CI", "CircleCI"
# - ChargeCategory: "Usage", "Commitment"
# - EffectiveCost: actual cost after discounts
# - ConsumedQuantity: minutes consumed
# - ConsumedUnit: "minutes"

# Export billing data to FOCUS format for unified reporting
# GitHub: use Billing API + webhooks
# GitLab: use CI/CD minutes API
```

### Cost Allocation

**Tagging strategy for CI/CD resources:**

```yaml
# GitHub Actions — use repository/environment tags
# All cost tags: team, project, cost-center, environment
env:
  COST_TEAM: platform-engineering
  COST_PROJECT: payment-service
  COST_CENTER: CC-1042
  COST_ENV: ${{ github.ref == 'refs/heads/main' && 'production' || 'development' }}

# Self-hosted runners — label with cost metadata
# Runner labels: team=platform, cost-center=CC-1042
```

**Showback model:**
- Weekly reports: CI minutes per team, per repo, per environment
- Trend analysis: cost trajectory over sprints
- Anomaly detection: sudden cost spikes trigger alerts

**Chargeback model:**
- Teams own CI budget from cloud allocation
- Overage requires approval from platform lead
- Reserved capacity pools shared across teams (discount leverage)

### CI Runner Cost Optimization

```yaml
# Tiered runner strategy
# Tier 1: Free/cheap (default)
# - ubuntu-latest, ubuntu-24.04-arm (GitHub hosted, 1x cost)
# - Self-hosted on owned hardware (0 per-minute)

# Tier 2: Moderate (when needed)
# - windows-latest (2x) — only for Windows-specific builds
# - Larger runners (2-4x) — only when standard runners OOM

# Tier 3: Expensive (minimize)
# - macos-13 (10x) — only for macOS/iOS native builds
# - macos-13-xlarge M1 (20x) — only for Apple Silicon validation

# Self-hosted autoscaling (Kubernetes-based)
# Scale to zero when no jobs queued
# Scale up on demand, scale down after 5min idle
# Spot instances for non-release builds
```

**Cost optimization checklist:**
- [ ] Tag all CI resources with team/cost-center/project
- [ ] Generate monthly showback reports
- [ ] Set per-team CI minutes budgets with alerts
- [ ] Default to cheapest runner tier, escalate only on failure
- [ ] Use spot/preemptible for self-hosted runners (non-release)
- [ ] Monitor cost per build metric — track over time
- [ ] Review and eliminate unused workflows monthly

## Step 26: Green CI/CD

Source: https://greensoftware.foundation/ | https://sci-guide.greensoftware.foundation/

### SCI Formula

Software Carbon Intensity. ISO-standardized (ISO 21031).

```
SCI = ((E * I) + M) per R

Where:
  E = Energy consumed by software (kWh)
  I = Location-based carbon intensity (gCO2eq/kWh)
  M = Embodied carbon (gCO2eq) — hardware lifecycle emissions
  R = Functional unit (e.g., per build, per request, per user)

Lower SCI = greener software.
```

For CI/CD specifically:
```
SCI_pipeline = ((runner_energy_kWh * grid_carbon_intensity) + embodied_carbon) / total_builds
```

### Energy-Efficient CI Patterns

**Build optimization (less compute = less energy):**
- Incremental builds — only rebuild what changed
- Build caching — avoid redundant compilation
- Test impact analysis — only run tests affected by changes
- Aggressive parallelism reduces wall-clock time (but total energy similar)

```yaml
# Incremental Docker builds with BuildKit
- uses: docker/build-push-action@v5
  with:
    cache-from: type=gha
    cache-to: type=gha,mode=max  # Cache all layers, skip unchanged

# Test impact analysis (example with Jest)
- run: npx jest --onlyChanged  # Only tests affected by changed files

# Path-based filtering — skip CI entirely for irrelevant changes
on:
  push:
    paths-ignore:
      - '**.md'
      - 'docs/**'
      - '.github/CODEOWNERS'
```

**Runtime optimization:**
- Use slim/minimal base images (smaller = faster pull = less energy)
- Multi-stage builds — final image has only what's needed
- Remove dev dependencies from production images

### Carbon-Aware Scheduling

Shift compute to times/regions when grid is cleanest.

```yaml
# Carbon-aware scheduling with Electricity Maps or WattTime
# Run heavy jobs when grid carbon intensity is lowest

# Option 1: Schedule during low-carbon windows
on:
  schedule:
    - cron: '0 4 * * *'  # 4am UTC — often low demand, more renewables

# Option 2: Region-aware runner selection
# Pick runner region based on current carbon intensity
jobs:
  build:
    runs-on: ${{ steps.region.outputs.runner }}
    steps:
      - id: region
        run: |
          # Query carbon intensity API, pick cleanest region
          REGION=$(curl -s "https://api.electricitymap.org/v3/carbon-intensity/latest?zone=$ZONES" \
            | jq -r 'min_by(.carbonIntensity) | .zone')
          echo "runner=ubuntu-latest"  # Map zone to runner label
```

**Carbon-aware tools:**
- Electricity Maps API — real-time grid carbon intensity
- WattTime API — marginal emissions data
- Green Software Foundation SCI toolkit
- Kepler (Kubernetes-based Energy/Emissions Probe)

### ARM Runners

ARM uses less energy per instruction than x86 for equivalent workloads.

```yaml
# GitHub-hosted ARM runner (same price as x86)
jobs:
  build:
    runs-on: ubuntu-24.04-arm  # Native ARM, no QEMU overhead

# Self-hosted ARM (AWS Graviton, Ampere Altra)
# 60% less energy than equivalent x86, often 20% cheaper
jobs:
  build:
    runs-on: [self-hosted, linux, arm64]
```

**When to use ARM:**
- Native ARM targets (mobile, IoT, AWS Graviton deployments)
- Java, Go, Rust, Node.js — all have excellent ARM support
- Docker multi-arch builds — build ARM natively, skip QEMU emulation
- CI runners on Graviton/Altra instances — lower energy + lower cost

### Incremental Builds

```yaml
# Bazel — hermetic incremental builds
- run: bazel test //...  # Only rebuilds changed targets

# Nx (monorepo) — affected-only builds
- run: npx nx affected --target=test --base=origin/main

# Gradle — build cache + incremental compilation
- run: ./gradlew build --build-cache --parallel

# Go — module cache + incremental compilation
- uses: actions/cache@v4
  with:
    path: |
      ~/.cache/go-build
      ~/go/pkg/mod
    key: go-${{ hashFiles('go.sum') }}
```

**Green CI checklist:**
- [ ] Measure baseline: total CI minutes, estimated energy/carbon
- [ ] Enable build caching everywhere (Docker, deps, compilation)
- [ ] Use path filters — skip CI for docs/non-code changes
- [ ] Prefer ARM runners for compatible workloads
- [ ] Schedule heavy jobs during low-carbon grid hours
- [ ] Track SCI score per pipeline over time
- [ ] Report CI carbon footprint in quarterly sustainability reports

## Step 27: Pipeline Governance

Policy enforcement, compliance, auditability in CI/CD pipelines.

### Policy Enforcement in Pipelines

Enforce organizational rules automatically — no human gatekeepers needed.

```yaml
# OPA/Gatekeeper policy check in pipeline
jobs:
  policy-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: OPA policy evaluation
        run: |
          # Check infrastructure manifests against policy
          opa eval --data policies/ --input manifests/ \
            --format pretty 'data.cicd.deny[msg]'
      - name: Conftest (OPA-based)
        run: |
          conftest test --policy policies/ manifests/
          conftest test --policy policies/ docker-compose.yml

# Kyverno CLI for K8s policy validation
      - name: Kyverno policy check
        run: kyverno apply policies/ --resource manifests/
```

**Policy categories:**
- **Security policies:** no privileged containers, no root user, required labels
- **Compliance policies:** approved base images only, required SBOM, license allowlist
- **Operational policies:** resource limits required, health checks required
- **Cost policies:** max CPU/memory requests, no GPU without approval

### Compliance Gates

Automated gates that block non-compliant deployments.

```yaml
# Compliance gate job — runs after build, before deploy
jobs:
  compliance-gate:
    needs: build
    runs-on: ubuntu-latest
    steps:
      # 1. SBOM verification
      - name: Verify SBOM exists
        run: |
          if [ ! -f sbom.spdx.json ]; then
            echo "::error::SBOM missing — required for compliance"
            exit 1
          fi

      # 2. Vulnerability threshold
      - name: Vulnerability gate
        run: |
          CRITICAL=$(trivy image --format json myapp:${{ github.sha }} \
            | jq '[.Results[].Vulnerabilities[] | select(.Severity=="CRITICAL")] | length')
          if [ "$CRITICAL" -gt 0 ]; then
            echo "::error::$CRITICAL critical vulnerabilities found"
            exit 1
          fi

      # 3. License compliance
      - name: License check
        run: |
          # Reject copyleft licenses in production deps
          license-checker --production --failOn 'GPL-3.0;AGPL-3.0'

      # 4. Image provenance verification
      - name: Verify image signature
        run: |
          cosign verify ${{ env.REGISTRY }}/${{ env.IMAGE }}@${{ github.sha }}

      # 5. Approved base image check
      - name: Base image policy
        run: |
          # Only allow base images from approved registry
          BASE=$(grep '^FROM' Dockerfile | head -1 | awk '{print $2}')
          if [[ ! "$BASE" =~ ^ghcr\.io/org/ ]]; then
            echo "::error::Base image must come from approved registry"
            exit 1
          fi
```

### Audit Trails

Immutable record of who did what, when, with what artifacts.

```yaml
# Audit logging in pipeline
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      # Capture full pipeline context
      - name: Generate audit record
        run: |
          cat > audit-record.json <<EOF
          {
            "timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
            "pipeline_id": "${{ github.run_id }}",
            "commit_sha": "${{ github.sha }}",
            "actor": "${{ github.actor }}",
            "event": "${{ github.event_name }}",
            "ref": "${{ github.ref }}",
            "repository": "${{ github.repository }}",
            "workflow": "${{ github.workflow }}",
            "environment": "${{ inputs.environment }}",
            "image_digest": "${{ needs.build.outputs.digest }}",
            "sbom_hash": "$(sha256sum sbom.spdx.json | awk '{print $1}')",
            "policy_results": "$(cat policy-results.json)",
            "approvals": $(cat approvals.json)
          }
          EOF

      # Immutable audit log — append to transparency log
      - name: Record in transparency log
        run: |
          # Sigstore Rekor — public immutable log
          rekor-cli upload \
            --artifact audit-record.json \
            --artifact-type application/json \
            --public-key keys/org-signing.pub

      # Ship to SIEM/compliance system
      - name: Send to audit backend
        run: |
          curl -X POST "${{ secrets.AUDIT_ENDPOINT }}" \
            -H "Content-Type: application/json" \
            -d @audit-record.json
```

**Audit requirements:**
- Every deployment must record: actor, commit, image digest, policy results
- Audit logs are append-only (use Rekor, DynamoDB with immutability, or WORM storage)
- Retention: minimum 1 year (SOC 2), 7 years (financial regulations)
- Logs must be queryable for incident investigation

### Approval Workflows

Structured approval gates for high-risk deployments.

```yaml
# GitHub Environments with required reviewers
# Configure in repo settings: Settings > Environments > production

jobs:
  deploy-production:
    runs-on: ubuntu-latest
    environment:
      name: production
      url: https://app.example.com
    steps:
      - uses: actions/checkout@v4
      - run: ./deploy.sh

# Multi-stage approval with conditions
jobs:
  deploy-production:
    needs: [compliance-gate, staging-validation]
    runs-on: ubuntu-latest
    environment:
      name: production
    # Additional runtime checks before deploy
    steps:
      - name: Verify all gates passed
        run: |
          if [ "${{ needs.compliance-gate.result }}" != "success" ]; then
            echo "::error::Compliance gate must pass"
            exit 1
          fi
          if [ "${{ needs.staging-validation.result }}" != "success" ]; then
            echo "::error::Staging validation must pass"
            exit 1
          fi
      - run: ./deploy.sh

# GitLab: protected environments + manual approval
deploy_production:
  stage: deploy
  environment:
    name: production
    deployment_tier: production
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
      when: manual
      allow_failure: false
```

**Approval workflow patterns:**
- **Auto-deploy to staging:** no human approval needed (CI passes = deploy)
- **Manual approval for production:** required reviewer in environment settings
- **Separation of duties:** deployer cannot be code author
- **Time-based approvals:** approvals expire after 4 hours (re-verify)
- **Emergency bypass:** break-glass procedure with mandatory post-incident review

**Governance checklist:**
- [ ] Define policy-as-code (OPA/Conftest/Kyverno) for all compliance rules
- [ ] Compliance gates block non-compliant builds (not advisory)
- [ ] Every deployment has immutable audit trail
- [ ] Production requires human approval + separation of duties
- [ ] Audit logs retained per regulatory requirements
- [ ] Quarterly review of governance policies for relevance
- [ ] Emergency bypass documented with mandatory post-mortem

## Step 28: Deployment at Scale

Source: https://www.spinnaker.io/, https://argoproj.github.io/rollouts/, https://flagger.app/

### Spinnaker vs Argo Rollouts vs Flagger

| Feature | Spinnaker | Argo Rollouts | Flagger |
|---------|-----------|---------------|---------|
| **Scope** | Full CD platform (build, deploy, manage) | K8s-native rollout controller | Progressive delivery controller (Istio/Linkerd/NGINX/Gloo) |
| **Overhead** | Heavy — requires dedicated infra (GCP/AWS services, Redis, Clouddriver) | Lightweight — single CRD + controller in-cluster | Lightweight — single controller + metrics provider |
| **Canary** | Native canary stages with manual/auto judgement | Native Rollout CRD with weight-based traffic splitting | Native with automated metric analysis |
| **Analysis** | Manual gates or Kayenta (automated metric analysis) | RolloutAnalysis with Prometheus metrics | Built-in AnalysisTemplate with PromQL, Datadog, CloudWatch, New Relic |
| **Traffic Mgmt** | Integrated load balancers (AWS ALB/NLB, GCP LB) | Istio/NGINX/ALB traffic splitting via header/weight | Istio VirtualService, Linkerd TrafficSplit, NGINX Ingress annotations, Gloo Upstream |
| **GitOps** | External (Halyard config, not GitOps-native) | Native ArgoCD integration (same project) | Works with Flux or ArgoCD |
| **Multi-cloud** | First-class — AWS, GCP, Azure, K8s, Titus | K8s-only | K8s-only |
| **UI** | Rich built-in dashboard | Argo Rollouts UI (optional), ArgoCD integration | Grafana dashboards (no built-in UI) |
| **Learning Curve** | Steep — complex architecture | Moderate — CRD-based, familiar to K8s users | Moderate — requires metrics provider setup |
| **Best For** | Large orgs needing full CD platform with multi-cloud | Teams already on ArgoCD wanting K8s-native canary | Teams wanting automated metric-based promotion with existing service mesh |

### Decision Guide

```
Need multi-cloud CD platform?
  YES → Spinnaker (or evaluate ArgoCD + Rollouts + Workflows)
  NO  ↓
Already using ArgoCD?
  YES → Argo Rollouts (native integration, same project)
  NO  ↓
Have service mesh (Istio/Linkerd)?
  YES → Flagger (best mesh integration)
  NO  ↓
Want simplest setup?
  Argo Rollouts (CRD-only, no mesh required with NGINX Ingress)
  OR Flagger with NGINX Ingress (no mesh needed)
```

**When to pick each:**
- **Spinnaker:** 50+ microservices, multi-cloud, need deployment pipelines with approval stages, bake stages, manual judgement. Worth the infra cost at scale.
- **Argo Rollouts:** K8s-first, already using ArgoCD, want GitOps-native progressive delivery. Best ecosystem fit for modern K8s shops.
- **Flagger:** Metric-driven promotion is priority, already have Istio/Linkerd. Minimal config for automated canary with metric analysis.

### Argo Rollouts Quick Start

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  replicas: 5
  strategy:
    canary:
      steps:
        - setWeight: 20
        - pause: { duration: 5m }
        - setWeight: 40
        - pause: { duration: 5m }
        - setWeight: 60
        - pause: { duration: 5m }
        - setWeight: 80
        - pause: { duration: 5m }
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
        interval: 1m
      - name: request-duration
        thresholdRange:
          max: 500
        interval: 1m
```

## Step 29: Multi-Environment Management

Source: https://kustomize.io/, https://argoproj.github.io/application-set/

### Environment-as-Code Patterns

#### Directory-per-Environment

```
k8s/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
├── overlays/
│   ├── dev/
│   │   ├── kustomization.yaml
│   │   └── patch-replicas.yaml
│   ├── staging/
│   │   ├── kustomization.yaml
│   │   └── patch-replicas.yaml
│   └── production/
│       ├── kustomization.yaml
│       ├── patch-replicas.yaml
│       └── hpa.yaml
```

#### Kustomize Overlays

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

# overlays/production/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
  - hpa.yaml
namePrefix: prod-
namespace: production
patches:
  - path: patch-replicas.yaml

# overlays/production/patch-replicas.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  template:
    spec:
      containers:
        - name: myapp
          resources:
            requests:
              cpu: 500m
              memory: 512Mi
            limits:
              cpu: "1"
              memory: 1Gi
```

#### ArgoCD ApplicationSets

```yaml
# One ApplicationSet generates ArgoCD Applications for all environments
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
            replicas: "1"
          - env: staging
            cluster: https://staging-cluster
            namespace: staging
            replicas: "2"
          - env: production
            cluster: https://prod-cluster
            namespace: production
            replicas: "3"
  template:
    metadata:
      name: 'myapp-{{env}}'
    spec:
      project: default
      source:
        repoURL: https://github.com/org/k8s-manifests
        targetRevision: main
        path: 'apps/myapp/overlays/{{env}}'
      destination:
        server: '{{cluster}}'
        namespace: '{{namespace}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

#### Git Generator (branch-per-env)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: myapp
spec:
  generators:
    - git:
        repoURL: https://github.com/org/k8s-manifests
        revision: HEAD
        directories:
          - path: 'apps/myapp/overlays/*'
  template:
    metadata:
      name: 'myapp-{{path.basename}}'
    spec:
      source:
        path: '{{path}}'
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{path.basename}}'
```

### Dev/Staging/Prod Parity

**Parity spectrum — what to match vs what to differ:**

| Aspect | Dev | Staging | Prod |
|--------|-----|---------|------|
| Container image | Same (same tag/sha) | Same | Same |
| Config/secrets | Dev values | Prod-like values | Real values |
| Resource limits | Minimal (100m/128Mi) | Medium (500m/512Mi) | Full (1/1Gi+) |
| Replicas | 1 | 2 | 3+ |
| Database | Local/minimal | Prod-like schema + anonymized data | Real data |
| TLS/ingress | Self-signed or none | Valid cert | Valid cert + WAF |
| Monitoring | Basic | Full (same dashboards) | Full + alerts |
| Feature flags | All enabled | Prod flags | Prod flags |

**Parity rules:**
- Same container image across all environments — only config differs
- Staging must run same health checks, readiness probes as production
- Schema migrations tested against staging before production
- Same ingress controller and configuration (except hostname)
- Staging should mirror production topology (not a reduced subset)

### Ephemeral Preview Environments

```yaml
# ArgoCD ApplicationSet for PR-based preview environments
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: preview-envs
spec:
  generators:
    - pullRequest:
        github:
          owner: org
          repo: myapp
          tokenRef:
            secretName: github-token
            key: token
          labels:
            - preview
  template:
    metadata:
      name: 'preview-{{number}}'
    spec:
      project: preview
      source:
        repoURL: https://github.com/org/k8s-manifests
        targetRevision: main
        path: apps/myapp/overlays/preview
        helm:
          parameters:
            - name: image.tag
              value: '{{head_sha}}'
            - name: ingress.host
              value: 'pr-{{number}}.preview.example.com'
      destination:
        server: https://kubernetes.default.svc
        namespace: 'preview-{{number}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

**Preview env lifecycle:**
1. PR opened with `preview` label → ApplicationSet creates ArgoCD Application
2. Namespace `preview-{PR#}` created, app deployed with PR's commit SHA
3. Ingress routes `pr-{PR#}.preview.example.com` to namespace
4. Bot comments preview URL on PR
5. New commits to PR → auto-sync updates preview
6. PR merged/closed → ApplicationSet removes Application → namespace deleted
7. TTL controller (72h) catches missed cleanup events

**Preview env best practices:**
- Use lightweight infra (reduced replicas, no HPA, small resource limits)
- Share external service dependencies (databases, caches) via namespaced prefixes
- Anonymize/seed test data for preview databases
- Limit concurrent preview envs per org (cost cap)
- Auto-destroy stale previews (>72h without PR activity)

## Step 30: Database CI/CD

Source: https://www.liquibase.com/, https://flywaydb.org/, https://atlasgo.io/

### Schema Testing

#### Ephemeral Database (for CI testing)

```yaml
# GitHub Actions — spin up DB, run migrations, test, destroy
jobs:
  db-test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: myapp_test
          POSTGRES_PASSWORD: test
        ports: ['5432:5432']
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      # Run all migrations against ephemeral DB
      - run: npx prisma migrate deploy
        env:
          DATABASE_URL: postgresql://postgres:test@localhost:5432/myapp_test
      # Seed test data
      - run: npx prisma db seed
        env:
          DATABASE_URL: postgresql://postgres:test@localhost:5432/myapp_test
      # Run integration tests
      - run: npm test -- --testPathPattern=integration
        env:
          DATABASE_URL: postgresql://postgres:test@localhost:5432/myapp_test
```

#### Shadow Database (migration validation)

Separate database used to diff current schema against desired state.

```yaml
# Prisma shadow DB pattern
- run: |
    # Create shadow database
    PGPASSWORD=test psql -h localhost -U postgres -c "CREATE DATABASE myapp_shadow;"
    # Generate migration SQL without applying
    npx prisma migrate diff \
      --from-schema-datamodel prisma/schema.prisma \
      --to-schema-datamodel prisma/schema.prisma \
      --shadow-database-url "postgresql://postgres:test@localhost:5432/myapp_shadow" \
      --script > migration.sql
    # Review generated SQL
    cat migration.sql
    # Validate against shadow DB
    npx prisma migrate deploy
```

**Atlas (declarative schema diffing):**
```yaml
# Atlas — compare desired schema against running DB
- run: |
    atlas schema diff \
      --from "postgres://postgres:test@localhost:5432/myapp_test" \
      --to "file://schema.hcl" \
      --dev-url "docker://postgres/16"
```

### Migration Verification

#### Migration Linting

```yaml
# Atlas lint — detect dangerous migrations
- run: |
    atlas migrate lint \
      --dir "file://migrations" \
      --dev-url "docker://postgres/16" \
      --latest 1 \
      --format '{{ range .Diagnostics }}{{ .Text }}{{ endl }}{{ end }}'

# Squawk — PostgreSQL linting (detects unsafe ALTER TABLE)
- run: |
    npm install -g squawk-cli
    squawk --.pg-version 16 migrations/*.sql

# SQLFluff — general SQL linting
- run: |
    pip install sqlfluff
    sqlfluff lint migrations/ --dialect postgres
```

**Dangerous patterns detected by linting:**
- `ALTER TABLE ... ADD COLUMN ... NOT NULL` without DEFAULT (locks table)
- `DROP TABLE` without backup confirmation
- `ALTER TYPE ... ADD VALUE` (can't be rolled back in transaction)
- Missing index on foreign key column
- `CREATE INDEX` without `CONCURRENTLY` (locks table)

#### Dry-Run Verification

```yaml
# Flyway — dry-run mode
- run: |
    flyway migrate \
      -url=jdbc:postgresql://localhost:5432/myapp_shadow \
      -dryRunOutput=dry-run.sql \
      -locations=filesystem:migrations
    # Review SQL that would execute
    cat dry-run.sql

# golang-migrate — validate SQL syntax
- run: |
    migrate -database "postgres://postgres:test@localhost:5432/myapp_test?sslmode=disable" \
      -path ./migrations \
      validate

# Liquibase — preview SQL
- run: |
    liquibase updateSQL --changelog-file=changelog.xml > preview.sql
```

### Rollback Strategies

#### Expand-Contract Pattern (Zero-Downtime)

Safe schema changes without downtime. Three phases:

```
Phase 1: EXPAND (backward compatible)
  - Add new column (nullable or with default)
  - Add new table
  - Add new index CONCURRENTLY
  - Deploy code that writes to BOTH old and new columns

Phase 2: MIGRATE DATA
  - Backfill new column from old column
  - Deploy code that reads from NEW column
  - Verify data consistency

Phase 3: CONTRACT (cleanup)
  - Remove old column
  - Remove old table
  - Remove dual-write code
```

**Example: Rename column safely**
```sql
-- Phase 1: Expand
ALTER TABLE users ADD COLUMN email_address TEXT;
-- Backfill
UPDATE users SET email_address = email WHERE email_address IS NULL;
-- Code: write to both columns, read from email_address

-- Phase 2: Code reads/writes only email_address

-- Phase 3: Contract
ALTER TABLE users DROP COLUMN email;
```

**Example: Add NOT NULL column**
```sql
-- Phase 1: Expand (nullable + default)
ALTER TABLE orders ADD COLUMN status TEXT DEFAULT 'pending';
-- Backfill existing rows
UPDATE orders SET status = 'pending' WHERE status IS NULL;
-- Phase 2: Add NOT NULL constraint (after all rows have value)
ALTER TABLE orders ALTER COLUMN status SET NOT NULL;
```

#### Compensating Migrations

Forward-only migrations with compensating logic for rollback.

```yaml
# Migration file structure
migrations/
├── 001_add_status_column.sql        # Forward
├── 001_add_status_column_rollback.sql  # Compensating
├── 002_create_audit_table.sql
├── 002_create_audit_table_rollback.sql
```

```yaml
# Atlas — reversible migrations
- run: |
    atlas migrate diff add_status_column \
      --dir "file://migrations" \
      --to "file://schema.hcl" \
      --dev-url "docker://postgres/16"

# Flyway — undo migrations (commercial feature)
# Liquibase — rollback by tag
- run: liquibase rollback-count --count=1 --changelog-file=changelog.xml
```

**Compensating migration patterns:**
```sql
-- Forward: add column
ALTER TABLE users ADD COLUMN display_name TEXT;
-- Rollback: drop column
ALTER TABLE users DROP COLUMN IF EXISTS display_name;

-- Forward: create index
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);
-- Rollback: drop index
DROP INDEX CONCURRENTLY IF EXISTS idx_users_email;

-- Forward: create table + seed data
CREATE TABLE roles (id SERIAL PRIMARY KEY, name TEXT NOT NULL);
INSERT INTO roles (name) VALUES ('admin'), ('user'), ('viewer');
-- Rollback: drop table (data lost — document this)
DROP TABLE IF EXISTS roles CASCADE;
```

**Database CI/CD checklist:**
- [ ] Every migration runs against ephemeral DB in CI
- [ ] Migrations linted for dangerous operations (table locks, missing defaults)
- [ ] Dry-run verification before production apply
- [ ] Expand-contract for zero-downtime column/table changes
- [ ] Compensating migrations for every forward migration
- [ ] Migration files version-controlled alongside application code
- [ ] Staging runs production migration before production
- [ ] Backup verified before production migration

## Step 31: Progressive Delivery Detailed

Source: https://argoproj.github.io/rollouts/features/analysis/, https://docs.flagger.app/

### Canary Analysis

#### AnalysisTemplate with PromQL

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: success-rate
spec:
  args:
    - name: service-name
    - name: namespace
      value: default
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
            sum(rate(http_requests_total{
              service="{{args.service-name}}",
              namespace="{{args.namespace}}",
              status=~"2.."
            }[5m]))
            /
            sum(rate(http_requests_total{
              service="{{args.service-name}}",
              namespace="{{args.namespace}}"
            }[5m]))
    - name: latency-p99
      interval: 1m
      count: 5
      successCondition: result[0] <= 500
      failureLimit: 2
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
            histogram_quantile(0.99,
              sum(rate(http_request_duration_seconds_bucket{
                service="{{args.service-name}}",
                namespace="{{args.namespace}}"
              }[5m])) by (le)
            ) * 1000
    - name: error-rate
      interval: 1m
      count: 5
      successCondition: result[0] <= 0.01
      failureLimit: 1
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
            sum(rate(http_requests_total{
              service="{{args.service-name}}",
              namespace="{{args.namespace}}",
              status=~"5.."
            }[5m]))
            /
            sum(rate(http_requests_total{
              service="{{args.service-name}}",
              namespace="{{args.namespace}}"
            }[5m]))
    - name: pod-restarts
      interval: 1m
      count: 3
      successCondition: result[0] == 0
      failureLimit: 0
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
            sum(kube_pod_container_status_restarts_total{
              namespace="{{args.namespace}}",
              pod=~"{{args.service-name}}-canary.*"
            })
```

**Datadog analysis (alternative to Prometheus):**
```yaml
    - name: dd-request-success-rate
      interval: 1m
      count: 5
      successCondition: result >= 0.99
      provider:
        datadog:
          address: https://api.datadoghq.com
          query: |
            sum:http.requests{service:{{args.service-name}},status_code:2xx}.as_rate() /
            sum:http.requests{service:{{args.service-name}}}.as_rate()
```

### Automated Rollback

#### Rollback Triggers

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

    # Abort on high latency
    - name: latency-p95
      interval: 1m
      count: 3
      successCondition: result[0] <= 1000
      failureLimit: 1
      provider:
        prometheus:
          address: http://prometheus.monitoring:9090
          query: |
            histogram_quantile(0.95,
              sum(rate(http_request_duration_seconds_bucket{
                service="{{args.service-name}}"
              }[5m])) by (le)
            ) * 1000

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

#### Alerting on Rollback

```yaml
# Argo Rollouts — notify on rollback
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  strategy:
    canary:
      analysis:
        templates:
          - templateName: strict-analysis
      # Notification on analysis failure
      metadata:
        annotations:
          notifications.argoproj.io/subscribe.on-rollout-aborted.slack: |
            "ops-alerts"
```

```yaml
# Flagger — alerting configuration
apiVersion: flagger.app/v1beta1
kind: AlertProvider
metadata:
  name: slack
spec:
  type: slack
  address: https://hooks.slack.com/services/xxx/yyy/zzz
---
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: myapp
spec:
  analysis:
    alerts:
      - name: slack-alert
        severity: error
        providerRef:
          name: slack
```

### Traffic Management

#### Header-Based Routing

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

```yaml
# Argo Rollouts — header-based routing with Istio
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  strategy:
    canary:
      canaryMetadata:
        labels:
          role: canary
      stableMetadata:
        labels:
          role: stable
      trafficRouting:
        istio:
          virtualServices:
            - name: myapp-vsvc
              routes:
                - primary
      steps:
        - setWeight: 0
        - setHeaderRoute:
            name: canary-header
            match:
              - headerName: x-canary
                headerValue:
                  exact: "true"
        - pause: { duration: 10m }
```

#### Weight-Based Routing

```yaml
# Progressive traffic shifting
steps:
  - setWeight: 10       # 10% to canary
  - pause: { duration: 5m }
  - analysis:
      templates:
        - templateName: success-rate
  - setWeight: 25       # 25% to canary
  - pause: { duration: 5m }
  - analysis:
      templates:
        - templateName: success-rate
  - setWeight: 50       # 50% to canary
  - pause: { duration: 10m }
  - analysis:
      templates:
        - templateName: success-rate
  - setWeight: 75       # 75% to canary
  - pause: { duration: 5m }
  - setWeight: 100      # Full promotion
```

#### Traffic Mirroring (Shadow Traffic)

Send production traffic copy to canary without affecting users.

```yaml
# Istio — mirror traffic to canary
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts:
    - myapp.example.com
  http:
    - route:
        - destination:
            host: myapp
            subset: stable
      mirror:
        host: myapp
        subset: canary
      mirrorPercentage:
        value: 100.0
```

```yaml
# Argo Rollouts — mirror step
steps:
  - mirror:
      percentage: 100
      duration: 10m
      # Traffic copied to canary, responses discarded
  - analysis:
      templates:
        - templateName: success-rate
  - setWeight: 10
  - pause: { duration: 5m }
```

### Progressive Delivery Lifecycle (7 Steps)

```
1. BUILD → container image built, tagged with SHA
   |
2. DEPLOY CANARY → canary pods created alongside stable
   |
3. MIRROR/HEADER-ROUTE → test canary with shadow traffic or internal headers
   |
4. ANALYZE → AnalysisTemplate evaluates PromQL/metrics
   |  ├─ PASS → proceed to next step weight
   |  └─ FAIL → automated rollback, alert team
   |
5. SHIFT WEIGHT → incrementally increase canary traffic (10% → 25% → 50% → 75%)
   |
6. PROMOTE → canary becomes stable, old stable scaled down
   |
7. OBSERVE → post-deployment monitoring (error rate, latency, saturation)
```

**Full Rollout with all 7 phases:**
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
      # Anti-affinity: canary and stable on different nodes
      antiAffinity:
        preferredDuringSchedulingIgnoredDuringExecution:
          weight: 100
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
              - templateName: full-analysis

        - setWeight: 25
        - pause: { duration: 5m }
        - analysis:
            templates:
              - templateName: full-analysis

        - setWeight: 50
        - pause: { duration: 10m }
        - analysis:
            templates:
              - templateName: full-analysis

        - setWeight: 75
        - pause: { duration: 5m }
        - analysis:
            templates:
              - templateName: full-analysis

        # Step 6: Full promotion (100% handled automatically)

        # Step 7: Post-deploy monitoring via background analysis
      analysis:
        templates:
          - templateName: post-deploy-monitor
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

## Step 32: SLSA Detailed

### SLSA Levels Requirements

| Level | Build-as-Code | Provenance | Isolation | Non-falsifiable | Ephemeral |
|-------|--------------|------------|-----------|-----------------|-----------|
| L1 | Optional | Optional | Optional | Optional | Optional |
| L2 | Required | Required | Optional | Optional | Optional |
| L3 | Required | Required | Required | Required | Required |
| L4 | Required | Required | Required + Hermetic | Required | Required + Two-party review |

**L1** — Provenance exists (may be self-attested). Basic supply chain visibility.
**L2** — Hosted build platform generates signed provenance. Harder to tamper.
**L3** — Hardened build platform, isolated from tenants, provenance non-falsifiable.
**L4** — Hermetic/reproducible builds, two-person review of all changes.

### Provenance Generation

#### GitHub Attestations (native, simplest)

```yaml
# Requires: GitHub Enterprise or public repo + actions/attest-build-provenance
name: Build with provenance
on:
  push:
    tags: ['v*']

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      id-token: write      # OIDC for Sigstore
      attestations: write  # Store attestation
      contents: read
    steps:
      - uses: actions/checkout@v4

      - name: Build artifact
        run: make build

      - name: Generate provenance attestation
        uses: actions/attest-build-provenance@v2
        with:
          subject-path: './build/myapp'
          # Attaches DSSE envelope with SLSA provenance to the artifact

      - name: Verify attestation
        run: gh attestation verify ./build/myapp --repo ${{ github.repository }}
```

#### slsa-github-generator (SLSA L3 compliant)

```yaml
# Delegator pattern — reusable workflow runs in isolated runner
name: Release
on:
  push:
    tags: ['v*']

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      hashes: ${{ steps.hash.outputs.hashes }}
    steps:
      - uses: actions/checkout@v4
      - run: make build
      - id: hash
        run: |
          set -euo pipefail
          echo "hashes=$(sha256sum ./build/myapp | base64 -w0)" >> "$GITHUB_OUTPUT"

  provenance:
    needs: build
    permissions:
      actions: read
      id-token: write
      packages: write
    # slsa-framework/slsa-github-generator/delegator/generic-container
    # — runs in ephemeral, isolated runner (L3 requirement)
    uses: slsa-framework/slsa-github-generator/.github/workflows/generator_generic_slsa3.yml@v2.1.0
    with:
      base64-subjects: "${{ needs.build.outputs.hashes }}"
      provenance-name: "myapp.intoto.jsonl"
      upload-assets: true
```

#### Tekton Chains (Kubernetes-native)

```yaml
# Tekton Chains watches TaskRuns, auto-generates provenance
# Install Chains: kubectl apply -f https://github.com/tektoncd/chains/releases/latest/download/release.yaml

# ConfigMap — configure Chains
apiVersion: v1
kind: ConfigMap
metadata:
  name: chains-config
  namespace: tekton-chains
data:
  artifacts.oci.storage: oci          # Store in OCI registry
  artifacts.oci.format: simplesigning
  artifacts.taskrun.storage: tekton   # Store in TaskRun
  artifacts.taskrun.format: in-toto   # SLSA in-toto format
  transparency.enabled: "true"        # Publish to Rekor
  signers.x509.fulcio.enabled: "true" # Keyless via Fulcio

# PipelineRun generates task, Chains auto-signs
apiVersion: tekton.dev/v1
kind: PipelineRun
metadata:
  name: build-and-sign
  annotations:
    chains.tekton.dev/transparency-upload: "true"
```

### in-toto Attestation Format

```json
{
  "_type": "https://in-toto.io/Statement/v1",
  "subject": [
    {
      "name": "pkg:github.com/org/myapp@v1.2.3",
      "digest": { "sha256": "abc123..." }
    }
  ],
  "predicateType": "https://slsa.dev/provenance/v1",
  "predicate": {
    "buildDefinition": {
      "buildType": "https://actions.github.io/buildtypes/workflow/v1",
      "externalParameters": {
        "workflow": { "ref": "refs/tags/v1.2.3", "repository": "https://github.com/org/myapp" }
      },
      "resolvedDependencies": [
        { "uri": "pkg:npm/express@4.18.2", "digest": { "sha512": "..." } }
      ]
    },
    "runDetails": {
      "builder": { "id": "https://github.com/actions/runner" },
      "metadata": {
        "invocationId": "https://github.com/org/myapp/actions/runs/12345",
        "startedOn": "2026-01-15T10:30:00Z",
        "finishedOn": "2026-01-15T10:32:15Z"
      }
    }
  }
}
```

**DSSE envelope** wraps the statement for transport:
```json
{
  "payloadType": "application/vnd.in-toto+json",
  "payload": "<base64-encoded statement>",
  "signatures": [{ "keyid": "", "sig": "<base64 signature>" }]
}
```

**SLSA checklist:**
- [ ] L2 minimum: use hosted CI (GitHub Actions) with signed provenance
- [ ] L3: slsa-github-generator delegator workflow (isolated runner)
- [ ] Provenance attestation attached to release artifacts
- [ ] Verify provenance on deploy: `slsa-verifier verify-artifact`
- [ ] in-toto v1 statement format (predicateType matches SLSA version)

## Step 33: Sigstore

### Architecture Overview

```
Developer → OIDC Provider (GitHub/GitLab/Google)
  │
  ▼
Fulcio (Certificate Authority)
  │  Issues short-lived x509 cert bound to OIDC identity
  ▼
Cosign (Signing Tool)
  │  Signs artifact with Fulcio cert, keyless
  ▼
Rekor (Transparency Log)
  │  Immutable Merkle tree log entry, publicly auditable
  ▼
Verifier → fetches Rekor entry, verifies signature + identity
```

### Fulcio — Short-lived Certificate Authority

```bash
# Fulcio issues ephemeral x509 certs from OIDC identity
# No long-lived keys — cert expires in ~10 minutes
# OIDC token includes: issuer (iss), subject (sub), email

# Manual Fulcio flow (cosign handles this automatically):
# 1. Get OIDC token from GitHub Actions
TOKEN=$(curl -sLS "${ACTIONS_ID_TOKEN_REQUEST_URL}" \
  -H "Authorization: bearer ${ACTIONS_ID_TOKEN_REQUEST_TOKEN}" \
  -H "Accept: application/json; api-version=2.0" \
  -d "audience=sigstore" | jq -r '.value')

# 2. Fulcio issues cert from token
# POST https://fulcio.sigstore.dev/api/v1/signingCert
# Body: { "publicKey": { "content": "<base64>", "algorithm": "ecdsa" }, "signedEmailAddress": "<base64 email>" }

# Cert includes:
# Subject: https://github.com/org/repo/.github/workflows/build.yml@refs/heads/main
# Issuer: https://token.actions.githubusercontent.com
# Extensions: GitHub Actions workflow identity, runner environment
```

### Rekor — Transparency Log

```bash
# Rekor stores signed log entries in tamper-proof Merkle tree
# Each entry: artifact hash + signature + certificate + timestamp
# Globally auditable — anyone can verify log integrity

# Search Rekor for artifact
rekor-cli search --sha256 abc123def456

# Get log entry by UUID
rekor-cli get --uuid <log-entry-uuid>

# Verify Merkle inclusion proof
rekor-cli verify --uuid <log-entry-uuid>

# Rekor entry structure:
# {
#   "body": "<base64 DSSE envelope>",
#   "integratedTime": 1705312200,
#   "logID": "...",         // Merkle tree root hash
#   "logIndex": 12345678,
#   "verification": {
#     "inclusionProof": { "hashes": [...], "logIndex": ..., "rootHash": ... }
#   }
# }
```

### Cosign — Keyless Signing

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

# --- Store signatures in OCI (default) ---
# Cosign stores signature as OCI artifact alongside image
# Manifest: ghcr.io/org/myapp:sha256-abc123.sig
# No separate signature registry needed
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

  generate-provenance:
    needs: build-and-sign
    permissions:
      id-token: write
      attestations: write
    runs-on: ubuntu-latest
    steps:
      - uses: actions/attest-build-provenance@v2
        with:
          subject-name: ghcr.io/${{ github.repository }}
          subject-digest: sha256:${{ needs.build-and-sign.outputs.digest }}
          push-to-registry: true
```

**Sigstore checklist:**
- [ ] Cosign installed in CI (sigstore/cosign-installer action)
- [ ] Keyless signing enabled (`--yes` flag for non-interactive OIDC)
- [ ] Strict verification: pin `--certificate-identity` and `--certificate-oidc-issuer`
- [ ] Rekor transparency enabled (default, disables with `--tlog-upload=false`)
- [ ] Sign both container images and build artifacts (blobs)

## Step 34: Dependency Security

### Dependabot (GitHub Native)

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"
    schedule:
      interval: "weekly"
      day: "monday"
    open-pull-requests-limit: 10
    reviewers: ["team-security"]
    labels: ["dependencies", "security"]
    groups:
      dev-dependencies:
        dependency-type: "development"
        update-types: ["minor", "patch"]
      production-dependencies:
        dependency-type: "production"
        update-types: ["patch"]

  - package-ecosystem: "docker"
    directory: "/"
    schedule:
      interval: "weekly"

  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "weekly"
    # Keep actions updated (important for SHA-pinned actions)

  - package-ecosystem: "pip"
    directory: "/"
    schedule:
      interval: "weekly"

# Security-only updates (no version bump needed)
# Dependabot auto-creates PRs for CVEs in dependencies
# Configured via GitHub UI: Settings → Code security → Dependabot
```

**Dependabot strengths:** Zero-config for GitHub repos, auto-merges patch updates, security alerts from GitHub Advisory Database, groups related updates.

**Dependabot limits:** No self-hosted GitLab/Bitbucket, limited grouping logic, no PinDigests by default, slower adoption of new ecosystems.

### Renovate (Configurable, 80+ Package Managers)

```jsonc
// renovate.json — main config
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": [
    "config:recommended",
    ":semanticCommits",
    "group:monorepos",
    "group:recommended",
    "workarounds:all"
  ],
  "timezone": "America/New_York",
  "schedule": ["before 6am on monday"],
  "prConcurrentLimit": 10,
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
    },
    {
      "matchPackagePatterns": ["^@types/"],
      "groupName": "TypeScript type definitions",
      "automerge": true
    },
    {
      "matchManagers": ["dockerfile"],
      "pinDigests": true,
      "groupName": "Docker base images"
    }
  ],
  "docker-compose": {
    "pinDigests": true
  },
  "vulnerabilityAlerts": {
    "enabled": true,
    "labels": ["security"]
  },
  "pinDigests": true
}
```

**Renovate strengths:** Self-hosted (or Mend-hosted), 80+ managers (npm/pip/go/docker/terraform/helm/k8s/ansible...), rich grouping, pinDigests, automerge, regex manager for custom deps, JSON5 config with inheritance.

### Socket.dev (Behavior-based Detection)

```yaml
# Socket detects malicious packages by analyzing behavior, not just CVEs
# Catches: typosquatting, dependency confusion, install scripts, network calls, env exfil

# GitHub App install → auto PR review comments
# npm/yarn integration via socket.yml

# socket.yml
socket:
  organizationId: "org-abc123"
  enabledFeatures:
    - dependency-risk
    - supply-chain-protection

# In CI — block install of risky packages
# Use Socket API to gate merges:
# POST https://api.socket.dev/v0/scan
```

**Socket.dev catches (examples):**
- Package with `postinstall` script accessing `~/.ssh/`
- Typosquat: `lod-ash` vs `lodash`
- Dependency confusion: internal package name in public registry
- New package with zero history pulling network data at install

### Snyk (SCA + SAST + IaC)

```bash
# SCA — Software Composition Analysis
snyk test                       # Scan open-source deps for vulnerabilities
snyk monitor                    # Continuous monitoring, alert on new CVEs
snyk test --severity-threshold=high  # Only high/critical

# SAST — Static Application Security Testing
snyk code test                  # Scan source code for security issues

# IaC — Infrastructure as Code scanning
snyk iac test ./terraform/      # Scan Terraform/CloudFormation/k8s manifests
snyk iac test ./k8s/            # Misconfigurations (no resource limits, root user, etc.)

# Container scanning
snyk container test myapp:latest
snyk container test --file=Dockerfile myapp:latest  # Context-aware
```

```yaml
# GitHub Actions — Snyk integration
- uses: snyk/actions/node@master
  env:
    SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
  with:
    command: test
    args: --severity-threshold=high --fail-on=upgradable

- uses: snyk/actions/iac@master
  env:
    SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
  with:
    args: --severity-threshold=high
```

### Comparison Matrix

| Feature | Dependabot | Renovate | Socket.dev | Snyk |
|---------|-----------|----------|------------|------|
| Primary function | Auto-update deps | Auto-update deps | Malicious pkg detection | SCA + SAST + IaC |
| Platforms | GitHub only | GitHub/GitLab/Bitbucket/self-hosted | GitHub App + API | GitHub/GitLab/Bitbucket + CLI |
| Package managers | 15 ecosystems | 80+ managers | npm/pip/Go/Maven (growing) | 10+ languages + containers |
| CVE scanning | Via GH Advisory DB | Via OSV/GitHub | N/A (behavior focus) | Snyk VulnDB (proprietary) |
| Malicious detection | ❌ | ❌ | ✅ Behavior-based | Partial (heuristic) |
| Auto-PR creation | ✅ | ✅ | ❌ (review comments) | ❌ (PR checks) |
| Automerge | Limited (patch only) | ✅ Full control | N/A | N/A |
| Digest pinning | ❌ | ✅ | N/A | N/A |
| Grouping | Basic | Advanced (regex, rules) | N/A | N/A |
| Self-hosted | ❌ | ✅ (Renovate OSS) | ❌ | ✅ (Snyk Broker) |
| SAST | ❌ | ❌ | ❌ | ✅ |
| IaC scanning | ❌ | ❌ | ❌ | ✅ |
| Cost | Free (GitHub) | Free (OSS) / Mend paid | Free tier + paid | Free tier + paid |

**Recommended stack:**
- **Renovate** for dependency updates (superior config + pinDigests)
- **Socket.dev** for malicious package detection (complements CVE scanning)
- **Snyk** for vulnerability scanning (SCA + SAST + IaC in one tool)
- **Dependabot** if GitHub-only and want zero-config (backup to Renovate)

**Dependency security checklist:**
- [ ] Automated dependency updates enabled (Dependabot or Renovate)
- [ ] Digest pinning enabled (Renovate `pinDigests: true`)
- [ ] Security-only alerts configured (Snyk or GitHub Security tab)
- [ ] Malicious package detection active (Socket.dev)
- [ ] Auto-merge for patch updates of dev dependencies
- [ ] Vulnerability PRs merge within SLA (critical: 24h, high: 7d, medium: 30d)

## Step 35: GitHub Actions Hardening

### OIDC for Cloud Auth (No Stored Credentials)

```yaml
# AWS — OIDC provider (no AWS_ACCESS_KEY_ID stored in secrets)
# Setup: aws iam create-open-id-connect-provider
#   --url https://token.actions.githubusercontent.com
#   --client-id-list sts.amazonaws.com
#   --thumbprint-list 6938fd4d98bab03faadb97b34396831e3780aea1

permissions:
  id-token: write
  contents: read

steps:
  - name: Configure AWS credentials
    uses: aws-actions/configure-aws-credentials@v4
    with:
      role-to-assume: arn:aws:iam::123456789012:role/github-actions-deploy
      aws-region: us-east-1
      # Role trust policy restricts to specific repo + branch:
      # "StringEquals": {"token.actions.githubusercontent.com:aud": "sts.amazonaws.com"}
      # "StringLike": {"token.actions.githubusercontent.com:sub": "repo:org/repo:ref:refs/heads/main"}

  - name: Deploy to S3
    run: aws s3 sync ./dist s3://my-bucket/
```

```yaml
# GCP — Workload Identity Federation
permissions:
  id-token: write
  contents: read

steps:
  - uses: google-github-actions/auth@v2
    with:
      workload_identity_provider: projects/123/locations/global/workloadIdentityPools/github/providers/github
      service_account: deploy@my-project.iam.gserviceaccount.com
      # No JSON key file — OIDC token exchanged for GCP token

  - uses: google-github-actions/deploy-cloudrun@v2
    with:
      service: myapp
      image: gcr.io/my-project/myapp:latest
```

```yaml
# Azure — Federated credentials
permissions:
  id-token: write
  contents: read

steps:
  - uses: azure/login@v2
    with:
      client-id: ${{ secrets.AZURE_CLIENT_ID }}
      tenant-id: ${{ secrets.AZURE_TENANT_ID }}
      subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}
      # Federated credential: subject = repo:org/repo:ref:refs/heads/main

  - run: az webapp deploy --name myapp --src-path ./dist.zip
```

**OIDC trust policy best practices:**
- Pin to specific repo AND branch/tag (not wildcard)
- Use `ref:refs/heads/main` for deploy, `ref:refs/tags/v*` for release
- Separate roles per environment (staging vs production)
- Short session duration (`MaxSessionDuration: 3600`)

### SHA-Pinned Actions (pinact Tool)

```bash
# Pin all actions to full SHA (prevents tag mutation attacks)
# pinact — official tool from SIGSTORE/sigstore
go install github.com/sigstore/pinact/cmd/pinact@latest

# Pin actions in workflow files
pinact run .github/workflows/*.yml
# Transforms: actions/checkout@v4 → actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4

# Verify pinned actions (CI check)
pinact run --check .github/workflows/*.yml
```

```yaml
# BEFORE (vulnerable — tag can be force-pushed)
- uses: actions/checkout@v4
- uses: docker/build-push-action@v5

# AFTER (SHA-pinned — immutable)
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
- uses: docker/build-push-action@14487ce63c7a62a4a324b0bfb37086795e31c6c1 # v6.12.0
```

```yaml
# CI gate — fail if actions not SHA-pinned
name: Pin Check
on: [pull_request]
jobs:
  pinact:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: sigstore/cosign-installer@v3
      - run: go install github.com/sigstore/pinact/cmd/pinact@latest
      - run: pinact run --check .github/workflows/*.yml
```

### CODEOWNERS for Workflow Protection

```bash
# .github/CODEOWNERS
# Require security team review for workflow changes
/.github/workflows/    @org/platform-security
/.github/dependabot.yml  @org/platform-security
/.github/CODEOWNERS    @org/platform-security

# Require infra team for Terraform/deploy
/terraform/            @org/infrastructure
/deploy/               @org/infrastructure
```

```yaml
# Branch protection: require CODEOWNERS review
# Settings → Branches → main → Require review from Code Owners ✅
# + Require pull request reviews (min 1-2 approvals)
# + Dismiss stale reviews on new pushes
# + Require status checks to pass before merge
```

### Environment Protection Rules

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

**Environment configuration (UI):**

| Setting | Staging | Production |
|---------|---------|------------|
| Required reviewers | 0 | 2 (platform-team) |
| Wait timer | 0 min | 15 min |
| Deployment branches | `main` only | `main` + `v*` tags |
| Custom protection rules | None | Branch policy + reviewers |
| Variables | `ENV=staging` | `ENV=production` |
| Secrets | Staging credentials | Production credentials |

**Environment secrets scoping:**
```yaml
# Secrets scoped to environment (not repo-wide)
# deploy-production only has prod AWS role, staging has staging role
# Limits blast radius — compromised staging secret ≠ production access
```

### Least Privilege Permissions

```yaml
# TOP-LEVEL — restrict default for all jobs
# Always set restrictive defaults, elevate per-job
permissions: {}  # No permissions by default (empty)

jobs:
  lint:
    permissions:
      contents: read    # Only what's needed
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run lint

  test:
    permissions:
      contents: read
      packages: read    # Pull private packages
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test

  deploy:
    permissions:
      contents: read
      id-token: write   # OIDC for cloud auth
      deployments: write
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: ./deploy.sh

  publish:
    permissions:
      contents: read
      packages: write   # Push to GHCR
      id-token: write   # Sigstore signing
      attestations: write
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: docker build -t ghcr.io/org/myapp:latest .
      - run: docker push ghcr.io/org/myapp:latest
```

**Permission reference:**

| Permission | Read | Write | Use case |
|-----------|------|-------|----------|
| `contents` | checkout | create releases | Always read, rarely write |
| `packages` | pull images | push images | Container registry |
| `id-token` | N/A | OIDC token | Cloud auth, signing |
| `deployments` | read status | create deployments | CD workflows |
| `issues` | read | create/close | Bot issues |
| `pull-requests` | read | write comments | Bot PR reviews |
| `attestations` | N/A | write | Build provenance |
| `security-events` | read | write | CodeQL upload |
| `actions` | read | write | Workflow dispatch |

**Hardening checklist:**
- [ ] Top-level `permissions: {}` (restrictive default, elevate per-job)
- [ ] OIDC for AWS/GCP/Azure (no long-lived credentials in secrets)
- [ ] All actions SHA-pinned (pinact in CI gate)
- [ ] CODEOWNERS protects `/.github/workflows/` (security team review)
- [ ] Environment protection: reviewers + wait timer for production
- [ ] Environment-scoped secrets (not repo-wide)
- [ ] Deployment branches restricted (main + tags only for prod)
- [ ] Fork PRs: no secret access, `pull_request_target` used cautiously
- [ ] Dependabot/Renovate configured for workflow action updates
- [ ] Rollout history retained (revisionHistoryLimit >= 3) for quick rollback