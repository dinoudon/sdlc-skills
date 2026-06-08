---
name: sdlc-adversarial-review
description: "Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, orchestrator synthesizes findings and applies fixes. Includes Google/Stripe/Meta code review culture, DORA velocity metrics, and automated tooling integration."
version: 2.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, code-review, pr-review, adversarial, multi-agent, security, architecture, google, stripe, dora, semgrep, codeql]
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
Reference: OWASP Top 10 2021, CWE/SANS Top 25.""",
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
```

## DORA Velocity Metrics

Track review velocity against DORA benchmarks:

| Metric | Elite | High | Medium | Low |
|--------|-------|------|--------|-----|
| PR to first review | <4 hours | <1 day | <1 week | >1 week |
| PR review to merge | <1 day | <3 days | <1 week | >1 week |
| PR size | <400 LOC | <800 LOC | <1500 LOC | >1500 LOC |
| PR lifetime | <1 day | <3 days | <1 week | >1 week |

**Key findings from DORA research:**
- Elite performers review in hours, not days
- Long review queues correlate with lower deployment frequency
- Small batch size (small PRs) enables fastest review cycles
- WIP limits on PRs-in-review improve throughput

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
