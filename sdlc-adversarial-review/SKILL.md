---
name: sdlc-adversarial-review
description: "Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, orchestrator synthesizes findings and applies fixes. Includes Google/Stripe/Meta code review culture, DORA velocity metrics (5 metrics incl. reliability), SLSA supply chain verification, AI-assisted review guardrails, and automated tooling integration."
version: 3.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, code-review, pr-review, adversarial, multi-agent, security, architecture, google, stripe, dora, semgrep, codeql, slsa, supply-chain, sbom, sigstore, ai-review]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, github-code-review, github-pr-workflow]
---

# Adversarial PR Review

3-agent parallel review system: architecture + security + quality reviewers run in parallel, orchestrator synthesizes and fixes. Based on Google/Microsoft/Meta/Stripe engineering practices and DORA metrics.

## When to Use

Trigger when user:
- Reviews a PR before merging to main
- Wants thorough code review (architecture + security + quality)
- Has significant refactors to validate
- Needs security-sensitive changes reviewed

## When NOT to Use

- Trivial changes (typos, comments, version bumps)
- Draft PRs still in progress
- Hotfixes needing immediate merge

## Step 0: Automated Pre-Review (Before Human Review)

Run automated checks first — machines handle mechanical checks, humans handle design/nuance.

```bash
# Semgrep — fast pattern-based SAST
semgrep --config=auto --severity=ERROR --severity=WARNING .

# CodeQL — deep semantic analysis (if GitHub repo)
gh codeql analyze --language=javascript

# Trivy — dependency + secrets + IaC scanning
trivy fs --scanners vuln,secret,misconfig .
```

**Tool selection matrix:**
| Tool | Speed | Depth | Best for |
|------|-------|-------|----------|
| Semgrep | Fast | Medium | Security patterns, code standards, banned APIs |
| CodeQL | Slow | Deep | Cross-function taint analysis, SQLi/XSS/SSRF |
| Trivy | Fast | Medium | Dependency CVEs, exposed secrets, IaC misconfig |
| cosign/Sigstore | Fast | Low | Artifact signature verification, provenance |

### SLSA Supply Chain Review (Step 0b)

Verify supply chain integrity before code review. SLSA (Supply-chain Levels for Software Artifacts) framework: https://slsa.dev/

| SLSA Level | Requirement | Review Action |
|------------|-------------|---------------|
| L0 | No guarantees | Flag as risk — no provenance |
| L1 | Provenance exists | Verify build provenance attestation present |
| L2 | Hosted build platform | Verify builds run on hosted CI (not dev machines) |
| L3 | Hardened builds | Verify isolated, ephemeral build environments |

```bash
# Verify SLSA provenance (GitHub Actions)
gh attestation verify <artifact> --owner <org>

# SBOM verification — check all dependencies are declared
# Syft generates SBOM, Grype scans it
syft dir:. -o spdx-json > sbom.spdx.json
grype sbom:sbom.spdx.json

# Sigstore/cosign — verify container/artifact signatures
cosign verify --certificate-identity=<workflow> --certificate-oidc-issuer=https://token.actions.githubusercontent.com <image>
```

**Review checklist — supply chain:**
- [ ] SBOM present and up-to-date (SPDX or CycloneDX format)
- [ ] All dependencies pinned by hash, not mutable tags
- [ ] Build provenance attestation exists (SLSA L1+)
- [ ] No unpinned GitHub Actions (use `@sha256` not `@v1`)
- [ ] No `curl | bash` install patterns
- [ ] No typosquatting-susceptible dependency names
- [ ] Lockfile committed and verified in CI
- [ ] Container images signed (Sigstore/cosign)
- [ ] No dependency confusion vectors (private registry scoped)

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

Report findings with severity and suggested fix.
Reference: C4 model for system context, hexagonal architecture for dependency direction.""",
        "toolsets": ["terminal", "file"]
    },
    {
        "goal": "Review PR for security vulnerabilities",
        "context": """Focus on (OWASP Top 10 + supply chain):
1. Injection vectors — SQL, NoSQL, LDAP, OS command
2. Auth issues — broken auth, session management, missing checks
3. Secrets exposure — hardcoded keys, tokens, passwords
4. Input validation — all user input sanitized? SSRF prevention?
5. XSS prevention — output encoding, CSP headers
6. CSRF protection — tokens on state-changing endpoints
7. Rate limiting — on auth and expensive endpoints
8. Dependency vulnerabilities — CVEs in dependencies
9. Supply chain — typosquatting, compromised packages

Report findings with severity and suggested fix.
Reference: OWASP Top 10 2025 (https://owasp.org/Top10/), CWE/SANS Top 25.""",
        "toolsets": ["terminal", "file"]
    },
    {
        "goal": "Review PR for code quality",
        "context": """Focus on (Google review priority order):
1. Functionality — does this do what user needs? Edge cases?
2. Complexity — is code more complex than needed? Over-engineering?
3. Tests — present, correct, maintainable? Coverage meets threshold?
4. Naming — clear, descriptive, consistent?
5. Comments — explain WHY, not WHAT
6. Error handling — comprehensive? Graceful degradation?
7. Performance — N+1 queries, unnecessary allocations, blocking I/O?
8. Documentation — API docs, inline docs, README updated for public changes? (DORA 2024: docs quality differentiates high performers)
9. PR description — clear what/why/how/testing sections?

Report findings with severity and suggested fix.
Reference: Google eng-practices review standards.""",
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

For each finding:
- If auto-fixable (lint, format, simple refactor) → fix and commit
- If needs human judgment → flag with comment
- If architectural → create issue for follow-up

## Step 4: Re-run CI

After fixes, push and verify CI passes. All automated checks must pass before merge.

## Review Templates

### Architecture Review
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

### Security Review
```
- [ ] No hardcoded secrets (Trivy secrets scan)
- [ ] Input validation on all user input
- [ ] Auth checks on all endpoints
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (output encoding)
- [ ] CSRF protection (tokens)
- [ ] Rate limiting on auth/expensive endpoints
- [ ] Dependency vulnerabilities checked (Trivy/Dependabot)
- [ ] No SSRF vectors
- [ ] No path traversal vectors
```

### Quality Review
```
- [ ] Test coverage meets threshold (>80%)
- [ ] Edge cases tested
- [ ] Error handling comprehensive
- [ ] Naming descriptive and consistent
- [ ] No code duplication
- [ ] Performance considered (no N+1, no blocking I/O)
- [ ] Comments explain WHY, not WHAT
- [ ] PR size <400 LOC (or justified)
- [ ] Documentation updated (inline docs, API docs, README if public interface changed)
- [ ] CHANGELOG entry added (if user-facing change)
```

## DORA Velocity Metrics

Track review velocity against DORA benchmarks:

| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| PR to first review | <4 hours | <1 day | <1 week | >1 week |
| PR review to merge | <1 day | <3 days | <1 week | >1 week |
| PR size | <400 LOC | <800 LOC | <1500 LOC | >1500 LOC |
| PR lifetime | <1 day | <3 days | <1 week | >1 week |
| Reliability (DORA 2024 5th metric) | >99.99% | >99.9% | >99% | <99% |

**Key findings from DORA research:**
- Elite performers review in hours, not days
- Long review queues correlate with lower deployment frequency
- Small batch size (small PRs) enables fastest review cycles
- WIP limits on PRs-in-review improve throughput
- **DORA 2024 adds Reliability as 5th metric** — operational reliability (SLIs/SLOs met) predicts software delivery performance; review must validate changes don't degrade reliability posture
- **Documentation quality correlates with high performance** (DORA 2024) — teams with high-quality docs ship faster with fewer defects; review should verify docs updated alongside code
- **AI-assisted review/testing yields better outcomes than AI for generation alone** (DORA 2024) — use AI tools for review, test generation, and analysis before relying on them for code generation

## Code Review Culture

### Google Engineering Practices
Source: https://google.github.io/eng-practices/review/

**Reviewer priorities (in order):**
1. Design — is this the right approach? Right abstraction?
2. Functionality — does this do what user needs? Edge cases?
3. Complexity — is code more complex than needed?
4. Tests — are tests present, correct, maintainable?
5. Naming — clear, descriptive names
6. Comments — explain WHY, not WHAT
7. Style — enforce consistent style (automate this away)
8. Nit-picks — optional, prefix with "Nit:"

**Speed expectations:**
- Respond to review requests within 4 hours
- Small changes (<200 LOC) should review in under 1 hour
- Never let PR sit unreviewed for >1 business day

**Comment conventions:**
- "Nit:" — optional, author decides
- "FYI:" — no action needed, informational
- Blocking comments — must fix before merge
- Every comment must explain WHY or provide suggestion

### Microsoft Research Findings
Source: Bacchelli & Bird, ICSE 2013

- Primary benefit of code review: **knowledge transfer**, NOT defect finding
- Reviewers spend ~60% of time understanding code, ~20% on defects
- Review fatigue is real: effectiveness drops after ~60 minutes
- Short, focused review sessions > marathon reviews
- Incremental reviews (see only new changes since last review) improve quality

### Stripe Code Review
- Knowledge-sharing — review as learning opportunity
- Substantive engagement — understand the change, don't skim
- Clear PR descriptions — what, why, how, testing

### Meta Code Review
- Ship it culture — approve quickly, don't block on nitpicks
- "Ship it" with comments — LGTM but note improvements
- Stacked diffs — break large features into dependent PR chain

### Review Anti-Patterns
- **Bikeshedding** — arguing about trivial things (naming, formatting)
- **Rubber stamping** — approving without reading
- **Slow reviews** — PRs sitting for days kills velocity
- **NIT overload** — too many optional comments blocks author
- **Design-by-committee** — too many reviewers with conflicting opinions

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

## Screenshots (if UI)
[Before/after]

## Checklist
- [ ] PR <400 LOC (or justified in description)
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
    │ Arch   │  │ Sec    │  │ Qual   │
    │ Review │  │ Review │  │ Review │
    └────────┘  └────────┘  └────────┘
```

## Advanced: Stacked Diffs

Source: https://blog.pragmaticengineer.com/stacked-diffs/ (Gergely Orosz)

Instead of one large PR, create chain of small dependent diffs stacked on each other. Each diff is one logical change.

**Meta's approach:** Small, focused diffs (~100-300 lines). Each depends on parent but can be reviewed independently. Enables parallel review while developer continues building on top.

**ghstack** (Edward Yang, PyTorch/Meta): https://github.com/ezyang/ghstack
```bash
# Creates stacked PRs from stacked commits
ghstack submit
```

**Other tools:**
- Graphite (https://graphite.dev/) — commercial stacked PR tool
- Sapling (Meta's VCS): https://sapling-scm.com/ — built-in stacking

**Key benefit:** Reviewer sees small, logical units instead of massive diffs.

## Advanced: AI-Assisted Review

| Tool | Focus | Source |
|------|-------|--------|
| CodeRabbit | Line-by-line review, bug detection | https://coderabbit.ai/ |
| Sourcery | Python-focused, inline suggestions | https://sourcery.ai/ |
| GitHub Copilot PR review | Native GitHub integration | https://github.com/features/copilot |

**Pattern:** AI handles first pass (style, obvious bugs, boilerplate). Humans focus on architecture, logic, design decisions.

### AI-Assisted Review Guardrails (DORA 2024)

DORA 2024 finding: AI for review/testing outperforms AI for code generation alone. Use AI as review multiplier, not replacement.

**Use AI for:**
- First-pass code review (style, obvious bugs, boilerplate)
- Test generation and coverage gap analysis
- Security pattern detection (SAST augmentation)
- Documentation generation and consistency checks
- Dependency vulnerability triage and prioritization

**Don't use AI for:**
- Final architectural decisions (human judgment required)
- Security-critical approval (human sign-off mandatory)
- Acceptance of AI suggestions without reading (anti-pattern: rubber stamping AI output)
- Blind trust of AI-generated tests (tests must verify behavior, not just pass)

**Guardrails:**
- All AI-generated code/review comments need human approval before merge
- Track AI suggestion acceptance rate — low rate = tool mismatch, high rate = possible rubber stamping
- AI review findings are advisory; human reviewer retains final authority
- Require human reviewer for security-sensitive changes regardless of AI review quality
- Document which AI tools were used in review (auditability)
- Validate AI suggestions against project conventions — AI may suggest idioms that conflict with codebase style

## Advanced: Review Metrics

**Process metrics:**
- Review turnaround time (request to first response)
- Total review cycle time (request to merge)
- PR size distribution (lines changed, files touched)
- Review iteration count (comments-to-merge cycles)

**Quality metrics:**
- Defect escape rate (bugs found post-merge vs during review)
- Comment density (comments per 100 lines changed)
- Post-merge revert rate

**Research findings (SmartBear 2024):**
- Best defect detection: patches under 400 lines
- Diminishing returns after 200-400 LOC per review
- Review rate >500 LOC/hour drops defect detection significantly
- Sweet spot: 60-90 min review sessions

Source: https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/

## Pitfalls

1. **Don't run on every PR** — use for significant changes (>100 LOC, security-sensitive)
2. **Don't auto-fix architecture issues** — create issues
3. **Don't ignore medium/low findings** — accumulate into tech debt
4. **Don't skip re-running CI after fixes**
5. **Don't bikeshed** — focus on logic, not style
6. **Don't rubber stamp** — actually read the diff
7. **Don't let PRs sit** — respond within 4 hours (Google SLA)
8. **Don't review for >60 minutes** — fatigue degrades quality
9. **Don't skip automated checks** — machines handle mechanical, humans handle design
10. **Don't mix abstraction levels** — architecture comments in arch review, security in security review

## STRIDE Threat Modeling (Microsoft)

Systematic threat enumeration using Data Flow Diagrams (DFDs). Source: Microsoft SDL, Adam Shostack.

### Step 1: Create Data Flow Diagram (DFD)

DFD elements and what each represents:

| Element | Symbol | Description |
|---------|--------|-------------|
| External Entity | Rectangle | Users, external systems, APIs |
| Process | Circle/rounded rect | Code that processes data |
| Data Store | Two parallel lines | Databases, files, caches |
| Data Flow | Arrow | Data movement between elements |
| Trust Boundary | Dashed line | Where privilege levels change |

### Step 2: STRIDE-per-Element Table

Map each DFD element type to applicable threat categories:

| DFD Element | S (Spoofing) | T (Tampering) | R (Repudiation) | I (Info Disclosure) | D (Denial of Service) | E (Elevation of Privilege) |
|-------------|:---:|:---:|:---:|:---:|:---:|:---:|
| External Entity | ✓ | | ✓ | | | |
| Process | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Data Store | | ✓ | ✓ | ✓ | ✓ | |
| Data Flow | | ✓ | | ✓ | ✓ | |

**Threat definitions:**
- **Spoofing** — Impersonating a user/system. Mitigate with strong auth, MFA, certificate pinning.
- **Tampering** — Unauthorized modification of data/code. Mitigate with MACs, signatures, integrity checks.
- **Repudiation** — Denying actions performed. Mitigate with audit logs, digital signatures, non-repudiation.
- **Information Disclosure** — Exposing data to unauthorized parties. Mitigate with encryption at rest/transit, access controls.
- **Denial of Service** — Making system unavailable. Mitigate with rate limiting, redundancy, resource quotas.
- **Elevation of Privilege** — Gaining unauthorized access levels. Mitigate with least privilege, input validation, sandboxing.

### Step 3: DREAD Risk Rating

Score each identified threat 1-10 on DREAD criteria:

| Criterion | 1-3 (Low) | 4-6 (Medium) | 7-10 (High) |
|-----------|-----------|--------------|-------------|
| **D**amage | Minor data loss | Service degradation | Complete data loss/ransom |
| **R**eproducibility | Hard, needs rare conditions | Sometimes reproducible | Always reproducible |
| **E**xploitability | Expert required, custom tool | Advanced user, known tool | Any user, browser only |
| **A**ffected users | Few/specific | Some users | All users |
| **D**iscoverability | Hard to find, not obvious | Can be found with effort | Obvious in URL/API/UI |

**Risk score** = (D + R + E + A + D) / 5

| Score Range | Priority | Action |
|-------------|----------|--------|
| 7-10 | Critical | Fix before merge |
| 4-7 | High | Fix within sprint |
| 2-4 | Medium | Track in backlog |
| 1-2 | Low | Accept risk with documentation |

### STRIDE Review Checklist
- [ ] DFD created for all new services/features
- [ ] Trust boundaries identified and documented
- [ ] Each DFD element analyzed against STRIDE categories
- [ ] Threats scored using DREAD
- [ ] Critical/High threats have mitigations before merge
- [ ] Residual risks documented and accepted by security owner

## OWASP Threat Modeling Process

OWASP approach focuses on understanding what we're building, what can go wrong, and what we do about it. Source: https://owasp.org/www-community/Threat_Modeling

### Step 1: Identify Assets

Enumerate all assets that need protection:

| Asset Type | Examples | Classification |
------------|----------|----------------|
| Data | PII, credentials, financial records, health data | Confidentiality + Integrity |
| Services | Auth, payment, notification, search | Availability + Integrity |
| Infrastructure | Databases, message queues, caches, CDNs | Availability |
| Code | Source, build artifacts, configs | Integrity |
| Secrets | API keys, encryption keys, certificates | Confidentiality |

### Step 2: Identify Threat Agents

Who/what can threaten each asset:

| Agent Type | Motivation | Capability |
------------|------------|------------|
| External attacker | Financial gain, disruption | Varies — script kiddie to APT |
| Malicious insider | Revenge, financial gain | High — has credentials/knowledge |
| Compromised service | Lateral movement | Medium — has service permissions |
| Automated bot | Scraping, credential stuffing | Low-medium — script-based |
| Supply chain | Backdoor, data exfiltration | High — trusted position |

### Step 3: Map Trust Boundaries

Document every trust boundary crossing in the system:

```
[Internet] ──── FW ──── [DMZ]
                           │
                  ──── App Firewall ──── [App Tier]
                                           │
                                    ──── DB ACL ──── [Data Tier]
```

Trust boundary types:
- Network boundaries (public → DMZ → internal → admin)
- Process boundaries (OS process isolation, containers)
- User/process boundaries (privileged vs unprivileged)
- Third-party boundaries (SaaS, APIs, vendor integrations)
- Service mesh boundaries (namespace isolation, mTLS zones)

### Step 4: Enumerate Attack Surfaces

For each trust boundary crossing, enumerate exposure points:

| Surface | Example | Risk |
---------|---------|------|
| API endpoints | REST/GraphQL/gRPC interfaces | Injection, broken auth, SSRF |
| Authentication flows | Login, token refresh, SSO | Credential stuffing, session fixation |
| File uploads | User content, imports | Path traversal, malware upload |
| Message queues | Async processing pipelines | Poison messages, replay attacks |
| Admin interfaces | Dashboards, management APIs | Privilege escalation, default creds |
| Webhooks | Incoming notifications | Forgery, SSRF |
| CLI/SDK | Internal tools, scripts | Command injection |

### OWASP Threat Modeling Deliverable
- [ ] Asset inventory with classification labels
- [ ] Threat agent profiles with capability assessments
- [ ] Trust boundary diagram with all crossings marked
- [ ] Attack surface register with mitigations
- [ ] Gap analysis against OWASP ASVS (Application Security Verification Standard)

## PASTA Methodology (Risk-Centric Threat Modeling)

Process for Attack Simulation and Threat Analysis. 7-stage risk-centric methodology that aligns business objectives with technical security. Source: UcedaVelez & Morana, 2015.

### 7 Stages

| Stage | Name | Activity | Output |
|-------|------|----------|--------|
| **I** | Define Objectives | Map business goals to security requirements. What is the system protecting? What is business impact of compromise? | Business impact assessment, security objectives |
| **II** | Define Technical Scope | Enumerate all tech components: frameworks, protocols, APIs, infrastructure, third-party deps. | Technical scope document, architecture inventory |
| **III** | Application Decomposition | Create DFDs, identify entry points, data flows, trust zones. Map components to functions. | DFDs, use cases, entry point catalog |
| **IV** | Threat Analysis | Identify threat sources, threat events, attack vectors. Use MITRE ATT&CK for TTP mapping. | Threat library, attack trees |
| **V** | Vulnerability Analysis | Map CVEs, misconfigs, design flaws to components from Stage III. Cross-reference with threat catalog from Stage IV. | Vulnerability register, root cause analysis |
| **VI** | Attack Modeling | Simulate attack paths using attack trees or attack graphs. Map attack chains across components. | Attack models, kill chain sequences |
| **VII** | Risk & Impact Analysis | Calculate risk = likelihood × impact. Prioritize. Define countermeasures and residual risk acceptance. | Risk matrix, countermeasure roadmap, risk register |

### PASTA Integration with Review

When PASTA review is requested (complex/risky changes):
1. **Pre-merge**: Stages I-III (scope + decomposition) — ensure DFD exists
2. **During review**: Stages IV-VI (threat + vuln + attack modeling)
3. **Post-merge**: Stage VII — finalize risk register, assign countermeasures

### PASTA vs STRIDE vs OWASP Selection

| Methodology | Best For | Effort | When |
|-------------|----------|--------|------|
| STRIDE | Component-level threat enumeration | Low-Medium | Every PR touching security boundaries |
| OWASP TM | Full system threat modeling | Medium | New services, major features |
| PASTA | Risk-centric, business-aligned | High | High-risk systems, regulatory requirements, financial/health/PII |

## Microservices Security Architecture Review

Security review for distributed systems. Covers zero trust principles, service mesh, identity propagation, secrets management, and network segmentation.

### Zero Trust Architecture

Principle: "Never trust, always verify." Every request is authenticated and authorized regardless of network location.

| Principle | Implementation | Review Check |
|-----------|---------------|--------------|
| Verify explicitly | Auth every request with strong identity | No implicit trust based on network location |
| Least privilege access | Just-in-time, just-enough-access (JIT/JEA) | Services have minimum required permissions |
| Assume breach | Encrypt all traffic, segment networks, use threat detection | Blast radius minimized by design |

**Zero trust checklist:**
- [ ] No service trusts another based solely on network position
- [ ] Every inter-service call authenticated (mTLS, JWT, API key)
- [ ] Every inter-service call authorized (policy engine, RBAC, ABAC)
- [ ] No shared secrets between services (use SPIFFE/SPIRE for identity)
- [ ] Lateral movement prevented by network policies

### Service Mesh mTLS

Mutual TLS between all services. Enforced by service mesh (Istio, Linkerd, Consul Connect).

| Aspect | Requirement | Review Check |
|--------|-------------|--------------|
| Certificate rotation | Auto-rotate with short-lived certs (<24h) | No long-lived mTLS certs |
| Strict mTLS | No permissive mode (plaintext fallback disabled) | `PeerAuthentication: STRICT` |
| Certificate authority | Internal CA, not public (use SPIRE, Vault PKI) | No self-signed or public CA for internal |
| Identity verification | SPIFFE ID in SAN, not just certificate validity | Verify workload identity, not just cert chain |

```yaml
# Istio strict mTLS example
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: istio-system
spec:
  mtls:
    mode: STRICT
```

### Identity Propagation

User/service identity must propagate through entire call chain.

| Pattern | Mechanism | Trade-off |
|---------|-----------|-----------|
| JWT propagation | Pass JWT through service chain | Stateless but token size grows with claims |
| OAuth2 token exchange | Exchange token at each hop (RFC 8693) | Secure but adds latency |
| SPIFFE/SPIRE | Workload identity separate from user identity | Strong identity but infrastructure overhead |
| OPA/Gatekeeper | Policy evaluation at each service hop | Flexible but policy management complexity |

**Review checklist:**
- [ ] User identity propagated to all downstream services (no identity loss at service boundaries)
- [ ] Service-to-service calls use workload identity (not user token forwarded directly)
- [ ] Token validation at every hop (no trusting upstream service's claims without verification)
- [ ] No hardcoded service credentials (identity from platform, not config)
- [ ] Token audience restriction prevents replay across services

### Secrets Management

| Aspect | Requirement | Anti-Pattern |
|--------|-------------|--------------|
| Storage | Vault, AWS Secrets Manager, Azure KeyVault | Env vars, config files, source code |
| Rotation | Auto-rotate with short TTL | Static secrets, manual rotation |
| Access | Per-service secrets, scoped to least privilege | Shared secrets across services |
| Audit | Log every secret access | No access logging |
| Injection | Sidecar/CSI driver, not env vars | Secrets in container env (visible in `docker inspect`) |

**Review checklist:**
- [ ] No secrets in source code (Trivy/secret-scanner clean)
- [ ] Secrets stored in dedicated secret manager
- [ ] Secrets scoped per service (no shared credentials)
- [ ] Auto-rotation configured with appropriate TTL
- [ ] Secret access audited and monitored
- [ ] No secrets in container images (multi-stage builds, `.dockerignore`)
- [ ] Secrets injected via sidecar or CSI driver (not env vars)
- [ ] Emergency secret rotation procedure documented

### Network Segmentation

| Layer | Tool | Review Check |
|-------|------|--------------|
| Kubernetes NetworkPolicy | Calico, Cilium | Default-deny ingress; explicit allow per service |
| Service mesh authorization | Istio AuthorizationPolicy | L7 policies (method, path, headers) |
| Cloud VPC/subnet | AWS VPC, GCP VPC | Services in separate subnets, no unnecessary peering |
| API Gateway | Kong, Envoy, AWS API Gateway | Rate limiting, auth, WAF at edge |
| Egress controls | Istio ServiceEntry, network policy | Services can only reach required external endpoints |

**Network segmentation checklist:**
- [ ] Default-deny network policies in all namespaces
- [ ] Explicit allow rules per service-to-service communication
- [ ] L7 authorization policies (not just L3/L4)
- [ ] Egress restricted to known external dependencies
- [ ] No hostNetwork/hostPID on production pods
- [ ] Database/cache not directly accessible from outside cluster
- [ ] Admin interfaces on separate network segment
- [ ] Service mesh enforces mutual TLS across namespace boundaries

## Security Architecture Checklist for Microservices

Comprehensive checklist for reviewing microservices security architecture:

### Authentication & Authorization
- [ ] Centralized identity provider (Keycloak, Auth0, Cognito)
- [ ] OAuth2/OIDC for user authentication (no custom auth)
- [ ] Service-to-service auth via mTLS + SPIFFE or short-lived JWT
- [ ] RBAC/ABAC enforced at service level (not just gateway)
- [ ] Token validation at every service (don't trust gateway alone)
- [ ] No authentication bypass paths (health checks excluded from auth)

### Data Protection
- [ ] Encryption at rest (AES-256, cloud KMS managed keys)
- [ ] Encryption in transit (TLS 1.3, mTLS internally)
- [ ] PII field-level encryption or tokenization
- [ ] Data classification labels on all data stores
- [ ] No sensitive data in logs (redact PII, credentials, tokens)
- [ ] Data retention policies enforced automatically

### API Security
- [ ] Input validation on all endpoints (schema validation, allowlisting)
- [ ] Rate limiting per user/IP/service
- [ ] API versioning with deprecation policy
- [ ] CORS configured correctly (no wildcard `*` in production)
- [ ] GraphQL: depth limiting, query complexity analysis, introspection disabled
- [ ] gRPC: message validation, deadline enforcement
- [ ] No sensitive data in URL query parameters

### Observability & Incident Response
- [ ] Structured logging with correlation IDs (trace entire request)
- [ ] Security event logging (auth failures, access denials, privilege changes)
- [ ] Alerting on anomalous patterns (spike in 401/403, unusual data access)
- [ ] Distributed tracing (Jaeger, Zipkin, OpenTelemetry)
- [ ] Runbooks for common security incidents
- [ ] Incident response plan tested quarterly

### Resilience & Availability
- [ ] Circuit breakers between services (prevent cascade failures)
- [ ] Bulkhead pattern (isolate failure domains)
- [ ] Graceful degradation (fallback behavior, not 500 errors)
- [ ] Chaos engineering for security (fault injection, latency injection)
- [ ] DDoS protection at edge (CDN, WAF, rate limiting)
- [ ] Backup and recovery tested (RTO/RPO documented)

### Supply Chain & Dependencies
- [ ] All dependencies pinned by hash (not mutable tags)
- [ ] SBOM generated for every build
- [ ] Container images scanned (Trivy) and signed (Sigstore/cosign)
- [ ] Base images minimal (distroless, scratch) and updated
- [ ] No `latest` tags in production manifests
- [ ] Dependency update automation (Renovate, Dependabot) with security prioritization

### Deployment & Configuration
- [ ] Immutable infrastructure (no SSH into containers)
- [ ] Configuration from ConfigMaps/Secrets (not baked into images)
- [ ] Security context: non-root user, read-only filesystem, drop all capabilities
- [ ] Pod security standards enforced (restricted profile)
- [ ] No privileged containers or host mounts
- [ ] Canary/blue-green deployment with automatic rollback on security regression
