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

openfeature.SetProvider(unleash.NewProvider(