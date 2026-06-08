---
name: sdlc-requirements-engineering
description: "Requirements: user story mapping (Jeff Patton), BDD/Gherkin (Cucumber, pytest-bdd), acceptance criteria (Given/When/Then), impact mapping (Gojko Adzic), example mapping, specification by example, JTBD framework, RICE scoring, WSJF prioritization, user story splitting, NFR patterns, requirements traceability, OKR alignment, design thinking, AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility."
version: 3.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, requirements, user-stories, bdd, gherkin, acceptance-criteria, impact-mapping, example-mapping, specification-by-example, jtbd, rice-scoring, wsjf, story-splitting, nfr, traceability, okr, design-thinking, ai-ml, stride, owasp-asvs, wcag-2.2, accessibility]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-prd-to-production]
---

# Requirements Engineering

User story mapping, BDD/Gherkin, acceptance criteria, impact mapping, example mapping, specification by example, JTBD, RICE scoring, WSJF, user story splitting, NFR patterns, requirements traceability, OKR alignment, design thinking, AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility.

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
OKR Alignment (Objectives → Key Results)
    │
    ▼
Impact Mapping (strategic goals)
    │
    ▼
RICE / WSJF (prioritize)
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
BDD/Gherkin (automation)
    │
    ▼
Acceptance Criteria + NFRs + Security (STRIDE/ASVS) + A11y (WCAG 2.2) (verification)
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
