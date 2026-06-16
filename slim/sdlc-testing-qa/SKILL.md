---
name: sdlc-testing-qa
description: "Test pyramid (unit/integration/e2e), TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance testing (k6/Locust), security testing (SAST/DAST), accessibility testing, AI-assisted test generation, serverless testing patterns, ML model testing, API contract testing, database testing, concurrency testing, observability-driven testing, visual regression testing, test data management. Includes Google testing culture, Netflix testing culture, Spotify Squad Health Check, Observability-Driven Development, and test architecture patterns."
version: 4.8.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, testing, tdd, bdd, playwright, pytest, k6, security, sast, dast, accessibility, contract-testing, chaos-engineering, mutation-testing, property-based, ai-test-generation, serverless-testing, ml-testing, visual-regression, performance-testing, test-data-management]
    related_skills: [sdlc-cicd-pipeline, sdlc-deployment, test-driven-development, security-review-owasp]
---

# Testing & Quality Assurance

Test pyramid, TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance, security, accessibility testing, AI-assisted test generation, serverless testing patterns, ML model testing, concurrency testing, observability-driven testing.

## When to Use

Trigger when user:
- Writes tests (unit, integration, e2e)
- Sets up TDD or BDD workflows
- Runs performance/load tests
- Scans for security vulnerabilities
- Tests accessibility compliance
- Measures test quality
- Generates tests with AI/LLM assistance
- Tests serverless functions locally

## Step 1: Test Pyramid / Trophy

### Testing Trophy (Kent C. Dodds)
Source: https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications

```
        /  E2E  \\\\        few, expensive, critical flows only
       /---------\\\\
      / Integration\\\\     BULK of tests — test components together
     /---------------\\\\
    /     Unit Tests   \\\\ many, fast — only for complex logic
   /---------------------\\\\
       Static Analysis    cheapest — catches most bugs
```

Key principles:
- "Write tests. Not too many. Mostly integration."
- "The more your tests resemble the way your software is used, the more confidence they give you."
- Test behavior, not implementation details

### Test Size Classification (Google)

| Size | Runtime | Resources |
|------|---------|-----------|
| Small | < 1 min | 1 machine, no network |
| Medium | < 5 min | 1 machine, localhost |
| Large | Any | Multiple machines |

### Key Principles

- **Flaky tests are worse than no tests** — they erode trust
- **Hermetic tests** — no external dependencies, deterministic
- **Don't mock what you don't own** — wrap external APIs
- **Hyrum's Law** — all observable behaviors become contracts

## Step 2: Unit Tests

### pytest
```python
def test_calculate_discount():
    assert calculate_discount(100, 0.1) == 90.0

@pytest.mark.parametrize("price,discount,expected", [
    (100, 0.1, 90.0),
    (200, 0.25, 150.0),
])
def test_calculate_discount_parametrized(price, discount, expected):
    assert calculate_discount(price, discount) == expected
```

### Vitest (JavaScript/TypeScript)
```typescript
import { describe, it, expect } from 'vitest';

describe('calculateDiscount', () => {
  it('applies percentage discount', () => {
    expect(calculateDiscount(100, 0.1)).toBe(90.0);
  });

  it('handles zero discount', () => {
    expect(calculateDiscount(100, 0)).toBe(100.0);
  });
});
```

## Step 3: Integration Tests

### pytest + testcontainers
```python
from testcontainers.postgres import PostgresContainer

@pytest.fixture(scope="session")
def postgres():
    with PostgresContainer("postgres:16") as pg:
        yield pg

def test_user_creation(postgres):
    engine = create_engine(postgres.get_connection_url())
    user = create_user(engine, "test@example.com")
    assert user.email == "test@example.com"
```

### Testing Library (JavaScript)
Source: https://testing-library.com/docs/guiding-principles

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

test('user can submit form', async () => {
  render(<LoginForm />);
  await userEvent.type(screen.getByLabelText(/email/i), 'user@example.com');
  await userEvent.type(screen.getByLabelText(/password/i), 'password123');
  await userEvent.click(screen.getByRole('button', { name: /submit/i }));
  expect(screen.getByText(/welcome/i)).toBeInTheDocument();
});
```

### MSW (Mock Service Worker)
Source: https://mswjs.io/

```typescript
import { http, HttpResponse } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  http.get('/api/users', () => {
    return HttpResponse.json([{ id: 1, name: 'Alice' }]);
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

## Step 4: E2E Tests

### Playwright
```typescript
import { test, expect } from '@playwright/test';

test('user can login', async ({ page }) => {
  await page.goto('/login');
  await page.fill('[data-testid="email"]', 'user@example.com');
  await page.fill('[data-testid="password"]', 'password123');
  await page.click('[data-testid="submit"]');
  await expect(page).toHaveURL('/dashboard');
});
```

```bash
npx playwright test
npx playwright test --shard=1/3
npx playwright codegen http://localhost:3000  # Record tests
```

## Step 5: TDD

### Cycle: Red → Green → Refactor
```python
# Step 1: RED — write failing test
def test_romanize():
    assert romanize(1) == "I"

# Step 2: GREEN — minimal code to pass
def romanize(n):
    return "I"

# Step 3: REFACTOR — improve while tests pass
def romanize(n):
    numerals = [(1000, "M"), (900, "CM"), (500, "D"), (400, "CD"),
                (100, "C"), (90, "XC"), (50, "L"), (40, "XL"),
                (10, "X"), (9, "IX"), (5, "V"), (4, "IV"), (1, "I")]
    result = ""
    for value, numeral in numerals:
        while n >= value:
            result += numeral
            n -= value
    return result
```

### BDD (pytest-bdd)
```python
from pytest_bdd import scenario, given, when, then, parsers

@scenario('cart.feature', 'Add item to cart')
def test_add_to_cart():
    pass

@given('an empty cart')
def empty_cart(cart):
    cart.clear()

@when(parsers.parse('I add a "{item}" with price {price:f}'))
def add_item(cart, item, price):
    cart.add(item, price)
```

## Step 6: Property-Based Testing

### Hypothesis (Python)
Source: https://hypothesis.works/

```python
from hypothesis import given, strategies as st

@given(st.lists(st.integers()))
def test_sort_is_idempotent(xs):
    assert sorted(sorted(xs)) == sorted(xs)

@given(st.integers(min_value=0, max_value=1000))
def test_roundtrip(n):
    assert parse_roman(romanize(n)) == n

# Ghostwriter — auto-generate test stubs from type hints
# hypothesis write my_module.my_function
```

### Hypothesis: Stateful Testing

```python
from hypothesis.stateful import RuleBasedStateMachine, rule, invariant
import hypothesis.strategies as st

class CounterModel(RuleBasedStateMachine):
    def __init__(self):
        super().__init__()
        self.model = 0        # expected value
        self.real = Counter()  # actual implementation

    @rule(n=st.integers(min_value=1, max_value=100))
    def increment(self, n):
        self.model += n
        self.real.increment(n)

    @rule(n=st.integers(min_value=1, max_value=100))
    def decrement(self, n):
        self.model = max(0, self.model - n)
        self.real.decrement(n)

    @invariant()
    def model_matches_real(self):
        assert self.model == self.real.value

TestCounter = CounterModel.TestCase
```

### Hypothesis: @st.composite and target()

```python
from hypothesis import strategies as st, target

@st.composite
def valid_email(draw):
    local = draw(st.text(alphabet=st.characte