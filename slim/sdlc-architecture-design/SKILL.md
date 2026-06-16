---
name: sdlc-architecture-design
description: "System design, C4 diagrams, API design, database schema, code architecture, ADRs, branching, dependency management. Covers DDIA patterns, fitness functions, DDD, platform engineering, serverless, edge computing, resilience, auth/authz, event-driven architecture, CQRS, CDC, data mesh, microservices decomposition."
version: 4.9.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, architecture, c4, api-design, database, clean-architecture, ddd, serverless, edge, resilience, event-sourcing, cqrs, microservices]
    related_skills: [sdlc-requirements-engineering, sdlc-cicd-pipeline, architecture-blueprint, api-design, sdlc-deployment]
---

---
name: sdlc-architecture-design
description: "System design, C4 diagrams, API design, database schema, code architecture, ADRs, branching, dependency management. Covers DDIA patterns, fitness functions, DDD, platform engineering, serverless, edge computing, resilience, auth/authz, event-driven architecture, CQRS, CDC, data mesh, microservices decomposition."
version: 4.9.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, architectu
## When to Use
Trigger when user:
- Designs system architecture or draws C4 diagrams
- Designs REST/GraphQL/gRPC APIs
- Creates database schemas or migrations
- Chooses architecture patterns (Clean, Hexagonal, DDD)
- Sets up branching strategy or code review workflow
- Writes Architecture Decision Records
## Step 1: C4 Diagrams
4 hierarchical levels — each zooms into element from level above:
| Level | Shows | Audience |
|-------|-------|----------|
| Context | System as box, actors connected | Everyone |
| Container | Apps/services inside system boundary with tech labels | Devs, ops |
| Component | Classes/packages/modules fulfilling responsibilities | Devs |
| Code | UML class diagram (rarely needed for entire system) | Devs |
**Key rules:** Don't mix abstraction levels. Supplement with arc42 doc template.
```dsl
workspace {
  model {
    user = person "User"
    softwareSystem = softwareSystem "Platform" {
      webApp = container "Web App" "React" "TypeScript"
      api = container "API" "FastAPI" "Python"
      db = container "Database" "PostgreSQL"
    }
    user -> webApp "uses"
    webApp -> api "JSON/HTTPS"
    api -> db "SQL/TCP"
  }
}

views {
```
## Step 2: API Design
```yaml
openapi: 3.1.0
info:
  title: Order API
  version: 1.0.0
paths:
  /orders:
    get:
      summary: List orders
      parameters:
        - name: status
          in: query
          schema: { type: string, enum: [pending, shipped, delivered] }
      responses:
        '200':
          description: Order list
```
## Step 3: Database Schema Design
```sql
-- Soft delete
ALTER TABLE orders ADD COLUMN deleted_at TIMESTAMP NULL;

-- Audit trail
CREATE TABLE order_audit (
  id BIGSERIAL PRIMARY KEY,
  order_id UUID NOT NULL,
  action VARCHAR(10) NOT NULL,
  old_data JSONB,
  new_data JSONB,
  changed_at TIMESTAMP DEFAULT NOW(),
  changed_by UUID
);
```
## Step 4: Code Architecture Patterns
```
src/
  domain/          # Entities, value objects, domain services
  application/     # Use cases, DTOs, ports
  infrastructure/  # Frameworks, DB, external services
```
## Step 5: Branching Strategies
- Single `main` branch
- Short-lived feature branches (< 1 day)
- Feature flags for incomplete work
- Deploy from main continuously
```
main ← release branches
  ↑
develop ← feature branches
  ↑
hotfix branches → main
```
## Step 6: Code Review
- Small PRs (< 400 lines ideal, < 200 best)
- Template with checklist: tests, docs, breaking changes
- Required reviewers: 1-2
```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
  - repo: https://github.com/astral-sh/ruff-pre-commit
    hooks:
      - id: ruff
      - id: ruff-format
```
## Step 7: Dependency Management
```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["config:recommended"],
  "packageRules": [
    { "matchUpdateTypes": ["minor", "patch"], "automerge": true }
  ],
  "schedule": ["before 6am on Monday"]
}
```
## Step 8: Architecture Decision Records
```markdown
# ADR-001: Use PostgreSQL as primary database
## Status
Accepted — 2025-01-15
## Context
We need a relational database. Requirements: ACID, JSON support, full-text search.
## Decision
Use PostgreSQL 16 as primary database.
## Consequences
+ JSONB eliminates need for separate document store
- Requires more ops than managed NoSQL
## Alternatives Considered
- DynamoDB: rejected (poor relational queries)
- MongoDB: rejected (no ACID for multi-doc)
```

**Best practices:** One ADR per significant decision. Immutable — supersede with new ADR. Store in `docs/adr/`. Tooling: adr-tools (CLI), Log4brains (static site).
## Step 9: Architecture Fitness Functions
| Type | Description | Example |
|------|-------------|---------|
| Atomic + Triggered | Tests single characteristic on event | Layer violation check on PR |
| Atomic + Continuous | Tests single characteristic always | Performance monitoring |
| Holistic + Triggered | Tests combination on event | Load test + resilience on deploy |
| Holistic + Continuous | Tests combination always | Chaos engineering in production |
```python
# ArchUnit-style: enforce dependency rules
def test_domain_never_depends_on_infra():
    rule = no_classes().that().reside_in("..domain..").should().depend_on_classes_that().reside_in("..infra..")
    rule.check(import_classes)

# Performance fitness function in CI
def test_api_latency_p99_under_200ms():
    result = load_test("/api/orders", concurrent_users=100, duration="30s")
    assert result.p99_latency_ms < 200
```
## Step 10: Platform Engineering
Treat platform as product. Build Internal Developer Platform (IDP) with golden paths.
| Concept | Description |
|---------|-------------|
| IDP | Self-service layer abstracting infrastructure |
| Golden Paths | Pre-approved workflows for common tasks |
| Platform-as-Product | Platform team has roadmap, SLAs, internal customers |
- Software Catalog: register all services, track ownership
- Software Templates: scaffold from golden path templates
- TechDocs: docs-as-code (Markdown in repo, rendered in Backstage)
```yaml
apiVersion: score.dev/v1b1
metadata:
  name: orders-service
containers:
  main:
    image: orders:latest
    variables:
      DB_HOST: ${resources.db.host}
service:
  ports:
    http:
      port: 8080
resources:
  db:
    type: postgres
```
## Step 11: Event-Driven Architecture
| Pattern | Description |
|---------|-------------|
| Event Notification | Producer sends event, doesn't care about consumers |
| Event-Carried State Transfer | Event carries enough data, no callback needed |
| Choreography | Services react to events, no central coordinator |
| Orchestration | Central coordinator manages workflow |
**Key concepts:** eventual consistency, idempotent consumers, dead letter queues, schema registry (Avro/Protobuf)
## Step 12: CQRS + Event Sourcing
```
Commands → Write Side (Domain Model) → Events → Read Side (Projections) → Queries
```
## Step 13: Resilience Patterns
```
Closed → (failures exceed threshold) → Open → (timeout) → Half-Open → (probe succeeds) → Closed
```
## Step 14: Distributed Consensus
```
All nodes → FOLLOWER
  → election timeout (150-300ms random) → CANDIDATE → request vote
  → majority votes → LEADER → heartbeats every 50-150ms
  → leader fails → followers timeout → new election
```
## Step 15: Idempotency Patterns
```python
def handle_payment(request):
    idempotency_key = request.headers["Idempotency-Key"]
    existing = db.query("SELECT response FROM idempotency_keys WHERE key = ?", idempotency_key)
    if existing:
        return JSONResponse(existing.response, status=200)

    db.execute("INSERT INTO idempotency_keys (key, status) VALUES (?, 'processing')", idempotency_key)
    try:
        result = process_payment(request.body)
        db.execute("UPDATE idempotency_keys SET status='complete', response=? WHERE key=?",
                   json.dumps(result), idempotency_key)
        return JSONResponse(result)
    except Exception as e:
        db.execute("DELETE FROM idempotency_keys WHERE key=?", idempotency_key)
        raise
```
## Step 16: OAuth2/OIDC
```
Browser → Auth Server: GET /authorize?response_type=code&code_challenge=SHA256(verifier)&...
Auth Server → Browser: redirect with AUTH_CODE
Browser → Auth Server: POST /token with code_verifier
Server verifies SHA256(code_verifier) == stored code_challenge
```
## Step 17: JWT Best Practices
| Algorithm | Type | Use Case |
|-----------|------|----------|
| HS256 | Symmetric | Single service, same team controls issuer/verifier |
| RS256 | Asymmetric | Multiple services verify, public key distribution |
| ES256 | Asymmetric (ECDSA) | Same as RS256 but more efficient |
**Recommendation:** RS256 for microservices. Distribute public key via JWKS endpoint.
| Storage | XSS | CSRF | Recommended |
|---------|-----|------|-------------|
| localStorage | Vulnerable | Safe | No |
| HttpOnly cookie | Immune | Vulnerable (mitigate with SameSite) | Yes |
| In-memory (JS) | Window only while script runs | Safe | Yes (SPA + silent refresh) |
**Refresh token rotation:** client sends refresh_token_1 → receives new access_token + refresh_token_2. If refresh_token_1 reused → ALL tokens for user invalidated.
## Step 18: Authorization Patterns
```sql
CREATE TABLE roles (id UUID PRIMARY KEY, name VARCHAR(50) UNIQUE);
CREATE TABLE permissions (id UUID PRIMARY KEY, resource VARCHAR(100), action VARCHAR(50));
CREATE TABLE role_permissions (role_id UUID REFERENCES roles, permission_id UUID REFERENCES permissions);
CREATE TABLE user_roles (user_id UUID, role_id UUID);
```
## Step 19: Rate Limiting
| Algorithm | Burst | Memory | Precision | Use Case |
|-----------|-------|--------|-----------|----------|
| Token Bucket | Yes (configurable) | O(1) | High | API rate limiting |
| Sliding Window Log | No | O(n) | Exact | Low-traffic precise |
| Sliding Window Counter | Partial | O(1) | Approximate | High-traffic general |
| Leaky Bucket | No (smooths) | O(1) | Exact | Backend protection |
| Fixed Window | Yes (boundary burst) | O(1) | High | Simple per-minute |
```lua
local key = KEYS[1]
local bucket_size = tonumber(ARGV[1])
local refill_rate = tonumber(ARGV[2])
local now = tonumber(ARGV[3])
local cost = tonumber(ARGV[4])

local data = redis.call('HMGET', key, 'tokens', 'last_refill')
local tokens = tonumber(data[1]) or bucket_size
local last_refill = tonumber(data[2]) or now
local elapsed = now - last_refill
tokens = math.min(bucket_size, tokens + elapsed * refill_rate)

if tokens >= cost then
  tokens = tokens - cost
  redis.call('HMSET', key, 'tokens', tokens, 'last_refill', now)
```
## Step 20: Message Queue Comparison
| Feature | Kafka | RabbitMQ | NATS | Pulsar |
|---------|-------|----------|------|--------|
| Model | Distributed log | Message broker | Pub/sub + JetStream | Distributed log |
| Throughput | Very high | Medium | Very high | Very high |
| Latency | Low ms | Sub-ms | Sub-ms | Low ms |
| Retention | Time/size-based | Until consumed | JetStream: configurable | Tiered storage |
| Replay | Yes | No | JetStream: yes | Yes |
| Ops complexity | High | Low-Medium | Very low | High |
| Best for | Event streaming, high-throughput | Task queues, complex routing | Low-latency, IoT | Multi-tenant streaming |
**Decision guide:**
- Event sourcing / stream processing: Kafka or Pulsar
- Task queues / complex routing: RabbitMQ
- Low-latency microservices / IoT: NATS
- Simple setup, minimal ops: NATS
**Consumer groups:** max consumers = partition count. Each partition → exactly one consumer in group.
**Exactly-once:** `enable.idempotence=true` + transactional API + `isolation.level=read_committed`.
**Schema Registry:** enforce Avro/Protobuf compatibility. Modes: BACKWARD (default), FORWARD, FULL.
**Dead Letter Queues:**
```java
@KafkaListener(topics = "orders")
public void consume(ConsumerRecord<String, String> record) {
    try {
        process(record);
    } catch (Exception e) {
        kafkaTemplate.send("orders.DLQ", record.key(), record.value());
    }
}
```
## Step 21: CDC (Change Data Capture)
```
PostgreSQL WAL → Debezium connector → Kafka → Consumers
                                              ├── Search index (Elasticsearch)
                                              ├── Cache (Redis)
                                              └── Analytics (Snowflake)
```
## Step 22: Data Pipelines
| Aspect | ETL | ELT |
|--------|-----|-----|
| Transform | Before loading | After loading (in target DB) |
| Modern choice | Legacy / strict compliance | Most new systems |
| Tools | Informatica, Talend | dbt, Spark in data lake |
**Kappa preferred.** Single stream processing layer. Reprocess by replaying event log. Simpler than Lambda (one codebase).
## Step 23: Saga Pattern
| Aspect | Choreography | Orchestration |
|--------|-------------|---------------|
| Coupling | Low (events only) | Medium (orchestrator knows steps) |
| Complexity | Hard to trace | Centralized, easier to reason |
| Debugging | Distributed tracing needed | Single point of inspection |
| Testing | Hard (event chains) | Easier (mock orchestrator) |
| Good for | Simple 2-3 step sagas | Complex multi-step workflows |
| Failure handling | Each service handles own compensation | Orchestrator triggers compensations |
| Adding steps | Add event listener in new service | Add step in orchestrator |
```
OrderService ──[OrderPlaced]──▶ InventoryService
                                      ├──[ItemsReserved]──▶ PaymentService
                                      │                          ├──[PaymentProcessed]──▶ ShippingService
                                      │                          └──[PaymentFailed]──▶ InventoryService
                                      └──[ReservationFailed]──▶ OrderService [OrderCancelled]
```
## Step 24: Microservices Decomposition
1. **Identify seam** — bounded context with clear API boundary
2. **Build façade** — proxy/intercept traffic (API gateway, service mesh)
3. **Extract service** — implement new service, same contract
4. **Route traffic** — gradually shift (canary, feature flag, weighted routing)
5. **Migrate data** — dual-write or CDC-based sync
6. **Remove monolith code** — delete extracted code once 100% on new service
7. **Repeat** — next bounded context
**Anti-patterns:** big-bang rewrite, shared database between old and new, no rollback plan.
| Signal | Threshold |
|--------|-----------|
| Shared database | Any shared tables = critical |
| Synchronous chains | >3 hops = warning |
| Lock-step deployments | Any = critical |
| Cross-service joins | Any = critical |
| Temporal coupling | Deep chains = critical |
```
Weights: Shared DB table=10, Sync chain(each)=5, Shared lib=8, Lock-step=10, Cross-join=10
0-15=Healthy, 16-40=Moderate, 41-70=Distributed monolith, 71+=Monolith
```
## Step 25: Service Mesh
| Feature | Istio (Envoy) | Linkerd (Rust) | Cilium (eBPF) |
|---------|---------------|----------------|----------------|
| Resource cost | High (50-100MB/sidecar) | Low (10-20MB) | Very low (kernel) |
| mTLS | Automatic | Automatic (default) | SPIFFE-based |
| Traffic mgmt | Rich: fault injection, mirroring | Basic: retries, timeouts | Full L7 |
| Observability | Kiali, Jaeger/Zipkin | Viz dashboard, tap | Hubble (flow visibility) |
| Multi-cluster | Trust federation | Gateway linking | ClusterMesh (native) |
| Install complexity | High | Low | Medium (kernel ≥5.10) |
| Best for | Enterprise, complex routing | Simple, resource-constrained | Kernel-level perf |
Istio ambient mode eliminates sidecars. Two layers:
- **ztunnel (L4):** per-node daemon handles mTLS, L4 auth, basic telemetry
- **waypoint proxy (L7):** optional per-service Envoy proxy for L7 policies
**Benefits:** Lower resource cost, simpler debugging, gradual adoption (L4 first, add L7 where needed).
**Status:** GA in Istio 1.22+; production-ready as of 2024.
**Decision guide:**
- Simple, fast, low-resource: Linkerd
- Complex enterprise routing, multi-cluster: Istio (sidecar or ambient)
- Kernel-level performance, eBPF: Cilium
- Gradual adoption, no sidecar overhead: Istio ambient or Cilium eBPF
## Step 26: API Gateway Comparison
| Feature | Kong | Envoy | APISIX |
|---------|------|-------|--------|
| Language | Lua (Nginx) | C++ | Lua + etcd |
| Performance | High (50-100k RPS) | Very high (100k+) | Very high (100k+) |
| Plugin ecosystem | 100+ | Fewer, WASM extensible | 80+ built-in (OSS) |
| gRPC support | Native proxy + gateway | Native (designed for it) | Native + transcoding |
| GUI | Kong Manager (commercial) | No GUI | Built-in Dashboard (OSS) |
| Config store | PostgreSQL or Cassandra | xDS API (dynamic) | etcd (distributed KV) |
| Service discovery | DNS, Consul, K8s | xDS, EDS, K8s | DNS, K8s, Consul, Nacos |
| Auth plugins | Keycloak, OIDC, JWT, LDAP | JWT, RBAC, ext_authz | Keycloak, OIDC, JWT, LDAP |
| Rate limiting | Built-in (local + Redis) | Built-in (local) | Built-in (local + Redis) |
| Extensibility | Lua, Go (PDK), WASM | WASM, Lua, ext_proc | Lua, WASM, Java, Go |
| License | Apache 2.0 / Enterprise | Apache 2.0 | Apache 2.0 |
**Decision guide:**
- Enterprise support, large plugin ecosystem: Kong
- Sidecar/service mesh, low-level proxy: Envoy
- Full-featured OSS, built-in dashboard: APISIX
- gRPC-first: Envoy or APISIX
- WASM extensibility: Envoy (native), Kong and APISIX (growing)
## Step 27: Kubernetes Gateway API
Replaces Ingress. Richer, role-oriented, extensible.
```yaml
# GatewayClass — infrastructure provider
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: cilium
spec:
  controllerName: io.cilium/gateway-controller
---
# Gateway — cluster operator configures listeners
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: main-gateway
  namespace: infra
spec:
```
## Step 28: Serverless Architecture
| Platform | Max Timeout | Cold Start | Key Differentiator |
|----------|-------------|------------|-------------------|
| AWS Lambda | 15 min | 100-500ms | Broadest ecosystem |
| Cloudflare Workers | 30s | ~0ms (V8 isolates) | 300+ PoPs, zero cold start |
| Google Cloud Functions | 60 min | 100-300ms | GCP integration |
```
Event-driven or HTTP? → Yes → Bursty/low traffic? → Yes → Serverless
                                                → No → Steady-state > breakeven? → Yes → Containers
                                                                                    → No → Serverless
                 → No → Long-running (>15min)? → Yes → Containers/Step Functions
                                               → No → CPU intensive (>10GB)? → Yes → Containers
                                                                                  → No → Serverless
```
## Step 29: Edge Architecture
| Platform | Runtime | Locations | Key Differentiator |
|----------|---------|-----------|-------------------|
| Cloudflare Workers | V8 isolates | 300+ cities | KV, R2, D1, Durable Objects |
| Deno Deploy | V8 isolates (Deno) | 35+ regions | Native Deno, Web Standard APIs |
| Fastly Compute | WASM | 90+ PoPs | True isolation via WASM sandbox |
| Strategy | Description | Use Case |
|----------|-------------|----------|
| Stale-while-revalidate | Serve stale, refresh in background | High-traffic content |
| Purge-on-write | Invalidate on data mutation | E-commerce inventory |
| Tiered caching | Edge → Shield → Origin | Reduce origin load |
| Criteria | Edge Compute | Origin/Serverless |
|----------|-------------|-------------------|
| Latency | < 50ms globally | < 200ms acceptable |
| Data freshness | Eventually consistent OK | Strong consistency needed |
| CPU intensive | No (isolate limits) | Yes |
| State | Read-heavy, cached | Write-heavy, transactional |
**1. Edge-side rendering (ESR):** Render HTML at edge, personalize per user/region. Tools: Cloudflare Workers + D1, Next.js Edge Runtime.
**2. Edge caching + stale-while-revalidate:**
```
Client → Edge PoP → cache HIT? → return cached
                   → cache MISS? → origin → cache response → return
```
## Step 30: Multi-Cloud Architecture
| Approach | Complexity | Portability | When to Use |
|----------|-----------|-------------|-------------|
| Single cloud | Low | Low | Startups, specific features needed |
| Cloud-agnostic containers | Medium | Medium | Most teams |
| Active-active multi-cloud | High | High | Regulated industries, global SLAs |
| Abstraction layer (Pulumi/TF) | Medium | High | All teams — IaC portability |
- **Compute:** Docker on ECS/Cloud Run/AKS/GKE
- **Database:** PostgreSQL everywhere (RDS, Cloud SQL, Azure DB, Neon)
- **Storage:** S3-compatible (MinIO, R2)
- **Messaging:** AMQP/CloudEvents (RabbitMQ, SQS, Pub/Sub)
- **Observability:** OpenTelemetry
1. Lowest-common-denominator: miss best features of each cloud
2. Active-active across clouds: complex networking, data sync
3. Abstracting too early: wrap services before understanding needs
4. One Terraform module for all clouds: unmaintainable
## Step 31: 12-Factor App (Extended)
| # | Factor | Principle |
|---|--------|-----------|
| 1 | Codebase | One codebase, many deploys |
| 2 | Dependencies | Explicitly declare and isolate |
| 3 | Config | Store in environment variables |
| 4 | Backing services | Treat as attached resources |
| 5 | Build, release, run | Separate build and run stages |
| 6 | Processes | Stateless processes |
| 7 | Port binding | Export services via port binding |
| 8 | Concurrency | Scale out via process model |
| 9 | Disposability | Fast startup, graceful shutdown |
| 10 | Dev/prod parity | Keep environments similar |
| 11 | Logs | Treat as event streams |
| 12 | Admin processes | Run as one-off processes |
| # | Factor | Tools |
|---|--------|-------|
| 13 | API-First | OpenAPI, contract testing (Pact) |
| 14 | Telemetry | OpenTelemetry, Prometheus, Grafana |
| 15 | AuthN/AuthZ | OAuth2/OIDC, OPA, SPIFFE |
| 16 | Cost Awareness | Kubecost, Infracost, FinOps tagging |
| 17 | Supply Chain Security | SBOM (Syft/Trivy), SLSA, Sigstore |
## Step 32: Green Software
- **Energy Efficiency:** minimize energy per request
- **Carbon Awareness:** shift compute to cleaner grid times/regions
- **Hardware Efficiency:** maximize utilization, extend lifespan
```yaml
# Shift batch workloads to low-carbon windows
# Use carbon-aware scheduler (e.g., KEDA + Carbon Aware SDK)
triggers:
  - type: carbon-intensity
    carbonIntensityThreshold: 200  # gCO2/kWh
    region: westeurope
```
## Step 33: API Governance
```yaml
X-Request-ID: uuid-v4        # unique per request, generated at edge
X-Correlation-ID: uuid-v4    # groups related requests (saga, batch)
X-Trace-ID: hex-128          # OpenTelemetry trace ID
```
## Step 34: Service Discovery
| Feature | Client-Side | Server-Side | DNS-Based |
|---------|------------|-------------|-----------|
| How it works | Client queries registry, picks instance | LB queries registry, routes | Client resolves DNS to IP |
| Examples | Eureka, Consul client | AWS ALB, Nginx+Consul | K8s CoreDNS, Consul DNS |
| Load balancing | Client-side (Ribbon) | Server-side (LB algorithm) | DNS round-robin (limited) |
| Failover | Fast (cached list, immediate retry) | Medium (LB health check interval) | Slow (DNS TTL, 30-60s) |
| Protocol support | Any (app-level) | L4 (TCP) or L7 (HTTP/gRPC) | L3 (IP only) |
| Best for | Microservices, polyglot | Most production | Simple setups, K8s native |
```yaml
apiVersion: v1
kind: Service
metadata:
  name: order-service
spec:
  selector:
    app: order
  ports:
    - port: 8080
# DNS: order-service.default.svc.cluster.local
```
## Step 35: Error Handling
```json
{
  "type": "https://api.example.com/errors/out-of-stock",
  "title": "Out of Stock",
  "status": 409,
  "detail": "Item SKU-12345 is out of stock",
  "instance": "/orders/abc-123"
}
```
## Step 36: Data Mesh
1. **Domain Ownership:** teams own data pipelines end-to-end
2. **Data as a Product:** discoverable, addressable, trustworthy, self-describing
3. **Self-Serve Platform:** infra-as-code for data products
4. **Federated Computational Governance:** global policies enforced computationally
- "Data Mesh = New Central Team" — central platform team only
- "All Data Must Be Mesh" — only expose products, keep scratch internal
- "No Platform Investment" — invest in self-serve platform first
- "Premature Mesh" — for 10+ domain teams; simpler for smaller orgs
```
┌──────────────────────────────────────────────────┐
│                 Data Product                      │
├──────────────────────────────────────────────────┤
│  Input Ports (consume) │ Output Ports (expose)   │
│  Data Pipeline         │ Metadata (schema, lineage)│
│  SLA Contract          │ Infrastructure           │
└──────────────────────────────────────────────────┘
```
## Step 37: K8s Operator Patterns
```go
func (r *MyReconciler) Reconcile(ctx context.Context, req ctrl.Request) (ctrl.Result, error) {
    cr := &v1alpha1.MyApp{}
    if err := r.Get(ctx, req.NamespacedName, cr); err != nil {
        return ctrl.Result{}, client.IgnoreNotFound(err)
    }

    deploy := &appsv1.Deployment{}
    err := r.Get(ctx, types.NamespacedName{Name: cr.Name, Namespace: cr.Namespace}, deploy)

    if apierrors.IsNotFound(err) {
        deploy = r.buildDeployment(cr)
        if err := r.Create(ctx, deploy); err != nil {
            return ctrl.Result{}, err
        }
    } else if err == nil {
```
## Pitfalls (Consolidated)
1. **Don't start with microservices** — modular monolith first
2. **Don't design API without OpenAPI/SDL first**
3. **Don't use GitFlow for SaaS** — trunk-based + feature flags
4. **Don't skip EXPLAIN ANALYZE** — index problems compound
5. **Don't review style manually** — automate lint/format
6. **Don't write ADRs after the fact** — write during decision
7. **Don't mix C4 levels** — one abstraction per diagram
8. **Don't skip fitness functions** — architecture erodes without automated checks
9. **Don't force DDD on CRUD** — DDD Lite is enough
10. **Don't use Ingress for new K8s projects** — Gateway API is standard
11. **Don't ignore carbon cost** — include in fitness functions
12. **Don't allow unstructured logging** — enforce structured JSON + correlation IDs
13. **Don't default to servers** — evaluate serverless-first
14. **Don't store JWTs in localStorage** — HttpOnly cookies or in-memory + BFF
15. **Don't use HS256 across microservices** — RS256
16. **Don't use fixed window rate limiting** — boundary bursts allow 2x limit
17. **Don't skip idempotency keys on POST endpoints**
18. **Don't poll for CDC** — use Debezium/log-based capture
19. **Don't shard prematurely** — optimize queries, add replicas first
20. **Don't ignore circuit breaker composition order** — retry wraps breaker
21. **Don't allow unbounded retries** — use retry budget (max 20%)
## Quick Reference: Pattern Selection
| Situation | Pattern |
|-----------|---------|
| Start new project | Modular monolith, Clean Architecture |
| Complex domain | DDD bounded contexts, aggregates |
| Distributed transactions | Saga (choreography <5 steps, orchestration >5) |
| Read-heavy workload | CQRS with separate read DB |
| Audit requirements | Event sourcing |
| Legacy migration | Strangler Fig |
| Cross-service sync | CDC (Debezium) + Kafka |
| Rate limiting | Token bucket or sliding window counter |
| Authentication | OAuth2 + PKCE, BFF pattern |
| Authorization | RBAC (coarse) + ReBAC (fine-grained) |
| Service-to-service | Service mesh (Linkerd/Cilium/Istio) |
| External traffic | API Gateway (Kong/APISIX/Envoy) |
| Latency-sensitive | Edge compute (Cloudflare Workers) |
| Bursty workloads | Serverless (Lambda/Cloud Functions) |
| Multi-cloud | Containers + PostgreSQL + S3-compatible + Terraform |
## Related Skills
  - [sdlc-prd-to-production](sdlc-prd-to-production): End-to-end workflow: PRD → design doc → implementation → code review → testing → deployment → monito
  - [sdlc-deployment](sdlc-deployment): Deployment strategies: canary, blue-green, rolling, progressive delivery, feature flags, rollback, d
  - [sdlc-platform-engineering](sdlc-platform-engineering): Platform engineering: internal developer portals (IDP), Backstage, golden paths, service catalog, se