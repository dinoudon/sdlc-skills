---
name: sdlc-platform-engineering
description: "Platform engineering: internal developer portals (IDP), Backstage, golden paths, service catalog, self-service infrastructure, platform-as-a-product, developer productivity metrics (SPACE, DORA), platform team structure, paved roads, template repos, feature flags, secrets management, environment management."
version: 6.0.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, platform-engineering, idp, backstage, golden-paths, service-catalog, self-service, developer-productivity, paved-roads, dora, space-framework]
    related_skills: [sdlc-developer-tooling, sdlc-deployment, sdlc-observability, sdlc-cicd-pipeline, sdlc-architecture-design]
---## When to Use

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


## Pitfalls

1. **Platform without users** — Build what developers need, not what you think they need. Survey first.
2. **Golden cage, not golden path** — If the platform is too rigid, developers can't do their jobs. Allow escape hatches.
3. **Premature abstraction** — Don't build abstractions until you have 3+ concrete use cases.
4. **No feedback loop** — Platform teams must sit with developer teams regularly.
5. **Measuring activity not impact** — "We deployed 50 features" ≠ "Developers are more productive."

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
