---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling, progressive delivery, feature flags, rollback, database migrations, zero-downtime patterns. Gateway API, OpenFeature, serverless, edge, production hardening, multi-region, DR, cost optimization, IaC testing, GitOps, service mesh, multi-tenancy, FinOps."
version: 4.9.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-green, rolling, feature-flags, progressive-delivery, kubernetes, zero-downtime, gateway-api, openfeature, finops, serverless, edge-deployment, production-hardening, multi-region, disaster-recovery, iac-testing, gitops, service-mesh, multi-tenancy]
    related_skills: [sdlc-cicd-pipeline, sdlc-testing-qa, sdlc-observability]
---

# Deployment Strategies

## When to Use

Trigger when user:
- Deployment strategy, feature flags, rollback, zero-downtime DB migrations
- Health checks, multi-region, DR, cost optimization, GitOps, service mesh, IaC testing

## Strategy Comparison

| Strategy | Zero-Downtime | Instant Rollback | Infra Cost | Complexity | Best For |
|----------|--------------|-----------------|------------|------------|----------|
| Rolling | Yes | No (slow) | Low | Low | Default K8s, low-risk |
| Blue-Green | Yes | Yes | High (2x) | Medium | Critical apps, DB-schema safe |
| Canary | Yes | Fast | Low-Medium | Medium | Gradual validation |
| Feature Flags | Yes | Yes (toggle off) | Low | Medium | Decoupling deploy/release |
| Progressive | Yes | Auto | Low-Medium | High | Automated safety at scale |

**Recommended combo:** Feature flags + Argo Rollouts/Flagger for canary + automated rollback.

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

## Step 3: Canary with Gateway API (Recommended)

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

Gateway API > Nginx Ingress annotations: portable, structured CRDs, header/path/method routing natively, first-class in Flagger and Argo Rollouts.

## Step 4: Progressive Delivery

### Argo Rollouts (standalone, wider adoption)

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
```

### AnalysisTemplate

```yaml
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

**Flagger vs Argo Rollouts:** Flagger lighter, relies on service mesh, Flux-native. Argo Rollouts standalone, built-in traffic routing, richer steps.

## Step 5: Feature Flags

### OpenFeature (CNCF Standard)

```go
import "github.com/open-feature/go-sdk/openfeature"

openfeature.SetProvider(unleash.NewProvider(config))
client := openfeature.NewClient("myapp")

darkMode, _ := client.BooleanValue(ctx, "dark-mode", false, evalCtx)
```

**Providers:** LaunchDarkly, Unleash, Flagsmith, Flipt, AWS AppConfig, Azure App Config.

### Feature Flag Taxonomy

| Type | Purpose | Lifetime | Cleanup |
|------|---------|----------|---------|
| **Release** | Decouple deploy/release | Days-weeks | Mandatory after 100% |
| **Experiment** | A/B test | Weeks-months | After winner chosen |
| **Ops** | Runtime control | Long-lived | Not needed |
| **Permission** | Access control | Permanent | Not needed |

### Flag Hygiene

```yaml
flag_hygiene:
  max_age_release_flags: 60d
  max_age_experiment_flags: 120d
  max_concurrent_flags: 200
  required_fields: [owner, type, ticket, expected_removal_date]
  automated_checks: [stale_flag_detection, orphan_flag_detection]
```

## Step 6: Rollback Strategies

```bash
kubectl rollout history deployment/myapp
kubectl rollout undo deployment/myapp
kubectl rollout undo deployment/myapp --to-revision=3
kubectl rollout status deployment/myapp --timeout=300s
```

**Automated triggers:** `progressDeadlineSeconds` for K8s, Argo CD `automated.selfHeal`, Spinnaker + Kayenta canary score.

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
| gh-ost | MySQL online schema migration |

### Migration Scale Decision

| Tool | Scale | Best For |
|------|-------|----------|
| Direct ALTER | <1M rows | Small tables, off-peak |
| gh-ost | 1M-1B rows | Single-shard MySQL |
| Vitess | 1B+ rows, 100+ shards | Distributed MySQL |

### Migration Testing in CI

Run in CI: seed baseline schema -> seed test data -> run pending migrations -> test old app against new schema -> test new app -> rollback migration -> verify row counts.

## Step 8: Serverless Deployment

### AWS Lambda Powertools (Python)

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

### Cloud Run & Azure Container Apps

**Cloud Run:** `gcloud run deploy myapp --image ... --concurrency 80 --cpu 2 --memory 2Gi --max-instances 100 --min-instances 2 --cpu-boost`. Traffic split: `--no-traffic` then `--to-revisions=v2=10,v1=90`.

**Azure Container Apps:** `az containerapp create --name myapp --min-replicas 0 --max-replicas 50 --scale-rule-type azure-queue ...`

## Step 9: Cold Start & Serverless Optimization

| Runtime | Typical Cold Start | Notes |
|---------|-------------------|-------|
| Node.js | 150-300ms | Fast, large node_modules hurts |
| Python | 200-500ms | Import-heavy frameworks slow |
| Go | 50-100ms | Compiled binary, near-instant |
| Java (no SnapStart) | 3-6s | JVM class loading |
| Java (SnapStart) | 200-400ms | Lambda only |
| Rust | 10-30ms | Fastest |

**Checklist:** Prefer Go/Rust/Node.js, use ARM64, bundle/minify, lazy-init heavy clients, reuse connections, Provisioned Concurrency for p99, SnapStart for Java, min-instances > 0 on Cloud Run.

**Lazy init + connection reuse:**
```python
_db_client = None
def get_db():
    global _db_client
    if _db_client is None:
        _db_client = boto3.client('dynamodb')
    return _db_client

# Module-level session (persists across warm invocations)
import requests
session = requests.Session()  # NOT requests.get() per invocation
```

**Size targets:** <10 MB Python, <5 MB Node.js, <25 MB Java.

## Step 10: Production Hardening

### Health Check Probes

```yaml
spec:
  template:
    spec:
      containers:
      - name: myapp
        ports:
        - containerPort: 8080
        startupProbe:
          httpGet: { path: /healthz/startup, port: 8080 }
          periodSeconds: 5
          failureThreshold: 30    # 150s max startup
          timeoutSeconds: 3
        livenessProbe:
          httpGet: { path: /healthz/live, port: 8080 }
          periodSeconds: 10
          failureThreshold: 3     # 30s fail -> restart
          timeoutSeconds: 5
        readinessProbe:
          httpGet: { path: /healthz/ready, port: 8080 }
          periodSeconds: 5
          failureThreshold: 3     # 15s fail -> removed from endpoints
          timeoutSeconds: 3
```

**Probe rules:**
- Startup: block on init (migrations, cache warmup)
- Liveness: NO external deps (DB down ≠ restart). Only detect hangs.
- Readiness: check external deps. Failure = remove from LB, not restart.

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

**Shutdown sequence:** Pod marked -> removed from endpoints -> preStop (sleep 5s) -> SIGTERM -> drain -> SIGKILL.

### Resource Limits and PDB

```yaml
resources:
  requests: { cpu: "250m", memory: "256Mi" }
  limits: { cpu: "1000m", memory: "512Mi" }
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

**Rules:** Memory limits = 1.5-2x request. Consider removing CPU limits for latency-sensitive services.

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

## Step 11: Multi-Region Deployment

| Pattern | Latency | Consistency | Cost | Best For |
|---------|---------|-------------|------|----------|
| Active-active | Lowest | Eventual/tunable | Highest | Global SaaS, gaming |
| Active-passive | Higher | Strong (sync) | Medium | Enterprise, DR |
| Follow-the-sun | Varies | Eventual | 0.7-1x | Timezone patterns |
| Global LB + single | Medium | Strong | Low | Single-market |

| Database | Conflict Resolution | Latency |
|----------|---------------------|---------|
| CockroachDB | Serializable, automatic | 50-200ms cross-region |
| Google Spanner | External consistency (TrueTime) | 50-100ms write |
| Aurora Global | Write forwarding to primary | <10ms read, primary write |
| DynamoDB Global | Last-writer-wins | <10ms local |

### Active-Passive Failover (Terraform)

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

**When to go multi-region:** Users in 3+ regions (latency > 200ms), regulatory requirements, SLA requires 99.99%+, DR mandate (RTO < 1 hour).

## Step 12: Disaster Recovery

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

### DR Testing & Failover

**Testing:** Monthly backup restoration, quarterly full failover drill, annual game day.
**Failover sequence:** Health check fails -> CloudWatch alarm (3 min) -> Lambda promotes DB replica (2-5 min) -> Route53 DNS switch (30-60s) -> Alert via SNS/PagerDuty.

## Step 13: Cost Optimization

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
      minAllowed: { cpu: 100m, memory: 128Mi }
      maxAllowed: { cpu: 2000m, memory: 2Gi }
```

**Rules:** Set requests at p50, limits at p99. Run VPA "Off" mode for 2 weeks first.

### Spot Instances (Karpenter)

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

### Spot-Eligible Checklist

- [ ] Stateless or persistent storage outside pod
- [ ] Graceful shutdown handler (SIGTERM) with < 120s drain
- [ ] Horizontal scaling (replicas > 1)
- [ ] PDB configured
- [ ] No strict latency SLA (p99 < 500ms) for critical path
- [ ] Interruption handler deployed
- [ ] Mix of instance types
- [ ] Pod anti-affinity across nodes/zones

### Reserved Capacity

| Commitment | Discount | Best For |
|-----------|----------|----------|
| 1-year no upfront | 30-40% | Stable baseline |
| 3-year all upfront | 60-70% | Long-lived core |
| Compute Savings Plan | 20-30% | Mixed workloads |
| Spot | 60-90% | Stateless, batch |

**Layered purchasing:** Base load (40%) -> Reserved. Variable (40%) -> On-Demand. Burst (20%) -> Spot.

## Step 14: Deployment Verification

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
```

**Smoke test rules:** < 30 seconds, test health + read + write + auth, fail = block promotion.

### Canary Analysis Decision Matrix

| Metric | Threshold | Action |
|--------|-----------|--------|
| Error rate | canary > baseline + 0.5% | Auto-rollback |
| P99 latency | canary > baseline * 1.1 | Auto-rollback |
| CPU/memory | canary > 80% | Auto-rollback |
| Error budget burn | > 1x rate | Pause, investigate |
| Business metric | canary < baseline * 0.95 | Auto-rollback |

### Verification Level Matrix

| Level | Catches | Latency | When |
|-------|---------|---------|------|
| K8s probes | Container crash, OOM | Instant | Always |
| Smoke tests | Broken endpoints | Minutes | After every deploy |
| Synthetics | Broken user flows | Minutes | Continuous in prod |
| Canary analysis | Latency regression | 10-30 min | Progressive rollout |

## Step 15: Infrastructure-as-Code Testing

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

### OPA/Rego Policy

```rego
package terraform.aws.s3

deny[msg] {
    resource := input.planned_values.root_module.resources[_]
    resource.type == "aws_s3_bucket"
    not startswith(resource.values.bucket, "internal-")
    msg := sprintf("S3 bucket '%s' must have 'internal-' prefix", [resource.values.bucket])
}
```

## Step 16: GitOps Patterns

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

### Sync Waves & Hooks

```yaml
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

## Step 17: Service Mesh

| Feature | Istio | Linkerd | Cilium |
|---------|-------|---------|--------|
| Proxy | Envoy (C++) | linkerd2-proxy (Rust) | eBPF kernel |
| Sidecar | Yes (Ambient=no) | Yes | No (sidecar-less) |
| L4 overhead | 1-3ms | <1ms | ~0ms |
| Resource cost | High | Low | Lowest |
| Observability | Kiali, Prometheus | Built-in viz | Hubble |

### Istio VirtualService

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
        - destination: { host: myapp, subset: v2 }
    - route:
        - destination: { host: myapp, subset: v1 }
          weight: 90
        - destination: { host: myapp, subset: v2 }
          weight: 10
      retries: { attempts: 3, perTryTimeout: 2s }
      timeout: 10s
```

## Step 18: Edge Deployment

### Edge Platform Decision Matrix

```
Feature              CF Workers       Deno Deploy      Lambda@Edge
─────────────────────────────────────────────────────────────────────
Runtime              V8 isolates      V8 isolates      Node.js/Python
Startup              ~0ms             ~0ms             50-500ms
Max CPU time         30s (paid)       30s (paid)       5s/30s
State                KV, R2, D1, DO   Deno KV          DynamoDB/S3
Global PoPs          300+             35+              220+
Vendor lock-in       Medium           Low              High
```

}
```
### Cloudflare Durable Objects

Stateful edge primitives: KV (eventual-consistent config), R2 (S3-compatible storage), D1 (SQLite), Durable Objects (stateful singletons with strong consistency), Queues.

## Step 19: Multi-Tenancy

### Namespace Isolation

Per tenant: RBAC Role (full access in namespace) + NetworkPolicy (default deny ingress, allow same-tenant namespace) + ResourceQuota (CPU, memory, pods limits).

### Tenant Data Isolation

| Pattern | Isolation | Cost | Best For |
|---------|-----------|------|----------|
| Row-level (shared schema) | Logical | Lowest | < 100 tenants |
| Schema-per-tenant | Schema | Medium | 100-1K tenants |
| Database-per-tenant | Physical | Highest | Enterprise, compliance |

## Step 20: FinOps

### Cost Allocation Labels

```yaml
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

### FinOps Maturity

| Level | Name | Capabilities | Timeline |
|-------|------|--------------|----------|
| 0 | Inform | Basic cloud billing | Day 1 |
| 1 | Showback | Kubecost, namespace allocation | Month 1-2 |
| 2 | Chargeback | Labels enforced, internal billing | Month 3-6 |
| 3 | Optimize | VPA rightsizing, spot, reserved | Month 6-12 |
| 4 | Automate | Auto-rightsizing, Karpenter, cost alerts | Month 12+ |

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

## Sources

Flagger: https://docs.flagger.app/ | Argo Rollouts: https://argoproj.github.io/rollouts/ | OpenFeature: https://openfeature.dev/ | Gateway API: https://gateway-api.sigs.k8s.io/ | Karpenter: https://karpenter.sh/docs/ | ArgoCD: https://argo-cd.readthedocs.io/ | Flux: https://fluxcd.io/docs/ | Istio: https://istio.io/latest/docs/ | Cilium: https://docs.cilium.io/
