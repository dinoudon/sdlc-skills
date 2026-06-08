---
name: sdlc-requirements-engineering
description: "Requirements: user story mapping (Jeff Patton), BDD/Gherkin (Cucumber, pytest-bdd), acceptance criteria (Given/When/Then), impact mapping (Gojko Adzic), example mapping, specification by example, JTBD framework, RICE scoring, WSJF prioritization, user story splitting, NFR patterns, requirements traceability, OKR alignment, design thinking, AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility, event storming (Brandolini), domain storytelling (Hofer), platform team requirements, cost of delay (Reinertsen), requirements elicitation techniques, ATDD."
version: 4.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, requirements, user-stories, bdd, gherkin, acceptance-criteria, impact-mapping, example-mapping, specification-by-example, jtbd, rice-scoring, wsjf, story-splitting, nfr, traceability, okr, design-thinking, ai-ml, stride, owasp-asvs, wcag-2.2, accessibility, event-storming, domain-storytelling, platform-teams, cost-of-delay, elicitation, atdd]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-prd-to-production]
---

# Requirements Engineering

User story mapping, BDD/Gherkin, acceptance criteria, impact mapping, example mapping, specification by example, JTBD, RICE scoring, WSJF, user story splitting, NFR patterns, requirements traceability, OKR alignment, design thinking, AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility, event storming (Brandolini), domain storytelling (Hofer), platform team requirements, cost of delay analysis (Reinertsen), requirements elicitation techniques, ATDD.

## When to Use

Trigger when user:
- Writes user stories or acceptance criteria
- Sets up BDD/Gherkin workflows
- Creates impact maps or story maps
- Refines backlog or runs example mapping sessions
- Formalizes requirements as executable specifications
- Discovers customer needs via JTBD interviews
- Prioritizes features using RICE or WSJF
- Splits large user stories into sprint-sized pieces
- Defines non-functional requirements (performance, security, accessibility)
- Creates or audits requirements traceability matrices
- Aligns requirements to OKRs (Objectives → Key Results → User Stories)
- Runs design thinking workshops (empathize, define, ideate, prototype, test)
- Defines requirements for AI/ML features (data, metrics, bias)
- Maps security requirements using STRIDE or OWASP ASVS
- Specifies accessibility requirements under WCAG 2.2 AA
- Runs event storming workshops (domain events, commands, aggregates)
- Creates domain storytelling diagrams (actors, work objects, processes)
- Defines platform team requirements (API contracts, SLAs, developer experience)
- Analyzes cost of delay using CD3 or WSJF prioritization
- Conducts requirements elicitation (interviews, surveys, observation, prototyping, brainstorming)
- Sets up ATDD (acceptance test-driven development) workflows

## Step 1: User Story Mapping

Source: Jeff Patton, "User Story Mapping" (2014), https://www.jpattonassociates.com/user-story-mapping/

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

### Gherkin Syntax
```gherkin
Feature: <feature name>
  As a <role>
  I want <capability>
  So that <benefit>

  Background:
    Given <shared preconditions>

  Scenario: <scenario name>
    Given <precondition>
    And <additional precondition>
    When <action/event>
    And <additional action>
    Then <expected outcome>
    And <additional outcome>
    But <exception/negative>

  Scenario Outline: <parameterized scenario>
    Given <precondition> with <param1>
    When <action> with <param2>
    Then <outcome>

    Examples:
      | param1 | param2 | outcome |
      | val1   | val2   | result1 |
      | val3   | val4   | result2 |
```

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

Source: https://www.mountaingoatsoftware.com/blog/the-difference-between-a-story-and-a-acceptance-criteria

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
  [ ] All fields required (first, last, email, password)
  [ ] Password masked in UI
```

### Pattern C: Rule-based (Rule:Condition:Example)
```
RULE: Password requirements
  CONDITION: Password must meet complexity
    EXAMPLE: "Passw0rd!" -> accepted
    EXAMPLE: "pass" -> rejected (too short)
    EXAMPLE: "password" -> rejected (no uppercase/digit/special)

RULE: Email uniqueness
  CONDITION: No duplicate emails allowed
    EXAMPLE: register with existing email -> error message
```

## Step 4: Impact Mapping

Source: Gojko Adzic, "Impact Mapping" (2012), https://www.impactmapping.org/

Strategic planning connecting business goals to deliverables through actors and impacts.

```
IMPACT MAP: [Feature/Project Name]
===================================

GOAL: Increase conversion rate from 2% to 5% by Q4
│
├── ACTOR: New visitor
│   ├── IMPACT: Understands product value in <30 seconds
│   │   ├── DELIVERABLE: Hero section with value prop
│   │   ├── DELIVERABLE: Product demo video
│   │   └── DELIVERABLE: Social proof testimonials
│   ├── IMPACT: Finds relevant product quickly
│   │   ├── DELIVERABLE: Category filtering
│   │   └── DELIVERABLE: Search with autocomplete
│   └── IMPACT: Trusts the site enough to buy
│       ├── DELIVERABLE: Security badges
│       └── DELIVERABLE: Money-back guarantee display
│
├── ACTOR: Returning visitor
│   ├── IMPACT: Remembers previous browsing
│   │   ├── DELIVERABLE: Recently viewed items
│   │   └── DELIVERABLE: Personalized recommendations
│   └── IMPACT: Completes abandoned purchase
│       ├── DELIVERABLE: Cart reminder emails
│       └── DELIVERABLE: One-click reorder
│
└── ACTOR: Internal sales team
    ├── IMPACT: Focuses on high-value leads
    │   ├── DELIVERABLE: Lead scoring dashboard
    │   └── DELIVERABLE: CRM integration
    └── IMPACT: Reduces manual follow-up
        ├── DELIVERABLE: Automated email sequences
        └── DELIVERABLE: Chat bot for FAQs
```

**Rules:**
- One goal per map (measurable, time-bound)
- Actors = anyone who can influence outcome
- Impacts = behavior changes that achieve goal
- Deliverables = things we can build/do
- Cut branches that don't connect to goal

## Step 5: Example Mapping

Source: Matt Wynne, Cucumber team, https://cucumber.io/docs/bdd/example-mapping/

25-minute workshop to discover rules, examples, and questions before writing scenarios.

**4 card colors:**
- **YELLOW:** Business rule / acceptance criteria
- **GREEN:** Concrete examples illustrating rules
- **RED:** Questions / unknowns / open issues
- **BLUE:** User story being discussed

```
EXAMPLE MAPPING SESSION
=======================
USER STORY (Blue): As a customer, I want to apply a discount
                    code so that I get a price reduction.

RULE (Yellow): Valid discount codes reduce total by percentage
  Example (Green): Code "SAVE20" on $100 order -> $80 total
  Example (Green): Code "SAVE10" on $50 order -> $45 total

RULE (Yellow): Expired codes are rejected
  Example (Green): Code "SUMMER24" (expired) -> error message
  Question (Red): What exact error message to show?

RULE (Yellow): Only one code per order
  Example (Green): Apply "SAVE20" then "SAVE10" -> error
  Question (Red): Replace existing or reject second?

RULE (Yellow): Minimum order value for certain codes
  Example (Green): "VIP50" on order < $200 -> error
  Question (Red): Does min value vary by code?

TIMEBOX: 25 minutes. If questions remain, spike/investigate
before next session.
```

**Session protocol:**
1. Pick one story
2. Write story on blue card
3. Stakeholders state rules (yellow cards)
4. Team writes concrete examples (green cards)
5. Questions go on red cards
6. Stop at 25 min
7. If no red cards and examples cover all rules → ready to automate
8. If red cards remain → need investigation

## Step 6: Specification by Example

Source: Gojko Adzic, "Specification by Example" (2011), https://specificationbyexample.com/

Use concrete examples instead of abstract specifications. Examples become executable specifications AND living documentation.

```
SPECIFICATION BY EXAMPLE WORKFLOW
==================================

1. DERIVE SCOPE
   - Identify what to specify (not everything)
   - Focus on business rules, not implementation
   - Use impact mapping to find scope

2. SPECIFY collaboratively
   - Whole team writes examples together
   - Use Example Mapping (25-min sessions)
   - Examples in business language

3. FORMALIZE specification
   - Turn examples into structured format
   - Gherkin scenarios or similar
   - Keep business-readable

4. AUTOMATE specification
   - Implement step definitions
   - Executable specification = automated test
   - Thin automation layer (not GUI scripts)

5. VALIDATE frequently
   - Run specifications as tests
   - Both specification AND validation
   - Failures = misunderstandings or bugs

6. EVOLVE specification
   - Refactor living docs
   - Keep in sync with system
   - Remove redundant specifications
```

### Living Documentation Structure
```
specification/
├── features/
│   ├── authentication/
│   │   ├── login.feature
│   │   ├── registration.feature
│   │   └── password_reset.feature
│   ├── orders/
│   │   ├── placement.feature
│   │   ├── payment.feature
│   │   └── fulfillment.feature
│   └── reports/
│       ├── sales_dashboard.feature
│       └── inventory.feature
├── step_definitions/
│   ├── auth_steps.py
│   ├── order_steps.py
│   └── report_steps.py
└── support/
    ├── world.py
    └── helpers.py
```

## Step 7: Jobs-to-be-Done (JTBD)

Source: Clayton Christensen, "Competing Against Luck" (2016); Anthony Ulwick, "Jobs to Be Done" (2016), https://strategyn.com/jobs-to-be-done/

Framework for discovering what customers truly hire products to do. Focus on the "job" (progress in a specific circumstance), not demographics or product features.

### Job Statement Format
```
When [SITUATION], I want to [MOTIVATION], so I can [EXPECTED OUTCOME].
```

### Job Mapping
```
JOB MAP: Online food ordering
================================

CORE JOB: Get a meal without cooking when short on time

FUNCTIONAL JOBS:
  1. Find food I want to eat right now
  2. Know total cost before committing
  3. Get food delivered to my location
  4. Know when food will arrive

EMOTIONAL JOBS:
  1. Feel confident food will be good
  2. Avoid guilt of spending on delivery
  3. Not feel anxious about wait time

RELATED JOBS:
  1. Track calorie intake for diet
  2. Accommodate dietary restrictions for group
  3. Impress guests with food choice
```

### Outcome-Driven Innovation (ODI) - Ulwick Method
```
OUTCOME STATEMENTS (desired outcomes, not solutions):
  - Minimize time to find a meal that matches my mood
  - Minimize likelihood of food arriving cold
  - Minimize number of steps to place a reorder
  - Maximize confidence that portion size is adequate
  - Minimize time spent deciding between options
  - Maximize certainty that delivery time estimate is accurate

OPPORTUNITY SCORE = Importance + (Importance - Satisfaction)
  Score > 10 = opportunity for innovation
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

## Step 8: RICE Scoring (Prioritization)

Source: Intercom, https://www.intercom.com/blog/rice-simple-prioritization-for-product-managers/

Scoring model for prioritizing features. Reduces HiPPO (Highest Paid Person's Opinion) bias.

```
RICE SCORE = (Reach × Impact × Confidence) / Effort

REACH: # of people affected per time period
  1000 = 1000 customers/quarter
  500  = 500 customers/quarter

IMPACT: Effect on individual person (3-point scale)
  3 = massive impact
  2 = high impact
  1 = medium impact
  0.5 = low impact
  0.25 = minimal impact

CONFIDENCE: How sure are we? (% expressed as decimal)
  100% = 1.0  (high confidence, backed by data)
  80%  = 0.8  (medium confidence, some data)
  50%  = 0.5  (low confidence, mostly intuition)
  20%  = 0.2  (wild guess, gut feeling)

EFFORT: Person-months (whole team, not one person)
  5 = 5 person-months
  0.5 = half a person-month
```

### RICE Scoring Example
```
FEATURE SCORING TABLE
=====================
Feature            | Reach | Impact | Confidence | Effort | RICE
-------------------|-------|--------|------------|--------|------
SSO integration    | 500   | 2      | 0.8        | 3      | 267
Export to CSV      | 2000  | 0.5    | 1.0        | 0.5    | 2000
Dark mode          | 3000  | 0.25   | 0.5        | 1      | 375
API rate limiting  | 100   | 3      | 1.0        | 2      | 150
Two-factor auth    | 800   | 2      | 0.8        | 2      | 640

PRIORITY ORDER: Export to CSV > 2FA > Dark mode > SSO > Rate limiting

NOTE: Confidence < 50% should trigger research spike before committing.
NOTE: Effort includes design + dev + testing + deployment.
```

### RICE Anti-Patterns
- Don't inflate reach numbers (use real analytics)
- Don't score impact without user research
- Don't use effort as a single developer's estimate
- Don't score in isolation — calibration session with team

## Step 9: WSJF - Weighted Shortest Job First (SAFe)

Source: SAFe framework, https://www.scaledagileframework.com/wsjf/

Priority model for continuous backlog refinement in SAFe. Sequences jobs for maximum economic benefit.

```
WSJF = Cost of Delay / Job Duration

COST OF DELAY = User/Business Value + Time Criticality + Risk Reduction/Opportunity Enablement

Each component scored relative to other items on Fibonacci scale:
  1, 2, 3, 5, 8, 13, 20

JOB DURATION = Relative size (story points, t-shirt sizes)
  Also Fibonacci: 1, 2, 3, 5, 8, 13, 20

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

PRIORITY ORDER: Fix payment bug > GDPR > Mobile responsive > API v2 > New report
```

### WSJF Rules
- Score items relative to each other, not absolute
- Smallest CoD item always gets score of 1 for each dimension
- Duration must include entire value stream (not just dev)
- Re-score when new items enter backlog
- Split items with duration > 13 points before scoring

## Step 10: User Story Splitting Patterns

Source: Richard Lawrence, https://agileforall.com/patterns-for-splitting-user-stories/

When stories are too large (> 1 sprint), split using these patterns.

### Pattern 1: Workflow Steps
```
EPIC: User can purchase product online

SPLIT BY WORKFLOW:
  1. User can add product to cart
  2. User can enter shipping address
  3. User can enter payment information
  4. User can review order before submission
  5. User receives order confirmation
  6. User can track order status
```

### Pattern 2: Data Variations
```
EPIC: User can upload profile picture

SPLIT BY DATA:
  1. User can upload JPEG image
  2. User can upload PNG image
  3. User can upload WebP image
  4. User can upload HEIC image (mobile)
  5. System rejects unsupported formats with error message

NOTE: Start with most common format. Each data variation is
independently testable and deployable.
```

### Pattern 3: CRUD Operations
```
EPIC: Admin can manage discount codes

SPLIT BY CRUD:
  1. Admin can CREATE discount code (with validation)
  2. Admin can READ/view list of active discount codes
  3. Admin can UPDATE discount code (change %, expiry)
  4. Admin can DELETE/deactivate discount code
  5. Admin can see discount code usage analytics
```

### Pattern 4: Business Rule Variations
```
EPIC: System calculates shipping cost

SPLIT BY BUSINESS RULE:
  1. Free shipping for orders over $50
  2. Standard shipping rate for domestic orders
  3. International shipping rate (by zone)
  4. Expedited shipping surcharge
  5. Oversized item surcharge
  6. Multiple warehouse split-shipment logic
```

### Pattern 5: Operations (Happy Path / Edge Cases)
```
EPIC: User can log in

SPLIT:
  1. Happy path: valid credentials -> dashboard
  2. Invalid password -> error message, retry
  3. Account locked after 5 failed attempts
  4. Password expired -> force change flow
  5. SSO login via Google
  6. SSO login via Microsoft
  7. Session timeout -> redirect to login
```

### Pattern 6: Platform/Interface
```
EPIC: Notifications work across platforms

SPLIT BY PLATFORM:
  1. Email notifications
  2. Push notifications (iOS)
  3. Push notifications (Android)
  4. In-app notification center
  5. SMS notifications
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

NFRs define system qualities, not features. Specify as constraints with measurable thresholds.

### Performance Budgets
```
PERFORMANCE BUDGET: E-commerce checkout
=======================================
Metric                    | Budget        | Measurement
--------------------------|---------------|------------------
Time to interactive (TTI) | < 3s on 3G    | Lighthouse CI
First contentful paint    | < 1.5s        | Lighthouse CI
Largest contentful paint  | < 2.5s        | Core Web Vitals
Cumulative layout shift   | < 0.1         | Core Web Vitals
First input delay         | < 100ms       | Real User Monitoring
API response time (p95)   | < 200ms       | APM (Datadog/NR)
API response time (p99)   | < 500ms       | APM
Database query time (p95) | < 50ms        | Query analyzer
Error rate                | < 0.1%        | Error tracking
Throughput                | > 1000 rps    | Load testing

ENFORCEMENT: CI pipeline fails if Lighthouse score < 90.
Budget reviewed monthly with performance dashboard.
```

### Security Requirements Patterns (STRIDE + OWASP ASVS)

Source: Microsoft STRIDE, https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats; OWASP ASVS, https://owasp.org/www-project-application-security-verification-standard/

```
SECURITY REQUIREMENTS: Web Application
=======================================

THREAT MODEL (STRIDE):
  Spoofing        -> Authentication requirements
  Tampering       -> Integrity / input validation requirements
  Repudiation     -> Logging / audit trail requirements
  Information Disclosure -> Encryption / access control requirements
  Denial of Service     -> Rate limiting / resource limits requirements
  Elevation of Privilege -> Authorization / least privilege requirements

OWASP ASVS MAPPING:
  V1  Architecture      -> Threat model documented, security design review
  V2  Authentication    -> MFA, password policy, session management
  V3  Session Mgmt      -> Token rotation, idle timeout, secure cookie flags
  V4  Access Control     -> RBAC, IDOR testing, admin re-auth
  V5  Validation        -> Input sanitization, output encoding, parameterized queries
  V6  Crypto            -> AES-256 at rest, TLS 1.3 in transit, key rotation
  V7  Error/Logging     -> No PII in logs, audit trail, alerting on anomalies
  V8  Data Protection   -> PII classification, retention policy, right to erasure
  V9  Communication     -> HSTS, certificate pinning (mobile), mTLS (service-to-service)
  V10 Malicious Code    -> Dependency scanning, code signing, SBOM generation
  V11 Business Logic    -> Race condition testing, rate limiting, abuse case testing
  V12 Files/Resources   -> Upload validation, path traversal prevention, sandbox
  V13 API               -> Schema validation, auth on all endpoints, pagination limits
  V14 Config            -> Hardened defaults, no debug in prod, secrets in vault

Authentication:
  [ ] MFA available for all user accounts
  [ ] Password minimum 12 chars, checked against breach DB
  [ ] Session tokens rotate on privilege escalation
  [ ] Account lockout after 5 failed attempts (15 min)
  [ ] OAuth 2.0 + PKCE for third-party auth

Authorization:
  [ ] RBAC with principle of least privilege
  [ ] API endpoints enforce auth (no open endpoints)
  [ ] Horizontal privilege escalation tested (IDOR)
  [ ] Admin actions require re-authentication

Data Protection:
  [ ] PII encrypted at rest (AES-256)
  [ ] PII encrypted in transit (TLS 1.3)
  [ ] Secrets in vault (not environment variables)
  [ ] Database credentials rotated every 90 days
  [ ] PII access logged with audit trail

Input Validation:
  [ ] All inputs sanitized (SQL injection prevention)
  [ ] Output encoding (XSS prevention)
  [ ] CSRF tokens on all state-changing requests
  [ ] File upload: type validation + size limit + sandbox scan

Dependencies:
  [ ] Automated dependency scanning (Snyk/Dependabot)
  [ ] CVE > 7.0 fixed within 7 days
  [ ] No known high/critical CVEs in production

ABUSE CASES:
  [ ] Rate limiting on login (max 5 attempts / 15 min)
  [ ] Rate limiting on API (per-user, per-IP quotas)
  [ ] Account enumeration prevention (generic error messages)
  [ ] Mass assignment protection (allowlist fields)
  [ ] Business logic abuse scenarios documented and tested
```

### Accessibility Requirements (WCAG 2.2 AA)

Source: W3C WCAG 2.2, https://www.w3.org/TR/WCAG22/; WAI-ARIA 1.2, https://www.w3.org/TR/wai-aria-1.2/

WCAG 2.2 new success criteria beyond 2.1:
  - 2.4.11 Focus Not Obscured (Minimum) - focused element not entirely hidden
  - 2.4.12 Focus Not Obscured (Enhanced) - focused element fully visible
  - 2.4.13 Focus Appearance - focus indicator has sufficient size/contrast
  - 2.5.7 Dragging Movements - alternatives to drag gestures
  - 2.5.8 Target Size (Minimum) - 24x24 CSS px minimum touch targets
  - 3.2.6 Consistent Help - help mechanisms in consistent location
  - 3.3.7 Redundant Entry - don't ask for same info twice in flow
  - 3.3.8 Accessible Authentication (Minimum) - no cognitive function tests for auth
  - 3.3.9 Accessible Authentication (Enhanced) - no object recognition or user-supplied media
```
ACCESSIBILITY REQUIREMENTS
===========================
Perceivable:
  [ ] All images have descriptive alt text
  [ ] Color contrast ratio >= 4.5:1 (text), 3:1 (large text)
  [ ] Content readable at 200% zoom without horizontal scroll
  [ ] Captions on all video content
  [ ] No information conveyed by color alone
  [ ] Reflow at 320px CSS width without loss of content
  [ ] Text spacing adjustable without breaking layout
  [ ] Status messages use ARIA live regions (role="status", "alert")

Operable:
  [ ] All interactive elements keyboard-accessible
  [ ] Focus order follows logical reading sequence
  [ ] No keyboard traps
  [ ] Skip-to-content link available
  [ ] Touch targets >= 24x24 CSS pixels (WCAG 2.2 min), >= 44x44 preferred
  [ ] Focus indicator: 2px solid, 3:1 contrast ratio (2.4.13)
  [ ] Focused element never entirely obscured by sticky/fixed elements (2.4.11)
  [ ] Drag operations have single-pointer alternative (2.5.7)
  [ ] Keyboard shortcuts can be remapped or disabled

Understandable:
  [ ] Page language declared in HTML
  [ ] Form labels visible and associated with inputs
  [ ] Error messages identify field + describe error
  [ ] Consistent navigation across pages
  [ ] Consistent help mechanism location across pages (3.2.6)
  [ ] No redundant entry in multi-step flows (3.3.7)
  [ ] Auth does not require cognitive function tests (3.3.8)

Robust:
  [ ] Valid HTML (no duplicate IDs, proper nesting)
  [ ] ARIA roles used correctly (not overloaded)
  [ ] Works with screen readers (VoiceOver, NVDA)
  [ ] Automated a11y tests in CI (axe-core, pa11y)

  ARIA PATTERNS:
  - Disclosure: aria-expanded, aria-controls on toggle buttons
  - Modal dialogs: role="dialog", aria-modal="true", focus trap
  - Tabs: role="tablist"/"tab"/"tabpanel", aria-selected, keyboard arrows
  - Combobox: role="combobox", aria-expanded, aria-activedescendant, aria-controls
  - Alert: role="alert" for assertive, role="status" for polite live regions
  - Breadcrumb: nav with aria-label="Breadcrumb", aria-current="page" on last
  - Tree view: role="tree"/"treeitem", aria-expanded, aria-level
  - Menu: role="menu"/"menuitem", aria-haspopup, keyboard arrow navigation
  - Progress: role="progressbar", aria-valuenow/min/max
  - Tooltip: role="tooltip", aria-describedby on trigger

  SCREEN READER TESTING:
  [ ] NVDA (Windows) - test on each major release
  [ ] VoiceOver (macOS/iOS) - test on each major release
  [ ] TalkBack (Android) - test if mobile supported
  [ ] JAWS (Windows) - test for enterprise/corporate users
  [ ] Verify: headings structure, landmark regions, form labels, live regions
  [ ] Verify: dynamic content announcements (AJAX updates, toast messages)
  [ ] Verify: custom widget keyboard interaction matches ARIA pattern
  [ ] Include screen reader users in usability testing (not just automated scans)

TESTING: axe-core in CI blocks merge on critical violations.
Manual screen reader audit (NVDA + VoiceOver) before each release.
Pa11y CI for regression on key pages. Lighthouse a11y score >= 95.
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
  - Observability: Logging requirements? Alerting thresholds?
```

## Step 12: Requirements Traceability Matrix

Source: IEEE 830, https://www.iso.org/standard/74529.html

Links requirements forward to design, code, tests, and backward to business goals. Ensures nothing is missed, nothing is gold-plated.

```
REQUIREMENTS TRACEABILITY MATRIX (RTM)
=======================================
ID    | Requirement          | Design     | Code        | Test Case  | Goal
------|----------------------|------------|-------------|------------|--------
REQ-1 | User login via email | AUTH-001   | auth/login  | TC-101     | G-01
REQ-2 | User login via SSO   | AUTH-002   | auth/sso    | TC-102     | G-01
REQ-3 | Password reset flow  | AUTH-003   | auth/reset  | TC-103     | G-01
REQ-4 | Cart add/remove      | ORD-001    | cart/ops    | TC-201     | G-02
REQ-5 | Checkout payment     | ORD-002    | pay/flow    | TC-202     | G-02
REQ-6 | Order confirmation   | ORD-003    | order/conf  | TC-203     | G-02
REQ-7 | Inventory check      | INV-001    | inv/check   | TC-301     | G-03
REQ-8 | Low stock alert      | INV-002    | inv/alert   | TC-302     | G-03

GOALS:
  G-01: Secure user access (CISO mandate)
  G-02: Increase conversion rate 2% -> 5%
  G-03: Reduce stockouts by 50%

COVERAGE:
  Requirements with no test case = gap
  Test cases with no requirement = gold-plating
  Requirements with no design = analysis incomplete
  Requirements with no goal = orphan (question value)
```

### Forward and Backward Traceability
```
FORWARD (Goal -> Code):
  Business Goal -> Requirement -> Design -> Code -> Test
  "Are we building the right thing?"

BACKWARD (Code -> Goal):
  Test -> Code -> Design -> Requirement -> Business Goal
  "Why does this code exist?"

IMPACT ANALYSIS:
  If REQ-5 (Checkout payment) changes:
    Design: ORD-002 (review)
    Code: pay/flow (review + refactor)
    Tests: TC-202 (update)
    Goal: G-02 (verify still met)
```

### RTM Tools and Formats
```
LIGHTWEIGHT (spreadsheet):
  - CSV with columns: ID, Requirement, Source, Priority, Design Ref, Code Ref, Test Ref, Status
  - Auto-filter for coverage gaps
  - Conditional formatting for orphan detection

HEAVYWEIGHT (ALM tools):
  - Jira: links between Epic/Story/Test/Deployment
  - Azure DevOps: work item links with traceability views
  - IBM DOORS: formal requirements management

CODE-FIRST (living traceability):
  - Requirement IDs in code comments: // REQ-5
  - Test names include requirement ID: test_checkout_payment_REQ5()
  - Build script verifies all REQ-IDs have test coverage
  - Architecture Decision Records link to requirements
```

### RTM Maintenance Rules
1. Update matrix when requirements change
2. Review matrix at each sprint review
3. Flag orphan items (no upstream/downstream link)
4. Matrix is auditable artifact for compliance (ISO 27001, SOC2)
5. Archive matrix with each release for regulatory traceability

## Step 13: Event Storming (Alberto Brandolini)

Source: Alberto Brandolini, "Introducing EventStorming" (2021), https://www.eventstorming.com/

Collaborative workshop technique for rapidly exploring complex business domains. Uses colored sticky notes on a large timeline to discover domain events, commands, aggregates, and read models. Entire domain on one wall — big picture.

### The Four Phases

```
EVENT STORMING PHASES
=====================

PHASE 1: BIG PICTURE (Domain Exploration)
  Participants: Everyone (devs, domain experts, product, UX, ops)
  Duration: 2-4 hours
  Goal: Surface the whole domain, find pain points

  ORANGE stickies = DOMAIN EVENTS (past tense)
    - "Order was placed"
    - "Payment was processed"
    - "Inventory was reserved"
    - "Shipment was dispatched"

  RED/PINK stickies = PAIN POINTS / HOT SPOTS
    - "Why does this take 3 days?"
    - "Nobody knows who owns this"
    - "This breaks every Black Friday"

  YELLOW stickies = QUESTIONS / ASSUMPTIONS
    - "Does this happen before or after payment?"
    - "What if the customer cancels mid-shipment?"

PHASE 2: DESIGN LEVEL (Process Modeling)
  Duration: 2-4 hours per bounded context
  Goal: Identify commands, aggregates, policies, read models

  BLUE stickies = COMMANDS (actions that trigger events)
    - "Place Order"
    - "Process Payment"
    - "Reserve Inventory"

  YELLOW stickies = AGGREGATES (consistency boundaries)
    - "Order" (owns: items, status, total)
    - "Payment" (owns: amount, method, status)
    - "Inventory" (owns: stock levels per warehouse)

  PURPLE/LILAC stickies = POLICIES / BUSINESS RULES
    - "When payment fails, cancel order"
    - "When stock < threshold, trigger reorder"
    - "When order placed, start 30-min payment timer"

  GREEN stickies = READ MODELS (projections for UI/reporting)
    - "Order Summary" (for customer dashboard)
    - "Inventory Report" (for warehouse ops)
    - "Sales Dashboard" (for management)

PHASE 3: ENFORCE THE TIMELINE
  - Put events in chronological order (left to right)
  - Resolve contradictions between experts
  - Identify missing events
  - Mark bounded context boundaries

PHASE 4: EXTRACT USER STORIES
  - Each command → aggregate → event = candidate user story
  - Policies → acceptance criteria
  - Read models → UI requirements
```

### Event Storming Artifact Map

```
EVENT STORMING: Order Management Domain
========================================

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
          ├── POLICY: Start payment timer
          │   └── EVENT: PaymentTimerStarted
          │
          ├── COMMAND: ProcessPayment
          │   └── AGGREGATE: Payment
          │       ├── EVENT: PaymentSucceeded
          │       │   ├── POLICY: Confirm order
          │       │   │   └── EVENT: OrderConfirmed
          │       │   └── POLICY: Notify warehouse
          │       │       └── EVENT: ShipmentRequested
          │       └── EVENT: PaymentFailed
          │           └── POLICY: Cancel order after 3 retries
          │               └── EVENT: OrderCancelled
          │
          └── READ MODEL: OrderSummary (for customer)
              READ MODEL: PendingPayments (for finance)
              READ MODEL: WarehouseQueue (for ops)
```

### Event Storming Rules
1. **No screens** — use physical or virtual sticky notes on a timeline
2. **Everyone participates** — devs, domain experts, product, UX, ops
3. **No naming things yet** — focus on events, not code structure
4. **Orange first** — discover events before modeling commands/aggregates
5. **Respect the timeline** — left-to-right chronological order
6. **Embrace conflicts** — disagreements reveal missing knowledge
7. **Timebox** — 2-4 hours for big picture, 2 hours for design level
8. **Photo everything** — the wall IS the documentation

## Step 14: Domain Storytelling (Stefan Hofer)

Source: Stefan Hofer, "Domain Storytelling" (2021), https://www.domainstorytelling.org/

Visual modeling technique where domain experts tell stories about how they work. Uses a pictographic notation with actors, work objects, and activities. Stories are told step-by-step, drawn as the expert narrates.

### Notation

```
DOMAIN STORYTELLING NOTATION
============================

ACTORS (People and Organizations):
  ┌──────────┐
  │  👤 User  │  = Person (circle + icon)
  └──────────┘
  ┌──────────┐
  │ 🏢 Org   │  = Organization (rectangle + icon)
  └──────────┘

WORK OBJECTS (Documents, Data, Physical Items):
  📄 Order      = Digital document
  📦 Package    = Physical object
  💰 Invoice    = Business artifact
  📧 Email      = Communication artifact

ACTIVITIES (Verbs):
  ────creates───▶     actor creates work object
  ────sends─────▶     actor sends work object to actor
  ────processes─▶     actor processes work object
  ────reads─────▶     actor reads work object
  ────archives──▶     actor archives work object

CONNECTORS:
  Actor ──verb──▶ Work Object ──verb──▶ Actor
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
8. 👤 Warehouse Worker ──creates─▶ 📄 ShippingLabel
9. 📦 Package + 📄 ShippingLabel ──sent──▶ 👤 Courier
10. 👤 Courier ──delivers───▶ 📦 Package ──to──▶ 👤 Customer
11. 👤 Customer ──confirms───▶ 📄 DeliveryConfirmation
12. 📄 DeliveryConfirmation ──sent──▶ 👤 Finance
13. 👤 Finance ──creates─────▶ 📄 Invoice
14. 📄 Invoice ──sent────────▶ 👤 Customer

INSIGHTS FROM STORY:
  - No automated inventory check between steps 3-5
  - Courier handoff (step 9) is manual, error-prone
  - Invoice created post-delivery (could be earlier)
  - Customer has no visibility between steps 5-10
```

### Domain Storytelling Workflow

```
DOMAIN STORYTELLING SESSION
===========================
Duration: 30-90 minutes per story
Participants: 1-2 domain experts + 1 modeler + observers

1. SETUP
   - Domain expert picks a concrete scenario (last Thursday, not "in general")
   - Modeler draws on whiteboard or tool (WPS Modeler, Excalidraw)

2. TELL & DRAW
   - Expert narrates: "First, I create an order..."
   - Modeler draws actor → verb → work object
   - Ask: "What happens next?" "Who does that go to?"
   - Number each step

3. REFINE
   - Walk through story backward (step N → step 1)
   - Ask: "Does this always happen?" (find variations)
   - Ask: "What if this fails?" (find exceptions)
   - Add alternative paths

4. COLLECT
   - Take photo/screenshot
   - Name the story with verb phrase ("How orders get fulfilled")
   - Note: who told it, when, which scenario

5. ANALYZE
   - Look for: handoffs, bottlenecks, missing steps, delays
   - Compare stories from different experts (find inconsistencies)
   - Extract: actors → roles, work objects → domain model, verbs → use cases
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

EXAMPLE:
  Domain story shows "Warehouse Worker manually counts stock"
  → User Story: "As a warehouse worker, I want automated stock counts"
  → NFR: "Stock count must complete in < 30 seconds for 10K SKUs"
  → Acceptance Criteria: Given 10K SKUs, When scan initiated, Then count in < 30s
```

## Step 15: Requirements for Platform Teams

Platform teams build internal products consumed by other engineering teams. Requirements differ from end-user product requirements — customers are developers, value is developer productivity.

### API Contract Requirements

```
API CONTRACT CHECKLIST
======================
DESIGN:
  [ ] API style chosen (REST, gRPC, GraphQL) with rationale
  [ ] OpenAPI 3.1 / gRPC proto / GraphQL schema published
  [ ] Consistent naming conventions (camelCase, plural resources)
  [ ] Standard error format (RFC 7807 Problem Details)
  [ ] Pagination pattern defined (cursor-based preferred)
  [ ] Filtering/sorting semantics documented
  [ ] Versioning strategy (URL path, header, content-type)
  [ ] Deprecation policy (minimum notice period, sunset header)

DOCUMENTATION:
  [ ] Interactive API docs (Swagger UI, Redoc, GraphQL Playground)
  [ ] Getting started guide (< 5 minutes to first call)
  [ ] Code examples in 3+ languages
  [ ] Changelog with migration guides
  [ ] Architecture decision records for key design choices

TESTING:
  [ ] Contract tests (Pact, Spring Cloud Contract)
  [ ] Consumer-driven contract tests
  [ ] Backward compatibility checks in CI (breaking change detection)
  [ ] Mock server for consumer development
  [ ] Integration test environment with realistic data

CONSUMPTION:
  [ ] SDK/client libraries for top 3 languages
  [ ] Rate limits documented (per-client, global)
  [ ] Authentication/authorization pattern (OAuth2, API keys, JWT)
  [ ] Webhook/callback pattern for async operations
  [ ] Retry-after headers for rate limiting
  [ ] Idempotency keys for non-idempotent operations
```

### SLA Requirements

```
PLATFORM SLA TEMPLATE
=====================
SERVICE: [Service Name]
TIER: [Gold/Silver/Bronze]

AVAILABILITY:
  Target: 99.9% (8.76 hrs downtime/year) / 99.95% / 99.99%
  Measurement: Successful requests / total requests (excluding 4xx)
  Exclusions: Scheduled maintenance (max 4 hrs/month, announced 72 hrs ahead)
  Incident response:
    P1 (service down): 15 min ack, 1 hr mitigation
    P2 (degraded): 30 min ack, 4 hr mitigation
    P3 (minor): 4 hr ack, 24 hr mitigation

PERFORMANCE:
  Latency: p50 < 50ms, p95 < 200ms, p99 < 500ms
  Throughput: > 10,000 requests/sec sustained
  Payload: max request body 10MB, max response 50MB

DATA:
  Durability: 99.999999999% (11 nines) for stored data
  Consistency: eventual (< 5 sec) / strong (per operation)
  Retention: 90 days default, configurable per tenant
  Backup: daily snapshots, 30-day retention, cross-region

RELIABILITY:
  Error budget: 0.1% = 43.8 min/month of errors allowed
  Circuit breaker: open after 5 consecutive failures
  Retry policy: exponential backoff, max 3 retries
  Timeout: 30s default, configurable per operation
  Bulkhead: per-client resource isolation

REPORTING:
  Status page: public real-time status
  Incident reports: post-mortem within 48 hrs
  Monthly SLA report: availability, latency, error budget
```

### Developer Experience (DX) Requirements

```
DEVELOPER EXPERIENCE CHECKLIST
===============================
ONBOARDING:
  [ ] Time to first API call < 5 minutes
  [ ] Quickstart tutorial (copy-paste-runnable)
  [ ] Sandbox environment with realistic data
  [ ] Self-service API key provisioning
  [ ] "Hello world" in README of every repo

DEBUGGING:
  [ ] Request tracing (trace-id in every response header)
  [ ] Error messages include: what happened, why, how to fix
  [ ] Structured logging (JSON) with correlation IDs
  [ ] Dashboard showing API health per consumer
  [ ] Changelog notification (email/Slack on breaking changes)

SELF-SERVICE:
  [ ] Developer portal with docs, examples, status
  [ ] CLI tool for common operations
  [ ] Terraform/Pulumi provider for infrastructure resources
  [ ] Self-service configuration (rate limits, webhooks)
  [ ] Feature flags for beta/preview features

FEEDBACK:
  [ ] GitHub Issues or internal ticket system
  [ ] Response time SLA for developer questions (< 24 hrs)
  [ ] Monthly developer satisfaction survey (NPS)
  [ ] Public roadmap with voting
  [ ] Office hours (weekly drop-in)

OBSERVABILITY:
  [ ] Consumer-facing dashboard (latency, errors, usage)
  [ ] Per-client usage metrics (requests, data transfer)
  [ ] Alerting on anomalous patterns (consumer side)
  [ ] Cost visibility (if usage-based pricing)
```

## Step 16: Cost of Delay Analysis (Don Reinertsen)

Source: Don Reinertsen, "The Principles of Product Development Flow" (2009)

Cost of delay (CoD) quantifies the economic impact of not delivering a feature. Used to make economically rational prioritization decisions instead of gut-feel.

### CD3 (Cost of Delay Divided by Duration)

```
CD3 = Cost of Delay / Duration

COST OF DELAY = Revenue lost per unit time + penalty costs + risk costs

EXAMPLE:
  Feature A: Online checkout redesign
    Revenue impact: $50K/week additional revenue
    Penalty costs: $0 (no regulatory)
    Risk costs: $5K/week (competitor launching similar)
    Cost of Delay: $55K/week
    Duration: 4 weeks
    CD3 = $55K / 4 = $13.75K per week of investment

  Feature B: GDPR compliance
    Revenue impact: $0 direct
    Penalty costs: $200K/week (regulatory fine risk)
    Risk costs: $10K/week (reputation)
    Cost of Delay: $210K/week
    Duration: 8 weeks
    CD3 = $210K / 8 = $26.25K per week of investment

  Feature C: Dark mode
    Revenue impact: $2K/week (retention improvement)
    Penalty costs: $0
    Risk costs: $0
    Cost of Delay: $2K/week
    Duration: 2 weeks
    CD3 = $2K / 2 = $1K per week of investment

PRIORITY ORDER: B ($26.25K) > A ($13.75K) > C ($1K)
```

### WSJF Detailed (Weighted Shortest Job First)

```
WSJF = Cost of Delay / Job Size (duration)

COST OF DELAY COMPONENTS (Fibonacci relative scoring: 1,2,3,5,8,13,20):

1. USER/BUSINESS VALUE
   How much value does this deliver to users or business?
   20 = critical for business survival
   13 = major revenue/growth impact
   8  = significant improvement
   5  = moderate value
   3  = nice to have
   2  = minor value
   1  = trivial

2. TIME CRITICALITY
   Does the value decay over time? Is there a deadline?
   20 = hard deadline (regulatory, contract, market window)
   13 = strong urgency (competitor advantage fading)
   8  = moderate urgency (customer expectation set)
   5  = some urgency (PR/announcement date)
   3  = mild urgency (Q4 OKR)
   2  = low urgency (no deadline)
   1  = no time pressure

3. RISK REDUCTION / OPPORTUNITY ENABLEMENT
   Does this reduce risk or enable future work?
   20 = eliminates existential risk / opens major new market
   13 = significant risk reduction / enables 3+ future features
   8  = moderate risk reduction / enables 2 future features
   5  = some risk reduction / enables 1 future feature
   3  = minor risk reduction
   2  = minimal risk reduction
   1  = no risk reduction

JOB SIZE (Fibonacci):
   Story points or t-shirt sizes converted to relative effort
   1, 2, 3, 5, 8, 13, 20

CALCULATION METHOD:
   1. Score smallest item = 1 for each CoD dimension
   2. Score all other items relative to smallest
   3. CoD = sum of 3 dimensions
   4. WSJF = CoD / Size
   5. Highest WSJF = highest priority

IMPORTANT RULES:
   - Always relative, never absolute
   - Score one dimension at a time across all items
   - Normalize by smallest item (gets score 1)
   - Job duration = entire value stream (not just dev time)
   - Split items with size > 13 before scoring
   - Re-score when new items enter backlog
```

### WSJF Full Example

```
WSJF SCORING WORKSHOP
=====================

BACKLOG ITEMS:
  1. Fix payment timeout bug
  2. Add multi-currency support
  3. GDPR data export feature
  4. Dark mode UI
  5. API rate limiting
  6. Mobile push notifications

STEP 1: Score User/Business Value (relative to smallest = 1)
  Payment bug:      13  (blocks transactions)
  Multi-currency:    8  (opens international market)
  GDPR export:      13  (regulatory requirement)
  Dark mode:         1  (minimal business impact)
  Rate limiting:      5  (prevents abuse)
  Push notifications: 5  (improves engagement)

STEP 2: Score Time Criticality
  Payment bug:      20  (losing customers NOW)
  Multi-currency:    5  (Q3 OKR)
  GDPR export:      20  (regulatory deadline)
  Dark mode:         1  (no deadline)
  Rate limiting:      8  (DOD review next month)
  Push notifications: 3  (planned for Q4)

STEP 3: Score Risk Reduction / Opportunity Enablement
  Payment bug:      13  (prevents churn)
  Multi-currency:    8  (enables EU expansion)
  GDPR export:      13  (prevents fines)
  Dark mode:         1  (no risk reduction)
  Rate limiting:      8  (enables partner API)
  Push notifications: 3  (enables notifications system)

STEP 4: Score Job Size
  Payment bug:       3  (small fix)
  Multi-currency:   13  (complex integration)
  GDPR export:       8  (data pipeline + UI)
  Dark mode:          5  (UI theming)
  Rate limiting:      5  (middleware + config)
  Push notifications: 8  (multiple platform integration)

STEP 5: Calculate WSJF
  Item            | Biz | Time | Risk | CoD | Size | WSJF
  ----------------|-----|------|------|-----|------|------
  Payment bug     | 13  | 20   | 13   | 46  | 3    | 15.3
  GDPR export     | 13  | 20   | 13   | 46  | 8    | 5.8
  Rate limiting    | 5   | 8    | 8    | 21  | 5    | 4.2
  Multi-currency  |  8  |  5   |  8   | 21  | 13   | 1.6
  Push notifs     |  5  |  3   |  3   | 11  | 8    | 1.4
  Dark mode       |  1  |  1   |  1   |  3  | 5    | 0.6

PRIORITY ORDER:
  1. Fix payment timeout bug (WSJF 15.3)
  2. GDPR data export (WSJF 5.8)
  3. API rate limiting (WSJF 4.2)
  4. Multi-currency support (WSJF 1.6)
  5. Push notifications (WSJF 1.4)
  6. Dark mode (WSJF 0.6)
```

### CoD/WSJF Anti-Patterns
1. **Don't score without domain experts** — devs alone miss business value
2. **Don't mix absolute and relative** — pick one scale and stick to it
3. **Don't ignore time decay** — some value expires (regulatory, market windows)
4. **Don't skip risk reduction** — platform/tech debt items often score high here
5. **Don't confuse effort with value** — small items aren't automatically high priority
6. **Don't score once and forget** — re-score quarterly or when context changes
7. **Don't forget transaction costs** — switching costs, context switching, coordination

## Step 17: Requirements Elicitation Techniques

Requirements elicitation = actively discovering stakeholder needs. No single technique captures all requirements — use a combination.

### Technique 1: Interviews

```
STRUCTURED INTERVIEW GUIDE
==========================

PREPARATION:
  - Identify interviewee role and context
  - Prepare semi-structured question guide (not rigid script)
  - Review existing documentation and prior interview notes
  - Set 45-60 min time limit, record with permission

QUESTION TYPES:
  OPEN-ENDED (explore):
    "Walk me through your typical [process]"
    "What frustrates you most about [current system]?"
    "If you could change one thing, what would it be?"

  PROBING (deepen):
    "Can you give me a specific example?"
    "What happened the last time that occurred?"
    "How often does that happen?"

  CLOSED (confirm):
    "Do you use [feature] daily or weekly?"
    "Is the issue in step 3 or step 4?"

  CONTEXTUAL (understand environment):
    "Show me how you do that right now"
    "What other tools do you use alongside this?"
    "Who else is involved in this process?"

INTERVIEW PROTOCOL:
  1. Build rapport (5 min) — casual chat, explain purpose
  2. Current state (15 min) — "How do you do X today?"
  3. Pain points (15 min) — "What's broken? What's slow?"
  4. Desired state (15 min) — "What would ideal look like?"
  5. Priorities (5 min) — "If you could fix one thing first?"
  6. Wrap-up (5 min) — "Anything I didn't ask about?"

ANTI-PATTERNS:
  - Leading questions: "Don't you think we need X?"
  - Solution bias: "Would you use a button that does X?"
  - Dominant interviewee: redirect to questions
  - No follow-up: schedule 2nd interview if needed
```

### Technique 2: Surveys

```
SURVEY DESIGN GUIDE
===================

WHEN TO USE:
  - Large stakeholder group (> 20 people)
  - Validate findings from interviews
  - Quantify frequency/severity of issues
  - Remote/distributed stakeholders

QUESTION DESIGN:
  LIKERT SCALE (satisfaction/frequency):
    "How satisfied are you with the current search feature?"
    [1] Very dissatisfied  [2] Dissatisfied  [3] Neutral  [4] Satisfied  [5] Very satisfied

  RANKING (prioritization):
    "Rank these features by importance to your workflow:"
    [ ] Advanced search  [ ] Export  [ ] Dashboards  [ ] Notifications  [ ] API access

  MULTIPLE CHOICE (usage patterns):
    "How often do you use the reporting feature?"
    [ ] Daily  [ ] Weekly  [ ] Monthly  [ ] Rarely  [ ] Never

  OPEN TEXT (discovery):
    "What's the biggest obstacle in your daily workflow?"

SURVEY RULES:
  - Max 15 questions (completion rate drops after 10 min)
  - Start with easy questions, end with open text
  - Avoid double-barreled questions ("Is X fast and reliable?")
  - Avoid negation ("Don't you think X is bad?")
  - Pilot test with 3-5 people before full deployment
  - Incentivize completion (gift card, recognition)
  - Response rate target: > 40% for internal, > 10% for external
```

### Technique 3: Observation (Shadowing)

```
OBSERVATION GUIDE
=================

WHEN TO USE:
  - Users can't articulate their own workflow (tacit knowledge)
  - Physical environment matters (warehouse, factory, field)
  - Current process has undocumented workarounds
  - Validating what people say vs. what they do

OBSERVATION PROTOCOL:
  1. GET PERMISSION — explain purpose, get consent to observe
  2. MINIMAL INTERFERENCE — observe silently, note questions for later
  3. CONTEXTUAL INQUIRY — observe in natural work environment
  4. NOTE WORKAROUNDS — sticky notes, spreadsheets, personal tools
  5. TIMING — measure durations of key activities
  6. ARTIFACTS — photograph/digitize documents, forms, screens

WHAT TO OBSERVE:
  [ ] Sequence of steps (actual, not prescribed)
  [ ] Time spent per step
  [ ] Errors and recovery actions
  [ ] Workarounds and personal tools
  [ ] Handoffs between people/systems
  [ ] Information needs (what they look up, when)
  [ ] Frustration signals (sighs, repetitions, complaints)
  [ ] Environmental factors (noise, interruptions, multitasking)

DATA CAPTURE:
  ┌─────────┬──────────┬────────────┬──────────────┬───────────┐
  │ Time    │ Actor    │ Action     │ Work Object  │ Notes     │
  ├─────────┼──────────┼────────────┼──────────────┼───────────┤
  │ 9:02    │ Sarah    │ Opens CRM  │ Customer rec │ Slow load │
  │ 9:03    │ Sarah    │ Calls      │ Customer     │ Gets VM   │
  │ 9:05    │ Sarah    │ Logs note  │ CRM activity │ Must click│
  │         │          │            │              │ 3 times   │
  │ 9:08    │ Sarah    │ Opens SS   │ Order lookup │ Copy-paste│
  │         │          │            │              │ ID manual │
  └─────────┴──────────┴────────────┴──────────────┴───────────┘
  INSIGHT: 6 min per customer, 30% spent on tool switching
```

### Technique 4: Prototyping

```
PROTOTYPING GUIDE
=================

FIDELITY LEVELS:

PAPER (30 min):
  - Sketches on paper/whiteboard
  - For: early concept validation, brainstorming
  - Audience: internal team, friendly stakeholders

LOW-FI DIGITAL (2-4 hrs):
  - Wireframes (Balsamiq, Whimsical)
  - For: flow validation, layout feedback
  - Audience: product team, stakeholders

INTERACTIVE (1-3 days):
  - Clickable mockup (Figma, Sketch, Adobe XD)
  - For: user testing, stakeholder demos
  - Audience: end users, executives

HIGH-FI (1-2 weeks):
  - Near-production UI with realistic data
  - For: final validation, developer handoff
  - Audience: all stakeholders

PROTOTYPING PROTOCOL:
  1. Define hypothesis: "Users can complete checkout in < 3 clicks"
  2. Build minimum prototype to test hypothesis
  3. Test with 5 users (Nielsen: 5 finds 85% of issues)
  4. Observe behavior, note confusion points
  5. Iterate: modify prototype, re-test
  6. Document findings → requirements

CONCIERGE PROTOTYPE:
  - No code, human performs the service manually
  - Example: manually send personalized recommendations via email
  - Validates: demand, workflow, value proposition
  - Before building any automation

A/B TEST PROTOTYPE:
  - Two variants deployed to subset of users
  - Measure: conversion, engagement, error rate
  - Statistical significance: p < 0.05, minimum sample size calculated
  - Duration: at least 2 full business cycles
```

### Technique 5: Brainstorming

```
BRAINSTORMING GUIDE
===================

STRUCTURED BRAINSTORMING (prevents groupthink):

METHOD 1: BRAINWRITING (6-3-5)
  6 people, 3 ideas each, 5 minutes per round
  1. Each person writes 3 ideas on paper (5 min)
  2. Pass paper to next person
  3. Read previous ideas, write 3 more (5 min)
  4. Repeat until all papers return to originator
  5. Result: 108 ideas in 30 minutes
  6. Cluster and vote

METHOD 2: REVERSE BRAINSTORMING
  "How could we make this WORSE?"
  1. List all ways to make problem worse
  2. Reverse each item into a solution
  3. Example: "Make checkout take 10 steps" → "Reduce to 1 step"

METHOD 3: WORST POSSIBLE IDEA
  Deliberately generate terrible ideas
  1. "What's the worst feature we could build?"
  2. Discuss why each is bad
  3. Flip bad ideas into good ones
  4. Liberates creativity, removes judgment fear

METHOD 4: HOW MIGHT WE (HMW)
  Frame opportunities as questions
  Format: "How might we [action] so that [outcome]?"
  Examples:
    - "How might we reduce checkout steps so that abandonment drops?"
    - "How might we notify users so that they never miss a delivery?"
    - "How might we simplify onboarding so that time-to-value < 5 min?"

VOTING:
  DOT VOTING: each person gets 3-5 dots, vote on best ideas
  IMPACT/FEASIBILITY MATRIX: plot ideas on 2x2 grid
  MULTI-VOTING: rank top 3, sum scores, prioritize

SESSION PROTOCOL:
  1. Frame problem (5 min)
  2. Diverge — generate ideas, no judgment (15-30 min)
  3. Cluster — group similar ideas (10 min)
  4. Converge — vote and rank (10 min)
  5. Select — top 3-5 ideas for further exploration
  6. Document — photos, notes, decision rationale
```

### Elicitation Technique Selection Guide

```
TECHNIQUE SELECTION MATRIX
==========================
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

## Step 18: Acceptance Test-Driven Development (ATDD)

Source: Kent Beck, "Test-Driven Development" (2003); Lisa Crispin & Janet Gregory, "Agile Testing" (2009)

ATDD starts with acceptance tests written before implementation. The "Three Amigos" (developer, tester, business analyst) collaborate to define acceptance criteria as executable tests.

### ATDD Workflow (Detailed)

```
ATDD CYCLE: DISCUSS → DISTILL → DEVELOP → DEMO
================================================

PHASE 1: DISCUSS (Three Amigos Meeting)
  Participants: Developer, Tester, Business Analyst (or Product Owner)
  Duration: 30-60 minutes per story
  Output: Shared understanding + concrete examples

  STEPS:
    1. Read user story aloud
    2. BA presents acceptance criteria
    3. Ask: "Can you give me an example?"
    4. Ask: "What about edge cases?"
    5. Ask: "What could go wrong?"
    6. Write examples on whiteboard/sticky notes
    7. Agree on scope: what's in/out for this story

  EXAMPLE:
    STORY: "As a customer, I want to apply a discount code"
    DISCUSSION:
      BA: "Customer enters code at checkout, price reduces"
      Dev: "What if code is expired?"
      Test: "What if they enter two codes?"
      BA: "Only one code per order. Expired shows error."
      Test: "What error message?"
      BA: "'This discount code has expired'"
      Dev: "Case-sensitive codes?"
      BA: "No, case-insensitive"
    EXAMPLES CAPTURED:
      - "SAVE20" on $100 → $80
      - "save20" on $100 → $80 (case insensitive)
      - Expired code → error: "This discount code has expired"
      - Two codes → error: "Only one discount code per order"

PHASE 2: DISTILL (Write Acceptance Tests)
  Convert examples into executable specifications
  Use Given/When/Then (Gherkin) or equivalent

  GHERKIN:
    Feature: Apply discount code
      Scenario: Valid percentage discount
        Given a cart with total $100.00
        And a discount code "SAVE20" with 20% off
        When the customer applies discount code "SAVE20"
        Then the cart total becomes $80.00
        And the discount "SAVE20" is shown in order summary

      Scenario: Case-insensitive code
        Given a cart with total $100.00
        And a discount code "SAVE20" with 20% off
        When the customer applies discount code "save20"
        Then the cart total becomes $80.00

      Scenario: Expired discount code
        Given a discount code "SUMMER24" that expired on 2024-08-31
        When the customer applies discount code "SUMMER24"
        Then the error "This discount code has expired" is shown
        And the cart total remains unchanged

      Scenario: Only one code per order
        Given a cart with discount code "SAVE20" already applied
        When the customer applies discount code "SAVE10"
        Then the error "Only one discount code per order" is shown
        And the cart still has "SAVE20" applied

PHASE 3: DEVELOP (Implement to Pass Tests)
  Standard TDD loop AT EACH ACCEPTANCE TEST LEVEL:

  For each scenario:
    1. RUN TEST → RED (test fails, expected)
    2. Write minimum code to make test pass
    3. RUN TEST → GREEN (test passes)
    4. REFACTOR (improve code, keep tests green)
    5. Move to next scenario

  IMPLEMENTATION LAYERS:
    ┌─────────────────────────────┐
    │  Acceptance Tests (Gherkin) │  ← What the system does
    ├─────────────────────────────┤
    │  Step Definitions (Glue)    │  ← How to drive the system
    ├─────────────────────────────┤
    │  System Under Test (SUT)    │  ← Business logic + UI + DB
    └─────────────────────────────┘

  AUTOMATION EXAMPLE (pytest-bdd):
    # tests/test_discount.py
    from pytest_bdd import scenarios, given, when, then, parsers

    scenarios('../features/discount.feature')

    @given(parsers.parse('a cart with total ${total:f}'))
    def cart_with_total(cart, total):
        cart.set_total(total)

    @given(parsers.parse('a discount code "{code}" with {pct:d}% off'))
    def discount_code(db, code, pct):
        db.create_discount(code=code, percent=pct, expires=None)

    @when(parsers.parse('the customer applies discount code "{code}"'))
    def apply_discount(cart, code):
        cart.apply_discount(code)

    @then(parsers.parse('the cart total becomes ${expected:f}'))
    def verify_total(cart, expected):
        assert cart.total == expected

    @then(parsers.parse('the error "{message}" is shown'))
    def verify_error(cart, message):
        assert cart.last_error == message

PHASE 4: DEMO (Stakeholder Review)
  1. Run ALL acceptance tests in front of stakeholders
  2. Show passing scenarios = working requirements
  3. Use as living documentation
  4. Identify missing scenarios during demo
  5. Capture new scenarios → add to test suite
  6. Sign off: "Requirements met" or "Needs changes"
```

### ATDD Integration with BDD

```
ATDD + BDD COMBINED WORKFLOW
=============================

  User Story
      │
      ▼
  Three Amigos (Discuss)
      │
      ▼
  Example Mapping (25 min)
      │
      ▼
  Acceptance Criteria (Gherkin Scenarios)
      │
      ├─── Write failing acceptance test (RED)
      │         │
      │         ▼
      │    TDD inner loop:
      │      Unit test → RED → Code → GREEN → Refactor
      │         │
      │         ▼
      ├─── Acceptance test passes (GREEN)
      │
      ▼
  Demo to stakeholders
      │
      ▼
  Living documentation (auto-generated from tests)
      │
      ▼
  Requirements Traceability Matrix (link scenario ↔ requirement ↔ code)
```

### ATDD Anti-Patterns
1. **Don't skip Three Amigos** — writing tests alone misses perspectives
2. **Don't write tests after code** — that's just testing, not ATDD
3. **Don't automate GUI** — test at service/API layer, GUI is too brittle
4. **Don't make tests dependent on each other** — each scenario independent
5. **Don't ignore failing tests** — red test = unresolved requirement
6. **Don't write mega-scenarios** — one scenario = one behavior
7. **Don't skip the demo** — stakeholder feedback catches missed requirements
8. **Don't treat test code as second-class** — refactor test code too

## How These Practices Connect

### Design Thinking → OKR Alignment → Impact Mapping

Source: IDEO, https://designthinking.ideo.com/; John Doerr, "Measure What Matters" (2018)

Design thinking discovers user needs. OKRs translate needs into measurable outcomes.
Impact maps bridge outcomes to deliverables.

DESIGN THINKING PHASES:
  1. EMPATHIZE - User interviews, shadowing, journey mapping
     Output: Empathy maps, pain point inventory, user personas (evidence-based)
  2. DEFINE - Synthesize findings into problem statements
     Output: "How Might We" statements, problem framing
  3. IDEATE - Divergent thinking, solution brainstorming
     Output: Solution concepts, assumption mapping
  4. PROTOTYPE - Low-fi wireframes, clickable mockups, service blueprints
     Output: Testable artifacts (paper, Figma, concierge)
  5. TEST - Validate prototypes with real users
     Output: Validated learnings, pivoted or refined requirements

INTEGRATION WITH REQUIREMENTS:
  Empathy maps → JTBD discovery
  Problem statements → OKR Objectives
  Validated prototypes → User Stories with acceptance criteria
  Assumption map → RICE confidence scores

OKR ALIGNMENT PATTERN:
  OBJECTIVE: [Qualitative, inspiring goal]
  ├── KEY RESULT 1: [Measurable outcome, specific metric + target]
  │   ├── USER STORY 1.1: As a [role], I want [capability], so that [benefit]
  │   │   ├── AC: Given/When/Then
  │   │   └── NFR: [performance/security/a11y constraint]
  │   └── USER STORY 1.2: ...
  ├── KEY RESULT 2: [Measurable outcome]
  │   ├── USER STORY 2.1: ...
  │   └── USER STORY 2.2: ...
  └── KEY RESULT 3: [Measurable outcome]
      └── USER STORY 3.1: ...

EXAMPLE:
  OBJECTIVE: Become the most trusted checkout experience in e-commerce
  ├── KR1: Reduce checkout abandonment from 68% to 45%
  │   ├── US: Guest checkout (no forced account creation)
  │   ├── US: Auto-fill address from postal code
  │   └── US: Save cart across sessions
  ├── KR2: Achieve 99.9% payment success rate
  │   ├── US: Retry failed payments with alternate provider
  │   └── US: Pre-validate card before order submission
  └── KR3: NPS score from 32 to 55 for checkout flow
      ├── US: Real-time order total (no surprise fees)
      └── US: Delivery date estimate before payment

TRACEABILITY: Each User Story traces up to KR, each KR traces to Objective.
RTM column "Goal" now links to OKR ID, not just business goal ID.

### AI/ML Feature Requirements

When requirements include AI/ML features, add these categories alongside standard requirements.

DATA REQUIREMENTS:
  [ ] Training data source documented (origin, license, refresh cadence)
  [ ] Data volume specified (minimum rows/samples for acceptable performance)
  [ ] Data quality checks: completeness, accuracy, consistency, timeliness
  [ ] Data labeling process defined (human-in-the-loop, inter-annotator agreement >= 0.8)
  [ ] Feature store schema documented (feature name, type, source, freshness)
  [ ] PII handling in training data (anonymization, consent, retention)
  [ ] Data versioning (DVC, Delta Lake, or equivalent)
  [ ] Train/validation/test split ratios specified (e.g., 70/15/15)

MODEL METRICS:
  [ ] Primary metric defined (accuracy, F1, BLEU, AUC-ROC, MAE, etc.)
  [ ] Minimum threshold for deployment (e.g., F1 >= 0.85)
  [ ] Baseline model score documented (to measure improvement)
  [ ] Latency budget: inference time p95/p99 (e.g., < 100ms p95)
  [ ] Throughput requirement (predictions/second)
  [ ] Model size constraint (if edge/mobile deployment)
  [ ] Monitoring: drift detection (data drift, concept drift)
  [ ] Retraining trigger: metric drop below threshold OR scheduled cadence

BIAS AND FAIRNESS TESTING:
  [ ] Protected attributes identified (race, gender, age, disability, etc.)
  [ ] Fairness metrics defined: demographic parity, equalized odds, predictive parity
  [ ] Bias testing on subgroups (intersectional: e.g., young women of color)
  [ ] Disparate impact ratio >= 0.8 (four-fifths rule)
  [ ] Model card published (Mitchell et al., 2019) documenting intended use, limitations, fairness evaluation
  [ ] Adversarial testing: prompt injection (LLMs), adversarial examples (CV), data poisoning
  [ ] Human review loop for high-stakes predictions (credit, health, hiring, criminal justice)
  [ ] Explainability: SHAP/LIME for tabular, attention maps for NLP/CV (when required)

MLOPS REQUIREMENTS:
  [ ] Model registry (MLflow, SageMaker, Vertex AI)
  [ ] Experiment tracking (parameters, metrics, artifacts)
  [ ] CI/CD for model pipeline (train → evaluate → register → deploy)
  [ ] A/B testing or canary deployment for model updates
  [ ] Rollback mechanism for model version
  [ ] Feature pipeline monitoring (stale features, missing data alerts)
  [ ] Model serving SLA (availability, latency, error rate)

GHERKIN FOR AI/ML:
  Scenario: Model rejects low-confidence prediction
    Given the fraud detection model is deployed
    When a transaction is scored with confidence < 0.7
    Then the transaction is flagged for human review
    And the prediction is logged with confidence score

  Scenario: Model retraining triggered on drift
    Given the model monitors data drift hourly
    When KL-divergence exceeds 0.15 for any feature
    Then a retraining job is queued
    And the on-call engineer is notified

### Connection Diagram

```
Design Thinking (empathize + define)
    │
    ▼
JTBD (discover needs)
    │
    ▼
Requirements Elicitation (interviews, surveys, observation, prototyping, brainstorming)
    │
    ▼
Event Storming (domain events, commands, aggregates)
    │
    ▼
Domain Storytelling (actors, work objects, processes)
    │
    ▼
OKR Alignment (Objectives → Key Results)
    │
    ▼
Impact Mapping (strategic goals)
    │
    ▼
Cost of Delay Analysis (CD3, WSJF) / RICE (prioritize)
    │
    ▼
User Story Mapping (release planning)
    │
    ▼
User Story Splitting (sprint-sized)
    │
    ▼
Example Mapping (story refinement)
    │
    ▼
Specification by Example (formalization)
    │
    ▼
ATDD (acceptance test-driven development)
    │
    ▼
BDD/Gherkin (automation)
    │
    ▼
Acceptance Criteria + NFRs + Security (STRIDE/ASVS) + A11y (WCAG 2.2) (verification)
    │
    ▼
Platform Team Requirements (API contracts, SLAs, DX) - if applicable
    │
    ▼
Requirements Traceability Matrix (audit)
    │
    ▼
AI/ML Requirements (data, metrics, bias) - if applicable
```

## Pitfalls

1. **Don't write flat backlogs** — use story mapping for 2D visualization
2. **Don't skip example mapping** — 25 min saves hours of rework
3. **Don't write GUI-level Gherkin** — declarative, business-language steps
4. **Don't skip acceptance criteria** — every story needs clear AC
5. **Don't over-specify** — focus on business rules, not implementation
6. **Don't skip living documentation** — specs must stay in sync with code
7. **Don't write mega-scenarios** — one scenario = one behavior
8. **Don't skip impact mapping** — connects deliverables to business goals
9. **Don't confuse JTBD with features** — jobs are progress in a circumstance, not solutions
10. **Don't score RICE without data** — confidence < 50% needs a research spike
11. **Don't use absolute effort in WSJF** — always relative sizing
12. **Don't accept stories > 1 sprint** — split first using patterns
13. **Don't skip NFRs** — performance, security, accessibility are requirements, not nice-to-haves
14. **Don't skip traceability** — orphan requirements and gold-plating creep in without RTM
15. **Don't skip OKR alignment** — requirements without measurable outcomes are guesswork
16. **Don't confuse empathy with assumptions** — design thinking requires real user contact, not personas from thin air
17. **Don't treat AI/ML like normal features** — data quality, model metrics, and bias testing are first-class requirements
18. **Don't bolt on security** — STRIDE threat model early, map to OWASP ASVS, trace to tests
19. **Don't test accessibility last** — WCAG 2.2 AA baked into acceptance criteria from story creation
20. **Don't skip event storming for complex domains** — big picture on one wall prevents costly misunderstandings
21. **Don't confuse domain storytelling with UML** — stories are told by experts, not drawn by architects
22. **Don't treat platform APIs like internal code** — API contracts, SLAs, and DX are first-class requirements
23. **Don't prioritize by gut feel alone** — CD3 and WSJF make cost of delay explicit and comparable
24. **Don't rely on single elicitation technique** — combine interviews, surveys, observation, prototyping, brainstorming
25. **Don't write acceptance tests after code** — ATDD with Three Amigos catches requirement gaps before implementation
