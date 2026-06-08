---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling, progressive delivery (Flagger/Argo Rollouts), feature flags (LaunchDarkly/Unleash/OpenFeature), rollback, database migrations, zero-downtime patterns. v3: Gateway API traffic splitting, OpenFeature CNCF standard, FinOps (OpenCost/Karpenter/FOCUS), AnalysisTemplate, multi-cluster progressive delivery. v3.1: Serverless (Lambda/Cloud Run/Container Apps), edge deployment (Cloudflare Workers/Deno Deploy), cold start optimization, serverless observability. v4: Production hardening (health probes, graceful shutdown, PDB), multi-region patterns (active-active/passive, follow-the-sun), disaster recovery (RPO/RTO, failover automation), cost optimization (right-sizing, spot/reserved), deployment verification (smoke tests, synthetic monitoring, canary analysis). v4.3: Deployment failure case studies (Knight Capital, AWS S3, Cloudflare, GitLab, Facebook BGP), successful deployment patterns (Netflix, Google, Amazon, Etsy), database migration war stories (gh-ost, expand-contract, Vitess), feature flag case studies (Facebook Gate, LaunchDarkly, Microsoft flight rings)."
version: 4.3.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-green, rolling, feature-flags, progressive-delivery, flagger, argo-rollouts, kubernetes, zero-downtime, gateway-api, openfeature, finops, opencost, karpenter, analysis-template, multi-cluster, database-migration, serverless, lambda, cloud-run, container-apps, edge-deployment, cloudflare-workers, cold-start, serverless-observability, production-hardening, health-checks, graceful-shutdown, pdb, multi-region, disaster-recovery, rpo-rto, cost-optimization, spot-instances, deployment-verification, smoke-tests, synthetic-monitoring, canary-analysis, failure-case-studies, deployment-patterns, database-migration-war-stories, feature-flag-case-studies]
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
- Configures health checks, probes, or graceful shutdown
- Sets up multi-region or disaster recovery deployments
- Optimizes deployment costs (right-sizing, spot, reserved)
- Implements deployment verification (smoke tests, synthetic monitoring)

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

## Step 11: Serverless Deployment — AWS Lambda

Packaged functions deployed per-request. No servers to manage. Pay per invocation + duration.

### ARM64 (Graviton2)

34% better price-performance vs x86_64. Set architecture in function config:

```yaml
# SAM template
Globals:
  Function:
    Architectures: [arm64]
    Runtime: python3.12
    MemorySize: 256
    Timeout: 10
```

```bash
# CDK
new lambda.Function(this, 'Fn', {
  architecture: lambda.Architecture.ARM_64,
  runtime: lambda.Runtime.PYTHON_3_12,
  handler: 'index.handler',
  code: lambda.Code.fromAsset('lambda/'),
});
```

**Caveat:** all native dependencies must be compiled for arm64. Use Docker-based Lambda build with `--platform linux/arm64`.

### SnapStart (Java)

Snapshot initialized execution environment. Subsequent cold starts restore from snapshot instead of re-initializing JVM. Reduces cold start from ~3-6s to ~200ms.

```yaml
# SAM
MyFunction:
  Type: AWS::Serverless::Function
  Properties:
    Runtime: java21
    SnapStart:
      ApplyOn: PublishedVersions
```

```bash
# Publish version after deploy — SnapStart only applies to published versions
aws lambda publish-version --function-name MyFunction --description "snapstart"
```

**Constraints:**
- Only Java (Corretto 11+) supported
- No file descriptors, sockets, or encryption contexts can be snapshotted — use `beforeCheckpoint()` / `afterRestore()` hooks
- Each published version stores a snapshot (cost per stored snapshot)
- Not compatible with Provisioned Concurrency

### Provisioned Concurrency

Pre-initializes N execution environments. Eliminates cold starts for those N invocations. Costs money even when idle.

```yaml
# SAM with alias
MyFunction:
  Type: AWS::Serverless::Function
  Properties:
    AutoPublishAlias: live
    ProvisionedConcurrencyConfig:
      ProvisionedConcurrentExecutions: 10
```

```bash
# CDK
const alias = new lambda.Alias(this, 'Live', {
  aliasName: 'live',
  version: fn.currentVersion,
  provisionedConcurrentExecutions: 10,
});
```

**Cost model:** pay for provisioned concurrency * memory * time, even at zero traffic. Reserve for latency-critical paths only.

### Lambda Layers

Share code/libraries across functions without bundling in each deployment package.

```bash
# Create layer from requirements
mkdir -p python/lib/python3.12/site-packages
pip install requests boto3 -t python/lib/python3.12/site-packages
zip -r shared-deps.zip python/
aws lambda publish-layer-version \
  --layer-name shared-deps \
  --zip-file fileb://shared-deps.zip \
  --compatible-runtimes python3.12 \
  --compatible-architectures arm64 x86_64
```

**Limits:** 5 layers per function, 250 MB unzipped total (function + all layers). 50 MB zipped per layer.

### Powertools for AWS Lambda

Best-practice utilities for structured logging, tracing, metrics, idempotency, batch processing.

```python
from aws_lambda_powertools import Logger, Tracer, Metrics
from aws_lambda_powertools.event_handler import APIGatewayRestResolver
from aws_lambda_powertools.utilities.typing import LambdaContext

logger = Logger()
tracer = Tracer()
metrics = Metrics(namespace="MyApp")
app = APIGatewayRestResolver()

@app.get("/items/<item_id>")
@tracer.capture_method
def get_item(item_id: str):
    logger.info("Fetching item", extra={"item_id": item_id})
    metrics.add_metric(name="GetItem", unit="Count", value=1)
    return {"id": item_id, "name": "widget"}

@logger.inject_lambda_context
@tracer.capture_lambda_handler
@metrics.log_metrics
def handler(event: dict, context: LambdaContext):
    return app.resolve(event, context)
```

**Key features:**
- `Logger`: structured JSON logs with correlation IDs, Lambda context auto-injected
- `Tracer`: X-Ray subsegment creation, captures request/response
- `Metrics`: CloudWatch EMF (Embedded Metric Format) — no custom metric filters needed
- `Idempotency`: DynamoDB-backed idempotent processing for SQS/API GW events
- `BatchProcessor`: partial failure handling for SQS, Kinesis, DynamoDB Streams

## Step 12: Serverless Deployment — Google Cloud Run

Container-based serverless. Run any container with per-request billing. Scales to zero.

### Concurrency

Each container instance handles multiple concurrent requests (default: 80, max: 1000). Lower concurrency = more instances = higher cost but more isolation.

```bash
gcloud run deploy myapp \
  --image gcr.io/myproject/myapp:latest \
  --concurrency 80 \
  --cpu 2 \
  --memory 2Gi \
  --max-instances 100
```

```yaml
# service.yaml
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: myapp
spec:
  template:
    metadata:
      annotations:
        autoscaling.knative.dev/maxScale: "100"
    spec:
      containerConcurrency: 80
      containers:
      - image: gcr.io/myproject/myapp:latest
        resources:
          limits:
            cpu: "2"
            memory: "2Gi"
```

**Guidelines:**
- CPU-bound workloads: lower concurrency (1-8), higher CPU
- I/O-bound (typical web apps): higher concurrency (80-250)
- WebSockets: concurrency = 1 per connection (each WS is long-lived)

### Min Instances (Avoid Cold Starts)

```bash
gcloud run deploy myapp --min-instances 2
```

Keeps N instances warm. Costs money at idle. Use for latency-sensitive production services.

### Traffic Splitting (Revisions)

Deploy new revision without shifting traffic, then split:

```bash
gcloud run deploy myapp --image gcr.io/myproject/myapp:v2 --no-traffic
# Test revision at .preview subdomain
gcloud run services update-traffic myapp --to-revisions=myapp-00042=10,myapp-00041=90
# Gradually increase
gcloud run services update-traffic myapp --to-revisions=myapp-00042=50,myapp-00041=50
gcloud run services update-traffic myapp --to-latest
```

**Canary with tag:** `--tag=canary` gives revision unique URL (`canary---myapp-xxx.run.app`) for testing before shifting traffic.

### Startup CPU Boost

Extra CPU during container startup. Reduces boot time for heavy init (JVM, Node.js bundling, Python import graph).

```bash
gcloud run deploy myapp --cpu-boost
```

```yaml
# service.yaml
metadata:
  annotations:
    run.googleapis.com/startup-cpu-boost: "true"
```

Doubles CPU during startup (e.g., 1 CPU → 2 CPU) then drops to limit. No extra cost — only duration is shorter.

## Step 13: Azure Container Apps

Serverless container platform on Azure. Built on K8s but abstracts nodes away.

### KEDA Autoscaling

Scale on custom metrics beyond HTTP: queue length, event hub messages, CPU, custom Prometheus queries.

```bash
az containerapp create \
  --name myapp \
  --resource-group mygroup \
  --image myregistry.azurecr.io/myapp:v1 \
  --min-replicas 0 \
  --max-replicas 50 \
  --scale-rule-name queue-scaler \
  --scale-rule-type azure-queue \
  --scale-rule-metadata queueName=myqueue connection=storage-connection \
  --scale-rule-auth connection=storage-connection
```

```yaml
# Bicep / ARM: multiple KEDA scalers
scale:
  minReplicas: 0
  maxReplicas: 50
  rules:
  - name: queue-scaler
    custom:
      type: azure-queue
      metadata:
        queueName: orders
        queueLength: "5"
      auth:
      - secretRef: storage-connection
        triggerParameter: connection
  - name: cpu-scaler
    custom:
      type: cpu
      metadata:
        type: Utilization
        value: "70"
```

**KEDA scaler types:** azure-servicebus, rabbitmq, kafka, cron, redis, postgresql, external (Prometheus), etc.

### Dapr Sidecar

Distributed Application Runtime as sidecar. Service invocation, pub/sub, state management, secrets — all without SDK in app code.

```yaml
az containerapp create \
  --name myapp \
  --dapr-enabled \
  --dapr-app-id myapp \
  --dapr-app-port 8080 \
  --dapr-app-protocol http
```

```yaml
# Bicep: Dapr with pub/sub
dapr:
  enabled: true
  appId: order-processor
  appPort: 8080
  appProtocol: http
  components:
  - name: pubsub
    type: pubsub.azure.servicebus
    version: v1
    metadata:
    - name: connectionString
      secretRef: servicebus-connection
```

**Dapr components in Container Apps:** pubsub, bindings, state stores, secret stores — configured at environment level, shared across apps.

### Revisions

Immutable snapshots. New revision created on any config change (image, env var, scale rule, Dapr config).

```bash
# Deploy new revision (blue/green — old still serves traffic)
az containerapp revision copy \
  --name myapp \
  --resource-group mygroup \
  --image myregistry.azurecr.io/myapp:v2

# Traffic splitting
az containerapp ingress traffic set \
  --name myapp \
  --resource-group mygroup \
  --revision-weight myapp--v2=20 myapp--v1=80
```

**Revision modes:**
- `Single`: latest active revision gets 100% traffic (default, simplest)
- `Multiple`: manual traffic splitting across revisions

## Step 14: Edge Deployment

Run code at CDN edge locations. Sub-50ms latency globally. Limited runtime (no filesystem, no native binaries).

### Cloudflare Workers

V8 isolate-based. No containers. Each request gets a lightweight isolate (not a VM). ~0ms cold start.

```typescript
// wrangler.toml
// name = "myapp"
// main = "src/index.ts"
// compatibility_date = "2024-01-01"

export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    const url = new URL(request.url);
    return new Response(`Hello from ${url.pathname}`);
  },
};
```

```typescript
// Durable Objects — stateful edge compute (single-instance coordination)
export class Counter {
  state: DurableObjectState;
  value: number = 0;

  constructor(state: DurableObjectState, env: Env) {
    this.state = state;
  }

  async fetch(request: Request): Promise<Response> {
    this.value++;
    await this.state.storage.put("count", this.value);
    return new Response(`Count: ${this.value}`);
  }
}

// Worker routes to Durable Object
export default {
  async fetch(request: Request, env: Env): Promise<Response> {
    const id = env.COUNTER.idFromName("global");
    const stub = env.COUNTER.get(id);
    return stub.fetch(request);
  },
};
```

**Cloudflare platform primitives:**

| Primitive | Type | Use Case |
|-----------|------|----------|
| KV | Key-value (eventual consistency) | Config, feature flags, session cache |
| R2 | Object storage (S3-compatible) | Images, blobs, backups (zero egress) |
| D1 | SQLite at edge | Relational data, full-text search |
| Durable Objects | Stateful singletons | Counters, rate limiting, coordination |
| Queues | Message queue | Async processing, decoupling |
| Hyperdrive | Connection pooler | Accelerate DB queries from edge |
| Vectorize | Vector database | Semantic search, RAG |

```typescript
// KV — read config at edge
const config = await env.MY_KV.get("feature-flags", { type: "json" });

// R2 — serve images
const object = await env.MY_BUCKET.get("images/photo.webp");
return new Response(object.body, { headers: { "Content-Type": "image/webp" } });

// D1 — query SQLite
const { results } = await env.DB.prepare(
  "SELECT * FROM users WHERE id = ?"
).bind(userId).all();
```

### Deno Deploy

V8 isolate-based. Native TypeScript. Global distribution at 35+ edge locations.

```typescript
// main.ts — deployed via `deployctl deploy --project=myapp main.ts`
Deno.serve(async (req: Request) => {
  const url = new URL(req.url);
  if (url.pathname === "/api/hello") {
    return new Response(JSON.stringify({ message: "hello" }), {
      headers: { "content-type": "application/json" },
    });
  }
  return new Response("Not Found", { status: 404 });
});
```

**Deno Deploy KV (DenoKV):**
```typescript
const kv = await Deno.openKv();
await kv.set(["users", "alice"], { name: "Alice", role: "admin" });
const entry = await kv.get(["users", "alice"]);
```

**Limits:** 50ms CPU time per request (free), 500ms (Pro). No native modules. No filesystem. Use DenoKV or external DB for persistence.

## Step 15: Cold Start Optimization

Cold start = time from request arrival to first code execution when no warm instance exists. Varies by runtime, package size, and initialization work.

### Runtime Choice Impact

| Runtime | Typical Cold Start | Notes |
|---------|-------------------|-------|
| Node.js | 150-300ms | Fast, but large `node_modules` hurts |
| Python | 200-500ms | Import-heavy frameworks (Django, Flask) slow |
| Go | 50-100ms | Compiled binary, near-instant init |
| Java (no SnapStart) | 3-6s | JVM class loading, Spring context |
| Java (SnapStart) | 200-400ms | Snapshot restore, only on Lambda |
| Rust | 10-30ms | Fastest cold start, smallest binary |
| .NET (NativeAOT) | 100-200ms | Ahead-of-time compilation |

### Package Size Optimization

```python
# Python: use slim base, exclude dev dependencies
# requirements.txt — pin only what you need
boto3==1.34.0       # NOT aws-sdk (use boto3, not full AWS SDK)
requests==2.31.0
# NOT Django (200MB) — use lightweight (FastAPI, Lambda Powertools)

# .gitignore-style for Lambda packaging
# SAM: exclude files from deployment
# template.yaml
Resources:
  MyFunction:
    Metadata:
      Sam:
        Exclude:
          - "*.pyc"
          - "__pycache__"
          - "tests/"
          - ".venv/"
```

```javascript
// Node.js: tree-shake, exclude devDependencies
// esbuild bundler config
{
  "bundle": true,
  "minify": true,
  "platform": "node",
  "target": "node20",
  "external": ["@aws-sdk/*"]  // provided by Lambda runtime
}
```

**Size targets:** <10 MB zipped for Python, <5 MB for Node.js (bundled), <25 MB for Java.

### Lazy Initialization

Move heavy work out of module scope. Init on first request, not during cold start.

```python
import os
import boto3

# BAD: initialized on every cold start even if not used in this invocation
db_client = boto3.client('dynamodb')
s3_client = boto3.client('s3')
ssm_client = boto3.client('ssm')
config = json.loads(ssm_client.get_parameter(Name='/myapp/config')['Parameter']['Value'])

# GOOD: lazy init with module-level caching
_db_client = None
def get_db():
    global _db_client
    if _db_client is None:
        _db_client = boto3.client('dynamodb')
    return _db_client

def handler(event, context):
    # Only init what this invocation needs
    table = get_db().get_item(...)
```

```typescript
// Node.js: top-level await for async init, only if needed
let db: DynamoDBClient;

export async function getDb(): Promise<DynamoDBClient> {
  if (!db) {
    db = new DynamoDBClient({});
    // Optional: pre-warm connection
    await db.send(new ListTablesCommand({}));
  }
  return db;
}
```

### Keep-Alive and Connection Reuse

Reuse HTTP connections and DB connections across invocations (warm container reuse).

```python
# Python: use module-level session (persists across warm invocations)
import requests
session = requests.Session()  # NOT requests.get() per invocation
# Connection pooling built-in

# boto3: reuse client objects (they use connection pools)
import boto3
dynamodb = boto3.resource('dynamodb')  # module-level, reused in warm containers
table = dynamodb.Table('my-table')

def handler(event, context):
    table.get_item(Key={'pk': event['id']})  # reuses connection
```

```typescript
// Node.js: reuse fetch/HTTP agent
import { Agent } from 'undici';

const agent = new Agent({
  keepAliveTimeout: 60_000,
  connections: 10,
});

export const handler = async (event) => {
  const res = await fetch('https://api.example.com/data', {
    dispatcher: agent,  // reuse TCP connections
  });
};
```

**Critical:** DB connections (RDS, Postgres) should use RDS Proxy. Direct connections from Lambda exhaust connection pool during scale-up.

### Cold Start Optimization Checklist

1. Prefer Go/Rust/Node.js over Java/Python for latency-critical paths
2. Use ARM64 architecture (Lambda) — faster startup, cheaper
3. Bundle/minify — remove unused dependencies, tree-shake
4. Lazy-init heavy clients (DB, S3, SSM) — only init what request needs
5. Reuse connections (HTTP keep-alive, DB connection pooling)
6. Set Provisioned Concurrency for p99 latency requirements
7. Use Lambda Layers for shared deps (avoids duplicate bundling)
8. Enable SnapStart for Java workloads
9. Enable Startup CPU Boost on Cloud Run
10. Set min-instances > 0 on Cloud Run for prod services

## Step 16: Serverless Observability

Serverless challenges: no persistent hosts, ephemeral logs, distributed across many tiny invocations, no SSH.

### Challenge → Solution Map

| Challenge | Solution |
|-----------|----------|
| No persistent log files | Structured JSON logs → CloudWatch Logs / GCP Cloud Logging |
| Distributed traces across services | X-Ray / Cloud Trace / OTEL |
| No host metrics (CPU, memory per host) | Lambda Insights / Powertools Metrics (EMF) |
| Log correlation across invocations | Powertools Logger with `correlation_id` |
| Cold start visibility | X-Ray subsegments, Powertools `cold_start` annotation |
| Cost per log ingestion | CloudWatch Logs Insights queries, log sampling |

### AWS X-Ray Tracing

```yaml
# SAM: enable active tracing
MyFunction:
  Type: AWS::Serverless::Function
  Properties:
    Tracing: Active
```

```python
from aws_xray_sdk.core import xray_recorder
from aws_xray_sdk.core import patch_all

patch_all()  # auto-patch boto3, requests, etc.

@xray_recorder.capture('process_order')
def process_order(order):
    subsegment = xray_recorder.current_subsegment()
    subsegment.put_annotation('order_id', order['id'])
    subsegment.put_metadata('order', order)
    # ...
```

**X-Ray provides:** service map, trace waterfall, error/latency analysis, annotation-based filtering.

### Powertools Logger

```python
from aws_lambda_powertools import Logger
from aws_lambda_powertools.logging import correlation_paths

logger = Logger(service="order-service")

@logger.inject_lambda_context(correlation_id_path=correlation_paths.API_GATEWAY_REST)
def handler(event, context):
    logger.info("Processing order", extra={
        "order_id": event["pathParameters"]["id"],
        "http_method": event["httpMethod"],
    })
    # Output: {"level":"INFO","message":"Processing order","order_id":"123",
    #          "cold_start":true,"function_name":"OrderFn","request_id":"abc",
    #          "service":"order-service","timestamp":"2024-01-15T10:30:00Z"}
```

**Auto-included fields:** `cold_start`, `function_name`, `function_memory_size`, `function_arn`, `function_request_id`, `xray_trace_id`, `service`, `timestamp`.

### OpenTelemetry Lambda Layer

```bash
# Install OTEL Lambda layer (Python example)
# ARN: arn:aws:lambda:{region}:901920570463:layer:aws-otel-python-amd64-ver-1-x:1
# For ARM: aws-otel-python-arm64-ver-1-x

# SAM
MyFunction:
  Properties:
    Layers:
      - !Sub arn:aws:lambda:${AWS::Region}:901920570463:layer:aws-otel-python-arm64-ver-1-x:1
    Environment:
      Variables:
        AWS_LAMBDA_EXEC_WRAPPER: /opt/otel-instrument
        OTEL_SERVICE_NAME: order-service
        OTEL_EXPORTER_OTLP_ENDPOINT: "http://otel-collector:4317"
        OTEL_TRACES_SAMPLER: parentbased_traceidratio
        OTEL_TRACES_SAMPLER_ARG: "0.1"
```

**OTEL Lambda layer provides:** auto-instrumentation for AWS SDK, HTTP clients, DB drivers. Exports traces to any OTLP-compatible backend (Jaeger, Grafana Tempo, Honeycomb, Datadog).

### CloudWatch Embedded Metric Format (EMF)

Publish custom metrics as structured logs. No `PutMetricData` API call — metrics appear in CloudWatch automatically.

```python
from aws_lambda_powertools.metrics import Metrics, MetricUnit

metrics = Metrics(namespace="MyApp", service="order-service")

@metrics.log_metrics(capture_cold_start_metric=True)
def handler(event, context):
    metrics.add_metric(name="OrderCreated", unit=MetricUnit.Count, value=1)
    metrics.add_metric(name="OrderValue", unit=MetricUnit.None_, value=49.99)
    metrics.add_dimension(name="environment", value="production")
    metrics.add_dimension(name="region", value="us-east-1")
    # EMF JSON line emitted automatically on return
```

```json
// EMF log line (auto-parsed by CloudWatch):
{
  "_aws": {
    "Timestamp": 1705312200000,
    "CloudWatchMetrics": [{
      "Namespace": "MyApp",
      "Dimensions": [["environment", "region"]],
      "Metrics": [
        {"Name": "OrderCreated", "Unit": "Count"},
        {"Name": "OrderValue", "Unit": "None"}
      ]
    }]
  },
  "environment": "production",
  "region": "us-east-1",
  "OrderCreated": 1,
  "OrderValue": 49.99
}
```

**Benefits over `PutMetricData`:** no API cost, no rate limiting, batched with logs, metric dimensions from log context.

### Serverless Observability Stack

```
Request → Lambda (Powertools Logger + OTEL layer)
  → Structured JSON logs → CloudWatch Logs Insights (query)
  → X-Ray traces → X-Ray Console / Grafana Tempo
  → EMF metrics → CloudWatch Metrics → Alarms / Dashboards
  → Lambda Insights (enhanced metrics: init duration, memory max, cold start)
```

**Recommended for production:**
- Powertools Logger for structured logging + correlation
- OTEL layer OR X-Ray SDK for distributed tracing (not both — they conflict)
- EMF via Powertools Metrics for custom metrics
- Lambda Insights for infrastructure metrics (enable in function config)
- CloudWatch Logs Insights for ad-hoc queries across invocations

## Step 17: Production Hardening Checklist

Production readiness gates. Every service must pass before go-live.

### Health Check Probes (Kubernetes)

Three probe types. All three mandatory for production.

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: myapp
        ports:
        - containerPort: 8080
        # Startup probe: slow-starting apps get time to initialize
        # Kubelet uses this FIRST. Only after it succeeds do liveness/readiness run.
        startupProbe:
          httpGet:
            path: /healthz/startup
            port: 8080
          initialDelaySeconds: 0
          periodSeconds: 5
          failureThreshold: 30    # 30 * 5s = 150s max startup time
          timeoutSeconds: 3
        # Liveness probe: is the process alive? Restart if failing.
        livenessProbe:
          httpGet:
            path: /healthz/live
            port: 8080
          periodSeconds: 10
          failureThreshold: 3     # 30s of failure → restart
          timeoutSeconds: 5
        # Readiness probe: can it serve traffic? Remove from Service if not.
        readinessProbe:
          httpGet:
            path: /healthz/ready
            port: 8080
          periodSeconds: 5
          failureThreshold: 3     # 15s of failure → removed from endpoints
          timeoutSeconds: 3
          successThreshold: 1
```

**Probe endpoint implementation:**

```python
# FastAPI health check endpoints
from fastapi import FastAPI, Response, status

app = FastAPI()
_is_ready = False
_is_started = False

@app.get("/healthz/startup")
async def startup():
    """Called once by kubelet. Returns 200 when init complete."""
    if _is_started:
        return {"status": "started"}
    return Response(status_code=status.HTTP_503_SERVICE_UNAVAILABLE)

@app.get("/healthz/live")
async def liveness():
    """Check process is alive. Keep cheap — no external deps.
    Only fail if the process is truly stuck (deadlock, thread exhaustion)."""
    return {"status": "alive"}

@app.get("/healthz/ready")
async def readiness():
    """Check can serve traffic. OK to check DB/cache connectivity here.
    Failure removes pod from Service endpoints (no restart)."""
    checks = {}
    healthy = True
    try:
        await db.execute("SELECT 1")
        checks["database"] = "ok"
    except Exception:
        checks["database"] = "fail"
        healthy = False
    if healthy:
        return {"status": "ready", "checks": checks}
    return Response(status_code=status.HTTP_503_SERVICE_UNAVAILABLE)
```

**Probe design rules:**
- Startup probe: block on init (migrations, cache warmup, model load)
- Liveness probe: NO external deps (DB down ≠ restart app). Only detect hangs.
- Readiness probe: check external deps (DB, cache, downstream). Failure = remove from LB, not restart.
- `timeoutSeconds` < `periodSeconds`. Use `initialDelaySeconds` only if no startup probe.

### Graceful Shutdown

Handle SIGTERM. Drain connections, finish in-flight requests, close resources.

```yaml
spec:
  template:
    spec:
      terminationGracePeriodSeconds: 60   # default 30s; increase for long-lived requests
      containers:
      - name: myapp
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 5"]
              # 5s delay: removes pod from endpoints BEFORE app gets SIGTERM
              # Prevents new connections during shutdown
```

```go
// Go: graceful shutdown
srv := &http.Server{Addr: ":8080", Handler: mux}

go func() {
    sigChan := make(chan os.Signal, 1)
    signal.Notify(sigChan, syscall.SIGTERM, syscall.SIGINT)
    <-sigChan

    // Stop accepting new requests
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()
    if err := srv.Shutdown(ctx); err != nil {
        log.Printf("shutdown error: %v", err)
    }
    // Close DB connections, flush buffers, etc.
    db.Close()
    metrics.Flush()
}()

srv.ListenAndServe()
```

```python
# Python/FastAPI: graceful shutdown
import signal, asyncio

@app.on_event("shutdown")
async def shutdown():
    await engine.dispose()  # close DB pool
    # flush any buffered logs/metrics

# uvicorn handles SIGTERM → triggers shutdown event → drains connections
```

**Shutdown sequence:**
1. Pod marked for termination
2. Endpoint controller removes pod from Service endpoints
3. `preStop` hook runs (sleep 5s to let endpoints propagate)
4. SIGTERM sent to main process
5. App stops accepting new connections, drains in-flight
6. `terminationGracePeriodSeconds` expires → SIGKILL

### Resource Limits and Requests

```yaml
resources:
  requests:
    cpu: "250m"       # guaranteed: 0.25 CPU
    memory: "256Mi"   # guaranteed: 256 MB
  limits:
    cpu: "1000m"      # burst up to 1 CPU (throttled beyond)
    memory: "512Mi"   # OOMKilled beyond 512 MB
```

**Rules:**
- Always set requests. Scheduler needs them for bin-packing.
- Memory limits: set to 1.5-2x request. Memory not compressible → OOMKill.
- CPU limits: consider removing for latency-sensitive services. Throttling causes jitter. Use requests only + namespace quota.
- Use VPA recommender to right-size from historical data.
- Use `LimitRange` for namespace defaults.

```yaml
# Namespace defaults
apiVersion: v1
kind: LimitRange
metadata:
  name: default-limits
spec:
  limits:
  - default:
      cpu: "500m"
      memory: "512Mi"
    defaultRequest:
      cpu: "100m"
      memory: "128Mi"
    type: Container
```

### Pod Disruption Budget (PDB)

Protects voluntary disruptions (node drain, cluster upgrade, autoscaler consolidation).

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
  minAvailable: 80%     # at least 80% of pods must remain during disruption
  # OR: maxUnavailable: 1   # at most 1 pod down at a time
  selector:
    matchLabels:
      app: myapp
```

**PDB rules:**
- Use `minAvailable` for small deployments (e.g., `2` for 3-replica service)
- Use `maxUnavailable: 1` for large deployments (avoids percentage rounding issues)
- PDB + `terminationGracePeriodSeconds` = safe node drain
- Karpenter respects PDBs during node consolidation
- PDB does NOT protect against involuntary disruptions (OOMKill, node crash)

### Full Production Hardening Checklist

| Item | Check | How |
|------|-------|-----|
| Startup probe | Slow starts don't trigger restarts | `startupProbe` with adequate `failureThreshold` |
| Liveness probe | Detects deadlocks/hangs | Simple HTTP check, no external deps |
| Readiness probe | Removes unhealthy from LB | Checks DB, cache, downstream |
| Graceful shutdown | No dropped connections | `preStop` + SIGTERM handler + drain |
| Resource requests | Scheduler can bin-pack | CPU + memory requests set |
| Resource limits | No noisy neighbor | CPU limits (optional) + memory limits |
| PDB | Survives node drains | `minAvailable` or `maxUnavailable` set |
| Pod anti-affinity | Spread across nodes | `topologySpreadConstraints` or `podAntiAffinity` |
| Security context | Non-root, read-only FS | `runAsNonRoot: true`, `readOnlyRootFilesystem: true` |
| Network policy | Least-privilege networking | `NetworkPolicy` restricting ingress/egress |
| Priority class | Critical pods scheduled first | `priorityClassName: system-cluster-critical` |
| Topology spread | Survive zone failure | `topologySpreadConstraints` across zones |

## Step 18: Multi-Region Deployment Patterns

Deploy across regions for latency, availability, and compliance.

### Active-Active

All regions serve read/write traffic simultaneously. Highest availability and lowest latency. Hardest to implement (conflict resolution).

```
             ┌─────────────┐
             │  Global LB   │  (Route53 / Cloud DNS / Cloudflare)
             │ latency-based│
             └──────┬───────┘
          ┌─────────┼─────────┐
          ▼         ▼         ▼
    ┌──────────┐ ┌──────────┐ ┌──────────┐
    │ us-east  │ │ eu-west  │ │ ap-south │
    │  Active  │ │  Active  │ │  Active  │
    │ RW + RW  │ │  RW + RW │ │  RW + RW │
    └────┬─────┘ └────┬─────┘ └────┬─────┘
         │            │            │
         └──────┬─────┘────────────┘
                ▼
         ┌──────────────┐
         │ Multi-master  │  (CockroachDB / Spanner / Aurora Global)
         │    Database   │
         └──────────────┘
```

**Requirements:**
- Multi-master or globally replicated database (CockroachDB, Google Spanner, Aurora Global Database with write forwarding)
- Conflict resolution strategy: last-writer-wins, CRDTs, or application-level merge
- Session affinity or global session store (Redis Global Datastore)
- Idempotent writes (retry-safe operations)

```yaml
# Route53 latency-based routing
Resources:
  UseastRecord:
    Type: AWS::Route53::RecordSet
    Properties:
      Name: api.example.com
      Type: A
      SetIdentifier: us-east-1
      Region: us-east-1
      AliasTarget:
        DNSName: !GetAtt UseastAlb.DNSName
        HostedZoneId: !GetAtt UseastAlb.CanonicalHostedZoneID
      HealthCheckId: !Ref UseastHealthCheck
  EuwestRecord:
    Type: AWS::Route53::RecordSet
    Properties:
      Name: api.example.com
      Type: A
      SetIdentifier: eu-west-1
      Region: eu-west-1
      AliasTarget:
        DNSName: !GetAtt EuwestAlb.DNSName
        HostedZoneId: !GetAtt EuwestAlb.CanonicalHostedZoneID
      HealthCheckId: !Ref EuwestHealthCheck
```

**Database options:**

| Database | Type | Conflict Resolution | Latency |
|----------|------|---------------------|---------|
| CockroachDB | Multi-master SQL | Serializable, automatic | 50-200ms cross-region |
| Google Spanner | Multi-master SQL | External consistency (TrueTime) | <10ms read, 50-100ms write |
| Aurora Global | Primary + read replicas | Write forwarding to primary | <10ms read, primary-region write |
| DynamoDB Global Tables | Multi-master KV | Last-writer-wins | <10ms local read/write |
| YugabyteDB | Multi-master SQL | Serializable, Raft-based | 50-150ms cross-region |

### Active-Passive (Warm Standby)

One region serves traffic. Secondary region ready to take over. Simpler than active-active. Failover requires DNS switch.

```
             ┌──────────────┐
             │   Route53     │
             │ failover      │
             └──┬────────┬───┘
           primary    secondary
              ▼          ▼
       ┌──────────┐ ┌──────────┐
       │ us-east  │ │ eu-west  │
       │  Active  │ │ Standby  │
       │  RW      │ │  R only  │
       └────┬─────┘ └────┬─────┘
            │             │
            ▼             ▼
       ┌──────────┐ ┌──────────┐
       │ Primary  │→│ Replica  │  (async replication)
       │   DB     │ │   DB     │
       └──────────┘ └──────────┘
```

```yaml
# Route53 failover routing
Resources:
  PrimaryRecord:
    Type: AWS::Route53::RecordSet
    Properties:
      Name: api.example.com
      Type: A
      Failover: PRIMARY
      SetIdentifier: primary
      HealthCheckId: !Ref PrimaryHealthCheck
      AliasTarget:
        DNSName: !GetAtt PrimaryAlb.DNSName
        HostedZoneId: !GetAtt PrimaryAlb.CanonicalHostedZoneID
  SecondaryRecord:
    Type: AWS::Route53::RecordSet
    Properties:
      Name: api.example.com
      Type: A
      Failover: SECONDARY
      SetIdentifier: secondary
      AliasTarget:
        DNSName: !GetAtt SecondaryAlb.DNSName
        HostedZoneId: !GetAtt SecondaryAlb.CanonicalHostedZoneID
```

**Failover automation:**
1. Route53 health check fails (30s interval, 3 failures = 90s detection)
2. DNS automatically points to secondary
3. Secondary promotes replica to primary (if DB supports, e.g., Aurora failover)
4. Application in secondary region starts accepting writes

**Downside:** RPO > 0 (async replication lag), failover time 1-5 min (DNS TTL + DB promotion).

### Follow-the-Sun

Route traffic to region where it's business hours. Off-peak region runs at reduced capacity or standby.

```
Time (UTC)    Active Region     Standby Region
00:00-08:00   ap-south (Mumbai) us-east, eu-west (scaled down)
08:00-16:00   eu-west (Ireland) us-east, ap-south (scaled down)
16:00-00:00   us-east (Virginia) eu-west, ap-south (scaled down)
```

```yaml
# Karpenter NodePool with schedule-based scaling
apiVersion: karpenter.sh/v1beta1
kind: NodePool
metadata:
  name: myapp-ap-south
spec:
  template:
    spec:
      requirements:
      - key: karpenter.sh/capacity-type
        operator: In
        values: ["spot", "on-demand"]
  disruption:
    consolidationPolicy: WhenUnderutilized
    consolidateAfter: 5m
  # Cron-based scaling handled by KEDA or custom controller
```

**Implementation with KEDA Cron scaler:**
```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: myapp-ap-south
spec:
  scaleTargetRef:
    name: myapp
  minReplicaCount: 2     # off-peak minimum
  maxReplicaCount: 50
  triggers:
  - type: cron
    metadata:
      timezone: Asia/Kolkata
      start: 0 9 * * *     # scale up at 9 AM IST
      end: 0 17 * * *      # scale down at 5 PM IST
      desiredReplicas: "30"
```

**Cost benefit:** 60-70% savings vs running all regions at full capacity 24/7.

### Multi-Region Checklist

| Concern | Active-Active | Active-Passive | Follow-the-Sun |
---------|---------------|----------------|----------------|
| Availability | 99.99%+ | 99.95% | 99.9% |
| RPO | 0 (sync) | seconds-minutes (async) | seconds-minutes |
| RTO | seconds | 1-5 min | 1-5 min |
| Cost | 2-3x single region | 1.5x (standby infra) | 0.7-1.0x (scale down off-peak) |
| Complexity | Very high | Medium | Medium-high |
| DB requirement | Multi-master | Replica + promote | Replica + promote |
| Best for | Global SaaS, fintech | DR compliance, enterprise | Global with clear timezone patterns |

## Step 19: Disaster Recovery Patterns

### RPO and RTO

| Tier | RPO | RTO | Strategy | Cost |
|------|-----|-----|----------|------|
| Tier 0 | 0 | < 1 min | Multi-region active-active, sync replication | $$$$$ |
| Tier 1 | < 1 min | < 15 min | Active-passive, async replication, automated failover | $$$$ |
| Tier 2 | < 1 hour | < 1 hour | Cross-region backup + automated restore | $$$ |
| Tier 3 | < 24 hours | < 4 hours | Daily backups + manual restore | $$ |
| Tier 4 | < 7 days | < 24 hours | Weekly backups + manual rebuild | $ |

**RPO** (Recovery Point Objective): max acceptable data loss. Driven by backup/replication frequency.
**RTO** (Recovery Time Objective): max acceptable downtime. Driven by failover automation speed.

### Backup Strategies

**3-2-1 rule:** 3 copies, 2 different media, 1 offsite.

```bash
# PostgreSQL: continuous archiving + point-in-time recovery
# postgresql.conf
archive_mode = on
archive_command = 'aws s3 cp %p s3://my-backup-bucket/wal/%f'
wal_level = replica
max_wal_senders = 3

# Base backup (daily)
pg_basebackup -h primary.db -D /backup/base -Ft -z -P
aws s3 sync /backup/base s3://my-backup-bucket/base/$(date +%Y%m%d)/
```

```yaml
# Kubernetes: Velero for cluster-level backup
apiVersion: velero.io/v1
kind: Schedule
metadata:
  name: daily-backup
  namespace: velero
spec:
  schedule: "0 2 * * *"    # 2 AM daily
  template:
    includedNamespaces:
    - production
    storageLocation: aws-s3
    volumeSnapshotLocations:
    - aws
    ttl: 720h               # 30 day retention
    snapshotVolumes: true
```

```bash
# Velero restore
velero restore create --from-backup daily-backup-20250101020000 \
  --include-namespaces production \
  --namespace-mappings production:production-restored
```

**Backup strategy matrix:**

| Component | Method | Frequency | Retention | RPO |
-----------|--------|-----------|-----------|-----|
| PostgreSQL | WAL archiving + base backup | Continuous WAL, daily base | 30 days | Near-zero |
| MySQL | binlog + mysqldump | Continuous binlog, daily dump | 30 days | Near-zero |
| MongoDB | oplog + mongodump | Continuous oplog, daily dump | 30 days | Near-zero |
| S3/GCS | Versioning + lifecycle | Per-object | 90 days versions | Zero |
| K8s manifests | Git (GitOps) | Every commit | Forever | Zero |
| K8s state | Velero | Daily | 30 days | 24 hours |
| Secrets | Vault snapshot + encrypted backup | Hourly | 30 days | 1 hour |
| Container images | Registry replication | Per push | 90 days | Zero |

### Failover Automation

```yaml
# AWS: automated failover with CloudWatch alarm + Lambda
Resources:
  PrimaryHealthAlarm:
    Type: AWS::CloudWatch::Alarm
    Properties:
      AlarmName: primary-region-unhealthy
      MetricName: HealthCheckStatus
      Namespace: AWS/Route53
      Dimensions:
      - Name: HealthCheckId
        Value: !Ref PrimaryHealthCheck
      Statistic: Minimum
      Period: 60
      EvaluationPeriods: 3
      Threshold: 1
      ComparisonOperator: LessThanThreshold
      AlarmActions:
      - !Ref FailoverLambda

  FailoverLambda:
    Type: AWS::Serverless::Function
    Properties:
      Handler: failover.handler
      Runtime: python3.12
      Timeout: 300
      Environment:
        Variables:
          DB_CLUSTER_ID: !Ref AuroraCluster
          SECONDARY_REGION: eu-west-1
```

```python
# failover.py — automated DB promotion + DNS switch
import boto3

def handler(event, context):
    rds = boto3.client('rds', region_name='us-east-1')
    route53 = boto3.client('route53')

    # 1. Promote read replica in secondary region
    rds.failover_db_cluster(
        DBClusterIdentifier='myapp-secondary',
        TargetDBInstanceIdentifier='myapp-secondary-instance-1'
    )

    # 2. Wait for promotion
    waiter = rds.get_waiter('db_instance_available')
    waiter.wait(DBInstanceIdentifier='myapp-secondary-instance-1')

    # 3. Update Route53 to point to secondary
    # (handled by failover routing policy automatically via health checks)

    # 4. Notify team
    sns = boto3.client('sns')
    sns.publish(
        TopicArn='arn:aws:sns:us-east-1:123456:ops-alerts',
        Subject='FAILOVER EXECUTED',
        Message='Primary region unhealthy. Failed over to eu-west-1.'
    )
```

**Failover runbook (automated steps):**
1. Health check fails → CloudWatch alarm fires (3 min detection)
2. Lambda promotes DB replica in secondary region (2-5 min)
3. Route53 failover routing switches DNS (TTL-dependent, 30-60s)
4. Secondary region app instances accept traffic
5. Alert team via SNS/PagerDuty
6. Post-failover: verify data consistency, update monitoring dashboards

### DR Testing

```bash
# Chaos engineering: simulate region failure
# AWS Fault Injection Simulator
aws fis create-experiment-template \
  --description "Simulate us-east-1 failure" \
  --actions '{
    "stopInstances": {
      "actionId": "aws:ec2:stop-instances",
      "parameters": {
        "instanceIds": "i-xxx,i-yyy"
      }
    }
  }' \
  --stop-conditions '[{
    "source": "aws:cloudwatch:alarm",
    "value": "arn:aws:cloudwatch:us-east-1:123456:alarm:dr-test-passed"
  }]'
```

**DR test cadence:**
- Monthly: backup restoration test (restore to test env, verify data)
- Quarterly: full failover drill (DNS switch, DB promotion, app validation)
- Annually: game day (simulate region outage, run full runbook)

## Step 20: Cost Optimization for Deployments

Complements Step 10 (FinOps monitoring). Focus on infrastructure-level savings.

### Right-Sizing

```bash
# VPA recommender — get CPU/memory recommendations
kubectl describe vpa myapp-vpa
# Output:
#   Recommendation:
#     Lower Bound:  cpu: 50m, memory: 128Mi
#     Target:       cpu: 200m, memory: 256Mi
#     Upper Bound:  cpu: 500m, memory: 512Mi
```

```yaml
# Vertical Pod Autoscaler — auto-apply recommendations
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: myapp-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  updatePolicy:
    updateMode: "Auto"    # Auto = apply recommendations (pods restarted)
  resourcePolicy:
    containerPolicies:
    - containerName: myapp
      minAllowed:
        cpu: 100m
        memory: 128Mi
      maxAllowed:
        cpu: 2000m
        memory: 2Gi
```

**Right-sizing rules:**
1. Set requests at p50 usage, limits at p99 (or 2x requests)
2. Run VPA in "Off" mode for 2 weeks, review recommendations
3. Apply in "Auto" mode for non-critical workloads first
4. Right-size before scaling horizontally — vertical is cheaper
5. Re-evaluate quarterly — workload profiles change

### Spot Instances

60-90% savings. Best for stateless, fault-tolerant workloads.

```yaml
# Karpenter: prefer spot, fall back to on-demand
apiVersion: karpenter.sh/v1beta1
kind: NodePool
metadata:
  name: spot-pool
spec:
  template:
    spec:
      requirements:
      - key: karpenter.sh/capacity-type
        operator: In
        values: ["spot"]
      - key: node.kubernetes.io/instance-type
        operator: In
        values:
        - m5.large
        - m5.xlarge
        - m6i.large
        - m6i.xlarge
        - m5a.large      # diverse types = lower interruption rate
  limits:
    cpu: "500"
  disruption:
    consolidationPolicy: WhenUnderutilized
```

```yaml
# Pod: prefer spot nodes, tolerate interruption
spec:
  tolerations:
  - key: "karpenter.sh/capacity-type"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        preference:
          matchExpressions:
          - key: karpenter.sh/capacity-type
            operator: In
            values: ["spot"]
```

**Spot best practices:**
- Use multiple instance types (Karpenter handles this automatically)
- Set PDB to allow graceful draining on interruption
- Checkpoint long-running jobs (save state to S3 every N minutes)
- Use Spot Instance Advisor to pick low-interruption instance types
- Never use spot for: databases, stateful sets, single-replica critical services

### Reserved Capacity / Savings Plans

| Commitment | Discount | Flexibility | Best For |
|-----------|----------|-------------|----------|
| 1-year no upfront | 30-40% | Instance family locked | Stable production baseline |
| 1-year all upfront | 40-50% | Instance family locked | Same + budget predictability |
| 3-year all upfront | 60-70% | Instance family locked | Long-lived core services |
| Compute Savings Plan | 20-30% | Any compute (EC2/Fargate/Lambda) | Mixed workloads |
| Spot | 60-90% | Interruptible | Stateless, batch, dev/staging |

**Strategy: layered purchasing:**
```
Base load (40% of infra) → Reserved/Savings Plan (1-3 year)
Variable load (40%)      → On-Demand
Burst/dev/staging (20%)  → Spot
```

```bash
# AWS Cost Explorer: find Reserved Instance candidates
aws ce get-savings-plans-purchase-recommendation \
  --savings-plans-type COMPUTE_SP \
  --term-in-years ONE_YEAR \
  --payment-option NO_UPFRONT \
  --lookback-period-in-days SIXTY_DAYS
```

### Deployment-Specific Cost Optimization

1. **Blue-green cost:** run green at 10% capacity during validation, scale to 100% only at cutover
2. **Canary cost:** limit canary to spot nodes, cap at 5% of total compute
3. **Multi-region cost:** follow-the-sun scales down off-peak regions (see Step 18)
4. **CI/CD cost:** ephemeral test environments, auto-destroy after merge
5. **Log cost:** sample logs in non-critical paths (10% sampling), full logging for errors

## Step 21: Deployment Verification Patterns

Verify deployments are correct before, during, and after traffic shift.

### Smoke Tests

Fast, shallow checks that critical paths work after deploy. Run against new version before traffic shift.

```yaml
# Argo Rollouts: pre-promotion smoke test
apiVersion: argoproj.io/v1alpha1
kind: Rollout
spec:
  strategy:
    blueGreen:
      activeService: myapp-active
      previewService: myapp-preview
      prePromotionAnalysis:
        templates:
        - templateName: smoke-tests
        args:
        - name: service-url
          value: http://myapp-preview.production:8080
---
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: smoke-tests
spec:
  args:
  - name: service-url
  metrics:
  - name: smoke-test
    count: 1
    provider:
      job:
        spec:
          backoffLimit: 0
          template:
            spec:
              restartPolicy: Never
              containers:
              - name: smoke
                image: curlimages/curl:8.5.0
                command:
                - /bin/sh
                - -c
                - |
                  set -e
                  # Health check
                  curl -sf {{args.service-url}}/healthz/ready
                  # Critical user flows
                  curl -sf {{args.service-url}}/api/v1/products | jq '.items | length > 0'
                  # Auth check
                  curl -sf -H "Authorization: Bearer $TOKEN" {{args.service-url}}/api/v1/me
                  echo "All smoke tests passed"
```

```python
# pytest smoke tests (run as K8s Job or CI step)
import requests, os

BASE_URL = os.environ["SERVICE_URL"]

def test_health():
    r = requests.get(f"{BASE_URL}/healthz/ready", timeout=5)
    assert r.status_code == 200

def test_critical_api():
    r = requests.get(f"{BASE_URL}/api/v1/products", timeout=10)
    assert r.status_code == 200
    assert len(r.json()["items"]) > 0

def test_auth_flow():
    r = requests.post(f"{BASE_URL}/api/v1/auth/login",
                      json={"user": "smoke", "pass": "test"}, timeout=10)
    assert r.status_code == 200
    token = r.json()["token"]
    r = requests.get(f"{BASE_URL}/api/v1/me",
                     headers={"Authorization": f"Bearer {token}"}, timeout=10)
    assert r.status_code == 200

def test_write_flow():
    r = requests.post(f"{BASE_URL}/api/v1/orders",
                      json={"item_id": "smoke-test", "qty": 1}, timeout=10)
    assert r.status_code in (200, 201)
```

**Smoke test rules:**
- Run in < 30 seconds. Not exhaustive — just critical paths.
- Test: health endpoint, read path, write path, auth flow, external dependency connectivity.
- Fail = block promotion. Do not proceed with traffic shift.
- Run against preview/canary endpoint, not production active.

### Synthetic Monitoring

Continuously run simulated user flows in production. Detect issues before real users report them.

```yaml
# Blackbox exporter (Prometheus) — HTTP probes
scrape_configs:
- job_name: 'synthetic-probes'
  metrics_path: /probe
  params:
    module: [http_2xx]
  static_configs:
  - targets:
    - https://api.example.com/healthz/ready
    - https://api.example.com/api/v1/products
    - https://app.example.com/login
  relabel_configs:
  - source_labels: [__address__]
    target_label: __param_target
  - source_labels: [__param_target]
    target_label: instance
  - target_label: __address__
    replacement: blackbox-exporter:9115
```

```yaml
# Grafana synthetic monitoring (checkly-style)
# k6 scripted check
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  vus: 1,
  duration: '1m',
  thresholds: {
    http_req_duration: ['p(95)<500'],
    http_req_failed: ['rate<0.01'],
  },
};

export default function () {
  // Simulate user login flow
  let loginRes = http.post('https://api.example.com/api/v1/auth/login',
    JSON.stringify({ user: 'synthetic', pass: __ENV.SYNTH_PASS }),
    { headers: { 'Content-Type': 'application/json' } });

  check(loginRes, {
    'login status 200': (r) => r.status === 200,
    'login < 500ms': (r) => r.timings.duration < 500,
  });

  let token = loginRes.json('token');

  let productsRes = http.get('https://api.example.com/api/v1/products', {
    headers: { 'Authorization': `Bearer ${token}` },
  });

  check(productsRes, {
    'products status 200': (r) => r.status === 200,
    'has products': (r) => r.json('items').length > 0,
  });

  sleep(5);
}
```

**Synthetic monitoring stack:**

| Tool | Type | Protocol | Alert Integration |
|------|------|----------|-------------------|
| Prometheus Blackbox | Probes | HTTP, TCP, DNS, ICMP | Alertmanager |
| Grafana k6 Cloud | Scripted flows | HTTP, WebSocket, gRPC | Grafana alerts |
| Checkly | Scripted flows | HTTP, Playwright (browser) | PagerDuty, Slack |
| Datadog Synthetics | Scripted + browser | HTTP, WebSocket, DNS | Datadog alerts |
| AWS CloudWatch Synthetics | Canary scripts | HTTP, browser (Playwright) | CloudWatch alarms |

### Canary Analysis (Automated)

Statistical comparison of canary vs baseline metrics. Not just threshold checks — compare distributions.

```yaml
# Argo Rollouts AnalysisTemplate: multi-metric canary analysis
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: canary-analysis-comprehensive
spec:
  args:
  - name: service-name
  - name: baseline-hash
  - name: canary-hash
  metrics:
  # Success rate: canary must be >= baseline - 0.5%
  - name: success-rate
    interval: 1m
    count: 5
    failureLimit: 2
    successCondition: result[0] >= result[1] - 0.005
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          sum(rate(http_requests_total{
            service="{{args.service-name}}",
            status=~"2.*",
            pod_hash="{{args.canary-hash}}"
          }[5m]))
          /
          sum(rate(http_requests_total{
            service="{{args.service-name}}",
            pod_hash="{{args.canary-hash}}"
          }[5m]))
  # Latency: canary p99 must not exceed baseline by >10%
  - name: latency-comparison
    interval: 1m
    count: 5
    failureLimit: 2
    successCondition: result[0] <= result[1] * 1.1
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          histogram_quantile(0.99,
            sum(rate(http_request_duration_seconds_bucket{
              service="{{args.service-name}}",
              pod_hash="{{args.canary-hash}}"
            }[5m])) by (le)
          )
  # Error budget burn rate
  - name: error-budget-burn
    interval: 2m
    count: 3
    failureLimit: 1
    successCondition: result[0] < 1    # burn rate < 1x = healthy
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          sum(rate(http_requests_total{
            service="{{args.service-name}}",
            status=~"5.*",
            pod_hash="{{args.canary-hash}}"
          }[10m]))
          /
          sum(rate(http_requests_total{
            service="{{args.service-name}}",
            pod_hash="{{args.canary-hash}}"
          }[10m]))
          / 0.001   # 0.1% error budget
  # Saturation: canary CPU must not exceed 80%
  - name: cpu-saturation
    interval: 1m
    count: 5
    successCondition: result[0] <= 80
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          max(rate(container_cpu_usage_seconds_total{
            pod=~".*canary.*",
            namespace="production"
          }[5m])) * 100
```

**Kayenta (Netflix) analysis — comparison-based:**
```json
// Kayenta scoring config (for Spinnaker pipelines)
{
  "canaryConfig": {
    "canaryAnalysisConfig": {
      "beginCanaryAnalysisAfterMins": 5,
      "canaryAnalysisIntervalMins": 1,
      "lookbackMins": 10,
      "metricsAccountName": "atlas",
      "scopes": [{
        "scopeName": "default",
        "controlScope": { "scope": "app.canary_control" },
        "experimentScope": { "scope": "app.canary_experiment" }
      }]
    },
    "combinedCanaryResultStrategy": "LOWEST",
    "canaryHealthCheckHandler": { "minimumCanaryResultScore": 75 },
    "metrics": [
      { "name": "errors", "query": { "type": "atlas", "q": "nf.errors,app,:eq,:sum" } },
      { "name": "latency", "query": { "type": "atlas", "q": "nf.latency,app,:eq,:avg" } }
    ]
  }
}
```

**Canary analysis decision matrix:**

| Metric | Threshold | Action |
|--------|-----------|--------|
| Error rate | canary > baseline + 0.5% | Auto-rollback |
| P99 latency | canary > baseline * 1.1 | Auto-rollback |
| CPU/memory | canary > 80% | Auto-rollback |
| Error budget burn | > 1x rate | Pause, investigate |
| Business metric | canary < baseline * 0.95 | Auto-rollback |
| Throughput | canary < baseline * 0.5 | Pause (potential issue) |

**Analysis flow:**
1. Deploy canary at 5-10% traffic
2. Run AnalysisTemplate (Prometheus queries every 1m for 5-10m)
3. Compare canary metrics against baseline
4. If all metrics pass → increase traffic weight
5. Repeat at 30%, 60%, 100%
6. Any metric fails → auto-rollback, alert team

## Step 22: Deployment Failure Case Studies

Learn from catastrophic production failures. Each case study includes root cause, lesson, and prevention.

### Knight Capital (August 1, 2012) — $440M Loss in 45 Minutes

**What happened:** Knight Capital deployed new trading software to 8 servers. Server #7 had dead code from a decommissioned feature (Power Peg) still using the same flag (SMRI). The new code repurposed the flag. When activated, server #7 executed rogue trades at extreme volumes.

**Root cause:**
- Dead code left in production — Power Peg feature was obsolete but never removed
- Deployment reused old feature flag (SMRI) for new purpose
- No automated verification that all servers ran identical code
- Manual deployment process on 8 servers (human error window)

**Impact:** $440M loss in 45 minutes. Company required $400M emergency rescue. Stock dropped 75%.

**Prevention:**
```bash
# Dead code detection in CI
# Fail build if deprecated features aren't fully removed
grep -rn "Power Peg\|SMRI\|DEPRECATED" src/ && \
  echo "ERROR: Dead code detected" && exit 1

# Automated deployment verification — all servers must report same version
for server in $(cat server_list.txt); do
  deployed=$(ssh "$server" "cat /app/VERSION")
  [[ "$deployed" == "$EXPECTED_VERSION" ]] || \
    echo "MISMATCH on $server: $deployed vs $EXPECTED_VERSION" && exit 1
done
```

**Lesson:** Remove dead code aggressively. Never reuse feature flags. Automate deployment verification across all instances. Dead code is a ticking time bomb.

---

### AWS S3 Outage (February 28, 2017) — Cascading Restart

**What happened:** During routine capacity addition to S3 index subsystem, an operator executed a command that removed more servers than intended. The subsystem entered a full restart. During restart, S3 couldn't serve any requests. Dependent services (Lambda, ECS, EC2 status checks, CloudWatch) cascaded.

**Root cause:**
- Human operator removed too many S3 index servers in single command
- Subsystem required full restart (not incremental recovery)
- S3 was a hidden dependency for nearly all AWS services
- No blast radius limit on operational commands

**Impact:** ~4 hours of S3 unavailability in us-east-1. Cascading failures across dozens of AWS services. Estimated $150M+ in customer losses.

**Prevention:**
```yaml
# Blast radius controls — limit operational actions
apiVersion: policy/v1
kind: LimitRange
metadata:
  name: max-servers-per-action
spec:
  rules:
  - maxAffectedServers: 5
    cooldownPeriod: 300s  # Wait 5 min between batches
    requiresApproval: true
```

**Lesson:** Every operational command needs blast radius limits. Critical subsystems must support incremental recovery (not all-or-nothing restarts). Hidden dependencies create cascading failures — map them before incidents.

---

### Cloudflare Outage (July 2, 2019) — ReDoS Regex

**What happened:** A WAF rule update contained a poorly written regex. When triggered by specific attack patterns, the regex entered catastrophic backtracking (ReDoS). CPU spiked to 100% on all Cloudflare edge servers globally.

**Root cause:**
- Regex with exponential backtracking: `(?:(?:\"|'|\]|\}|\\|\d|(?:nan|infinity|true|false|null|undefined|...(truncated)`
- No regex complexity analysis in CI/CD pipeline
- WAF rules deployed globally simultaneously (no staged rollout)
- No CPU timeout on regex evaluation

**Impact:** Global Cloudflare outage. All websites behind Cloudflare returned 502 errors for 27 minutes.

**Prevention:**
```python
# Regex complexity checker — detect catastrophic backtracking
import re2  # Google RE2 engine (no backtracking, guaranteed linear time)

# Alternative: timeout-based regex execution
import signal

def regex_with_timeout(pattern, text, timeout=1):
    """Fail fast on slow regex."""
    signal.signal(signal.SIGALRM, lambda *a: TimeoutError())
    signal.alarm(timeout)
    try:
        result = re.search(pattern, text)
        signal.alarm(0)
        return result
    except TimeoutError:
        raise ValueError(f"Regex timeout after {timeout}s — likely ReDoS: {pattern[:80]}")

# CI check: reject regexes with nested quantifiers
REDOSSUSPECT = re.compile(r'\(.*[+*].*\)[+*]')  # (a+)+ style
```

**Lesson:** Never deploy regex rules globally at once — stage them. Test regex complexity in CI (use RE2 or timeout guards). Catastrophic backtracking is a real attack vector.

---

### GitLab Database Outage (January 31, 2017) — `rm -rf` on Production

**What happened:** A GitLab engineer was investigating replication lag on the production database. During manual recovery, he ran `rm -rf` on the wrong directory — deleting the production database. Backup systems were broken: LVM snapshots were 24h old, regular backups hadn't run in months, Azure snapshots had failed silently.

**Root cause:**
- `rm -rf /var/opt/gitlab/postgresql/data` instead of target directory
- No safeguards on destructive commands against production
- Backup systems silently broken (no monitoring/alerting)
- Single engineer with root access performing live surgery on production DB

**Impact:** 6 hours of downtime. ~5,000 projects, ~5,000 comments, ~700 users permanently lost. Real-time recovery streamed on YouTube.

**Prevention:**
```bash
# Shell alias — refuse rm -rf on known production paths
alias rm='rm_w_check'
rm_w_check() {
  for arg in "$@"; do
    case "$arg" in
      /var/opt/gitlab/*|/data/postgresql/*|/var/lib/postgresql/*)
        echo "BLOCKED: Would delete production data: $arg"
        echo "Use 'command rm' to override (requires 2-person approval)"
        return 1 ;;
    esac
  done
  command rm "$@"
}

# Backup verification — alert if backup age > threshold
backup_age=$(find /backups -name "*.tar.gz" -mmin -1440 | wc -l)
[[ "$backup_age" -eq 0 ]] && alert "NO BACKUPS IN LAST 24 HOURS"
```

**Lesson:** Never run destructive commands on production without safeguards. Monitor backup systems as carefully as production. Automate backup verification. One engineer should never have unilateral delete access to production data.

---

### Facebook/Meta BGP Outage (October 4, 2021) — 6-Hour Global Outage

**What happened:** During routine maintenance, a BGP configuration change was applied that inadvertently withdrew all Facebook BGP routes from the internet. Facebook's DNS servers became unreachable. Internal tools that relied on Facebook's own infrastructure also went down, creating a chicken-and-egg problem for recovery.

**Root cause:**
- BGP route withdrawal propagated globally within minutes
- Audit tool that should have caught the issue had a bug
- Physical access to data centers was controlled by systems that were now down
- Internal communication tools (Workplace, Messenger) were also down

**Impact:** ~6 hours of total Facebook/Instagram/WhatsApp/ Messenger outage. $60M+ in lost revenue. Physical access to data centers required manual intervention with security escorts.

**Prevention:**
```yaml
# BGP safety — route leak prevention
# Use RPKI (Resource Public Key Infrastructure) to validate route origins
bgp_safety:
  rpki_validation: required
  max_prefix_limit: 1000  # Alert if withdrawing more than N prefixes
  commit_confirm_timeout: 300  # Auto-revert if not confirmed in 5 min
  out_of_band_management:
    - independent_ssh_bastion
    - serial_console_access
    - physical_key_card_access  # Never depend on same infra for access
```

**Lesson:** Out-of-band access is mandatory — never depend on your own infrastructure for recovery. BGP changes need staged rollout (like any deployment). Audit tools need their own audits. Chicken-and-egg dependencies in recovery paths are lethal.

## Step 23: Successful Deployment Patterns

Learn from organizations that do deployments at extreme scale.

### Netflix — Spinnaker, Red/Black Deploys, Chaos Engineering

**Stack:** Spinnaker (deployment orchestrator), Kayenta (canary analysis), Chaos Monkey/Simian Army.

**Deployment pattern (red/black, aka blue/green):**
```json
// Spinnaker pipeline config
{
  "stages": [{
    "type": "deploy",
    "strategy": "redblack",
    "rollback": { "onFailure": true },
    "maxRemainingAsgs": 2,
    "delayBeforeDisableSec": 60,
    "delayBeforeScaleDownSec": 600
  }, {
    "type": "canary",
    "analysis": {
      "canaryConfig": {
        "canaryAnalysisIntervalMins": 5,
        "canaryResultScore": 95,
        "metricsAccountName": "atlas"
      }
    }
  }]
}
```

**Key practices:**
- Immutable infrastructure — never patch, always replace
- Red/black (not rolling) — instant rollback by re-enabling old ASG
- Kayenta automated canary analysis — statistical comparison, not thresholds
- Chaos engineering validates resilience continuously (not just during deploys)
- 100+ deployments per day across 1000+ microservices

**Why it works:** Spinnaker decouples deployment from CI. Red/black means zero mixed-version traffic. Chaos engineering proves the system handles failure *before* real failures hit.

---

### Google — SRE Error Budgets, Progressive Delivery

**Stack:** Borg (orchestration), SRE practices, error budgets, progressive rollouts.

**Error budget model:**
```yaml
# SRE error budget definition
service:
  name: search-api
  slo:
    availability: 99.99%    # 52.6 minutes downtime/year
    latency_p99: 200ms
  error_budget:
    monthly: 4.32 minutes   # 99.99% of 30 days
    burn_rate_alert:
      - window: 1h
        burn_rate: 14.4x    # Budget exhausted in 2 hours
        severity: page
      - window: 6h
        burn_rate: 6x       # Budget exhausted in 5 days
        severity: ticket

# Progressive rollout gated by error budget
rollout:
  stages: [1%, 5%, 25%, 50%, 100%]
  promotion_criteria:
    - error_budget_remaining > 50%  # Can't push if budget depleted
    - no_active_incidents
    - latency_within_slo
  pause_on_budget_exhaustion: true
```

**Key practices:**
- Error budgets balance reliability vs velocity — if budget remains, ship fast; if depleted, focus on reliability
- Progressive rollout with automated promotion/rollback at each stage
- Change velocity gated by reliability metrics
- Postmortems are blameless and mandatory for any budget breach

**Why it works:** Error budgets give teams a quantitative framework for risk. No subjective "is it safe to deploy?" — the budget tells you.

---

### Amazon — Two-Pizza Teams, Cell Architecture

**Stack:** Cell-based architecture, two-pizza team ownership, automated deployments.

**Cell architecture pattern:**
```
                    ┌─────────────────┐
                    │   Traffic       │
                    │   Router        │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
         ┌────┴────┐   ┌────┴────┐   ┌────┴────┐
         │ Cell 1  │   │ Cell 2  │   │ Cell 3  │
         │ (1/100  │   │ (1/100  │   │ (1/100  │
         │ users)  │   │ users)  │   │ users)  │
         └─────────┘   └─────────┘   └─────────┘
```

**Key practices:**
- Each cell serves a subset of users (hash user_id → cell)
- Blast radius = 1/N of traffic (deploy to one cell at a time)
- Two-pizza teams (6-10 people) own full lifecycle: build, deploy, operate
- "You build it, you run it" — no separate ops team
- Deploy pipeline: bake → test → 1 cell → all cells (hours apart)

**Why it works:** Cell architecture limits blast radius structurally, not just procedurally. Team ownership means code quality and operational readiness are aligned incentives.

---

### Etsy — Deployinator

**Stack:** Deployinator (custom web UI), feature flags, trunk-based development.

**Deployinator pattern:**
```ruby
# Deployinator — one-click deploy with instant rollback
class Deployinator
  def deploy(environment, branch)
    # 1. Run smoke tests against staging
    run_smoke_tests!(environment)
    # 2. Feature-flag the new code (dark launch)
    enable_feature_flags!(branch)
    # 3. Deploy to production (trunk-based, always deploy HEAD)
    deploy_to_production!(branch)
    # 4. Monitor dashboards for 15 minutes
    monitor_rollout!(duration: 15.minutes)
  end

  def rollback(environment)
    # Instant: disable feature flags, revert to last known good
    disable_new_flags!
    revert_to_last_good_deploy!
  end
end
```

**Key practices:**
- 50+ deploys per day from single web interface
- Trunk-based development — no long-lived branches
- Feature flags for every change — deploy ≠ release
- Deploy button visible to entire engineering (transparency)
- Instant rollback = one click (flag toggle or revert)

**Why it works:** Low friction deployment + feature flags = developers ship small changes frequently. Small changes are easy to debug and rollback.

## Step 24: Database Migration War Stories

Schema changes are the hardest part of zero-downtime deployments. These patterns solve them.

### GitHub — gh-ost (Online Schema Migration via Binlog Streaming)

**Problem:** `ALTER TABLE` on large MySQL tables locks the table (or requires expensive copy). pt-online-schema-change has triggers overhead.

**gh-ost approach:** Stream binlog events to apply changes incrementally, no triggers needed.

**Step-by-step:**
```bash
# 1. gh-ost creates a ghost table with new schema
#    (does NOT use triggers — reads binlog instead)

gh-ost \
  --host=production-db.example.com \
  --database=github \
  --table=pull_requests \
  --alter="ADD COLUMN merged_by_id BIGINT DEFAULT NULL" \
  --chunk-size=1000 \
  --max-load=Threads_running=25 \      # Pause if DB overloaded
  --critical-load=Threads_running=100 \ # Abort if dangerously loaded
  --initially-drop-ghost-table \
  --initially-drop-old-table \
  --serve-socket-file=/tmp/gh-ost.sock \ # Interactive control
  --postpone-cut-over-flag-file=/tmp/gh-ost-cut-over \
  --execute

# 2. gh-ost process:
#    a) Create _pull_requests_gho (ghost table) with new schema
#    b) Copy rows from pull_requests → _pull_requests_gho (chunked)
#    c) Stream binlog: apply INSERT/UPDATE/DELETE to ghost table in real-time
#    d) When copy catches up → atomic table swap (cut-over)
#    e) Old table becomes _pull_requests_del (dropped later)

# 3. Interactive control via socket:
echo "throttle" | nc -U /tmp/gh-ost.sock  # Pause migration
echo "chunk-size=500" | nc -U /tmp/gh-ost.sock  # Adjust speed
echo "cut-over" | nc -U /tmp/gh-ost.sock  # Trigger final swap
```

**Key details:**
- Binlog streaming = no triggers = less write amplification
- Interactive throttling: slow down during peak hours, speed up at night
- Atomic cut-over: rename tables in single transaction (<1ms lock)
- Automatic rollback if cut-over fails

---

### Stripe — Expand-Contract Pattern (Dual-Write)

**Problem:** Rename a column used by hundreds of services. Can't do it atomically — old code reads old column, new code reads new column.

**Expand-contract steps:**

```
Phase 1: EXPAND (add new column, keep old)
┌─────────────────────────────────────────────────┐
│ ALTER TABLE customers ADD COLUMN email_new TEXT; │
│ -- App code: write to BOTH email and email_new   │
│ -- Read from: email (old column)                 │
└─────────────────────────────────────────────────┘

Phase 2: MIGRATE DATA (backfill + dual-write)
┌──────────────────────────────────────────────────────┐
│ UPDATE customers SET email_new = email WHERE email_new│
│   IS NULL LIMIT 10000;  -- Batched backfill           │
│ -- App code: still reads from email                   │
│ -- App code: writes to BOTH columns                   │
│ -- Background job: backfills remaining rows            │
└──────────────────────────────────────────────────────┘

Phase 3: SWITCH READS
┌──────────────────────────────────────────────────────┐
│ -- App code: read from email_new (after verifying     │
│   backfill complete)                                  │
│ -- App code: still writes to BOTH columns             │
│ -- Verify: no reads from old column in monitoring     │
└──────────────────────────────────────────────────────┘

Phase 4: CONTRACT (remove old column)
┌──────────────────────────────────────────────────────────┐
│ -- Stop writing to old column                            │
│ ALTER TABLE customers DROP COLUMN email;                 │
│ ALTER TABLE customers RENAME COLUMN email_new TO email;  │
└──────────────────────────────────────────────────────────┘
```

**Stripe's implementation details:**
```ruby
# Dual-write in application code (Ruby)
class Customer < ApplicationRecord
  # Expand phase: write both columns
  def update_email(new_email)
    self.email = new_email
    self.email_new = new_email
    save!
  end

  # Migration status checked via feature flag
  def read_email
    if Feature.enabled?(:read_from_email_new)
      email_new
    else
      email
    end
  end
end
```

**Why it works:** Every phase is independently deployable and rollback-safe. Old code and new code coexist at every step. No atomic cutover required.

---

### Vitess — Large-Scale MySQL Schema Migration

**Problem:** GitHub's gh-ost works for single-shard MySQL. What about 1000+ shards?

**Vitess approach:**
```
1. Schema migration submitted as DDL to vtctld
2. Vitess applies migration to each shard independently
3. Each shard migration uses online DDL (no table locks)
4. Progress tracked per-shard, aggregated globally
5. Throttle based on replication lag across all shards
```

```bash
# Submit online DDL via Vitess
vtctldclient ApplySchema \
  --ddl_strategy="online" \
  --sql="ALTER TABLE orders ADD COLUMN shipping_method VARCHAR(50)" \
  commerce

# Monitor migration progress
vtctldclient OnlineDDL show commerce

# Throttle all migrations if replication lag > 10s
vtctldclient --server localhost:15999 ThrottledApp online-ddl --throttle
```

**Vitess-specific features:**
- `gh-ost` and `pt-osc` integrated per-shard
- Automatic retry on transient failures
- Cross-shard consistency guarantees
- Throttle based on replication lag (protects replicas)
- Cancel/rollback individual shard migrations

**When to use which:**

| Tool | Scale | Complexity | Best For |
|------|-------|------------|----------|
| Direct ALTER | <1M rows | Low | Small tables, off-peak |
| gh-ost | 1M-1B rows | Medium | Single-shard MySQL/Postgres |
| Vitess | 1B+ rows, 100+ shards | High | Distributed MySQL |
| pt-osc | 1M-1B rows | Medium | Legacy MySQL (triggers OK) |
| Online DDL (MySQL 8+) | <100M rows | Low | Instant DDL for supported ops |

## Step 25: Feature Flag Case Studies

### Facebook Gate — Thousands of Feature Flags at Scale

**Scale:** Facebook runs thousands of concurrent feature flags ("gates") controlling everything from UI experiments to backend infrastructure changes.

**Gate system architecture:**
```python
# Facebook Gate — simplified model
class Gate:
    """Each gate has: name, owner, audience rules, kill switch."""
    def __init__(self, name, owner, default=False):
        self.name = name
        self.owner = owner  # Required: who to page if gate causes issues
        self.default = default
        self.audience_rules = []  # User segments, % rollout, etc.
        self.kill_switch = False   # Emergency disable

    def evaluate(self, user, context):
        if self.kill_switch:
            return self.default
        for rule in self.audience_rules:
            if rule.matches(user, context):
                return rule.value
        return self.default

# Gate categories (Facebook taxonomy):
# 1. Release gates: control new feature rollout
# 2. Experiment gates: A/B test variants
# 3. Ops gates: operational kill switches
# 4. Permission gates: access control
```

**Key practices at scale:**
- Every gate has an owner (mandatory) — no orphaned flags
- Kill switches for every gate — instant disable without deploy
- Gate dependencies tracked — disabling parent gate cascades
- Automated cleanup — unused gates flagged after 90 days
- Gate audit log — who changed what, when, why

---

### LaunchDarkly — Feature Flag Patterns

**Flag types and lifecycles:**
```yaml
# Flag taxonomy (LaunchDarkly model)
flags:
  # 1. Release flag — controls feature rollout, short-lived
  - name: new-checkout-flow
    type: release
    lifecycle: create → ramp (1% → 10% → 50% → 100%) → cleanup
    ttl: 30 days  # Auto-remind to clean up

  # 2. Experiment flag — A/B test, medium-lived
  - name: checkout-button-color
    type: experiment
    variants: [control, variant_a, variant_b]
    metric: conversion_rate
    ttl: 90 days

  # 3. Ops flag — operational control, long-lived
  - name: payment-provider-fallback
    type: ops
    default: stripe
    fallback: paypal
    ttl: indefinite

  # 4. Permission flag — access control, permanent
  - name: admin-dashboard-access
    type: permission
    targeting: admins_only
    ttl: indefinite
```

**SDK pattern:**
```javascript
// LaunchDarkly SDK — Node.js
const LDClient = require('launchdarkly-node-server-sdk');
const client = LDClient.init('sdk-key');

// Evaluate flag with context
const context = {
  kind: 'user',
  key: 'user-123',
  email: 'user@example.com',
  plan: 'enterprise',
  country: 'US'
};

const showNewCheckout = await client.variation('new-checkout-flow', context, false);
if (showNewCheckout) {
  // New checkout flow
}

// Flag change listener — react to flag changes in real-time
client.on('update:checkout-button-color', (flag) => {
  console.log(`Flag changed: ${flag.key}, new value: ${flag.value}`);
});
```

---

### Microsoft — Flight Rings

**Ring-based rollout:**
```
Ring 0 (Canary)     → Microsoft internal employees (~1% of users)
Ring 1 (Preview)    → Windows Insiders, early adopters (~5%)
Ring 2 (Broad)      → General availability (all remaining users)
Ring 3 (Enterprise) → Managed enterprise deployments (controlled)
```

**Implementation:**
```xml
<!-- Windows Update targeting via rings -->
<DeploymentRing>
  <Ring name="Canary" percentage="1">
    <Criteria>
      <EmployeeStatus>Microsoft</EmployeeStatus>
      <DeviceType>Internal</DeviceType>
    </Criteria>
  </Ring>
  <Ring name="Preview" percentage="5">
    <Criteria>
      <InsiderStatus>Dev|Beta|ReleasePreview</InsiderStatus>
    </Criteria>
  </Ring>
  <Ring name="Broad" percentage="100">
    <Criteria>
      <Exclusions>
        <BlockedDriver>version<2.0</BlockedDriver>
      </Exclusions>
    </Criteria>
  </Ring>
</DeploymentRing>
```

**Key practices:**
- Ring progression requires sign-off at each stage
- Automatic hold if crash rate exceeds threshold
- Separate ring for enterprise (managed, not experimental)
- Rollback propagates backward through rings
- Metrics compared between rings before promotion

---

### Feature Flag Taxonomy (Comprehensive)

| Type | Purpose | Lifetime | Cleanup | Example |
|------|---------|----------|---------|---------|
| **Release** | Decouple deploy from release | Days-weeks | Mandatory after 100% rollout | `new-search-algorithm` |
| **Experiment** | A/B test, measure impact | Weeks-months | Mandatory after winner chosen | `checkout-button-red` |
| **Ops** | Runtime operational control | Long-lived | Not needed (permanent) | `enable-circuit-breaker` |
| **Permission** | Access control | Permanent | Not needed | `beta-user-access` |

**Flag hygiene rules:**
```yaml
flag_hygiene:
  max_age_release_flags: 60d      # Auto-create ticket to clean up
  max_age_experiment_flags: 120d  # Auto-create ticket to clean up
  max_concurrent_flags: 200       # Alert if exceeding
  required_fields:
    - owner                       # Who owns this flag
    - type                        # release/experiment/ops/permission
    - ticket                      # Link to issue/ticket
    - expected_removal_date       # When to clean up
  automated_checks:
    - stale_flag_detection        # Flags with no evaluation in 30 days
    - orphan_flag_detection       # Flags with no owner
    - dependency_check            # Flags referenced in code but not in system

# CI check: fail build if release flags exceed threshold
ci_flag_check:
  command: "flag-audit --type release --max-age 60d --fail-on-stale"
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
- AWS Lambda best practices: https://docs.aws.amazon.com/lambda/latest/dg/best-practices.html
- AWS Lambda Powertools: https://docs.powertools.aws.dev/lambda/python/latest/
- AWS SnapStart: https://docs.aws.amazon.com/lambda/latest/dg/snapstart.html
- AWS X-Ray: https://docs.aws.amazon.com/xray/latest/devguide/xray-services-lambda.html
- Cloud Run docs: https://cloud.google.com/run/docs
- Cloud Run traffic splitting: https://cloud.google.com/run/docs/rollouts-rollbacks-traffic-migration
- Azure Container Apps: https://learn.microsoft.com/en-us/azure/container-apps/
- Azure Container Apps KEDA: https://learn.microsoft.com/en-us/azure/container-apps/scale-app
- Azure Container Apps Dapr: https://learn.microsoft.com/en-us/azure/container-apps/dapr-overview
- Cloudflare Workers: https://developers.cloudflare.com/workers/
- Cloudflare Durable Objects: https://developers.cloudflare.com/durable-objects/
- Deno Deploy: https://deno.com/deploy
- OpenTelemetry Lambda: https://opentelemetry.io/docs/faas/
- Cold start benchmarks: https://maxday.github.io/lambda-perf/

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
13. **Don't initialize all clients at module scope** — lazy-init to minimize cold start
14. **Don't use Provisioned Concurrency + SnapStart together** — they're mutually exclusive on Lambda
15. **Don't ignore ARM64** — Graviton Lambda is 34% cheaper with similar or better performance
16. **Don't connect Lambda directly to RDS** — use RDS Proxy to avoid connection exhaustion
17. **Don't mix X-Ray SDK and OTEL layer** — they conflict; pick one
18. **Don't use `PutMetricData` in hot paths** — use EMF (Powertools Metrics) for zero-cost custom metrics
19. **Don't set Cloud Run concurrency too high** — test with your workload; 80 is safe default
20. **Don't deploy Dapr components without secrets** — use Azure Key Vault references, not inline connection strings
