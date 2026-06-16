---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling, progressive delivery, feature flags, rollback, database migrations, zero-downtime patterns. Gateway API, OpenFeature, serverless, edge, production hardening, multi-region, DR, cost optimization, IaC testing, GitOps, service mesh, multi-tenancy, FinOps."
version: 4.9.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-green, rolling, feature-flags, progressive-delivery, kubernetes, zero-downtime, gateway-api, openfeature, finops, serverless, edge-deployment, production-hardening, multi-region, disaster-recovery, iac-testing, gitops, service-mesh, multi-tenancy]
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
- Tests infrastructure-as-code (Terraform, CloudFormation, Pulumi)
- Configures GitOps workflows (ArgoCD, Flux)
- Sets up service mesh (Istio, Linkerd, Cilium)

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
      maxSurge: 2
      maxUnavailable: 1
  template:
    spec:
      containers:
      - name: myapp
        image: myapp:v2
```

**Key params:** `maxSurge` (extra pods), `maxUnavailable` (min down), `minReadySeconds` (ready wait), `progressDeadlineSeconds` (fail timeout).

**Downside:** no instant rollback, mixed versions serve traffic.

## Step 2: Blue-Green Deployments

```yaml
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

**Key requirement:** both versions must share same DB schema (expand-contract migration).

**AWS:** Route53 weighted routing between ALB target groups, or ECS second service behind same ALB.

## Step 3: Canary Deployments

### Gateway API Traffic Splitting (Recommended)

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

**Advantages over Nginx Ingress annotations:** portable across implementations, structured CRDs, header/path/method routing natively, first-class in Flagger and Argo Rollouts.

**Flagger + Gateway API:**
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

## Step 4: Progressive Delivery

### Flagger

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
    webhooks:
    - name: acceptance-test
      type: pre-rollout
      url: http://flagger-loadtester.test/
      timeout: 30s
      metadata:
        type: bash
        cmd: "curl -sd 'test' http://myapp-canary.test:8080/api/health"
```

### Argo Rollouts

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

**Flagger vs Argo Rollouts:** Flagger is lighter, relies on service mesh, Flux-native. Argo Rollouts is standalone, built-in traffic routing, richer steps, wider adoption.

### Comprehensive AnalysisTemplate

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
  - name: error-rate
    interval: 1m
    count: 5
    failureLimit: 2
    successCondition: result[0] <= {{args.error-rate-threshold}}
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          sum(rate(http_requests_total{service="{{args.service-name}}",namespace="{{args.namespace}}",status=~"5.*"}[2m]))
          /
          sum(rate(http_requests_total{service="{{args.service-name}}",namespace="{{args.namespace}}"}[2m]))
  - name: p99-latency
    interval: 1m
    count: 5
    failureLimit: 2
    successCondition: result[0] <= {{args.p99-latency-threshold}}
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          histogram_quantile(0.99,sum(rate(http_request_duration_seconds_bucket{service="{{args.service-name}}",namespace="{{args.namespace}}"}[2m])) by (le)) * 1000
  - name: cpu-usage
    interval: 1m
    count: 5
    successCondition: result[0] <= 80
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          max(rate(container_cpu_usage_seconds_total{pod=~"{{args.service-name}}-canary.*",namespace="{{args.namespace}}"}[2m])) * 100
```

## Step 5: Feature Flags

### OpenFeature (CNCF Standard)

Vendor-agnostic API. Swap LaunchDarkly -> Unleash without changing app code.

```go
import "github.com/open-feature/go-sdk/openfeature"

openfeature.SetProvider(unleash.NewProvider(config))
client := openfeature.NewClient("myapp")

darkMode, _ := client.BooleanValue(ctx, "dark-mode", false, evalCtx)
details, _ := client.BooleanEvaluation(ctx, "new-checkout", false, evalCtx)
// details.Reason = "TARGETING_MATCH", details.Variant = "experiment-a"
```

```typescript
// Node.js
import { OpenFeature } from '@openfeature/server-sdk';
import { LaunchDarklyProvider } from '@openfeature/launchdarkly-provider';

OpenFeature.setProvider(new LaunchDarklyProvider({ sdkKey: 'sdk-xxx' }));
const client = OpenFeature.getClient();
const showNewUI = await client.getBooleanValue('new-ui', false, { targetingKey: userId });
```

**Providers:** LaunchDarkly, Unleash, Flagsmith, Flipt, CloudBees, Split, AWS AppConfig, Azure App Config.

**Hook pattern** — cross-cutting concerns:
```go
client.AddHooks(&MetricsHook{}, &AuditHook{})
// MetricsHook: record flag evaluation latency per flag key
// AuditHook: log every flag change for compliance
```

### Unleash (Open-Source)
```bash
docker run -p 4242:4242 unleashorg/unleash-server
```

**Activation strategies:** gradualRollout, userIds, IPs, hostnames.

### LaunchDarkly (SaaS)
- Server-side SDK evaluates locally (rules cached, streamed via SSE)
- Context-based targeting: user segments, percentage rollouts

### Flag Hygiene
```yaml
flag_hygiene:
  max_age_release_flags: 60d
  max_age_experiment_flags: 120d
  max_concurrent_flags: 200
  required_fields:
    - owner
    - type
    - ticket
    - expected_removal_date
  automated_checks:
    - stale_flag_detection    # No evaluation in 30 days
    - orphan_flag_detection   # No owner
```

### Feature Flag Taxonomy

| Type | Purpose | Lifetime | Cleanup |
|------|---------|----------|---------|
| **Release** | Decouple deploy/release | Days-weeks | Mandatory after 100% |
| **Experiment** | A/B test | Weeks-months | After winner chosen |
| **Ops** | Runtime control | Long-lived | Not needed |
| **Permission** | Access control | Permanent | Not needed |

## Step 6: Rollback Strategies

```bash
kubectl rollout history deployment/myapp
kubectl rollout undo deployment/myapp
kubectl rollout undo deployment/myapp --to-revision=3
kubectl rollout status deployment/myapp --timeout=300s
```

**Automated triggers:** `progressDeadlineSeconds` for K8s, Argo CD `automated.selfHeal`, Spinnaker + Kayenta canary score.

**Database rollback:** expand-contract migrations — add new columns (expand) -> deploy app -> drop old columns (contract). Tools: Flyway, Liquibase, golang-migrate, Alembic, Atlas.

## Step 7: Zero-Downtime Database Migrations

### Expand-Contract Pattern

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
    - name: Seed baseline schema
      run: migrate -path ./migrations -database $DB_URL up
    - name: Seed test data
      run: psql $DB_URL < ./test-fixtures/seed-data.sql
    - name: Run pending migrations
      run: migrate -path ./migrations -database $DB_URL up
    - name: Run old app tests against new schema
      run: APP_VERSION=previous go test ./... -run TestDatabase
    - name: Run new app tests against new schema
      run: go test ./... -run TestDatabase
    - name: Rollback migration
      run: migrate -path ./migrations -database $DB_URL down 1
    - name: Check row counts match baseline
      run: psql $DB_URL -c "SELECT COUNT(*) FROM users" | grep -q "$EXPECTED_COUNT"
```

## Step 9: Serverless Deployment — AWS Lambda

### Lambda Powertools (Python)

```python
from aws_lambda_powertools import Logger, Tracer, Metrics, MetricUnit
from aws_lambda_powertools.event_handler import APIGatewayRestResolver

logger = Logger(service="myapp")
tracer = Tracer(service="myapp")
metrics = Metrics(namespace="MyApp", service="myapp")
app = APIGatewayRestResolver()

@app.get("/items/<item_id>")
@tracer.capture_method
def get_item(item_id: str):
    logger.info("Fetching item", extra={"item_id": item_id})
    metrics.add_metric(name="GetItem", unit=MetricUnit.Count, value=1)
    return {"id": item_id, "name": "widget"}

@logger.inject_lambda_context
@tracer.capture_lambda_handler
@metrics.log_metrics
def handler(event: dict, context):
    return app.resolve(event, context)
```

## Step 10: Serverless Deployment — Google Cloud Run

```bash
gcloud run deploy myapp \
  --image gcr.io/myproject/myapp:latest \
  --concurrency 80 \
  --cpu 2 \
  --memory 2Gi \
  --max-instances 100 \
  --min-instances 2 \
  --cpu-boost
```

**Traffic splitting:**
```bash
gcloud run deploy myapp --image gcr.io/myproject/myapp:v2 --no-traffic
gcloud run services update-traffic myapp --to-revisions=myapp-00042=10,myapp-00041=90
gcloud run services update-traffic myapp --to-latest
```

## Step 11: Azure Container Apps

```bash
az containerapp create \
  --name myapp \
  --resource-group mygroup \
  --image myregistry.azurecr.io/myapp:v1 \
  --min-replicas 0 --max-replicas 50 \
  --scale-rule-name queue-scaler \
  --scale-rule-type azure-queue \
  --scale-rule-metadata queueName=myqueue connection=storage-connection
```

**KEDA scaler types:** azure-servicebus, rabbitmq, kafka, cron, redis, postgresql, external (Prometheus).

**Traffic splitting:**
```bash
az containerapp revision copy --name myapp --resource-group mygroup \
  --image myregistry.azurecr.io/myapp:v2
az containerapp ingress traffic set --name myapp --resource-group mygroup \
  --revision-weight myapp--v2=20 myapp--v1=80
```

## Step 12: Edge Deployment

### Cloudflare Workers

```typescript
export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    const url = new URL(request.url);
    return new Response(`Hello from ${url.pathname}`);
  },
};
```

**Platform primitives:** KV (config/cache), R2 (S3-compatible storage), D1 (SQLite), Durable Objects (stateful singletons), Queues, Vectorize.

### Deno Deploy

```typescript
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

## Step 13: Cold Start Optimization

| Runtime | Typical Cold Start | Notes |
|---------|-------------------|-------|
| Node.js | 150-300ms | Fast, large node_modules hurts |
| Python | 200-500ms | Import-heavy frameworks slow |
| Go | 50-100ms | Compiled binary, near-instant |
| Java (no SnapStart) | 3-6s | JVM class loading |
| Java (SnapStart) | 200-400ms | Lambda only |
| Rust | 10-30ms | Fastest |

**Checklist:**
1. Prefer Go/Rust/Node.js over Java/Python for latency-critical paths
2. Use ARM64 architecture (Lambda) — faster startup, cheaper
3. Bundle/minify — remove unused dependencies
4. Lazy-init heavy clients (DB, S3, SSM)
5. Reuse connections (HTTP keep-alive, DB pooling)
6. Set Provisioned Concurrency for p99 requirements
7. Enable SnapStart for Java
8. Enable Startup CPU Boost on Cloud Run
9. Set min-instances > 0 on Cloud Run

**Lazy init example:**
```python
_db_client = None
def get_db():
    global _db_client
    if _db_client is None:
        _db_client = boto3.client('dynamodb')
    return _db_client
```

**Critical:** DB connections should use RDS Proxy. Direct connections from Lambda exhaust connection pool during scale-up.

### Package Size Optimization

```python
# Python: use slim base, pin only what you need
# requirements.txt
boto3==1.34.0
requests==2.31.0
# NOT Django (200MB) — use FastAPI
```

```javascript
// Node.js: tree-shake with esbuild
{
  "bundle": true,
  "minify": true,
  "platform": "node",
  "target": "node20",
  "external": ["@aws-sdk/*"]  // provided by Lambda runtime
}
```

**Size targets:** <10 MB zipped for Python, <5 MB for Node.js (bundled), <25 MB for Java.

### Connection Reuse

```python
# Module-level session (persists across warm invocations)
import requests
session = requests.Session()  # NOT requests.get() per invocation

import boto3
dynamodb = boto3.resource('dynamodb')  # reused in warm containers
table = dynamodb.Table('my-table')

def handler(event, context):
    table.get_item(Key={'pk': event['id']})  # reuses connection
```

```typescript
// Node.js: reuse HTTP agent
import { Agent } from 'undici';

const agent = new Agent({
  keepAliveTimeout: 60_000,
  connections: 10,
});

export const handler = async (event) => {
  const res = await fetch('https://api.example.com/data', {
    dispatcher: agent,
  });
};
```

### Serverless Observability

| Challenge | Solution |
|-----------|----------|
| No persistent log files | Structured JSON logs -> CloudWatch/Cloud Logging |
| Distributed traces | X-Ray / Cloud Trace / OTEL |
| No host metrics | Lambda Insights / Powertools Metrics (EMF) |
| Log correlation | Powertools Logger with `correlation_id` |
| Cold start visibility | X-Ray subsegments, Powertools `cold_start` |

```yaml
# OTEL Lambda Layer
MyFunction:
  Properties:
    Layers:
      - !Sub arn:aws:lambda:${AWS::Region}:901920570463:layer:aws-otel-python-arm64-ver-1-x:1
    Environment:
      Variables:
        AWS_LAMBDA_EXEC_WRAPPER: /opt/otel-instrument
        OTEL_SERVICE_NAME: order-service
        OTEL_EXPORTER_OTLP_ENDPOINT: "http://otel-collector:4317"
```

**Stack:** Powertools Logger (structured logs) + OTEL or X-Ray (traces, not both) + EMF via Powertools Metrics + Lambda Insights + CloudWatch Logs Insights.

## Step 14: Production Hardening

### Health Check Probes (Kubernetes)

```yaml
spec:
  template:
    spec:
      containers:
      - name: myapp
        ports:
        - containerPort: 8080
        startupProbe:
          httpGet:
            path: /healthz/startup
            port: 8080
          periodSeconds: 5
          failureThreshold: 30    # 150s max startup
          timeoutSeconds: 3
        livenessProbe:
          httpGet:
            path: /healthz/live
            port: 8080
          periodSeconds: 10
          failureThreshold: 3     # 30s fail -> restart
          timeoutSeconds: 5
        readinessProbe:
          httpGet:
            path: /healthz/ready
            port: 8080
          periodSeconds: 5
          failureThreshold: 3     # 15s fail -> removed from endpoints
          timeoutSeconds: 3
```

**Probe rules:**
- Startup: block on init (migrations, cache warmup)
- Liveness: NO external deps (DB down ≠ restart). Only detect hangs.
- Readiness: check external deps. Failure = remove from LB, not restart.

**Probe endpoint implementation:**

```python
from fastapi import FastAPI, Response, status

app = FastAPI()
_is_ready = False
_is_started = False

@app.get("/healthz/startup")
async def startup():
    if _is_started:
        return {"status": "started"}
    return Response(status_code=status.HTTP_503_SERVICE_UNAVAILABLE)

@app.get("/healthz/live")
async def liveness():
    return {"status": "alive"}

@app.get("/healthz/ready")
async def readiness():
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

### Graceful Shutdown

```yaml
spec:
  template:
    spec:
      terminationGracePeriodSeconds: 60
      containers:
      - name: myapp
        lifecycle:
          preStop:
            exec:
              command: ["/bin/sh", "-c", "sleep 5"]
```

```go
// Go
srv := &http.Server{Addr: ":8080", Handler: mux}
go func() {
    sigChan := make(chan os.Signal, 1)
    signal.Notify(sigChan, syscall.SIGTERM, syscall.SIGINT)
    <-sigChan
    ctx, cancel := context.WithTimeout(context.Background(), 30*time.Second)
    defer cancel()
    srv.Shutdown(ctx)
    db.Close()
}()
srv.ListenAndServe()
```

**Shutdown sequence:** Pod marked -> removed from endpoints -> preStop (sleep 5s) -> SIGTERM -> drain -> SIGKILL.

### Resource Limits and Pod Disruption Budget

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "1000m"
    memory: "512Mi"
---
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
  maxUnavailable: 1
  selector:
    matchLabels:
      app: myapp
```

**Rules:** Memory limits = 1.5-2x request. Consider removing CPU limits for latency-sensitive services (throttling causes jitter).

### Production Hardening Checklist

| Item | How |
|------|-----|
| Startup probe | `startupProbe` with adequate `failureThreshold` |
| Liveness probe | Simple HTTP check, no external deps |
| Readiness probe | Checks DB, cache, downstream |
| Graceful shutdown | `preStop` + SIGTERM handler + drain |
| Resource requests | CPU + memory requests set |
| PDB | `minAvailable` or `maxUnavailable` |
| Pod anti-affinity | `topologySpreadConstraints` |
| Security context | `runAsNonRoot: true`, `readOnlyRootFilesystem: true` |
| Network policy | Least-privilege networking |
| Topology spread | Across zones |

## Step 15: Multi-Region Deployment

| Pattern | Latency | Consistency | Cost | Complexity | Best For |
|---------|---------|-------------|------|------------|----------|
| Active-active | Lowest | Eventual/tunable | Highest | Very high | Global SaaS, gaming |
| Active-passive | Higher | Strong (sync) | Medium | Medium | Enterprise, DR |
| Follow-the-sun | Varies | Eventual | 0.7-1x | Medium-high | Timezone patterns |
| Global LB + single | Medium | Strong | Low | Low | Single-market |

| Database | Conflict Resolution | Latency |
|----------|---------------------|---------|
| CockroachDB | Serializable, automatic | 50-200ms cross-region |
| Google Spanner | External consistency (TrueTime) | 50-100ms write |
| Aurora Global | Write forwarding to primary | <10ms read, primary write |
| DynamoDB Global | Last-writer-wins | <10ms local |

### Active-Passive Failover

```hcl
resource "aws_route53_health_check" "primary" {
  fqdn              = "app-primary.example.com"
  port               = 443
  type               = "HTTPS"
  resource_path      = "/healthz"
  failure_threshold  = 3
  request_interval   = 10
}

resource "aws_route53_record" "app" {
  zone_id = aws_route53_zone.main.zone_id
  name    = "app.example.com"
  type    = "A"
  failover_routing_policy { type = "PRIMARY" }
  set_identifier  = "primary"
  health_check_id = aws_route53_health_check.primary.id
  alias {
    name    = aws_lb.primary.dns_name
    zone_id = aws_lb.primary.zone_id
  }
}
```

### When to go multi-region:
- Users in 3+ regions (latency > 200ms)
- Regulatory requirements (data sovereignty, GDPR)
- SLA requires 99.99%+ availability
- DR mandate (RTO < 1 hour)

### When NOT to:
- Single-region user base

### CockroachDB Multi-Region

```sql
-- Configure regions
ALTER DATABASE mydb SET PRIMARY REGION "us-east1";
ALTER DATABASE mydb ADD REGION "eu-west1";
ALTER DATABASE mydb ADD REGION "ap-southeast1";

-- Table-level locality
ALTER TABLE users SET LOCALITY GLOBAL;            -- replicated to all regions
ALTER TABLE orders SET LOCALITY REGIONAL BY ROW;  -- row pinned to user's region
ALTER TABLE sessions SET LOCALITY REGIONAL BY TABLE; -- all data in primary
```

**Conflict resolution patterns:**

| Pattern | Use Case | Tradeoff |
|---------|----------|----------|
| Last-writer-wins | Session state, preferences | Data loss on concurrent writes |
| Merge (CRDT) | Counters, sets, maps | Limited data types |
| App-level resolution | Orders, inventory | Complex but semantically correct |
| Serializable DB (Spanner/Cockroach) | Financial | Higher latency |

## Step 16: Disaster Recovery

### RPO/RTO Tiers

| Tier | RPO | RTO | Strategy |
|------|-----|-----|----------|
| 0 | 0 | < 1 min | Active-active, sync replication |
| 1 | < 1 min | < 15 min | Active-passive, automated failover |
| 2 | < 1 hour | < 1 hour | Cross-region backup + automated restore |
| 3 | < 24 hours | < 4 hours | Daily backups + manual restore |

### Backup Strategy Matrix

| Component | Method | RPO |
|-----------|--------|-----|
| PostgreSQL | WAL archiving + base backup | Near-zero |
| MySQL | binlog + mysqldump | Near-zero |
| S3/GCS | Versioning + lifecycle | Zero |
| K8s manifests | Git (GitOps) | Zero |
| K8s state | Velero | 24 hours |

```yaml
# Velero scheduled backup
apiVersion: velero.io/v1
kind: Schedule
metadata:
  name: daily-backup
spec:
  schedule: "0 2 * * *"
  template:
    includedNamespaces: [production]
    storageLocation: aws-s3
    ttl: 720h
    snapshotVolumes: true
```

### DR Testing Cadence
- Monthly: backup restoration test
- Quarterly: full failover drill
- Annually: game day (simulate region outage)

### Automated Failover Script

```bash
#!/bin/bash
# failover.sh — promotes standby DB and updates DNS
set -euo pipefail

PRIMARY_REGION="${1:-us-east-1}"
STANDBY_REGION="${2:-us-west-2}"
DB_IDENTIFIER="${3:-myapp-primary}"

# 1. Verify primary is truly down
if aws rds describe-db-instances \
  --db-instance-identifier "${DB_IDENTIFIER}" \
  --region "${PRIMARY_REGION}" \
  --query 'DBInstances[0].DBInstanceStatus' \
  --output text 2>/dev/null | grep -q "available"; then
  echo "WARNING: Primary still available. Aborting."
  exit 1
fi

# 2. Promote read replica
aws rds promote-read-replica \
  --db-instance-identifier "${DB_IDENTIFIER}-replica" \
  --region "${STANDBY_REGION}"

# 3. Wait for promotion
aws rds wait db-instance-available \
  --db-instance-identifier "${DB_IDENTIFIER}-replica" \
  --region "${STANDBY_REGION}"

# 4. Update DNS
aws route53 change-resource-record-sets \
  --hosted-zone-id "${HOSTED_ZONE_ID}" \
  --change-batch file://dns-failover.json

echo "Failover complete."
```

### Failover Runbook
1. Health check fails -> CloudWatch alarm (3 min detection)
2. Lambda promotes DB replica (2-5 min)
3. Route53 failover switches DNS (30-60s)
4. Secondary region accepts traffic
5. Alert team via SNS/PagerDuty

## Step 17: Cost Optimization

### Right-Sizing with VPA

```yaml
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
    updateMode: "Auto"
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

**Rules:** Set requests at p50, limits at p99. Run VPA "Off" mode for 2 weeks first. Right-size before scaling horizontally.

### Spot Instances (60-90% savings)

```yaml
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
        values: ["m5.large", "m5.xlarge", "m6i.large", "m6i.xlarge"]
  limits:
    cpu: "500"
  disruption:
    consolidationPolicy: WhenUnderutilized
```

**Spot rules:** multiple instance types, PDB for graceful draining, checkpoint long jobs, never for databases/single-replica critical services.

### Reserved Capacity

| Commitment | Discount | Best For |
|-----------|----------|----------|
| 1-year no upfront | 30-40% | Stable baseline |
| 3-year all upfront | 60-70% | Long-lived core |
| Compute Savings Plan | 20-30% | Mixed workloads |
| Spot | 60-90% | Stateless, batch |

**Layered purchasing:** Base load (40%) -> Reserved. Variable (40%) -> On-Demand. Burst (20%) -> Spot.

## Step 18: Deployment Verification

### Smoke Tests (Argo Rollouts)

```yaml
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
                  curl -sf {{args.service-url}}/healthz/ready
                  curl -sf {{args.service-url}}/api/v1/products | jq '.items | length > 0'
                  echo "All smoke tests passed"
```

**Smoke test rules:** < 30 seconds, test health + read + write + auth, fail = block promotion.

### Synthetic Monitoring

| Tool | Type | Alert Integration |
|------|------|-------------------|
| Prometheus Blackbox | Probes (HTTP, TCP, DNS) | Alertmanager |
| Grafana k6 Cloud | Scripted flows | Grafana alerts |
| Checkly | Scripted + Playwright | PagerDuty, Slack |
| Datadog Synthetics | Scripted + browser | Datadog alerts |
| AWS CloudWatch Synthetics | Canary scripts | CloudWatch alarms |

### Canary Analysis Decision Matrix

| Metric | Threshold | Action |
|--------|-----------|--------|
| Error rate | canary > baseline + 0.5% | Auto-rollback |
| P99 latency | canary > baseline * 1.1 | Auto-rollback |
| CPU/memory | canary > 80% | Auto-rollback |
| Error budget burn | > 1x rate | Pause, investigate |
| Business metric | canary < baseline * 0.95 | Auto-rollback |

## Step 19: Automated Rollback

### Metric-Based (Argo Rollouts)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: AnalysisTemplate
metadata:
  name: latency-rollback
spec:
  args:
  - name: service-name
  metrics:
  - name: p99-latency
    interval: 30s
    count: 10
    successCondition: result[0] < 500
    failureLimit: 3
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          histogram_quantile(0.99,sum(rate(http_request_duration_seconds_bucket{service="{{args.service-name}}",route!="health"}[2m])) by (le)) * 1000
  - name: error-rate
    interval: 30s
    count: 10
    successCondition: result[0] < 0.01
    failureLimit: 2
    provider:
      prometheus:
        address: http://prometheus.monitoring:9090
        query: |
          sum(rate(http_requests_total{service="{{args.service-name}}",code=~"5.."}[2m])) /
          sum(rate(http_requests_total{service="{{args.service-name}}"}[2m]))
```

### Kayenta (Netflix) — Statistical Analysis

| Test | Use Case | How |
|------|----------|-----|
| Mann-Whitney U | Metric distributions | Non-parametric, no normality assumption |
| Bootstrapping | Ratio metrics (error rates) | Resamples 1000+ times for confidence interval |

**Kayenta vs Flagger vs Argo Analysis:**

| Feature | Kayenta | Flagger | Argo Analysis |
|---------|---------|---------|---------------|
| Mann-Whitney U | Yes | No | No |
| Multi-metric weighted score | Built-in | Manual | Manual |
| Standalone API | Yes | No | No |
| K8s native | No | Yes | Yes |

## Step 20: Deployment Failure Case Studies

### Knight Capital (2012) — $440M loss in 45 min
Dead code repurposed old feature flag. 8 servers, 1 had stale code.
**Lesson:** Remove dead code aggressively. Never reuse feature flags. Automate deployment verification across all instances.

### AWS S3 (2017) — Cascading restart
Operator removed too many S3 index servers. Full restart required.
**Lesson:** Blast radius limits on operational commands. Incremental recovery, not all-or-nothing.

### Cloudflare (2019) — ReDoS regex
WAF rule regex caused catastrophic backtracking. Global CPU spike.
**Lesson:** Stage regex rules. Test complexity in CI. Use RE2 or timeout guards.

### GitLab (2017) — `rm -rf` on production DB
Engineer ran `rm -rf` on wrong directory. Backups were silently broken.
**Lesson:** Safeguards on destructive commands. Monitor backup systems.

### Facebook BGP (2021) — 6-hour global outage
BGP config withdrew all routes. Internal tools also went down.
**Lesson:** Out-of-band access mandatory. Staged rollout for network changes.

## Step 21: Successful Deployment Patterns

| Org | Pattern | Key Practice |
|-----|---------|--------------|
| Netflix | Spinnaker + red/black | Immutable infra, Kayenta canary analysis, chaos engineering |
| Google | SRE error budgets | Error budget gates rollout: budget remains = ship fast, depleted = focus on reliability |
| Amazon | Cell architecture | Each cell = 1/N users, blast radius limited structurally |
| Etsy | Deployinator | One-click deploy, feature flags for every change, 50+ deploys/day |

## Step 22: Database Migration War Stories

### gh-ost (GitHub) — Online schema migration via binlog

```bash
gh-ost \
  --host=production-db.example.com \
  --database=github \
  --table=pull_requests \
  --alter="ADD COLUMN merged_by_id BIGINT DEFAULT NULL" \
  --chunk-size=1000 \
  --max-load=Threads_running=25 \
  --critical-load=Threads_running=100 \
  --execute
```

No triggers — reads binlog. Interactive throttling via socket. Atomic cut-over (<1ms lock).

### Expand-Contract (Stripe) — Dual-write

```
Phase 1: EXPAND   — ADD COLUMN, write to BOTH
Phase 2: MIGRATE  — backfill data in batches
Phase 3: SWITCH   — read from new column
Phase 4: CONTRACT — DROP old column
```

### When to use which migration tool

| Tool | Scale | Best For |
|------|-------|----------|
| Direct ALTER | <1M rows | Small tables, off-peak |
| gh-ost | 1M-1B rows | Single-shard MySQL |
| Vitess | 1B+ rows, 100+ shards | Distributed MySQL |
| Online DDL (MySQL 8+) | <100M rows | Instant DDL for supported ops |

## Step 23: Infrastructure-as-Code Testing

### 4-Layer Strategy

```
Layer 1: Static Analysis (seconds)
├── Syntax: terraform validate, cfn-lint
├── Security: tfsec, Checkov, Trivy Config
└── Policy: OPA/Rego, Sentinel

Layer 2: Unit Tests (seconds, mocked)
├── terraform test (built-in)
└── Terratest unit

Layer 3: Integration Tests (minutes, real infra)
├── Terratest: plan + apply + validate + destroy
└── kitchen-terraform

Layer 4: Compliance (continuous)
├── Drift detection
├── OPA/Gatekeeper, Sentinel
└── Infracost (cost estimation)
```

### Terratest

```go
func TestTerraformS3Bucket(t *testing.T) {
    t.Parallel()
    terraformOptions := &terraform.Options{
        TerraformDir: "../examples/s3-bucket",
        Vars: map[string]interface{}{
            "bucket_name": fmt.Sprintf("test-bucket-%s", uniqueId),
        },
    }
    defer terraform.Destroy(t, terraformOptions)
    terraform.InitAndApply(t, terraformOptions)
    bucketName := terraform.Output(t, terraformOptions, "bucket_name")
    url := fmt.Sprintf("https://%s.s3.amazonaws.com", bucketName)
    http_helper.HttpGetWithRetry(t, url, nil, 200, "", 30, 10*time.Second)
}
```

### OPA/Rego

```rego
package terraform.aws.s3

deny[msg] {
    resource := input.planned_values.root_module.resources[_]
    resource.type == "aws_s3_bucket"
    not startswith(resource.values.bucket, "internal-")
    msg := sprintf("S3 bucket '%s' must have 'internal-' prefix", [resource.values.bucket])
}
```

### CI Integration

```yaml
name: IaC Tests
on: [pull_request]
jobs:
  iac-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: tfsec
        uses: aquasecurity/tfsec-action@v1.0.3
      - name: Checkov
        uses: bridgecrewio/checkov-action@v12
        with:
          directory: terraform/
          framework: terraform
      - name: OPA/Conftest
        run: conftest test terraform-plan.json -p policy/
      - name: Terraform Unit Tests
        run: terraform test -filter=unit_test.tftest.hcl
      - name: Terratest
        run: cd tests && go test -v -timeout 30m -parallel 4 ./...
      - name: Infracost
        uses: infracost/infracost-gh-action@v1
```

## Step 24: GitOps Patterns

### ArgoCD App-of-Apps

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: cluster-apps
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/org/gitops-infra.git
    targetRevision: main
    path: cluster-apps/production
  destination:
    server: https://kubernetes.default.svc
    namespace: argocd
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### ArgoCD ApplicationSets (Multi-Cluster)

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: multi-cluster-app
  namespace: argocd
spec:
  generators:
    - clusters:
        selector:
          matchLabels:
            env: production
    - git:
        repoURL: https://github.com/org/apps.git
        revision: main
        directories:
          - path: apps/*
  template:
    metadata:
      name: '{{name}}-{{path.basename}}'
    spec:
      source:
        repoURL: https://github.com/org/apps.git
        targetRevision: main
        path: '{{path}}'
      destination:
        server: '{{server}}'
        namespace: '{{path.basename}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

### Flux v2

### ArgoCD Sync Waves & Phases

```yaml
# Order resources with sync waves (annotations)
# Wave -1: CRDs, Wave 0: Namespaces/RBAC, Wave 1: Secrets/ConfigMaps
# Wave 2: Deployments/Services, Wave 3: Ingress/NetworkPolicy
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  annotations:
    argocd.argoproj.io/sync-wave: "2"
---
# PreSync hook: run DB migration before app deploy
apiVersion: batch/v1
kind: Job
metadata:
  name: db-migrate
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: HookSucceeded
```

### Progressive Delivery with GitOps

```yaml
# Flux + Flagger
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: myapp
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  analysis:
    interval: 1m
    threshold: 5
    maxWeight: 50
    stepWeight: 10
    metrics:
      - name: request-success-rate
        thresholdRange:
          min: 99
```

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: app-source
  namespace: flux-system
spec:
  interval: 1m
  url: https://github.com/org/app
  ref:
    branch: main
---
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: app
  namespace: flux-system
spec:
  interval: 10m
  path: ./deploy/overlays/production
  prune: true
  sourceRef:
    kind: GitRepository
    name: app-source
  healthChecks:
    - apiVersion: apps/v1
      kind: Deployment
      name: myapp
```

## Step 25: Service Mesh

### Comparison

| Feature | Istio | Linkerd | Cilium |
|---------|-------|---------|--------|
| Proxy | Envoy (C++) | linkerd2-proxy (Rust) | eBPF kernel |
| Sidecar | Yes (Ambient=no) | Yes | No (sidecar-less) |
| L4 overhead | 1-3ms | <1ms | ~0ms |
| L7 policies | Yes | Yes | Yes |
| Resource cost | High | Low | Lowest |
| Observability | Kiali, Prometheus | Built-in viz | Hubble |

### Istio Traffic Management

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: myapp
spec:
  hosts: [myapp.example.com]
  gateways: [myapp-gateway]
  http:
    - match:
        - headers:
            x-canary:
              exact: "true"
      route:
        - destination:
            host: myapp
            subset: v2
    - route:
        - destination:
            host: myapp
            subset: v1
          weight: 90
        - destination:
            host: myapp
            subset: v2
          weight: 10
      retries:
        attempts: 3
        perTryTimeout: 2s
      timeout: 10s
```

### Cilium L7 Policy

### Istio DestinationRule

```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: myapp
spec:
  host: myapp
  trafficPolicy:
    connectionPool:
      tcp:
        maxConnections: 100
      http:
        http1MaxPendingRequests: 100
        http2MaxRequests: 1000
    outlierDetection:
      consecutive5xxErrors: 5
      interval: 30s
      baseEjectionTime: 30s
      maxEjectionPercent: 50
    loadBalancer:
      simple: LEAST_REQUEST
  subsets:
    - name: v1
      labels:
        version: v1
    - name: v2
      labels:
        version: v2
```

### Istio Ambient Mesh (sidecar-less)

```yaml
# Enable ambient mode per namespace
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    istio.io/dataplane-mode: ambient
```

**Ambient vs sidecar:**
```
Feature              Sidecar          Ambient
──────────────────────────────────────────────
Overhead per pod     ~50MB RAM        0 (shared ztunnel)
Latency overhead     1-3ms            <0.5ms (L4)
Upgrade path         Pod restart      Rolling ztunnel update
L7 policies          Always           Opt-in per namespace
```

```yaml
apiVersion: cilium.io/v2
kind: CiliumNetworkPolicy
metadata:
  name: api-policy
  namespace: production
spec:
  endpointSelector:
    matchLabels:
      app: myapp
  ingress:
    - fromEndpoints:
        - matchLabels:
            app: frontend
      toPorts:
        - ports:
            - port: "8080"
          rules:
            http:
              - method: GET
                path: "/api/v1/.*"
              - method: POST
                path: "/api/v1/orders"
```

## Step 26: Edge Platform Decision Matrix

```
Feature              CF Workers       Deno Deploy      Lambda@Edge
─────────────────────────────────────────────────────────────────────
Runtime              V8 isolates      V8 isolates      Node.js/Python
Startup              ~0ms             ~0ms             50-500ms
Max CPU time         30s (paid)       30s (paid)       5s/30s
Memory               128MB            50MB             128MB-10GB
State                KV, R2, D1, DO   Deno KV          DynamoDB/S3
Global PoPs          300+             35+              220+
WebSockets           Yes (Durable Obj) Yes             No
Vendor lock-in       Medium           Low              High
```

## Step 27: Multi-Tenancy

### Namespace Isolation

```yaml
# RBAC per tenant
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: tenant-admin
  namespace: acme
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
---
# NetworkPolicy: default deny + allow same tenant
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: acme
spec:
  podSelector: {}
  policyTypes: [Ingress]
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-same-tenant
  namespace: acme
spec:
  podSelector: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          tenant: acme
---
# ResourceQuota per tenant
apiVersion: v1
kind: ResourceQuota
metadata:
  name: tenant-quota
  namespace: acme
spec:
  hard:
    requests.cpu: "20"
    requests.memory: "40Gi"
    limits.cpu: "40"
    limits.memory: "80Gi"
    pods: "100"
```

### Tenant-Aware Data

| Pattern | Isolation | Cost | Best For |
|---------|-----------|------|----------|
| Row-level (shared schema) | Logical | Lowest | < 100 tenants |
| Schema-per-tenant | Schema | Medium | 100-1K tenants |
| Database-per-tenant | Physical | Highest | Enterprise, compliance |

### Virtual Clusters

| Tool | Isolation | Overhead | Best For |
|------|-----------|----------|----------|
| vCluster | Virtual API server | ~256MB per vcluster | Full K8s per tenant |
| Capsule | Namespace grouping | Minimal | Org-aligned teams |
| HNC | Namespace tree | Minimal | Hierarchical orgs |

## Step 28: FinOps Practices

### Cost Allocation Labels

```yaml
# Enforce cost labels via OPA/Gatekeeper
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredLabels
metadata:
  name: require-cost-labels
spec:
  match:
    kinds:
    - apiGroups: ["apps"]
      kinds: ["Deployment", "StatefulSet"]
  parameters:
    labels:
    - key: "cost-center"
    - key: "team"
    - key: "environment"
```

### Kubecost / OpenCost

```bash
helm install kubecost cost-analyzer \
  --repo https://kubecost.github.io/cost-analyzer-helm-chart \
  --namespace kubecost --create-namespace \
  --set kubecostToken="your-token"
```

| Category | Allocation Method |
|----------|-------------------|
| Compute (CPU/RAM) | Proportional to requests |
| Storage (PV/PVC) | Per-PVC |
| Network | Flow-based sampling |
| Shared costs | Equal, weighted, or proportional |
| Idle costs | Distribute or leave unallocated |

### FinOps Maturity

| Level | Name | Capabilities | Timeline |
|-------|------|--------------|----------|
| 0 | Inform | Basic cloud billing | Day 1 |
| 1 | Showback | Kubecost, namespace allocation | Month 1-2 |
| 2 | Chargeback | Labels enforced, internal billing | Month 3-6 |
| 3 | Optimize | VPA rightsizing, spot, reserved | Month 6-12 |
| 4 | Automate | Auto-rightsizing, Karpenter, cost alerts | Month 12+ |

## Step 29: Edge Deep Dive

### Cloudflare Durable Objects

```javascript
// Durable Objects — stateful edge (rate limiter)
export class RateLimiter {
  constructor(state, env) {
    this.state = state;
    this.requests = [];
  }
  async fetch(request) {
    const now = Date.now();
    this.requests = this.requests.filter(t => now - t < 60000);
    if (this.requests.length >= 100) {
      return new Response('Rate limited', { status: 429 });
    }
    this.requests.push(now);
    return new Response('OK');
  }
}
```

```javascript
// KV + R2 + D1 — edge data access
export default {
  async fetch(request, env) {
    const url = new URL(request.url);
    // KV — eventually-consistent config cache
    const cached = await env.MY_KV.get(url.pathname, { type: 'json' });
    if (cached) return Response.json(cached);
    // R2 — S3-compatible object storage
    if (url.pathname.startsWith('/assets/')) {
      const object = await env.MY_BUCKET.get(url.pathname.slice(8));
      if (object) return new Response(object.body);
    }
    // D1 — SQLite at edge
    const { results } = await env.DB.prepare(
      "SELECT * FROM products WHERE category = ?1 LIMIT 10"
    ).bind(url.searchParams.get('category')).all();
    await env.MY_KV.put(url.pathname, JSON.stringify(results), { expirationTtl: 300 });
    return Response.json(results);
  }
};
```

### Lambda@Edge

```javascript
// 4 trigger points in CloudFront
// 1. Viewer Request — A/B testing at edge
exports.viewerRequest = async (event) => {
  const request = event.Records[0].cf.request;
  const headers = request.headers;
  const cookie = (headers.cookie || []).find(c => c.value.includes('experiment='));
  if (!cookie) {
    const variant = Math.random() < 0.5 ? 'a' : 'b';
    request.origin = {
      custom: {
        domainName: variant === 'a' ? 'origin-a.example.com' : 'origin-b.example.com',
        port: 443, protocol: 'https',
      }
    };
    headers.cookie = [{ key: 'Cookie', value: `experiment=${variant}` }];
  }
  return request;
};
```

**Lambda@Edge limits:** 5s timeout (viewer), 30s (origin). 128MB memory (viewer), 10GB (origin). No env vars, no Lambda layers. Must deploy in us-east-1.

### Deno Deploy KV

```typescript
// Deno KV — strongly-consistent globally distributed KV
Deno.serve(async (req: Request) => {
  const kv = await Deno.openKv();
  const key = ["users", new URL(req.url).searchParams.get("id")];
  const entry = await kv.get(key);
  // CAS (compare-and-swap) update
  const result = await kv.atomic()
    .check(entry)
    .set(key, { ...entry.value, lastAccess: Date.now() })
    .commit();
  if (!result.ok) return new Response("Conflict, retry", { status: 409 });
  return Response.json(entry.value);
});
```

## Step 30: Tenant Data Isolation

### PostgreSQL Row-Level Security

```sql
-- Add tenant_id column, enable RLS
ALTER TABLE orders ADD COLUMN tenant_id TEXT NOT NULL;
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

-- Policy: users see only their tenant's rows
CREATE POLICY tenant_isolation ON orders
  USING (tenant_id = current_setting('app.tenant_id'));

-- Application sets tenant context per request
SET LOCAL app.tenant_id = 'acme';
SELECT * FROM orders;  -- returns only acme rows
```

### Schema-per-tenant

```sql
CREATE SCHEMA tenant_acme;
CREATE TABLE tenant_acme.orders (LIKE public.orders_template INCLUDING ALL);
SET search_path TO tenant_acme, public;
```

### Helm Test Hooks

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: smoke-test
  annotations:
    "helm.sh/hook": test
    "helm.sh/hook-delete-policy": before-hook-creation,hook-succeeded
spec:
  restartPolicy: Never
  containers:
  - name: smoke
    image: curlimages/curl:latest
    command:
    - sh
    - -c
    - |
      set -e
      curl -sf http://myapp:8080/healthz
      curl -sf http://myapp:8080/api/v1/status | jq .
```

```bash
helm test myapp --timeout 120s --logs
```

### Spot Interruption Handler

```bash
helm install node-termination-handler aws-node-termination-handler \
  --repo https://aws.github.io/eks-charts \
  --namespace kube-system \
  --set enableSpotInterruptionDraining=true \
  --set enableRebalanceRecommendationDraining=true
```

**Spot interruption flow:**
```
AWS sends interruption notice (2 min warning)
    → NTH receives IMDS notification
    → Cordon node (no new pods)
    → Taint with NoSchedule
    → Drain existing pods (PDB respected)
    → Karpenter provisions replacement node
```

### Spot-Eligible Checklist

- [ ] Stateless or persistent storage outside pod
- [ ] Graceful shutdown handler (SIGTERM) with < 120s drain
- [ ] Horizontal scaling (replicas > 1)
- [ ] PDB configured
- [ ] No strict latency SLA (p99 < 500ms) for critical path
- [ ] Interruption handler deployed
- [ ] Mix of instance types
- [ ] Pod anti-affinity across nodes/zones

### Goldilocks (Namespace-Level VPA Dashboard)

```bash
helm install goldilocks fairwinds-stable/goldilocks \
  --namespace goldilocks --create-namespace \
  --set vpa.enabled=true
kubectl label namespace acme goldilocks.fairwinds.io/enabled=true
kubectl -n goldilocks port-forward svc/goldilocks-dashboard 8080:80
```

### Verification Level Decision Matrix

| Level | Catches | Latency | Cost | When |
|-------|---------|---------|------|------|
| K8s probes | Container crash, OOM | Instant | Free | Always |
| Smoke tests | Broken endpoints | Minutes | Low | After every deploy |
| Synthetics | Broken user flows | Minutes | Medium | Continuous in prod |
| Canary analysis | Latency regression | 10-30 min | Medium | Progressive rollout |
| Kayenta | Statistical regressions | 30-60 min | High | High-stakes deploys |

## Sources

- Canary: https://docs.flagger.app/usage/deployment-strategies
- Argo Rollouts: https://argoproj.github.io/rollouts/
- Flagger: https://docs.flagger.app/
- OpenFeature: https://openfeature.dev/
- Gateway API: https://gateway-api.sigs.k8s.io/
- Karpenter: https://karpenter.sh/docs/
- AWS Lambda Powertools: https://docs.powertools.aws.dev/lambda/python/latest/
- Cloud Run: https://cloud.google.com/run/docs
- Cloudflare Workers: https://developers.cloudflare.com/workers/
- Deno Deploy: https://deno.com/deploy
- Terratest: https://terratest.gruntwork.io/
- Checkov: https://www.checkov.io/
- OPA: https://www.openpolicyagent.org/
- ArgoCD: https://argo-cd.readthedocs.io/
- Flux v2: https://fluxcd.io/docs/
- Istio: https://istio.io/latest/docs/
- Linkerd: https://linkerd.io/docs/
- Cilium: https://docs.cilium.io/
- OpenCost: https://www.opencost.io/

## Pitfalls

1. **Don't skip expand-contract for DB migrations** — old and new app versions must coexist
2. **Don't deploy without rollback plan** — always have a way back
3. **Don't skip canary analysis** — manual eyeballing doesn't catch latency regressions
4. **Don't use feature flags as permanent branches** — clean up after full rollout
5. **Don't skip smoke tests after deploy** — verify critical paths work
6. **Don't skip health checks** — liveness + readiness probes are mandatory
7. **Don't deploy on Fridays** — unless 24/7 on-call
8. **Don't use Nginx Ingress annotations for new canary** — use Gateway API
9. **Don't skip migration testing in CI** — schema drift causes outages
10. **Don't initialize all clients at module scope** — lazy-init for cold start
11. **Don't use Provisioned Concurrency + SnapStart together** — mutually exclusive on Lambda
12. **Don't connect Lambda directly to RDS** — use RDS Proxy
13. **Don't mix X-Ray SDK and OTEL layer** — they conflict
14. **Don't run Terratest against production** — use sandbox environments
15. **Don't assume CF Workers KV is strongly consistent** — use Durable Objects for strong reads
