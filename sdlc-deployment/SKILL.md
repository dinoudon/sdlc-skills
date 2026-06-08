---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling, progressive delivery (Flagger/Argo Rollouts), feature flags (LaunchDarkly/Unleash/OpenFeature), rollback, database migrations, zero-downtime patterns. v3: Gateway API traffic splitting, OpenFeature CNCF standard, FinOps (OpenCost/Karpenter/FOCUS), AnalysisTemplate, multi-cluster progressive delivery."
version: 3.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-green, rolling, feature-flags, progressive-delivery, flagger, argo-rollouts, kubernetes, zero-downtime, gateway-api, openfeature, finops, opencost, karpenter, analysis-template, multi-cluster, database-migration]
    related_skills: [sdlc-cicd-pipeline, sdlc-testing-qa, sdlc-observability]
---

# Deployment Strategies

Canary, blue-green, rolling, progressive delivery, feature flags, rollback, database migrations, zero-downtime patterns.

## When to Use

Trigger when user:
- Chooses deployment strategy
- Configures canary/blue-green/rolling deployments
- Sets up feature flags
- Implements rollback mechanisms
- Plans database migrations for zero-downtime

## Strategy Comparison

| Strategy | Zero-Downtime | Instant Rollback | Infra Cost | Complexity | Best For |
|----------|--------------|-----------------|------------|------------|----------|
| Rolling | Yes | No (slow) | Low | Low | Default K8s, low-risk |
| Blue-Green | Yes | Yes | High (2x) | Medium | Critical apps, DB-schema safe |
| Canary | Yes | Fast | Low-Medium | Medium | Gradual validation |
| Feature Flags | Yes | Yes (toggle off) | Low | Medium | Decoupling deploy/release |
| Progressive | Yes | Auto | Low-Medium | High | Automated safety at scale |

**Recommended combo:** Feature flags for logic control + Argo Rollouts/Flagger for infra-level canary + automated rollback.

## Step 1: Rolling Deployments

Kubernetes default strategy. Incrementally replace old pods with new ones.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2        # Extra pods during rollout
      maxUnavailable: 1   # Min pods that can be down
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:v2
```

**Key params:**
- `maxSurge`: max extra pods above desired count (int or %)
- `maxUnavailable`: max pods that can be unavailable during update
- `minReadySeconds`: time new pod must be ready before proceeding
- `progressDeadlineSeconds`: timeout before rollout marked failed

**AWS ECS rolling:** `minimumHealthyPercent: 75` means ECS replaces 25% at a time.

**Downside:** no instant rollback, mixed versions serve traffic simultaneously.

## Step 2: Blue-Green Deployments

Two identical environments (blue=live, green=new). Deploy to green, test, switch traffic atomically. Rollback = switch back to blue.

```yaml
# Argo CD blue-green strategy
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  strategy:
    blueGreen:
      activeService: myapp-active
      previewService: myapp-preview
      autoPromotionEnabled: false
      prePromotionAnalysis:
        templates:
        - templateName: smoke-tests
```

**Key requirement:** zero-downtime data migration — both versions must share same DB schema (or use expand-contract migration).

**AWS implementations:**
- Route53 weighted/latency routing between two ALB target groups
- Elastic Beanstalk blue-green: `eb clone`, swap CNAMEs
- ECS: second service behind same ALB, update listener rules

**Pros:** instant rollback, full production test before cutover.
**Cons:** 2x infrastructure cost during deployment.

## Step 3: Canary Deployments

Route small % traffic (5-10%) to new version. Monitor error rates, latency, saturation. Gradually increase if healthy.

```yaml
# Nginx Ingress canary annotation (legacy — prefer Gateway API)
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-weight: "10"
```

### Gateway API Traffic Splitting (Recommended)

Gateway API (GA since Kubernetes 1.1, CNCF) replaces Ingress annotations with
structured, portable traffic splitting via HTTPRoute. Works across Nginx, Envoy,
Cilium, Istio, and all conformant implementations.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: myapp-canary
spec:
  parentRefs:
  - name: myapp-gateway
    sectionName: https
  hostnames:
  - myapp.example.com
  rules:
  - backendRefs:
    - name: myapp-stable
      port: 8080
      weight: 90
    - name: myapp-canary
      port: 8080
      weight: 10
```

**Advantages over Nginx Ingress annotations:**
- Portable across implementations (no vendor lock-in)
- Structured CRDs (not annotation string parsing)
- Header-based, path-based, and method-based routing natively
- First-class in Flagger: `spec.service.gatewayRefs` for automatic HTTPRoute management
- Mesh support via GAMMA initiative (Gateway API for Mesh Management and Administration)

**Flagger + Gateway API integration:**
```yaml
apiVersion: flagger.app/v1beta1
kind: Canary
spec:
  service:
    gatewayRefs:
    - name: myapp-gateway
      namespace: infra
```

**Argo Rollouts + Gateway API:**
```yaml
spec:
  strategy:
    canary:
      trafficRouting:
        plugins:
          argoproj-labs/gatewayAPI:
            httpRoute: myapp-canary
            namespace: default
```

**Netflix canary:** Zuul + Spinnaker + Kayenta (automated canary analysis).

**AWS canary:**
- ALB weighted target groups
- Lambda aliases with traffic shifting
- CodeDeploy canary: `TrafficRoutingConfig: Type=TimeBasedCanary, Interval=5, Percentage=10`

## Step 4: Progressive Delivery

Automated canary/blue-green with metric-based promotion. "Canary with guardrails."

### Flagger (Flux ecosystem, CNCF)
Works with: Istio, Linkerd, Nginx, Contour, Gloo, Traefik, Gateway API.

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
  service:
    port: 8080
  analysis:
    interval: 1m
    threshold: 5         # Max failed checks before rollback
    maxWeight: 50        # Max canary traffic %
    stepWeight: 10       # Traffic increment per step
    metrics:
    - name: request-success-rate
      thresholdRange:
        min: 99          # Min 99% success
      interval: 1m
    - name: request-duration
      thresholdRange:
        max: 500         # Max 500ms p99
      interval: 1m
    webhooks:
    - name: acceptance-test
      type: pre-rollout
      url: http://flagger-loadtester.test/
      timeout: 30s
      metadata:
        type: bash
        cmd: "curl -sd 'test' http://myapp-canary.test:8080/api/health"
```

### Argo Rollouts (Argoproj)
CRD: `Rollout` (drop-in replacement for `Deployment`).

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
      - setWeight: 10
      - pause: {duration: 2m}
      - analysis:
          templates:
          - templateName: success-rate
      - setWeight: 30
      - pause: {duration: 5m}
      - setWeight: 60
      - pause: {duration: 5m}
      canaryService: myapp-canary
      stableService: myapp-stable
      trafficRouting:
        nginx:
          stableIngress: myapp-ingress
---
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: success-rate
spec:
  args:
  - name: service-name
  metrics:
  - name: success-rate
    interval: 1m
    count: 5
    successCondition: result[0] >= 0.99
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          sum(rate(http_requests_total{service="{{args.service-name}}",status=~"2.*"}[2m]))
          /
          sum(rate(http_requests_total{service="{{args.service-name}}"}[2m]))
```

**Flagger vs Argo Rollouts:**
- Flagger: lighter, relies on service mesh for traffic splitting, Flux-native
- Argo Rollouts: standalone, built-in traffic routing, richer step definitions, wider adoption

### Comprehensive AnalysisTemplate (Automated Canary Decisions)

Production-grade AnalysisTemplate with multiple Prometheus queries covering
error rate, latency percentiles, saturation, and business metrics:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: canary-analysis
spec:
  args:
  - name: service-name
  - name: namespace
    value: default
  - name: error-rate-threshold
    value: "0.01"
  - name: p99-latency-threshold
    value: "500"
  metrics:
  # Error rate: reject if >1% of requests fail
  - name: error-rate
    interval: 1m
    count: 5
    failureLimit: 2
    successCondition: result[0] <= {{args.error-rate-threshold}}
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          sum(rate(http_requests_total{
            service="{{args.service-name}}",
            namespace="{{args.namespace}}",
            status=~"5.*"
          }[2m]))
          /
          sum(rate(http_requests_total{
            service="{{args.service-name}}",
            namespace="{{args.namespace}}"
          }[2m]))
  # P99 latency: reject if >500ms
  - name: p99-latency
    interval: 1m
    count: 5
    failureLimit: 2
    successCondition: result[0] <= {{args.p99-latency-threshold}}
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          histogram_quantile(0.99,
            sum(rate(http_request_duration_seconds_bucket{
              service="{{args.service-name}}",
              namespace="{{args.namespace}}"
            }[2m])) by (le)
          ) * 1000
  # Saturation: reject if CPU >80%
  - name: cpu-usage
    interval: 1m
    count: 5
    successCondition: result[0] <= 80
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          max(rate(container_cpu_usage_seconds_total{
            pod=~"{{args.service-name}}-canary.*",
            namespace="{{args.namespace}}"
          }[2m])) * 100
  # Business metric: reject if conversion rate drops >10%
  - name: conversion-rate
    interval: 2m
    count: 3
    successCondition: result[0] >= result[1] * 0.9
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          sum(rate(purchases_total{
            service="{{args.service-name}}",
            namespace="{{args.namespace}}"
          }[5m]))
          /
          sum(rate(page_views_total{
            service="{{args.service-name}}",
            namespace="{{args.namespace}}"
          }[5m]))
```

**Kayenta (Netflix) alternative:** For Spinnaker-based pipelines, Kayenta
provides similar automated canary analysis with Atlas/Prometheus/Datadog backends.

## Step 5: Feature Flags

Decouple deploy from release. Ship code with flags OFF. Toggle ON at runtime.

### OpenFeature (CNCF Standard)
Vendor-agnostic API for feature flags. CNCF incubating project. Decouples flag evaluation from provider SDKs — swap LaunchDarkly → Unleash without changing application code.

```go
import (
    "github.com/open-feature/go-sdk/openfeature"
)

// Initialize once at startup — swap providers without changing code
openfeature.SetProvider(unleash.NewProvider(config))
// or: openfeature.SetProvider(launchdarkly.NewProvider(sdkKey))

client := openfeature.NewClient("myapp")

// Boolean flag
darkMode, _ := client.BooleanValue(ctx, "dark-mode", false, evalCtx)

// String flag with targeting
bannerText, _ := client.StringVar(ctx, "homepage-banner", "Welcome", evalCtx)

// Object flag
cfg, _ := client.ObjectValue(ctx, "ab-test-config", defaultCfg, evalCtx)

// Evaluation details (includes reason, variant, metadata)
details, _ := client.BooleanEvaluation(ctx, "new-checkout", false, evalCtx)
// details.Reason = "TARGETING_MATCH"
// details.Variant = "experiment-a"
```

```typescript
// Node.js — same API, different provider
import { OpenFeature } from '@openfeature/server-sdk';
import { LaunchDarklyProvider } from '@openfeature/launchdarkly-provider';

OpenFeature.setProvider(new LaunchDarklyProvider({ sdkKey: 'sdk-xxx' }));
const client = OpenFeature.getClient();
const showNewUI = await client.getBooleanValue('new-ui', false, { targetingKey: userId });
```

**Hook pattern** — cross-cutting concerns without changing flag logic:
```go
client.AddHooks(&MetricsHook{}, &AuditHook{})
// MetricsHook: record flag evaluation latency per flag key
// AuditHook: log every flag change for compliance
```

**Providers:** LaunchDarkly, Unleash, Flagsmith, Flipt, CloudBees, Split, AWS AppConfig, Azure App Config

### Unleash (Open-Source)
```bash
docker run -p 4242:4242 unleashorg/unleash-server
```

**Activation strategies:** gradualRollout, userIds, IPs, hostnames

### LaunchDarkly (SaaS)
- Server-side SDK evaluates locally (rules cached, streamed via SSE)
- Context-based targeting: user segments, percentage rollouts

## Step 6: Rollback Strategies

### Kubernetes
```bash
# View rollout history
kubectl rollout history deployment/myapp

# Rollback to previous
kubectl rollout undo deployment/myapp

# Rollback to specific revision
kubectl rollout undo deployment/myapp --to-revision=3

# Check rollout status
kubectl rollout status deployment/myapp --timeout=300s
```

### Automated Rollback Triggers
- Kubernetes `progressDeadlineSeconds`: auto-fail if stuck
- Argo CD: sync retry limits, `automated.selfHeal` with `prune`
- Spinnaker + Kayenta: canary score < threshold → auto rollback

### Database Rollback
- Use **expand-contract migrations**: add new columns (expand) → deploy app → drop old columns (contract)
- Migration scripts must be backward-compatible with old app version
- Tools: Flyway, Liquibase, golang-migrate, Alembic

## Step 7: Zero-Downtime Database Migrations

### Expand-Contract Pattern
1. **Expand:** Add new columns/tables (backward compatible)
2. **Migrate:** Deploy app that reads both old and new schema
3. **Contract:** Drop old columns/tables (after all services updated)

```sql
-- Step 1: Expand (no downtime)
ALTER TABLE users ADD COLUMN email_normalized VARCHAR(255);
UPDATE users SET email_normalized = LOWER(email);

-- Step 2: App reads both columns during transition

-- Step 3: Contract (after app fully migrated)
ALTER TABLE users DROP COLUMN email;
ALTER TABLE users RENAME COLUMN email_normalized TO email;
```

### Migration Tools
| Tool | Ecosystem |
|------|-----------|
| Flyway | Java, SQL |
| Alembic | Python |
| golang-migrate | Go |
| Prisma Migrate | JS/TS |
| Atlas | Go, SQL-first |

## Step 8: Database Migration Testing

Test migrations in CI before deploying to production. Catches schema errors,
data loss, and backward-compatibility issues early.

### CI Migration Test Pipeline

```yaml
# .github/workflows/migration-test.yaml
jobs:
  migration-test:
    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_DB: testdb
          POSTGRES_PASSWORD: test
        ports: ['5432:5432']
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    steps:
    - uses: actions/checkout@v4

    # 1. Apply current production schema (seed from dump or migrations)
    - name: Seed baseline schema
      run: migrate -path ./migrations -database $DB_URL up

    # 2. Load representative data sample
    - name: Seed test data
      run: psql $DB_URL < ./test-fixtures/seed-data.sql

    # 3. Apply new migration
    - name: Run pending migrations
      run: migrate -path ./migrations -database $DB_URL up

    # 4. Verify backward compatibility: old app version still works
    - name: Run old app tests against new schema
      run: APP_VERSION=previous go test ./... -run TestDatabase

    # 5. Run new app tests
    - name: Run new app tests against new schema
      run: go test ./... -run TestDatabase

    # 6. Verify rollback works
    - name: Rollback migration
      run: migrate -path ./migrations -database $DB_URL down 1

    # 7. Verify data integrity after rollback
    - name: Check row counts match baseline
      run: psql $DB_URL -c "SELECT COUNT(*) FROM users" | grep -q "$EXPECTED_COUNT"
```

### Schema Diffing

```bash
# Atlas: compare live DB vs migration directory
atlas schema diff --from "postgres://localhost/db" --to "file://migrations" \
  --dev-url "docker://postgres/16/dev"

# Detect destructive changes (DROP COLUMN, TRUNCATE, type narrowing)
atlas schema inspect --dsn "postgres://localhost/db" | atlas schema diff \
  --to "file://migrations" --destructive
```

### Migration Testing Checklist
1. **Baseline test:** apply migrations to empty DB, verify schema matches expected
2. **Forward test:** apply new migration to production-like schema, verify no errors
3. **Backward test:** rollback migration, verify old schema restored
4. **Data integrity test:** row counts, constraint checks, foreign key validity
5. **Performance test:** `EXPLAIN ANALYZE` queries against new schema (index coverage)
6. **Compatibility test:** old app version still works with new schema (expand phase)

## Step 9: Multi-Cluster Progressive Delivery

Deploy across multiple clusters with Argo CD ApplicationSets + Argo Rollouts.
Environments: dev → staging → production-us → production-eu.

### ApplicationSet with Progressive Rollout

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: myapp
  namespace: argocd
spec:
  generators:
  - list:
      elements:
      - cluster: production-us
        url: https://kubernetes.us-east-1.example.com
        region: us-east-1
      - cluster: production-eu
        url: https://kubernetes.eu-west-1.example.com
        region: eu-west-1
  strategy:
    type: RollingSync
    rollingSync:
      steps:
      - matchExpressions:
        - key: region
          operator: In
          values: ["us-east-1"]
        maxUpdate: 100%
      - matchExpressions:
        - key: region
          operator: In
          values: ["eu-west-1"]
        maxUpdate: 50%    # 1 of 2 EU clusters at a time
        delaySeconds: 300 # Wait 5 min between EU clusters
  template:
    metadata:
      name: '{{cluster}}-myapp'
    spec:
      project: default
      source:
        repoURL: https://github.com/org/k8s-manifests
        targetRevision: HEAD
        path: overlays/{{cluster}}
      destination:
        server: '{{url}}'
        namespace: myapp
```

### RolloutPerCluster Pattern

Each cluster runs its own Rollout with independent AnalysisTemplate:

```yaml
# overlays/production-us/rollout.yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: myapp
spec:
  strategy:
    canary:
      steps:
      - setWeight: 10
      - pause: {duration: 10m}
      - analysis:
          templates:
          - templateName: canary-analysis
          args:
          - name: service-name
            value: myapp
      - setWeight: 50
      - pause: {duration: 15m}
      - analysis:
          templates:
          - templateName: canary-analysis
      - setWeight: 100
```

**Key patterns:**
- Argo CD Progressive Sync: rollout to us-east first, wait, then eu-west
- Per-cluster AnalysisTemplate with region-specific Prometheus endpoints
- Cross-cluster traffic via DNS (Route53 health checks) — if canary fails in us-east, don't proceed to eu-west
- Staggered rollouts reduce blast radius (failure in us-east doesn't affect eu-west)

## Step 10: FinOps — Cost-Aware Deployments

Track, optimize, and allocate deployment costs. CNCF FinOps tooling.

### OpenCost (CNCF Incubating)

Real-time Kubernetes cost monitoring. Provider-agnostic (AWS/GCP/Azure/on-prem).

```bash
# Install OpenCost
helm install opencost opencost/opencost \
  --namespace opencost --create-namespace \
  --set opencost.prometheus.internal.enabled=false \
  --set opencost.prometheus.external.url=http://prometheus.monitoring:9090
```

```yaml
# Query deployment cost via OpenCost API
# GET /allocation?window=7d&aggregate=controller
# Response per-workload:
# {
#   "myapp-canary": {
#     "cpuCost": 12.50, "ramCost": 8.30, "totalCost": 20.80,
#     "cpuEfficiency": 0.45, "ramEfficiency": 0.62
#   }
# }
```

**Cost allocation labels:** add `team`, `app`, `env` labels to workloads for
chargeback/showback reporting in OpenCost dashboards.

### Karpenter — Just-in-Time Node Provisioning

Replaces Cluster Autoscaler. Provisions right-sized nodes in <60s.

```yaml
apiVersion: karpenter.sh/v1beta1
kind: NodePool
metadata:
  name: burst-deploy
spec:
  template:
    spec:
      requirements:
      - key: karpenter.sh/capacity-type
        operator: In
        values: ["spot", "on-demand"]
      - key: node.kubernetes.io/instance-type
        operator: In
        values: ["m5.large", "m5.xlarge", "m6i.large"]
  limits:
    cpu: "1000"
    memory: 2000Gi
  disruption:
    consolidationPolicy: WhenUnderutilized
    expireAfter: 720h  # 30 days max node lifetime
```

**Cost optimization patterns:**
- Karpenter provisions Spot instances for canary/burst workloads (60-90% cheaper)
- Consolidation: automatically bin-packs pods onto fewer nodes after scale-down
- Node expiry: forces periodic node rotation (security + cost)
- Combine with PodDisruptionBudgets for safe consolidation

### FOCUS Spec (FinOps Open Cost and Usage Specification)

Standardized cloud cost data format. Ingest billing data from AWS CUR, GCP Billing Export, Azure Cost Management into unified schema.

```sql
-- FOCUS-aligned query: cost per deployment environment
SELECT
  ChargePeriodStart,
  ServiceName,
  Tags['env'] AS environment,
  SUM(EffectiveCost) AS total_cost,
  SUM(ConsumedQuantity) AS resource_usage
FROM focus_billing_table
WHERE ChargePeriodStart >= '2025-01-01'
GROUP BY ChargePeriodStart, ServiceName, environment
ORDER BY total_cost DESC;
```

**Integration:** export FOCUS data to OpenCost for Kubernetes + cloud cost correlation.

### Cost-Aware Deployment Patterns

1. **Canary cost monitoring:** alert if canary deployment cost exceeds 2x baseline
2. **Right-size during rollout:** use VPA recommendations before scaling canary
3. **Spot for non-prod:** dev/staging on Spot, prod on On-Demand
4. **Schedule-based scaling:** Karpenter NodePool with time-of-day limits
5. **Cost regression tests:** CI job checks `opencost /allocation` API for anomalous spend

```yaml
# GitHub Actions: cost regression check
- name: Check deployment cost
  run: |
    COST=$(curl -s "http://opencost.opencost:9090/allocation?window=1h&aggregate=deployment" \
      | jq '.data["myapp-canary"].totalCost')
    if (( $(echo "$COST > 50" | bc -l) )); then
      echo "::error::Canary cost $$COST exceeds $50/hour threshold"
      exit 1
    fi
```

## Sources

- Canary deployments: https://docs.flagger.app/usage/deployment-strategies
- Blue-green: https://martinfowler.com/bliki/BlueGreenDeployment.html
- Argo Rollouts: https://argoproj.github.io/rollouts/
- Flagger: https://docs.flagger.app/
- Feature flags: https://openfeature.dev/
- Unleash: https://docs.getunleash.io/
- LaunchDarkly: https://docs.launchdarkly.com/home
- Kubernetes deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- Gateway API: https://gateway-api.sigs.k8s.io/
- Gateway API traffic splitting: https://gateway-api.sigs.k8s.io/guides/traffic-splitting/
- OpenFeature specification: https://openfeature.dev/specification
- OpenCost: https://www.opencost.io/
- Karpenter: https://karpenter.sh/docs/
- FOCUS spec: https://focus.finops.org/
- Argo CD ApplicationSets: https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/
- Atlas schema diffing: https://atlasgo.io/concepts/declarative-vs-versioned

## Pitfalls

1. **Don't skip expand-contract for DB migrations** — old and new app versions must coexist
2. **Don't deploy without rollback plan** — always have a way back
3. **Don't skip canary analysis** — manual eyeballing doesn't catch latency regressions
4. **Don't use feature flags as permanent branches** — clean up flags after full rollout
5. **Don't skip smoke tests after deploy** — verify critical paths work
6. **Don't mix blue-green with 2x DB cost** — use expand-contract pattern
7. **Don't skip health checks** — liveness + readiness probes are mandatory
8. **Don't deploy on Fridays** — unless you have 24/7 on-call
9. **Don't use Nginx Ingress annotations for new canary setups** — Gateway API is portable and first-class in Flagger/Argo Rollouts
10. **Don't skip migration testing in CI** — schema drift in production causes outages
11. **Don't ignore canary cost** — a misconfigured canary can rack up cloud spend fast
12. **Don't skip OpenFeature hooks** — audit logging is required for compliance in regulated environments
