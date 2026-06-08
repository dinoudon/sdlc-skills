---
name: sdlc-requirements-engineering
description: "Requirements: user story mapping (Jeff Patton), BDD/Gherkin (Cucumber, pytest-bdd), acceptance criteria (Given/When/Then), impact mapping (Gojko Adzic), example mapping, specification by example (Gojko Adzic), JTBD framework, RICE scoring, WSJF prioritization, user story splitting, NFR patterns, requirements traceability, OKR alignment (Doerr), design thinking, Lean UX (Gothelf), design sprint (Knapp), dual-track agile (Cagan), AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility, event storming (Brandolini), domain storytelling (Hofer), platform team requirements, cost of delay (Reinertsen), requirements elicitation techniques, ATDD, strategic DDD (bounded contexts, context mapping, ubiquitous language), tactical DDD (entities, value objects, aggregates, domain events, repositories, domain services, factories), event storming workshop (6-phase, process+design level), domain storytelling workshop (notation, discovery patterns)."
version: 4.7.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, requirements, user-stories, bdd, gherkin, acceptance-criteria, impact-mapping, example-mapping, specification-by-example, jtbd, rice-scoring, wsjf, story-splitting, nfr, traceability, okr, design-thinking, lean-ux, design-sprint, dual-track-agile, ai-ml, stride, owasp-asvs, wcag-2.2, accessibility, event-storming, domain-storytelling, platform-teams, cost-of-delay, elicitation, atdd, strategic-ddd, tactical-ddd, bounded-contexts, context-mapping, ubiquitous-language, domain-events, aggregates, value-objects, event-storming-workshop, domain-storytelling-workshop]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-prd-to-production, sdlc-domain-driven-design]
---

# Requirements Engineering

User story mapping, BDD/Gherkin, acceptance criteria, impact mapping, example mapping, specification by example, JTBD, RICE scoring, WSJF, user story splitting, NFR patterns, requirements traceability, OKR alignment, design thinking, Lean UX, design sprint, dual-track agile, AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility, event storming (Brandolini), domain storytelling (Hofer), platform team requirements, cost of delay analysis (Reinertsen), requirements elicitation techniques, ATDD, strategic DDD (bounded contexts, context mapping, ubiquitous language), tactical DDD (entities, value objects, aggregates, domain events, repositories, domain services, factories), event storming workshop (6-phase, process+design level), domain storytelling workshop (notation, discovery patterns).

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
- Runs event storming workshops (domain events, commands, aggregates, 6-phase process)
- Creates domain storytelling diagrams (actors, work objects, processes, notation-based)
- Designs bounded contexts and context maps for complex domains
- Applies DDD strategic patterns (partnership, customer-supplier, conformist, ACL, OHS, shared kernel)
- Models domain with tactical DDD (entities, value objects, aggregates, domain events, repositories)
- Defines ubiquitous language within bounded contexts
- Runs event storming at process-level and design-level
- Uses domain storytelling to discover bounded contexts, aggregates, and events
- Defines platform team requirements (API contracts, SLAs, developer experience)
- Analyzes cost of delay using CD3 or WSJF prioritization
- Conducts requirements elicitation (interviews, surveys, observation, prototyping, brainstorming)
- Sets up ATDD (acceptance test-driven development) workflows
- Runs design sprint workshops (5-day: Understand/Diverge/Decide/Prototype/Validate)
- Applies Lean UX hypothesis-driven design (Think/Make/Check loop, assumptions mapping)
- Uses dual-track agile (discovery validates before delivery builds)
- Aligns requirements to OKRs and traces KRs to user stories
- Scopes via impact mapping (Goal→Actor→Impact→Deliverable, cutting deliverables)
- Formalizes specification by example (6 practices, BDD at scale, living documentation)
- Models strategic DDD (bounded contexts, context mapping, ubiquitous language)
- Applies tactical DDD (entities, value objects, aggregates, domain events, repositories, domain services, factories)
- Facilitates event storming workshops (6-phase: chaotic exploration → aggregates)
- Uses domain storytelling to discover domain boundaries and aggregates

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

## Step 19: Impact Mapping (Gojko Adzic)

Source: Gojko Adzic, "Impact Mapping" (2012), https://www.impactmapping.org/

4-level mind map connecting business goals to deliverables. Scoping tool: cut deliverables that don't serve the goal.

### 4 Levels
```
IMPACT MAP STRUCTURE
====================

Level 1: GOAL (WHY are we doing this?)
  - Measurable business objective
  - Time-bound
  - Example: "Increase trial-to-paid conversion from 12% to 25% by Q3"

Level 2: ACTORS (WHO can help/hinder the goal?)
  - Primary: direct users
  - Secondary: indirect stakeholders (admins, support, partners)
  - Example: "Trial user", "Account manager", "Support agent"

Level 3: IMPACTS (HOW should actors' behavior change?)
  - Behavior changes that achieve the goal
  - NOT deliverables — think "user does X" not "we build Y"
  - Example: "Trial user explores 3+ features in first session"

Level 4: DELIVERABLES (WHAT can we build/do to cause impacts?)
  - Features, campaigns, process changes
  - Multiple deliverables per impact (brainstorm)
  - Example: "Interactive product tour", "Feature recommendation email"
```

### Scoping by Cutting Deliverables
```
SCOPING PROCESS
===============

Step 1: Map all 4 levels (diverge — brainstorm everything)
Step 2: For each deliverable ask: "Does this DIRECTLY cause the impact?"
Step 3: Cut deliverables where impact is uncertain or indirect
Step 4: For remaining deliverables ask: "Is there a simpler way to cause this impact?"
Step 5: Prioritize remaining deliverables by effort vs impact certainty

CUTTING EXAMPLE:
  GOAL: Reduce support tickets by 40%
  ├── ACTOR: End user
  │   ├── IMPACT: Solves common issues without contacting support
  │   │   ├── DELIVERABLE: In-app help center ← KEEP (direct)
  │   │   ├── DELIVERABLE: AI chatbot ← CUT (high effort, uncertain impact)
  │   │   └── DELIVERABLE: Video tutorials ← KEEP (low effort, direct)
  │   └── IMPACT: Avoids errors that cause tickets
  │       ├── DELIVERABLE: Input validation ← KEEP (direct)
  │       └── DELIVERABLE: Complete UI redesign ← CUT (scope creep)
  │
  └── ACTOR: Support agent
      └── IMPACT: Resolves tickets faster
          ├── DELIVERABLE: Canned responses ← KEEP (direct)
          └── DELIVERABLE: AI-powered ticket routing ← CUT (defer to Phase 2)
```

### Impact Map Anti-Patterns
1. **Too many goals** — one map = one goal; multiple goals = multiple maps
2. **Vague goals** — "improve UX" is not measurable; "reduce task completion time from 5min to 2min" is
3. **Missing actors** — forgetting internal actors (ops, support, compliance)
4. **Impacts as deliverables** — "build feature X" is not an impact; "user completes task Y" is
5. **No cutting** — the map becomes a wishlist, not a scoping tool
6. **Building full map alone** — collaborative with business + tech + UX

### Integration with User Story Mapping
Impact map deliverables → Story map backbone tasks → User stories in backlog
Each deliverable from impact map maps to one or more user activities on story map.

## Step 20: Specification by Example

Source: Gojko Adzic, "Specification by Example" (2011), https://specificationbyexample.com/

6 key practices for executable specifications. Living documentation that stays in sync with code.

### 6 Key Practices
```
PRACTICE 1: DERIVING SCOPE FROM GOALS
  Start from business goals, not features
  Impact map → Example mapping → Spec
  Example: Goal "reduce returns" → Spec "Size recommendation accuracy"

PRACTICE 2: SPECIFYING COLLABORATIVELY
  Three Amigos: BA + Dev + Tester
  Example mapping (25 min) before writing specs
  Shared ownership — not BA throws docs over wall

PRACTICE 3: ILLUSTRATING WITH EXAMPLES
  Concrete examples > abstract rules
  "Valid email" → "user@example.com ✓", "user@ ✗"
  Examples are the specification, not illustrations of it

PRACTICE 4: EXTRACTING SPECIFICATION
  From examples, extract rules (business logic)
  Group examples by rule
  Identify gaps: "What about edge case X?"

PRACTICE 5: IMPLEMENTING SPECIFICATION
  Convert examples to executable tests (Gherkin)
  Automation layer: step definitions drive system
  Tests ARE the specification

PRACTICE 6: VALIDATING FREQUENTLY
  Run specs as regression suite
  Failing spec = requirement change detected
  Specs always reflect current behavior
```

### BDD at Scale
```
SCALING PATTERN
===============

Level 1: Feature Files (per capability)
  features/
    checkout/
      discount.feature
      shipping.feature
    account/
      registration.feature
      profile.feature

Level 2: Step Definitions (reusable)
  steps/
    common.py      # shared steps: "Given user is logged in"
    checkout.py    # domain-specific steps
    account.py

Level 3: Domain-Specific Language (DSL)
  Page objects, API clients, test helpers
  Abstraction layer: specs speak business, DSL speaks system

Level 4: Living Documentation (auto-generated)
  Tools: Pickles, Relish, SpecFlow LivingDoc
  Publish spec status: green = all pass, red = regression
  Non-technical stakeholders read living docs, not code
```

### Living Documentation
```
LIVING DOCUMENTATION PIPELINE
=============================

  Source Code (Gherkin .feature files)
      │
      ▼
  CI/CD Pipeline (run all specs)
      │
      ├── PASS → Generate HTML report
      │           │
      │           ▼
      │     Publish to docs portal
      │     (auto-updated on every commit)
      │
      └── FAIL → Alert team
                  │
                  ▼
            Spec = living regression test
            Failure = requirement change detected

BENEFITS:
  - Specs always current (failing tests break build)
  - Single source of truth (code IS the spec)
  - Non-technical stakeholders can verify behavior
  - Onboarding: read specs to understand system
```

### Example Mapping (Matt Wynne)

Source: Matt Wynne, "Example Mapping" (2015), https://cucumber.io/blog/example-mapping-introduction/

25-minute structured discovery sessions. Four colors, one rule per card type.

```
EXAMPLE MAPPING SESSION
========================

SETUP:
  - Timer: 25 minutes (strict)
  - Participants: Three Amigos (BA + Dev + Tester)
  - Materials: colored index cards or sticky notes

FOUR CARD COLORS:
  YELLOW (top)  = Story being discussed (one per session)
  BLUE          = Rules (business rules that govern the story)
  GREEN         = Examples (concrete examples illustrating rules)
  RED           = Questions (things we don't know yet)

SESSION FLOW:
  1. BA writes YELLOW card with story title (1 min)
  2. Team identifies BLUE rule cards (5 min)
     - "What rules govern this story?"
     - "What constraints apply?"
  3. Team writes GREEN example cards under each rule (10 min)
     - Concrete inputs/outputs
     - Edge cases, boundary values
     - Happy path AND error paths
  4. RED question cards placed whenever:
     - Team disagrees on behavior
     - Example reveals ambiguity
     - External dependency unknown
  5. Decision at 25 min:
     - All GREEN, no RED → ready to code
     - RED cards exist → story not ready, resolve questions first
     - Too many rules → split story

EXAMPLE:
  YELLOW: "Apply discount code at checkout"
  ├── BLUE: "Code must be valid and not expired"
  │   ├── GREEN: "SAVE20 applied → 20% off"
  │   ├── GREEN: "EXPIRED code → error message"
  │   └── GREEN: "FAKE code → error message"
  ├── BLUE: "One code per order"
  │   ├── GREEN: "Second code rejected with message"
  │   └── GREEN: "Replace existing code allowed"
  ├── BLUE: "Minimum order amount required"
  │   ├── GREEN: "Order $49, min $50 → error"
  │   └── GREEN: "Order $50, min $50 → discount applied"
  └── RED: "Can staff override max codes per order?"

RULES:
  - One story per session (go deeper, not wider)
  - No laptops open (face the cards, face each other)
  - Facilitator keeps time, not content
  - Questions are progress, not failure
  - If session ends with no RED cards AND < 5 rules → story is ready
```

### Feature File Organization
```
FEATURE FILE DIRECTORY STRUCTURE
==================================

features/
  domain/                         # organize by business domain, not UI layer
    checkout/
      discount_codes.feature
      shipping_options.feature
      payment_methods.feature
    account/
      registration.feature
      password_reset.feature
      profile_management.feature
    catalog/
      search.feature
      filtering.feature
      recommendations.feature
  support/                        # shared infrastructure
    authentication.feature
    error_handling.feature
  smoke/                          # critical-path smoke tests
    critical_checkout.feature
    critical_login.feature

CONVENTIONS:
  - One feature per .feature file
  - File name = kebab-case of feature name
  - Feature description includes As a/I want/So that
  - Max 10 scenarios per file (split if more)
  - Background only for truly shared preconditions
  - No nested folders > 3 levels deep
```

### Tagging Strategy
```
TAG TAXONOMY
=============

SCOPE TAGS (what to run):
  @smoke        # critical path, run on every commit
  @regression   # full suite, run nightly
  @wip          # work in progress, skip in CI
  @manual       # cannot automate, manual test only

DOMAIN TAGS (feature area):
  @checkout
  @account
  @catalog
  @payments
  @admin

ENVIRONMENT TAGS (where to run):
  @staging
  @production-safe   # safe to run in prod (read-only)
  @requires-auth     # needs authenticated session
  @requires-external # depends on third-party service

PRIORITY TAGS:
  @critical    # P0, blocks release
  @high        # P1, must pass for release
  @medium      # P2, should pass
  @low         # P3, nice to have

USAGE IN CI/CD:
  # Run smoke tests on every PR
  pytest --tags="@smoke"

  # Run regression excluding external dependencies
  pytest --tags="@regression and not @requires-external"

  # Run only checkout tests in staging
  pytest --tags="@checkout and @staging"

NAMING CONVENTION:
  - Tags are lowercase, kebab-case for multi-word
  - Place tags above Feature or Scenario keyword
  - Feature-level tags apply to all scenarios in file
  - Scenario-level tags override feature-level
```

### SpecFlow vs Cucumber Comparison
```
FRAMEWORK COMPARISON
=====================

                    SpecFlow (.NET)          Cucumber (Ruby/JS/Java)
Language:           C# / .NET               Ruby, JS, Java, Python
Gherkin version:    Gherkin 5+               Gherkin 5+
IDE support:        Visual Studio, Rider     VS Code, IntelliJ
Living docs:        SpecFlow+ LivingDoc      Cucumber Reports / HTML
Step definitions:   [Given/When/Then]        Given/When/Then (decorators)
Tag filtering:      --filter @tag            --tags @tag
Parallel exec:      xUnit/NUnit parallel     Cucumber parallel (JS)
Plugin ecosystem:   SpecFlow+ (paid)         Open-source ecosystem
Community:          .NET-focused             Polyglot, larger community
Best for:           .NET shops               Polyglot / Ruby / JS shops

LIVING DOCUMENTATION TOOLS:
  SpecFlow+ LivingDoc (SpecFlow):
    - Generates HTML from .feature files + test results
    - Integrates with Azure DevOps pipelines
    - Shows pass/fail per scenario with screenshots
    - Searchable, filterable by tag
    - Command: livingdoc test-assembly <dll> -t <json>

  Cucumber Reports (Cucumber JS/Java):
    - JSON reporter generates machine-readable output
    - HTML formatter creates browsable report
    - Integrates with CI (Jenkins, GitHub Actions)
    - Command: cucumber --format html --out report.html

  Pickles (legacy, .NET):
    - Generates static HTML from feature files
    - No longer actively maintained
    - Use SpecFlow+ LivingDoc instead

DECISION MATRIX:
  If .NET stack → SpecFlow + SpecFlow+ LivingDoc
  If Ruby/JS/Python → Cucumber + Cucumber Reports
  If Java → Cucumber-JVM + Cucumber Reports
  If polyglot → Cucumber (works across languages)
```

### Specification by Example Anti-Patterns
1. **Specs written by one person** — collaborative or they're just tests, not specs
2. **Specs too detailed** — one scenario = one behavior, not end-to-end workflow
3. **Specs too abstract** — "system handles errors properly" is not a spec
4. **Specs not automated** — manual specs rot; automate or they become fiction
5. **Specs as documentation afterthought** — write specs FIRST, code second
6. **Specs not maintained** — red specs must be triaged: bug or requirement change?
7. **Ignoring gaps** — example mapping should surface "we don't know X" as questions
8. **Coupling specs to implementation** — specs should survive code refactoring

## Step 21: Lean UX

Source: Jeff Gothelf & Josh Seiden, "Lean UX" (2nd ed., 2016)

Hypothesis-driven design. Replace deliverable-based requirements with testable assumptions.

### Hypothesis Format
```
LEAN UX HYPOTHESIS
==================

WE BELIEVE THAT [building this feature / making this change]
FOR [these users/personas]
WILL RESULT IN [this outcome / behavior change]
WE WILL KNOW WE ARE RIGHT WHEN [this measurable signal]

EXAMPLE:
  We believe that adding social proof (purchase notifications)
  for new visitors
  will result in a 15% increase in first-purchase conversion
  we will know we are right when conversion rate >= 15% (currently 0%)
  AND average order value does not decrease

ASSUMPTIONS BREAKDOWN:
  Desirability: Do users want this?
  Feasibility: Can we build this?
  Viability: Should we build this (business value)?
  Usability: Can users figure this out?
```

### Think → Make → Check Loop
```
LEAN UX CYCLE
=============

THINK (Hypothesize)
  ├── Identify assumptions (what do we believe?)
  ├── Rank by risk (what could kill us if wrong?)
  ├── Convert to hypotheses (testable statements)
  └── Define success metrics (how will we know?)
      │
      ▼
MAKE (Build Experiment)
  ├── Choose MVP type (see below)
  ├── Build minimum to test hypothesis
  ├── Define experiment parameters (sample size, duration)
  └── Set up measurement (analytics, heatmaps, surveys)
      │
      ▼
CHECK (Learn)
  ├── Run experiment
  ├── Measure results against success criteria
  ├── Decide: pivot, persevere, or kill
  └── Document learning, update assumptions map
      │
      ▼
  (Back to THINK with updated knowledge)
```

### Assumptions Mapping
```
ASSUMPTION MAP (2x2 Matrix)
============================

          HIGH RISK
              │
  ┌───────────┼───────────┐
  │  RISKY    │   MOST    │
  │  (test    │   RISKY   │
  │  first)   │  (test    │
  │           │  ASAP)    │
  ├───────────┼───────────┤
  │  SAFE     │  RISKY    │
  │  (assume  │  (test    │
  │  for now) │  if time) │
  └───────────┼───────────┘
              │
         LOW RISK
    LOW CERTAINTY    HIGH CERTAINTY

PROCESS:
  1. List all assumptions (brainstorm with team)
  2. Rate each: risk (impact if wrong) x certainty (how sure are we?)
  3. Plot on 2x2 matrix
  4. Test top-right quadrant first (high risk, low certainty)
  5. Bottom-left quadrant: assume true until evidence suggests otherwise
```

### MVP Types for Experiments
```
MVP TYPES (ordered by effort)
==============================

1. LANDING PAGE MVP
   - Single page describing the feature
   - Measure: sign-up rate, click-through
   - Effort: 1-2 days
   - Tests: desirability

2. WIZARD OF OZ MVP
   - Looks automated, actually manual behind the scenes
   - Measure: usage patterns, willingness to pay
   - Effort: 1 week
   - Tests: usability + desirability

3. CONCIERGE MVP
   - Manual service replacing automated feature
   - Measure: value delivered, user satisfaction
   - Effort: 1-2 weeks
   - Tests: viability + desirability

4. PIECE OF FAKEWORK (Fake Door)
   - Button/link that doesn't exist yet, measure clicks
   - Measure: click-through rate on non-existent feature
   - Effort: hours
   - Tests: desirability (demand signal)

5. SINGLE-FEATURE MVP
   - Minimal working feature
   - Measure: adoption, retention, satisfaction
   - Effort: 2-4 weeks
   - Tests: all four (desirability, feasibility, usability, viability)

6. EMAIL MVP
   - Manual email workflow simulating automated process
   - Measure: response rate, conversion
   - Effort: days
   - Tests: desirability + viability

CHOOSING:
  If testing demand → Landing Page or Fake Door
  If testing usability → Wizard of Oz or Concierge
  If testing viability → Concierge or Email
  If testing feasibility → Single-Feature MVP
```

### Lean UX Anti-Patterns
1. **Building before testing** — MVP first, full feature second
2. **No success criteria** — "we'll see how it goes" is not a hypothesis
3. **Testing everything** — use assumptions map to prioritize
4. **No experiment discipline** — define sample size and duration upfront
5. **Ignoring negative results** — learning what doesn't work IS progress
6. **Premature optimization** — don't optimize until hypothesis validated

## Step 22: Design Sprint

Source: Jake Knapp, "Sprint" (2016), https://designsprintkit.withgoogle.com/

5-day process to answer critical business questions through design, prototyping, and testing with users.

### 5-Day Process
```
DESIGN SPRINT SCHEDULE
======================

DAY 1: UNDERSTAND (Map the problem)
  Morning:
    - Lightning Talks (experts present context, 10-15 min each)
    - How Might We notes (on every insight)
    - Map: user journey on whiteboard (start → end)
  Afternoon:
    - Target selection: which part of map to sprint on?
    - Define sprint questions: "What do we need to learn?"
    - Long-term goal: "6 months from now, what does success look like?"
  Output: Sprint map, target, sprint questions, long-term goal

DAY 2: DIVERSE (Sketch competing solutions)
  Morning:
    - Lightning Demos (3-min demos of existing solutions, 10-15 min research)
    - Note-taking: "What's interesting? Steal-worthy?"
  Afternoon:
    - Four-Step Sketch (individual, silent):
      1. Notes (20 min): gather key information
      2. Ideas (20 min): rough, rough, rough ideas
      3. Crazy 8s (8 min): 8 variations of best idea, 1 min each
      4. Solution Sketch (30-90 min): detailed 3-panel storyboard
    - All sketches anonymous (quality ideas, not politics)
  Output: Solution sketches (one per participant)

DAY 3: DECIDE (Choose the best solution)
  Morning:
    - Art Museum: post all solution sketches on wall
    - Heat Map: everyone places dot stickers on interesting parts
    - Speed Critique (3 min per sketch):
      1. Narrator describes sketch
      2. Team calls out standout ideas
      3. Creator explains missed points
      4. Scribe captures key ideas
    - Straw Poll: each person votes for one solution
    - Super Vote: Decider picks final direction
  Afternoon:
    - Storyboard: 10-15 frames showing user's journey through solution
    - Detail: what user sees, does, and experiences
  Output: Storyboard (ready for prototyping)

DAY 4: PROTOTYPE (Build just enough to test)
  Morning:
    - Assign roles: Makers (build), Stitcher (assembles), Writer (copy), Asset Collector (images/data)
    - Choose tools: Figma, Keynote, HTML/CSS, slides, paper
  All Day:
    - Build prototype (ONE DAY, not a real product)
    - Trick: look real, not functional
    - Stitcher assembles pieces into coherent experience
  Afternoon:
    - Trial Run: team walks through prototype
    - Fix obvious issues, note edge cases
  Output: Clickable/interactive prototype

DAY 5: VALIDATE (Test with real users)
  Morning:
    - 5 user interviews (60 min each, back-to-back)
    - Interview script:
      1. Friendly conversation (5 min)
      2. Context questions (10 min)
      3. Introduction to prototype (5 min)
      4. Detailed tasks (25 min)
      5. Quick questions (5 min)
    - Team observes via live video stream
  Afternoon:
    - Notes review: what patterns emerged?
    - Categorize: positive, negative, neutral per user
    - Identify patterns (3/5 users did X → pattern)
    - Decide: persevere, pivot, or kill
  Output: User feedback, decision (go/no-go/pivot)
```

### Crazy 8s (Detailed)
```
CRAZY 8S FORMAT
===============

Setup:
  - Fold paper into 8 panels
  - Set timer: 1 minute per panel (8 minutes total)
  - Start with your best idea from Notes/Ideas phase

Rules:
  1. One minute per panel — no exceptions
  2. Push for variation: try the opposite, try a different UI, try a different user flow
  3. Ugly is fine — speed matters, not polish
  4. If stuck: sketch the same idea from different user's perspective
  5. Quantity over quality — force divergent thinking

Common mistakes:
  - Spending too long on first panel
  - Drawing same solution 8 times (not variation)
  - Policking instead of sketching
  - Judging ideas during sketching

After Crazy 8s:
  - Review all 8 panels
  - Combine best elements into Solution Sketch (3-panel storyboard)
```

### Design Sprint Rules
1. **No devices during sprint activities** — laptops/phones closed
2. **Timebox everything** — strict timekeeper role
3. **One conversation at a time** — facilitator enforces
4. **Everyone sketches** — no spectators, even CEO
5. **Anonymous sketches** — ideas compete on merit, not politics
6. **Decider decides** — one person has final vote (usually product lead or exec)
7. **Test with 5 users** — enough to find patterns, not statistical significance
8. **Prototype in one day** — if it takes longer, scope down

### Design Sprint Anti-Patterns
1. **Skipping Day 1** — understanding problem wastes Day 3-4 building wrong thing
2. **Consensus voting** — Super Vote, not committee consensus
3. **Building real product** — prototype is for learning, not shipping
4. **Testing with team members** — real users only
5. **No follow-up** — sprint result needs next steps (MVP, further sprints, kill)

## Step 23: Dual-Track Agile

Source: Marty Cagan, "Inspired" (2nd ed., 2017); Jeff Patton, "Dual Track Development"

Discovery track validates what to build. Delivery track builds validated items. Both run in parallel.

### Discovery vs Delivery Tracks
```
DUAL-TRACK WORKFLOW
===================

DISCOVERY TRACK (1-2 sprints ahead)
  Purpose: Validate ideas BEFORE committing to build
  Activities:
    - User interviews
    - Prototype testing
    - Assumption validation
    - Feasibility spikes
    - Opportunity assessment
  Output: Validated backlog items (ready for delivery)

DELIVERY TRACK (builds validated items)
  Purpose: Build, test, ship validated solutions
  Activities:
    - Sprint planning (from validated backlog)
    - Development
    - Testing (ATDD/BDD)
    - Deployment
  Output: Shipped features

FLOW:
  Discovery: [Idea] → [Prototype] → [Test] → [Validated] → to Delivery backlog
  Delivery:  [Sprint Planning] → [Build] → [Test] → [Ship]

  DISCOVERY                          DELIVERY
  ┌─────────────────────┐           ┌─────────────────────┐
  │ Interview users     │           │ Sprint 10           │
  │ Prototype solution A│           │ Build feature X     │
  │ Test with 5 users   │           │ (validated in Sprint│
  │ → VALIDATED         │──────────▶│  8 discovery)       │
  │                     │           │                     │
  │ Prototype solution B│           │ Sprint 11           │
  │ Test with 5 users   │           │ Build feature Y     │
  │ → INVALIDATED (kill)│           │ (validated in Sprint│
  │                     │           │  9 discovery)       │
  └─────────────────────┘           └─────────────────────┘
```

### Validation Methods (4 Lenses)
```
VALIDATION MATRIX
=================

1. VALUE VALIDATION (Do users want this?)
   Methods:
   - User interviews (qualitative)
   - Fake door MVP (demand signal)
   - Landing page conversion rate
   - Prototype testing (preference, willingness)
   Signal: Users try to use it, ask for it, or pay for it

2. USABILITY VALIDATION (Can users use this?)
   Methods:
   - Usability testing (task completion rate)
   - A/B test UI variations
   - Heatmap analysis
   - Cognitive walkthrough
   Signal: Users complete tasks without help, low error rate

3. FEASIBILITY VALIDATION (Can we build this?)
   Methods:
   - Technical spikes (timeboxed proof-of-concept)
   - API/dependency analysis
   - Performance modeling
   - Architecture review
   Signal: Prototype works within constraints (time, cost, tech)

4. VIABILITY VALIDATION (Should we build this for business?)
   Methods:
   - Business model canvas review
   - Legal/compliance check
   - Cost-benefit analysis
   - Stakeholder alignment
   Signal: Revenue/cost model works, no blockers from legal/compliance

ALL FOUR MUST PASS:
  Value ✓ + Usability ✓ + Feasibility ✓ + Viability ✓ = Ready for Delivery
  Any fail → iterate in Discovery or kill idea
```

### Dual-Track Anti-Patterns
1. **Discovery as research-only** — must produce validated backlog items, not just reports
2. **No handoff** — discovery findings must reach delivery team, not sit in wiki
3. **Same team, no split** — some overlap OK, but dedicated discovery members help
4. **Discovery sprint = delivery sprint** — discovery should be 1-2 sprints ahead
5. **Skipping validation** — "we know what users want" is assumption, not validation
6. **Testing only value** — all four lenses required (value, usability, feasibility, viability)

## Step 24: OKR Alignment

Source: John Doerr, "Measure What Matters" (2018); Christina Wodtke, "Radical Focus" (2016)

Connect requirements to measurable business outcomes via Objectives and Key Results.

### OKR Structure
```
OKR ANATOMY
===========

OBJECTIVE (What do we want to achieve?)
  - Qualitative, inspiring, time-bound
  - Answers: "Where do we want to go?"
  - Example: "Become the fastest checkout in e-commerce"
  - NOT a feature, NOT a metric

KEY RESULTS (How do we measure progress?)
  - Quantitative, measurable, outcome-based
  - Answers: "How do we know we got there?"
  - 2-5 KRs per Objective
  - Each KR = metric + target + timeframe
  - Example: "Reduce checkout time from 45s to 15s by Q3"

INITIATIVES (What will we do to achieve KRs?)
  - Projects, features, experiments
  - Answers: "What will we try?"
  - Multiple initiatives per KR
  - Example: "One-click checkout feature", "Auto-fill address"
```

### Engineering Examples
```
OKR EXAMPLES FOR ENGINEERING
=============================

OBJECTIVE 1: Achieve world-class platform reliability
  KR1: Uptime from 99.5% to 99.95% (measured monthly)
  KR2: P95 latency from 800ms to 200ms for core APIs
  KR3: Mean time to recovery (MTTR) from 4h to 30min
  Initiatives:
    - Implement circuit breakers on all external calls
    - Add distributed tracing (Jaeger/Zipkin)
    - Build automated rollback on deployment failure

OBJECTIVE 2: Eliminate security as a blocker for enterprise sales
  KR1: Complete SOC 2 Type II audit by Q2
  KR2: Reduce critical vulnerabilities from 12 to 0
  KR3: Achieve 100% OWASP ASVS L2 compliance for auth module
  Initiatives:
    - Implement SAST/DAST in CI/CD pipeline
    - Migrate to OAuth 2.0 + PKCE for all auth flows
    - Encrypt PII at rest with customer-managed keys

OBJECTIVE 3: Make data-driven product decisions
  KR1: 100% of features ship with analytics instrumentation
  KR2: A/B test coverage from 5% to 40% of user-facing features
  KR3: Time to insight from 2 weeks to 2 hours (self-serve analytics)
  Initiatives:
    - Build event tracking SDK (auto-capture user actions)
    - Implement feature flag framework (LaunchDarkly/Unleash)
    - Create self-serve analytics dashboard (Metabase/Superset)
```

### Alignment Cascade
```
OKR ALIGNMENT CASCADE
=====================

COMPANY OKR
  Objective: "Become market leader in [category]"
  KR1: Revenue from $10M to $50M
  KR2: Active users from 100K to 500K
      │
      ▼
PRODUCT OKR
  Objective: "Deliver best-in-class onboarding experience"
  KR1: Trial-to-paid conversion from 12% to 30%
  KR2: Time to value from 7 days to 1 day
      │
      ▼
ENGINEERING OKR
  Objective: "Eliminate onboarding friction"
  KR1: Onboarding completion rate from 45% to 85%
  KR2: Setup time from 30min to 5min
      │
      ▼
TEAM OKR
  Objective: "One-click project setup"
  KR1: First project created in < 2 minutes
  KR2: Zero configuration errors in setup flow

CASCADE RULES:
  - Each level's KRs contribute to parent's KR
  - Alignment is bottom-up AND top-down (not just top-down mandate)
  - Teams set their own initiatives to achieve KRs
  - No more than 3-5 objectives per level
```

### Common OKR Mistakes
```
OKR ANTI-PATTERNS
=================

1. OBJECTIVE AS TASK LIST
   Wrong: "Implement feature X, Y, Z"
   Right: "Become the preferred choice for [user segment]"
   Fix: Objectives are outcomes, not outputs

2. KEY RESULT AS TASK
   Wrong: "Launch new pricing page"
   Right: "Increase conversion rate from 2% to 5%"
   Fix: KRs measure outcomes, not completion

3. TOO MANY OKRs
   Wrong: 7 objectives, 35 key results
   Right: 3 objectives, 9-12 key results
   Fix: Focus; if everything is priority, nothing is

4. SET-AND-FORGET
   Wrong: Set in Q1, review in Q4
   Right: Weekly check-ins, monthly reviews, quarterly resets
   Fix: OKRs need active management

5. SAND-BAGGING
   Wrong: Set easy targets to guarantee 100% completion
   Right: Set ambitious targets; 70% completion = success
   Fix: OKRs should be stretch goals, not forecasts

6. CONFUSING OUTPUT WITH OUTCOME
   Wrong: "Ship 10 features" (output)
   Right: "Increase user retention from 40% to 60%" (outcome)
   Fix: Measure impact, not activity

7. NO FEEDBACK LOOP
   Wrong: OKRs don't influence backlog prioritization
   Right: Every sprint goal connects to at least one KR
   Fix: OKRs drive prioritization decisions
```

### KR vs Tasks: Clear Distinction
```
KEY RESULTS vs TASKS
====================

KEY RESULT:
  - Measures OUTCOME (what changed in the world)
  - Specific metric + target + timeframe
  - Answer: "Did we achieve the outcome?"
  - Example: "Reduce checkout abandonment from 68% to 45%"
  - Proof: dashboard showing metric change

TASK:
  - Describes OUTPUT (what we built/did)
  - Specific action or deliverable
  - Answer: "Did we do the work?"
  - Example: "Implement guest checkout feature"
  - Proof: feature shipped, PR merged

RELATIONSHIP:
  Tasks are INITIATIVES toward KRs
  One KR may require many tasks
  Tasks without KRs = busywork
  KRs without tasks = wishful thinking

EXAMPLE MAPPING:
  KR: "Reduce checkout abandonment from 68% to 45%"
  ├── Task 1: Implement guest checkout
  ├── Task 2: Add auto-fill address
  ├── Task 3: Show total before payment step
  ├── Task 4: Remove unnecessary form fields
  └── Task 5: Add progress indicator

  If all tasks done but abandonment still 60%:
    → Tasks complete, KR not achieved
    → Need different approach (re-think, re-discover)
```

### OKR Integration with Requirements Engineering
```
OKR → REQUIREMENTS FLOW
========================

OKR Objective
    │
    ▼
Key Results (measurable outcomes)
    │
    ├── Impact Map (KR = Goal in impact map)
    │       │
    │       ▼
    │   Actors → Impacts → Deliverables
    │
    ├── User Stories (each deliverable = stories)
    │       │
    │       ▼
    │   Acceptance Criteria (Given/When/Then)
    │
    └── Traceability Matrix
            │
            ▼
        Each story traces: Story → KR → Objective
```

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

FAIRNESS REQUIREMENTS (deeper specification):
  [ ] Demographic parity: P(Ŷ=1|A=a) = P(Ŷ=1|A=b) for all protected groups A
    - Positive prediction rate equal across groups
    - Threshold: |P(Ŷ=1|A=a) - P(Ŷ=1|A=b)| <= 0.05
  [ ] Equalized odds: P(Ŷ=1|Y=y, A=a) = P(Ŷ=1|Y=y, A=b)
    - True positive and false positive rates equal across groups
    - Prevents systematic disadvantage for any group
  [ ] Predictive parity: P(Y=1|Ŷ=1, A=a) = P(Y=1|Ŷ=1, A=b)
    - Precision equal across groups
  [ ] Individual fairness: similar individuals get similar predictions
    - Metric: Lipschitz constraint on model function
  [ ] Intersectional fairness: test on combinations (e.g., young Black women, elderly disabled men)
    - Subgroup size >= 30 for statistical validity
    - Report worst-case subgroup metric

MODEL CARDS (Mitchell et al., 2019):
  Source: https://arxiv.org/abs/1810.03993

  MODEL CARD TEMPLATE:
    Model Details:
      - Name, version, type (classification, regression, generation)
      - Owner, license, contact
      - Date released, last updated
    Intended Use:
      - Primary use case
      - Primary users (who should use this?)
      - Out-of-scope uses (what this model should NOT be used for)
    Factors:
      - Relevant factors (age, gender, geography)
      - Evaluation factors (subgroups tested)
    Metrics:
      - Primary metric (e.g., F1, AUC-ROC)
      - Disaggregated metrics by subgroup
      - Confidence intervals
    Evaluation Data:
      - Datasets used for evaluation
      - Preprocessing applied
    Training Data:
      - Datasets used for training
      - Preprocessing, augmentation
    Quantitative Analyses:
      - Performance tables by subgroup
      - Fairness metrics (demographic parity, equalized odds)
    Ethical Considerations:
      - Potential harms (false positives/negatives impact)
      - Mitigation strategies
    Caveats and Recommendations:
      - Known limitations
      - Recommended monitoring

DATASHEETS FOR DATASETS (Gebru et al., 2021):
  Source: https://arxiv.org/abs/1803.09010

  DATASHEET TEMPLATE:
    Motivation:
      - Why was this dataset created?
      - Who created it? Who funded it?
    Composition:
      - What does each instance represent?
      - What labels/annotations exist?
      - Is there missing data? How much?
      - Does the dataset contain sensitive data (PII)?
    Collection Process:
      - How was data collected? (crawling, survey, sensors, human annotation)
      - What populations are represented?
      - What populations are underrepresented or excluded?
      - Collection time period
    Uses:
      - Has the dataset been used before? Where?
      - What tasks is it suitable for?
      - What tasks is it NOT suitable for?
    Distribution:
      - How is the dataset distributed?
      - License terms
    Maintenance:
      - Who maintains the dataset?
      - Update frequency

EU AI ACT REQUIREMENTS (Regulation 2024/1689):
  Source: https://eur-lex.europa.eu/eli/reg/2024/1689

  HIGH-RISK AI SYSTEMS (Annex III):
    - Biometric identification
    - Critical infrastructure management
    - Education and vocational training
    - Employment, worker management
    - Access to essential services (credit, insurance, social benefits)
    - Law enforcement
    - Migration, asylum, border control
    - Administration of justice

  MANDATORY REQUIREMENTS FOR HIGH-RISK AI (Article 9):
    [ ] Risk management system (continuous, documented)
    [ ] Data governance (training data quality, relevance, representativeness)
    [ ] Technical documentation (architecture, training process, evaluation)
    [ ] Record-keeping (automatic logs for traceability)
    [ ] Transparency (clear information to deployers)
    [ ] Human oversight (ability to intervene, override)
    [ ] Accuracy, robustness, cybersecurity (tested against adversarial attacks)
    [ ] Conformity assessment before market placement

  TRANSPARENCY REQUIREMENTS FOR ALL AI (Article 50):
    [ ] Users informed they are interacting with AI (chatbots, deepfakes)
    [ ] AI-generated content labeled as such
    [ ] Deepfake content marked as artificially generated
    [ ] Emotion recognition systems: inform subjects

  PENALTIES:
    - Up to EUR 35M or 7% global turnover (prohibited AI practices)
    - Up to EUR 15M or 3% global turnover (other violations)
    - Up to EUR 7.5M or 1% global turnover (incorrect info to authorities)

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

### Requirements Traceability (Forward, Backward, Impact Analysis)

Full traceability ensures every requirement links to design, code, and tests. Enables impact analysis when requirements change.

```
FORWARD TRACEABILITY (requirement → downstream)
=================================================

  Requirement (ID: REQ-101)
    "User can reset password via email link"
    │
    ├── Design (ID: DES-101)
    │     "Password reset flow: email → token → new password form"
    │
    ├── Code (commit: abc123, PR #245)
    │     src/auth/password_reset.py
    │     src/templates/reset_email.html
    │
    └── Test (ID: TC-101, TC-102, TC-103)
          TC-101: Valid token resets password
          TC-102: Expired token shows error
          TC-103: Invalid token shows error

BACKWARD TRACEABILITY (test → upstream requirement)
=====================================================

  Test TC-101: "Valid token resets password"
    │
    ├── Code: src/auth/password_reset.py:reset_password()
    │
    ├── Design: DES-101 "Password reset flow"
    │
    └── Requirement: REQ-101 "User can reset password via email"

WHY BOTH DIRECTIONS:
  Forward: "Is every requirement implemented and tested?" (coverage)
  Backward: "Why does this test exist?" (orphan detection)
  Missing forward link = untested requirement
  Missing backward link = orphan test (delete or justify)

IMPACT ANALYSIS
================

When REQ-101 changes ("add SMS option for password reset"):

  REQ-101 (changed)
    ├── DES-101: MUST UPDATE (add SMS flow diagram)
    ├── DES-102: AFFECTED (SMS gateway integration)
    ├── Code:
    │     src/auth/password_reset.py: MODIFY (add SMS path)
    │     src/auth/sms_service.py: CREATE (new)
    │     src/templates/reset_sms.html: CREATE (new)
    ├── Tests:
    │     TC-101: NO CHANGE (email path still works)
    │     TC-104: CREATE (SMS token delivery)
    │     TC-105: CREATE (SMS rate limiting)
    └── Dependencies:
          SMS Gateway API: NEW DEPENDENCY
          Rate limiting service: UPDATE

IMPACT ANALYSIS PROCESS:
  1. Identify changed requirement
  2. Follow forward links to find affected design/code/tests
  3. Follow backward links to find related requirements
  4. Estimate effort for each affected item
  5. Present to team: "Changing REQ-101 affects 3 files, 2 tests, 1 new dependency"
```

### Traceability Matrix
```
REQUIREMENTS TRACEABILITY MATRIX (RTM)
========================================

Req ID  | Requirement              | Design  | Code                | Test Cases      | Status
--------|--------------------------|---------|---------------------|-----------------|--------
REQ-101 | Password reset via email | DES-101 | password_reset.py   | TC-101,102,103  | Verified
REQ-102 | Two-factor authentication| DES-102 | tfa_service.py      | TC-201,202,203  | Verified
REQ-103 | Session timeout 30min    | DES-103 | session_manager.py  | TC-301          | In Test
REQ-104 | OAuth2 SSO login         | DES-104 | (not started)       | (not written)   | Designed
REQ-105 | Account lockout 5 fails  | DES-105 | lockout.py          | TC-501,502      | Failed
REQ-106 | Password complexity rules| (none)  | (not started)       | (not written)   | New

COLUMNS:
  Req ID:       Unique requirement identifier
  Requirement:  Short description
  Design:       Design document/section ID
  Code:         Source file(s) implementing requirement
  Test Cases:   Test case IDs covering requirement
  Status:       New | Designed | In Dev | In Test | Verified | Failed

COVERAGE METRICS (from RTM):
  Requirements with code:  4/6 = 67%
  Requirements with tests: 3/6 = 50%
  Requirements verified:   2/6 = 33%
  Orphan tests: 0
  Orphan code: 0

MAINTENANCE:
  - Update RTM on every requirement change
  - Review RTM in sprint retrospectives
  - Auto-generate from tooling where possible
  - Flag "Designed" items stuck > 2 sprints
```

### Traceability Tools
```
TOOL COMPARISON
================

IBM DOORS (Enterprise):
  - Industry standard for regulated industries (aerospace, defense, automotive)
  - Formal requirements management with full traceability
  - Impact analysis built-in (what-if scenarios)
  - Baseline management (versioned requirement snapshots)
  - Heavy, expensive, steep learning curve
  - Best for: ISO 26262, DO-178C, IEC 62304 compliance

Jama Connect (Mid-Market):
  - Modern web-based requirements management
  - Live traceability (real-time impact analysis)
  - Review workflows with electronic signatures
  - Test management integration
  - REST API for CI/CD integration
  - Best for: Medical devices, automotive, complex systems

Azure DevOps (Lightweight):
  - Work items as requirements (Product Backlog Items / User Stories)
  - Links between work items create traceability
  - Query-based traceability views
  - Test Plans link test cases to requirements
  - Extensions: "Requirements Hub", "Traceability Matrix"
  - Best for: Agile teams already on Azure DevOps
  - Limitation: No formal impact analysis, manual link maintenance

OPEN-SOURCE ALTERNATIVES:
  - OSRMT: Open-source requirements management tool
  - ReqView: Requirements management with traceability (free tier)
  - GitHub Issues + Labels: Lightweight traceability for small teams
  - Confluence + Jira Links: Manual but flexible
```

### Connection Diagram

```
Design Thinking (empathize + define)
    │
    ▼
Lean UX (hypothesize, test assumptions)
    │
    ▼
JTBD (discover needs)
    │
    ▼
Design Sprint (Understand/Diverge/Decide/Prototype/Validate)
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
Strategic DDD (bounded contexts, context mapping, ubiquitous language)
    │
    ▼
Tactical DDD (entities, value objects, aggregates, domain events, repositories)
    │
    ▼
Event Storming Workshop (6-phase: chaotic → aggregates, process + design level)
    │
    ▼
Domain Storytelling Workshop (notation, expert/modeler/audience, discover BCs)
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
Dual-Track Agile (Discovery validates → Delivery builds)
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
    │
    ▼
OKR Feedback Loop (KR results → next cycle objectives)
```

## Step 26: Strategic Domain-Driven Design (DDD)

Source: Eric Evans, "Domain-Driven Design" (2003); Vaughn Vernon, "Implementing Domain-Driven Design" (2013)

Strategic DDD focuses on discovering and defining bounded contexts, mapping their relationships, and establishing ubiquitous language. Used when domain complexity is high and teams need clear ownership boundaries.

### Bounded Contexts

```
BOUNDED CONTEXT DEFINITION
==========================

A bounded context is an explicit boundary within which a domain model exists.
Each bounded context has its own:
  - Ubiquitous language (terms mean specific things here)
  - Model (entities, value objects, aggregates)
  - Team ownership
  - Database/schema (ideally)

IDENTIFYING BOUNDED CONTEXTS:
  1. Business Capability Boundaries
     - Each capability = potential bounded context
     - Example: "Ordering", "Shipping", "Billing", "Inventory"
     - Aligns with business departments or value streams

  2. Same-Term-Different-Meaning Heuristic
     - If same word means different things in different contexts → separate BCs
     - "Customer" in Sales = lead/prospect
     - "Customer" in Billing = account with payment method
     - "Customer" in Shipping = delivery address recipient
     - Each is a different model in a different BC

  3. Language Boundaries
     - Where ubiquitous language changes → bounded context boundary
     - Walk through conversations: when does vocabulary shift?

  4. Organizational Boundaries
     - Conway's Law: system structure mirrors org structure
     - Team ownership = natural BC boundary
     - But don't force it — org changes, domains persist
```

```
BOUNDED CONTEXT EXAMPLE: E-Commerce
====================================

┌─────────────────────────────────────────────────────────┐
│                    E-COMMERCE SYSTEM                     │
│                                                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │   CATALOG    │  │   ORDERING   │  │   SHIPPING   │  │
│  │              │  │              │  │              │  │
│  │ Product      │  │ Order        │  │ Shipment     │  │
│  │ Category     │  │ LineItem     │  │ Package      │  │
│  │ Price (list) │  │ Cart         │  │ Tracking     │  │
│  │ SKU          │  │ Price (sale) │  │ Carrier      │  │
│  │ Description  │  │ Discount     │  │ DeliveryDate │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
│                                                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │   BILLING    │  │  INVENTORY   │  │    IDENTITY  │  │
│  │              │  │              │  │              │  │
│  │ Invoice      │  │ StockLevel   │  │ User         │  │
│  │ Payment      │  │ Warehouse    │  │ Role         │  │
│  │ Account      │  │ Reservation  │  │ Permission   │  │
│  │ Receipt      │  │ ReorderPoint │  │ Credential   │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
└─────────────────────────────────────────────────────────┘

NOTE: "Price" exists in Catalog (list price) and Ordering (sale price/discount)
      Same word, different meanings → confirms separate BCs
```

### Context Mapping

```
CONTEXT MAPPING PATTERNS
========================

CONTEXT MAP = visual showing all BCs + relationship patterns between them.

RELATIONSHIP PATTERNS:

1. PARTNERSHIP
   Two teams/BCs with mutual dependency, joint planning.
   - Teams succeed or fail together
   - Synchronized releases, shared planning
   - Use when: tight integration needed, willing to coordinate
   - Example: Ordering ↔ Inventory (order must check stock)

2. CUSTOMER-SUPPLIER
   Upstream (supplier) provides, downstream (customer) consumes.
   - Customer can request features but supplier decides priority
   - Upstream has more power in relationship
   - Use when: one team serves another
   - Example: Catalog (supplier) → Ordering (customer)

3. CONFORMIST
   Downstream conforms to upstream's model with no influence.
   - No negotiation — take it or leave it
   - Use when: external system, third-party API
   - Example: Payment Gateway (upstream) → Billing (conformist)

4. ANTICORRUPTION LAYER (ACL)
   Downstream builds a translation layer to protect its model.
   - Adapter translates upstream model into downstream model
   - Isolates downstream BC from upstream changes
   - Use when: integrating legacy or external systems
   - Example: Legacy ERP ──[ACL]──> Modern Inventory BC

5. OPEN HOST SERVICE + PUBLISHED LANGUAGE
   Upstream exposes protocol/API with well-defined language.
   - Open Host Service: standardized API (REST, gRPC, events)
   - Published Language: shared schema (Avro, Protobuf, OpenAPI)
   - Use when: many consumers need access
   - Example: Catalog API with OpenAPI spec + product schema

6. SHARED KERNEL
   Two BCs share a subset of the model.
   - Shared code/schema must be agreed upon by both teams
   - Changes require consent from both sides
   - Use when: overlap is small and stable
   - Example: Shared Address value object used by Billing + Shipping
   - DANGER: can become a ball-and-chain; use sparingly

7. SEPARATE WAYS
   No integration — each BC solves its own problem independently.
   - No shared model, no API, no events
   - Use when: integration cost > benefit
   - Example: Internal HR system ≠ Customer-facing app
```

```
CONTEXT MAP DIAGRAM: E-Commerce
================================

                    ┌─────────┐
         partnership│ CATALOG │
        ┌───────────│         │
        │           └────┬────┘
        │                │ customer-supplier
        │                ▼
   ┌────┴─────┐    ┌──────────┐    ┌──────────┐
   │ INVENTORY│◄───│ ORDERING │───►│ SHIPPING │
   │          │    │          │    │          │
   └──────────┘    └─────┬────┘    └──────────┘
                   partnership      │
                        │           │ customer-supplier
                        ▼           ▼
                  ┌──────────┐ ┌──────────┐
                  │ BILLING  │ │  COURIER │
                  │          │ │ (conform)│
                  └─────┬────┘ └──────────┘
                        │ ACL
                        ▼
                  ┌──────────┐
                  │ LEGACY   │
                  │ ERP      │
                  └──────────┘
```

### Ubiquitous Language

```
UBIQUITOUS LANGUAGE
===================

Shared vocabulary within a bounded context. Every term has ONE meaning.
Code, conversations, docs, and UI all use the same words.

PRINCIPLES:
  1. One term = one meaning within BC
     - "Order" in Ordering BC means: customer's purchase intent
     - Do NOT reuse "Order" to mean "internal restock request" in Inventory BC

  2. Code reflects language
     - Class/method names match domain terms
     - If domain says "place an order", code has Order.place()
     - If domain says "shipment dispatched", event is ShipmentDispatched

  3. Language evolves with understanding
     - When experts correct you, update the language AND the code
     - Glossary is living document, not one-time artifact

  4. Not everyone uses same language globally
     - Each BC has its own ubiquitous language
     - Same concept in different BCs gets different names

BUILDING THE GLOSSARY:
  ┌────────────────┬───────────────────────────────────────┐
  │ Term           │ Definition (within Ordering BC)       │
  ├────────────────┼───────────────────────────────────────┤
  │ Order          │ Customer's intent to purchase items   │
  │ Cart           │ Temporary collection before ordering  │
  │ LineItem       │ Single product + quantity in order    │
  │ Place Order    │ Convert cart into confirmed order     │
  │ Cancel Order   │ Void order before fulfillment starts  │
  │ Order Total    │ Sum of line item prices minus discount│
  └────────────────┴───────────────────────────────────────┘

ANTI-PATTERNS:
  - Using generic terms: "data", "object", "item", "record"
  - Using technical terms in domain discussions: "table", "row", "endpoint"
  - Same word, different meanings within one BC
  - Different words, same meaning within one BC (synonyms)
```

## Step 27: Tactical Domain-Driven Design (DDD)

Source: Eric Evans, "Domain-Driven Design" (2003); Vaughn Vernon, "Implementing Domain-Driven Design" (2013)

Tactical DDD provides building blocks for modeling within a bounded context. These are the code-level patterns that make the ubiquitous language executable.

### Entities

```
ENTITIES
========

Objects defined by their identity, not their attributes.
Two entities are equal if they have the same ID, even if all other fields differ.

CHARACTERISTICS:
  - Identity-based equality: order #1234 is always order #1234
  - Mutable: fields can change, identity persists
  - Lifecycle: created, modified, potentially archived
  - Has a unique identifier (UUID, auto-increment, natural key)

EXAMPLES:
  ┌─────────────────────────────────────────────┐
  │ ENTITY: Order                               │
  │                                             │
  │ Identity: orderId (UUID)                    │
  │ Attributes:                                 │
  │   - customerId: UUID                        │
  │   - status: Draft | Placed | Shipped | ...  │
  │   - placedAt: DateTime (nullable)           │
  │   - shippingAddress: Address (value object)  │
  │                                             │
  │ Equality: orderA == orderB iff same orderId │
  │                                             │
  │ Behavior:                                   │
  │   - place() → sets status, placedAt         │
  │   - cancel() → sets status to Cancelled     │
  │   - addItem(product, qty) → adds line item  │
  └─────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────┐
  │ ENTITY: Customer                            │
  │                                             │
  │ Identity: customerId (UUID)                 │
  │ Attributes:                                 │
  │   - name: String                            │
  │   - email: Email (value object)             │
  │   - tier: Bronze | Silver | Gold            │
  │                                             │
  │ Equality: same customerId = same customer   │
  │ Mutable: name, email, tier can change       │
  └─────────────────────────────────────────────┘

WHEN TO USE ENTITY vs VALUE OBJECT:
  - Does it have a lifecycle? → Entity
  - Would two with same attributes be identical? → Value Object
  - Do I need to track changes over time? → Entity
```

### Value Objects

```
VALUE OBJECTS
=============

Objects defined by their attributes, not identity.
Two value objects are equal if all their attributes are equal.
Immutable — to "change" a value object, create a new one.

CHARACTERISTICS:
  - Attribute-based equality: Money(10, "USD") == Money(10, "USD")
  - Immutable: no setters, operations return new instances
  - No identity: no ID field
  - Self-validating: constructor enforces invariants

EXAMPLES:
  ┌─────────────────────────────────────────────┐
  │ VALUE OBJECT: Money                         │
  │                                             │
  │ Attributes:                                 │
  │   - amount: Decimal (not float!)            │
  │   - currency: String (ISO 4217)             │
  │                                             │
  │ Equality: same amount + same currency       │
  │ Immutable:                                  │
  │   - add(other): returns new Money           │
  │     (asserts same currency)                 │
  │   - multiply(factor): returns new Money     │
  │                                             │
  │ Invariants:                                 │
  │   - amount >= 0 (or specify if negative OK) │
  │   - currency is 3-letter ISO code           │
  │   - amount precision matches currency       │
  │     (JPY=0 decimals, USD=2 decimals)        │
  └─────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────┐
  │ VALUE OBJECT: Address                       │
  │                                             │
  │ Attributes:                                 │
  │   - street: String                          │
  │   - city: String                            │
  │   - state: String                           │
  │   - postalCode: String                      │
  │   - country: String (ISO 3166)              │
  │                                             │
  │ Equality: all fields match                  │
  │ Immutable: change = new Address             │
  │                                             │
  │ Why value object?                           │
  │   - Two addresses with same fields = same   │
  │   - No lifecycle tracking needed            │
  │   - Can be shared/reused safely             │
  └─────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────┐
  │ VALUE OBJECT: DateRange                     │
  │                                             │
  │ Attributes:                                 │
  │   - start: LocalDate                        │
  │   - end: LocalDate                          │
  │                                             │
  │ Invariants:                                 │
  │   - end >= start                            │
  │                                             │
  │ Operations:                                 │
  │   - contains(date): boolean                 │
  │   - overlaps(other): boolean                │
  │   - duration(): Duration                    │
  │   - extend(days): new DateRange             │
  └─────────────────────────────────────────────┘

VALUE OBJECT BENEFITS:
  - Simpler (no identity management)
  - Safer (immutable, no side effects)
  - Reusable (can be shared across aggregates)
  - Testable (equality is deterministic)
```

### Aggregates

```
AGGREGATES
==========

A cluster of entities and value objects treated as a single unit for data changes.
One entity is the AGGREGATE ROOT — the only entry point from outside.

CHARACTERISTICS:
  - Transactional boundary: all changes within aggregate are atomic
  - Reference by ID only: other aggregates reference this one by root ID
  - Keep small: fewer entities = less contention, simpler transactions
  - Root enforces invariants: consistency rules enforced at root level

STRUCTURE:
  ┌─────────────────────────────────────────────────────┐
  │ AGGREGATE: Order (root: Order entity)               │
  │                                                     │
  │  ┌─────────────┐                                    │
  │  │ Order (ROOT)│ ← Only entry point from outside    │
  │  │             │                                    │
  │  │ orderId     │                                    │
  │  │ customerId  │ ← Reference by ID (not object ref) │
  │  │ status      │                                    │
  │  └──────┬──────┘                                    │
  │         │ contains                                  │
  │         ▼                                           │
  │  ┌──────────────┐  ┌──────────────┐                 │
  │  │  LineItem 1  │  │  LineItem 2  │                 │
  │  │              │  │              │                 │
  │  │ lineItemId   │  │ lineItemId   │                 │
  │  │ productId    │  │ productId    │ ← ID reference  │
  │  │ quantity     │  │ quantity     │                 │
  │  │ unitPrice:   │  │ unitPrice:   │                 │
  │  │   Money(VO)  │  │   Money(VO)  │                 │
  │  └──────────────┘  └──────────────┘                 │
  │                                                     │
  │  INVARIANTS (enforced by root):                     │
  │   - Order total = sum of line item subtotals        │
  │   - Cannot add items to shipped order               │
  │   - At least 1 line item to place order             │
  └─────────────────────────────────────────────────────┘

RULES:
  1. Reference other aggregates by ID only
     Order has customerId (not Customer object)
     LineItem has productId (not Product object)

  2. One transaction modifies one aggregate
     Cross-aggregate = eventual consistency via domain events

  3. Keep aggregates small
     If aggregate has > 5-7 entities, consider splitting
     Smaller = less lock contention, better performance

  4. Delete cascade within aggregate
     Deleting Order deletes its LineItems
     But NOT across aggregate boundaries
```

### Domain Events

```
DOMAIN EVENTS
=============

Something that happened in the domain that domain experts care about.
Named in past tense. Immutable facts. Used for cross-aggregate and cross-BC communication.

CHARACTERISTICS:
  - Past tense: OrderPlaced (not PlaceOrder — that's a command)
  - Immutable: events happened, cannot be unhappened
  - Contains: what happened, when, involved aggregate IDs
  - Cross-BC consistency: events bridge bounded contexts

STRUCTURE:
  ┌─────────────────────────────────────────────┐
  │ DOMAIN EVENT: OrderPlaced                   │
  │                                             │
  │ eventId: UUID (unique event identifier)     │
  │ occurredAt: DateTime (when it happened)     │
  │ orderId: UUID (which aggregate)             │
  │ customerId: UUID (who)                      │
  │ items: List<{productId, quantity, price}>   │
  │ totalAmount: Money                          │
  │                                             │
  │ Published to: message bus / event store     │
  │ Consumers:                                  │
  │   - Inventory BC: reserve stock             │
  │   - Billing BC: create invoice draft        │
  │   - Analytics: update sales metrics         │
  └─────────────────────────────────────────────┘

EVENT NAMING:
  Domain:    OrderPlaced, PaymentProcessed, ShipmentDispatched
  Technical: OrderCreatedEvent, OnPaymentSuccess (avoid these)

CROSS-BC CONSISTENCY (Eventual):
  Order BC                    Inventory BC
  ─────────                   ─────────────
  OrderPlaced ────event────►  ReserveStock
                              (eventually consistent,
                               may fail → compensation)

EVENT SOURCING (optional extension):
  - Store events instead of current state
  - Rebuild state by replaying events
  - Full audit trail, temporal queries
  - Not always necessary — start with state-based, add events when needed
```

### Repositories

```
REPOSITORIES
============

Collection-like interface for accessing aggregates.
One repository per aggregate root. Hides persistence details.

CHARACTERISTICS:
  - One per aggregate root: OrderRepository, CustomerRepository
  - In-memory collection metaphor: findById(), save(), remove()
  - Returns fully-loaded aggregates (no lazy loading across boundaries)
  - Hides database details: callers don't know if SQL, NoSQL, or in-memory

INTERFACE:
  ┌─────────────────────────────────────────────┐
  │ REPOSITORY: OrderRepository                 │
  │                                             │
  │ findById(orderId: UUID): Order | null       │
  │   - Load complete aggregate with all        │
  │     contained entities and value objects    │
  │                                             │
  │ save(order: Order): void                    │
  │   - Insert new or update existing           │
  │   - Persists entire aggregate atomically    │
  │                                             │
  │ remove(order: Order): void                  │
  │   - Delete aggregate and contained entities │
  │                                             │
  │ findByCustomerId(customerId: UUID): Order[] │
  │   - Query method (use carefully)            │
  │   - Returns full aggregates                 │
  └─────────────────────────────────────────────┘

ANTI-PATTERNS:
  - Repository per entity (not per aggregate)
  - Leaking persistence details (SQL in domain layer)
  - Lazy loading across aggregate boundaries
  - Generic repository (Repository<T>) — too abstract, loses domain meaning
```

### Domain Services

```
DOMAIN SERVICES
===============

Stateless operations that don't naturally belong to any entity or value object.
Used for cross-aggregate operations or calculations involving multiple BCs.

WHEN TO USE:
  - Operation spans multiple aggregates
  - Operation doesn't belong to any single entity
  - Operation requires access to external services (via ports)
  - Pure calculation involving multiple value objects

EXAMPLE:
  ┌─────────────────────────────────────────────┐
  │ DOMAIN SERVICE: PricingService              │
  │                                             │
  │ calculateTotal(                             │
  │   items: LineItem[],                        │
  │   customer: Customer,                       │
  │   discountCode: DiscountCode | null         │
  │ ): Money                                    │
  │                                             │
  │ Logic:                                      │
  │   - Sum line item subtotals                 │
  │   - Apply customer tier discount            │
  │   - Apply discount code if valid            │
  │   - Apply tax based on shipping address     │
  │                                             │
  │ Why service? Spans Order + Customer + Code  │
  │ Doesn't belong to any single aggregate      │
  └─────────────────────────────────────────────┘

RULES:
  - Stateless: no instance fields that change
  - Named after domain operation: PricingService, not CalculationHelper
  - Lives in domain layer, not application layer
  - No infrastructure dependencies (inject via ports)
```

### Factories

```
FACTORIES
=========

Encapsulate complex creation logic for aggregates and value objects.
Ensure created objects satisfy all invariants.

WHEN TO USE:
  - Aggregate creation involves multiple steps
  - Construction requires data from multiple sources
  - Reconstitution from persistence (different from creation)
  - Creation logic is complex enough to warrant encapsulation

EXAMPLE:
  ┌─────────────────────────────────────────────┐
  │ FACTORY: OrderFactory                       │
  │                                             │
  │ createFromCart(cart: Cart, customer:         │
  │   Customer): Order                          │
  │                                             │
  │ Steps:                                      │
  │   1. Validate cart is not empty             │
  │   2. Create Order entity                    │
  │   3. Convert cart items to LineItems        │
  │   4. Apply customer tier pricing            │
  │   5. Set shipping address from customer     │
  │   6. Calculate total                        │
  │   7. Return fully-formed Order aggregate    │
  │                                             │
  │ reconstitute(data: OrderData): Order        │
  │   - Rebuild from persistence (no events)    │
  │   - Used by repository internally           │
  └─────────────────────────────────────────────┘

FACTORY PLACEMENT:
  - Static method on aggregate root: Order.createFromCart()
  - Separate factory class: OrderFactory (if complex)
  - Factory method on repository: for reconstitution
```

## Step 28: Event Storming Workshop

Source: Alberto Brandolini, "Introducing EventStorming" (2021), https://www.eventstorming.com/

Structured workshop format using colored sticky notes to discover domain behavior collaboratively. Goes beyond basic event storming (Step 13) with explicit 6-phase process and both process-level and design-level variants.

### Sticky Note Colors

```
EVENT STORMING STICKY NOTE COLORS
===================================

ORANGE stickies = DOMAIN EVENTS (past tense)
  - "Order was placed"
  - "Payment was processed"
  - "Inventory was reserved"
  - "Shipment was dispatched"
  - Something that happened, observable, indisputable

YELLOW stickies = COMMANDS (actions/decisions)
  - "Place Order"
  - "Process Payment"
  - "Cancel Subscription"
  - Triggered by actors or systems
  - Cause domain events to happen

BLUE stickies = ACTORS (people, roles, systems)
  - "Customer"
  - "Warehouse Manager"
  - "Payment Gateway" (external system)
  - "Cron Job" (automated trigger)
  - Who or what triggers commands

PURPLE stickies = POLICIES / BUSINESS RULES
  - "When payment fails, cancel order"
  - "When stock < threshold, trigger reorder"
  - "When order placed, start 30-min payment timer"
  - Reactive logic: "When [event], then [command]"
  - Can be automated or manual processes

RED stickies = HOT SPOTS / PAIN POINTS
  - "Why does this take 3 days?"
  - "Nobody knows who owns this"
  - "This breaks every Black Friday"
  - Disagreements, bottlenecks, unknowns
  - Mark anything contentious or problematic

GREEN stickies = READ MODELS / VIEWS (design level)
  - "Order Summary" (for customer dashboard)
  - "Inventory Report" (for warehouse ops)
  - "Sales Dashboard" (for management)
  - Data needed to make decisions or display UI

PINK stickies = EXTERNAL SYSTEMS
  - "Stripe" (payment processor)
  - "Twilio" (SMS provider)
  - "FedEx API" (carrier)
  - Systems outside your control
```

### Six Workshop Phases

```
EVENT STORMING 6-PHASE WORKSHOP
================================

PHASE 1: CHAOTIC EXPLORATION (15-30 min)
  Goal: Dump all domain events on the wall
  Rules:
    - Everyone writes orange stickies simultaneously
    - No discussion yet — just write
    - One event per sticky, past tense
    - Place on timeline (roughly left to right)
    - No filtering, no judgment
  Output: Wall covered in orange stickies
  Energy: High — lots of activity, some chaos

PHASE 2: ENFORCE THE TIMELINE (30-45 min)
  Goal: Put events in chronological order
  Rules:
    - Walk along timeline left to right
    - Discuss: "Does this happen before or after that?"
    - Resolve contradictions between experts
    - Merge duplicates, split overloaded events
    - Add missing events discovered during discussion
    - Mark hot spots (red) for contentious items
  Output: Ordered timeline of domain events
  Energy: Medium — focused discussion

PHASE 3: EXPLICIT WALK-THROUGH (30-45 min)
  Goal: Walk through the timeline with concrete scenarios
  Rules:
    - Pick a real scenario (e.g., "Customer buys last item")
    - Walk through events in order
    - Verify: "Does this always happen?"
    - Find alternative paths and exceptions
    - Mark hot spots where scenario breaks
  Output: Validated event timeline with alternatives
  Energy: Medium — narrative, collaborative

PHASE 4: PEOPLE AND SYSTEMS (15-30 min)
  Goal: Identify who and what triggers events
  Rules:
    - Add blue stickies (actors) above events
    - Add pink stickies (external systems) where relevant
    - Identify: who initiates? who receives? what system?
    - Note handoffs between people/departments
  Output: Events attributed to actors and systems
  Energy: Lower — organizational mapping

PHASE 5: POLICIES AND COMMANDS (30-45 min)
  Goal: Add reactive logic and triggering commands
  Rules:
    - Add yellow stickies (commands) before events
    - Add purple stickies (policies) connecting events to commands
    - Pattern: "When [event], then [policy], triggers [command]"
    - Identify automated vs manual policies
    - Find missing commands and policies
  Output: Full chain: Actor → Command → Aggregate → Event → Policy → Command
  Energy: Medium-High — modeling intensifies

PHASE 6: AGGREGATES (20-30 min)
  Goal: Identify consistency boundaries
  Rules:
    - Group command + event clusters
    - Each cluster suggests an aggregate
    - Mark aggregate boundaries with large stickies or boxes
    - Validate: "Is this a valid transactional boundary?"
    - Identify bounded context boundaries (where language shifts)
  Output: Aggregates identified, bounded contexts emerging
  Energy: Medium — architectural decisions
```

```
WORKSHOP TIMELINE EXAMPLE
==========================

TIME →→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→→

ACTOR:    [Customer]          [System]         [Warehouse]
            │                    │                   │
CMD:      [Place Order]    [Process Payment]  [Pick & Pack]
            │                    │                   │
AGGREGATE:  │                   │                   │
          ┌─┴───────┐    ┌─────┴─────┐      ┌─────┴─────┐
          │  Order   │    │  Payment  │      │Inventory  │
          └─┬───────┘    └─────┬─────┘      └─────┬─────┘
EVENT:      │                  │                   │
        [OrderPlaced]   [PaymentSucceeded]  [StockReserved]
            │                  │                   │
POLICY:     │            [When payment OK,        │
            │             confirm order]           │
            │                  │                   │
            ▼                  ▼                   ▼
        [OrderConfirmed] [ShipmentRequested] [ItemPicked]

HOT SPOT: [RED] "What if payment gateway is down?"
HOT SPOT: [RED] "Race condition on last item in stock"
```

### Process-Level vs Design-Level Event Storming

```
PROCESS-LEVEL vs DESIGN-LEVEL
==============================

PROCESS-LEVEL (Big Picture):
  - Who: Entire team + stakeholders
  - Duration: 2-4 hours
  - Focus: Business process end-to-end
  - Granularity: High-level events, actors, pain points
  - Output: Shared understanding, pain points, bounded context hints
  - Stickies: Orange (events), Red (hot spots), Blue (actors)
  - No code concepts: no aggregates, no commands, no read models
  - When: Early discovery, before committing to architecture

DESIGN-LEVEL (Deep Dive):
  - Who: Dev team + 1-2 domain experts per BC
  - Duration: 2-4 hours per bounded context
  - Focus: Single bounded context internals
  - Granularity: Commands, aggregates, policies, read models
  - Output: Aggregate design, domain events, integration points
  - Stickies: All colors (orange, yellow, blue, purple, green, red)
  - Code-concepts OK: aggregates are code candidates
  - When: After bounded contexts identified, before implementation

PROGRESSION:
  Process-Level → identifies bounded contexts
                 → each BC gets Design-Level session
                   → each Design-Level → aggregate/event/code design
```

## Step 29: Domain Storytelling Workshop

Source: Stefan Hofer, "Domain Storytelling" (2021), https://www.domainstorytelling.org/

Workshop technique where domain experts narrate how they work while modelers draw using standardized pictographic notation. Stories reveal domain concepts, workflows, and boundaries.

### Notation

```
DOMAIN STORYTELLING NOTATION
=============================

PERSON (Actor):
  ┌──────────┐
  │  👤 Name │  = Person who does work
  └──────────┘
  Examples: Customer, Warehouse Manager, Sales Rep

RECTANGLE (Work Object):
  ┌──────────┐
  │  📄 Name │  = Document, data, or physical item
  └──────────┘
  Examples: Order, Invoice, Package, Report

COMPUTER (System):
  ┌──────────┐
  │  💻 Name │  = Software system or tool
  └──────────┘
  Examples: ERP, Payment Gateway, CRM, Warehouse App

ARROW (Activity):
  ────verb────▶     = Actor performs action on work object
  (numbered: 1., 2., 3., ...)

READING PATTERN:
  Actor ──verb──▶ Work Object ──verb──▶ Actor
  "Customer creates Order"
  "Order is sent to Warehouse Worker"
  "Warehouse Worker picks Items"

COMPOSITION:
  Person ──creates──▶ Rectangle ──sent to──▶ Person
  Person ──uses──▶ Computer ──processes──▶ Rectangle
  Computer ──generates──▶ Rectangle ──sent to──▶ Person

GROUPING:
  [Team Name]
  ┌─────────────────────────────┐
  │  👤 Person1  👤 Person2     │  = Group/Team
  │  👤 Person3                 │
  └─────────────────────────────┘
```

### Reading Pattern

```
READING A DOMAIN STORY
========================

Each story reads like a sentence:
  Step N: [Actor] ──[verb]──▶ [Work Object]

EXAMPLE:
  1. 👤 Customer ──creates───▶ 📄 Order
  2. 📄 Order ────received by──▶ 👤 Sales Rep
  3. 👤 Sales Rep ──validates──▶ 📄 Order
  4. 👤 Sales Rep ──sends──────▶ 📄 ConfirmedOrder
  5. 📄 ConfirmedOrder ──sent──▶ 👤 Warehouse Worker
  6. 👤 Warehouse Worker ──picks──▶ 📦 Items
  7. 👤 Warehouse Worker ──packs──▶ 📦 Package
  8. 👤 Warehouse Worker ──creates─▶ 📄 ShippingLabel
  9. 📦 Package + 📄 ShippingLabel ──sent──▶ 👤 Courier
  10. 👤 Courier ──delivers───▶ 📦 Package ──to──▶ 👤 Customer

READING RULES:
  - Each numbered step = one action
  - Arrow direction = flow of work object or action
  - Multiple arrows in one step = parallel or combined action
  - Story tells WHO does WHAT with WHICH object
```

### Workshop Process

```
DOMAIN STORYTELLING WORKSHOP
=============================

ROLES:
  - Expert: Domain expert who tells the story (1-2 people)
  - Modeler: Draws the story as it's told (1 person)
  - Audience: Observers who verify and ask questions (3-10 people)

PROCESS:
  1. EXPERT TELLS
     - Expert picks a concrete scenario (last Thursday, not "in general")
     - Narrates step by step: "First I do this, then that..."
     - Uses their own words (domain language)

  2. MODELER DRAWS
     - Draws each step as expert narrates
     - Uses standard notation (person, rectangle, computer, arrow)
     - Numbers each step
     - Asks: "What happens next?" "Who does that go to?"
     - Pauses to confirm: "So you create an order and send it to...?"

  3. AUDIENCE VERIFIES
     - Audience watches and listens
     - Asks: "Does this always happen?" (find variations)
     - Asks: "What if this fails?" (find exceptions)
     - Points out: "I do it differently" (find inconsistencies)
     - No interrupting the expert mid-sentence

  4. ITERATE
     - Walk through story backward (step N → step 1)
     - Add alternative paths
     - Add exception handling
     - Compare stories from different experts

DURATION: 30-90 minutes per story
OUTPUT: Numbered pictographic diagram of workflow
```

### Discover Domain Patterns

```
DOMAIN STORYTELLING → DDD DISCOVERY
====================================

EXTRACT BOUNDED CONTEXTS:
  - Where actors change → potential BC boundary
  - Where work objects transform → different model needed
  - Where language shifts → separate BC
  - Example: Steps 1-4 (Sales language) vs Steps 5-9 (Warehouse language)

EXTRACT AGGREGATES:
  - Work objects that are created, modified, and passed around → aggregates
  - Objects that maintain consistency → aggregate root
  - Objects always grouped together → same aggregate
  - Example: Order + LineItems always together → Order aggregate

EXTRACT DOMAIN EVENTS:
  - Each handoff between actors → potential domain event
  - State changes mentioned in narration → domain events
  - "When X happens, then Y" → event + policy
  - Example: "When order is confirmed, warehouse gets notified"
           → OrderConfirmed event → NotifyWarehouse policy

EXTRACT COMMANDS:
  - Actions that trigger state changes → commands
  - "I place the order" → PlaceOrder command
  - "We process the payment" → ProcessPayment command

EXTRACT REPOSITORIES:
  - "I look up the order" → OrderRepository.findById()
  - "I check if customer exists" → CustomerRepository.exists()

EXAMPLE EXTRACTION:
  Domain Story: "Sales Rep validates Order, sends ConfirmedOrder to Warehouse"
  → BC: Ordering (Sales Rep, Order, ConfirmedOrder)
  → BC: Inventory (Warehouse Worker, Items, Package)
  → Aggregate: Order (contains: status, lineItems)
  → Event: OrderConfirmed
  → Command: ConfirmOrder
  → Policy: When OrderConfirmed, notify Warehouse
  → Handoff: Ordering BC → Inventory BC (via domain event)
```

## NFR Patterns (Performance Budgets, Accessibility, i18n, ISO 25010)

Non-functional requirement patterns that apply across domains. Specify as acceptance criteria, not vague aspirations.

### Performance Budgets (Web/SPA)

Source: Google Web Vitals, https://web.dev/vitals/

```
CORE WEB VITALS (LCP, FID/INP, CLS)
=====================================

Largest Contentful Paint (LCP):
  - Measures: loading performance (time largest element renders)
  - Good: <= 2.5s
  - Needs Improvement: 2.5s - 4.0s
  - Poor: > 4.0s
  - Budget example: "LCP <= 2.5s on 3G Fast connection for 75th percentile"
  - Optimization: preload hero image, server-side render critical content, CDN

First Input Delay (FID) / Interaction to Next Paint (INP):
  - FID measures: interactivity delay (first click to response)
  - INP measures: responsiveness (all interactions, worst case)
  - Good FID: <= 100ms; Good INP: <= 200ms
  - Budget example: "INP <= 200ms for 75th percentile of interactions"
  - Optimization: code splitting, defer non-critical JS, web workers

Cumulative Layout Shift (CLS):
  - Measures: visual stability (unexpected layout movement)
  - Good: <= 0.1
  - Needs Improvement: 0.1 - 0.25
  - Poor: > 0.25
  - Budget example: "CLS <= 0.1 across all pages"
  - Optimization: set explicit dimensions on images/ads, font-display: swap

BUNDLE SIZE BUDGETS:
  - JavaScript: <= 200KB compressed (initial load)
  - CSS: <= 50KB compressed
  - Total page weight: <= 1MB (initial load)
  - Font files: <= 100KB per weight/style
  - Image budget: <= 500KB for hero images (WebP/AVIF preferred)

PERFORMANCE BUDGET SPEC (in acceptance criteria):
  Given user loads homepage on 3G Fast connection
  When page finishes loading
  Then LCP <= 2.5s
  And CLS <= 0.1
  And total JS bundle <= 200KB gzipped
  And Time to Interactive <= 3.5s

MONITORING:
  - Real User Monitoring (RUM) for field data
  - Lighthouse CI for lab data in pipeline
  - Performance budgets in webpack/rollup/vite config
  - Alert when budget exceeded by > 10%
```

### Accessibility Requirements (WCAG 2.2)

Source: W3C, https://www.w3.org/TR/WCAG22/

```
WCAG 2.2 AA REQUIREMENTS (new in 2.2)
========================================

NEW SUCCESS CRITERIA IN WCAG 2.2:
  2.4.11 Focus Not Obscured (Minimum) [AA]:
    - When component receives keyboard focus, not entirely hidden
    - Acceptance: "Tab to any interactive element, focus indicator visible"

  2.4.12 Focus Not Obscured (Enhanced) [AAA]:
    - No part of focused component hidden by author-created content

  2.4.13 Focus Appearance [AAA]:
    - Focus indicator area >= 2px perimeter, 3:1 contrast ratio

  2.5.7 Dragging Movements [AA]:
    - Any dragging action has single-pointer alternative
    - Acceptance: "Drag-to-reorder list also has arrow button controls"

  2.5.8 Target Size (Minimum) [AA]:
    - Touch/pointer targets >= 24x24 CSS pixels (unless inline, essential, or browser-controlled)
    - Acceptance: "All buttons/links >= 24x24px, tested on mobile"

  3.2.6 Consistent Help [A]:
    - Help mechanism (contact, chatbot) in same relative order across pages
    - Acceptance: "Help link appears in same position on all pages"

  3.3.7 Redundant Entry [A]:
    - Don't ask for same info twice in same session
    - Auto-populate from earlier input
    - Acceptance: "Billing address auto-filled from shipping if same"

  3.3.8 Accessible Authentication (Minimum) [AA]:
    - No cognitive function test for auth (memorize, transcribe, calculate)
    - Acceptance: "Login supports password manager autofill, no CAPTCHA without alternative"

EXISTING KEY CRITERIA (carried from 2.1):
  1.1.1 Non-text Content: alt text for images
  1.3.1 Info and Relationships: semantic HTML
  1.4.3 Contrast (Minimum): 4.5:1 text, 3:1 large text
  2.1.1 Keyboard: all functionality via keyboard
  2.4.7 Focus Visible: visible focus indicator
  3.1.1 Language of Page: html lang attribute
  4.1.2 Name, Role, Value: ARIA for custom widgets

ACCEPTANCE CRITERIA TEMPLATE:
  Given [feature/component]
  When user [action with assistive technology]
  Then [expected accessible behavior]
  And WCAG criterion [X.X.X] satisfied

TESTING APPROACH:
  - Automated: axe-core, Lighthouse a11y audit in CI
  - Manual: keyboard-only navigation, screen reader (NVDA, VoiceOver)
  - User testing: include people with disabilities
  - Document: VPAT (Voluntary Product Accessibility Template)
```

### Internationalization (i18n) Requirements
```
I18N REQUIREMENTS CHECKLIST
============================

ENCODING:
  [ ] UTF-8 everywhere (database, API, frontend, file I/O)
  [ ] No hardcoded character encodings
  [ ] BOM handling specified (or explicitly excluded)
  [ ] Database collation set to utf8mb4 (MySQL) or UTF-8 (Postgres)

TEXT DIRECTION (RTL):
  [ ] CSS logical properties: margin-inline-start (not margin-left)
  [ ] dir="rtl" on <html> or container element
  [ ] Icon mirroring: arrows, progress bars, navigation flipped for RTL
  [ ] Form field alignment: labels right-aligned in RTL
  [ ] Text alignment: start/end (not left/right)

TEXT EXPANSION:
  [ ] UI accommodates 200% text expansion (German, Finnish)
  [ ] Button/label containers use min-width, not fixed-width
  [ ] No text baked into images
  [ ] Truncation strategy: ellipsis with tooltip for long translations
  [ ] Character count limits documented per field (for translators)

LOCALE-DEPENDENT FORMATTING:
  [ ] Dates: Intl.DateTimeFormat or library (moment/luxon/date-fns)
  [ ] Numbers: Intl.NumberFormat (decimal separator, grouping)
  [ ] Currency: locale-aware symbol position, decimal places
  [ ] Addresses: configurable fields per country
  [ ] Phone numbers: E.164 format storage, locale display

STRING EXTERNALIZATION:
  [ ] All user-facing strings in resource files (not hardcoded)
  [ ] String keys: namespace.module.key (e.g., checkout.payment.error)
  [ ] No string concatenation: use placeholders with context
  [ ] Pluralization rules: CLDR plural categories (zero/one/two/few/many/other)
  [ ] Gender agreement: context-aware translations

ACCEPTANCE CRITERIA:
  Given application locale is set to "ar-SA" (Arabic, Saudi Arabia)
  When user views checkout page
  Then text flows right-to-left
  And currency displays as "١٢٣٫٤٥ ر.س."
  And date displays as "١٤٤٥/٠٦/٢٠" (Hijri calendar)
  And all labels have correct Arabic translations
  And no English fallback text visible
```

### ISO/IEC 25010 Quality Model

Source: ISO/IEC 25010:2023, https://www.iso.org/standard/35733.html

```
QUALITY CHARACTERISTICS (8 categories)
========================================

1. FUNCTIONAL SUITABILITY
   Functional completeness: functions cover all tasks
   Functional correctness: correct results with needed precision
   Functional appropriateness: functions facilitate task completion
   Spec: "Search returns results matching query with 99.9% accuracy"

2. PERFORMANCE EFFICIENCY
   Time behavior: response times, processing times
   Resource utilization: CPU, memory, disk, network usage
   Capacity: max concurrent users, data volume, throughput
   Spec: "API responds within 200ms p95 under 1000 concurrent users"

3. COMPATIBILITY
   Co-existence: can operate with other software without conflict
   Interoperability: exchange and use information with other systems
   Spec: "Works alongside existing CRM without data conflicts"

4. INTERACTION CAPABILITY (was Usability)
   Appropriateness recognizability: users recognize if product suits needs
   Learnability: ease of learning
   Operability: ease of operation
   User error protection: prevents user errors
   User engagement: engaging/pleasant to use
   Inclusivity: accessible to diverse users (WCAG 2.2)
   Self-descriptiveness: explains itself
   Spec: "New user completes first task within 5 minutes without help"

5. RELIABILITY
   Availability: operational when needed (uptime SLA)
   Fault tolerance: operates despite hardware/software faults
   Recoverability: recover data and state after interruption
   Spec: "99.95% uptime, recovers from failure within 30 seconds"

6. SECURITY
   Confidentiality: data accessible only to authorized users
   Integrity: prevents unauthorized data modification
   Non-repudiation: actions attributable to actors
   Accountability: actions traceable to entity
   Authenticity: identity verified
   Resistance: resists attacks (OWASP ASVS)
   Spec: "All PII encrypted at rest (AES-256) and in transit (TLS 1.3)"

7. FLEXIBILITY
   Adaptability: adapt to different/environments without special action
   Scalability: handle growing workload
   Installability: install/uninstall in specified environment
   Replaceability: replace same functionality in same environment
   Spec: "Auto-scales from 100 to 10,000 users without config change"

8. MAINTAINABILITY
   Modularity: composed of discrete components
   Reusability: assets usable in other systems
   Analysability: impact of change assessed
   Modifiability: modified without introducing defects
   Testability: test criteria established and tests performed
   Spec: "Any component replaceable without affecting others (no circular deps)"

MAPPING TO REQUIREMENTS:
  For each NFR, specify:
    1. Quality characteristic (from above)
    2. Sub-characteristic
    3. Measure (quantitative metric)
    4. Target (threshold)
    5. Priority (must/should/could)
  Example: "Performance Efficiency > Time Behavior > API p95 latency <= 200ms (must)"
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
26. **Don't skip scoping in impact maps** — cutting deliverables is the whole point; maps without cuts are wishlists
27. **Don't write specs alone** — specification by example requires collaborative Three Amigos, not solo documentation
28. **Don't skip Lean UX experiments** — build-measure-learn replaces opinion-driven feature decisions with evidence
29. **Don't prototype for a week in design sprints** — prototype in ONE DAY, test with 5 users on day 5
30. **Don't skip discovery track** — dual-track means validating BEFORE building, not after shipping
31. **Don't confuse KRs with tasks** — key results measure outcomes (metric changed), tasks measure outputs (work done)
32. **Don't skip example mapping for BDD** — 4-color cards (yellow/blue/green/red) in 25-min sessions catch ambiguity before code
33. **Don't specify NFRs vaguely** — use ISO 25010 categories with measurable thresholds, not "fast" or "secure"
34. **Don't ignore text expansion in i18n** — German/Finnish expand 200%; hard-coded widths break layouts
35. **Don't treat AI fairness as optional** — demographic parity and equalized odds are measurable; Model Cards and Datasheets are documented requirements
36. **Don't ignore WCAG 2.2 new criteria** — Focus Not Obscured, Target Size, Dragging Movements are new AA requirements
37. **Don't skip EU AI Act compliance for high-risk AI** — Article 9 mandates risk management, data governance, human oversight; penalties up to EUR 35M
38. **Don't skip strategic DDD for complex domains** — bounded contexts prevent monolithic confusion; same-term-different-meaning heuristic catches hidden boundaries
39. **Don't model aggregates too large** — keep aggregates small (5-7 entities max); large aggregates cause contention and slow transactions
40. **Don't use technical terms in domain discussions** — ubiquitous language means domain terms in code, not "table", "row", "endpoint"
41. **Don't skip event storming phases** — chaotic exploration before design level; process-level before aggregates; each phase builds on previous
42. **Don't confuse entities with value objects** — entities have identity and lifecycle; value objects are immutable and defined by attributes (Money, Address)
43. **Don't reference aggregates by object** — always reference by ID across aggregate boundaries; object references leak transactional boundaries
44. **Don't skip domain storytelling notation** — standard pictographic notation enables shared understanding; freeform diagrams cause confusion
45. **Don't mix ubiquitous languages across BCs** — same word in different BCs should have different models; shared kernel is dangerous, use sparingly
