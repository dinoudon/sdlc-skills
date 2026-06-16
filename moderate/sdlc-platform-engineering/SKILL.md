---
name: sdlc-platform-engineering
description: "Platform engineering: internal developer portals (IDP), Backstage, golden paths, service catalog, self-service infrastructure, platform-as-a-product, developer productivity metrics (SPACE, DORA), platform team structure, paved roads, template repos, feature flags, secrets management, environment management."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, platform-engineering, idp, backstage, golden-paths, service-catalog, self-service, developer-productivity, paved-roads, dora, space-framework]
    related_skills: [sdlc-developer-tooling, sdlc-deployment, sdlc-observability, sdlc-cicd-pipeline, sdlc-architecture-design]
---

# Platform Engineering

Internal developer platforms, golden paths, service catalogs, and platform-as-a-product thinking. How Netflix, Spotify, and Google build platforms.

## When to Use

Trigger when user:
- Builds internal developer portal (IDP)
- Implements Backstage or similar platform
- Designs golden paths or paved roads
- Creates service catalog or software catalog
- Plans self-service infrastructure for developers
- Measures developer productivity (DORA, SPACE)
- Structures platform teams
- Designs template repositories or scaffolding
- Plans feature flags, secrets management, or environment management platform

## Step 1: Platform-as-a-Product

### What is Platform Engineering?
Source: Gartner, CNCF

Platform engineering treats internal infrastructure as a product. The "customers" are developers. The platform team builds abstractions that reduce cognitive load.

```
Before Platform Engineering:
Developer → K8s YAML → Terraform → Helm → DNS → TLS → Monitoring → ...
(cognitive load: HIGH)

After Platform Engineering:
Developer → `service create my-app` → platform handles the rest
(cognitive load: LOW)
```

### Platform Team Structure
Source: Team Topologies

```
┌─────────────────────────────────────────────────┐
│                 PLATFORM TEAM                    │
├─────────────┬─────────────┬─────────────────────┤
│  Infra      │  Developer  │  Security           │
│  Platform   │  Experience │  Platform           │
│             │             │                     │
│ K8s, CI/CD, │ IDP, CLI,   │ Policy, compliance, │
│ networking, │ templates,  │ secrets, SBOM       │
│ compute     │ onboarding  │                     │
└─────────────┴─────────────┴─────────────────────┘
        │               │               │
        ▼               ▼               ▼
   ┌─────────┐   ┌─────────┐   ┌─────────┐
   │ Stream  │   │ Stream  │   │ Stream  │
   │ Aligned │   │ Aligned │   │ Aligned │
   │ Team A  │   │ Team B  │   │ Team C  │
   └─────────┘   └─────────┘   └─────────┘
```

Platform team interacts via:
- **X-as-a-Service**: Platform provides APIs/tools, teams consume
- **Enabling**: Platform team coaches teams on best practices
- **Collaboration**: Joint sessions for complex features

## Step 2: Internal Developer Portal (IDP)

### What is an IDP?

Single pane of glass for developers to:
- Discover services (software catalog)
- Create new services (templates)
- View documentation
- Manage environments
- Monitor health
- Track DORA metrics

### Backstage (Spotify) — The Standard

Source: https://backstage.io/

```
Backstage Architecture:
┌──────────────────────────────────────┐
│           Backstage Frontend          │
│  ┌─────────┐ ┌──────────┐ ┌───────┐ │
│  │ Catalog  │ │ Templates│ │ Docs  │ │
│  └────┬────┘ └────┬─────┘ └───┬───┘ │
│       │           │            │      │
│  ┌────┴───────────┴────────────┴───┐ │
│  │        Backstage Backend         │ │
│  │  ┌─────────┐  ┌──────────────┐  │ │
│  │  │ Plugin  │  │   Plugin     │  │ │
│  │  │ System  │  │   Registry   │  │ │
│  │  └─────────┘  └──────────────┘  │ │
│  └──────────────────────────────────┘ │
└──────────────────────────────────────┘
         │              │
    ┌────┴────┐    ┌────┴────┐
    │ K8s     │    │ GitHub  │
    │ Cluster │    │ API     │
    └─────────┘    └─────────┘
```

### Backstage catalog-info.yaml

```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: payment-service
  description: Handles payment processing via Stripe
  annotations:
    github.com/project-slug: myorg/payment-service
    backstage.io/techdocs-ref: dir:.
  tags: [payments, go, critical]
  links:
    - url: https://grafana.myorg.com/payment-service
      title: Grafana Dashboard
spec:
  type: service
  lifecycle: production
  owner: team-payments
  system: billing
  providesApis: [payment-api]
  dependsOn: [component:user-service, resource:stripe-db]
  apis:
    - ref: payment-api
```

### Software Catalog Entity Model

```
Kind        | What it represents
------------|-------------------
Component   | Deployable service, library, website
API         | API exposed by a component
Resource    | Infrastructure (DB, queue, bucket)
System      | Collection of related components
Domain      | Business domain (billing, auth)
Group       | Team or organizational unit
User        | Individual team member
Template    | Scaffolding for new components
```

## Step 3: Golden Paths & Paved Roads

### Golden Path Definition

A golden path is the opinionated, supported, easiest way to do something. Not a mandate — the path of least resistance.

```
Golden Path: "Use our Node.js template with Postgres, deployed to K8s 
via our CI pipeline. Here's the template."

Alternative: "You can use whatever you want, but you own everything 
— CI, deployment, monitoring, on-call."

Most teams choose the golden path because it's easier.
```

### Golden Path Components

```
1. Template repository (cookiecutter / Backstage template)
2. CI/CD pipeline (pre-configured GitHub Actions)
3. Deployment target (K8s namespace + Helm chart)
4. Observability stack (Prometheus + Grafana dashboard)
5. Documentation scaffold (TechDocs / README template)
6. Security baseline (SAST, dependency scanning, SLSA)
7. On-call integration (PagerDuty service + runbook)
8. Cost tracking (FinOps tags + budget alerts)
```

### Golden Path Template (Backstage)

```yaml
# template.yaml
apiVersion: scaffolder.backstage.io/v1beta3
kind: Template
metadata:
  name: create-nodejs-service
  title: Create Node.js Service
  description: Creates a new Node.js microservice with golden path
spec:
  owner: platform-team
  type: service
  parameters:
    - title: Service Details
      properties:
        name:
          title: Service name
          type: string
          pattern: "^[a-z0-9-]+$"
        owner:
          title: Owner
          type: string
          ui:field: OwnerPicker
        database:
          title: Database
          type: string
          enum: [postgres, mysql, none]
  steps:
    - id: fetch-template
      action: fetch:template
      input:
        url: ./skeleton
        values:
          name: ${{ parameters.name }}
          owner: ${{ parameters.owner }}
          database: ${{ parameters.database }}
    - id: github-create
      action: github:repo:create
      input:
        repoUrl: github.com?owner=myorg&repo=${{ parameters.name }}
    - id: register-catalog
      action: catalog:register
      input:
        repoContentsUrl: ${{ steps.github-create.output.repoContentsUrl }}
        catalogInfoPath: /catalog-info.yaml
```

## Step 4: Self-Service Infrastructure

### Self-Service Patterns

```
Pattern 1: CLI Tool
$ platform create service --name payment-api --lang go --db postgres
$ platform deploy --env staging
$ platform logs --follow

Pattern 2: Web Portal (IDP)
Click "Create Service" → Fill form → Service created

Pattern 3: API
POST /api/v1/services
{ "name": "payment-api", "language": "go", "database": "postgres" }

Pattern 4: GitOps
Push catalog-info.yaml → Platform reconciles → Resources created
```

### Self-Service Infrastructure Stack

```
Layer 1: Compute
  └─ K8s namespaces, node pools, resource quotas

Layer 2: Data
  └─ Managed databases, caches, queues (via operators)

Layer 3: Networking
  └─ Service mesh, ingress, DNS, TLS

Layer 4: Security
  └─ Secrets, RBAC, network policies, SBOM

Layer 5: Observability
  └─ Metrics, logs, traces, dashboards, alerts

Layer 6: CI/CD
  └─ Pipelines, artifact registries, deployment automation
```

## Step 5: Developer Productivity Metrics

### DORA Metrics
Source: Google DORA / State of DevOps

| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| **Deployment Frequency** | On-demand (multiple/day) | Weekly–monthly | Monthly–quarterly | <Quarterly |
| **Lead Time for Changes** | <1 hour | 1 day–1 week | 1 week–1 month | >1 month |
| **Change Failure Rate** | <5% | 5-10% | 10-15% | >15% |
| **Time to Restore Service** | <1 hour | <1 day | 1 day–1 week | >1 week |

### SPACE Framework
Source: Forsgren et al., 2021

| Dimension | What It Measures | Example Metrics |
|-----------|-----------------|-----------------|
| **S**atisfaction | Developer happiness | eNPS, satisfaction surveys |
| **P**erformance | Code/commit quality | PR review time, defect rate |
| **A**ctivity | Output volume | Commits, PRs, deployments |
| **C**ommunication | Collaboration | Review turnaround, doc updates |
| **E**fficiency | Flow, interruptions | Focus time, context switches |

### Developer Experience Survey

```
Questions (1-5 scale):
1. I can get my development environment set up in <1 hour
2. I can deploy to staging without asking anyone
3. I can find documentation when I need it
4. Our CI/CD pipeline is fast and reliable
5. I spend most of my time on value-adding work
6. I can debug production issues efficiently
7. Our tools and processes don't slow me down
8. I understand how our systems work together
```

## Step 6: Platform Components

### Feature Flags Platform

```
Architecture:
┌──────────┐    ┌───────────┐    ┌──────────┐
│ App Code  │───▶│ Feature   │───▶│ Flag     │
│           │    │ Flag SDK  │    │ Store    │
└──────────┘    └───────────┘    └────┬─────┘
                                      │
                               ┌──────┴──────┐
                               │ Admin UI    │
                               │ (targeting, │
                               │  rollout %) │
                               └─────────────┘

Tools: LaunchDarkly, Unleash, Flagsmith, GrowthBook (open-source)
```

### Secrets Management

```
Pattern: Centralized secrets with per-service access

┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ Vault        │    │ K8s Secrets  │    │ Env Vars     │
│ (HashiCorp/  │───▶│ (synced from │───▶│ (injected    │
│  AWS SM/GSM) │    │  vault)      │    │  at runtime) │
└──────────────┘    └──────────────┘    └──────────────┘

Rotation: Auto-rotate every 90 days, zero-downtime rotation
Access: RBAC per service, audit logging, break-glass procedure
```

### Environment Management

```
Environments:
- Local: Developer laptop (docker-compose)
- Preview: PR-based ephemeral environments
- Staging: Shared, production-like
- Production: Live, protected

Pattern: Environment-as-Code
- Terraform/Pulumi for infrastructure
- Helm/Kustomize for app config
- GitOps (Argo CD) for deployment
```

## Step 7: Platform Adoption & Change Management

### Adoption Strategy

```
Phase 1: Build Trust (Month 1-3)
  - Embed with 2-3 pilot teams
  - Solve their top pain point
  - Collect testimonials and metrics
  - Iterate based on feedback

Phase 2: Prove Value (Month 3-6)
  - Publish before/after metrics
  - Run internal demo days
  - Create migration guides
  - Offer office hours

Phase 3: Scale (Month 6-12)
  - Make platform the default path
  - Sunset legacy alternatives
  - Add self-service onboarding
  - Track adoption metrics

Phase 4: Optimize (Month 12+)
  - Reduce toil in platform itself
  - Add advanced features based on demand
  - Measure developer satisfaction
  - Publish internal case studies
```

### Platform Adoption Metrics

```
Leading Indicators:
  - Teams onboarded to platform: X/total
  - Services using golden path: X/total
  - Template usage count: X/month
  - Platform API calls: X/day
  - Self-service actions: X/day

Lagging Indicators:
  - Time to first deployment: before vs after
  - Onboarding time for new engineers: before vs after
  - Production incidents: before vs after
  - Developer satisfaction score: before vs after
  - DORA metrics improvement: before vs after
```

### Internal Developer Portal (IDP) Comparison

| Portal | Backing | Type | Best For |
|--------|---------|------|----------|
| Backstage | Spotify, CNCF | Open-source | Large orgs, customization |
| Port | Port.io | SaaS | Quick setup, enterprise |
| Cortex | Cortex.io | SaaS | Service ownership |
| OpsLevel | OpsLevel | SaaS | Service maturity |
| configure8 | configure8 | SaaS | Multi-cloud |
| Kratix | Syntasso | Open-source | Platform-as-product |

## Step 8: Platform Team Anti-Patterns

### Anti-Pattern: Ivory Tower

```
Symptom: Platform team builds in isolation, ships features nobody asked for.
Cause: No embedded relationship with product teams.
Fix: Platform engineers pair with product teams 1 day/week.
```

### Anti-Pattern: Ticket Queue

```
Symptom: Platform team becomes a ticket queue ("create me a database").
Cause: No self-service, everything requires manual intervention.
Fix: Build self-service APIs and CLIs for common requests.
```

### Anti-Pattern: Golden Cage

```
Symptom: Developers can't do anything outside the golden path.
Cause: Platform is too rigid, no escape hatches.
Fix: Golden path = easiest path, not only path. Allow opt-out with documented trade-offs.
```

### Anti-Pattern: Resume-Driven Development

```
Symptom: Platform team picks trendy tech (K8s, service mesh, eBPF) without business need.
Cause: Engineers optimize for learning, not business value.
Fix: Every platform decision must map to a developer pain point.
```

## Step 9: Platform-as-Code Examples

### Service Template (cookiecutter)

```yaml
# cookiecutter.json
{
  "service_name": "my-service",
  "team_name": "platform",
  "language": ["python", "go", "typescript"],
  "database": ["postgres", "mysql", "none"],
  "cache": ["redis", "none"],
  "ci_provider": ["github-actions", "gitlab-ci"],
  "cloud": ["aws", "gcp", "azure"]
}
```

### Terraform Module for New Service

```hcl
module "new_service" {
  source = "./modules/service"
  
  name        = var.service_name
  team        = var.team_name
  language    = var.language
  cpu         = "500m"
  memory      = "512Mi"
  replicas    = 2
  
  database = var.database != "none" ? {
    engine  = var.database
    size    = "db.t3.micro"
    storage = 20
  } : null
  
  monitoring = {
    enabled   = true
    pagerduty = var.pagerduty_service_id
    dashboards = true
  }
}
```

### GitHub Actions Golden Path Pipeline

```yaml
name: Service CI/CD
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    uses: org/platform/.github/workflows/test.yml@v2
    with:
      language: ${{ vars.SERVICE_LANGUAGE }}
  
  security:
    uses: org/platform/.github/workflows/security.yml@v2
  
  deploy-staging:
    needs: [test, security]
    if: github.ref == 'refs/heads/main'
    uses: org/platform/.github/workflows/deploy.yml@v2
    with:
      environment: staging
  
  deploy-production:
    needs: deploy-staging
    uses: org/platform/.github/workflows/deploy.yml@v2
    with:
      environment: production
    environment: production
```

## Pitfalls

1. **Platform without users** — Build what developers need, not what you think they need. Survey first.
2. **Golden cage, not golden path** — If the platform is too rigid, developers can't do their jobs. Allow escape hatches.
3. **Premature abstraction** — Don't build abstractions until you have 3+ concrete use cases.
4. **No feedback loop** — Platform teams must sit with developer teams regularly.
5. **Measuring activity not impact** — "We deployed 50 features" ≠ "Developers are more productive."
6. **Copy-paste Backstage** — Backstage is a framework, not a product. Expect 6+ months of customization.
7. **Ignoring developer experience** — If the platform CLI has 200 commands, developers won't use it.
8. **No adoption strategy** — Platform doesn't sell itself. Run enablement sessions, write docs, embed with teams.
9. **Over-engineering** — Start with a simple CLI or wiki. Scale to full IDP when complexity demands it.
10. **Shadow IT** — If developers bypass the platform, the platform isn't solving their problems. Find out why.

## Sources

- Backstage (Spotify): https://backstage.io/docs/
- Team Topologies: https://teamtopologies.com/
- DORA / State of DevOps: https://dora.dev
- SPACE Framework: https://queue.acm.org/detail.cfm?id=3454121
- CNCF Platform White Paper: https://tag-app-delivery.cncf.io/
- Port.io State of IDP: https://www.port.io/state-of-internal-developer-portals
- Humanitec Platform Orchestrator: https://humanitec.com/
- Gartner Platform Engineering: https://www.gartner.com/en/articles/what-is-platform-engineering
- Spotify Engineering: https://engineering.atspotify.com/
- Netflix Platform: https://netflixtechblog.com/
