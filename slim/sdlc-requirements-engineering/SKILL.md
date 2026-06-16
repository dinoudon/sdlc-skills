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

---
name: sdlc-requirements-engineering
description: "Requirements: user story mapping (Jeff Patton), BDD/Gherkin (Cucumber, pytest-bdd), acceptance criteria (Given/When/Then), impact mapping (Gojko Adzic), example mapping, specification by example (Gojko Adzic), JTBD framework, RICE scoring, WSJF prioritization, user story splitting, NFR patterns, requirements traceability, OKR alignment (Doerr), design thinking, Lean UX (Gothelf), design sprint (Knapp), dual-track agile (Cagan), AI/ML requirem
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
## Step 2: BDD / Gherkin
Source: https://cucumber.io/docs/gherkin/
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
```
## Step 3: Acceptance Criteria
Source: https://www.mountaingoatsoftware.com/blog/the-difference-between-a-story-and-a-acceptance-criteria
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
```
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
```
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
```
## Step 7: Jobs-to-be-Done (JTBD)
Source: Clayton Christensen, "Competing Against Luck" (2016); Anthony Ulwick, "Jobs to Be Done" (2016), https://strategyn.com/jobs-to-be-done/
Framework for discovering what customers truly hire products to do. Focus on the "job" (progress in a specific circumstance), not demographics or product features.
```
When [SITUATION], I want to [MOTIVATION], so I can [EXPECTED OUTCOME].
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
```
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
## Step 10: User Story Splitting Patterns
Source: Richard Lawrence, https://agileforall.com/patterns-for-splitting-user-stories/
When stories are too large (> 1 sprint), split using these patterns.
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
## Step 11: Non-Functional Requirements Patterns
NFRs define system qualities, not features. Specify as constraints with measurable thresholds.
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
```
## Step 13: Event Storming (Alberto Brandolini)
Source: Alberto Brandolini, "Introducing EventStorming" (2021), https://www.eventstorming.com/
Collaborative workshop technique for rapidly exploring complex business domains. Uses colored sticky notes on a large timeline to discover domain events, commands, aggregates, and read models. Entire domain on one wall — big picture.
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
```
## Step 14: Domain Storytelling (Stefan Hofer)
Source: Stefan Hofer, "Domain Storytelling" (2021), https://www.domainstorytelling.org/
Visual modeling technique where domain experts tell stories about how they work. Uses a pictographic notation with actors, work objects, and activities. Stories are told step-by-step, drawn as the expert narrates.
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
```
## Step 15: Requirements for Platform Teams
Platform teams build internal products consumed by other engineering teams. Requirements differ from end-user product requirements — customers are developers, value is developer productivity.
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
```
## Step 16: Cost of Delay Analysis (Don Reinertsen)
Source: Don Reinertsen, "The Principles of Product Development Flow" (2009)
Cost of delay (CoD) quantifies the economic impact of not delivering a feature. Used to make economically rational prioritization decisions instead of gut-feel.
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
```
## Step 17: Requirements Elicitation Techniques
Requirements elicitation = actively discovering stakeholder needs. No single technique captures all requirements — use a combination.
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

```
## NFR Patterns (Performance Budgets, Accessibility, i18n, ISO 25010)
Non-functional requirement patterns that apply across domains. Specify as acceptance criteria, not vague aspirations.
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