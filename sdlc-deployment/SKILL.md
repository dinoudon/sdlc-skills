---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling, progressive delivery (Flagger/Argo Rollouts), feature flags (LaunchDarkly/Unleash/OpenFeature), rollback, database migrations, zero-downtime patterns. v3: Gateway API traffic splitting, OpenFeature CNCF standard, FinOps (OpenCost/Karpenter/FOCUS), AnalysisTemplate, multi-cluster progressive delivery. v3.1: Serverless (Lambda/Cloud Run/Container Apps), edge deployment (Cloudflare Workers/Deno Deploy), cold start optimization, serverless observability."
version: 3.2.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-green, rolling, feature-flags, progressive-delivery, flagger, argo-rollouts, kubernetes, zero-downtime, gateway-api, openfeature, finops, opencost, karpenter, analysis-template, multi-cluster, database-migration, serverless, lambda, cloud-run, container-apps, edge-deployment, cloudflare-workers, cold-start, serverless-observability]
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
