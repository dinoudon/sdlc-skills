---
name: sdlc-prd-to-production
description: "End-to-end orchestration: PRD → spec → plan → implement → simplify → review → ship → deploy. 8-stage pipeline chaining multiple skills. Includes YC startup fast track."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, pipeline, prd, production, orchestration, ycombinator, startup, end-to-end]
    related_skills: [sdlc-requirements-engineering, sdlc-architecture-design, sdlc-testing-qa, sdlc-deployment, sdlc-adversarial-review]
---

# PRD-to-Production Pipeline

8-stage orchestration: PRD → spec → plan → implement → simplify → review → ship → deploy. Chains multiple skills. Includes YC startup fast track mode.

## When to Use

Trigger when user:
- Starts a new feature from scratch
- Needs end-to-end development pipeline
- Wants to go from idea to production
- Asks "how do I build this from start to finish?"

## The 8 Stages

```
1. PRD        → requirements document (YAML frontmatter + markdown)
2. Spec       → non-PRD documents (README, architect notes, previews)
3. Plan       → bite-sized tasks, file paths, code
4. Implement  → execute plan with verification
5. Simplify   → reduce LOC via deletion, consolidation, patterns
6. Review     → adversarial review (3 agents parallel)
7. Ship       → commit, bump version, update changelog, create PR
8. Deploy     → canary → monitor → rollout
```

## Stage 1: PRD

### PRD Template (from mattpocock/skills)
```markdown
## Problem Statement
The problem from the user's perspective.

## Solution
The solution from the user's perspective.

## User Stories
1. As an <actor>, I want a <feature>, so that <benefit>

## Technical Design
- Architecture changes
- Database schema changes
- API contract changes

## Testing Seams
- Where to test (highest seam possible)

## Non-Functional Requirements
- Performance, security, accessibility targets
```

### Commands
```bash
# Generate PRD from conversation
hermes skill use sdlc-requirements-engineering

# Publish to issue tracker
hermes skill use github-issues
```

## Stage 2: Spec (non-PRD)

For smaller changes, README or architect notes may suffice.
- Minimal sketch for visual work
- Responsive states, interaction annotations

## Stage 3: Plan

### Planning Process
1. Read PRD + non-PRD docs
2. Confirm latest codebase state
3. Decompose into vertical slices
4. Each task: exact file path + code sketch
5. Commit plan to .hermes/plans/

### Vertical Slices
Each slice = narrow but COMPLETE path through every layer:
- Frontend UI component
- API endpoint
- Database schema
- Tests

## Stage 4: Implement

### Implementation Rules
- Follow existing patterns (imports, naming, structure)
- No new dependencies without approval
- Inline single-use code
- Test each task before moving to next
- Commit after each task

### Verification Loop
```bash
# After each task
npm run build && npm run lint && npm test
```

## Stage 5: Simplify

### Simplification Process
1. Verify: lint, build, test pass
2. Scan for multi-layer duplication
3. Apply known extraction patterns
4. Re-verify
5. Show net changes to user

### Rules
- Smallest correct implementation
- Prefer editing over adding
- Prefer deleting over editing
- NEVER add backward compatibility
- NEVER add migration/adapter unless instructed

## Stage 6: Review

```bash
hermes skill use sdlc-adversarial-review
```

3 agents review in parallel:
- Architecture reviewer
- Security reviewer
- Quality reviewer

## Stage 7: Ship

### Ship Workflow (from gstack)
```bash
# Merge base branch
git fetch origin main && git merge origin/main

# Run tests
npm test

# Review diff
git diff main

# Bump version
npm version patch  # or minor/major

# Update changelog
git log --oneline > CHANGELOG.md

# Commit and push
git add -A && git commit -m "feat: new feature"
git push origin feature/new-feature

# Create PR
gh pr create --title "feat: new feature" --body "Closes #42"
```

## Stage 8: Deploy

### Progressive Delivery
```bash
# Canary deploy (1% traffic)
kubectl argo rollouts set image myapp myapp=myapp:v1.2.0

# Monitor for 5 minutes
# If healthy → promote
kubectl argo rollouts promote myapp

# If unhealthy → auto-rollback
```

## YC Startup Mode (Fast Track)

For early-stage startups, compress the pipeline:

### YC Fast Track
```
1. Talk to 10 users → find pain
2. Build MVP (1-2 weeks, monolith, boring tech)
3. Ship embarrassingly early
4. Get feedback, iterate weekly
5. Measure retention, not vanity metrics
```

### YC Pragmatism
- **Monolith first** — split services only when needed
- **Boring technology** — Rails, Django, Next.js
- **Technical debt is fine early** — speed > perfection
- **Don't build for scale you don't have**
- **Do things that don't scale** — manual before automation
- **Founder writes code** — no code reviews for first 6 months

### Startup vs Enterprise Pipeline
| Stage | Startup | Enterprise |
|-------|---------|------------|
| PRD | 1-page doc | Full spec |
| Spec | README | Architect notes |
| Plan | Mental | Documented |
| Implement | Solo | Team |
| Simplify | Post-MVP | Pre-merge |
| Review | Self-review | 3-agent adversarial |
| Ship | Push to main | PR + CI |
| Deploy | git push | Canary + monitoring |

## Pipeline Metrics

```yaml
metrics:
  lead_time: "< 1 week"           # PRD → production
  deploy_frequency: "> 1/day"
  change_failure_rate: "< 5%"
  time_to_restore: "< 1 hour"
```

## Pitfalls

1. **Don't follow all 8 stages for trivial changes**
2. **Don't skip simplify stage** — tech debt compounds
3. **Don't write PRD without talking to users first**
4. **Don't deploy without monitoring**
5. **Don't use enterprise pipeline for startups** — too slow
6. **Don't use startup pipeline for regulated industries**
