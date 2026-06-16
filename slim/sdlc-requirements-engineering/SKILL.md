---
name: sdlc-requirements-engineering
description: "Requirements: user story mapping (Jeff Patton), BDD/Gherkin (Cucumber, pytest-bdd), acceptance criteria (Given/When/Then), impact mapping (Gojko Adzic), example mapping, specification by example (Gojko Adzic), JTBD framework, RICE scoring, WSJF prioritization, user story splitting, NFR patterns, requirements traceability, OKR alignment (Doerr), design thinking, Lean UX (Gothelf), design sprint (Knapp), dual-track agile (Cagan), AI/ML requirements, STRIDE security, OWASP ASVS, WCAG 2.2 AA accessibility, event storming (Brandolini), domain storytelling (Hofer), platform team requirements, cost of delay (Reinertsen), requirements elicitation techniques, ATDD, strategic DDD (bounded contexts, context mapping, ubiquitous language), tactical DDD (entities, value objects, aggregates, domain events, repositories, domain services, factories), event storming workshop (6-phase, process+design level), domain storytelling workshop (notation, discovery patterns)."
version: 4.8.0-moderate
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc-moderate, sdlc, requirements, user-stories, bdd, gherkin, acceptance-criteria, impact-mapping, example-mapping, specification-by-example, jtbd, rice-scoring, wsjf, story-splitting, nfr, traceability, okr, design-thinking, lean-ux, design-sprint, dual-track-agile, ai-ml, stride, owasp-asvs, wcag-2.2, accessibility, event-storming, domain-storytelling, platform-teams, cost-of-delay, elicitation, atdd, strategic-ddd, tactical-ddd, bounded-contexts, context-mapping, ubiquitous-language, domain-events, aggregates, value-objects, event-storming-workshop, domain-storytelling-workshop]
    related_skills: [sdlc-architecture-design, sdlc-testing-qa, sdlc-prd-to-production, sdlc-architecture-design]
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

**Best