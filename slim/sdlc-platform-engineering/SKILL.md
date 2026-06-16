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

---
name: sdlc-platform-engineering
description: "Platform engineering: internal developer portals (IDP), Backstage, golden paths, service catalog, self-service infrastructure, platform-as-a-product, developer productivity metrics (SPACE, DORA), platform team structure, paved roads, template repos, feature flags, secrets management, environment management."
version: 6.0.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, platform-engin
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
## Step 2: Internal Developer Portal (IDP)
Single pane of glass for developers to:
- Discover services (software catalog)
- Create new services (templates)
- View documentation
- Manage environments
- Monitor health
- Track DORA metrics
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
```
## Step 3: Golden Paths & Paved Roads
A golden path is the opinionated, supported, easiest way to do something. Not a mandate — the path of least resistance.
```
Golden Path: "Use our Node.js template with Postgres, deployed to K8s 
via our CI pipeline. Here's the template."

Alternative: "You can use whatever you want, but you own everything 
— CI, deployment, monitoring, on-call."

Most teams choose the golden path because it's easier.
```
## Step 4: Self-Service Infrastructure
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
## Step 5: Developer Productivity Metrics
Source: Google DORA / State of DevOps
| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| **Deployment Frequency** | On-demand (multiple/day) | Weekly–monthly | Monthly–quarterly | <Quarterly |
| **Lead Time for Changes** | <1 hour | 1 day–1 week | 1 week–1 month | >1 month |
| **Change Failure Rate** | <5% | 5-10% | 10-15% | >15% |
| **Time to Restore Service** | <1 hour | <1 day | 1 day–1 week | >1 week |
Source: Forsgren et al., 2021
| Dimension | What It Measures | Example Metrics |
|-----------|-----------------|-----------------|
| **S**atisfaction | Developer happiness | eNPS, satisfaction surveys |
| **P**erformance | Code/commit quality | PR review time, defect rate |
| **A**ctivity | Output volume | Commits, PRs, deployments |
| **C**ommunication | Collaboration | Review turnaround, doc updates |
| **E**fficiency | Flow, interruptions | Focus time, context switches |
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
## Step 7: Platform Adoption & Change Management
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
```
## Step 8: Platform Team Anti-Patterns
```
Symptom: Platform team builds in isolation, ships features nobody asked for.
Cause: No embedded relationship with product teams.
Fix: Platform engineers pair with product teams 1 day/week.
```
## Step 9: Platform-as-Code Examples
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
```
## Step 10: Platform Team Structure
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
```
## Step 14: Observability & Monitoring
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
```
## Step 15: GitOps & Infrastructure as Code
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
```
## Step 16: Developer Productivity
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
```
## Step 14: Observability and Monitoring
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
```
## Step 15: GitOps and Infrastructure as Code
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
```
## Step 16: Developer Productivity
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
```
## Step 17: Service Mesh and Networking
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
```
## Step 18: Database Operations
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
```
## Step 19: Chaos Engineering
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
```
## Step 20: Platform as a Product
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
```
## Step 21: SRE Practices
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
```
## Step 22: Kubernetes Operations
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
```
## Step 23: CI/CD for Platform
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
```
## Step 24: Platform Security
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
```
## Step 25: Platform Team Operations
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
```
## Step 26: Platform Documentation
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
```
## Step 27: Platform Budget Management
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
```
## Step 28: Platform Vendor Management
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
```
## Step 29: Platform API Design
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
```
## Step 30: Platform Testing Strategy
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
```
## Step 31: Platform Migration Strategies
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
```
## Step 32: Platform Governance
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
```
## Step 33: Platform Metrics
```
Deployment frequency:
  - How often code is deployed to production
  - Elite: Multiple times per day
  - High: Once per day to once per week
  - Medium: Once per week to once per month
  - Low: Less than once per month

Lead time for changes:
  - Time from commit to production
  - Elite: Less than 1 hour
  - High: 1 day to 1 week
  - Medium: 1 week to 1 month
  - Low: More than 1 month

Change failure rate:
```
## Related Skills
  - [sdlc-developer-relations](sdlc-developer-relations): Developer Relations (DevRel) program design: advocacy, community, marketing, enablement. Developer e
  - [sdlc-observability](sdlc-observability): Observability: OpenTelemetry 2024, GenAI semantic conventions, eBPF (Cilium/Hubble/Tetragon), sideca
  - [sdlc-cicd-pipeline](sdlc-cicd-pipeline): CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix