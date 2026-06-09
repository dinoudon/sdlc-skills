---
name: sdlc-requirements-engineering
description: "Requirements: user story mapping, BDD/Gherkin, acceptance criteria, impact mapping, example mapping, specification by example, JTBD, RICE scoring, WSJF prioritization, user story splitting, NFR patterns, requirements traceability, OKR alignment, design thinking, Lean UX, design sprint, dual-track agile, AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility, event storming, domain storytelling, platform team requirements, cost of delay, requirements elicitation, ATDD, strategic DDD, tactical DDD."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc-slim, sdlc, requirements, user-stories, bdd, gherkin, acceptance-criteria, impact-mapping, example-mapping, specification-by-example, jtbd, rice-scoring, wsjf, story-splitting, nfr, traceability, okr, design-thinking, lean-ux, design-sprint, dual-track-agile, ai-ml, stride, owasp-asvs, wcag-2.2, accessibility, event-storming, domain-storytelling, platform-teams, cost-of-delay, elicitation, atdd, strategic-ddd, tactical-ddd]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-prd-to-production]
---

# Requirements Engineering

User story mapping, BDD/Gherkin, acceptance criteria, impact mapping, example mapping, specification by example, JTBD, RICE/WSJF prioritization, story splitting, NFR patterns, traceability, OKR alignment, design thinking, Lean UX, design sprint, dual-track agile, AI/ML requirements, STRIDE/OWASP security, WCAG 2.2 AA, event storming, domain storytelling, platform team requirements, cost of delay, elicitation techniques, ATDD, strategic/tactical DDD.

## When to Use

Trigger when user:
- Writes user stories, acceptance criteria, or BDD/Gherkin scenarios
- Creates impact maps, story maps, or example mapping sessions
- Prioritizes features (RICE, WSJF, cost of delay)
- Splits large user stories or defines NFRs
- Runs design thinking/Lean UX/design sprint workshops
- Specifies AI/ML, security (STRIDE/OWASP), or accessibility (WCAG 2.2) requirements
- Runs event storming or domain storytelling workshops
- Models domain with strategic/tactical DDD
- Defines platform team requirements (API contracts, SLAs, DX)
- Conducts requirements elicitation (interviews, surveys, observation)

## Step 1: User Story Mapping

Source: Jeff Patton, "User Story Mapping" (2014)

2D visualization replacing flat backlog. Horizontal = user activities (backbone). Vertical = priority/slices.

```
USER STORY MAP
==============
[Backbone - Activities in order]
  Act1          Act2          Act3          Act4
  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐
  │Task 1.1 │  │Task 2.1 │  │Task 3.1 │  │Task 4.1 │  <- Walking Skeleton
  │Task 1.2 │  │Task 2.2 │  │Task 3.2 │  │Task 4.2 │
  ├─────────┤  ├─────────┤  ├─────────┤  ├─────────┤
  │Task 1.3 │  │Task 2.3 │  │Task 3.3 │  │Task 4.3 │  <- MVP Slice
  │Task 1.4 │  │Task 2.4 │  │Task 3.4 │  │Task 4.4 │
  ├─────────┤  ├─────────┤  ├─────────┤  ├─────────┤
  │Task 1.5 │  │Task 2.5 │  │Task 3.5 │  │Task 4.5 │  <- Release 2
  └─────────┘  └─────────┘  └─────────┘  └─────────┘
```

**Key practices:**
- Build map collaboratively with whole team
- Slice horizontally to define releases
- Each task below backbone = candidate user story
- Use to find MVP (minimum viable product)

## Step 2: BDD / Gherkin

Source: https://cucumber.io/docs/gherkin/

### pytest-bdd Implementation
```python
# features/login.feature
Feature: Login
  Scenario: Successful login
    Given user is on login page
    When user enters valid credentials
    Then user sees dashboard

# tests/test_login.py
from pytest_bdd import scenarios, given, when, then, parsers

scenarios('../features/login.feature')

@given('user is on login page')
def login_page(browser):
    browser.get('/login')

@when('user enters valid credentials')
def enter_credentials(browser):
    browser.fill('#username', 'user@example.com')
    browser.fill('#password', 'secret')
    browser.click('#submit')

@then('user sees dashboard')
def dashboard_visible(browser):
    assert browser.is_visible('#dashboard')
```

### Gherkin Anti-Patterns
- GUI-level steps (too brittle)
- Mega-scenarios (too many steps)
- Vague steps ("I do stuff")
- Coupling steps to implementation

**Best practice:** write declarative, business-language steps.

## Step 3: Acceptance Criteria

### Pattern A: Given/When/Then (Scenario-based)
```
AC-1: Successful order placement
  GIVEN user has items in cart
  AND user is logged in
  WHEN user clicks "Place Order"
  THEN order is created with status "pending"
  AND confirmation email is sent
  AND cart is cleared

AC-2: Order fails with empty cart
  GIVEN user cart is empty
  WHEN user clicks "Place Order"
  THEN error message "Cart is empty" is displayed
  AND no order is created
```

### Pattern B: Checklist Format
```
Acceptance Criteria for: User Registration
  [ ] Email field validates format (RFC 5322)
  [ ] Password minimum 8 chars, 1 upper, 1 number, 1 special
  [ ] Duplicate email shows "Email already registered"
  [ ] Success redirects to verification page
  [ ] Verification email sent within 60 seconds
  [ ] Account inactive until email verified
```

## Step 4: Impact Mapping

Source: Gojko Adzic, "Impact Mapping" (2012), https://www.impactmapping.org/

```
IMPACT MAP: [Feature/Project Name]
===================================

GOAL: Increase conversion rate from 2% to 5% by Q4
│
├── ACTOR: New visitor
│   ├── IMPACT: Understands product value in <30 seconds
│   │   ├── DELIVERABLE: Hero section with value prop
│   │   └── DELIVERABLE: Product demo video
│   └── IMPACT: Finds relevant product quickly
│       ├── DELIVERABLE: Category filtering
│       └── DELIVERABLE: Search with autocomplete
│
├── ACTOR: Returning visitor
│   └── IMPACT: Completes abandoned purchase
│       ├── DELIVERABLE: Cart reminder emails
│       └── DELIVERABLE: One-click reorder
│
└── ACTOR: Internal sales team
    └── IMPACT: Focuses on high-value leads
        ├── DELIVERABLE: Lead scoring dashboard
        └── DELIVERABLE: CRM integration
```

**Rules:** One goal per map (measurable, time-bound). Actors = anyone who influences outcome. Impacts = behavior changes. Deliverables = things we build. Cut branches that don't connect to goal.

## Step 5: Example Mapping

Source: Matt Wynne, Cucumber team, https://cucumber.io/docs/bdd/example-mapping/

25-minute workshop. 4 card colors: YELLOW = business rule, GREEN = example, RED = question, BLUE = user story.

```
EXAMPLE MAPPING SESSION
=======================
USER STORY (Blue): As a customer, I want to apply a discount code.

RULE (Yellow): Valid discount codes reduce total by percentage
  Example (Green): Code "SAVE20" on $100 order -> $80 total

RULE (Yellow): Expired codes are rejected
  Example (Green): Code "SUMMER24" (expired) -> error message
  Question (Red): What exact error message to show?

RULE (Yellow): Only one code per order
  Example (Green): Apply "SAVE20" then "SAVE10" -> error
  Question (Red): Replace existing or reject second?

TIMEBOX: 25 minutes. If questions remain, spike/investigate.
```

**Protocol:** Pick one story → state rules (yellow) → write examples (green) → capture questions (red) → stop at 25 min → no red cards = ready to automate.

## Step 6: Specification by Example

Source: Gojko Adzic, "Specification by Example" (2011)

```
WORKFLOW:
  1. DERIVE SCOPE - identify what to specify (not everything)
  2. SPECIFY collaboratively - whole team, Example Mapping sessions
  3. FORMALIZE - turn examples into Gherkin or structured format
  4. AUTOMATE - implement step definitions (thin layer, not GUI)
  5. VALIDATE - run specs as tests; failures = misunderstandings
  6. EVOLVE - refactor living docs, keep in sync with system
```

## Step 7: Jobs-to-be-Done (JTBD)

Source: Clayton Christensen; Anthony Ulwick

### Job Statement Format
```
When [SITUATION], I want to [MOTIVATION], so I can [EXPECTED OUTCOME].
```

### JTBD Interview Framework
```
1. CONTEXT: "Tell me about the last time you..."
2. STRUGGLE: "What was hardest about that?"
3. TRIGGERS: "What caused you to look for a solution?"
4. ALTERNATIVES: "What did you try before this?"
5. DECISION: "What made you choose this solution?"
6. EXPERIENCE: "How well does it do the job?"

ANTI-PATTERNS:
  - Don't ask "Would you use X?" (hypothetical bias)
  - Don't ask about preferences (revealed > stated)
  - Do ask about last specific instance (concrete memory)
  - Do ask about workarounds (shows underserved needs)
```

### Outcome-Driven Innovation (ODI)
```
OPPORTUNITY SCORE = Importance + (Importance - Satisfaction)
  Score > 10 = opportunity for innovation
```

## Step 8: RICE Scoring

Source: Intercom

```
RICE SCORE = (Reach × Impact × Confidence) / Effort

REACH: # of people affected per time period
IMPACT: 3=massive, 2=high, 1=medium, 0.5=low, 0.25=minimal
CONFIDENCE: 1.0=high(data), 0.8=medium(some data), 0.5=low(intuition), 0.2=guess
EFFORT: person-months (whole team)
```

### RICE Scoring Example
```
Feature            | Reach | Impact | Confidence | Effort | RICE
-------------------|-------|--------|------------|--------|------
SSO integration    | 500   | 2      | 0.8        | 3      | 267
Export to CSV      | 2000  | 0.5    | 1.0        | 0.5    | 2000
Dark mode          | 3000  | 0.25   | 0.5        | 1      | 375
API rate limiting  | 100   | 3      | 1.0        | 2      | 150
Two-factor auth    | 800   | 2      | 0.8        | 2      | 640

PRIORITY: Export to CSV > 2FA > Dark mode > SSO > Rate limiting
NOTE: Confidence < 50% should trigger research spike.
```

## Step 9: WSJF & Cost of Delay

Source: SAFe framework; Don Reinertsen

```
WSJF = Cost of Delay / Job Duration

COST OF DELAY = User/Business Value + Time Criticality + Risk Reduction/Opportunity Enablement

Each component: Fibonacci scale (1, 2, 3, 5, 8, 13, 20)
Job Duration: relative size (also Fibonacci)
IMPORTANT: Normalize by smallest job. Don't use absolute time.
```

### WSJF Scoring Example
```
BACKLOG ITEM          | Biz Value | Time Crit | Risk Red | CoD | Size | WSJF
---------------------|-----------|-----------|----------|-----|------|-----
Fix payment bug      | 20        | 13        | 13       | 46  | 3    | 15.3
GDPR compliance      | 13        | 20        | 20       | 53  | 8    | 6.6
Mobile responsive    | 13        | 8         | 5        | 26  | 5    | 5.2
New report feature   | 8         | 5         | 3        | 16  | 13   | 1.2
API v2 migration     | 5         | 3         | 8        | 16  | 8    | 2.0

PRIORITY: Fix payment bug > GDPR > Mobile responsive > API v2 > New report
```

### CD3 (Cost of Delay Divided by Duration)
```
CD3 = Cost of Delay / Duration
Cost of Delay = Revenue lost/week + penalty costs + risk costs

Example:
  GDPR compliance: $200K/week penalty + $10K/week risk = $210K/week CoD
  Duration: 8 weeks → CD3 = $26.25K/week of investment
  vs. Dark mode: $2K/week CoD, 2 weeks → CD3 = $1K/week
  Priority: GDPR first despite longer duration
```

### WSJF Rules
- Score items relative to each other, not absolute
- Smallest item gets score of 1 for each dimension
- Duration must include entire value stream (not just dev)
- Re-score when new items enter backlog
- Split items with duration > 13 points before scoring

## Step 10: User Story Splitting Patterns

Source: Richard Lawrence, https://agileforall.com/patterns-for-splitting-user-stories/

### Pattern 1: Workflow Steps
```
EPIC: User can purchase product online
SPLIT:
  1. User can add product to cart
  2. User can enter shipping address
  3. User can enter payment information
  4. User can review order before submission
  5. User receives order confirmation
```

### Pattern 2: CRUD Operations
```
EPIC: Admin can manage discount codes
SPLIT:
  1. Admin can CREATE discount code
  2. Admin can READ/view list of active codes
  3. Admin can UPDATE discount code
  4. Admin can DELETE/deactivate discount code
```

### Pattern 3: Business Rule Variations
```
EPIC: System calculates shipping cost
SPLIT:
  1. Free shipping for orders over $50
  2. Standard shipping rate for domestic orders
  3. International shipping rate (by zone)
  4. Expedited shipping surcharge
```

### Story Splitting Decision Tree
```
Is the story > 1 sprint?
├── YES: Can you identify sequential workflow steps?
│   ├── YES: Split by workflow steps
│   └── NO: Does it handle different data types?
│       ├── YES: Split by data variations
│       └── NO: Is it a CRUD entity?
│           ├── YES: Split by CRUD operations
│           └── NO: Does it have multiple business rules?
│               ├── YES: Split by business rules
│               └── NO: Does it have happy/edge paths?
│                   ├── YES: Split by operations
│                   └── NO: Spike it first, then re-split
└── NO: Ready for sprint
```

## Step 11: Non-Functional Requirements Patterns

### Performance Budgets
```
Metric                    | Budget        | Measurement
--------------------------|---------------|------------------
Time to interactive (TTI) | < 3s on 3G    | Lighthouse CI
Largest contentful paint  | < 2.5s        | Core Web Vitals
Cumulative layout shift   | < 0.1         | Core Web Vitals
First input delay         | < 100ms       | Real User Monitoring
API response time (p95)   | < 200ms       | APM (Datadog/NR)
API response time (p99)   | < 500ms       | APM
Error rate                | < 0.1%        | Error tracking
Throughput                | > 1000 rps    | Load testing

ENFORCEMENT: CI pipeline fails if Lighthouse score < 90.
```

### Security Requirements (STRIDE + OWASP ASVS)

```
THREAT MODEL (STRIDE):
  Spoofing        -> Authentication requirements
  Tampering       -> Integrity / input validation
  Repudiation     -> Logging / audit trail
  Information Disclosure -> Encryption / access control
  Denial of Service     -> Rate limiting / resource limits
  Elevation of Privilege -> Authorization / least privilege

OWASP ASVS MAPPING:
  V2  Authentication    -> MFA, password policy, session management
  V4  Access Control     -> RBAC, IDOR testing, admin re-auth
  V5  Validation        -> Input sanitization, output encoding, parameterized queries
  V6  Crypto            -> AES-256 at rest, TLS 1.3 in transit, key rotation
  V8  Data Protection   -> PII classification, retention policy, right to erasure
  V13 API               -> Schema validation, auth on all endpoints, pagination limits
  V14 Config            -> Hardened defaults, no debug in prod, secrets in vault

CHECKLIST:
  [ ] MFA available for all user accounts
  [ ] Password minimum 12 chars, checked against breach DB
  [ ] Session tokens rotate on privilege escalation
  [ ] RBAC with principle of least privilege
  [ ] PII encrypted at rest (AES-256) and in transit (TLS 1.3)
  [ ] Secrets in vault (not environment variables)
  [ ] All inputs sanitized (SQL injection prevention)
  [ ] Output encoding (XSS prevention)
  [ ] CSRF tokens on all state-changing requests
  [ ] Automated dependency scanning (Snyk/Dependabot)
  [ ] CVE > 7.0 fixed within 7 days
  [ ] Rate limiting on login (max 5 attempts / 15 min)
```

### Accessibility Requirements (WCAG 2.2 AA)

Source: W3C WCAG 2.2, https://www.w3.org/TR/WCAG22/

WCAG 2.2 new criteria: Focus Not Obscured (2.4.11), Focus Appearance (2.4.13), Dragging Movements (2.5.7), Target Size 24x24px (2.5.8), Consistent Help (3.2.6), Redundant Entry (3.3.7), Accessible Authentication (3.3.8).

```
CHECKLIST:
Perceivable:
  [ ] All images have descriptive alt text
  [ ] Color contrast >= 4.5:1 (text), 3:1 (large text)
  [ ] Content readable at 200% zoom without horizontal scroll
  [ ] No information conveyed by color alone

Operable:
  [ ] All interactive elements keyboard-accessible
  [ ] No keyboard traps
  [ ] Touch targets >= 24x24 CSS pixels (WCAG 2.2 min)
  [ ] Focus indicator: 2px solid, 3:1 contrast ratio
  [ ] Focused element never entirely obscured by sticky elements
  [ ] Drag operations have single-pointer alternative

Understandable:
  [ ] Form labels visible and associated with inputs
  [ ] Error messages identify field + describe error
  [ ] Auth does not require cognitive function tests

Robust:
  [ ] Valid HTML, ARIA roles used correctly
  [ ] Automated a11y tests in CI (axe-core, pa11y)
  [ ] Screen reader testing (NVDA, VoiceOver) before release

TESTING: axe-core in CI blocks merge on critical violations.
Lighthouse a11y score >= 95.
```

### NFR Elicitation Checklist
```
When gathering NFRs, ask about:
  - Performance: What's acceptable response time? Concurrent users?
  - Scalability: Expected growth? Peak load patterns?
  - Availability: Uptime SLA? Maintenance windows? DR strategy?
  - Security: Compliance requirements? Data classification?
  - Accessibility: Legal requirements? Target WCAG level?
  - Usability: User expertise level? Error tolerance?
  - Maintainability: Team size? Deployment frequency?
  - Portability: Multi-browser? Mobile? Offline?
```

## Step 12: Requirements Traceability Matrix

Source: IEEE 830

```
REQUIREMENTS TRACEABILITY MATRIX (RTM)
=======================================
ID    | Requirement          | Design     | Code        | Test Case  | Goal
------|----------------------|------------|-------------|------------|--------
REQ-1 | User login via email | AUTH-001   | auth/login  | TC-101     | G-01
REQ-2 | User login via SSO   | AUTH-002   | auth/sso    | TC-102     | G-01
REQ-3 | Cart add/remove      | ORD-001    | cart/ops    | TC-201     | G-02
REQ-4 | Checkout payment     | ORD-002    | pay/flow    | TC-202     | G-02

GOALS: G-01: Secure user access | G-02: Increase conversion 2% -> 5%

COVERAGE:
  Requirements with no test case = gap
  Test cases with no requirement = gold-plating
  Requirements with no goal = orphan (question value)
```

### RTM Maintenance Rules
1. Update matrix when requirements change
2. Review matrix at each sprint review
3. Flag orphan items (no upstream/downstream link)
4. Matrix is auditable artifact for compliance (ISO 27001, SOC2)
5. Archive matrix with each release

## Step 13: Event Storming (Alberto Brandolini)

Source: Alberto Brandolini, https://www.eventstorming.com/

### The Four Phases

```
PHASE 1: BIG PICTURE (2-4 hours, everyone)
  ORANGE = DOMAIN EVENTS (past tense): "Order was placed"
  RED/PINK = PAIN POINTS: "Why does this take 3 days?"
  YELLOW = QUESTIONS: "What if customer cancels mid-shipment?"

PHASE 2: DESIGN LEVEL (2-4 hours per bounded context)
  BLUE = COMMANDS: "Place Order", "Process Payment"
  YELLOW = AGGREGATES: "Order", "Payment", "Inventory"
  PURPLE = POLICIES: "When payment fails, cancel order"
  GREEN = READ MODELS: "Order Summary", "Sales Dashboard"

PHASE 3: ENFORCE TIMELINE - chronological order, resolve contradictions

PHASE 4: EXTRACT USER STORIES
  Each command → aggregate → event = candidate user story
  Policies → acceptance criteria
  Read models → UI requirements
```

### Event Storming Artifact Map

```
TIME →→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→

COMMAND: Place Order
  └── AGGREGATE: Order
      └── EVENT: OrderPlaced
          │
          ├── POLICY: Reserve inventory
          │   └── COMMAND: ReserveStock
          │       └── AGGREGATE: Inventory
          │           └── EVENT: StockReserved
          │
          ├── COMMAND: ProcessPayment
          │   └── AGGREGATE: Payment
          │       ├── EVENT: PaymentSucceeded
          │       │   └── POLICY: Confirm order
          │       │       └── EVENT: OrderConfirmed
          │       └── EVENT: PaymentFailed
          │           └── POLICY: Cancel after 3 retries
          │               └── EVENT: OrderCancelled
          │
          └── READ MODEL: OrderSummary (for customer)
              READ MODEL: WarehouseQueue (for ops)
```

### Event Storming Rules
1. No screens — physical or virtual sticky notes
2. Everyone participates — devs, domain experts, product, UX, ops
3. Orange first — discover events before modeling
4. Embrace conflicts — disagreements reveal missing knowledge
5. Timebox — 2-4 hours for big picture
6. Photo everything — the wall IS the documentation

## Step 14: Domain Storytelling (Stefan Hofer)

Source: Stefan Hofer, https://www.domainstorytelling.org/

### Notation

```
ACTORS: 👤 Person, 🏢 Organization
WORK OBJECTS: 📄 Document, 📦 Physical item, 💰 Business artifact
ACTIVITIES: ──creates──▶  ──sends──▶  ──processes──▶  ──reads──▶
(numbered sequence: 1., 2., 3., ...)
```

### Domain Story Example

```
DOMAIN STORY: Order Fulfillment
================================
Told by: Sarah (Warehouse Manager)

1. 👤 Customer ───creates───▶ 📄 Order
2. 📄 Order ────received by──▶ 👤 Sales Rep
3. 👤 Sales Rep ──validates──▶ 📄 Order
4. 👤 Sales Rep ──sends──────▶ 📄 ConfirmedOrder
5. 📄 ConfirmedOrder ──sent──▶ 👤 Warehouse Worker
6. 👤 Warehouse Worker ──picks──▶ 📦 Items
7. 👤 Warehouse Worker ──packs──▶ 📦 Package
8. 📦 Package ──sent──▶ 👤 Courier
9. 👤 Courier ──delivers───▶ 📦 Package ──to──▶ 👤 Customer

INSIGHTS:
  - No automated inventory check between steps 3-5
  - Courier handoff (step 8) is manual, error-prone
  - Customer has no visibility between steps 5-9
```

### Domain Stories → Requirements
```
EXTRACTION PATTERNS:
  Actors → User roles / personas
  Work Objects → Domain entities / aggregates
  Activities → User stories / use cases
  Handoffs → Integration points / APIs
  Bottlenecks → NFR requirements (performance, automation)
  Missing steps → Gaps in current system
```

## Step 15: Platform Team Requirements

### API Contract Checklist
```
DESIGN:
  [ ] API style chosen (REST, gRPC, GraphQL) with rationale
  [ ] OpenAPI 3.1 / gRPC proto / GraphQL schema published
  [ ] Standard error format (RFC 7807 Problem Details)
  [ ] Pagination pattern defined (cursor-based preferred)
  [ ] Versioning strategy (URL path, header, content-type)
  [ ] Deprecation policy (minimum notice period)

DOCUMENTATION:
  [ ] Interactive API docs (Swagger UI, Redoc)
  [ ] Getting started guide (< 5 minutes to first call)
  [ ] Code examples in 3+ languages
  [ ] Changelog with migration guides

TESTING:
  [ ] Contract tests (Pact, Spring Cloud Contract)
  [ ] Backward compatibility checks in CI
  [ ] Mock server for consumer development

CONSUMPTION:
  [ ] SDK/client libraries for top 3 languages
  [ ] Rate limits documented (per-client, global)
  [ ] Idempotency keys for non-idempotent operations
```

### SLA Template
```
PLATFORM SLA
============
AVAILABILITY: 99.9% (8.76 hrs downtime/year)
  P1 (service down): 15 min ack, 1 hr mitigation
  P2 (degraded): 30 min ack, 4 hr mitigation
  P3 (minor): 4 hr ack, 24 hr mitigation

PERFORMANCE: p50 < 50ms, p95 < 200ms, p99 < 500ms
THROUGHPUT: > 10,000 rps sustained

RELIABILITY:
  Error budget: 0.1% = 43.8 min/month
  Circuit breaker: open after 5 consecutive failures
  Retry: exponential backoff, max 3 retries
  Timeout: 30s default, configurable per operation

REPORTING: Status page (real-time), post-mortem within 48 hrs,
  monthly SLA report (availability, latency, error budget)
```

### Developer Experience Checklist
```
ONBOARDING:
  [ ] Time to first API call < 5 minutes
  [ ] Quickstart tutorial (copy-paste-runnable)
  [ ] Sandbox environment with realistic data
  [ ] Self-service API key provisioning

DEBUGGING:
  [ ] Request tracing (trace-id in every response header)
  [ ] Error messages include: what happened, why, how to fix
  [ ] Structured logging (JSON) with correlation IDs

SELF-SERVICE:
  [ ] Developer portal with docs, examples, status
  [ ] CLI tool for common operations
  [ ] Terraform/Pulumi provider for infrastructure resources
```

## Step 16: Requirements Elicitation Techniques

### Technique Selection Matrix
```
Situation                          | Best Technique
-----------------------------------|-------------------
New domain, few stakeholders       | Interview + Observation
Large stakeholder group            | Survey + Brainstorming
Physical/manual processes          | Observation (shadowing)
Unclear requirements               | Prototyping + Interview
Validation of existing requirements| Survey + Prototype test
Cross-team alignment               | Brainstorming + Event Storming
Regulatory/compliance              | Interview + Document Analysis
Greenfield product                 | JTBD Interview + Prototype
Legacy system replacement          | Observation + Domain Storytelling
Platform/API requirements          | Consumer interviews + API review
```

### Interview Guide
```
STRUCTURED INTERVIEW (45-60 min):
  1. Build rapport (5 min) — explain purpose
  2. Current state (15 min) — "How do you do X today?"
  3. Pain points (15 min) — "What's broken? What's slow?"
  4. Desired state (15 min) — "What would ideal look like?"
  5. Priorities (5 min) — "If you could fix one thing first?"

QUESTION TYPES:
  OPEN-ENDED: "Walk me through your typical [process]"
  PROBING: "Can you give me a specific example?"
  CLOSED: "Do you use [feature] daily or weekly?"
  CONTEXTUAL: "Show me how you do that right now"

ANTI-PATTERNS:
  - Leading questions: "Don't you think we need X?"
  - Solution bias: "Would you use a button that does X?"
```

### Brainstorming Methods
```
BRAINWRITING (6-3-5): 6 people, 3 ideas, 5 min/round → 108 ideas in 30 min
REVERSE BRAINSTORMING: "How could we make this WORSE?" → flip to solutions
HOW MIGHT WE: "How might we [action] so that [outcome]?"

VOTING: Dot voting (3-5 dots per person) or Impact/Feasibility 2x2 matrix
```

## Step 17: OKR Alignment

```
OBJECTIVE: Improve checkout conversion
├── KR1: Reduce cart abandonment from 70% to 45%
│   ├── Story: One-page checkout (AC: < 3 steps)
│   └── Story: Guest checkout (AC: no registration required)
├── KR2: Increase payment success rate from 92% to 99%
│   ├── Story: Retry failed payments (AC: auto-retry 3x)
│   └── Story: Multiple payment methods (AC: cards, PayPal, Apple Pay)
└── KR3: Reduce checkout time from 4 min to 90 seconds
    ├── Story: Auto-fill address (AC: from user profile)
    └── Story: Saved payment methods (AC: one-click pay)

ALIGNMENT RULES:
  - Every KR traces to 1+ user stories
  - Every user story traces to 1 KR (orphan = question value)
  - KRs measure outcomes (metric changed), not outputs (work done)
```

## Pitfalls

1. **Don't write flat backlogs** — use story mapping for 2D visualization
2. **Don't skip example mapping** — 25 min saves hours of rework
3. **Don't write GUI-level Gherkin** — declarative, business-language steps
4. **Don't skip acceptance criteria** — every story needs clear AC
5. **Don't over-specify** — focus on business rules, not implementation
6. **Don't skip living documentation** — specs must stay in sync with code
7. **Don't confuse JTBD with features** — jobs are progress in a circumstance
8. **Don't score RICE without data** — confidence < 50% needs research spike
9. **Don't use absolute effort in WSJF** — always relative sizing
10. **Don't accept stories > 1 sprint** — split first using patterns
11. **Don't skip NFRs** — performance, security, accessibility are requirements
12. **Don't skip traceability** — orphan requirements creep in without RTM
13. **Don't bolt on security** — STRIDE threat model early, map to OWASP ASVS
14. **Don't test accessibility last** — WCAG 2.2 AA baked into AC from start
15. **Don't skip event storming for complex domains** — big picture prevents costly misunderstandings
16. **Don't treat platform APIs like internal code** — contracts, SLAs, DX are first-class
17. **Don't prioritize by gut feel alone** — CD3 and WSJF make cost of delay explicit
18. **Don't rely on single elicitation technique** — combine interviews, surveys, observation
19. **Don't skip discovery track** — dual-track means validating BEFORE building
20. **Don't model aggregates too large** — keep small (5-7 entities max), large = contention
21. **Don't use technical terms in domain discussions** — ubiquitous language means domain terms in code
22. **Don't skip event storming phases** — chaotic exploration before design level
23. **Don't confuse entities with value objects** — entities have identity; value objects are immutable
24. **Don't reference aggregates by object** — always by ID across boundaries
25. **Don't mix ubiquitous languages across BCs** — same word in different BCs = different models
