---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling, progressive delivery (Flagger/Argo Rollouts), feature flags (LaunchDarkly/Unleash/OpenFeature), rollback, database migrations, zero-downtime patterns."
version: 2.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-green, rolling, feature-flags, progressive-delivery, flagger, argo-rollouts, kubernetes, zero-downtime]
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
# Nginx Ingress canary annotation
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/canary: "true"
    nginx.ingress.kubernetes.io/canary-weight: "10"
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

## Step 5: Feature Flags

Decouple deploy from release. Ship code with flags OFF. Toggle ON at runtime.

### OpenFeature (CNCF Standard)
Vendor-agnostic API for feature flags.

```go
client := openfeature.NewClient("myapp")
value, _ := client.BooleanValue(context.Background(), "dark-mode", false, evalCtx)
```

**Providers:** LaunchDarkly, Unleash, Flagsmith, Flipt, CloudBees, Split

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

## Pitfalls

1. **Don't skip expand-contract for DB migrations** — old and new app versions must coexist
2. **Don't deploy without rollback plan** — always have a way back
3. **Don't skip canary analysis** — manual eyeballing doesn't catch latency regressions
4. **Don't use feature flags as permanent branches** — clean up flags after full rollout
5. **Don't skip smoke tests after deploy** — verify critical paths work
6. **Don't mix blue-green with 2x DB cost** — use expand-contract pattern
7. **Don't skip health checks** — liveness + readiness probes are mandatory
8. **Don't deploy on Fridays** — unless you have 24/7 on-call
