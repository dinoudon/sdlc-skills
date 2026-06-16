---
name: sdlc-adversarial-review
description: "Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, orchestrator synthesizes findings and applies fixes. Includes DORA metrics, SLSA supply chain, AI-assisted review guardrails, threat modeling (STRIDE/ATT&CK), secure code patterns, compliance (SOC2/GDPR/HIPAA), OWASP API Top 10, container/IaC security, policy as code, zero trust, and supply chain security."
version: 4.8.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, code-review, pr-review, adversarial, multi-agent, security, architecture, dora, slsa, supply-chain, threat-modeling, compliance, owasp-api, zero-trust, policy-as-code]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, github-code-review, github-pr-workflow]
---

---
name: sdlc-adversarial-review
description: "Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, orchestrator synthesizes findings and applies fixes. Includes DORA metrics, SLSA supply chain, AI-assisted review guardrails, threat modeling (STRIDE/ATT&CK), secure code patterns, compliance (SOC2/GDPR/HIPAA), OWASP API Top 10, container/IaC security, policy as code, zero trust, and supply chain security."
version: 4.8.0-moderate
author: Dinoudon
lice
## When to Use
- Reviews a PR before merging to main
- Wants thorough code review (architecture + security + quality)
- Has significant refactors to validate
- Needs security-sensitive changes reviewed
## When NOT to Use
- Trivial changes (typos, comments, version bumps)
- Draft PRs still in progress
- Hotfixes needing immediate merge
## Step 0: Automated Pre-Review
Run automated checks first — machines handle mechanical checks, humans handle design/nuance.
```bash
# Semgrep — fast pattern-based SAST
semgrep --config=auto --severity=ERROR --severity=WARNING .

# CodeQL — deep semantic analysis (if GitHub repo)
gh codeql analyze --language=javascript

# Trivy — dependency + secrets + IaC scanning
trivy fs --scanners vuln,secret,misconfig .
```
## Step 1: Spawn 3 Reviewers in Parallel
```python
tasks = [
    {
        "goal": "Review PR for architecture concerns",
        "context": """Focus on (priority order):
1. Design — right abstraction? Right pattern? (Clean/Hexagonal/DDD)
2. Dependencies — point inward? No circular deps?
3. Module boundaries — respected? Interfaces deep (high leverage)?
4. ADRs followed? If new pattern, flag for ADR creation.
5. Architecture fitness functions — would this change break any?

Report findings with severity and suggested fix.""",
        "toolsets": ["terminal", "file"]
    },
    {
        "goal": "Review PR for security vulnerabilities",
```
## Step 2: Orchestrator Synthesizes
1. Collect all 3 review reports
2. Deduplicate findings
3. Prioritize: critical > high > medium > low
4. Cross-reference: architecture finding may explain security finding
5. Apply DORA metrics: flag if PR >400 LOC (break up), >1 day open (unblock)
## Step 3: Apply Fixes
- If auto-fixable (lint, format, simple refactor) → fix and commit
- If needs human judgment → flag with comment
- If architectural → create issue for follow-up
## Step 4: Re-run CI
After fixes, push and verify CI passes. All automated checks must pass before merge.
## Review Templates
```
- [ ] Follows established patterns (Clean/Hexagonal/DDD)
- [ ] Dependencies point inward
- [ ] Module boundaries respected
- [ ] Interfaces are deep (high leverage)
- [ ] No circular dependencies
- [ ] ADRs followed (or new ADR created)
- [ ] Architecture fitness functions pass
- [ ] C4 model consistency maintained
```
## DORA Velocity Metrics
| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| PR to first review | <4 hours | <1 day | <1 week | >1 week |
| PR review to merge | <1 day | <3 days | <1 week | >1 week |
| PR size | <400 LOC | <800 LOC | <1500 LOC | >1500 LOC |
| PR lifetime | <1 day | <3 days | <1 week | >1 week |
| Reliability (DORA 2024) | >99.99% | >99.9% | >99% | <99% |
**Key findings:** Elite performers review in hours. Small batch size enables fastest review. Documentation quality correlates with high performance (DORA 2024). AI for review/testing outperforms AI for code generation alone.
## Code Review Culture
**Google reviewer priorities (in order):**
1. Design — right approach? Right abstraction?
2. Functionality — does this do what user needs?
3. Complexity — more complex than needed?
4. Tests — present, correct, maintainable?
5. Naming — clear, descriptive
6. Comments — explain WHY, not WHAT
7. Style — automate this away
8. Nit-picks — optional, prefix with "Nit:"
**Speed:** Respond within 4 hours. Small changes (<200 LOC) review in <1 hour. Never let PR sit >1 business day.
**Anti-Patterns:**
- **Bikeshedding** — arguing about trivial things
- **Rubber stamping** — approving without reading
- **Slow reviews** — PRs sitting for days
- **NIT overload** — too many optional comments
- **Design-by-committee** — conflicting reviewer opinions
## PR Template
```markdown
## What
[1-2 sentence description]
## Why
[Link to issue, business context]
## How
[Technical approach, key decisions]
## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests pass
- [ ] Manual testing done
## Checklist
- [ ] PR <400 LOC (or justified)
- [ ] No hardcoded secrets
- [ ] Error handling comprehensive
- [ ] Comments explain WHY
```
## Multi-Agent Review Architecture
```
PR opened
    │
    ▼
┌─────────────────────────────────────┐
│  Orchestrator Agent                  │
│  1. Run automated checks (Semgrep,   │
│     CodeQL, Trivy)                   │
│  2. Spawn 3 reviewers in parallel    │
│  3. Collect findings                  │
│  4. Deduplicate + prioritize         │
│  5. Apply auto-fixes                 │
│  6. Present unified review           │
└─────────┬───────────┬───────────┬────┘
          │           │           │
    ┌─────▼──┐  ┌─────▼──┐  ┌─────▼──┐
```
## Stacked Diffs
Instead of one large PR, create chain of small dependent diffs (~100-300 lines each). Each is one logical change, can be reviewed independently. Enables parallel review.
**Tools:** ghstack (https://github.com/ezyang/ghstack), Graphite (https://graphite.dev/), Sapling (https://sapling-scm.com/)
## AI-Assisted Review
| Tool | Focus | Source |
|------|-------|--------|
| CodeRabbit | Line-by-line review, bug detection | https://coderabbit.ai/ |
| Sourcery | Python-focused, inline suggestions | https://sourcery.ai/ |
| GitHub Copilot PR review | Native GitHub integration | https://github.com/features/copilot |
**AI guardrails (DORA 2024):**
- Use AI for: first-pass review, test generation, security pattern detection, docs
- Don't use AI for: final architectural decisions, security-critical approval, blind acceptance
- Track AI suggestion acceptance rate — low = tool mismatch, high = rubber stamping
- All AI-generated code/comments need human approval before merge
- Require human reviewer for security-sensitive changes regardless of AI quality
## Pitfalls
1. **Don't run on every PR** — use for significant changes (>100 LOC, security-sensitive)
2. **Don't auto-fix architecture issues** — create issues
3. **Don't ignore medium/low findings** — accumulate into tech debt
4. **Don't skip re-running CI after fixes**
5. **Don't bikeshed** — focus on logic, not style
6. **Don't rubber stamp** — actually read the diff
7. **Don't let PRs sit** — respond within 4 hours
8. **Don't review for >60 minutes** — fatigue degrades quality
9. **Don't skip automated checks** — machines handle mechanical, humans handle design
10. **Don't mix abstraction levels** — architecture in arch review, security in security review
---
## STRIDE Threat Modeling
Systematic threat enumeration using Data Flow Diagrams (DFDs).
| Element | Symbol | Description |
|---------|--------|-------------|
| External Entity | Rectangle | Users, external systems, APIs |
| Process | Circle/rounded rect | Code that processes data |
| Data Store | Two parallel lines | Databases, files, caches |
| Data Flow | Arrow | Data movement between elements |
| Trust Boundary | Dashed line | Where privilege levels change |
| DFD Element | S (Spoofing) | T (Tampering) | R (Repudiation) | I (Info Disclosure) | D (Denial of Service) | E (Elevation of Privilege) |
|-------------|:---:|:---:|:---:|:---:|:---:|:---:|
| External Entity | ✓ | | ✓ | | | |
| Process | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Data Store | | ✓ | ✓ | ✓ | ✓ | |
| Data Flow | | ✓ | | ✓ | ✓ | |
Score each threat 1-10:
| Criterion | 1-3 (Low) | 4-6 (Medium) | 7-10 (High) |
|-----------|-----------|--------------|-------------|
| **D**amage | Minor data loss | Service degradation | Complete data loss |
| **R**eproducibility | Hard, rare conditions | Sometimes | Always |
| **E**xploitability | Expert required | Advanced user | Any user, browser |
| **A**ffected users | Few/specific | Some users | All users |
## OWASP Threat Modeling
| Asset Type | Examples | Classification |
|------------|----------|----------------|
| Data | PII, credentials, financial records | Confidentiality + Integrity |
| Services | Auth, payment, notification | Availability + Integrity |
| Infrastructure | Databases, message queues, caches | Availability |
| Code | Source, build artifacts, configs | Integrity |
| Secrets | API keys, encryption keys | Confidentiality |
| Agent Type | Motivation | Capability |
|------------|------------|------------|
| External attacker | Financial gain | Varies — script kiddie to APT |
| Malicious insider | Revenge, financial gain | High — has credentials |
| Compromised service | Lateral movement | Medium — service permissions |
| Supply chain | Backdoor, data exfiltration | High — trusted position |
| Surface | Risk |
|---------|------|
| API endpoints | Injection, broken auth, SSRF |
| Authentication flows | Credential stuffing, session fixation |
| File uploads | Path traversal, malware upload |
| Message queues | Poison messages, replay attacks |
| Webhooks | Forgery, SSRF |
- [ ] Asset inventory with classification labels
## PASTA vs STRIDE vs OWASP Selection
| Methodology | Best For | Effort | When |
|-------------|----------|--------|------|
| STRIDE | Component-level threats | Low-Medium | Every PR touching security boundaries |
| OWASP TM | Full system threat modeling | Medium | New services, major features |
| PASTA | Risk-centric, business-aligned | High | High-risk systems, regulatory requirements |
---
## Secure Code Review Patterns
| Use Case | Approved | Banned | Flag |
|----------|----------|--------|------|
| Symmetric encryption | AES-256-GCM, ChaCha20-Poly1305 | DES, 3DES, RC4, AES-CBC (no HMAC) | Non-AEAD mode |
| Asymmetric encryption | RSA-4096, X25519, Ed25519 | RSA-1024, RSA-2048 (new) | Key size < 2048 |
| Hashing | SHA-256, SHA-3, BLAKE2 | MD5, SHA-1 | Any use of MD5/SHA-1 |
| Key derivation | Argon2id, scrypt, PBKDF2 (>600k) | PBKDF2 (<100k), plain hash | Low iteration count |
| TLS | TLS 1.3 (preferred), TLS 1.2 (min) | TLS 1.0/1.1, SSLv3 | Protocol version config |
| Cert validation | Full chain + hostname verification | Disabled validation | `verify=False` |
**Crypto checklist:**
- [ ] No hardcoded keys, IVs, salts, or passwords
- [ ] IVs/nonces via CSPRNG, never reused
- [ ] Authenticated encryption (AEAD) for all symmetric encryption
- [ ] Key rotation mechanism exists and tested
- [ ] No custom crypto (use established libraries)
- [ ] Constant-time comparison for MACs/tokens (`hmac.compare_digest`)
```python
# BAD: ECB mode reveals patterns
cipher = AES.new(key, AES.MODE_ECB)
# GOOD: GCM mode (authenticated encryption)
cipher = AES.new(key, AES.MODE_GCM)

# BAD: Non-constant-time comparison
if user_mac == expected_mac:  # Timing attack
# GOOD: Constant-time comparison
if hmac.compare_digest(user_mac, expected_mac):

# BAD: Weak KDF
dk = hashlib.pbkdf2_hmac('sha256', password, salt, 1000)
# GOOD: Strong KDF
dk = hashlib.pbkdf2_hmac('sha256', password, salt, 600000)
```
## Compliance-Aware Code Review
| Criteria | Control | Code Review Check |
|----------|---------|-------------------|
| CC6.1 | Logical access | Auth required on all endpoints, RBAC enforced |
| CC6.3 | Access removal | Deprovisioning automated, access revoked on role change |
| CC6.7 | Data transmission | TLS 1.2+ on all connections, mTLS internally |
| CC7.1 | Vulnerability management | Dependency scanning, patching SLAs |
| CC7.2 | Monitoring | Security event logging, SIEM integration |
| CC8.1 | Change management | PR reviews required, CI/CD gates |
**SOC 2 checklist:**
- [ ] All access changes logged with actor, target, timestamp
- [ ] Audit logs immutable (append-only, no delete API)
- [ ] Encryption at rest (AES-256) and in transit (TLS 1.2+)
- [ ] Change management: all changes through PR, no direct deploys
- [ ] Separation of duties: deployer ≠ code author
- [ ] Vulnerability scan results gated in CI
| Right | Article | Endpoint | Review Check |
|-------|---------|----------|--------------|
| Erasure | Art. 17 | `DELETE /users/{id}/personal-data` | Cascades to all stores, < 30 days |
| Access | Art. 15 | `GET /users/{id}/data-export` | All PII, machine-readable |
| Rectification | Art. 16 | `PATCH /users/{id}/personal-data` | Propagates downstream |
| Portability | Art. 20 | `GET /users/{id}/data-portability` | JSON/CSV format |
## AI/ML Model Review
| Metric | Definition | Threshold |
|--------|-----------|-----------|
| Demographic Parity | Equal prediction rates across groups | < 10% difference |
| Equalized Odds | Equal TPR/FPR across groups | < 5% difference |
| Disparate Impact | 4/5ths rule | Ratio > 0.8 |
**Bias checklist:**
- [ ] Protected attributes identified
- [ ] Training data representativeness audited
- [ ] Fairness metrics computed across protected groups
- [ ] Proxy features identified (zip code → race correlation)
- [ ] Model cards published
- [ ] Ongoing bias monitoring in production
- [ ] Human-in-the-loop for high-stakes decisions
| Vector | Attack | Mitigation |
|--------|--------|------------|
| Direct injection | User overrides system prompt | Input/output separation, delimiters |
| Indirect injection | Malicious content in RAG docs | Content sanitization, trust boundaries |
| Jailbreaking | System prompt extraction | Prompt hardening, output filtering |
```python
def safe_llm_call(user_input: str, system_prompt: str) -> str:
    sanitized = sanitize_user_input(user_input)
    messages = [
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": f"<user_input>\n{sanitized}\n</user_input>"},
    ]
    response = llm.chat(messages=messages, max_tokens=1000, temperature=0.0)
    filtered = filter_pii(response.content)
    filtered = filter_system_prompt_leakage(filtered, system_prompt)
    return filtered
```
## Performance Review Patterns
| Context | Max Acceptable | Flag |
|---------|---------------|------|
| Hot path (API handler) | O(n log n) | O(n²) or worse |
| Background job | O(n²) acceptable | O(n³) or worse |
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
```
## OWASP API Security Top 10 2023
| # | Name | Attack Vector | Fix |
|---|------|--------------|-----|
| API1 | Broken Object Level Auth (BOLA) | Manipulate object IDs | Per-object ownership checks |
| API2 | Broken Authentication | Credential stuffing, token abuse | Rate limiting, short-lived tokens, MFA |
| API3 | Broken Object Property Auth | Mass assignment, excessive data | Explicit allowlist DTOs |
| API4 | Unrestricted Resource Consumption | No rate limits | Rate limiting, pagination caps |
| API5 | Function Level Authorization | Guess admin endpoints | RBAC on every endpoint |
| API6 | Sensitive Business Flows | Bot abuse | CAPTCHA, behavioral analysis |
| API7 | SSRF | Internal service access | URL allowlist, block private IPs |
| API8 | Security Misconfiguration | Verbose errors, CORS | Security headers, strict CORS |
| API9 | Improper Inventory | Shadow APIs | API inventory, deprecate old versions |
| API10 | Unsafe API Consumption | Trusting third-party data | Validate all external responses |
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
## Supply Chain Security
| Level | Build | Provenance | Key Guarantee |
|-------|-------|------------|---------------|
| SLSA 0 | No requirements | None | No guarantees |
| SLSA 1 | Scripted build | Unsigned provenance | Provenance available |
| SLSA 2 | Hosted CI platform | Signed by build service | Tamper resistance |
| SLSA 3 | Hardened, isolated, ephemeral | Non-falsifiable signature | Tamper proof |
**SolarWinds (2020):** Compromised build system injected backdoor. 18,000+ orgs affected.
- Build system must be isolated from source repo access
- Build process must be reproducible
- Multiple independent build pipelines for critical artifacts
**npm attacks:** `event-stream` (2018), `ua-parser-js` (2021), `colors`/`faker` (2022).
- Lockfile committed and verified in CI
- Dependencies pinned by hash
- `npm audit` run in CI with failure on high/critical
- No `preinstall`/`postinstall` from untrusted packages
**PyPI attacks:** `colorama` typosquat (2022), dependency confusion, `ctx` backdoor (2022).
- Internal packages scoped to private registry
- `pip-audit` run in CI
- Hash checking mode (`--require-hashes`)
- [ ] Lockfile entries with integrity hashes
- [ ] SBOM generated for every build (Syft, SPDX, CycloneDX)
## LLM/AI Security (OWASP LLM Top 10)
| Rank | Vulnerability | Review Focus |
|------|--------------|--------------|
| LLM01 | Prompt Injection | Input separation, output filtering |
| LLM02 | Sensitive Info Disclosure | Output filtering, PII detection |
| LLM03 | Supply Chain | Model provenance, data validation |
| LLM04 | Data/Model Poisoning | Data provenance, anomaly detection |
| LLM05 | Improper Output Handling | Output encoding, sandboxing |
| LLM06 | Excessive Agency | Least privilege, human approval |
| LLM07 | System Prompt Leakage | Prompt hardening, output filtering |
| LLM08 | Vector/Embedding Weaknesses | Source trust boundaries |
| LLM09 | Misinformation | Fact-checking, source attribution |
| LLM10 | Unbounded Consumption | Rate limiting, token budgets |
```python
FUNCTION_PERMISSIONS = {
    "read_user_profile": {"risk": "low", "confirmation": False},
    "update_user_email": {"risk": "medium", "confirmation": True},
    "delete_user_account": {"risk": "critical", "confirmation": True, "mfa_required": True},
    "execute_sql": {"risk": "critical", "disabled": True},
}

async def execute_llm_function(function_name: str, args: dict, user: User):
    perms = FUNCTION_PERMISSIONS.get(function_name, {"disabled": True})
    if perms.get("disabled"):
        raise PermissionError(f"Function {function_name} is disabled")
    if perms.get("confirmation"):
        return PendingConfirmation(function=function_name, args=args)
    return await call_function(function_name, args)
```
## Container Security Scanning
| Feature | Trivy | Grype | Snyk Container |
|---------|-------|-------|----------------|
| License | Apache 2.0 | Apache 2.0 | Commercial (free tier) |
| Speed | Fast | Fast | Medium |
| Dockerfile lint | Yes | No | Yes |
| Secret scanning | Yes | No | Yes |
| IaC scanning | Yes | No | No |
| Fix guidance | Yes | No | Yes |
| Best for | All-in-one | Fast dep scanning | Developer workflow |
```bash
trivy image --severity HIGH,CRITICAL --exit-code 1 myapp:latest
grype myapp:latest --fail-on high
snyk container test myapp:latest --severity-threshold=high
```
## IaC Security Scanning
| Feature | Checkov | tfsec | KICS |
|---------|---------|-------|------|
| Terraform | Excellent | Excellent (dedicated) | Good |
| CloudFormation | Yes | No | Yes |
| Kubernetes | Yes | Limited | Yes |
| Custom rules | Python/Bicep | Go | Rego (OPA) |
| Best for | Multi-platform | Terraform-specific | Multi-platform + OPA |
```bash
checkov -d . --framework terraform,kubernetes,cloudformation
tfsec . --minimum-severity HIGH
kics scan -p . --fail-on high
```
## Secret Detection
| Feature | git-secrets | TruffleHog | detect-secrets |
|---------|------------|------------|----------------|
| Approach | Pattern (regex) | Pattern + verified | Pattern (plugins) |
| Git integration | Hooks (pre-commit) | Full history scan | Pre-commit hook |
| Verified scanning | No | Yes (checks if live) | No |
| Best for | AWS repos | Comprehensive | Python repos |
```bash
git secrets --install && git secrets --register-aws
trufflehog git file://. --only-verified
detect-secrets scan > .secrets.baseline
```
## Policy as Code
| Feature | OPA (Rego) | Kyverno | Sentinel | Cloud Custodian |
|---------|-----------|---------|----------|-----------------|
| Language | Rego | YAML | HCL | YAML |
| Platform | Any | Kubernetes only | HashiCorp | AWS/Azure/GCP |
| Learning curve | High | Low | Medium | Low |
| Mutating webhooks | No | Yes | No | No |
| Best for | Complex multi-platform | K8s admission | Terraform governance | Cloud resource hygiene |
```rego
# OPA — Deny containers not from approved registry
deny[msg] {
    input.request.kind.kind == "Pod"
    container := input.request.object.spec.containers[_]
    not startswith(container.image, "registry.example.com/")
    msg := sprintf("Container '%s' must use approved registry", [container.name])
}
```
## Zero Trust Architecture
1. All data sources and computing services are resources
2. All communication secured regardless of network location
3. Access granted per-session, not persistent
4. Access determined by dynamic policy (identity, device, context)
5. Enterprise monitors integrity/security posture of all assets
6. Authentication and authorization dynamic and strictly enforced
7. Enterprise collects maximum info for trust decisions
| Model | Description | Complexity |
|-------|-------------|------------|
| Network-centric | Micro-segmentation, SDN/SDP | Medium |
| Identity-centric | Strong identity + MFA + device trust | Medium |
| Application-centric | Per-app access proxy (BeyondCorp) | High |
| Data-centric | Classification-driven, DLP + encryption | High |
**Zero Trust checklist:**
- [ ] No implicit trust based on network location
- [ ] All inter-service calls authenticated (mTLS, JWT)
- [ ] All inter-service calls authorized (policy engine, RBAC)
- [ ] Lateral movement prevented by network policies
- [ ] MFA enforced for all human access
- [ ] Policy engine: deny by default if unavailable (fail closed)
**SPIFFE ID:** `spiffe://trust-domain/workload-identifier`
## Secret Management
| Feature | Vault | AWS Secrets Manager | Sealed Secrets | External Secrets Operator |
|---------|-------|--------------------|----|----|
| Dynamic secrets | Yes | No (rotation only) | No | No (syncs) |
| Multi-cloud | Yes | AWS only | Any K8s | Any K8s + any provider |
| GitOps safe | No | No | Yes | Yes |
| Complexity | High | Low | Low | Medium |
| Best for | Dynamic creds, PKI | AWS-native | Simple GitOps | Multi-provider sync |
```yaml
# Vault Agent Injector
annotations:
  vault.hashicorp.com/agent-inject: "true"
  vault.hashicorp.com/role: "my-app"
  vault.hashicorp.com/agent-inject-secret-db-creds: "database/creds/my-role"
```
## Container Runtime Security
| Feature | Falco | Sysdig Secure | KubeArmor |
|---------|-------|---------------|-----------|
| Primary function | Detection (alert) | Detection + Response | Enforcement (block) |
| Kernel integration | eBPF / module | eBPF | AppArmor / BPF-LSM / SELinux |
| Enforcement | No (alert only) | Yes (kill, pause, isolate) | Yes (block at kernel) |
| Overhead | Low (2-5% CPU) | Medium (3-7%) | Low (1-3% BPF-LSM) |
| License | Apache 2.0 | Commercial | Apache 2.0 |
| Best for | Detection + audit | Full platform | Active blocking |
```yaml
# Falco rule — shell in container
- rule: Shell in Container
  condition: >
    spawned_process and container and
    proc.name in (bash, sh, zsh, dash, ksh)
  output: "Shell spawned (user=%user.name container=%container.name shell=%proc.name)"
  priority: WARNING
```
## DevSecOps Pipeline Security Gates
| Phase | Gate | Tools | FAIL Criteria |
|-------|------|-------|---------------|
| Plan | Threat Model | STRIDE, OWASP Threat Dragon | Unmitigated HIGH threats |
| Code | Pre-commit | detect-secrets, trufflehog | Any secret in diff |
| Build | SCA + SAST | Trivy, Semgrep, CodeQL | CRITICAL CVE; HIGH+ SAST |
| Test | DAST + IAST | ZAP, Burp Suite | OWASP Top 10 confirmed |
| Deploy | IaC Scan | Checkov, tfsec | Public S3; unencrypted DB; root |
| Operate | Runtime | Falco, Sysdig | Crypto mining; anomalous exec |
**FAIL BUILD summary:**
```
NEVER FAIL: INFO severity, low CVEs with no exploit, dev-only dep vulns
ALWAYS FAIL: Secrets in code, CRITICAL CVE with exploit, OWASP Top 10 confirmed,
             public storage with sensitive data, root container in prod manifest,
             unencrypted PII in IaC, zero-day (EPSS > 0.7)
CONDITIONAL: HIGH without exploit, MEDIUM SAST in non-critical, deprecated API usage
```
## OWASP SAMM Maturity Assessment
| Function | Practices |
|----------|-----------|
| Governance | Strategy & Metrics, Policy & Compliance, Education & Guidance |
| Design | Threat Assessment, Security Requirements, Security Architecture |
| Implementation | Secure Build, Secure Deployment, Defect Management |
| Verification | Architecture Assessment, Requirements Testing, Security Testing |
| Operations | Incident Management, Environment Management, Operational Management |
| Level | Name | Description |
|-------|------|-------------|
| 0 | Implicit | No formal practice |
| 1 | Initial | Basic practices in place |
| 2 | Defined | Documented and standardized |
| 3 | Managed | Measured and controlled |
| 4 | Optimized | Continuous improvement |
**SAMM checklist:**
- [ ] Assessment completed for 5 functions, 15 practices scored 0-3
- [ ] Maturity gaps identified between current and target
- [ ] Improvement roadmap created
- [ ] Re-assessment scheduled (quarterly)
---
## CSPM (Cloud Security Posture Management)
| Feature | CSPM | CWPP | CNAPP |
|---------|------|------|-------|
| Focus | Infrastructure misconfig | Runtime workload protection | Unified |
| Scope | Cloud control plane | VMs, containers, serverless | Full cloud-native |
| Detection | Config drift, compliance | Malware, exploits | Both + shift-left |
| Agent | API-based (agentless) | Agent-based | Both |
| Examples | AWS Config, Prisma Cloud | Aqua, Sysdig | Wiz, Orca |
| Misconfig | Risk | Fix |
|-----------|------|-----|
| Public S3 bucket | Data exfiltration | Block public access |
| Open Security Group (0.0.0.0/0) | Lateral movement | Restrict to known CIDRs |
| Unencrypted RDS/EBS | Data breach at rest | Enable KMS encryption |
| Over-permissive IAM (*:*) | Privilege escalation | Least privilege |
| Public IP on DB | Direct DB access | Set false, use bastion |
---
## API Security Testing
```
Autorize — Authorization testing (BOLA/BFLA)
JSON Web Tokens — JWT manipulation
Active Scan++ — Enhanced scanning
Param Miner — Hidden parameter discovery
InQL — GraphQL testing
Turbo Intruder — High-speed fuzzing
```
## Sigstore & SBOM
```bash
# Sign (keyless — uses OIDC)
cosign sign --yes ghcr.io/org/app@sha256:abc123

# Verify
cosign verify \
  --certificate-identity=user@example.com \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com \
  ghcr.io/org/app@sha256:abc123

# Attach SBOM
cosign attest --predicate sbom.spdx.json --type spdxjson \
  ghcr.io/org/app@sha256:abc123
```
## Microservices Security Architecture
| Principle | Implementation | Review Check |
|-----------|---------------|--------------|
| Verify explicitly | Auth every request with strong identity | No implicit trust from network location |
| Least privilege | JIT/JEA access | Services have minimum required permissions |
| Assume breach | Encrypt all, segment networks | Blast radius minimized by design |
| Pattern | Mechanism | Trade-off |
|---------|-----------|-----------|
| JWT propagation | Pass JWT through chain | Stateless, token size grows |
| OAuth2 token exchange | Exchange at each hop (RFC 8693) | Secure, adds latency |
| SPIFFE/SPIRE | Workload identity separate from user | Strong, infrastructure overhead |
| OPA/Gatekeeper | Policy eval at each hop | Flexible, management complexity |
**Identity propagation checklist:**
- [ ] User identity propagated to all downstream services
- [ ] Service-to-service uses workload identity (not forwarded user tokens)
- [ ] Token validation at every hop
- [ ] No hardcoded service credentials
- [ ] Token audience restriction prevents replay across services
| Layer | Tool | Review Check |
|-------|------|--------------|
| K8s NetworkPolicy | Calico, Cilium | Default-deny ingress; explicit allow per service |
| Service mesh authz | Istio AuthorizationPolicy | L7 policies (method, path, headers) |
## Advanced Threat Modeling
Hierarchical decomposition of attack goals. Root = attacker objective, leaves = exploit steps.
```
[Root Goal: Steal user data]
├── [AND] Compromise auth layer
│   ├── [OR] Credential stuffing
│   │   ├── Leaked credential databases
│   │   └── Password spray attacks
│   └── [OR] Session hijacking
│       ├── XSS-based cookie theft
│       └── Session fixation
├── [AND] Access data store
│   ├── [OR] SQL injection
│   └── [OR] SSRF to internal DB
└── [AND] Exfiltrate data
    ├── [OR] DNS tunneling
    └── [OR] Encrypted channel to C2
```
## ISO 27001 for Engineering
| Clause | Requirement | Engineering Mapping |
|--------|------------|---------------------|
| 4 | Context | Define in-scope systems, data classification |
| 5 | Leadership | Security policy in repo, CISO sign-off |
| 6 | Planning | Threat model per service, risk register |
| 7 | Support | Security training, tool budgets |
| 8 | Operation | CI/CD security gates, SAST/DAST |
| 9 | Performance | Security metrics, quarterly internal audits |
| 10 | Improvement | Post-incident reviews, maturity progression |
| Control | Name | Implementation |
|---------|------|----------------|
| A.8.25 | Secure dev lifecycle | SAST/DAST in CI, code review required |
| A.8.26 | App security requirements | Security requirements in user stories |
| A.8.27 | Secure architecture | Architecture review, defense-in-depth |
| A.8.28 | Secure coding | Coding standards (CWE Top 25), input validation |
| A.8.29 | Security testing | SAST (Semgrep), DAST (ZAP), SCA (Trivy) |
| A.8.30 | Outsourced dev | Vendor security assessment, code ownership |
| A.8.31 | Separation of envs | No prod data in dev, different access per env |
| A.8.32 | Change management | PR-based, approval gates, rollback |
| A.8.33 | Test information | No real PII/PHI in tests, synthetic data |
| A.8.34 | Test data protection | Automated scrubbing, retention limits |
## HIPAA Field-Level Encryption
```python
from cryptography.hazmat.primitives.ciphers.aead import AESGCM
import os

class PHIEncryption:
    """AES-256-GCM field-level encryption for PHI."""

    def encrypt_field(self, plaintext: str, field_name: str) -> bytes:
        field_key = self._derive_key(self._get_master_key(), field_name)
        aesgcm = AESGCM(field_key)
        nonce = os.urandom(12)
        ciphertext = aesgcm.encrypt(
            nonce, plaintext.encode("utf-8"),
            field_name.encode("utf-8"),  # AAD
        )
        return nonce + ciphertext
```
## GDPR Technical Implementation
```
Consent Management Platform (CMP):
  Purposes (10): Store/access device info, Basic ads, Personalised ads,
    Personalised content, Measurement, Improve products, Market research,
    Data sharing, Data linking, Precise geolocation

  Legal Bases: Consent (Art. 6(1)(a)), Legitimate Interest (Art. 6(1)(f))

  Consent string format: TCF v2
  Stored: first-party cookie or localStorage
```
## BeyondCorp — Google's Zero Trust
| Component | Function |
|-----------|----------|
| Device Inventory Service | Tracks all managed devices (serial, owner, patch level, EDR) |
| Access Proxy | Single entry point, TLS termination, auth, policy enforcement |
| Access Control Engine | Central policy evaluation (identity + device trust + context) |
| Continuous Evaluation | Re-evaluate sessions on signal change |
| Tier | Device Status | User Auth | Access Level |
|------|--------------|-----------|--------------|
| Full Trust | Managed, patched, encrypted, EDR active | MFA recent (<8h) | All apps |
| Partial Trust | Managed, some drift or off-network | MFA older (>8h) | Most apps, not crown jewels |
| Untrusted | Unmanaged or non-compliant | Basic auth | Public apps only, read-only |
**BeyondCorp checklist:**
- [ ] Device inventory covers all endpoints
- [ ] Access proxy is single entry point (no bypass)
- [ ] Trust tier computed dynamically, not cached indefinitely
- [ ] Continuous re-evaluation during active sessions
- [ ] Sensitive apps require highest trust tier
- [ ] No VPN dependency for internal app access
- [ ] Session termination on trust tier drop
---
## Dockerfile Security Examples
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
```
## Kubernetes Security Context Examples
```yaml
# GOOD: Restricted security context
apiVersion: v1
kind: Pod
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 1000
    seccompProfile:
      type: RuntimeDefault
  containers:
  - name: app
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
```
## Performance Profiling Tools
```bash
# Python — profiling
python -m cProfile -o profile.prof script.py
python -m snakeviz profile.prof  # Visualize

# Python — memory profiling
pip install memory_profiler
python -m memory_profiler script.py

# Python — concurrency analysis
py-spy record -o profile.svg -- python script.py

# Go — race detector
go test -race ./...

# Java — thread dump
```
## Cloud Misconfig Detection Scripts
```bash
# Find public S3 buckets
aws s3api list-buckets --query 'Buckets[].Name' --output text | while read bucket; do
  acl=$(aws s3api get-bucket-acl --bucket "$bucket" 2>/dev/null)
  if echo "$acl" | grep -q "AllUsers"; then
    echo "CRITICAL: $bucket is publicly accessible"
  fi
done

# Find security groups with 0.0.0.0/0
aws ec2 describe-security-groups \
  --filters Name=ip-permission.cidr,Values='0.0.0.0/0' \
  --query 'SecurityGroups[*].[GroupId,GroupName]' --output table
```
## Review Metrics
**Process metrics:**
- Review turnaround time (request to first response)
- Total review cycle time (request to merge)
- PR size distribution (lines changed, files touched)
- Review iteration count (comments-to-merge cycles)
**Quality metrics:**
- Defect escape rate (post-merge bugs vs during review)
- Comment density (comments per 100 lines changed)
- Post-merge revert rate
**Research findings (SmartBear 2024):**
- Best defect detection: patches under 400 lines
- Diminishing returns after 200-400 LOC per review
- Review rate >500 LOC/hour drops defect detection
- Sweet spot: 60-90 min review sessions