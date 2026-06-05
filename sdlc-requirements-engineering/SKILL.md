---
name: sdlc-requirements-engineering
description: "Requirements gathering, user stories, acceptance criteria, BDD/Gherkin, stakeholder analysis, estimation (story points, Monte Carlo), backlog prioritization (WSJF, RICE, MoSCoW), sprint planning. Includes YC/Amazon working backwards."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, requirements, user-stories, bdd, estimation, backlog, sprint, planning, scrum, agile, ycombinator, amazon]
    related_skills: [sdlc-architecture-design, sdlc-prd-to-production, github-issues, linear]
---

# Requirements Engineering & Planning

Covers the full planning phase: requirements → user stories → estimation → backlog → sprint. Includes YC startup pragmatism and Amazon Working Backwards.

## When to Use

Trigger when user:
- Writes user stories, acceptance criteria, or requirements docs
- Needs estimation (story points, t-shirt sizing, planning poker, Monte Carlo)
- Prioritizes backlog (WSJF, RICE, MoSCoW, Kano)
- Plans sprints or calculates velocity
- Creates stakeholder maps or RACI matrices
- Sets up BDD/Gherkin feature files
- Starts a new project or feature from scratch

## Step 1: User Stories & Acceptance Criteria

### Standard Format
```
As a [role], I want [feature], so that [benefit].
```

### Job Story Format (JTBD)
```
When [situation], I want to [motivation], so I can [outcome].
```

### Gherkin (BDD) Acceptance Criteria
```gherkin
Feature: Password Reset
  Scenario: User requests password reset via email
    Given a registered user with email "user@example.com"
    When they request a password reset
    Then they receive an email within 60 seconds
    And the reset link expires after 24 hours
    And old sessions are invalidated after reset
```

### GitHub CLI Workflow
```bash
# Create issue as user story
gh issue create \
  --title "User can reset password via email" \
  --body "As a registered user, I want to reset my password via email, so that I can regain access to my account." \
  --label "user-story,P1,sprint-3" \
  --project "Sprint Board"

# Add acceptance criteria as checklist
gh issue edit 42 --body "
## User Story
As a registered user, I want to reset my password via email...

## Acceptance Criteria
- [ ] Email sent within 60 seconds of request
- [ ] Link expires after 24 hours
- [ ] Password meets complexity requirements
- [ ] Old sessions invalidated after reset
"
```

### Requirements as Code (in-repo)
```bash
mkdir -p docs/requirements
cat > docs/requirements/REQ-001.md << 'EOF'
---
id: REQ-001
priority: P0
status: draft
type: functional
stakeholder: product-team
sprint: 2025-S1
---
# User Authentication
As a user, I want to log in with OAuth2 so that I can access my account securely.

## Acceptance Criteria
- [ ] Supports Google and GitHub OAuth providers
- [ ] Session persists for 7 days
- [ ] Refresh token rotation implemented
EOF

# Validate structure
npx markdownlint docs/requirements/
```

## Step 2: Stakeholder Analysis

### RACI Matrix (as code)
```yaml
# docs/raci.yaml
deliverables:
  auth_system:
    responsible: backend-team
    accountable: tech-lead
    consulted: security-team, product-manager
    informed: cto, qa-team
```

### Power/Interest Grid (Mendelow)
```
High Power, High Interest   → Manage Closely (CTO, Tech Lead)
High Power, Low Interest    → Keep Satisfied (Security, Legal)
Low Power, High Interest    → Keep Informed (End Users, Support)
Low Power, Low Interest     → Monitor (Marketing, Sales)
```

## Step 3: Estimation Techniques

### Story Points (Fibonacci: 1, 2, 3, 5, 8, 13, 21)
- Abstract relative sizing
- Accounts for complexity, uncertainty, effort
- Velocity = average points completed per sprint

### T-Shirt Sizing → Points
| Size | Points |
|------|--------|
| XS   | 1      |
| S    | 2      |
| M    | 3      |
| L    | 5      |
| XL   | 8      |
| XXL  | 13     |

### Three-Point Estimation (PERT)
```
E = (O + 4M + P) / 6
SD = (P - O) / 6
O=optimistic, M=most likely, P=pessimistic
```

### Monte Carlo Simulation
```python
import numpy as np
throughput = np.random.poisson(12, size=10000)
remaining = 50
sprints = []
for _ in range(10000):
    done, s = 0, 0
    while done < remaining:
        done += np.random.poisson(12)
        s += 1
    sprints.append(s)
print(f"P50: {np.percentile(sprints, 50)}, P85: {np.percentile(sprints, 85)}, P95: {np.percentile(sprints, 95)}")
```

### Planning Poker Tools
- planningpoker.com — free, browser-based
- Slack: /poker command
- Discord: Planning Poker Bot (`!start`, `!vote`, `!reveal`)

## Step 4: Backlog Prioritization

### WSJF (Weighted Shortest Job First)
```
WSJF = (Business Value + Time Criticality + Risk Reduction) / Job Size
```

### RICE Score
```
RICE = (Reach × Impact × Confidence) / Effort
```

### MoSCoW
- **Must have** — critical for launch
- **Should have** — important but not critical
- **Could have** — nice to have
- **Won't have** — explicitly out of scope

### Kano Model
| Type | Description | Strategy |
|------|-------------|----------|
| Must-be | Expected, absence = dissatisfaction | Implement first |
| One-dimensional | More = more satisfied | Optimize |
| Attractive | Unexpected delight | Differentiate |
| Indifferent | No impact on satisfaction | Deprioritize |
| Reverse | More = less satisfied | Avoid |

## Step 5: Sprint Planning

### Velocity-Based Capacity
```python
sprints = [
    {"committed": 30, "completed": 26},
    {"committed": 28, "completed": 28},
    {"committed": 32, "completed": 25},
    {"committed": 26, "completed": 26},
    {"committed": 30, "completed": 29},
]
velocities = [s["completed"] for s in sprints[-5:]]
avg = sum(velocities) / len(velocities)
print(f"Average velocity: {avg:.1f}")
print(f"Recommended commitment: {int(avg * 0.85)}-{int(avg)} points")
```

### Linear Cycles
```bash
linear cycle create --team ENG --name "Cycle 12" \
  --start 2025-01-06 --end 2025-01-20
```

## Step 6: PRD Generation (from mattpocock/skills)

### PRD Template
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

### PRD-to-Issues Pipeline
1. **PRD** → conversation context → publish to issue tracker
2. **Triage** → categorize (bug/enhancement), assign state
3. **To Issues** → break into vertical slices (tracer bullets)
4. Each issue = thin vertical slice through ALL layers

### Issue States (mattpocock triage)
```
needs-triage → needs-info → ready-for-agent → ready-for-human → wontfix
```

### Vertical Slice Rules
- Each slice delivers a narrow but COMPLETE path through every layer
- A completed slice is demoable/verifiable on its own
- Prefer many thin slices over few thick ones

## Step 7: Amazon Working Backwards

Start from the customer experience, work backwards to the technology:

```markdown
# Working Backwards: [Feature Name]

## Press Release
[Company] today announced [feature] that enables [customer] to [benefit].

## FAQ
**What is it?** [One sentence]
**Who is it for?** [Target user]
**Why now?** [Urgency/rationale]
**How does it work?** [Technical summary]

## Customer Experience
Step-by-step walkthrough of the user journey.

## Technical Design
Architecture, data model, API contracts.
```

## Step 8: YC/Startup Requirements

### Core Philosophy
- "Make something people want" — talk to users before writing specs
- "If you're not embarrassed by your first release, you launched too late"
- Build something a small number of users love, not a large number like
- Ship weekly (or more often). Speed of iteration > completeness

### Customer Development
1. **Talk to 10+ users** before writing any spec
2. **Watch them work** — don't ask what they want, observe what they do
3. **Find the pain** — what's broken in their current workflow?
4. **Build the smallest thing** that solves that pain
5. **Ship it** — get feedback, iterate

### YC Engineering Pragmatism
- **Monolith first** — split services only when needed
- **Boring technology** — Rails, Django, Next.js
- **Technical debt is fine early** — speed > perfection
- **Don't build for scale you don't have**
- **Do things that don't scale** — manual processes before automation

## Step 9: Story Mapping (from Jeff Patton)

### Visual Story Mapping
```
User Activities (backbone)
├── Browse Products
├── Add to Cart
├── Checkout
└── Track Order

User Tasks (walking skeleton)
├── Search products
├── Filter results
├── View product details
├── Add item to cart
├── Apply coupon
├── Enter payment
├── Confirm order
└── View order status

Release Slices
├── Release 1: Browse + basic cart
├── Release 2: Checkout + payment
└── Release 3: Order tracking + coupons
```

### Story Mapping Rules
1. **Backbone** = user activities left-to-right
2. **Walking skeleton** = minimum tasks per activity
3. **Release slices** = horizontal cuts for each release
4. **Prioritize top-down** — most valuable tasks first

## Step 10: Impact Mapping (from Gojko Adzic)

### Impact Map Structure
```
Goal: Increase conversion by 20%
├── Actor: Visitor
│   ├── Impact: Find products faster
│   │   ├── Deliverable: Search autocomplete
│   │   └── Deliverable: Category filters
│   └── Impact: Trust the site
│       ├── Deliverable: Security badges
│       └── Deliverable: Customer reviews
├── Actor: Returning Customer
│   └── Impact: Checkout faster
│       ├── Deliverable: Saved payment methods
│       └── Deliverable: One-click checkout
```

### Impact Mapping Rules
1. **Goal** = measurable business outcome
2. **Actors** = who can help/hinder the goal
3. **Impacts** = behavior changes needed
4. **Deliverables** = features that create impacts

## Pitfalls

1. **Don't estimate in hours** — story points abstract away individual speed differences
2. **Velocity needs 3-5 sprints** to stabilize — don't commit to early averages
3. **WSJF denominator (size) must be estimated first**
4. **Gherkin tests must be executable** — .feature files without implementations rot
5. **Requirements in docs/ without validation will rot** — use pre-commit hooks
6. **Don't write PRDs without talking to users first** — YC rule #1
7. **Don't over-specify early** — leave room for iteration
