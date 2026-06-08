---
name: sdlc-adversarial-review
description: "Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, orchestrator synthesizes findings and applies fixes. Includes Google/Stripe/Meta code review culture, DORA velocity metrics (5 metrics incl. reliability), SLSA supply chain verification, AI-assisted review guardrails, automated tooling integration, advanced threat modeling (attack trees, kill chain, MITRE ATT&CK), secure code review patterns, compliance-aware review (SOC2/GDPR/HIPAA), AI/ML model review, and performance review patterns."
version: 3.2.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, code-review, pr-review, adversarial, multi-agent, security, architecture, google, stripe, dora, semgrep, codeql, slsa, supply-chain, sbom, sigstore, ai-review, threat-modeling, mitre-attack, kill-chain, attack-trees, crypto-review, compliance, soc2, gdpr, hipaa, ai-ml-review, performance-review, concurrency]
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

## Advanced Threat Modeling: Attack Trees, Kill Chain, MITRE ATT&CK

### Attack Trees

Hierarchical decomposition of attack goals into sub-goals. Root = attacker objective, leaves = concrete exploit steps.

**Structure:**
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

**Review workflow:**
1. Identify root goal from PR context (what data/functionality is affected?)
2. Build tree with AND (all children required) / OR (any child sufficient) gates
3. For each leaf node, check if PR introduces or mitigates that vector
4. Calculate attack cost: sum effort of cheapest OR-path from root to leaf
5. Flag if attack cost < defense cost (economics of security)

**Attack tree checklist:**
- [ ] Root goal clearly defined (attacker objective)
- [ ] All OR-paths from root to leaves evaluated
- [ ] Cheapest attack path identified and mitigated
- [ ] New attack surfaces introduced by PR are leaf nodes in tree
- [ ] Defense cost proportional to attack cost

### Cyber Kill Chain Analysis (Lockheed Martin)

7-stage attack lifecycle. Review maps code changes to kill chain stages to identify where defenses should be strengthened.

| Stage | Description | Code Review Focus | Example Mitigation |
|-------|-------------|-------------------|-------------------|
| 1. Reconnaissance | Target research, scanning | Rate limiting, anti-bot, info leakage | No verbose error messages, no stack traces in prod |
| 2. Weaponization | Crafting exploit payload | Input validation, sanitization | Schema validation, allowlisting |
| 3. Delivery | Transmitting exploit to target | Transport security, WAF rules | TLS 1.3, CSP headers, email filtering |
| 4. Exploitation | Triggering vulnerability | Vulnerability surface reduction | Parameterized queries, output encoding, sandboxing |
| 5. Installation | Establishing persistence | File integrity, rootkit detection | Immutable containers, read-only FS, no SSH |
| 6. Command & Control | Remote control channel | Egress monitoring, DNS security | Egress network policies, DNS filtering, anomaly detection |
| 7. Actions on Objectives | Data theft, destruction | Data protection, access controls | Encryption at rest, DLP, audit logging |

**Kill chain review checklist:**
- [ ] Each PR change mapped to relevant kill chain stages
- [ ] Defense exists at minimum 3 stages (defense in depth)
- [ ] Stage 4 (Exploitation) defenses verified for all user inputs
- [ ] Stage 6 (C2) egress paths restricted by network policy
- [ ] Stage 7 (Actions) data protection verified for sensitive assets

### MITRE ATT&CK Mapping

Map code patterns to MITRE ATT&CK tactics and techniques for standardized threat vocabulary.

**Tactics (attacker goals):**
| Tactic ID | Name | Review Focus |
|-----------|------|-------------|
| TA0001 | Initial Access | Phishing, valid accounts, supply chain compromise |
| TA0002 | Execution | Command injection, script interpreters, deserialization |
| TA0003 | Persistence | Backdoors, scheduled tasks, boot/autostart |
| TA0004 | Privilege Escalation | SUID, kernel exploits, token manipulation |
| TA0005 | Defense Evasion | Obfuscation, log deletion, binary padding |
| TA0006 | Credential Access | Brute force, credential dumping, keylogging |
| TA0007 | Discovery | Network/service scanning, system info enumeration |
| TA0008 | Lateral Movement | Remote services, pass-the-hash, SSH hijacking |
| TA0009 | Collection | Data staging, screen capture, input capture |
| TA0010 | Exfiltration | Over C2, DNS, alternative protocols |
| TA0011 | Command and Control | Encrypted channels, web protocols, domain fronting |
| TA0040 | Impact | Data destruction, ransomware, defacement |

**Common technique-to-code mappings:**
| Technique ID | Name | Code Pattern to Review |
|-------------|------|----------------------|
| T1059 | Command and Scripting Interpreter | `os.system()`, `subprocess.call(shell=True)`, `eval()`, `exec()` |
| T1078 | Valid Accounts | Default credentials, weak password policy, credential reuse |
| T1190 | Exploit Public-Facing Application | Unpatched frameworks, SQLi, XSS, SSRF in API endpoints |
| T1053 | Scheduled Task/Job | Cron jobs, systemd timers, Kubernetes CronJobs with excessive perms |
| T1071 | Application Layer Protocol | DNS tunneling, HTTP beaconing, WebSocket C2 channels |
| T1048 | Exfiltration Over Alternative Protocol | DNS exfil, ICMP tunnel, steganography |
| T1486 | Data Encrypted for Impact | Ransomware detection, backup integrity, encryption key access |

**MITRE ATT&CK review checklist:**
- [ ] All user-facing endpoints mapped to T1190 mitigations
- [ ] No eval/exec/system calls (T1059) without sandboxing
- [ ] No default or weak credentials (T1078)
- [ ] Scheduled tasks/Jobs run with least privilege (T1053)
- [ ] Egress monitoring covers T1048/T1071 exfil vectors
- [ ] Backup integrity verified against T1486 (ransomware)

### Threat Modeling Automation

```bash
# pytm — Pythonic threat modeling from code
# Generates DFDs and STRIDE threats from annotated Python
pip install pytm
pytm --dfd diagram ./model.py

# Threatspec — annotate code with threat comments
# Generate threat model from source annotations
pip install threatspec
threatspec init
threatspec report

# MITRE ATT&CK Navigator — visualize coverage
# Export attack map as JSON, load at https://mitre-attack.github.io/attack-navigator/
```

## Secure Code Review Patterns

### Cryptography Review

Deep review of all cryptographic operations. Crypto bugs are silent — code runs, tests pass, but data is not actually protected.

**Algorithm selection matrix:**
| Use Case | Approved | Deprecated/Banned | Review Flag |
|----------|----------|-------------------|-------------|
| Symmetric encryption | AES-256-GCM, ChaCha20-Poly1305 | AES-CBC (without HMAC), DES, 3DES, RC4 | Non-AEAD mode usage |
| Asymmetric encryption | RSA-4096, X25519, Ed25519 | RSA-1024, RSA-2048 (new), DSA | Key size < 2048 |
| Hashing | SHA-256, SHA-3, BLAKE2 | MD5, SHA-1 | Any use of MD5/SHA-1 |
| Key derivation | Argon2id, scrypt, PBKDF2 (>600k iterations) | PBKDF2 (<100k), plain hash | Iteration count too low |
| TLS | TLS 1.3 (preferred), TLS 1.2 (min) | TLS 1.0, TLS 1.1, SSLv3 | Protocol version config |
| Certificate validation | Full chain validation, hostname verification | Disabled cert validation, self-signed in prod | `verify=False`, `NODE_TLS_REJECT_UNAUTHORIZED=0` |

**Crypto code review checklist:**
- [ ] No hardcoded keys, IVs, salts, or passwords
- [ ] IVs/nonces generated via CSPRNG (`os.urandom`, `secrets`, `SystemRandom`)
- [ ] IVs/nonces never reused (unique per encryption operation)
- [ ] Authenticated encryption (AEAD) used for all symmetric encryption
- [ ] Key rotation mechanism exists and tested
- [ ] No custom crypto implementations (use established libraries)
- [ ] Secure random number generator used (not `random`, `Math.random()`)
- [ ] Constant-time comparison for MACs/tokens (`hmac.compare_digest`)
- [ ] Key material zeroized after use where possible
- [ ] Certificate pinning implemented for mobile/desktop clients
- [ ] Cryptographic agility: algorithm can be replaced without code rewrite

**Common crypto anti-patterns:**
```python
# BAD: ECB mode reveals patterns
cipher = AES.new(key, AES.MODE_ECB)
# GOOD: GCM mode (authenticated encryption)
cipher = AES.new(key, AES.MODE_GCM)
# BAD: Predictable IV
iv = b'\x00' * 16
# GOOD: Random IV
iv = os.urandom(16)
# BAD: Non-constant-time comparison
if user_mac == expected_mac:  # Timing attack
# GOOD: Constant-time comparison
if hmac.compare_digest(user_mac, expected_mac):
# BAD: Weak KDF with low iterations
dk = hashlib.pbkdf2_hmac('sha256', password, salt, 1000)
# GOOD: Strong KDF
dk = hashlib.pbkdf2_hmac('sha256', password, salt, 600000)
# Or better: argon2id
```

### Authentication Flow Review

Review authentication flows end-to-end. Auth bugs allow account takeover.

**OAuth2/OIDC flow review:**
- [ ] `state` parameter used and validated (CSRF prevention)
- [ ] `nonce` parameter used for ID tokens (replay prevention)
- [ ] Token storage: HTTP-only secure cookies (not localStorage)
- [ ] Token refresh: rotation implemented (old refresh token invalidated)
- [ ] Token validation: signature, issuer, audience, expiry all checked
- [ ] Redirect URIs: exact match, no wildcards, no open redirects
- [ ] PKCE used for public clients (SPA, mobile)
- [ ] No tokens in URL query parameters (logged by servers/proxies)

**Session management review:**
- [ ] Session ID generated via CSPRNG (≥128 bits entropy)
- [ ] Session ID regenerated on login (prevent session fixation)
- [ ] Session timeout: idle (30 min) and absolute (12h) enforced
- [ ] Session invalidation on logout (server-side deletion)
- [ ] `Secure`, `HttpOnly`, `SameSite=Lax` flags on cookies
- [ ] No session data in URLs
- [ ] Concurrent session limits enforced
- [ ] Session binding to client fingerprint (IP + User-Agent hash)

**Password handling review:**
- [ ] Passwords hashed with Argon2id (or scrypt/PBKDF2 with high cost)
- [ ] No password length limit < 72 characters
- [ ] Common password check (HaveIBeenPwned API or local list)
- [ ] Rate limiting on login attempts (per account + per IP)
- [ ] Account lockout with exponential backoff
- [ ] No user enumeration (same error for invalid user vs wrong password)
- [ ] MFA supported and enforced for privileged accounts
- [ ] Password reset tokens: single-use, time-limited (15 min), invalidated after use

**Multi-factor authentication review:**
- [ ] TOTP implementation uses constant-time comparison
- [ ] Backup codes: hashed at rest, single-use, rate-limited
- [ ] SMS OTP: consider SIM-swap risk, prefer TOTP/WebAuthn
- [ ] WebAuthn/FIDO2: origin verification, user presence check
- [ ] MFA bypass paths: none exist (test for downgrade attacks)

### Input Validation Deep-Dive

Comprehensive input validation review. All external input is hostile until proven otherwise.

**Validation strategy layers:**
| Layer | Location | Purpose | Technology |
|-------|----------|---------|------------|
| L1: Transport | Edge/WAF | Block known attack patterns | ModSecurity, AWS WAF, CloudFlare |
| L2: Schema | API Gateway | Structural validation | JSON Schema, OpenAPI, Protobuf |
| L3: Business | Application | Semantic validation | Custom validators, domain rules |
| L4: Data | Database | Integrity constraints | CHECK constraints, triggers |

**Input validation checklist:**
- [ ] All user input validated at L2 (schema) and L3 (business logic)
- [ ] Allowlist validation preferred over blocklist
- [ ] Type coercion attacks prevented (e.g., `"1"` vs `1`, `null` vs missing)
- [ ] String inputs: max length enforced, encoding verified (UTF-8)
- [ ] Numeric inputs: range checked, NaN/Infinity handled
- [ ] File uploads: content-type verified (magic bytes, not extension), size limited, stored outside webroot
- [ ] Date/time inputs: timezone handling explicit, no parsing ambiguities
- [ ] JSON/XML inputs: depth limited, size limited, schema validated
- [ ] URL inputs: scheme allowlist (https only), no `file://`/`gopher://`/`data://`
- [ ] Header injection prevented (no newlines in HTTP headers)
- [ ] Log injection prevented (sanitized before logging)
- [ ] Regex: no ReDoS patterns (catastrophic backtracking)

**Language-specific validation patterns:**
```python
# Python — SQL injection prevention
# BAD
cursor.execute(f"SELECT * FROM users WHERE id = {user_id}")
# GOOD
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))
# Python — SSRF prevention
import ipaddress, urllib.parse
def validate_url(url):
    parsed = urllib.parse.urlparse(url)
    if parsed.scheme not in ('https',):
        raise ValueError("Only HTTPS allowed")
    ip = socket.getaddrinfo(parsed.hostname, None)[0][4][0]
    if ipaddress.ip_address(ip).is_private:
        raise ValueError("No private IPs allowed")
    return url
```
```javascript
// JavaScript — XSS prevention
// BAD
element.innerHTML = userInput;
// GOOD
element.textContent = userInput;

// JavaScript — Prototype pollution prevention
// BAD
Object.assign(target, userInput);
// GOOD
const sanitized = JSON.parse(JSON.stringify(userInput));
Object.assign(target, sanitized);
```

## Compliance-Aware Code Review

### SOC 2 Type II Review Patterns

SOC 2 Trust Services Criteria mapped to code patterns.

| Criteria | Control | Code Review Check |
----------|---------|-------------------|
| CC6.1 | Logical access controls | Auth required on all endpoints, RBAC enforced |
| CC6.2 | Access provisioning | User creation/deprovisioning auditable, role changes logged |
| CC6.3 | Access removal | Deprovisioning automated, access revoked on role change |
| CC6.6 | System boundaries | Network segmentation, firewall rules as code |
| CC6.7 | Data transmission | TLS 1.2+ on all connections, mTLS internally |
| CC6.8 | Unauthorized access prevention | Intrusion detection, anomaly alerting |
| CC7.1 | Vulnerability management | Dependency scanning, patching SLAs defined |
| CC7.2 | Monitoring | Security event logging, SIEM integration |
| CC7.3 | Incident response | Runbooks, escalation procedures, post-mortems |
| CC7.4 | Incident remediation | Root cause analysis, corrective actions tracked |
| CC8.1 | Change management | PR reviews required, CI/CD gates, approval workflows |
| CC9.1 | Risk mitigation | Risk register, treatment plans, periodic review |

**SOC 2 code review checklist:**
- [ ] All access changes logged with actor, target, timestamp, reason
- [ ] Audit logs immutable (append-only store, no delete API)
- [ ] Access reviews possible (who has access to what, reportable)
- [ ] Encryption at rest for all customer data (AES-256 or cloud KMS)
- [ ] Encryption in transit (TLS 1.2+ enforced, no downgrade)
- [ ] Change management: all changes go through PR, no direct deploys
- [ ] Separation of duties: deployer ≠ code author (enforced in CI)
- [ ] Vulnerability scan results gated in CI (critical = block)
- [ ] Security event logging: auth failures, access denials, privilege changes
- [ ] Data retention policy enforced programmatically
- [ ] Incident response runbooks exist for critical system components

### GDPR Code Patterns

EU General Data Protection Regulation — right to erasure, data minimization, consent management.

**Data subject rights implementation review:**
| Right | Article | Code Pattern | Review Check |
-------|---------|-------------|--------------|
| Right to erasure | Art. 17 | `DELETE` or anonymization endpoint | Exists, cascades to all stores, < 30 days |
| Right to access | Art. 15 | Data export endpoint | Returns all PII, machine-readable format |
| Right to rectification | Art. 16 | Profile update API | Propagates to downstream systems |
| Right to portability | Art. 20 | Data export in standard format | JSON/CSV, includes all provided data |
| Right to restrict | Art. 18 | Processing flag toggle | Non-PII operations continue, PII processing stops |
| Consent withdrawal | Art. 7 | Consent management API | Granular consent, easy withdrawal, no dark patterns |

**GDPR code review checklist:**
- [ ] Personal data identified and tagged in code (PII fields annotated)
- [ ] Data minimization: only collect necessary fields (no "just in case")
- [ ] Purpose limitation: data used only for stated purpose
- [ ] Consent recorded with timestamp, scope, and version
- [ ] Consent withdrawal as easy as giving consent (no friction)
- [ ] Data retention automated: auto-delete/anonymize after retention period
- [ ] Data portability: export endpoint in machine-readable format
- [ ] Right to erasure: cascading deletion across all data stores
- [ ] Data processing records maintained (Art. 30): what data, why, legal basis
- [ ] Cross-border transfer: data locality enforced, SCCs in place
- [ ] Breach notification: detection + 72h notification workflow automated
- [ ] No PII in logs, error messages, or analytics events
- [ ] Privacy by default: strictest settings applied without user action
- [ ] DPIA (Data Protection Impact Assessment) exists for high-risk processing

```python
# GDPR — Data erasure pattern
class UserDataService:
    def erase_user_data(self, user_id: str) -> ErasureResult:
        """GDPR Art. 17 — Right to erasure."""
        # Verify identity before erasure
        self._verify_data_subject(user_id)

        # Delete from primary store
        self.db.delete_user(user_id)

        # Cascade to all downstream stores
        self.search_index.remove_user(user_id)
        self.cache.invalidate(user_id)
        self.analytics.anonymize_user(user_id)
        self.email_service.remove_contact(user_id)
        self.third_party_processors.request_deletion(user_id)

        # Log erasure (without PII) for compliance audit
        self.audit_log.record(action="data_erasure", subject_hash=hash(user_id))

        return ErasureResult(
            status="complete",
            systems_erased=self._get_erasure_manifest(user_id),
            retention_exceptions=self._get_legal_holds(user_id)
        )
```

### HIPAA Code Patterns

US Health Insurance Portability and Accountability Act — protect PHI (Protected Health Information).

**PHI handling review:**
| HIPAA Rule | Code Requirement | Review Check |
------------|------------------|--------------|
| Access Control (§164.312(a)) | Unique user ID, emergency access, auto-logoff, encryption | All 4 implemented |
| Audit Controls (§164.312(b)) | Log all PHI access (who, what, when) | Audit trail immutable and complete |
| Integrity Controls (§164.312(c)) | PHI cannot be altered/destroyed improperly | Checksums, versioning, access controls |
| Transmission Security (§164.312(e)) | Encrypt PHI in transit | TLS 1.2+ mandatory, no exceptions |
| Encryption at Rest (§164.312(a)(2)(iv)) | Encrypt PHI at rest | AES-256, key management documented |
| Minimum Necessary | Access only to required PHI | Field-level access controls, not all-or-nothing |
| Business Associate | BAA for any third-party handling PHI | No PHI to services without signed BAA |

**HIPAA code review checklist:**
- [ ] All PHI fields identified and marked (`@phi` annotations, schema tags)
- [ ] PHI access logged: user ID, timestamp, fields accessed, purpose
- [ ] PHI never in URL parameters, query strings, or error messages
- [ ] PHI encrypted at rest (AES-256, key in KMS/HSM)
- [ ] PHI encrypted in transit (TLS 1.2+ enforced)
- [ ] PHI access follows minimum necessary principle (field-level ACLs)
- [ ] No PHI in application logs, debug output, or stack traces
- [ ] Audit logs immutable, tamper-evident, retained 6+ years
- [ ] Emergency access procedure exists (break-glass with logging)
- [ ] Auto-logoff configured (15 min idle timeout for PHI access)
- [ ] BAA verified for all third-party services processing PHI
- [ ] De-identification follows Safe Harbor or Expert Determination method
- [ ] Backup encryption: same standard as primary data
- [ ] Disposal: cryptographic erasure when PHI no longer needed

```python
# HIPAA — PHI access logging pattern
import functools
import hashlib
from datetime import datetime
from typing import Any, Callable

def phi_access_logged(func: Callable) -> Callable:
    """Decorator to log all PHI access per HIPAA §164.312(b)."""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        current_user = get_authenticated_user()
        result = func(*args, **kwargs)

        # Log access without exposing actual PHI
        audit_logger.info("phi_access", extra={
            "user_id": current_user.id,
            "user_role": current_user.role,
            "function": func.__name__,
            "timestamp": datetime.utcnow().isoformat(),
            "fields_accessed": get_phi_field_names(func),
            "patient_hash": hashlib.sha256(
                kwargs.get("patient_id", "").encode()
            ).hexdigest()[:16],
            "access_purpose": kwargs.get("purpose", "treatment"),
        })
        return result
    return wrapper

# Usage — every PHI read/write must be decorated
@phi_access_logged
def get_patient_record(patient_id: str, purpose: str = "treatment") -> dict:
    """Access patient record. All access logged per HIPAA."""
    return db.query(PatientRecord).filter(id=patient_id).first()
```

**Compliance review selection matrix:**
| Standard | Applies When | Review Effort | Key Focus |
|----------|-------------|---------------|-----------|
| SOC 2 | SaaS/B2B services, enterprise sales | Medium | Access controls, change management, monitoring |
| GDPR | Any service processing EU personal data | Medium-High | Data subject rights, consent, minimization, erasure |
| HIPAA | US healthcare data (PHI) | High | PHI access controls, audit logging, encryption |
| PCI DSS | Payment card data | High | Cardholder data isolation, encryption, network segmentation |
| ISO 27001 | Information security management | Medium | ISMS controls, risk assessment, continuous improvement |

## AI/ML Model Review

Review AI/ML models for security, bias, and robustness. ML systems have unique attack surfaces beyond traditional software.

### Bias and Fairness Testing

| Metric | Definition | Acceptable Threshold |
|--------|-----------|---------------------|
| Demographic Parity | P(Ŷ=1\|A=a) = P(Ŷ=1\|A=b) for all groups | < 10% difference |
| Equalized Odds | TPR and FPR equal across groups | < 5% difference |
| Predictive Parity | Precision equal across groups | < 5% difference |
| Calibration | P(Y=1\|Ŷ=s, A=a) = P(Y=1\|Ŷ=s, A=b) | < 5% difference |
| Individual Fairness | Similar inputs → similar outputs | Distance metric < threshold |

**Bias testing checklist:**
- [ ] Protected attributes identified (race, gender, age, disability, etc.)
- [ ] Training data representativeness audited
- [ ] Fairness metrics computed across all protected groups
- [ ] Disparate impact ratio > 0.8 (4/5ths rule)
- [ ] Proxy features identified and evaluated (zip code → race correlation)
- [ ] Model cards published (documenting intended use, limitations, bias analysis)
- [ ] Ongoing bias monitoring in production (not just pre-deployment)
- [ ] Human-in-the-loop for high-stakes decisions (hiring, lending, healthcare)

### Prompt Injection Defense (LLM Review)

Review LLM integrations for prompt injection and jailbreak vulnerabilities.

**Prompt injection attack vectors:**
| Vector | Attack | Mitigation |
|--------|--------|------------|
| Direct injection | User input overrides system prompt | Input/output separation, delimiters, XML tags |
| Indirect injection | Malicious content in retrieved documents | Content sanitization, trust boundaries on RAG sources |
| Jailbreaking | System prompt extraction/override | Prompt hardening, output filtering, red teaming |
| Data exfiltration | LLM leaks training data or PII | Output filtering, PII detection, differential privacy |
| Model denial of service | Extremely long inputs, recursive prompts | Token limits, rate limiting, cost controls |

**Prompt injection review checklist:**
- [ ] User input never directly concatenated with system prompt
- [ ] Delimiters/encodings used to separate instructions from data
- [ ] Output filtering: no PII leakage, no system prompt disclosure
- [ ] Tool/function calls validated before execution (no arbitrary code)
- [ ] RAG source trust boundaries enforced (untrusted content tagged)
- [ ] Rate limiting on LLM endpoints (cost control, abuse prevention)
- [ ] Token limits enforced (prevent context window overflow attacks)
- [ ] Red team testing for jailbreak and prompt extraction

```python
# Prompt injection defense pattern
def safe_llm_call(user_input: str, system_prompt: str) -> str:
    """LLM call with prompt injection defenses."""
    # 1. Input sanitization
    sanitized = sanitize_user_input(user_input)

    # 2. Structured prompt with clear boundaries
    messages = [
        {"role": "system", "content": system_prompt},
        {"role": "user", "content": f"<user_input>\n{sanitized}\n</user_input>"},
    ]

    # 3. Call with limits
    response = llm.chat(
        messages=messages,
        max_tokens=1000,
        temperature=0.0,  # Deterministic for safety-critical
    )

    # 4. Output filtering
    filtered = filter_pii(response.content)
    filtered = filter_system_prompt_leakage(filtered, system_prompt)

    return filtered
```

### Model Security Review

| Threat | Description | Mitigation |
|--------|-------------|------------|
| Model theft | Extraction via API queries | Rate limiting, query monitoring, watermarking |
| Data poisoning | Corrupting training data | Data validation, provenance tracking, anomaly detection |
| Adversarial examples | Inputs crafted to fool model | Adversarial training, input validation, ensemble methods |
| Model inversion | Reconstructing training data | Differential privacy, output perturbation |
| Backdoor attacks | Hidden triggers in model | Model provenance, activation analysis, testing |

**Model security checklist:**
- [ ] Model weights/paths not exposed via API or error messages
- [ ] Input validation on all model inputs (bounds checking, type validation)
- [ ] Output validation: model outputs bounded and sanitized
- [ ] Model versioning and provenance (signed model artifacts)
- [ ] Adversarial robustness tested (FGSM, PGD, C&W attacks)
- [ ] Training data pipeline secured (access control, integrity checks)
- [ ] Model serving infrastructure hardened (no shell access, read-only FS)
- [ ] Inference cost monitoring (detect abuse, model extraction attempts)
- [ ] Fallback behavior defined for model failure (graceful degradation)
- [ ] Model explainability for high-stakes decisions (SHAP, LIME)

## Performance Review Patterns

### Algorithmic Complexity Analysis

Review code for time and space complexity. Flag O(n²) or worse on user-facing paths.

**Complexity thresholds:**
| Context | Max Acceptable | Flag If Exceeded |
|---------|---------------|-----------------|
| Hot path (API handler) | O(n log n) | O(n²) or worse |
| Background job | O(n²) acceptable | O(n³) or worse |
| Startup/init | O(n²) acceptable | O(n³) or worse |
| Database query | O(n log n) with index | Full table scan on >10k rows |

**Common complexity anti-patterns:**
```python
# BAD: O(n²) — nested loop for lookup
def find_duplicates(items):
    duplicates = []
    for i, a in enumerate(items):
        for j, b in enumerate(items):
            if i != j and a == b:
                duplicates.append(a)
    return duplicates
# GOOD: O(n) — hash set
def find_duplicates(items):
    seen = set()
    duplicates = set()
    for item in items:
        if item in seen:
            duplicates.add(item)
        seen.add(item)
    return list(duplicates)

# BAD: O(n²) — repeated string concatenation
result = ""
for s in strings:
    result += s  # Creates new string each time
# GOOD: O(n) — join
result = "".join(strings)

# BAD: O(n) lookup in list
if item in large_list:  # Linear scan
# GOOD: O(1) lookup in set
if item in large_set:  # Hash lookup
```

**Complexity review checklist:**
- [ ] Hot-path functions analyzed for time complexity
- [ ] No O(n²) or worse on user-facing endpoints without justification
- [ ] Database queries use appropriate indexes (EXPLAIN plan reviewed)
- [ ] N+1 queries eliminated (use JOIN or batch loading)
- [ ] Pagination implemented for all list endpoints (no unbounded returns)
- [ ] Caching strategy defined for expensive computations
- [ ] Data structures chosen appropriately (list vs set vs dict)
- [ ] Sorting/filtering done at database layer, not application layer where possible

### Memory Leak Detection Patterns

Review for memory leaks in long-running processes.

**Common leak patterns:**
```python
# BAD: Unbounded cache growth
cache = {}
def get_user(user_id):
    if user_id not in cache:
        cache[user_id] = db.fetch(user_id)
    return cache[user_id]  # Never evicts, grows forever
# GOOD: LRU cache with size limit
from functools import lru_cache
@lru_cache(maxsize=1000)
def get_user(user_id):
    return db.fetch(user_id)
# BAD: Event listener never removed
def setup():
    emitter.on('event', handler)  # Leaked reference
# GOOD: Cleanup on teardown
def setup():
    emitter.on('event', handler)
    atexit.register(lambda: emitter.off('event', handler))
# BAD: File handle not closed
def read_file(path):
    f = open(path)
    return f.read()  # Handle leaked if exception occurs
# GOOD: Context manager
def read_file(path):
    with open(path) as f:
        return f.read()
```

**Memory leak review checklist:**
- [ ] All resources use context managers / try-finally / defer
- [ ] Caches have bounded size (LRU, TTL, or max entries)
- [ ] Event listeners/subscribers cleaned up on unmount/teardown
- [ ] Connection pools sized appropriately and connections returned
- [ ] Large data structures processed in chunks (streaming, generators)
- [ ] Circular references broken when no longer needed
- [ ] No global mutable state that grows unboundedly
- [ ] Memory profiling performed for long-running services

### Concurrency Bug Review

Review for race conditions, deadlocks, and data corruption in concurrent code.

**Concurrency bug patterns:**
```python
# BAD: Race condition — check then act
if not os.path.exists(path):  # Another process creates file here
    with open(path, 'w') as f:  # Overwrites or fails
        f.write(data)
# GOOD: Atomic create
fd = os.open(path, os.O_CREAT | os.O_EXCL | os.O_WRONLY)  # Fails if exists
# BAD: Non-atomic read-modify-write
counter = get_counter()  # Read
counter += 1              # Modify
set_counter(counter)      # Write  — lost update if concurrent
# GOOD: Atomic increment
db.execute("UPDATE counters SET value = value + 1 WHERE id = %s", (id,))
# BAD: Shared mutable state without synchronization
results = []
def process(item):
    results.append(transform(item))  # Race condition on list append
# GOOD: Thread-safe collection or lock
import threading
lock = threading.Lock()
results = []
def process(item):
    with lock:
        results.append(transform(item))
```

```javascript
// JavaScript — async race condition
// BAD: Race condition between check and use
async function updateUser(id, data) {
    const user = await db.findUser(id);      // Read
    const merged = { ...user, ...data };      // Modify
    await db.saveUser(id, merged);            // Write — lost update
}

// GOOD: Optimistic locking
async function updateUser(id, data) {
    const result = await db.updateUser(id, {
        ...data,
        _version: { $eq: currentVersion }
    });
    if (result.modifiedCount === 0) {
        throw new ConflictError("Concurrent modification");
    }
}
```

**Concurrency review checklist:**
- [ ] Shared mutable state protected by locks, atomics, or immutability
- [ ] Lock ordering consistent across all code paths (prevent deadlocks)
- [ ] No nested locks (deadlock risk) — use single lock or lock-free patterns
- [ ] Database transactions use appropriate isolation level
- [ ] Optimistic or pessimistic locking for concurrent DB writes
- [ ] No TOCTOU (time-of-check-time-of-use) vulnerabilities
- [ ] Async code: no shared mutable state between callbacks/promises
- [ ] Thread pool sizing appropriate for workload
- [ ] Connection pool limits prevent resource exhaustion
- [ ] Timeout on all external calls (prevent indefinite blocking)
- [ ] Idempotency keys for retried operations (prevent duplicate processing)
- [ ] Graceful shutdown: in-flight requests complete, no data loss

**Deadlock detection pattern:**
```python
# Review for consistent lock ordering
# BAD: Inconsistent ordering — deadlock possible
# Thread 1: acquire(A) then acquire(B)
# Thread 2: acquire(B) then acquire(A)

# GOOD: Consistent ordering
# All threads: acquire(A) then acquire(B) — alphabetical order
# Document lock ordering: LOCK_ORDER = [lock_a, lock_b, lock_c]
```

**Performance review tools:**
```bash
# Python — profiling
python -m cProfile -o profile.prof script.py
python -m snakeviz profile.prof  # Visualize
# Python — memory profiling
pip install memory_profiler
python -m memory_profiler script.py
# Python — concurrency analysis
pip install py-spy
py-spy record -o profile.svg -- python script.py
# Go — race detector
go test -race ./...
# Rust — built-in (borrow checker prevents data races at compile time)
# Review unsafe blocks for concurrency violations

# Java — thread dump analysis
jstack <pid> > thread_dump.txt
```
