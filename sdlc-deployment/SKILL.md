---
name: sdlc-deployment
description: "Deployment strategies: canary, blue-green, rolling. Feature flags, rollback strategies, Argo Rollouts, Flagger, K8s deployments. Includes Netflix chaos engineering and progressive delivery."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, deployment, canary, blue-green, feature-flags, kubernetes, argo, rollback, netflix, chaos]
    related_skills: [sdlc-cicd-pipeline, sdlc-observability, sdlc-testing-qa]
---

# Deployment Strategies & Feature Flags

Progressive delivery: canary, blue-green, rolling deployments, feature flags, rollback strategies. Includes Netflix chaos engineering and SV best practices.

## When to Use

Trigger when user:
- Deploys to production (canary, blue-green, rolling)
- Sets up feature flags
- Implements rollback strategies
- Configures Argo Rollouts, Flagger, or Spinnaker
- Needs zero-downtime deployment

## Step 1: Canary Deployment

Route small % traffic (1-5%) to new version, monitor, gradually increase.

### Argo Rollouts (K8s)
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
      - setWeight: 5
      - pause: { duration: 5m }
      - setWeight: 25
      - pause: { duration: 10m }
      - setWeight: 50
      - pause: { duration: 10m }
      - setWeight: 100
      analysis:
        templates:
        - templateName: success-rate
        startingStep: 1
```

### Argo Rollouts CLI
```bash
kubectl argo rollouts get rollout myapp --watch
kubectl argo rollouts promote myapp
kubectl argo rollouts abort myapp
```

## Step 2: Blue-Green Deployment

Two identical environments, swap traffic atomically.

### K8s Service Selector Swap
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
    version: blue  # Switch to 'green' for cutover
```

```bash
# Cutover
kubectl patch service myapp -p '{"spec":{"selector":{"version":"green"}}}'

# Rollback (instant)
kubectl patch service myapp -p '{"spec":{"selector":{"version":"blue"}}}'
```

## Step 3: Rolling Deployment

K8s default strategy.
```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 0
  minReadySeconds: 10
```

```bash
kubectl rollout status deployment/myapp
kubectl rollout undo deployment/myapp
kubectl rollout history deployment/myapp
```

## Step 4: Feature Flags

### Unleash (Open Source)
```bash
docker run -p 4242:4242 unleashorg/unleash-server
```

```javascript
import { initialize } from 'unleash-client';

const unleash = initialize({
  url: 'https://unleash.example.com/api',
  appName: 'myapp',
  customHeaders: { Authorization: 'token' },
});

if (unleash.isEnabled('new-checkout')) {
  // New checkout flow
}
```

### Flag Patterns
| Type | Purpose | Lifetime |
|------|---------|----------|
| Release | Hide unfinished features | Short (days) |
| Experiment | A/B testing | Medium (weeks) |
| Ops | Kill switches | Long (permanent) |
| Permission | Entitlements | Permanent |

## Step 5: Rollback Strategies

### K8s Rollback
```bash
kubectl rollout undo deployment/myapp
kubectl rollout undo deployment/myapp --to-revision=3
```

### ArgoCD Rollback
```bash
argocd app history myapp
argocd app rollback myapp REVISION
```

### Database Rollback (Expand-Contract)
```
Phase 1 (Expand):  Add new column, keep old one
Phase 2 (Migrate): Backfill data, switch reads
Phase 3 (Contract): Remove old column
```
Each phase is backward-compatible.

### Feature Flag Kill Switch
```bash
curl -X PUT http://unleash:4242/api/admin/features/new-checkout \
  -H "Authorization: $TOKEN" \
  -d '{"enabled": false}'
```

## Step 6: Ship Workflow (from gstack)

### Ship Flow
1. Detect + merge base branch
2. Run tests
3. Review diff
4. Bump VERSION
5. Update CHANGELOG
6. Commit + push
7. Create PR

### Land-and-Deploy Flow
1. Merge PR
2. Wait for CI
3. Wait for deploy
4. Verify production health

## Step 7: Self-Healing Deploy Pipeline (from earp-kit)

### 7 Phases
1. **Recon** — classify changes
2. **Merge** — merge PR to main
3. **Self-Heal CI** — if fails: read logs, classify, fix, re-push
4. **DB Migrations** — run if part of change
5. **Terraform Apply** — if infra changes
6. **Dev Verification** — confirm healthy, run smoke tests
7. **Production** — canary → full rollout

## Step 8: "You Build It, You Run It" (from Netflix + Amazon)

### Philosophy
- No central ops team — each team owns their service
- Engineers on-call for their own services
- Autonomy + accountability = ownership

### On-Call Best Practices
- Every engineer on-call for their service
- Reasonable alert volume — < 2 pages/shift
- Runbooks for every alert
- Post-mortem for every page

## Step 9: Progressive Delivery (from SV Best Practices)

### Deploy → Release → Observe Loop
```
Deploy code → Canary (1%) → Monitor metrics →
  Success → Expand (5% → 25% → 50% → 100%)
  Failure → Auto-rollback → Post-mortem
```

### Meta Gatekeeper Pattern
```python
if gatekeeper.isEnabled("new-checkout", user, percentage=5):
    show_new_checkout(user)
else:
    show_old_checkout(user)

# Auto-rollback if error rate spikes
if gatekeeper.getMetric("new-checkout", "error_rate") > 0.01:
    gatekeeper.disable("new-checkout")
```

## Step 10: Chaos Engineering (from Netflix)

### Principles
- Build confidence in handling failures
- Introduce controlled failures in production
- Minimize blast radius

### Tools
- **Chaos Monkey** — randomly terminates instances
- **Litmus** — Kubernetes chaos
- **Gremlin** — commercial chaos platform

```yaml
# Chaos experiment: kill random pod
apiVersion: litmuschaos.io/v1alpha1
kind: ChaosEngine
spec:
  experiments:
  - name: pod-delete
    spec:
      components:
        env:
        - name: TOTAL_CHAOS_DURATION
          value: '30'
```

## Pitfalls

1. **Don't canary without metrics** — need success rate, latency, error rate
2. **Don't blue-green without DB compatibility**
3. **Don't forget minReadySeconds** — pods may not be healthy
4. **Don't create permanent feature flags** — schedule cleanup
5. **Don't rollback DB without expand-contract**
6. **Don't skip smoke tests after deploy**
7. **Don't skip chaos experiments** — test failure handling in prod
