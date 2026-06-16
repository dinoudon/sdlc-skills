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

## Step 9: Cost Optimization & FinOps

### Cloud Cost Allocation

```
Tagging strategy:
  - team: engineering, data, ml
  - service: api, web, worker, database
  - environment: production, staging, dev
  - cost-center: product, infrastructure, r&d

Showback/Chargeback model:
  Showback (informational):
    - Monthly cost report per team
    - Trend analysis
    - Anomaly detection
    
  Chargeback (billing):
    - Internal billing per team
    - Budgets with alerts
    - Incentives for optimization

Cost optimization checklist:
  □ Right-size instances (CPU/memory utilization <40%)
  □ Reserved instances for predictable workloads (1-3 year)
  □ Spot instances for batch/ML training (60-90% savings)
  □ Auto-scaling policies (scale down during off-hours)
  □ Storage lifecycle (hot → warm → cold → archive)
  □ Database optimization (connection pooling, query optimization)
  □ CDN for static assets (reduce origin load)
  □ Compression (gzip/brotli for API responses)
  □ Idle resource cleanup (orphaned volumes, unused IPs)
```

### FinOps Dashboard

```
Key metrics:
  - Total cloud spend (monthly trend)
  - Cost per customer (unit economics)
  - Cost per transaction/request
  - Waste % (idle, over-provisioned)
  - Savings from optimization
  
Tools:
  - AWS Cost Explorer / GCP Cost Management / Azure Cost Management
  - Third-party: CloudHealth, Spot.io, Kubecost (Kubernetes)
  - Open source: OpenCost, Infracost (IaC cost estimation)
  
Alerts:
  - Daily spend exceeds 120% of daily budget
  - Unusual spike in specific service (>50% increase)
  - New resource type created (not in approved list)
  - Reserved instance utilization drops below 80%
```

## Step 10: Platform Team Structure

### Team Topologies for Platform

```
Platform Team Types:

1. Platform Product Team
   - Owns developer experience
   - Builds internal developer platform
   - Treats developers as customers
   - Measures adoption and satisfaction
   
2. Platform Infrastructure Team
   - Owns cloud infrastructure
   - Manages Kubernetes, networking, security
   - Provides infrastructure-as-code modules
   - On-call for infrastructure incidents
   
3. Platform Enablement Team
   - Coaching and training
   - Documentation and best practices
   - Community of practice facilitation
   - Tool evaluation and adoption

Team size:
  - Small org (<100 devs): 1 platform team (5-8 people)
  - Medium org (100-500): 2-3 platform teams
  - Large org (500+): Platform org with multiple teams
  
Reporting:
  - Reports to VP Engineering or CTO
  - Not under any product team (independence)
  - Regular stakeholder council with product teams
```

### Platform Adoption Metrics

```
Adoption metrics:
  - % of services on platform
  - % of deploys through platform
  - Self-service ratio (no tickets needed)
  - Time to onboard new service
  
Efficiency metrics:
  - Deploy frequency (before/after platform)
  - Lead time for changes
  - MTTR (mean time to recovery)
  - Change failure rate
  
Satisfaction metrics:
  - Developer NPS (quarterly survey)
  - Support ticket volume (should decrease)
  - Feature requests (what's missing)
  - Documentation satisfaction


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


## Step 14: Observability & Monitoring

### Observability Stack

```
Three pillars:
  1. Logs: Structured events
     - Format: JSON with correlation IDs
     - Levels: DEBUG, INFO, WARN, ERROR, FATAL
     - Tools: ELK Stack, Loki, Datadog Logs
     
  2. Metrics: Numerical measurements
     - Types: Counter, Gauge, Histogram, Summary
     - Labels: service, environment, endpoint
     - Tools: Prometheus, Datadog, New Relic
     
  3. Traces: Request flow across services
     - Distributed tracing with correlation IDs
     - Span hierarchy (service → operation → sub-operation)
     - Tools: Jaeger, Zipkin, Datadog APM

Golden signals (Google SRE):
  - Latency: Time to serve request
  - Traffic: Requests per second
  - Errors: Error rate (% of requests)
  - Saturation: Resource utilization (%)
```

### Alerting Strategy

```
Alert severity:
  P1 (Page): Service down, data loss, immediate response
  P2 (Urgent): Degraded performance, response within 1 hour
  P3 (Warning): Anomaly detected, response within 24 hours
  P4 (Info): Informational, no action required

Alert design principles:
  - Actionable: Every alert should have a clear response
  - Unique: No duplicate alerts for same issue
  - Contextual: Include relevant metrics and logs
  - Escalating: Auto-escalate if not acknowledged
  - Documented: Runbook link for each alert

Anti-patterns:
  ❌ Alert fatigue (too many non-actionable alerts)
  ❌ Alert storms (cascading alerts during outages)
  ❌ Missing context (alert without runbook)
  ❌ False positives (tune thresholds)
  ❌ Alerting on symptoms, not causes
```

## Step 15: GitOps & Infrastructure as Code

### GitOps Workflow

```
Repository structure:
  /infrastructure
    /environments
      /dev
        - main.tf
        - variables.tf
        - terraform.tfvars
      /staging
        - main.tf
        - variables.tf
        - terraform.tfvars
      /production
        - main.tf
        - variables.tf
        - terraform.tfvars
    /modules
      /vpc
      /eks
      /rds
    /shared
      - backend.tf
      - providers.tf

Workflow:
  1. Developer creates feature branch
  2. Changes to infrastructure code
  3. PR triggers plan (terraform plan)
  4. Review and approve
  5. Merge triggers apply (terraform apply)
  6. Drift detection (continuous)

Tools:
  - Terraform: Infrastructure provisioning
  - Atlantis: Terraform PR automation
  - Spacelift: Terraform management platform
  - Crossplane: Kubernetes-native infrastructure
  - Pulumi: Infrastructure as code (programming languages)
```

## Step 16: Developer Productivity

### Developer Experience Metrics

```
DORA metrics:
  1. Deployment frequency: How often code is deployed
  2. Lead time for changes: Commit to production
  3. Change failure rate: % of deployments causing failures
  4. Time to restore service: Recovery from failures

SPACE framework:
  S - Satisfaction and well-being
  P - Performance (code review, PR throughput)
  A - Activity (commits, PRs, reviews)
  C - Communication and collaboration
  E - Efficiency (flow state, interruptions)

Measurement tools:
  - Sleuth: DORA metrics tracking
  - LinearB: Engineering metrics
  - Pluralsight Flow: Code review analytics
  - Jellyfish: Engineering management
  - Athenian: Git analytics

Targets (elite performers):
  - Deploy frequency: Multiple times per day
  - Lead time: < 1 hour
  - Change failure rate: < 5%
  - Time to restore: < 1 hour


## Step 14: Observability and Monitoring

### Observability Stack

```
Three pillars:
  1. Logs: Structured events
     - Format: JSON with correlation IDs
     - Levels: DEBUG, INFO, WARN, ERROR, FATAL
     - Tools: ELK Stack, Loki, Datadog Logs
     
  2. Metrics: Numerical measurements
     - Types: Counter, Gauge, Histogram, Summary
     - Labels: service, environment, endpoint
     - Tools: Prometheus, Datadog, New Relic
     
  3. Traces: Request flow across services
     - Distributed tracing with correlation IDs
     - Span hierarchy (service then operation then sub-operation)
     - Tools: Jaeger, Zipkin, Datadog APM

Golden signals (Google SRE):
  - Latency: Time to serve request
  - Traffic: Requests per second
  - Errors: Error rate (% of requests)
  - Saturation: Resource utilization (%)
```

### Alerting Strategy

```
Alert severity:
  P1 (Page): Service down, data loss, immediate response
  P2 (Urgent): Degraded performance, response within 1 hour
  P3 (Warning): Anomaly detected, response within 24 hours
  P4 (Info): Informational, no action required

Alert design principles:
  - Actionable: Every alert should have a clear response
  - Unique: No duplicate alerts for same issue
  - Contextual: Include relevant metrics and logs
  - Escalating: Auto-escalate if not acknowledged
  - Documented: Runbook link for each alert

Anti-patterns:
  - Alert fatigue (too many non-actionable alerts)
  - Alert storms (cascading alerts during outages)
  - Missing context (alert without runbook)
  - False positives (tune thresholds)
  - Alerting on symptoms, not causes
```

## Step 15: GitOps and Infrastructure as Code

### GitOps Workflow

```
Repository structure:
  /infrastructure
    /environments/dev/main.tf, variables.tf, terraform.tfvars
    /environments/staging/main.tf, variables.tf, terraform.tfvars
    /environments/production/main.tf, variables.tf, terraform.tfvars
    /modules/vpc, /modules/eks, /modules/rds
    /shared/backend.tf, providers.tf

Workflow:
  1. Developer creates feature branch
  2. Changes to infrastructure code
  3. PR triggers plan (terraform plan)
  4. Review and approve
  5. Merge triggers apply (terraform apply)
  6. Drift detection (continuous)

Tools:
  - Terraform: Infrastructure provisioning
  - Atlantis: Terraform PR automation
  - Spacelift: Terraform management platform
  - Crossplane: Kubernetes-native infrastructure
  - Pulumi: Infrastructure as code (programming languages)
```

## Step 16: Developer Productivity

### Developer Experience Metrics

```
DORA metrics:
  1. Deployment frequency: How often code is deployed
  2. Lead time for changes: Commit to production
  3. Change failure rate: % of deployments causing failures
  4. Time to restore service: Recovery from failures

SPACE framework:
  S - Satisfaction and well-being
  P - Performance (code review, PR throughput)
  A - Activity (commits, PRs, reviews)
  C - Communication and collaboration
  E - Efficiency (flow state, interruptions)

Measurement tools:
  - Sleuth: DORA metrics tracking
  - LinearB: Engineering metrics
  - Pluralsight Flow: Code review analytics
  - Jellyfish: Engineering management
  - Athenian: Git analytics

Targets (elite performers):
  - Deploy frequency: Multiple times per day
  - Lead time: < 1 hour
  - Change failure rate: < 5%
  - Time to restore: < 1 hour
```


## Step 17: Service Mesh and Networking

### Service Mesh Architecture

```
Components:
  Data plane:
    - Sidecar proxies (Envoy, Linkerd-proxy)
    - Intercept all service-to-service traffic
    - Handle mTLS, load balancing, retries
    
  Control plane:
    - Configuration management
    - Certificate authority
    - Service discovery
    - Policy enforcement

Features:
  Traffic management:
    - Load balancing (round-robin, least connections)
    - Circuit breaking (prevent cascade failures)
    - Retry policies (automatic retries with backoff)
    - Timeout configuration
    - Rate limiting
    
  Security:
    - mTLS (mutual TLS between services)
    - Authorization policies (who can call what)
    - Certificate rotation (automatic)
    
  Observability:
    - Distributed tracing (automatic)
    - Metrics collection (request rate, latency, errors)
    - Access logging (who called what)

Tools:
  - Istio: Full-featured, complex
  - Linkerd: Lightweight, simple
  - Consul Connect: HashiCorp ecosystem
  - Cilium: eBPF-based, high performance
```

### Kubernetes Networking

```
Network policies:
  Default deny: Block all traffic by default
  Allow specific: Whitelist required communication
  
  Example:
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
    name: api-policy
  spec:
    podSelector:
      matchLabels:
        app: api
    ingress:
    - from:
      - podSelector:
          matchLabels:
            app: web
      ports:
      - port: 8080
    egress:
    - to:
      - podSelector:
          matchLabels:
            app: database
      ports:
      - port: 5432
  ```

Ingress controllers:
  - NGINX: Most common, flexible
  - Traefik: Auto-discovery, Let's Encrypt
  - Kong: API gateway features
  - Ambassador: Kubernetes-native
  - Istio Gateway: Service mesh integration
```

## Step 18: Database Operations

### Database Reliability Patterns

```
Connection pooling:
  - PgBouncer (PostgreSQL)
  - ProxySQL (MySQL)
  - Application-level pooling (HikariCP, node-pg-pool)
  - Pool size: 2-4 connections per CPU core

Read replicas:
  - Primary: Handles writes
  - Replicas: Handle reads
  - Lag monitoring: Alert if >1 second
  - Failover: Promote replica to primary

Backup strategy:
  - Continuous: WAL archiving (PostgreSQL), binlog (MySQL)
  - Daily: Full backup
  - Weekly: Offsite backup
  - Testing: Monthly restore test

High availability:
  - Multi-AZ deployment (AWS RDS)
  - Automatic failover (30-60 seconds)
  - Connection draining (graceful failover)
  - Health checks (every 10-30 seconds)
```

### Database Migration Best Practices

```
Zero-downtime migrations:
  1. Add new column (nullable)
  2. Deploy code that writes to both old and new
  3. Backfill existing data
  4. Deploy code that reads from new column
  5. Drop old column

Tools:
  - Flyway: SQL-based migrations
  - Liquibase: XML/YAML/SQL migrations
  - Alembic: Python (SQLAlchemy)
  - Django migrations: Python (Django ORM)
  - Prisma Migrate: TypeScript (Prisma)

Rollback strategy:
  - Every migration must have a rollback script
  - Test rollback in staging first
  - Keep migrations small and atomic
  - Never drop columns in same migration as add
```

## Step 19: Chaos Engineering

### Chaos Engineering Principles

```
Steady state hypothesis:
  "Under normal conditions, our API responds in <200ms with 99.9% uptime"

Experiment design:
  1. Define steady state (metrics that indicate healthy)
  2. Hypothesize (what happens if X fails?)
  3. Introduce failure (kill pod, add latency, fill disk)
  4. Observe (did steady state hold?)
  5. Learn (what broke? what needs improvement?)

Common experiments:
  - Pod termination: Kill random pods
  - Network latency: Add 500ms delay
  - Network partition: Block traffic between services
  - CPU stress: Consume 80% CPU
  - Memory pressure: Fill memory
  - Disk pressure: Fill disk
  - DNS failure: Block DNS resolution
  - Certificate expiry: Expire TLS certificates

Tools:
  - Chaos Monkey (Netflix): Random instance termination
  - Litmus: Kubernetes chaos engineering
  - Chaos Mesh: Kubernetes chaos platform
  - Gremlin: Enterprise chaos engineering
  - Toxiproxy: Network fault injection
```

### Game Day Playbook

```
Planning (2 weeks before):
  - Define scope (which services, which failures)
  - Notify stakeholders (no surprises)
  - Prepare rollback plan
  - Set up monitoring dashboards
  - Assign roles (chaos engineer, observer, communicator)

Execution:
  1. Verify steady state (all metrics healthy)
  2. Announce start (Slack, email)
  3. Inject failure (one at a time)
  4. Monitor (watch dashboards, alerts)
  5. Observe response (did team respond correctly?)
  6. Restore (fix the failure)
  7. Verify recovery (back to steady state)
  8. Repeat with next failure

Post-game day:
  - Blameless retrospective
  - Document findings
  - Create tickets for improvements
  - Update runbooks
  - Schedule next game day (quarterly)
```

## Step 20: Platform as a Product

### Platform Product Management

```
Treat platform as product:
  - Customers: Internal development teams
  - Product manager: Platform PM
  - Roadmap: Based on developer needs
  - Metrics: Adoption, satisfaction, efficiency
  
Platform backlog:
  - Developer feature requests
  - Reliability improvements
  - Security enhancements
  - Cost optimization
  - Compliance requirements

Prioritization:
  - RICE score (Reach, Impact, Confidence, Effort)
  - Developer vote (internal feature voting)
  - Usage data (what features are most used)
  - Support tickets (common pain points)
  
Communication:
  - Monthly platform newsletter
  - Quarterly roadmap review
  - Weekly office hours
  - Slack channel for questions
  - Changelog for every release
```

### Platform Adoption Metrics

```
Adoption metrics:
  - % of services on platform
  - % of deploys through platform
  - Self-service ratio (no tickets needed)
  - Time to onboard new service
  
Efficiency metrics:
  - Deploy frequency (before/after platform)
  - Lead time for changes
  - MTTR (mean time to recovery)
  - Change failure rate
  
Satisfaction metrics:
  - Developer NPS (quarterly survey)
  - Support ticket volume (should decrease)
  - Feature requests (what's missing)
  - Documentation satisfaction


## Step 21: SRE Practices

### SRE Principles

```
Service Level Objectives (SLOs):
  - Define reliability targets per service
  - Based on user expectations, not technical limits
  - Examples:
    - API: 99.9% availability, p99 latency <500ms
    - Web: 99.5% availability, LCP <2.5s
    - Background jobs: 99% success rate, <5 min processing

Error budgets:
  - Error budget = 1 - SLO
  - Example: 99.9% SLO = 0.1% error budget per month
  - Budget spent: Slow down feature releases, focus on reliability
  - Budget remaining: Accelerate feature development

Toil reduction:
  - Definition: Manual, repetitive, automatable work
  - Target: <50% of SRE time on toil
  - Process:
    1. Measure toil (time tracking)
    2. Prioritize by impact
    3. Automate highest-impact toil
    4. Track reduction over time

Incident management:
  - Severity levels (P1-P4)
  - On-call rotation (1 week, 2 people)
  - Escalation policy (auto-escalate after 15 min)
  - Post-mortem process (blameless, action items)
  - Communication templates (status page, stakeholders)
```

### Capacity Planning

```
Capacity model:
  Current capacity:
    - CPU: 80% utilization at peak
    - Memory: 70% utilization at peak
    - Storage: 60% utilization
    - Network: 40% utilization
  
  Growth rate:
    - Traffic: 10% month-over-month
    - Data: 20% month-over-month
    - Users: 15% month-over-month
  
  Planning horizon:
    - Short-term (1 month): Auto-scaling, spot instances
    - Medium-term (3 months): Reserved instances, scaling events
    - Long-term (12 months): Architecture review, major upgrades

Capacity triggers:
  - CPU > 70% sustained: Scale horizontally
  - Memory > 80% sustained: Scale vertically
  - Storage > 70%: Add storage, archive old data
  - Network > 60%: CDN, optimize payloads
```

## Step 22: Kubernetes Operations

### Kubernetes Best Practices

```
Resource management:
  - Set requests and limits for all containers
  - Use Vertical Pod Autoscaler (VPA) for right-sizing
  - Use Horizontal Pod Autoscaler (HPA) for scaling
  - Set Pod Disruption Budgets (PDB) for availability

Security:
  - Network policies (default deny)
  - Pod security standards (restricted, baseline, privileged)
  - RBAC (least privilege)
  - Secret management (external secrets operator)
  - Image scanning (Trivy, Grype)
  - OPA/Gatekeeper policies

Observability:
  - Prometheus + Grafana for metrics
  - Loki for logs
  - Jaeger for traces
  - Kubernetes events monitoring
  - Node and pod health checks

Operations:
  - GitOps for deployments (ArgoCD, Flux)
  - Canary deployments (Argo Rollouts, Flagger)
  - Blue-green deployments
  - Rolling updates with health checks
  - Automated rollbacks
```

### Kubernetes Cost Optimization

```
Right-sizing:
  - Use VPA recommendations
  - Set appropriate resource requests
  - Remove unused resources
  - Consolidate small pods

Spot/preemptible instances:
  - Use for stateless workloads
  - Implement graceful shutdown
  - Diversify instance types
  - Use spot termination handler

Cluster autoscaling:
  - Scale down during off-hours
  - Use cluster autoscaler
  - Set pod priority and preemption
  - Implement node affinity for workload placement

Tools:
  - Kubecost: Kubernetes cost monitoring
  - OpenCost: Open-source cost allocation
  - Goldilocks: VPA recommendations
  - kube-resource-report: Resource usage reports
```

## Step 23: CI/CD for Platform

### Platform CI/CD Pipeline

```
Infrastructure pipeline:
  1. Code change (Terraform, Helm chart)
  2. Plan (terraform plan, helm template)
  3. Validate (OPA policies, security scan)
  4. Review (PR approval)
  5. Apply (terraform apply, helm upgrade)
  6. Verify (health checks, smoke tests)
  7. Monitor (observability dashboards)

Application pipeline:
  1. Code commit
  2. Build (Docker image)
  3. Test (unit, integration, e2e)
  4. Scan (SAST, dependency, container)
  5. Push (container registry)
  6. Deploy (staging → production)
  7. Verify (health checks, canary analysis)
  8. Promote (canary → full rollout)

Tools:
  - GitHub Actions: CI/CD
  - ArgoCD: GitOps deployment
  - Tekton: Kubernetes-native CI/CD
  - Dagger: Programmable CI/CD
  - Earthly: Containerized builds
```

## Step 24: Platform Security

### Security Automation

```
Shift-left security:
  - IDE plugins (SAST, dependency scanning)
  - Pre-commit hooks (secrets detection)
  - PR checks (security scanning)
  - Automated remediation suggestions

Pipeline security:
  - SAST in CI (Semgrep, CodeQL)
  - DAST for web apps (OWASP ZAP)
  - Dependency scanning (Snyk, Dependabot)
  - Container scanning (Trivy, Grype)
  - Secret scanning (GitLeaks, TruffleHog)

Runtime security:
  - WAF (Web Application Firewall)
  - RASP (Runtime Application Self-Protection)
  - Container runtime security (Falco)
  - Network monitoring (Cilium Hubble)
  - Anomaly detection (ML-based)

Compliance automation:
  - Policy-as-code (OPA, Sentinel)
  - Drift detection (continuous compliance)
  - Audit logging (immutable logs)
  - Evidence collection (automated)
```


## Step 25: Platform Team Operations

### On-Call Rotation

```
Rotation structure:
  - Primary: 1 engineer, 1-week rotation
  - Secondary: 1 engineer, 1-week rotation
  - Escalation: Team lead → Engineering manager → VP Eng

On-call responsibilities:
  - Respond to pages within 5 minutes
  - Triage and mitigate incidents
  - Escalate if unable to resolve
  - Document incidents in post-mortem
  - Hand off to next rotation

Compensation:
  - On-call stipend: $200-500/week
  - Incident response: Hourly rate for active work
  - Weekend/holiday: 2x multiplier
  - Post-incident recovery time: Comp time

Tools:
  - PagerDuty: Alerting and escalation
  - Opsgenie: On-call management
  - VictorOps: Incident management
  - Slack: War room communication
  - Zoom: Incident bridge calls
```

### Incident Response Playbook

```
Detection:
  - Automated alerts (monitoring systems)
  - Customer reports (support tickets)
  - Internal discovery (team member notices)

Triage (5 minutes):
  1. Assess severity (P1-P4)
  2. Determine scope (which services, how many users)
  3. Assign incident commander
  4. Create incident channel (Slack)
  5. Post initial status update

Response:
  1. Investigate (logs, metrics, traces)
  2. Mitigate (rollback, scale, failover)
  3. Communicate (status page, stakeholders)
  4. Resolve (fix root cause or implement workaround)
  5. Verify (confirm service restored)

Post-incident:
  1. Blameless post-mortem (within 48 hours)
  2. Document root cause
  3. Create action items (preventive measures)
  4. Update runbooks
  5. Share learnings (team, organization)

Communication templates:
  Initial: "We are aware of [issue] affecting [service]. 
  Investigating. Updates every 30 minutes."
  
  Update: "Root cause identified: [cause]. Implementing fix. 
  ETA: [time]."
  
  Resolved: "Issue resolved. [Service] fully operational. 
  Post-mortem to follow."
```

## Step 26: Platform Documentation

### Internal Documentation Standards

```
Runbooks:
  - Purpose: Step-by-step operational procedures
  - Audience: On-call engineers, SREs
  - Format: Numbered steps, expected output, rollback
  - Review: Quarterly, after every incident use

Architecture docs:
  - Purpose: System design and decisions
  - Audience: All engineers
  - Format: C4 diagrams, ADRs, decision records
  - Review: On significant changes

API docs:
  - Purpose: Internal API contracts
  - Audience: Service consumers
  - Format: OpenAPI spec, code examples
  - Review: On every API change

Onboarding docs:
  - Purpose: New team member ramp-up
  - Audience: New hires
  - Format: Checklist, progressive complexity
  - Review: After every new hire (incorporate feedback)

Knowledge base:
  - Purpose: Tribal knowledge capture
  - Audience: All team members
  - Format: FAQ, troubleshooting, how-to
  - Review: Monthly, archive stale content
```

### Documentation Tooling

```
Wiki/knowledge base:
  - Confluence: Enterprise wiki
  - Notion: Flexible documentation
  - GitBook: Developer documentation
  - Slite: Team knowledge base

Diagramming:
  - Mermaid: Code-based diagrams
  - Excalidraw: Hand-drawn style
  - draw.io: General purpose
  - Lucidchart: Enterprise diagramming

Architecture decision records:
  - adr-tools: CLI for ADRs
  - Log4brains: ADR management
  - Custom: Markdown in repo

Docs-as-code:
  - Markdown in Git
  - PR-based review
  - Automated publishing
  - Version control
```

## Step 27: Platform Budget Management

### Budget Planning

```
Cost categories:
  Infrastructure:
    - Cloud compute (AWS, GCP, Azure): 40-50%
    - Storage (block, object, database): 15-20%
    - Networking (CDN, load balancers): 5-10%
    - Third-party services (monitoring, security): 10-15%
  
  Tooling:
    - CI/CD (GitHub Actions, CircleCI): 5-8%
    - Developer tools (IDEs, licenses): 3-5%
    - Collaboration (Slack, Zoom, Notion): 2-3%
  
  People:
    - Platform team salaries: Separate budget
    - Training and certifications: $2-5K/person
    - Conference attendance: $3-5K/person

Budget review:
  - Monthly: Actual vs budget analysis
  - Quarterly: Forecast update
  - Annually: Budget planning for next year

Cost optimization:
  - Reserved instances (1-3 year commitment)
  - Spot instances (batch workloads)
  - Right-sizing (match capacity to demand)
  - Auto-scaling (scale down during off-hours)
  - Storage lifecycle (hot → warm → cold → archive)
```

## Step 28: Platform Vendor Management

### Vendor Evaluation

```
Evaluation criteria:
  Technical (40%):
    - Feature completeness
    - Performance and reliability
    - Integration capabilities
    - API quality and documentation
  
  Commercial (30%):
    - Pricing model (usage, seat, flat)
    - Contract flexibility
    - Volume discounts
    - Payment terms
  
  Security (20%):
    - SOC 2 / ISO 27001
    - Data residency options
    - Encryption (at rest, in transit)
    - Access controls
  
  Support (10%):
    - Response time SLA
    - Support channels
    - Documentation quality
    - Community size

Vendor management:
  - Annual review of all vendors
  - Consolidation opportunities
  - Contract renegotiation
  - Performance monitoring
  - Exit planning (data portability)
```


## Step 29: Platform API Design

### Internal API Standards

```
REST conventions:
  - Use nouns for resources (/users, /projects)
  - Use HTTP methods (GET, POST, PUT, DELETE)
  - Use plural nouns (/users not /user)
  - Use kebab-case for URLs (/user-profiles)
  - Use camelCase for JSON fields

Versioning:
  - URL versioning: /v1/users
  - Header versioning: Accept: application/vnd.company.v2+json
  - Recommendation: URL versioning for public APIs

Pagination:
  - Cursor-based (preferred for large datasets)
  - Offset-based (simpler, but less efficient)
  - Page size: 20-100 items (configurable)
  - Include: total count, has_more, next_cursor

Error format:
  ```json
  {
    "error": {
      "code": "invalid_request",
      "message": "The 'email' field is required",
      "param": "email",
      "doc_url": "https://docs.internal/errors#invalid_request"
    }
  }
  ```

Rate limiting:
  - Internal: 1000 requests/minute per service
  - External: 100 requests/minute per API key
  - Headers: X-RateLimit-Limit, X-RateLimit-Remaining
```

## Step 30: Platform Testing Strategy

### Testing Pyramid for Platform

```
                    E2E tests (few)
                   /              \
              Integration tests (some)
             /                      \
        Unit tests (many)

Unit tests:
  - Test individual functions/classes
  - Mock external dependencies
  - Fast execution (<1ms per test)
  - Coverage target: 80%+

Integration tests:
  - Test service interactions
  - Use real dependencies (databases, APIs)
  - Moderate speed (100ms-1s per test)
  - Coverage: Critical paths

E2E tests:
  - Test full user workflows
  - Use real environment
  - Slow execution (1-10s per test)
  - Coverage: Key user journeys

Platform-specific tests:
  - Infrastructure tests (Terraform plan)
  - Configuration tests (OPA policies)
  - Security tests (SAST, dependency scanning)
  - Performance tests (load, stress)
```

## Step 31: Platform Migration Strategies

### Migration Playbook

```
Assessment:
  - Current state documentation
  - Target state definition
  - Gap analysis
  - Risk assessment
  - Timeline estimation

Planning:
  - Migration phases (incremental)
  - Rollback strategy
  - Communication plan
  - Training requirements
  - Success criteria

Execution:
  - Pilot migration (1-2 services)
  - Validate (functionality, performance)
  - Iterate (fix issues found)
  - Scale (migrate remaining services)
  - Decommission (old system)

Validation:
  - Functional testing (feature parity)
  - Performance testing (latency, throughput)
  - Security testing (vulnerabilities)
  - User acceptance testing (stakeholder sign-off)

Common migration patterns:
  - Strangler fig: Gradually replace components
  - Big bang: Complete cutover (risky)
  - Parallel run: Both systems running simultaneously
  - Canary: Route small traffic to new system
```

## Step 32: Platform Governance

### Governance Framework

```
Decision rights:
  - Who can approve infrastructure changes?
  - Who can deploy to production?
  - Who can modify security policies?
  - Who can approve vendor selections?

Standards:
  - Coding standards (language, style)
  - Architecture standards (patterns, technologies)
  - Security standards (encryption, access)
  - Operational standards (monitoring, alerting)

Compliance:
  - Policy-as-code (OPA, Sentinel)
  - Automated compliance checks
  - Regular audits (quarterly)
  - Exception handling process

Communication:
  - Architecture decision records (ADRs)
  - Technical design documents
  - Change management process
  - Stakeholder updates (monthly)
```
