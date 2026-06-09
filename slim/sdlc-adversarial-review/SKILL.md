---
name: sdlc-adversarial-review
description: "Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, orchestrator synthesizes findings and applies fixes. Includes DORA metrics, SLSA supply chain, threat modeling (STRIDE/DREAD), secure code patterns, compliance (SOC2/GDPR/HIPAA), OWASP API Top 10, container/IaC security, policy as code, zero trust."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, code-review, pr-review, adversarial, multi-agent, security, architecture, dora, slsa, supply-chain, threat-modeling, compliance, owasp-api, zero-trust]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, github-code-review, github-pr-workflow]
---

# Adversarial PR Review

3-agent parallel review: architecture + security + quality reviewers run in parallel, orchestrator synthesizes and fixes.

## When to Use / Not Use

**Use:** PR before merge, thorough code review needed, significant refactors, security-sensitive changes.
**Skip:** Trivial changes (typos, version bumps), draft PRs, hotfixes needing immediate merge.

## Step 0: Automated Pre-Review

```bash
semgrep --config=auto --severity=ERROR --severity=WARNING .
gh codeql analyze --language=javascript
trivy fs --scanners vuln,secret,misconfig .
```

**Tool selection matrix:**

| Tool | Speed | Depth | Best for |
|------|-------|-------|----------|
| Semgrep | Fast | Medium | Security patterns, banned APIs |
| CodeQL | Slow | Deep | Cross-function taint analysis |
| Trivy | Fast | Medium | Dependency CVEs, secrets, IaC |

### SLSA Supply Chain Review

| SLSA Level | Requirement | Review Action |
|------------|-------------|---------------|
| L1 | Provenance exists | Verify build provenance attestation present |
| L2 | Hosted build platform | Verify builds run on hosted CI (not dev machines) |
| L3 | Hardened builds | Verify isolated, ephemeral build environments |

```bash
gh attestation verify <artifact> --owner <org>
syft dir:. -o spdx-json > sbom.spdx.json
grype sbom:sbom.spdx.json
cosign verify --certificate-identity=<workflow> \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com <image>
```

**Supply chain checklist:**
- [ ] SBOM present and up-to-date (SPDX or CycloneDX)
- [ ] All dependencies pinned by hash, not mutable tags
- [ ] Build provenance attestation exists (SLSA L1+)
- [ ] No unpinned GitHub Actions (use `@sha256` not `@v1`)
- [ ] No `curl | bash` install patterns
- [ ] Lockfile committed and verified in CI
- [ ] Container images signed (Sigstore/cosign)

## Step 1: Spawn 3 Reviewers in Parallel

```python
tasks = [
    {
        "goal": "Review PR for architecture concerns",
        "context": """Focus on:
1. Design — right abstraction? Right pattern? (Clean/Hexagonal/DDD)
2. Dependencies — point inward? No circular deps?
3. Module boundaries — respected? Interfaces deep?
4. ADRs followed? If new pattern, flag for ADR creation.""",
        "toolsets": ["terminal", "file"]
    },
    {
        "goal": "Review PR for security vulnerabilities",
        "context": """Focus on (OWASP Top 10 + supply chain):
1. Injection vectors — SQL, NoSQL, OS command
2. Auth issues — broken auth, session management
3. Secrets exposure — hardcoded keys, tokens
4. Input validation — all user input sanitized? SSRF prevention?
5. XSS/CSRF prevention
6. Rate limiting on auth/expensive endpoints
7. Dependency vulnerabilities — CVEs""",
        "toolsets": ["terminal", "file"]
    },
    {
        "goal": "Review PR for code quality",
        "context": """Focus on (Google review priority order):
1. Functionality — does this do what user needs? Edge cases?
2. Complexity — is code more complex than needed?
3. Tests — present, correct, maintainable?
4. Naming — clear, descriptive, consistent?
5. Comments — explain WHY, not WHAT
6. Error handling — comprehensive? Graceful degradation?
7. Performance — N+1 queries, blocking I/O?""",
        "toolsets": ["terminal", "file"]
    }
]
# Run all 3 in parallel via delegate_task(tasks=tasks)
```

## Step 2: Orchestrator Synthesizes

1. Collect all 3 review reports
2. Deduplicate findings
3. Prioritize: critical > high > medium > low
4. Cross-reference: architecture finding may explain security finding
5. Apply DORA metrics: flag if PR >400 LOC (break up), >1 day open (unblock)

## Step 3: Apply Fixes

- Auto-fixable (lint, format, simple refactor) → fix and commit
- Needs human judgment → flag with comment
- Architectural → create issue for follow-up

## Step 4: Re-run CI

After fixes, push and verify CI passes. All automated checks must pass before merge.

## Multi-Agent Architecture

```
PR opened → Orchestrator Agent
  1. Run automated checks (Semgrep, CodeQL, Trivy)
  2. Spawn 3 reviewers in parallel
  3. Collect findings
  4. Deduplicate + prioritize
  5. Apply auto-fixes
  6. Present unified review
      │           │           │
  ┌─────▼──┐  ┌─────▼──┐  ┌─────▼──┐
  │ Arch   │  │ Sec    │  │ Qual   │
  │ Review │  │ Review │  │ Review │
  └────────┘  └────────┘  └────────┘
```

## Review Templates

### Architecture Review
- [ ] Follows established patterns (Clean/Hexagonal/DDD)
- [ ] Dependencies point inward
- [ ] Module boundaries respected
- [ ] Interfaces are deep (high leverage)
- [ ] No circular dependencies
- [ ] ADRs followed (or new ADR created)

### Security Review
- [ ] No hardcoded secrets
- [ ] Input validation on all user input
- [ ] Auth checks on all endpoints
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (output encoding)
- [ ] CSRF protection (tokens)
- [ ] Rate limiting on auth/expensive endpoints
- [ ] No SSRF or path traversal vectors

### Quality Review
- [ ] Test coverage meets threshold (>80%)
- [ ] Edge cases tested
- [ ] Error handling comprehensive
- [ ] Naming descriptive and consistent
- [ ] Performance considered (no N+1, no blocking I/O)
- [ ] Comments explain WHY, not WHAT
- [ ] PR size <400 LOC (or justified)
- [ ] Documentation updated

## DORA Velocity Metrics

| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| PR to first review | <4 hours | <1 day | <1 week | >1 week |
| PR review to merge | <1 day | <3 days | <1 week | >1 week |
| PR size | <400 LOC | <800 LOC | <1500 LOC | >1500 LOC |
| PR lifetime | <1 day | <3 days | <1 week | >1 week |

**Key findings:** Elite performers review in hours. Small batch size enables fastest review. AI for review/testing outperforms AI for code generation alone.

## Code Review Culture

**Google reviewer priorities:** Design → Functionality → Complexity → Tests → Naming → Comments → Style → Nits
**Speed:** Respond within 4 hours. Never let PR sit >1 business day.
**Anti-patterns:** Bikeshedding, rubber stamping, slow reviews, NIT overload, design-by-committee.

## PR Template
```markdown
## What [1-2 sentence description]
## Why [Link to issue, business context]
## How [Technical approach, key decisions]
## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
## Checklist
- [ ] PR <400 LOC (or justified)
- [ ] No hardcoded secrets
- [ ] Error handling comprehensive
```

## Pitfalls

1. Don't run on every PR — use for significant changes (>100 LOC, security-sensitive)
2. Don't auto-fix architecture issues — create issues
3. Don't ignore medium/low findings — accumulate into tech debt
4. Don't skip re-running CI after fixes
5. Don't bikeshed — focus on logic, not style
6. Don't rubber stamp — actually read the diff
7. Don't let PRs sit — respond within 4 hours
8. Don't review for >60 minutes — fatigue degrades quality
9. Don't skip automated checks — machines handle mechanical, humans handle design

## AI-Assisted Review

| Tool | Focus |
|------|-------|
| CodeRabbit | Line-by-line review, bug detection |
| Sourcery | Python-focused, inline suggestions |
| GitHub Copilot PR review | Native GitHub integration |

**AI guardrails:** Use AI for first-pass review, test generation, security pattern detection. Don't use AI for final architectural decisions or security-critical approval. Track acceptance rate — low = tool mismatch, high = rubber stamping.

---

## STRIDE Threat Modeling

| DFD Element | S (Spoofing) | T (Tampering) | R (Repudiation) | I (Info Disclosure) | D (DoS) | E (EoP) |
|-------------|:---:|:---:|:---:|:---:|:---:|:---:|
| External Entity | ✓ | | ✓ | | | |
| Process | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Data Store | | ✓ | ✓ | ✓ | ✓ | |
| Data Flow | | ✓ | | ✓ | ✓ | |

### DREAD Risk Rating

Score each threat 1-10: **D**amage, **R**eproducibility, **E**xploitability, **A**ffected users, **D**iscoverability.

| Score | Priority | Action |
|-------|----------|--------|
| 7-10 | Critical | Fix before merge |
| 4-7 | High | Fix within sprint |
| 2-4 | Medium | Track in backlog |

**STRIDE checklist:**
- [ ] DFD created for all new services/features
- [ ] Trust boundaries identified
- [ ] Each element analyzed against STRIDE categories
- [ ] Threats scored using DREAD
- [ ] Critical/High threats mitigated before merge

## Threat Modeling Selection

| Methodology | Best For | Effort | When |
|-------------|----------|--------|------|
| STRIDE | Component-level threats | Low-Medium | Every PR touching security boundaries |
| OWASP TM | Full system threat modeling | Medium | New services, major features |
| PASTA | Risk-centric, business-aligned | High | High-risk systems, regulatory requirements |

---

## Secure Code Review

### Cryptography

| Use Case | Approved | Banned |
|----------|----------|--------|
| Symmetric | AES-256-GCM, ChaCha20-Poly1305 | DES, 3DES, RC4, AES-CBC (no HMAC) |
| Asymmetric | RSA-4096, X25519, Ed25519 | RSA-1024, RSA-2048 (new) |
| Hashing | SHA-256, SHA-3, BLAKE2 | MD5, SHA-1 |
| Key derivation | Argon2id, scrypt, PBKDF2 (>600k) | PBKDF2 (<100k), plain hash |
| TLS | TLS 1.3 (preferred), TLS 1.2 (min) | TLS 1.0/1.1, SSLv3 |

**Crypto checklist:**
- [ ] No hardcoded keys, IVs, salts, or passwords
- [ ] Authenticated encryption (AEAD) for all symmetric encryption
- [ ] Key rotation mechanism exists
- [ ] No custom crypto (use established libraries)
- [ ] Constant-time comparison for MACs/tokens (`hmac.compare_digest`)

```python
# BAD: ECB mode reveals patterns
cipher = AES.new(key, AES.MODE_ECB)
# GOOD: GCM mode (authenticated encryption)
cipher = AES.new(key, AES.MODE_GCM)

# BAD: Timing attack
if user_mac == expected_mac: pass
# GOOD: Constant-time
if hmac.compare_digest(user_mac, expected_mac): pass
```

### Authentication & Session Management

**OAuth2/OIDC checklist:**
- [ ] `state` parameter used and validated (CSRF)
- [ ] Token storage: HTTP-only secure cookies (not localStorage)
- [ ] Token refresh: rotation implemented
- [ ] Token validation: signature, issuer, audience, expiry all checked
- [ ] Redirect URIs: exact match, no wildcards
- [ ] PKCE used for public clients

**Session checklist:**
- [ ] Session ID via CSPRNG (≥128 bits entropy)
- [ ] Session ID regenerated on login (prevent fixation)
- [ ] Timeout: idle (30 min) and absolute (12h)
- [ ] Invalidated on logout (server-side deletion)
- [ ] `Secure`, `HttpOnly`, `SameSite=Lax` flags

**Password checklist:**
- [ ] Hashed with Argon2id (or scrypt/PBKDF2 high cost)
- [ ] Rate limiting on login (per account + per IP)
- [ ] Account lockout with exponential backoff
- [ ] No user enumeration (same error for invalid user vs wrong password)
- [ ] MFA enforced for privileged accounts

### Input Validation

| Layer | Location | Purpose |
|-------|----------|---------|
| L1: Transport | Edge/WAF | Block known patterns |
| L2: Schema | API Gateway | Structural validation |
| L3: Business | Application | Semantic validation |
| L4: Data | Database | Integrity constraints |

**Input validation checklist:**
- [ ] All user input validated at L2 (schema) and L3 (business)
- [ ] Allowlist preferred over blocklist
- [ ] String inputs: max length enforced, UTF-8 verified
- [ ] File uploads: content-type via magic bytes, size limited
- [ ] URL inputs: scheme allowlist (https only), no `file://`/`gopher://`

```python
# SQL injection prevention
# BAD
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")
# GOOD
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))

# SSRF prevention
def validate_url(url):
    parsed = urllib.parse.urlparse(url)
    if parsed.scheme not in ('https',):
        raise ValueError("Only HTTPS allowed")
    ip = socket.getaddrinfo(parsed.hostname, None)[0][4][0]
    if ipaddress.ip_address(ip).is_private:
        raise ValueError("No private IPs allowed")
    return url
```

---

## OWASP API Security Top 10 2023

| # | Name | Fix |
|---|------|-----|
| API1 | Broken Object Level Auth (BOLA) | Per-object ownership checks |
| API2 | Broken Authentication | Rate limiting, short-lived tokens, MFA |
| API3 | Broken Object Property Auth | Explicit allowlist DTOs |
| API4 | Unrestricted Resource Consumption | Rate limiting, pagination caps |
| API5 | Function Level Authorization | RBAC on every endpoint |
| API6 | Sensitive Business Flows | CAPTCHA, behavioral analysis |
| API7 | SSRF | URL allowlist, block private IPs |
| API8 | Security Misconfiguration | Security headers, strict CORS |
| API9 | Improper Inventory | API inventory, deprecate old versions |
| API10 | Unsafe API Consumption | Validate all external responses |

### BOLA Mitigation Pattern

```python
# BAD: No ownership check
@app.get("/orders/{order_id}")
def get_order(order_id: str):
    return db.get_order(order_id)

# GOOD: Ownership check via authenticated context
@app.get("/orders/{order_id}")
def get_order(order_id: str, user = Depends(get_current_user)):
    order = db.get_order(order_id)
    if order.owner_id != user.id and user.role != "admin":
        raise HTTPException(status_code=404)  # 404, not 403
    return order
```

### Mass Assignment Prevention

```python
# BAD: User can set any field including role
@app.put("/users/me")
def update_user(data: dict, user = Depends(get_current_user)):
    db.update_user(user.id, data)

# GOOD: Explicit allowlist DTO
class UserUpdateRequest(BaseModel):
    name: str | None = None
    email: str | None = None
    # role is NOT included

@app.put("/users/me")
def update_user(data: UserUpdateRequest, user = Depends(get_current_user)):
    db.update_user(user.id, data.model_dump(exclude_unset=True))
```

---

## Compliance-Aware Code Review

### SOC 2 Type II

| Criteria | Control | Code Review Check |
|----------|---------|-------------------|
| CC6.1 | Logical access | Auth required on all endpoints, RBAC enforced |
| CC6.7 | Data transmission | TLS 1.2+ on all connections |
| CC7.1 | Vulnerability management | Dependency scanning, patching SLAs |
| CC7.2 | Monitoring | Security event logging |
| CC8.1 | Change management | PR reviews required, CI/CD gates |

**SOC 2 checklist:**
- [ ] All access changes logged with actor, target, timestamp
- [ ] Audit logs immutable (append-only, no delete API)
- [ ] Encryption at rest (AES-256) and in transit (TLS 1.2+)
- [ ] All changes through PR, no direct deploys
- [ ] Separation of duties: deployer ≠ code author

### GDPR

| Right | Article | Endpoint |
|-------|---------|----------|
| Erasure | Art. 17 | `DELETE /users/{id}/personal-data` |
| Access | Art. 15 | `GET /users/{id}/data-export` |
| Portability | Art. 20 | `GET /users/{id}/data-portability` |

**GDPR checklist:**
- [ ] Personal data identified and tagged (PII fields annotated)
- [ ] Data minimization: only collect necessary fields
- [ ] Consent recorded with timestamp, scope, version
- [ ] Data retention automated (auto-delete after period)
- [ ] No PII in logs, error messages, or analytics events

```python
# GDPR — Data erasure pattern
class UserDataService:
    def erase_user_data(self, user_id: str) -> ErasureResult:
        self._verify_data_subject(user_id)
        self.db.delete_user(user_id)
        self.search_index.remove_user(user_id)
        self.cache.invalidate(user_id)
        self.analytics.anonymize_user(user_id)
        self.third_party_processors.request_deletion(user_id)
        self.audit_log.record(action="data_erasure", subject_hash=hash(user_id))
        return ErasureResult(status="complete")
```

### HIPAA

| Rule | Requirement | Check |
|------|-------------|-------|
| Access Control | Unique user ID, emergency access, auto-logoff, encryption | All 4 implemented |
| Audit Controls | Log all PHI access (who, what, when) | Immutable and complete |
| Transmission Security | Encrypt PHI in transit | TLS 1.2+ mandatory |
| Encryption at Rest | Encrypt PHI | AES-256, KMS managed |

**HIPAA checklist:**
- [ ] All PHI fields identified and marked
- [ ] PHI access logged: user ID, timestamp, fields, purpose
- [ ] PHI never in URLs, query strings, or error messages
- [ ] Audit logs immutable, retained 6+ years
- [ ] Auto-logoff (15 min idle timeout)
- [ ] BAA verified for all third-party services processing PHI

### Compliance Selection

| Standard | Applies When | Key Focus |
|----------|-------------|-----------|
| SOC 2 | SaaS/B2B, enterprise sales | Access controls, change management |
| GDPR | EU personal data | Data subject rights, consent, erasure |
| HIPAA | US healthcare (PHI) | PHI access controls, audit logging |
| PCI DSS | Payment card data | Cardholder isolation, encryption |

---

## Supply Chain Security

### SLSA Levels

| Level | Build | Key Guarantee |
|-------|-------|---------------|
| SLSA 1 | Scripted build | Provenance available |
| SLSA 2 | Hosted CI platform | Tamper resistance |
| SLSA 3 | Hardened, isolated, ephemeral | Tamper proof |

**Case studies:** SolarWinds (2020) — compromised build system, 18,000+ orgs. npm attacks (`event-stream`, `ua-parser-js`) — lockfile + hash pinning required.

**Dependency checklist:**
- [ ] Lockfile entries with integrity hashes
- [ ] SBOM generated for every build
- [ ] Dependency vulnerability scanning in CI
- [ ] New dependency additions require security review
- [ ] No post-install scripts from untrusted packages
- [ ] Base container images pinned by digest, not tag

```bash
syft dir:. -o spdx-json > sbom.spdx.json
grype sbom:sbom.spdx.json --fail-on high
npm ci --ignore-scripts && npm audit --audit-level=high
```

---

## Container & IaC Security

### Container Scanner Comparison

| Feature | Trivy | Grype | Snyk Container |
|---------|-------|-------|----------------|
| License | Apache 2.0 | Apache 2.0 | Commercial (free tier) |
| Dockerfile lint | Yes | No | Yes |
| Secret scanning | Yes | No | Yes |
| IaC scanning | Yes | No | No |

**Dockerfile checklist:**
- [ ] Non-root user (`USER` directive)
- [ ] Base image pinned by digest, not `latest`
- [ ] Multi-stage build
- [ ] No secrets in build args or ENV
- [ ] `HEALTHCHECK` defined
- [ ] Minimal base image (distroless, alpine)

### IaC Scanner Comparison

| Feature | Checkov | tfsec | KICS |
|---------|---------|-------|------|
| Terraform | Excellent | Excellent (dedicated) | Good |
| Kubernetes | Yes | Limited | Yes |
| Custom rules | Python/Bicep | Go | Rego (OPA) |

**IaC checklist:**
- [ ] No public S3 buckets/databases without justification
- [ ] Encryption at rest for all storage
- [ ] Security groups: no `0.0.0.0/0` on non-HTTP ports
- [ ] IAM policies follow least privilege
- [ ] K8s: no privileged containers, resource limits set

```dockerfile
# BAD: Running as root, no multi-stage, unpinned base
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "server.js"]

# GOOD: Non-root, multi-stage, pinned, minimal
FROM node:20.11-alpine3.19 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --ignore-scripts && npm cache clean --force
COPY . .
RUN npm run build

FROM node:20.11-alpine3.19
RUN addgroup -g 1001 appgroup && adduser -u 1001 -G appgroup -s /bin/sh -D appuser
WORKDIR /app
COPY --from=builder --chown=appuser:appgroup /app/dist ./dist
COPY --from=builder --chown=appuser:appgroup /app/node_modules ./node_modules
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```

```hcl
# GOOD: Private with encryption
resource "aws_s3_bucket_public_access_block" "data" {
  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

```yaml
# GOOD: Restricted K8s security context
securityContext:
  runAsNonRoot: true
  readOnlyRootFilesystem: true
  allowPrivilegeEscalation: false
  capabilities:
    drop: ["ALL"]
```

---

## DevSecOps Pipeline Gates

| Phase | Gate | Tools | FAIL Criteria |
|-------|------|-------|---------------|
| Plan | Threat Model | STRIDE, OWASP Threat Dragon | Unmitigated HIGH threats |
| Code | Pre-commit | detect-secrets, trufflehog | Any secret in diff |
| Build | SCA + SAST | Trivy, Semgrep, CodeQL | CRITICAL CVE; HIGH+ SAST |
| Test | DAST + IAST | ZAP, Burp Suite | OWASP Top 10 confirmed |
| Deploy | IaC Scan | Checkov, tfsec | Public S3; root container |
| Operate | Runtime | Falco | Crypto mining; anomalous exec |

**FAIL BUILD rules:**
- **NEVER FAIL:** INFO severity, low CVEs with no exploit
- **ALWAYS FAIL:** Secrets in code, CRITICAL CVE with exploit, public storage with sensitive data, root container in prod
- **CONDITIONAL:** HIGH without exploit, MEDIUM SAST in non-critical

---

## Policy as Code

| Feature | OPA (Rego) | Kyverno | Sentinel |
|---------|-----------|---------|----------|
| Language | Rego | YAML | HCL |
| Platform | Any | Kubernetes only | HashiCorp |
| Best for | Complex multi-platform | K8s admission | Terraform governance |

```rego
# OPA — Deny containers not from approved registry
deny[msg] {
    input.request.kind.kind == "Pod"
    container := input.request.object.spec.containers[_]
    not startswith(container.image, "registry.example.com/")
    msg := sprintf("Container '%s' must use approved registry", [container.name])
}
```

**Policy as Code checklist:**
- [ ] Policies version-controlled with infrastructure code
- [ ] Policies tested before deployment
- [ ] Policy violations block PR merge
- [ ] Policy exceptions documented with approval and expiration

---

## Zero Trust Architecture

### NIST SP 800-207 Core Tenets

1. All communication secured regardless of network location
2. Access granted per-session, not persistent
3. Access determined by dynamic policy (identity, device, context)
4. Authentication and authorization dynamic and strictly enforced

### Service Identity & mTLS

| Pattern | Mechanism | Trade-off |
|---------|-----------|-----------|
| JWT propagation | Pass JWT through chain | Stateless, token size grows |
| SPIFFE/SPIRE | Workload identity separate from user | Strong, infrastructure overhead |
| OPA/Gatekeeper | Policy eval at each hop | Flexible, management complexity |

**Zero Trust checklist:**
- [ ] No implicit trust based on network location
- [ ] All inter-service calls authenticated (mTLS, JWT)
- [ ] All inter-service calls authorized (policy engine, RBAC)
- [ ] MFA enforced for all human access
- [ ] Policy engine: deny by default if unavailable (fail closed)
- [ ] Default-deny network policies in all namespaces

### mTLS Service Mesh Comparison

| Feature | Istio | Linkerd | Cilium |
|---------|-------|---------|--------|
| Data plane | Envoy (C++) | linkerd2-proxy (Rust) | eBPF + Envoy |
| Resource overhead | High (~50MB/pod) | Low (~10MB/pod) | Lowest (eBPF) |
| Best for | Complex policies | Simplicity | Performance-critical |

---

## Secret Detection & Management

### Scanner Comparison

| Feature | git-secrets | TruffleHog | detect-secrets |
|---------|------------|------------|----------------|
| Approach | Pattern (regex) | Pattern + verified | Pattern (plugins) |
| Best for | AWS repos | Comprehensive | Python repos |

**Secret detection checklist:**
- [ ] Secret scanning in CI (blocks merge)
- [ ] Pre-commit hooks installed
- [ ] Rotation procedure: if secret in history → rotate immediately

**Secret management:** No secrets in code, config, or images. Dynamic secrets preferred (Vault). Secret TTL ≤ 24h for dynamic creds. Secret access audited.

---

### OWASP Attack Surfaces

| Surface | Risk |
|---------|------|
| API endpoints | Injection, broken auth, SSRF |
| Authentication flows | Credential stuffing, session fixation |
| File uploads | Path traversal, malware upload |
| Webhooks | Forgery, SSRF |

**OWASP deliverable:** Asset inventory with classification, trust boundary diagram, attack surface register with mitigations.

---

## OWASP LLM/AI Security Top 10

| Rank | Vulnerability | Review Focus |
|------|--------------|--------------|
| LLM01 | Prompt Injection | Input separation, output filtering |
| LLM02 | Sensitive Info Disclosure | Output filtering, PII detection |
| LLM03 | Supply Chain | Model provenance, data validation |
| LLM06 | Excessive Agency | Least privilege, human approval |
| LLM07 | System Prompt Leakage | Prompt hardening, output filtering |
| LLM10 | Unbounded Consumption | Rate limiting, token budgets |

**Excessive Agency defense:**
```python
FUNCTION_PERMISSIONS = {
    "read_user_profile": {"risk": "low", "confirmation": False},
    "update_user_email": {"risk": "medium", "confirmation": True},
    "delete_user_account": {"risk": "critical", "confirmation": True, "mfa_required": True},
    "execute_sql": {"risk": "critical", "disabled": True},
}
```

**LLM checklist:**
- [ ] User input never directly concatenated with system prompt
- [ ] Output filtering: no PII leakage, no system prompt disclosure
- [ ] Tool/function calls validated before execution
- [ ] RAG source trust boundaries enforced
- [ ] Rate limiting on LLM endpoints

## Performance Review

| Context | Max Acceptable | Flag |
|---------|---------------|------|
| Hot path (API handler) | O(n log n) | O(n²) or worse |
| Database query | O(n log n) with index | Full table scan >10k rows |

```python
# BAD: O(n²) — nested loop
for i, a in enumerate(items):
    for j, b in enumerate(items):
        if i != j and a == b: ...
# GOOD: O(n) — hash set
seen = set()
for item in items:
    if item in seen: ...
    seen.add(item)

# BAD: Race condition
if not os.path.exists(path):
    with open(path, 'w') as f: f.write(data)
# GOOD: Atomic create
fd = os.open(path, os.O_CREAT | os.O_EXCL | os.O_WRONLY)
```

**Performance checklist:**
- [ ] Hot-path functions analyzed for time complexity
- [ ] No O(n²) on user-facing endpoints without justification
- [ ] N+1 queries eliminated
- [ ] Pagination on all list endpoints
- [ ] Shared mutable state protected by locks/atomics
- [ ] Timeout on all external calls
- [ ] All resources use context managers / try-finally

---

## Stacked Diffs

Instead of one large PR, create chain of small dependent diffs (~100-300 lines each). Each is one logical change, can be reviewed independently. Enables parallel review.

**Tools:** ghstack, Graphite (https://graphite.dev/), Sapling (https://sapling-scm.com/)

## Review Metrics

**Process:** Review turnaround time, total cycle time, PR size distribution, iteration count.
**Quality:** Defect escape rate, comment density, post-merge revert rate.

**Research findings (SmartBear 2024):**
- Best defect detection: patches under 400 lines
- Diminishing returns after 200-400 LOC per review
- Sweet spot: 60-90 min review sessions

## ISO 27001 Engineering Controls (A.8.25–A.8.34)

| Control | Name | Implementation |
|---------|------|----------------|
| A.8.25 | Secure dev lifecycle | SAST/DAST in CI, code review required |
| A.8.28 | Secure coding | Coding standards (CWE Top 25), input validation |
| A.8.29 | Security testing | SAST (Semgrep), DAST (ZAP), SCA (Trivy) |
| A.8.31 | Separation of envs | No prod data in dev, different access per env |
| A.8.32 | Change management | PR-based, approval gates, rollback |

**ISO 27001 audit readiness:**
- [ ] Risk register per service (asset → threat → control → residual risk)
- [ ] Threat models for all internet-facing services
- [ ] Access reviews quarterly (evidence + remediation)
- [ ] Vulnerability management: scan → triage → remediate within SLA
- [ ] Change management evidence: PR logs, approvals, deployment logs
- [ ] Incident response documented, tested annually

### Risk Assessment (5×5 Matrix)

Risk score = likelihood × impact. 1-4: low, 5-9: medium, 10-16: high, 17-25: critical.
Treatment: accept, mitigate, transfer, avoid.
