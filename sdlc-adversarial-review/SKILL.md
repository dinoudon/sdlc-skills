---
name: sdlc-adversarial-review
description: "Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, orchestrator synthesizes findings and applies fixes. Includes Google/Stripe/Meta code review culture."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, code-review, pr-review, adversarial, multi-agent, security, architecture, google, stripe]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, github-code-review, github-pr-workflow]
---

# Adversarial PR Review

3-agent parallel review system: architecture + security + quality reviewers run in parallel, orchestrator synthesizes and fixes. Includes Google/Stripe/Meta code review culture.

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

## Step 1: Spawn 3 Reviewers in Parallel

```python
tasks = [
    {
        "goal": "Review PR for architecture concerns",
        "context": "Focus on: design patterns, separation of concerns, scalability, module boundaries, dependency direction. Report findings with severity and suggested fix.",
        "toolsets": ["terminal", "file"]
    },
    {
        "goal": "Review PR for security vulnerabilities",
        "context": "Focus on: OWASP Top 10, auth issues, injection vectors, secrets exposure, input validation. Report findings with severity and suggested fix.",
        "toolsets": ["terminal", "file"]
    },
    {
        "goal": "Review PR for code quality",
        "context": "Focus on: readability, test coverage, naming, duplication, error handling, edge cases, performance. Report findings with severity and suggested fix.",
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

## Step 3: Apply Fixes

For each finding:
- If auto-fixable (lint, format, simple refactor) → fix and commit
- If needs human judgment → flag with comment
- If architectural → create issue for follow-up

## Step 4: Re-run CI

After fixes, push and verify CI passes.

## Review Templates

### Architecture Review
```
- [ ] Follows established patterns (Clean/Hexagonal/DDD)
- [ ] Dependencies point inward
- [ ] Module boundaries respected
- [ ] Interfaces are deep (high leverage)
- [ ] No circular dependencies
- [ ] ADRs followed
```

### Security Review
```
- [ ] No hardcoded secrets
- [ ] Input validation on all user input
- [ ] Auth checks on all endpoints
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CSRF protection
- [ ] Rate limiting
- [ ] Dependency vulnerabilities checked
```

### Quality Review
```
- [ ] Test coverage meets threshold (>80%)
- [ ] Edge cases tested
- [ ] Error handling comprehensive
- [ ] Naming descriptive and consistent
- [ ] No code duplication
- [ ] Performance considered
```

## Code Review Culture (from Google + Stripe + Meta)

### Google Code Review
- **Mandatory** — at least 1 LGTM required
- **OWNERS files** — auto-assign by directory
- **Automated checks first** — lint, test, type check before human
- **Time-to-review SLA** — hours, not days

### Stripe Code Review
- **Knowledge-sharing** — review as learning
- **Substantive engagement** — understand the change
- **Clear PR descriptions** — what, why, how, testing

### Meta Code Review
- **Ship it culture** — approve quickly, don't block on nitpicks
- **"Ship it" with comments** — LGTM but note improvements

### Review Anti-Patterns
- **Bikeshedding** — arguing about trivial things
- **Rubber stamping** — approving without reading
- **Slow reviews** — PRs sitting for days kills velocity

### PR Template
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
```

## Pitfalls

1. **Don't run on every PR** — use for significant changes
2. **Don't auto-fix architecture issues** — create issues
3. **Don't ignore medium/low findings** — accumulate into tech debt
4. **Don't skip re-running CI after fixes**
5. **Don't bikeshed** — focus on logic, not style
6. **Don't rubber stamp** — actually read the diff
