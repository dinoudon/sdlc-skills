# SDLC Skills — Handoff Document

**Date:** 2026-06-09
**Status:** 12 skills (11 complete × 3 tiers + api-documentation moderate-only)
**Repository:** /root/sdlc-skills/ (local), `dinoudon/sdlc-skills` (GitHub)

---

## What This Is

11 SDLC skills covering the full software development lifecycle, each in 3 size tiers:

| Tier | Target Lines | Use Case |
|:-----|:-------------|:---------|
| **slim** | ~800 | Quick reference, token-constrained contexts |
| **moderate** | ~2000 | Balanced — actionable code + essential theory |
| **full** | ~4500-6200 | Deep reference, comprehensive coverage |

## Skills Inventory

| Skill | Full | Moderate | Slim |
|:------|:-----|:---------|:-----|
| api-documentation | — | 1566 | — |
| sdlc-adversarial-review | 4805 | 1999 | 803 |
| sdlc-architecture-design | 5354 | 1901 | 1565 |
| sdlc-cicd-pipeline | 4420 | 1928 | 1091 |
| sdlc-deployment | 6156 | 1905 | 807 |
| sdlc-developer-tooling | 4747 | 2095 | 1389 |
| sdlc-observability | 5068 | 2021 | 813 |
| sdlc-prd-to-production | 4527 | 1773 | 759 |
| sdlc-requirements-engineering | 4467 | 1965 | 761 |
| sdlc-retrospective | 4790 | 2198 | 652 |
| sdlc-testing-qa | 5030 | 2065 | 798 |
| sdlc-throwaway-projects | 1762 | 1016 | 741 |
| **TOTAL** | **51,122** | **22,432** | **10,179** |

## Directory Structure

```
/root/sdlc-skills/
├── README.md
├── HANDOFF.md          ← this file
├── full/               ← 10 skills, ~4500-6200 lines each
│   ├── sdlc-adversarial-review/SKILL.md
│   ├── sdlc-architecture-design/SKILL.md
│   ├── sdlc-cicd-pipeline/SKILL.md
│   ├── sdlc-deployment/SKILL.md
│   ├── sdlc-developer-tooling/SKILL.md
│   ├── sdlc-observability/SKILL.md
│   ├── sdlc-prd-to-production/SKILL.md
│   ├── sdlc-requirements-engineering/SKILL.md
│   ├── sdlc-retrospective/SKILL.md
│   ├── sdlc-testing-qa/SKILL.md
│   └── sdlc-throwaway-projects/SKILL.md
├── moderate/           ← 10 skills, ~1800-2200 lines each
│   └── (same structure)
└── slim/               ← 10 skills, ~650-1565 lines each
    └── (same structure)
```

## Version Numbers

Each tier has a version suffix:
- **Full:** `4.8.0` / `4.9.0` (original)
- **Moderate:** `4.8.0-moderate` / `4.9.0-moderate`
- **Slim:** `4.8.0-slim` / `4.9.0-slim`

## How Moderate Versions Were Built

1. Read full SKILL.md, identify section boundaries (`grep -n '^## '`)
2. Remove duplicate/verbose/theoretical sections via `sed` line-range deletes
3. If under target (~2000 lines), add back essential sections
4. Update frontmatter: version suffix, add `sdlc-moderate` tag
5. Verify line count + frontmatter correctness

Key pattern: `sed -e 'START,ENDd'` for cuts, `cat top.md additions.md bottom.md` for insertions.

## How Slim Versions Were Built

1. Start from full SKILL.md
2. Aggressive condensation — keep only: When to Use, core steps (1-9), essential templates, pitfalls
3. Remove: all deep-dives, duplicate sections, verbose explanations, tangential topics
4. Update frontmatter: version suffix, add `sdlc-slim` tag

## Git History

```
8b168f7 v5.0.0: Three-tier structure (slim/moderate/full)
ee63489 v4.9.0: Epoch 30 - Architecture patterns, Team Topologies, Multi-tenancy, FinOps
38b6ac8 v4.8.0: Epoch 29 - Quality review #8 - fix cross-ref, normalize versions
2764ad8 v4.8.0: Epoch 28 - OWASP SAMM, DevSecOps, CSPM, LGTM stack, Honeycomb, eBPF
d96c793 v4.7.0: Epoch 27 - Staff eng, SRE deep dive, blameless postmortems
```

## Key Decisions Made

1. **Moderate = ~2000 lines, not strict 2000** — acceptable range is 1700-2200. Exact cuts depend on section boundaries.
2. **Slim varies more** — 650-1565 lines. Some skills (architecture-design) have more essential content that can't be cut below ~1500.
3. **Frontmatter metadata preserved** — `name`, `author`, `license`, `platforms` unchanged across tiers. Only `version` suffix and `tags` array modified.
4. **Content strategy:** Keep actionable code/examples/templates/checklists/pitfalls. Remove verbose explanations, duplicate concepts, theoretical background, deep-dive expansions.

## What's NOT Done

- **No automated testing** — skills are markdown, no lint/validation framework exists
- **No slim handoff was explicitly requested** — slim versions existed before this session
- **No GitHub push** — changes are local only. Push when ready:
  ```bash
  cd /root/sdlc-skills
  git add -A
  git commit -m "v5.1.0: Complete moderate tier for all 10 skills"
  git push origin main
  ```

## Session History

This work spanned multiple sessions:
- **Epochs 1-28:** Built full skills incrementally (v1.0 → v4.8.0)
- **Epoch 29-30:** Added architecture patterns, Team Topologies, multi-tenancy, FinOps
- **v5.0.0:** Created three-tier structure (slim/moderate/full)
- **This session:** Completed all 10 moderate versions (4 new: cicd-pipeline, prd-to-production, requirements-engineering, retrospective)
