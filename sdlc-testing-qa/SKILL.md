---
name: sdlc-testing-qa
description: "Test pyramid (unit/integration/e2e), TDD/BDD, property-based testing, mutation testing, performance testing (k6/Locust), security testing (SAST/DAST), accessibility testing. Includes Google testing culture."
version: 1.1.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, testing, tdd, bdd, playwright, pytest, k6, security, sast, dast, accessibility, google]
    related_skills: [sdlc-cicd-pipeline, sdlc-deployment, test-driven-development, security-review-owasp]
---

# Testing & Quality Assurance

Test pyramid, TDD/BDD, property-based testing, mutation testing, performance, security, accessibility testing. Includes Google testing culture and SV best practices.

## When to Use

Trigger when user:
- Writes tests (unit, integration, e2e)
- Sets up TDD or BDD workflows
- Runs performance/load tests
- Scans for security vulnerabilities
- Tests accessibility compliance
- Measures test quality

## Step 1: Test Pyramid

### Structure (bottom-up)
```
        /  E2E  \        ~10% — slow, brittle
       /---------\
      / Integration\     ~20% — module boundaries
     /---------------\
    /     Unit Tests   \ ~70% — fast, isolated
   /---------------------\
```

### Unit Tests (pytest)
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

### Integration Tests (pytest + testcontainers)
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

### E2E Tests (Playwright)
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

## Step 2: TDD

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
    vals = [(1000,"M"),(900,"CM"),(500,"D"),(400,"CD"),
            (100,"C"),(90,"XC"),(50,"L"),(40,"XL"),
            (10,"X"),(9,"IX"),(5,"V"),(4,"IV"),(1,"I")]
    result = ""
    for v, s in vals:
        while n >= v:
            result += s
            n -= v
    return result
```

### TDD Anti-Pattern: Horizontal Slices
**DO NOT** write all tests first, then all implementation.
**DO** vertical slices: one test → one implementation → repeat.

```
WRONG: test1, test2, test3 → impl1, impl2, impl3
RIGHT: test1 → impl1 → test2 → impl2 → test3 → impl3
```

## Step 3: BDD

### Gherkin Feature File
```gherkin
Feature: Shopping Cart
  Scenario: Add item to cart
    Given the cart is empty
    When I add a "Widget" with price 25.00
    Then the cart total should be 25.00
```

### pytest-bdd (Python)
```python
from pytest_bdd import scenarios, given, when, then, parsers

scenarios('../features/cart.feature')

@given('the cart is empty')
def empty_cart(cart):
    cart.clear()

@when(parsers.parse('I add a "{item}" with price {price:f}'))
def add_item(cart, item, price):
    cart.add(item, price)
```

## Step 4: Property-Based Testing

### Hypothesis (Python)
```python
from hypothesis import given, strategies as st

@given(st.lists(st.integers()))
def test_sort_is_idempotent(xs):
    assert sorted(sorted(xs)) == sorted(xs)

@given(st.integers(min_value=0, max_value=1000))
def test_roundtrip(n):
    assert parse_roman(romanize(n)) == n
```

## Step 5: Mutation Testing

### mutmut (Python)
```bash
mutmut run --paths-to-mutate=src/
mutmut results
```

### Mutation Score
- **> 90%** — excellent
- **70-90%** — good
- **< 70%** — tests are weak

## Step 6: Performance Testing

### k6 (Grafana)
```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '2m', target: 100 },
    { duration: '5m', target: 100 },
    { duration: '2m', target: 0 },
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'],
    http_req_failed: ['rate<0.01'],
  },
};

export default function () {
  const res = http.get('https://api.example.com/users');
  check(res, { 'status is 200': (r) => r.status === 200 });
  sleep(1);
}
```

```bash
k6 run script.js
k6 run --vus 50 --duration 30s script.js
```

### Locust (Python)
```python
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 3)

    @task(3)
    def index(self):
        self.client.get("/")
```

## Step 7: Security Testing

### SAST (Static)
```bash
semgrep --config=auto src/
bandit -r src/ -f json
```

### DAST (Dynamic)
```bash
zap-cli quick-scan http://localhost:8080
nuclei -u https://target.com -t cves/
```

### SCA (Software Composition)
```bash
snyk test
pip-audit --desc --format json
npm audit --production
```

### Secrets Detection
```bash
trufflehog git file://./ --only-verified
gitleaks detect --source . -v
```

## Step 8: Accessibility Testing

### axe-core + Playwright
```typescript
import AxeBuilder from '@axe-core/playwright';

test('no a11y violations', async ({ page }) => {
  await page.goto('/');
  const results = await new AxeBuilder({ page })
    .withTags(['wcag2a', 'wcag2aa'])
    .analyze();
  expect(results.violations).toEqual([]);
});
```

### Lighthouse CI
```bash
npx lhci autorun
```

## Step 9: Test Coverage

```bash
# Python
pytest --cov=src --cov-report=html

# JavaScript
npx jest --coverage

# Go
go test -coverprofile=coverage.out ./...
```

Targets: Statements > 80%, Branches > 75%, Functions > 80%.

## Step 10: Google Testing Culture

### Testing Trophy (not Pyramid)
```
        /  E2E  \        (few, expensive)
       /---------\
      / Integration\     (most tests here)
     /---------------\
    /     Unit Tests   \ (many, fast)
   /---------------------\
       Static Analysis    (lint, types)
```

### Test Size Classification
| Size | Runtime | Resources |
|------|---------|-----------|
| Small | < 1 min | 1 machine, no network |
| Medium | < 5 min | 1 machine, localhost |
| Large | Any | Multiple machines |

### Key Principles (from Google)
- **Flaky tests are worse than no tests** — they erode trust
- **Hermetic tests** — no external dependencies, deterministic
- **Don't mock what you don't own** — wrap external APIs
- **Hyrum's Law** — all observable behaviors become contracts
- **Scout rule** — leave code cleaner than you found it

## Step 11: Verification Loop (from ECC)

### 5 Phases
1. **Build** — project compiles
2. **Lint** — no violations
3. **Type Check** — no type errors
4. **Unit Tests** — all pass with coverage
5. **Integration/E2E** — critical paths verified

If ANY phase fails → STOP and fix.

## Step 12: QA Systematic Testing (from gstack)

### QA Checklist
- [ ] All pages load without errors
- [ ] Forms submit correctly
- [ ] Error states display properly
- [ ] Responsive layout works
- [ ] Keyboard navigation works
- [ ] Screen reader announces content
- [ ] No console errors

## Pitfalls

1. **Don't write more E2E than unit tests** — E2E is slow and brittle
2. **Don't mock everything in integration tests** — use real services
3. **Don't ignore flaky tests** — quarantine and fix
4. **Don't skip property-based tests for parsers**
5. **Don't run k6 against production without coordination**
6. **Don't rely solely on SAST** — combine with DAST
7. **Don't test a11y only with automated tools** — use screen readers
8. **Don't treat 100% coverage as target** — focus on critical paths
