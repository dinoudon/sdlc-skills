---
name: sdlc-requirements-engineering
description: "Requirements: user story mapping (Jeff Patton), BDD/Gherkin (Cucumber, pytest-bdd), acceptance criteria (Given/When/Then), impact mapping (Gojko Adzic), example mapping, specification by example."
version: 2.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, requirements, user-stories, bdd, gherkin, acceptance-criteria, impact-mapping, example-mapping, specification-by-example]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-prd-to-production]
---

# Requirements Engineering

User story mapping, BDD/Gherkin, acceptance criteria, impact mapping, example mapping, specification by example.

## When to Use

Trigger when user:
- Writes user stories or acceptance criteria
- Sets up BDD/Gherkin workflows
- Creates impact maps or story maps
- Refines backlog or runs example mapping sessions
- Formalizes requirements as executable specifications

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

## How These Practices Connect

```
Impact Mapping (strategic)
    │
    ▼
User Story Mapping (release planning)
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
Acceptance Criteria (verification)
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
