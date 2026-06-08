---
name: sdlc-testing-qa
description: "Test pyramid (unit/integration/e2e), TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance testing (k6/Locust), security testing (SAST/DAST), accessibility testing. Includes Google testing culture and test architecture patterns."
version: 2.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, testing, tdd, bdd, playwright, pytest, k6, security, sast, dast, accessibility, google, contract-testing, chaos-engineering, mutation-testing, property-based]
    related_skills: [sdlc-cicd-pipeline, sdlc-deployment, test-driven-development, security-review-owasp]
---

# Testing & Quality Assurance

Test pyramid, TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance, security, accessibility testing. Includes Google testing culture and test architecture patterns.

## When to Use

Trigger when user:
- Writes tests (unit, integration, e2e)
- Sets up TDD or BDD workflows
- Runs performance/load tests
- Scans for security vulnerabilities
- Tests accessibility compliance
- Measures test quality

## Step 1: Test Pyramid / Trophy

### Testing Trophy (Kent C. Dodds — 2019+)
Source: https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications

Replaces traditional "testing pyramid" with emphasis on integration tests:

```
        /  E2E  \\        few, expensive, critical flows only
       /---------\\
      / Integration\\     BULK of tests — test components together
     /---------------\\
    /     Unit Tests   \\ many, fast — only for complex logic
   /---------------------\\
       Static Analysis    cheapest — catches most bugs
```

**Key principles:**
- "Write tests. Not too many. Mostly integration."
- "The more your tests resemble the way your software is used, the more confidence they give you."
- Test behavior, not implementation details
- Avoid testing internals (state, lifecycle methods); test what user sees/does

### Test Size Classification (Google)
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

### MSW (Mock Service Worker) for API mocking
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

### fast-check (JavaScript/TypeScript)
Source: https://fast-check.dev/

```typescript
import fc from 'fast-check';

fc.assert(
  fc.property(fc.array(fc.integer()), arr =>
    JSON.parse(JSON.stringify(arr)).length === arr.length
  )
);

// Model-based testing
fc.assert(
  fc.property(fc.commands([addCmd, removeCmd, sizeCmd], { size: '+1' }), cmds => {
    const model = { size: 0 };
    const impl = new Set();
    fc.modelRun(() => ({ model, real: impl }), cmds);
  })
);
```

**2024-2026 developments:**
- Hypothesis "ghostwriter" auto-generates test code from type hints
- fast-check added `@fast-check/vitest` and `@fast-check/ava` helpers

## Step 7: Mutation Testing

### Pitest (Java)
Source: https://pitest.org/

```xml
<plugin>
  <groupId>org.pitest</groupId>
  <artifactId>pitest-maven</artifactId>
  <executions>
    <execution><goals><goal>mutationCoverage</goal></goals></execution>
  </executions>
</plugin>
```

### Stryker (JavaScript/TypeScript)
Source: https://stryker-mutator.io/

```json
// stryker.config.json
{
  "mutate": ["src/**/*.ts"],
  "testRunner": "vitest",
  "coverageAnalysis": "perTest"
}
```

### mutmut (Python)
```bash
mutmut run --paths-to-mutate=src/
mutmut results
```

### Mutation Score
- **> 90%** — excellent
- **70-90%** — good
- **< 70%** — tests are weak

## Step 8: Contract Testing

### Pact
Source: https://docs.pact.io/

Verifies that API provider and consumer agree on request/response shape without running both services together.

**Workflow:**
1. Consumer writes expectations (pact file)
2. Provider verifies against pact file
3. Pact Broker stores/shares contracts

```typescript
// Consumer test (Pact JS)
const interaction = {
  state: 'a user exists',
  uponReceiving: 'a request for user',
  withRequest: { method: 'GET', path: '/users/1' },
  willRespondWith: {
    status: 200,
    body: { id: 1, name: 'Alice' }
  }
};
```

**2024-2026 developments:**
- Pact v4 spec adds synchronous/async messages, binary content
- Pactflow (commercial) — managed Pact Broker with can-i-deploy checks
- Bi-directional contract testing (provider spec + consumer pact matched)

## Step 9: Chaos Engineering

### Principles
Source: https://principlesofchaos.org/

1. Build hypothesis around steady state behavior
2. Introduce real-world events (server failures, network partitions, resource exhaustion)
3. Run experiments in production (or staging with prod-like traffic)
4. Automate experiments to run continuously
5. Minimize blast radius

### Netflix Chaos Monkey
Source: https://netflix.github.io/chaosmonkey/

- Terminates random VM instances in production during business hours
- Part of Netflix Simian Army
- Integrates with Spinnaker

### Gremlin (Commercial)
Source: https://www.gremlin.com/

- Attack types: CPU, memory, disk, network (latency, packet loss), process kill, shutdown
- Free tier available for small teams

### AWS Fault Injection Service (FIS)
Source: https://aws.amazon.com/fis/

```json
{
  "description": "Terminate random EC2 instances",
  "targets": {
    "myInstances": {
      "resourceType": "aws:ec2:instance",
      "selectionMode": "COUNT(1)"
    }
  },
  "actions": {
    "terminate": {
      "actionId": "aws:ec2:terminate-instances",
      "parameters": {},
      "targets": ["myInstances"]
    }
  }
}
```

### LitmusChaos (CNCF Graduated)
Source: https://litmuschaos.io/

- Kubernetes-native chaos engineering
- ChaosHub with pre-built experiments
- Supports AWS, GCP, Azure

## Step 10: Performance Testing

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

### Locust (Python)
```python
from locust import HttpUser, task, between

class WebsiteUser(HttpUser):
    wait_time = between(1, 3)

    @task(3)
    def index(self):
        self.client.get("/")
```

## Step 11: Security Testing

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

## Step 12: Accessibility Testing

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

## Step 13: Test Architecture — Ports & Adapters

Source: "Growing Object-Oriented Software, Guided by Tests" (Freeman & Pryce)

**Pattern:** Hexagonal architecture makes code testable by isolating domain logic from infrastructure.

```python
# Port (interface)
class UserRepository(Protocol):
    def get_user(self, user_id: str) -> User: ...
    def save_user(self, user: User) -> None: ...

# Fake adapter for tests
class InMemoryUserRepository:
    def __init__(self):
        self.users = {}
    def get_user(self, user_id: str) -> User:
        return self.users[user_id]
    def save_user(self, user: User) -> None:
        self.users[user.id] = user

# Real adapter
class PostgresUserRepository:
    def __init__(self, db_session): ...
    # actual SQL
```

**Test strategy:**
- Domain logic tested via unit tests (pure functions, no I/O)
- Tests use fake/stub adapters — no real DB/network in unit tests
- Integration tests verify real adapters against real infrastructure
- Testcontainers for real infrastructure in CI

**Humble Object pattern** (Martin Fowler):
- Thin adapter layer, logic in testable core
- Source: https://martinfowler.com/bliki/HumbleObject.html

## Step 14: Test Coverage

```bash
# Python
pytest --cov=src --cov-report=html

# JavaScript
npx jest --coverage

# Go
go test -coverprofile=coverage.out ./...
```

Targets: Statements > 80%, Branches > 75%, Functions > 80%.

## Step 15: Verification Loop

### 5 Phases
1. **Build** — project compiles
2. **Lint** — no violations
3. **Type Check** — no type errors
4. **Unit Tests** — all pass with coverage
5. **Integration/E2E** — critical paths verified

If ANY phase fails → STOP and fix.

## Step 16: Snapshot Testing

Source: https://jestjs.io/docs/snapshot-testing, https://vitest.dev/guide/snapshot.html

Captures component/string output as .snap file, diffs on next run.

```typescript
// Jest/Vitest
expect(component).toMatchSnapshot();
expect(value).toMatchInlineSnapshot();  // Embeds in test file
```

**Key patterns:**
- Keep snapshots small, focused
- Use `toMatchInlineSnapshot()` for simple values (easier review)
- Mock non-deterministic data (dates, IDs) before snapshotting
- Treat .snap files as code — review diffs in PRs

## Step 17: Visual Regression Testing

### Percy (BrowserStack)
Source: https://docs.percy.io/docs/visual-testing-overview

Captures DOM snapshots, renders in consistent browser environment. Smart diffing ignores anti-aliasing.

### Chromatic (Storybook)
Source: https://www.chromatic.com/docs/visual-tests

Renders Storybook stories in real browsers. Turbosnap: only re-screenshots changed stories.

### Playwright Screenshots
Source: https://playwright.dev/docs/test-snapshots

```typescript
await page.screenshot({ path: 'screenshot.png' });
expect(page).toHaveScreenshot('name.png');  // Built-in visual comparison
```

**Key patterns:**
- Mask dynamic regions (ads, timestamps) with `mask` option
- Set consistent viewport, disable animations
- Threshold tuning: 0.1-0.3 for most UI

## Step 18: Fuzz Testing

### AFL++
Source: https://aflplus.plus/docs/

Coverage-guided fuzzer. Mutates inputs based on code coverage feedback.

### libFuzzer
Source: https://llvm.org/docs/LibFuzzer.html

In-process, coverage-guided fuzzer. Define `LLVMFuzzerTestOneInput()`.

### OSS-Fuzz (Google)
Source: https://google.github.io/oss-fuzz/

Continuous fuzzing service for open-source projects. Combines AFL++, libFuzzer, honggfuzz.

**Key patterns:**
- Provide seed corpus (valid inputs) for faster coverage
- Use sanitizer builds: `-fsanitize=address,undefined`
- Target parsers, decoders, protocol handlers, file format handlers

## Step 19: Load Testing Patterns

Source: https://grafana.com/docs/k6/latest/testing-guides/types-of-load-testing/

| Pattern | Description | k6 Scenario |
|---------|-------------|-------------|
| Smoke | Minimal load, verify system works | 1-2 VUs, short duration |
| Load | Expected concurrent users | Ramp to target, hold, ramp down |
| Stress | Push beyond expected load | Incrementally increase until failure |
| Soak | Sustained moderate load | 2+ hours, detect memory/connection leaks |
| Spike | Sudden burst of traffic | Quick ramp up, hold, quick ramp down |
| Breakpoint | Find breaking point | Incrementally increase until failure |

## Step 20: Test Data Management

**Factories > fixtures** for isolation.

```typescript
// Factory pattern
const createUser = (overrides = {}) => ({
  id: faker.string.uuid(),
  email: faker.internet.email(),
  name: faker.person.fullName(),
  ...overrides,
});
```

**Faker:** https://fakerjs.dev/ — generates realistic random data

**Key patterns:**
- Use sequences for unique fields: `(n) => user_${n}@test.com`
- Reset/auto-cleanup between tests (transaction rollback, truncate)
- Freeze faker seed for reproducibility when needed

## Step 21: Flaky Test Management

Source: https://testing.googleblog.com/2016/05/flaky-tests-at-google-and-how-we.html

**Detection:**
- Track pass/fail history per test across N runs
- Google: automated flakiness detection by re-running failed tests once

**Management:**
- Quarantine: exclude flaky from main suite, fix or delete
- Retry policies: `jest.retryTimes(3)`, `pytest-rerunfailures`
- Root causes: timing/race conditions, shared state, external deps

**Industry data:**
- Google found ~1.5% of tests flaky at any time
- Flaky tests erode trust, mask real failures

## Pitfalls

1. **Don't write more E2E than unit tests** — E2E is slow and brittle
2. **Don't mock everything in integration tests** — use real services (Testcontainers)
3. **Don't ignore flaky tests** — quarantine and fix
4. **Don't skip property-based tests for parsers** — Hypothesis/fast-check find edge cases
5. **Don't run k6 against production without coordination**
6. **Don't rely solely on SAST** — combine with DAST
7. **Don't test a11y only with automated tools** — use screen readers
8. **Don't treat 100% coverage as target** — focus on critical paths
9. **Don't skip contract tests for microservices** — Pact prevents integration surprises
10. **Don't skip chaos engineering in production** — staging doesn't match prod
11. **Don't test implementation details** — test behavior (Testing Library principle)
12. **Don't skip mutation testing** — coverage alone doesn't measure test quality
