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

---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling, progressive delivery, feature flags, rollback, database migrations, zero-downtime patterns. Gateway API, OpenFeature, serverless, edge, production hardening, multi-region, DR, cost optimization, IaC testing, GitOps, service mesh, multi-tenancy, FinOps."
version: 4.9.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-gre
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
```
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
## Step 3: Canary Deployments
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
```
## Step 4: Progressive Delivery
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
```
## Step 5: Feature Flags
Vendor-agnostic API. Swap LaunchDarkly -> Unleash without changing app code.
```go
import "github.com/open-feature/go-sdk/openfeature"

openfeature.SetProvider(unleash.NewProvider(config))
client := openfeature.NewClient("myapp")

darkMode, _ := client.BooleanValue(ctx, "dark-mode", false, evalCtx)
details, _ := client.BooleanEvaluation(ctx, "new-checkout", false, evalCtx)
// details.Reason = "TARGETING_MATCH", details.Variant = "experiment-a"
```
## Step 6: Rollback Strategies
```bash
kubectl rollout history deployment/myapp
kubectl rollout undo deployment/myapp
kubectl rollout undo deployment/myapp --to-revision=3
kubectl rollout status deployment/myapp --timeout=300s
```
## Step 7: Zero-Downtime Database Migrations
```sql
-- Step 1: Expand (no downtime)
ALTER TABLE users ADD COLUMN email_normalized VARCHAR(255);
UPDATE users SET email_normalized = LOWER(email);

-- Step 2: App reads both columns during transition

-- Step 3: Contract (after app fully migrated)
ALTER TABLE users DROP COLUMN email;
ALTER TABLE users RENAME COLUMN email_normalized TO email;
```
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
```
## Step 9: Serverless Deployment — AWS Lambda
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
## Step 12: Edge Deployment
```typescript
export default {
  async fetch(request: Request, env: Env, ctx: ExecutionContext): Promise<Response> {
    const url = new URL(request.url);
    return new Response(`Hello from ${url.pathname}`);
  },
};
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
## Step 14: Production Hardening
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
```
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
```
## Step 16: Disaster Recovery
| Tier | RPO | RTO | Strategy |
|------|-----|-----|----------|
| 0 | 0 | < 1 min | Active-active, sync replication |
| 1 | < 1 min | < 15 min | Active-passive, automated failover |
| 2 | < 1 hour | < 1 hour | Cross-region backup + automated restore |
| 3 | < 24 hours | < 4 hours | Daily backups + manual restore |
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
## Step 17: Cost Optimization
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
```
## Step 18: Deployment Verification
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
```
## Step 19: Automated Rollback
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
```
## Step 20: Deployment Failure Case Studies
Dead code repurposed old feature flag. 8 servers, 1 had stale code.
**Lesson:** Remove dead code aggressively. Never reuse feature flags. Automate deployment verification across all instances.
Operator removed too many S3 index servers. Full restart required.
**Lesson:** Blast radius limits on operational commands. Incremental recovery, not all-or-nothing.
WAF rule regex caused catastrophic backtracking. Global CPU spike.
**Lesson:** Stage regex rules. Test complexity in CI. Use RE2 or timeout guards.
Engineer ran `rm -rf` on wrong directory. Backups were silently broken.
**Lesson:** Safeguards on destructive commands. Monitor backup systems.
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
## Step 23: Infrastructure-as-Code Testing
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
```
## Step 24: GitOps Patterns
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
```
## Step 25: Service Mesh
| Feature | Istio | Linkerd | Cilium |
|---------|-------|---------|--------|
| Proxy | Envoy (C++) | linkerd2-proxy (Rust) | eBPF kernel |
| Sidecar | Yes (Ambient=no) | Yes | No (sidecar-less) |
| L4 overhead | 1-3ms | <1ms | ~0ms |
| L7 policies | Yes | Yes | Yes |
| Resource cost | High | Low | Lowest |
| Observability | Kiali, Prometheus | Built-in viz | Hubble |
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
```
## Step 28: FinOps Practices
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
## Step 29: Edge Deep Dive
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
```
## Step 30: Tenant Data Isolation
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