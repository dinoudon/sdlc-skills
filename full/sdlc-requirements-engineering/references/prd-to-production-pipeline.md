# PRD-to-Production Pipeline

Full 8-stage orchestration from requirement to deployed code (from earp-kit).

## Pipeline Stages

```
1. PRD Generation       → Comprehensive PRD, all requirements as must-deliver
2. Specification        → Structured implementation plan (plan mode)
3. Plan Refinement      → Multi-model counsel (2+ models review independently)
4. Implementation       → Multi-agent development (dispatched by change type)
5. Simplification       → Code clarity and consistency pass
6. Adversarial Review   → 3 parallel reviewers (arch, security, quality)
7. Feedback Address     → Resolve review findings
8. Self-Healing Deploy  → 7-phase merge to production with auto-healing
```

## Stage Details

### 1. PRD Generation
- Takes feature description from user
- Generates comprehensive PRD with all requirements as must-deliver (no deferred scope)
- Publishes PRD as GitHub Issue
- Triggers spec generation

### 2. Specification
- Enters plan mode
- Gathers context from standards index (conventions, architecture, testing patterns)
- Produces structured implementation plan
- Identifies critical files, patterns to follow, tests to write

### 3. Plan Refinement
- Implementation plan reviewed by multiple models independently
- Each provides independent perspective on approach
- Plan is refined based on counsel
- Key: models review in parallel, no anchoring bias

### 4. Multi-Agent Implementation
- Dispatches specialized agents based on change type:
  - Backend agent for API/service changes
  - Frontend agent for UI changes
  - Infrastructure agent for IaC changes
- Each agent gets relevant context (files, patterns, standards)

### 5. Simplification
- Code clarity and consistency pass
- Naming conventions, file organization
- Remove dead code, consolidate duplicates

### 6. Adversarial Review
- 3 parallel reviewers: Architecture, Security, Quality
- Orchestrator synthesizes, deduplicates, prioritizes
- Autonomous fix for high-severity issues

### 7. Feedback Address
- Human reviews remaining findings
- Address or defer with justification

### 8. Self-Healing Deploy
- Merge → CI monitoring → auto-fix failures → migrations → terraform → verify → promote
- See sdlc-cicd-pipeline skill for full 7-phase details

## When to Use This Pipeline
- Major features (multi-day effort)
- Cross-cutting changes (backend + frontend + infra)
- Security-sensitive features (auth, payments, data)

## When NOT to Use
- Bug fixes (use simpler branch → fix → PR → merge)
- Config changes (direct PR)
- Hotfixes (skip review, fast-track deploy)
