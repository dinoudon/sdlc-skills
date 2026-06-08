---
name: sdlc-testing-qa
description: "Test pyramid (unit/integration/e2e), TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance testing (k6/Locust), security testing (SAST/DAST), accessibility testing, AI-assisted test generation, serverless testing patterns, ML model testing, API contract testing, database testing, concurrency testing, observability-driven testing, visual regression testing, test data management. Includes Google testing culture, Netflix testing culture, Spotify Squad Health Check, Observability-Driven Development, and test architecture patterns."
version: 4.6.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, testing, tdd, bdd, playwright, pytest, k6, security, sast, dast, accessibility, google, contract-testing, chaos-engineering, mutation-testing, property-based, ai-test-generation, serverless-testing, ml-testing, api-contract, database-testing, concurrency-testing, observability-testing, visual-regression, performance-testing, security-automation, test-data-management, hypothesis, fast-check, netflix, spotify, simian-army, squad-health-check, observability-driven, game-day, TAP]
    related_skills: [sdlc-cicd-pipeline, sdlc-deployment, test-driven-development, security-review-owasp]
---

# Testing & Quality Assurance

Test pyramid, TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance, security, accessibility testing, AI-assisted test generation, serverless testing patterns. Includes Netflix testing culture, Google testing culture, Spotify Squad Health Check, Observability-Driven Development, and test architecture patterns.

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

### Hypothesis Advanced: Stateful Testing

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
# Generates sequences of increments/decrements, finds minimal failing sequence
```

### Hypothesis Advanced: Database-Backed Example DB

```python
from hypothesis import settings, database

# Persistent example database — survives across runs
@settings(database=directory(".hypothesis/examples"))
@given(st.integers())
def test_with_persistence(n):
    ...
```

### Hypothesis Advanced: @st.composite and target()

```python
from hypothesis import strategies as st, target

@st.composite
def valid_email(draw):
    local = draw(st.text(alphabet=st.characters(whitelist_categories=('L', 'N')), min_size=1, max_size=64))
    domain = draw(st.sampled_from(['example.com', 'test.org', 'mail.io']))
    return f"{local}@{domain}"

# target() for directed search — guide shrinking toward interesting values
@given(st.integers(min_value=-1000, max_value=1000))
def test_interesting_edge(n):
    interestingness = -abs(n)  # closer to 0 is more interesting
    target(interestingness)
    assert n != 0  # will converge toward 0 faster
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
```

### fast-check Advanced: Model-Based Testing with Commands

```typescript
import fc from 'fast-check';

// Define model + commands for stateful testing
class AddCommand implements fc.Command<QueueModel, QueueReal> {
  constructor(readonly value: number) {}
  check = (model: QueueModel) => true;  // precondition
  run(model: QueueModel, real: QueueReal): void {
    model.items.push(this.value);
    real.enqueue(this.value);
    expect(real.size()).toBe(model.items.length);
  }
  toString = () => `add(${this.value})`;
}

class DequeueCommand implements fc.Command<QueueModel, QueueReal> {
  check = (model: QueueModel) => model.items.length > 0;
  run(model: QueueModel, real: QueueReal): void {
    const expected = model.items.shift()!;
    expect(real.dequeue()).toBe(expected);
  }
  toString = () => `dequeue()`;
}

fc.assert(
  fc.property(
    fc.commands([fc.integer().map(n => new AddCommand(n)), fc.constant(new DequeueCommand())], { size: '+1' }),
    cmds => {
      const real = new QueueReal();
      fc.modelRun(() => ({ model: { items: [] as number[] }, real }), cmds);
    }
  )
);
// Finds shortest failing command sequence, reports as: add(5) → add(3) → dequeue()
```

### fast-check: @fast-check/vitest Integration

```typescript
// npm i -D @fast-check/vitest
import { testProp, fc } from '@fast-check/vitest';

testProp('reverse twice is identity', [fc.array(fc.integer())], arr => {
  expect([...arr].reverse().reverse()).toEqual(arr);
});
// Better stack traces, native vitest reporters, property-based test discovery
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

### Stryker Advanced: Incremental Mode

```json
// stryker.config.json — incremental mode
{
  "incremental": true,
  "incrementalFile": ".stryker-patches/stryker.incremental.json"
}
```

Incremental mode:
- First run: full mutation, stores surviving mutants in `.stryker-patches/`
- Subsequent runs: only mutates changed files + re-checks previously surviving mutants
- CI: run full mutation on main, incremental on PRs — fast feedback

### Surviving Mutants Analysis

Surviving mutants = tests that **should** catch a bug but don't. Review workflow:

```bash
npx stryker run --reporters html
open reports/mutation.html  # interactive diff of each surviving mutant
```

**Categories of surviving mutants:**
1. **Weak test** — test doesn't assert the mutated behavior (add assertion)
2. **Equivalent mutant** — code change produces identical behavior (safe to ignore)
3. **Redundant code** — dead code that mutation reveals (remove it)

**Action:** For each surviving mutant, either add a test that kills it or document it as equivalent mutant.

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

### Bi-Directional Contract Testing (Pactflow)

Source: https://docs.pactflow.io/docs/bi-directional-contract-testing/

Traditional Pact requires provider-side verification (provider runs test against consumer pact). Bi-directional mode removes that requirement by matching **independently generated** contracts:

**Workflow:**
1. Consumer generates pact file (consumer expectations)
2. Provider generates OAS spec (provider capabilities)
3. Pactflow matches both: consumer pact must be subset of provider OAS
4. No provider-side verification code needed

```
Consumer (Pact)  ──► Pactflow ◄──  Provider (OpenAPI spec)
   {GET /users/1       matches       paths:
     response:                           /users/{id}:
       200                                 get:
       body: id, name                        responses: 200
                                            schema: {id, name}
```

**Provider side:**
```bash
# Generate OAS spec from existing tests or CI
# Use Dredd, Schemathesis, or prism to validate OAS against running API
npm i -D @pactflow/pact-bi-directional-provider-dredd
```

**When to use bi-directional:**
- Provider team cannot write Pact verification tests (legacy, different language)
- Provider already has OpenAPI spec
- Want to decouple provider/consumer release cycles

**Can-I-Deploy gate:**
```bash
npx pact-broker can-i-deploy --pacticipant UserService --version $GIT_SHA --to production
# Returns exit code 0 (safe) or 1 (blocked)

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

### LitmusChaos (CNCF Graduated 2023)
Source: https://litmuschaos.io/

- Kubernetes-native chaos engineering framework
- CNCF Graduated project (2023) — production-grade, broad ecosystem adoption
- ChaosEngine CRD defines experiments declaratively:

```yaml
apiVersion: litmuschaos.io/v1alpha1
kind: ChaosEngine
metadata:
  name: pod-delete-chaos
spec:
  appinfo:
    appns: default
    applabel: app=nginx
    appkind: deployment
  chaosServiceAccount: litmus-admin
  experiments:
    - name: pod-delete
      spec:
        components:
          env:
            - name: TOTAL_CHAOS_DURATION
              value: '30'
            - name: CHAOS_INTERVAL
              value: '10'
            - name: FORCE
              value: 'false'
```

**ChaosHub:** Central marketplace for experiments (https://hub.litmuschaos.io/)
- 60+ pre-built experiments across categories:
  - Kubernetes: pod-delete, pod-cpu-hog, pod-memory-hog, node-drain, node-taint
  - Network: chaos-network-loss, chaos-network-latency, chaos-network-duplicate
  - AWS: ec2-terminate, ebs-loss, s3-stop, rds-failover
  - GCP: gce-instance-delete, gcp-disk-loss
  - Azure: azure-instance-stop, azure-disk-loss
  - VMware: vm-poweroff, vm-snapshot-delete
- Workflow CRD: chain experiments in sequence/parallel with conditions

### Chaos Mesh (CNCF Incubating)
Source: https://chaos-mesh.org/

Kubernetes-native chaos engineering (alternative to LitmusChaos). CNCF incubating project.

- CRDs: ChaosExperiment, ChaosSchedule, Workflow
- Fault types: PodChaos, NetworkChaos, IOChaos, StressChaos, TimeChaos, KernelChaos
- Built-in web dashboard for experiment management
- DNS chaos injection (unique feature)
- JVM chaos (inject faults into Java apps without modifying code)

```yaml
apiVersion: chaos-mesh.org/v1alpha1
kind: NetworkChaos
metadata:
  name: network-delay
spec:
  action: delay
  mode: one
  selector:
    labelSelectors:
      app: my-app
  delay:
    latency: '100ms'
    jitter: '20ms'
  duration: '5m'
```

**LitmusChaos vs Chaos Mesh:**
| Feature | LitmusChaos | Chaos Mesh |
|---------|-------------|------------|
| CNCF Status | Graduated | Incubating |
| Cloud targets | AWS/GCP/Azure | Primarily K8s |
| Dashboard | Litmus Portal | Built-in UI |
| Workflow | ChaosWorkflow CRD | Workflow CRD |
| JVM chaos | Via plugins | Built-in |
| Best for | Multi-cloud chaos | K8s-focused chaos |

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

### Faker.js Advanced

```typescript
import { faker, fakerEN_US, fakerDE } from '@faker-js/faker';

// Locale-specific instances
const usPhone = fakerEN_US.phone.number();
const germanAddress = fakerDE.location.streetAddress();

// Structured data with relations
const createOrder = () => ({
  id: faker.string.uuid(),
  customer: createUser(),
  items: faker.helpers.multiple(() => ({
    product: faker.commerce.productName(),
    price: parseFloat(faker.commerce.price()),
    quantity: faker.number.int({ min: 1, max: 10 }),
  }), { count: { min: 1, max: 5 } }),
  createdAt: faker.date.recent({ days: 30 }),
});

// Conditional data
const createPremiumUser = () => ({
  ...createUser(),
  plan: 'premium',
  features: faker.helpers.arrayElements(['analytics', 'api', 'sso', 'audit'], { min: 2, max: 4 }),
});
```

### Factory Libraries

```typescript
// fishery (TypeScript) — composable factories with traits and associations
import { Factory } from 'fishery';

const userFactory = Factory.define(({ sequence, params, associations }) => ({
  id: sequence,
  email: params.email || `user${sequence}@test.com`,
  name: params.name || faker.person.fullName(),
  posts: associations.hasMany('post'),
}));

// Usage
const user = userFactory.build({ name: 'Specific Name' });
const users = userFactory.buildList(5);
const premium = userFactory.build({ plan: 'premium' });

// factory_bot (Ruby)
FactoryBot.define do
  factory :user do
    name { Faker::Name.name }
    email { Faker::Internet.email }
    trait :admin do
      role { 'admin' }
    end
  end
end
```

### Deterministic Seeds

```typescript
// Freeze faker for reproducible tests
import { faker } from '@faker-js/faker';

beforeEach(() => {
  faker.seed(42);  // same data every run
});

// Or use a global seed from env
faker.seed(process.env.CI ? 12345 : Date.now());

// fast-check also supports seeded runs
fc.assert(property, { seed: 42, numRuns: 100 });

// Hypothesis — settings profile with fixed database
from hypothesis import settings, seed
@settings(database=directory(".hypothesis/examples"), derandomize=True)
@given(st.integers())
def test_deterministic(n): ...
```

**Key patterns:**
- Use sequences for unique fields: `(n) => user_${n}@test.com`
- Reset/auto-cleanup between tests (transaction rollback, truncate)
- Freeze faker seed for reproducibility when needed
- Separate test data generation (factories) from assertions (tests)
- Use traits/overloads for variants: `createUser({ plan: 'premium' })`

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

### Google Deflake Bot Pattern

Google's internal deflaking infrastructure:

```
CI detects flaky test
      ↓
Deflake bot creates tracking issue
      ↓
Bot auto-runs test 100x in isolation
      ↓
If reproducible → attaches logs, assigns owner
If not reproducible → marks as "environment flaky"
      ↓
Owner fixes within SLA or test is quarantined
```

**Implementing Deflake bot in your CI:**

```yaml
# GitHub Actions: auto-detect and track flaky tests
name: Flaky Test Detector
on:
  workflow_run:
    workflows: ["CI"]
    types: [completed]

jobs:
  detect-flaky:
    runs-on: ubuntu-latest
    steps:
      - name: Analyze test results
        run: |
          # Compare current run results with last N runs
          # If test passed in main branch but failed in PR, flag as flaky
          python scripts/detect_flaky.py \
            --history 10 \
            --threshold 0.1 \
            --output flaky-report.json

      - name: Create issue for flaky tests
        if: failure()
        uses: actions/github-script@v7
        with:
          script: |
            const flaky = require('./flaky-report.json');
            for (const test of flaky.tests) {
              await github.rest.issues.create({
                owner: context.repo.owner,
                repo: context.repo.repo,
                title: `[Flaky] ${test.name}`,
                labels: ['flaky-test', 'auto-detected'],
                body: `Flaky rate: ${test.flake_rate}\nLast failure: ${test.last_error}`
              });
            }
```

### Quarantine + Fix Workflow

```
Step 1: DETECT
  - CI fails intermittently on test X
  - Pass rate drops below 95% threshold

Step 2: QUARANTINE (immediate)
  - Move to quarantine suite (separate job/config)
  - Mark with @flaky annotation/tag
  - Main suite continues without flaky test

Step 3: DIAGNOSE (within 48h)
  - Reproduce locally 100x: pytest -x --count=100 test_foo.py
  - Root cause categories:
    - Timing: add proper wait/retry, use polling not sleep
    - Shared state: isolate test data, use transactions/rollback
    - External deps: mock or use testcontainers
    - Non-deterministic: fix random seeds, control time

Step 4: FIX
  - Apply fix, run 1000x in CI to verify
  - Remove @flaky annotation

Step 5: DELETE (if unfixed within 2 weeks)
  - Flaky test with no fix → delete it
  - A test that can't be trusted provides negative value
```

```typescript
// Jest: quarantine pattern with configuration
// jest.config.js
module.exports = {
  projects: [
    {
      displayName: 'stable',
      testMatch: ['**/*.test.ts'],
      testPathIgnorePatterns: ['quarantine'],
    },
    {
      displayName: 'quarantine',
      testMatch: ['**/quarantine/**/*.test.ts'],
      retryTimes: 3,  // allow retries in quarantine only
    },
  ],
};
```

```python
# pytest: quarantine via markers
# pytest.ini
[pytest]
markers = flaky: quarantined flaky tests

# conftest.py
def pytest_collection_modifyitems(items):
    for item in items:
        if "flaky" in item.keywords:
            item.add_marker(pytest.mark.xfail(strict=False))

# Usage: @pytest.mark.flaky(reason="race condition in DB setup")
```

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

## Step 22: AI-Assisted Test Generation

### Scaffold-and-Refine Pattern

LLM-generated tests follow a scaffold-and-refine workflow: generate initial tests, run them, fix failures, repeat. Typical LLM first-pass test suite achieves **70-80% pass rate** on first generation. Iterative refinement (2-3 rounds) brings this to 90%+.

**Workflow:**
```
1. SCAFFOLD — LLM generates test suite from function signature + docstring
2. RUN      — execute generated tests, collect failures
3. ANALYZE  — feed failures back to LLM with error output
4. REFINE   — LLM fixes failing tests, adds missing edge cases
5. VALIDATE — human reviews for meaningful assertions, not just pass/fail
```

**Key insight:** LLMs generate syntactically correct tests 85-95% of the time. Semantic correctness (asserting the right thing) requires human review. The scaffold phase is fast; refinement is where value accumulates.

### AI Test Generation Anti-Patterns

| Anti-Pattern | Example | Fix |
|---|---|---|
| **Trivial assertions** | `assert result is not None` | Assert specific values, types, structure |
| **Happy-path only** | Only tests valid input | Add boundary, empty, negative, overflow cases |
| **No adversarial inputs** | Missing injection, XSS, path traversal | Add security-relevant inputs (SQL, shell, path) |
| **Assertion-free tests** | Calls function, no assert | Every test must have at least one meaningful assertion |
| **Snapshot of current behavior** | Asserts current (wrong) output | Verify against spec/docstring, not current output |
| **Copy-paste duplication** | 5 tests that differ only in input | Parametrize |
| **Overmocking** | Mocks everything, tests nothing | Mock only external boundaries (DB, network, filesystem) |

### Test Generation Prompt Patterns

Provide the LLM with: function signature, docstring, type hints, and 1-2 example input/output pairs. More context = better tests.

```
PROMPT TEMPLATE:
---
Write comprehensive {framework} tests for this function.

Function:
```{language}
{function_signature_with_type_hints}
```

Docstring:
{docstring}

Examples:
{example_input_output_pairs}

Requirements:
- Test happy path, edge cases, error cases
- Include boundary values (empty, null, max, min, negative)
- Include adversarial inputs (injection, overflow, malformed)
- Use parametrized tests where appropriate
- Assert specific values, not just "no exception"
---
```

**Example prompt (Python):**
```
Write pytest tests for this function:

def calculate_discount(price: float, discount: float) -> float:
    """Apply percentage discount to price.

    Args:
        price: Original price (must be >= 0)
        discount: Discount as decimal 0.0-1.0

    Returns:
        Discounted price

    Raises:
        ValueError: If price < 0 or discount not in [0, 1]

    Examples:
        calculate_discount(100, 0.1) -> 90.0
        calculate_discount(50, 0.5) -> 25.0
    """
```

### AI Test Generation Tools

**CodiumAI / Qodo** (https://qodo.ai/)
- Analyzes function behavior, generates test suggestions with explanations
- Supports Python, JavaScript/TypeScript, Java, Go, C++
- IDE integration (VS Code, JetBrains) + CLI (`qodo gen`)
- Generates test names that describe behavior (not `test_1`, `test_2`)
- Distinguishes between "meaningful" and "trivial" tests

**Diffblue Cover** (https://www.diffblue.com/)
- Java-focused, autonomous test generation (no prompt needed)
- Generates JUnit tests with meaningful assertions
- Integrates with Maven/Gradle, runs in CI
- Used by Goldman Sachs, Amazon — enterprise-grade
- Captures current behavior as regression tests; human reviews for correctness

**GitHub Copilot Test Generation**
- `/tests` or `/test` slash command in Copilot Chat
- Generates test file from source file context
- Supports all major frameworks (pytest, Jest, JUnit, Go testing)
- Best results: open source file + test file side-by-side for style reference
- Limitation: tends toward happy-path; manually prompt for edge cases

**Hypothesis Ghostwriter** (Python, built-in)
```bash
# Auto-generates property-based tests from type hints
hypothesis write my_module.my_function
# Produces: @given strategies matching parameter types
```

### Practical Workflow

```
1. Write function + docstring + type hints
2. AI generates scaffold tests (70-80% pass on first run)
3. Run tests, collect failures
4. Feed failures to AI: "These tests fail: {errors}. Fix them."
5. Review: are assertions meaningful? Add adversarial inputs manually.
6. Run mutation testing (Stryker/mutmut) to verify test quality
7. Commit tests + source together
```

## Step 23: Serverless Testing Patterns

Serverless functions (AWS Lambda, Cloudflare Workers, Azure Functions) are hard to test because they depend on platform-specific runtime, event formats, and ephemeral state. Test locally first, then verify in cloud.

### AWS Lambda — SAM Local

```bash
# Install SAM CLI
pip install aws-sam-cli

# Invoke function locally with event payload
sam local invoke ProcessOrder --event events/order.json

# Start local API Gateway
sam local start-api --port 3000
# Now curl http://localhost:3000/orders triggers handler

# Run tests against local API
sam local start-lambda --port 3001
# pytest calls Lambda via boto3 pointing to localhost:3001
```

```python
# pytest + local Lambda
import boto3
import json

@pytest.fixture
def lambda_client():
    return boto3.client('lambda', endpoint_url='http://localhost:3001')

def test_process_order(lambda_client):
    response = lambda_client.invoke(
        FunctionName='ProcessOrder',
        Payload=json.dumps({"orderId": "123", "items": [{"sku": "A", "qty": 2}]})
    )
    body = json.loads(response['Payload'].read())
    assert body['status'] == 'confirmed'
```

**template.yaml for local testing:**
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Resources:
  ProcessOrder:
    Type: AWS::Serverless::Function
    Properties:
      Handler: app.handler
      Runtime: python3.12
      Timeout: 30
      Environment:
        Variables:
          TABLE_NAME: !Ref OrdersTable
```

### Cloudflare Workers — Miniflare

Source: https://miniflare.dev/

Miniflare is local Cloudflare Workers simulator. Wrangler uses it under the hood.

```bash
# Install
npm install -D miniflare wrangler

# Run worker locally
wrangler dev  # starts on http://localhost:8787

# Or use miniflare directly
npx miniflare --modules src/index.js --port 8787
```

```typescript
// Test with Vitest + @cloudflare/vitest-pool-workers
// vitest.config.ts
import { defineWorkersConfig } from '@cloudflare/vitest-pool-workers/config';

export default defineWorkersConfig({
  test: {
    poolOptions: {
      workers: {
        wrangler: { configPath: './wrangler.toml' },
        miniflare: {
          // Override bindings for tests
          DURABLE_OBJECTS: { COUNTER: 'Counter' },
          KV: { CACHE: true },
          R2: { BUCKET: true },
        },
      },
    },
  },
});
```

```typescript
// src/index.test.ts
import { env, SELF } from 'cloudflare:test';
import { describe, it, expect } from 'vitest';

describe('Worker', () => {
  it('responds with greeting', async () => {
    const res = await SELF.fetch('https://example.com/');
    expect(res.status).toBe(200);
    expect(await res.text()).toBe('Hello World');
  });

  it('uses KV', async () => {
    await env.CACHE.put('key', 'value');
    const res = await SELF.fetch('https://example.com/kv/key');
    expect(await res.text()).toBe('value');
  });
});
```

### Azure Functions — Local Testing

```bash
# Install Azure Functions Core Tools
npm install -g azure-functions-core-tools@4

# Run locally
func start  # reads local.settings.json, starts on port 7071

# Test
curl http://localhost:7071/api/HttpTrigger?name=test
```

### Serverless Testing Strategy

```
Layer 1: UNIT TESTS (fast, no platform)
  - Test handler logic in isolation
  - Mock event/context objects
  - Run in CI without cloud credentials

Layer 2: LOCAL INTEGRATION (medium, platform simulation)
  - SAM local / Miniflare / func start
  - Test with real event payloads
  - Verify integrations: KV, D1, DynamoDB Local, S3 local

Layer 3: CLOUD INTEGRATION (slow, real platform)
  - Deploy to staging/dev account
  - Invoke real endpoints
  - Verify IAM permissions, cold starts, timeouts
```

**Event fixtures:** Store real event payloads as JSON files. AWS: `sam local generate-event`, Cloudflare: capture from `wrangler tail`.

```bash
# Generate sample AWS events
sam local generate-event s3 put > events/s3-put.json
sam local generate-event sqs receive-message > events/sqs.json
sam local generate-event apigateway aws-proxy > events/api-gateway.json
```

**Common pitfalls:**
- Don't test only locally — IAM, VPC, timeouts only manifest in cloud
- Don't skip cold start testing — first invocation is 10-100x slower
- Don't ignore timeout limits — test what happens at 29s (Lambda max)
- Don't assume local == cloud — DynamoDB Local behaves differently than real DynamoDB

## Step 24: ML Model Testing Patterns

ML models require specialized testing beyond traditional software testing. Models degrade silently, encode bias, and depend on data quality. Test at every stage: data ingestion, training, serving, and monitoring.

### Data Validation

Validate training and inference data before it reaches the model. Schema drift and distribution shifts are the #1 cause of silent model failure.

```python
# Great Expectations — data validation
import great_expectations as gx

context = gx.get_context()
validator = context.sources.pandas_default.read_csv("train.csv")

validator.expect_column_values_to_not_be_null("feature_1")
validator.expect_column_values_to_be_between("age", min_value=0, max_value=150")
validator.expect_column_values_to_be_in_set("label", ["cat", "dog", "bird"])
validator.expect_column_mean_to_be_between("income", min_value=20000, max_value=200000)

results = validator.validate()
assert results.success, f"Data validation failed: {results.results}"
```

```python
# Pandera — DataFrame schema validation (type-safe)
import pandera as pa
from pandera import Column, Check

schema = pa.DataFrameSchema({
    "feature_1": Column(float, Check.in_range(0, 1)),
    "feature_2": Column(float, Check(lambda s: s.std() > 0)),
    "label": Column(str, Check.isin(["positive", "negative"])),
    "timestamp": Column(pa.DateTime, Check.le(pd.Timestamp.now())),
})

@pa.check_types
def train(df: pa.typing.DataFrame[schema]):
    ...  # guaranteed clean input
```

**Data validation checklist:**
- Schema matches expected columns, types, nullable constraints
- No distribution shift between train/test/production (KS test, PSI)
- No data leakage (features that encode the target)
- Label distribution balanced or intentionally skewed
- Missing value rates within acceptable bounds

### Model Performance Testing

Test model accuracy, latency, and resource consumption as first-class test concerns.

```python
# pytest — model performance regression tests
import pytest
from sklearn.metrics import accuracy_score, f1_score, precision_score, recall_score

@pytest.fixture(scope="session")
def model():
    return load_model("models/latest.pkl")

@pytest.fixture(scope="session")
def test_data():
    return load_test_set("data/test.parquet")

def test_accuracy_above_threshold(model, test_data):
    preds = model.predict(test_data.X)
    acc = accuracy_score(test_data.y, preds)
    assert acc >= 0.92, f"Accuracy {acc:.3f} below threshold 0.92"

def test_f1_per_class(model, test_data):
    preds = model.predict(test_data.X)
    f1 = f1_score(test_data.y, preds, average=None)
    for cls, score in zip(test_data.classes, f1):
        assert score >= 0.85, f"Class {cls} F1={score:.3f} below 0.85"

def test_inference_latency(model, test_data):
    import time
    sample = test_data.X[:1]
    start = time.perf_counter()
    for _ in range(100):
        model.predict(sample)
    avg_ms = (time.perf_counter() - start) / 100 * 1000
    assert avg_ms < 50, f"Avg inference {avg_ms:.1f}ms exceeds 50ms"

def test_model_size(model):
    import os
    size_mb = os.path.getsize("models/latest.pkl") / (1024 * 1024)
    assert size_mb < 500, f"Model size {size_mb:.0f}MB exceeds 500MB limit"
```

### Bias Testing

Test for demographic parity, equalized odds, and disparate impact across protected groups.

```python
# Fairlearn — bias testing
from fairlearn.metrics import MetricFrame, demographic_parity_difference
from sklearn.metrics import accuracy_score

def test_demographic_parity(model, test_data):
    preds = model.predict(test_data.X)
    metric_frame = MetricFrame(
        metrics=accuracy_score,
        y_true=test_data.y,
        y_pred=preds,
        sensitive_features=test_data.demographics["gender"]
    )
    # Max accuracy difference between groups < 5%
    diff = metric_frame.difference(method="between_groups")
    assert diff < 0.05, f"Gender accuracy gap: {diff:.3f}"

def test_no_disparate_impact(model, test_data):
    preds = model.predict(test_data.X)
    dp_diff = demographic_parity_difference(
        test_data.y, preds,
        sensitive_features=test_data.demographics["race"]
    )
    # Disparate impact ratio should be 0.8-1.2 (80% rule)
    assert abs(dp_diff) < 0.2, f"Disparate impact: {dp_diff:.3f}"
```

```python
# Aequitas — bias audit framework
from aequitas.group import Group
from aequitas.bias import Bias
from aequitas.fairness import Fairness

g = Group()
bias_df = g.get_crosstabs(df[["score", "label", "race", "gender"]])
b = Bias()
disparities = b.get_disparity_predefined_groups(bias_df, ref_group_dict={"race": "white"})
# Check: Statistical Parity, Equal Opportunity, Predictive Parity
```

**Bias testing checklist:**
- Test accuracy/F1/recall across each protected attribute
- Check for proxy features that encode protected attributes
- Validate fairness metrics: demographic parity, equalized odds, calibration
- Test intersectional groups (e.g., gender x race)
- Document acceptable trade-offs between fairness metrics

### Drift Detection

Models degrade as data distributions shift. Detect drift early with statistical tests and monitoring.

```python
# Alibi Detect — drift detection
from alibi_detect.cd import KSDrift, ChiSquareDrift, TabularDrift
import numpy as np

# Reference data = training set
cd = KSDrift(test_data.X_train, p_val=0.05)

# Test on new batch
result = cd.predict(test_data.X_new)
assert not result["data"]["is_drift"], f"Data drift detected: {result['data']['p_val']}"
```

```python
# Evidently AI — comprehensive drift reports
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset, DataQualityPreset

report = Report(metrics=[DataDriftPreset(), DataQualityPreset()])
report.run(reference_data=train_df, current_data=prod_df)
report.save_html("drift_report.html")

# Programmatic check
result = report.as_dict()
drift_share = result["metrics"][0]["result"]["drift_share"]
assert drift_share < 0.3, f"Drift share {drift_share:.2f} exceeds 30%"
```

```python
# Custom drift detection in CI pipeline
from scipy.stats import ks_2samp

def test_feature_drift(train_df, prod_sample_df):
    """Run in CI weekly — compare prod sample to training data."""
    drifted_features = []
    for col in train_df.select_dtypes(include=[float, int]).columns:
        stat, p_val = ks_2samp(train_df[col].dropna(), prod_sample_df[col].dropna())
        if p_val < 0.01:
            drifted_features.append((col, p_val))
    assert not drifted_features, f"Drifted features: {drifted_features}"
```

**Drift detection strategy:**
| Type | Method | When |
|------|--------|------|
| Data drift | KS test, PSI, Chi-square | Continuous monitoring |
| Concept drift | Performance decay over time | Weekly model eval |
| Prediction drift | Output distribution shift | Per-batch monitoring |
| Feature drift | Per-feature statistical tests | CI pipeline + monitoring |

## Step 25: API Contract Testing — Advanced

### Schema Validation

Validate API requests/responses against OpenAPI/JSON Schema specs automatically.

```python
# Schemathesis — property-based API testing from OpenAPI spec
import schemathesis
from schemathesis.checks import not_a_server_error, status_code_conformance

schema = schemathesis.from_url("http://localhost:8080/openapi.json")

@schema.parametrize()
def test_api_conformance(case):
    response = case.call()
    case.validate_response(response)
    # Auto-tests: status codes match spec, response body matches schema,
    # no 500s, content-type headers correct

# Run with coverage reporting
# st run http://localhost:8080/openapi.json --checks all --dry-run
```

```typescript
# OpenAPI schema validation with Ajv
import Ajv from 'ajv';
import addFormats from 'ajv-formats';

const ajv = new Ajv({ allErrors: true });
addFormats(ajv);

function validateAgainstSchema(data: unknown, schema: object): boolean {
  const validate = ajv.compile(schema);
  const valid = validate(data);
  if (!valid) {
    console.error('Schema violations:', validate.errors);
  }
  return valid;
}

// In tests
test('GET /users response matches schema', async () => {
  const res = await fetch('/api/users');
  const data = await res.json();
  expect(validateAgainstSchema(data, userSchema)).toBe(true);
});
```

### Backward Compatibility Testing

Detect breaking changes before they reach consumers.

```bash
# oasdiff — OpenAPI diff tool
oasdiff breaking openapi-v1.yaml openapi-v2.yaml
# Outputs: BREAKING CHANGE: removed endpoint GET /users/{id}
# Outputs: BREAKING CHANGE: required field 'email' added to POST /users

# CI gate: fail if breaking changes detected
oasdiff breaking openapi-v1.yaml openapi-v2.yaml --fail-on ERR
```

```bash
# optic — API governance
optic diff openapi-v1.yaml openapi-v2.yaml
# Visual diff of API changes with impact analysis
```

**Backward compatibility rules:**
1. Never remove a field from a response (consumers depend on it)
2. Never add a required field to a request (breaks existing callers)
3. Never change a field type (string → int breaks parsers)
4. Never change a URL path or HTTP method
5. Always add optional fields with defaults
6. Deprecate first, remove after N versions

### Breaking Change Detection in CI

```yaml
# GitHub Actions: block PRs with breaking API changes
name: API Compatibility Check
on:
  pull_request:
    paths: ['openapi/**', 'api/**']

jobs:
  check-breaking:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Compare OpenAPI specs
        run: |
          # Get base spec from main branch
          git show origin/main:openapi/spec.yaml > /tmp/base.yaml
          # Check for breaking changes
          oasdiff breaking /tmp/base.yaml openapi/spec.yaml --fail-on ERR \
            --format json > breaking-report.json || true
          if [ -s breaking-report.json ]; then
            echo "::error::Breaking API changes detected"
            cat breaking-report.json
            exit 1
          fi
```

**Breaking change detection tools:**
| Tool | Language | Method |
|------|----------|--------|
| oasdiff | Go/CLI | Static OpenAPI diff |
| optic | Node/CLI | API governance rules |
| openapi-diff | Java | Semantic diff |
| swagger-diff | Node | Breaking change detection |
| bump-cli | Python | Version-aware diff |

## Step 26: Database Testing Patterns

### Migration Testing

Test migrations forward and backward, against real databases, with production-like data volumes.

```python
# pytest + Alembic migration testing
import pytest
from alembic.config import Config
from alembic import command
from sqlalchemy import create_engine, inspect, text

@pytest.fixture(scope="module")
def db_engine():
    with PostgresContainer("postgres:16") as pg:
        engine = create_engine(pg.get_connection_url())
        yield engine

def test_migrations_up(db_engine):
    """All migrations apply cleanly."""
    alembic_cfg = Config("alembic.ini")
    alembic_cfg.set_main_option("sqlalchemy.url", str(db_engine.url))
    command.upgrade(alembic_cfg, "head")
    inspector = inspect(db_engine)
    assert "users" in inspector.get_table_names()
    assert "orders" in inspector.get_table_names()

def test_migrations_down(db_engine):
    """All migrations revert cleanly."""
    alembic_cfg = Config("alembic.ini")
    alembic_cfg.set_main_option("sqlalchemy.url", str(db_engine.url))
    command.downgrade(alembic_cfg, "base")
    inspector = inspect(db_engine)
    assert "users" not in inspector.get_table_names()

def test_migration_preserves_data(db_engine):
    """Migrate with data — verify no data loss."""
    alembic_cfg = Config("alembic.ini")
    alembic_cfg.set_main_option("sqlalchemy.url", str(db_engine.url))

    # Apply previous version, insert data
    command.upgrade(alembic_cfg, "abc123")
    with db_engine.connect() as conn:
        conn.execute(text("INSERT INTO users (email) VALUES ('test@example.com')"))
        conn.commit()

    # Apply new migration
    command.upgrade(alembic_cfg, "head")

    # Verify data survived
    with db_engine.connect() as conn:
        result = conn.execute(text("SELECT email FROM users")).fetchall()
        assert any("test@example.com" in r[0] for r in result)
```

```bash
# Flyway migration testing
flyway -url=jdbc:postgresql://localhost:5432/test migrate
flyway validate  # check migration scripts are consistent
flyway info      # show applied vs pending migrations
```

### Seed Data

Manage test data with factories and version-controlled seed scripts.

```python
# pytest — database seeding with factories
import factory
from sqlalchemy.orm import Session

class UserFactory(factory.alchemy.SQLAlchemyModelFactory):
    class Meta:
        model = User
        sqlalchemy_session = Session

    id = factory.Sequence(lambda n: n + 1)
    email = factory.LazyAttribute(lambda o: f"user{o.id}@test.com")
    name = factory.Faker("name")
    role = "member"

    class Params:
        admin = factory.Trait(role="admin", email=factory.LazyAttribute(lambda o: f"admin{o.id}@test.com"))

# Usage
user = UserFactory()                      # basic user
admin = UserFactory(admin=True)           # admin user
users = UserFactory.create_batch(10)      # batch

# Reset sequences between tests for deterministic IDs
@pytest.fixture(autouse=True)
def reset_factories():
    UserFactory.reset_sequence()
```

```python
# SQL seed scripts — version-controlled
# seeds/001_base_data.sql
# INSERT INTO roles (name, permissions) VALUES
#     ('admin', '["read","write","delete","admin"]'),
#     ('member', '["read","write"]'),
#     ('viewer', '["read"]')
# ON CONFLICT (name) DO NOTHING;

# In tests — load seed files
def load_seeds(engine, seed_dir="seeds"):
    for sql_file in sorted(Path(seed_dir).glob("*.sql")):
        with engine.connect() as conn:
            conn.execute(text(sql_file.read_text()))
            conn.commit()
```

### Transaction Rollback Isolation

Wrap each test in a transaction that rolls back — fastest isolation pattern.

```python
# pytest — transaction rollback per test
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, Session
from sqlalchemy import event

@pytest.fixture
def db_session(postgres):
    """Each test gets a session that rolls back after test completes."""
    engine = create_engine(postgres.get_connection_url())
    connection = engine.connect()
    transaction = connection.begin()
    session = Session(bind=connection)

    yield session

    session.close()
    transaction.rollback()
    connection.close()

# Alternative: nested transactions (savepoints)
@pytest.fixture
def nested_session(postgres):
    engine = create_engine(postgres.get_connection_url())
    SessionLocal = sessionmaker(bind=engine)
    session = SessionLocal()
    session.begin_nested()  # savepoint

    @event.listens_for(session, "after_transaction_end")
    def restart_savepoint(sess, trans):
        if trans.nested and not trans._parent.nested:
            sess.begin_nested()

    yield session
    session.rollback()
    session.close()
```

**Database testing patterns:**
| Pattern | Speed | Isolation | Best For |
|---------|-------|-----------|----------|
| Transaction rollback | Fastest | Per-test | Unit/integration tests |
| Truncate between tests | Fast | Per-test | Tests that need committed data |
| Fresh database per test | Slow | Perfect | Migration tests, schema changes |
| Shared database + cleanup | Medium | Fragile | Only when other patterns fail |

## Step 27: Concurrency Testing

### Race Condition Detection

```python
# Thread sanitizer pattern — detect data races
import threading
import time
from concurrent.futures import ThreadPoolExecutor

def test_concurrent_counter_increment():
    """Detect race condition in shared counter."""
    counter = UnsafeCounter()  # Replace with your implementation
    iterations = 10000

    with ThreadPoolExecutor(max_workers=10) as pool:
        futures = [pool.submit(counter.increment) for _ in range(iterations)]
        for f in futures:
            f.result()

    # If this fails, there's a race condition
    assert counter.value == iterations, f"Expected {iterations}, got {counter.value}"

def test_concurrent_dict_access():
    """Detect race in shared dictionary."""
    shared = {}
    errors = []

    def writer(key, value):
        try:
            shared[key] = value
            time.sleep(0.001)  # amplify timing window
            assert shared[key] == value
        except (KeyError, AssertionError) as e:
            errors.append(e)

    with ThreadPoolExecutor(max_workers=20) as pool:
        for i in range(100):
            pool.submit(writer, f"key_{i}", f"value_{i}")

    assert not errors, f"Race conditions detected: {len(errors)}"
```

```go
// Go — race detector (built-in)
// go test -race ./...
// Catches data races at runtime using happens-before analysis

func TestConcurrentMapAccess(t *testing.T) {
    m := make(map[string]int)
    var wg sync.WaitGroup

    for i := 0; i < 100; i++ {
        wg.Add(1)
        go func(n int) {
            defer wg.Done()
            key := fmt.Sprintf("key_%d", n)
            m[key] = n           // DATA RACE without sync
            _ = m[key]
        }(i)
    }
    wg.Wait()
    // -race flag will report: "WARNING: DATA RACE"
}
```

```java
// Java — JCStress for concurrency tests
// http://openjdk.java.net/projects/code-tools/jcstress/

@JCStressTest
@Outcome(id = "1, 1", expect = ACCEPTABLE, desc = "Both updates visible")
@Outcome(expect = FORBIDDEN, desc = "Race condition")
@State
public class UnsafeDCL {
    int v;
    @Actor
    void writer() { v = 1; }
    @Actor
    void reader(I_Result r) { r.r1 = v; }
}
```

### Load Testing Patterns

```javascript
// k6 — ramping VUs pattern
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate, Trend } from 'k6/metrics';

const errorRate = new Rate('errors');
const latency = new Trend('api_latency');

export const options = {
  scenarios: {
    ramp_up: {
      executor: 'ramping-vus',
      startVUs: 0,
      stages: [
        { duration: '2m', target: 50 },
        { duration: '5m', target: 50 },
        { duration: '2m', target: 200 },
        { duration: '5m', target: 200 },
        { duration: '2m', target: 0 },
      ],
    },
  },
  thresholds: {
    errors: ['rate<0.05'],
    api_latency: ['p(95)<500', 'p(99)<1500'],
    http_req_duration: ['p(95)<500'],
  },
};

export default function () {
  const res = http.get('https://api.example.com/endpoint');
  errorRate.add(res.status !== 200);
  latency.add(res.timings.duration);
  check(res, { 'status 200': (r) => r.status === 200 });
  sleep(1);
}
```

```python
# Locust — weighted tasks with wait times
from locust import HttpUser, task, between, tag, events

class APIUser(HttpUser):
    wait_time = between(0.5, 2)

    @tag('read')
    @task(5)  # 5x weight = more frequent
    def get_users(self):
        with self.client.get('/users', catch_response=True) as res:
            if res.status_code != 200:
                res.failure(f"Got {res.status_code}")

    @tag('write')
    @task(1)
    def create_user(self):
        self.client.post('/users', json={"name": "test", "email": "test@test.com"})

    def on_start(self):
        """Login once per virtual user."""
        self.client.post('/login', json={"email": "load@test.com", "password": "test"})
```

### Deadlock Detection

```python
# Python — detect deadlocks with timeout + threading
import threading
import time

def test_deadlock_detection():
    """Verify no deadlock occurs with concurrent lock acquisition."""
    lock_a = threading.Lock()
    lock_b = threading.Lock()
    deadlock_detected = threading.Event()

    def worker_1():
        with lock_a:
            time.sleep(0.01)  # amplify timing
            if not lock_b.acquire(timeout=2.0):
                deadlock_detected.set()
                return
            lock_b.release()

    def worker_2():
        with lock_b:
            time.sleep(0.01)
            if not lock_a.acquire(timeout=2.0):
                deadlock_detected.set()
                return
            lock_a.release()

    t1 = threading.Thread(target=worker_1)
    t2 = threading.Thread(target=worker_2)
    t1.start()
    t2.start()
    t1.join(timeout=5)
    t2.join(timeout=5)

    assert not deadlock_detected.is_set(), "Deadlock detected"
```

```java
// Java — detect deadlocks with ThreadMXBean
import java.lang.management.ManagementFactory;
import java.lang.management.ThreadMXBean;

@Test
void detectDeadlocks() {
    ThreadMXBean bean = ManagementFactory.getThreadMXBean();
    long[] threadIds = bean.findDeadlockedThreads();
    assertNull(threadIds, "Deadlock detected among threads: " +
        Arrays.toString(threadIds));
}
```

```go
// Go — detect deadlocks with go-deadlock
// import "github.com/sasha-s/go-deadlock"
// Replaces sync.Mutex globally, detects lock ordering violations at runtime

var mu1, mu2 deadlock.Mutex

func TestNoDeadlock(t *testing.T) {
    go func() {
        mu1.Lock()
        defer mu1.Unlock()
        mu2.Lock()
        defer mu2.Unlock()
    }()
    // go-deadlock panics with stack trace if deadlock detected
}
```

**Concurrency testing checklist:**
- Run Go tests with `-race` flag in CI
- Use thread sanitizers for C/C++ (`-fsanitize=thread`)
- Test with multiple thread counts (1, 2, N, N*2)
- Add timeouts to all lock acquisitions in tests
- Test lock ordering consistency
- Verify idempotency of operations under concurrent access

## Step 28: Observability-Driven Testing

### Testing with Traces

Use distributed traces to validate request flows across services.

```typescript
// OpenTelemetry trace assertions in integration tests
import { trace, SpanStatusCode } from '@opentelemetry/api';
import { NodeTracerProvider } from '@opentelemetry/sdk-trace-node';
import { InMemorySpanExporter, SimpleSpanProcessor } from '@opentelemetry/sdk-trace-base';

const exporter = new InMemorySpanExporter();
const provider = new NodeTracerProvider();
provider.addSpanProcessor(new SimpleSpanProcessor(exporter));
provider.register();

afterEach(() => exporter.reset());

test('order flow creates correct spans', async () => {
  await createOrder({ item: 'widget', qty: 2 });

  const spans = exporter.getFinishedSpans();
  const spanNames = spans.map(s => s.name);

  expect(spanNames).toContain('order.create');
  expect(spanNames).toContain('inventory.check');
  expect(spanNames).toContain('payment.charge');
  expect(spanNames).toContain('email.send');

  // Verify span relationships (parent-child)
  const orderSpan = spans.find(s => s.name === 'order.create');
  const paymentSpan = spans.find(s => s.name === 'payment.charge');
  expect(paymentSpan.parentSpanId).toBe(orderSpan.spanContext().spanId);

  // Verify no errors in trace
  for (const span of spans) {
    expect(span.status.code).not.toBe(SpanStatusCode.ERROR);
  }
});

test('failed payment marks span as error', async () => {
  await expect(createOrder({ item: 'widget', qty: 2, forcePayFail: true }))
    .rejects.toThrow();

  const spans = exporter.getFinishedSpans();
  const paymentSpan = spans.find(s => s.name === 'payment.charge');
  expect(paymentSpan.status.code).toBe(SpanStatusCode.ERROR);
  expect(paymentSpan.attributes['error.type']).toBe('PaymentDeclined');
});
```

```python
# Python — trace assertions with OpenTelemetry test exporter
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export.in_memory import InMemorySpanExporter

exporter = InMemorySpanExporter()
provider = TracerProvider()
provider.add_span_processor(SimpleSpanProcessor(exporter))
trace.set_tracer_provider(provider)

def test_api_tracing():
    call_api_endpoint()
    spans = exporter.get_finished_spans()

    # Verify span structure
    span_names = [s.name for s in spans]
    assert "HTTP GET /users" in span_names
    assert "db.query" in span_names

    # Verify attributes
    db_span = next(s for s in spans if s.name == "db.query")
    assert db_span.attributes["db.statement"].startswith("SELECT")
    assert db_span.attributes["db.system"] == "postgresql"

    # Verify duration (no span > 500ms)
    for span in spans:
        duration_ms = (span.end_time - span.start_time) / 1_000_000
        assert duration_ms < 500, f"Span {span.name} took {duration_ms:.0f}ms"

    exporter.clear()
```

### Using Production Data for Test Scenarios

Derive test cases from real production behavior — traces, logs, and metrics become test inputs.

```python
# Replay production traces as test scenarios
import json
from datetime import datetime, timedelta

def load_prod_traces(trace_file="prod_traces.json"):
    """Load sampled production traces for replay testing."""
    with open(trace_file) as f:
        return json.load(f)

def test_replay_production_scenarios():
    """Replay real production request patterns."""
    traces = load_prod_traces()

    for trace_data in traces[:100]:  # sample 100 traces
        request = {
            "method": trace_data["http.method"],
            "path": trace_data["http.target"],
            "headers": trace_data.get("http.request.headers", {}),
            "body": trace_data.get("http.request.body"),
        }

        response = app.test_client().open(**request)

        # Verify same status code as production
        prod_status = trace_data["http.status_code"]
        assert response.status_code == prod_status, \
            f"Prod returned {prod_status}, test returned {response.status_code} for {request['path']}"
```

```python
# Generate load test scenarios from production traffic patterns
def generate_k6_script_from_traces(trace_file="prod_traces.json"):
    """Convert production traces to k6 load test script."""
    traces = load_prod_traces(trace_file)

    # Analyze traffic patterns
    endpoint_weights = {}
    for t in traces:
        path = t["http.target"]
        endpoint_weights[path] = endpoint_weights.get(path, 0) + 1

    total = sum(endpoint_weights.values())
    scenarios = []
    for path, count in sorted(endpoint_weights.items(), key=lambda x: -x[1]):
        weight = count / total
        scenarios.append({
            "path": path,
            "weight": round(weight * 100),
            "p95_ms": t.get("http.duration_p95", 500),
        })

    return scenarios

# Use in k6: weighted scenarios matching production traffic distribution
```

```yaml
# Chaos experiments derived from production incidents
chaos_scenarios:
  - name: "Database connection pool exhaustion"
    source_incident: "INC-2024-0412"
    description: "Connection pool hit limit during Black Friday traffic"
    experiment:
      type: network_chaos
      target: database
      action: connection_limit
      limit: 5
      duration: 5m
    expected_behavior: "Graceful degradation, queue requests, alert fires"

  - name: "Cache stampede after Redis failover"
    source_incident: "INC-2024-0315"
    experiment:
      type: pod_chaos
      target: redis
      action: pod_delete
    expected_behavior: "Circuit breaker opens, fallback to DB, no thundering herd"
```

**Observability-driven testing patterns:**
| Pattern | Source | Use Case |
|---------|--------|----------|
| Trace replay | Production traces | Validate request flows match expected behavior |
| Traffic shadowing | Prod traffic copy | Test new code with real traffic patterns |
| Log-driven tests | Error logs | Reproduce production bugs as regression tests |
| Metric assertions | Prometheus/Grafana | Validate SLOs in integration tests |
| Incident replay | Incident timelines | Chaos experiments from past incidents |

**Key practices:**
- Sample production traces weekly, use as regression test suite
- Convert production 5xx responses into test cases automatically
- Derive load test weights from actual traffic distribution
- Build chaos experiments from post-incident reviews
- Assert on trace structure (spans, attributes, relationships) not just response codes
- Use OpenTelemetry test exporters to capture and verify instrumentation

## Step 29: Mutation Testing

Mutation testing introduces small faults (mutants) into source code, then checks if tests catch them. Higher mutation score = stronger test suite.

### Tools

| Tool | Language | Notes |
|------|----------|-------|
| PIT (pitest) | Java/JVM | Most mature. Supports incremental analysis, JUnit 5 |
| Stryker | JS/TS/C# | First-class Jest/Vitest support, dashboard for tracking |
| Cosmic Ray | Python | Works with pytest, AST-level mutations |
| Mull | C/C++ | LLVM-based, integrates with Google Test |

### Mutation Score Thresholds

| Score | Interpretation |
|-------|---------------|
| >80% | Excellent — very strong test suite |
| 70-80% | Strong — acceptable for most projects |
| 50-70% | Weak — significant test gaps exist |
| <50% | Poor — tests barely verify behavior |

### Incremental Mutation for CI

Full mutation testing is slow (minutes to hours). Run incremental in CI:

```yaml
# .github/workflows/mutation.yml
- name: Mutation testing (incremental)
  run: |
    # PIT: only mutate changed files
    ./gradlew pitest \
      -Dpit.mutationEngine=bytecode \
      -PtargetClasses="com.app.changed.*" \
      -Dpit.timestampedReports=false
```

```bash
# Stryker incremental mode
npx stryker run --incremental  # uses .stryker-tmp/ for previous results
```

### Equivalent Mutant Problem

Some mutants are semantically identical to original (equivalent) — no test can kill them. Mitigations:
- Auto-detect: static analysis to flag common equivalents (e.g., `i++` → `++i`)
- Timeout: mutants that don't cause test failure within N seconds likely equivalent
- Mark known equivalents manually; exclude from score calculation
- Use equivalent mutant detection plugins (PIT's `return values` mutator)

### Key Mutators

| Mutator Type | Example |
|-------------|---------|
| Conditionals | `>` → `>=`, `==` → `!=` |
| Return values | `return x` → `return null` |
| Void method calls | Remove method calls |
| Math | `+` → `-`, `*` → `/` |
| Negate conditionals | `if (a)` → `if (!a)` |

## Step 30: Visual Regression Testing

Catch unintended UI changes by comparing screenshots pixel-by-pixel.

### Tools

| Tool | Best For | Notes |
|------|----------|-------|
| Percy (BrowserStack) | Teams needing CI/CD integration | Snapshot diffs in PR, approve/reject workflow |
| Chromatic | Storybook projects | First-class Storybook integration, turbosnap |
| BackstopJS | Standalone scripts | OnBefore/OnReady scripts, Scenarios JSON config |
| Playwright `toHaveScreenshot()` | Minimal setup | Built-in, no external service, `pixelmatch` under hood |
| reg-suit | GitHub-centric | Stores snapshots in S3, comments on PRs |

### Playwright Visual Comparison

```typescript
import { test, expect } from '@playwright/test';

test('homepage visual check', async ({ page }) => {
  await page.goto('/');
  await expect(page).toHaveScreenshot('homepage.png', {
    maxDiffPixelRatio: 0.01,   // <1% pixels may differ
    animations: 'disabled',     // prevent flaky diffs
  });
});

// Component-level screenshot
test('button states', async ({ page }) => {
  const button = page.locator('button.submit');
  await expect(button).toHaveScreenshot('submit-button.png');
});
```

### Threshold Tuning

| Threshold | Use Case |
|-----------|----------|
| 0 (exact) | Critical UI (medical, financial dashboards) |
| 0.1 | High-fidelity apps (marketing pages) |
| 0.2 | Standard apps — good balance |
| 0.3 | Tolerant — apps with dynamic fonts, anti-aliasing |

### Masking Dynamic Content

```typescript
// Playwright: mask selectors that change every render
await expect(page).toHaveScreenshot('dashboard.png', {
  mask: [
    page.locator('.timestamp'),
    page.locator('.user-avatar'),
    page.locator('[data-testid="random-id"]'),
  ],
  maskColor: '#FF00FF',  // highlight masked areas
});
```

### Baseline Management

```
project/
  __screenshots__/          # checked into git (or S3)
    homepage.png            # approved baseline
    dashboard.png
  test-results/             # gitignored — new screenshots
    homepage.png            # diff generated here
```

**Workflow:** Run tests → diff against baseline → approve new baseline or fix regression. In CI, upload diffs as artifacts; require manual approval for baseline updates.

## Step 31: Performance Testing Patterns

### k6 Load Profiles

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

// Profile 1: Constant VUs
export const options = {
  scenarios: {
    constant_load: {
      executor: 'constant-vus',
      vus: 50,
      duration: '5m',
    },
  },
};

// Profile 2: Ramping VUs (step load)
export const rampingOptions = {
  scenarios: {
    ramping: {
      executor: 'ramping-vus',
      startVUs: 0,
      stages: [
        { duration: '2m', target: 100 },  // ramp up
        { duration: '5m', target: 100 },  // hold
        { duration: '2m', target: 0 },    // ramp down
      ],
    },
  },
};

// Profile 3: Constant Arrival Rate (requests/sec target)
export const arrivalOptions = {
  scenarios: {
    constant_rate: {
      executor: 'constant-arrival-rate',
      rate: 1000,              // 1000 iterations/sec
      timeUnit: '1s',
      duration: '5m',
      preAllocatedVUs: 200,    // initial VU pool
      maxVUs: 500,             // can scale up to
    },
  },
};

// Profile 4: Ramping Arrival Rate
export const rampArrivalOptions = {
  scenarios: {
    ramp_rate: {
      executor: 'ramping-arrival-rate',
      startRate: 100,
      timeUnit: '1s',
      stages: [
        { target: 500, duration: '3m' },   // ramp to 500 rps
        { target: 500, duration: '5m' },   // hold
        { target: 0, duration: '2m' },     // ramp down
      ],
      preAllocatedVUs: 200,
      maxVUs: 1000,
    },
  },
};
```

### Test Types

| Type | Purpose | Duration | Key Metric |
|------|---------|----------|------------|
| Smoke | Verify script works | 1-2 min, few VUs | Error rate |
| Load | Expected traffic | 15-30 min | p95 latency < SLA |
| Stress | Beyond capacity | 15-30 min | Where does it break? |
| Soak | Memory leaks, degradation | 4-24 hours | Memory growth, latency drift |
| Spike | Sudden traffic surge | Short bursts | Recovery time |
| Breakpoint | Find max capacity | Until failure | Max RPS before errors |

### k6 Thresholds

```javascript
export const options = {
  thresholds: {
    http_req_duration: ['p(95)<500', 'p(99)<1500'],  // 95th < 500ms, 99th < 1500ms
    http_req_failed: ['rate<0.01'],                    // <1% errors
    http_reqs: ['rate>100'],                           // >100 req/sec throughput
    iteration_duration: ['avg<3000'],                  // avg iteration < 3s
  },
};
```

### Tool Comparison

| Feature | k6 | Locust | Gatling | Artillery |
|---------|-----|--------|---------|-----------|
| Language | JS (Go engine) | Python | Scala/Kotlin/Java | YAML/JS |
| Protocol | HTTP/WS/gRPC | HTTP (extensible) | HTTP/JMS/gRPC | HTTP/WS |
| Distributed | k6-operator (k8s) | Native | Built-in | Fargate plugin |
| Realtime UI | k6 Cloud | Web UI built-in | HTML report | Dashboard |
| Best for | Dev teams, CI | Python shops, complex logic | Enterprise, JVM | Quick YAML setup |

## Step 32: Security Testing Automation

### OWASP ZAP Automation Framework

```yaml
# zap-automation.yaml — run ZAP in Docker headless
---
env:
  contexts:
    - name: "target-app"
      urls:
        - "https://staging.example.com"
      includePaths:
        - "https://staging.example.com/.*"
      excludePaths:
        - ".*logout.*"
  parameters:
    failOnError: true
    progressToStdout: true

jobs:
  # 1. Spider — crawl the app
  - type: spider
    parameters:
      context: "target-app"
      maxDuration: 5  # minutes

  # 2. Ajax spider for SPAs
  - type: ajaxSpider
    parameters:
      context: "target-app"
      maxDuration: 5

  # 3. Active scan
  - type: activeScan
    parameters:
      context: "target-app"
      maxRuleDurationInMins: 5
      maxScanDurationInMins: 30

  # 4. Report
  - type: report
    parameters:
      template: traditional-html
      reportFile: zap-report.html
```

```bash
# Run ZAP baseline scan (passive only, fast)
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://staging.example.com \
  -r zap-baseline.html

# Full scan (active + passive, slower)
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-full-scan.py \
  -t https://staging.example.com \
  -r zap-full.html

# API scan (OpenAPI spec)
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py \
  -t https://staging.example.com/openapi.json \
  -f openapi \
  -r zap-api.html
```

### SAST Tools

| Tool | Type | Integration | Strengths |
|------|------|-------------|-----------|
| Semgrep | Pattern-based | CLI, CI, PR comments | Custom rules, fast, 2000+ rulesets |
| CodeQL | Semantic analysis | GitHub native | Deep dataflow analysis, zero-config |
| SonarQube | Multi-language | CI, IDE, PR decoration | Quality gates, technical debt tracking |
| Bandit | Python SAST | CLI, pre-commit | Python-specific, low false positives |
| ESLint Security | JS/TS linting | IDE, CI | `eslint-plugin-security` rules |

### Pipeline Integration (SAST + DAST)

```yaml
# GitHub Actions — full security scan pipeline
security-scan:
  jobs:
    sast:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - uses: returntocorp/semgrep-action@v1
          with:
            config: >-
              p/owasp-top-ten
              p/security-audit
              p/secrets

    dast:
      needs: [deploy-staging]  # run after deploy
      runs-on: ubuntu-latest
      steps:
        - name: ZAP baseline scan
          uses: zaproxy/action-baseline@v0.12.0
          with:
            target: 'https://staging.example.com'
            rules_file_name: '.zap/rules.tsv'
            fail_action: true  # fail pipeline on high/medium

    dependency-check:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - uses: aquasecurity/trivy-action@master
          with:
            scan-type: 'fs'
            severity: 'CRITICAL,HIGH'
```

### API Fuzzing

```python
# Schemathesis — fuzz test from OpenAPI spec
import schemathesis

schema = schemathesis.from_url("https://api.example.com/openapi.json")

@schema.parametrize()
def test_api(case):
    response = case.call()
    case.validate_response(response)  # checks status codes, schema conformance

# Finds: 500s, schema violations, malformed responses
```

```bash
# RESTler — stateful API fuzzer from OpenAPI
restler-compile --api_spec api.json
restler-fuzz --grammar_file Compile/grammar.py \
  --target_ip staging.example.com \
  --target_port 443 \
  --time_budget 60  # minutes
```

| Tool | Approach | Strengths |
|------|----------|-----------|
| Schemathesis | Property-based from OpenAPI | Fast, pytest integration, schema validation |
| RESTler | Stateful grammar-based | Learns API state transitions, finds complex bugs |
| Fuzz-lightyear | Swagger-based | Sequence-aware fuzzing |
| Burp Suite headless | Full DAST suite | `burp-rest-api` for CI, deep scanning |

## Step 33: Test Data Management

### Synthetic Data Generation

```python
# Faker (Python)
from faker import Faker
fake = Faker()
Faker.seed(42)  # reproducible data

user = {
    "name": fake.name(),
    "email": fake.email(),
    "ssn": fake.ssn(),  # fake SSN
    "address": fake.address(),
    "credit_card": fake.credit_card_number(),
}
# Generate 1000 users
users = [fake.profile() for _ in range(1000)]
```

```javascript
// Faker (JavaScript)
import { faker } from '@faker-js/faker';
faker.seed(123);

const user = {
  name: faker.person.fullName(),
  email: faker.internet.email(),
  phone: faker.phone.number(),
  address: faker.location.streetAddress(),
};
```

### Factory Boy (Python — factory pattern)

```python
import factory
from app.models import User

class UserFactory(factory.Factory):
    class Meta:
        model = User

    name = factory.Faker('name')
    email = factory.LazyAttribute(lambda o: f"{o.name.lower().replace(' ', '.')}@example.com")
    is_active = True
    role = 'user'

class AdminFactory(UserFactory):
    role = 'admin'

class InactiveUserFactory(UserFactory):
    is_active = False
    deleted_at = factory.LazyFunction(datetime.now)

# Usage
user = UserFactory()                    # default user
admin = AdminFactory()                  # admin
batch = UserFactory.build_batch(50)     # 50 users
```

### Fixtures Patterns (pytest)

```python
import pytest

# Scope hierarchy: function → class → module → package → session
@pytest.fixture(scope="session")
def db_engine():
    """Create engine once per test session."""
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    yield engine
    engine.dispose()

@pytest.fixture(scope="function")
def db_session(db_engine):
    """Rollback after each test — perfect isolation."""
    connection = db_engine.connect()
    transaction = connection.begin()
    session = Session(bind=connection)
    yield session
    session.close()
    transaction.rollback()
    connection.close()

# Snapshot fixtures — store expected data
@pytest.fixture
def expected_user_response(snapshot):
    return snapshot("expected_user.json")
```

### Data Masking / Anonymization

| Technique | Library | Use Case |
|-----------|---------|----------|
| Presidio (Microsoft) | `presidio-analyzer`, `presidio-anonymizer` | PII detection + redaction in text |
| k-anonymity | `arx` (Java), custom scripts | Ensure each record matches ≥k others |
| Pseudonymization | Custom mapping tables | Replace real IDs with consistent fake IDs |
| Differential privacy | OpenDP, Google DP Library | Add statistical noise to datasets |

```python
# Presidio example
from presidio_analyzer import AnalyzerEngine
from presidio_anonymizer import AnonymizerEngine
from presidio_anonymizer.entities import OperatorConfig

analyzer = AnalyzerEngine()
anonymizer = AnonymizerEngine()

text = "John Smith, SSN 123-45-6789, lives at 123 Main St"
results = analyzer.analyze(text=text, language="en")
anonymized = anonymizer.anonymize(
    text=text,
    analyzer_results=results,
    operators={"DEFAULT": OperatorConfig("replace", {"new_value": "[REDACTED]"})},
)
# Output: "[REDACTED], SSN [REDACTED], lives at [REDACTED]"
```

### Test Isolation Strategies

```python
# Transaction rollback (fastest — no cleanup needed)
@pytest.fixture
def isolated_session(db_engine):
    conn = db_engine.connect()
    txn = conn.begin()
    session = Session(bind=conn)
    yield session
    session.close()
    txn.rollback()  # all changes discarded
    conn.close()

# Testcontainers (real database in Docker)
import pytest
from testcontainers.postgres import PostgresContainer

@pytest.fixture(scope="session")
def postgres():
    with PostgresContainer("postgres:16") as pg:
        yield pg

@pytest.fixture
def db(postgres):
    engine = create_engine(postgres.get_connection_url())
    Base.metadata.create_all(engine)
    yield Session(engine)
    engine.dispose()
```

## Step 34: Property-Based Testing Expanded

### Hypothesis Strategies

```python
from hypothesis import given, settings, assume
from hypothesis import strategies as st

# Built-in strategies
@given(
    x=st.integers(min_value=-1000, max_value=1000),
    y=st.integers(min_value=0),
    text=st.text(alphabet=st.characters(whitelist_categories=('L', 'N')), min_size=1, max_size=50),
    items=st.lists(st.integers(), min_size=1, max_size=100, unique=True),
)
def test_operations(x, y, text, items):
    assume(y != 0)  # skip cases where y=0
    result = divide(x, y)
    assert result * y == x or abs(result * y - x) < 0.001

# from_regex — generate strings matching a pattern
@given(email=st.from_regex(r"[a-z]{3,10}@[a-z]{3,8}\.(com|org)", fullmatch=True))
def test_email_parser(email):
    parsed = parse_email(email)
    assert parsed.is_valid

# builds — construct objects from strategies
@given(st.builds(
    User,
    name=st.text(min_size=1, max_size=50),
    age=st.integers(min_value=0, max_value=150),
))
def test_user_serialization(user):
    data = user.to_json()
    restored = User.from_json(data)
    assert user == restored

# deferred — recursive/self-referential strategies
tree_strategy = st.deferred(lambda: st.one_of(
    st.integers(),                              # leaf node
    st.tuples(tree_strategy, tree_strategy),    # branch node
))
```

### Shrinking Behavior

Hypothesis automatically shrinks failing examples to minimal reproducing cases:
```python
@given(st.lists(st.integers()))
def test_sort_idempotent(xs):
    assert sorted(sorted(xs)) == sorted(xs)
    # If fails with [5, 3, 1], Hypothesis shrinks to minimal case automatically
```

Control shrinking:
```python
from hypothesis import reject, assume

@given(st.integers())
@settings(suppress_health_check=list())  # don't suppress
def test_positive(x):
    if x <= 0:
        reject()  # explicit reject — Hypothesis won't try smaller values
    assert x > 0
```

### Stateful Testing

```python
from hypothesis.stateful import RuleBasedStateMachine, rule, invariant, initialize
from hypothesis import strategies as st

class LinkedListMachine(RuleBasedStateMachine):
    @initialize()
    def init_list(self):
        self.list = LinkedList()
        self.contents = []

    @rule(value=st.integers())
    def append(self, value):
        self.list.append(value)
        self.contents.append(value)

    @rule(index=st.integers(min_value=0))
    def get(self, index):
        assume(index < len(self.contents))
        assert self.list.get(index) == self.contents[index]

    @rule(index=st.integers(min_value=0))
    def remove(self, index):
        assume(index < len(self.contents))
        self.list.remove(index)
        del self.contents[index]

    @invariant()
    def length_matches(self):
        assert len(self.list) == len(self.contents)

# Generate and run state machine tests
TestLinkedList = LinkedListMachine.TestCase
TestLinkedList.settings = settings(max_examples=200, stateful_step_count=50)
```

### Ghostwriter — Auto-generate Tests

```bash
# Generate tests for a module automatically
hypothesis write app.calculator
# Output: property-based tests for every function in app.calculator

# Generate for specific function
hypothesis write app.calculator:divide
# Generates:
#   @given(a=..., b=...)
#   def test_fuzz_divide(a, b): ...
```

### fast-check (JavaScript) Comparison

```typescript
import fc from 'fast-check';

// Basic property
fc.assert(
  fc.property(
    fc.array(fc.integer()),
    (arr) => {
      const sorted = [...arr].sort((a, b) => a - b);
      return sorted.length === arr.length;
    }
  )
);

// With explicit examples + shrinking
fc.assert(
  fc.property(
    fc.string({ minLength: 1 }),
    fc.integer({ min: 0 }),
    (str, n) => str.slice(n).length <= str.length
  ),
  { numRuns: 1000, verbose: true }  // log all cases
);
```

| Feature | Hypothesis (Python) | fast-check (JS/TS) |
|---------|--------------------|--------------------|
| Shrinking | Automatic, very aggressive | Automatic, configurable |
| Stateful testing | `RuleBasedStateMachine` | `@fast-check/ava` model-based |
| Strategy composition | Excellent, composable | Good, `fc.oneof`, `fc.record` |
| Auto-generation | `hypothesis write` ghostwriter | None built-in |
| CI integration | pytest plugin | Jest/AVA/Vitest plugin |
| Performance | Python — slower | JS engine — faster |

## Step 35: Netflix Testing Culture

### Philosophy: Test in Production

Netflix pioneered "test in production" — the belief that staging environments
are fundamentally different from production. Instead of maintaining fragile
mirror environments, Netflix builds systems that are resilient in production
itself.

**Core principles:**
- Confidence comes from production observability, not staging validation
- Staging is useful for developer velocity, not production confidence
- Production is the only environment that matters
- Design for failure: if it can fail, it will fail

### Chaos Engineering Principles

Source: https://principlesofchaos.org/

```markdown
Chaos Engineering: discipline of experimenting on a system to build confidence
in its ability to withstand turbulent conditions in production.

Steps:
1. Define "steady state" — measurable output (latency, error rate, throughput)
2. Hypothesize: steady state continues during experiment
3. Introduce real-world events: server crash, disk full, network partition
4. Look for deviation from steady state
5. Fix or document the gap
```

### Simian Army

Netflix created the **Simian Army** — automated tools that inject failures:

| Monkey | What it does |
|--------|-------------|
| **Chaos Monkey** | Randomly kills production instances during business hours |
| **Chaos Kong** | Simulates entire AWS region failure |
| **Latency Monkey** | Injects network delays between services |
| **Conformity Monkey** | Finds instances not following best practices |
| **Security Monkey** | Finds security violations (open ports, SG changes) |
| **Doctor Monkey** | Detects unhealthy instances, removes from service |
| **Janitor Monkey** | Finds unused resources, cleans up waste |
| **10-18 Monkey** | Detects localized problems (specific time/region) |

Modern successor: **Chaos Monkey for Spring Boot** and **Litmus Chaos** (K8s).

### Chaos Kong Execution

```yaml
# Chaos Kong: simulate region failure
experiment:
  name: "chaos-kong-us-east-1"
  type: "region-failure"
  target:
    cloud: aws
    region: us-east-1
  steady_state:
    - metric: "availability"
      threshold: ">99.9%"
    - metric: "p99_latency"
      threshold: "<500ms"
  actions:
    - type: "dns-failover"
      config:
        origin: "us-east-1"
        failover_to: "us-west-2"
    - type: "traffic-shift"
      config:
        from: "us-east-1"
        to: "us-west-2"
        duration: "30m"
  rollback:
    automatic: true
    conditions:
      - "availability < 99.0%"
      - "p99_latency > 2000ms"
```

### Game Day Format

Netflix runs structured **Game Day** exercises to practice incident response:

```markdown
## Game Day Template

### Pre-Game (1 week before)
- [ ] Define scope: blast radius, duration, rollback plan
- [ ] Identify participants: on-call, observers, scribes
- [ ] Prepare runbooks and rollback procedures
- [ ] Notify stakeholders (NO notification to on-call — surprise element)

### Game Day Script (2-4 hours)
[00:00] Injection begins
  - Inject failure via Chaos Monkey / manual trigger
  - Start timer

[00:02] Detection phase
  - Monitor: does alerting fire?
  - Measure: time from injection to alert

[00:10] Triage phase
  - On-call responds to alert
  - Measure: time from alert to diagnosis
  - Scribe documents: what was tried, what worked

[00:30] Mitigation phase
  - Apply fix / mitigation
  - Measure: time from diagnosis to mitigation

[01:00] Recovery verification
  - Confirm steady state restored
  - Run smoke tests

[02:00] Blameless Retrospective
  - What went well?
  - What surprised us?
  - What do we need to fix?
  - Action items with owners and deadlines

### Metrics to Track
| Metric | Target |
|--------|--------|
| MTTD (Mean Time to Detect) | < 5 min |
| MTTR (Mean Time to Resolve) | < 30 min |
| Alert accuracy | > 95% (no false positives) |
| Runbook coverage | 100% of critical services |
```

### Netflix Testing in CI/CD Pipeline

```python
# Netflix-style: test chaos in CI before going to production
import pytest
from chaos_engineering import inject_fault, steady_state_check

class TestPaymentServiceResilience:
    """Run chaos experiments as part of CI pipeline."""

    @pytest.fixture
    def payment_service(self):
        svc = PaymentService()
        svc.start()
        yield svc
        svc.stop()

    def test_survives_database_connection_loss(self, payment_service):
        """Service should degrade gracefully when DB is unavailable."""
        # Measure steady state
        baseline = steady_state_check(
            payment_service,
            metrics=["success_rate", "latency_p99"],
            duration_seconds=30
        )
        assert baseline["success_rate"] > 0.99

        # Inject fault
        with inject_fault("database", "connection_refused", duration="60s"):
            degraded = steady_state_check(
                payment_service,
                metrics=["success_rate", "latency_p99"],
                duration_seconds=30
            )
            # Service should use fallback/cache, not crash
            assert degraded["success_rate"] > 0.90
            assert payment_service.health_status != "dead"

        # Verify recovery
        recovered = steady_state_check(
            payment_service,
            metrics=["success_rate", "latency_p99"],
            duration_seconds=30
        )
        assert recovered["success_rate"] > 0.99
```

---

## Step 36: Google Testing Culture

Source: https://testing.googleblog.com/
Book: "Software Engineering at Google" (O'Reilly, 2020)

### Test Size Classification

Google classifies all tests by **size** (not by test pyramid layer):

| Size | Wall time | Resources | Network | I/O | Sleep/lock |
|------|-----------|-----------|---------|-----|------------|
| **Small** | < 1 min | 1 process, 1 machine | No | No | No |
| **Medium** | < 5 min | 1 machine, localhost OK | Localhost only | Allowed | Allowed |
| **Large** | Unlimited | Multiple machines | Real network | Allowed | Allowed |

```python
# Google test size annotations (pytest equivalent)
import pytest

@pytest.mark.small  # Pure logic, no I/O, no network
def test_calculate_tax():
    assert calculate_tax(100, 0.08) == 8.0

@pytest.mark.medium  # May use localhost DB, file I/O
def test_save_user(tmp_path):
    db = Database(f"sqlite:///{tmp_path}/test.db")
    db.create_user("alice")
    assert db.get_user("alice").name == "alice"

@pytest.mark.large  # Full integration, real network, slow
def test_end_to_end_checkout(live_api, payment_gateway):
    response = live_api.post("/checkout", items=[...])
    assert response.status_code == 200
    assert payment_gateway.last_charge.amount == 100.0
```

### TAP (Test Automation Platform)

Google's **TAP** is the central test execution system:

```markdown
TAP Architecture:
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│  Developer   │───>│  TAP Service │───>│  Test Farm  │
│  (submit)    │    │  (scheduler) │    │ (executors) │
└─────────────┘    └──────────────┘    └─────────────┘
        │                  │                   │
        v                  v                   v
   Presubmit          Test results         Artifacts
   (must pass)        (CQ dashboard)       (logs, traces)

TAP Responsibilities:
- Schedules test execution across distributed fleet
- Aggregates results across all test shards
- Tracks flakiness metrics per test
- Enforces presubmit blocking rules
```

### Presubmit vs Post-Submit

```markdown
## Presubmit (before merge)
- Runs SMALL tests (must be < 1 minute total)
- Runs MEDIUM tests for changed components
- LARGES tests run as advisory (not blocking)
- ALL presubmit tests must pass for merge

## Post-Submit (after merge)
- Runs full test suite including LARGE tests
- Results on CQ (Continuous Quality) dashboard
- Failures create bugs automatically (owner attribution)
- Rollback if post-submit fails (broken build policy)

## Implementation with Bazel
```bash
# Presubmit: run only affected small/medium tests
bazel test --test_tag_filters=small,medium --test_timeout=300 //...

# Post-submit: run everything
bazel test --test_tag_filters=small,medium,large //...

# Sharded execution for speed
bazel test --test_sharding_strategy=disabled //path/to/tests:all
```

### Hermetic Tests

**Hermetic tests** are deterministic and self-contained:

```python
# ❌ NON-HERMETIC: depends on external state
def test_current_weather():
    response = requests.get("https://api.weather.com/current")
    assert response.status_code == 200  # Fails offline, varies by time

# ✅ HERMETIC: all dependencies under test control
def test_weather_processing():
    # Use recorded fixture, not live API
    with open("fixtures/weather_response.json") as f:
        weather_data = json.load(f)

    result = process_weather(weather_data)
    assert result["temperature_f"] == 72
    assert result["conditions"] == "sunny"
```

**Hermeticity checklist:**
- No dependence on external network services (use stubs/fakes)
- No dependence on current time (inject clock)
- No dependence on filesystem outside test sandbox
- No dependence on environment variables not in test config
- No shared mutable state between tests
- Tests can run in any order, any parallelism

### Flakiness Policy

Google's strict flakiness policy:

```markdown
## Flaky Test Policy

Definition: A test is flaky if it passes on one run and fails on the next
with NO code changes.

### Detection
- TAP tracks pass/fail history per test across all CLs
- Threshold: >1% flake rate over last 1000 runs = "flaky"
- Automatic flagging in CQ dashboard

### Consequences
1. Flaky test is removed from presubmit (reduces confidence)
2. Bug filed automatically, assigned to last modifier
3. Owner has 2 weeks to fix or delete
4. If unfixed after 2 weeks → test is deleted automatically

### Flakiness Budget
- Team gets 2% flakiness budget per quarter
- Exceeding budget → all presubmit tests blocked until fixed
- Drives teams to invest in test quality

### Anti-Flakiness Patterns
```python
# ❌ BAD: depends on timing
def test_cache_expiry():
    cache.set("key", "value", ttl=1)
    sleep(1.1)  # Flaky! Race condition on slow CI
    assert cache.get("key") is None

# ✅ GOOD: inject time dependency
def test_cache_expiry():
    clock = FakeClock()
    cache = Cache(clock=clock)
    cache.set("key", "value", ttl=1)
    clock.advance(seconds=1.1)
    assert cache.get("key") is None
```
```

### Testing on the Toilet (TotT)

Google publishes internal **Testing on the Toilet** — short (1-page) articles
posted in bathroom stalls to spread testing knowledge:

```markdown
## Sample TotT Articles (Examples)

### "Test Behaviors, Not Methods"
Don't write one test per method. Write tests per behavior.

❌ test_getUser(), test_getUser_notFound(), test_getUser_invalidId()
✅ test_returnsUser_whenExists(), test_returns404_whenNotFound()

### "Who Tests the Tests?"
Mutation testing: if you change the production code, does a test fail?
If not, the test is not adding value.

### "Don't Mock What You Don't Own"
Wrap external APIs in your own interface, then mock your wrapper.
If the external API changes, you only update the wrapper.

### "The Practical Test Pyramid"
Small tests: 80% (fast, cheap, many)
Medium tests: 15% (reasonable speed, integration)
Large tests: 5% (slow, expensive, critical paths only)
```

---

## Step 37: Spotify Squad Health Check

Source: https://engineering.atspotify.com/2014/09/16/squad-health-check/

### The 11 Health Indicators

Spotify's Squad Health Check uses **11 indicators** to assess team health:

| # | Indicator | Green (Good) | Yellow (Some Concern) | Red (Not Good) |
|---|-----------|--------------|----------------------|----------------|
| 1 | **Easy to release** | Releasing is simple, push-button | Releasing takes some coordination | Releasing is painful, risky |
| 2 | **Suitable process** | Process fits the work | Process is OK but could improve | Process is heavy, doesn't fit |
| 3 | **Tech quality** | Code is clean, easy to change | Some tech debt, manageable | Tech debt slows everything |
| 4 | **Value** | Delivering high user value | Value is OK, could be better | Not sure we're building the right thing |
| 5 | **Speed** | Fast feedback, quick iterations | Reasonable speed | Slow, waiting on others |
| 6 | **Mission** | Clear purpose, aligned goals | Somewhat clear | Unclear, conflicting priorities |
| 7 | **Fun** | Enjoy working here | It's OK | Not fun, demotivating |
| 8 | **Learning** | Growing skills, exploring | Some learning opportunities | Stagnating |
| 9 | **Support** | Good support from org | Some support | Lack of support, blockers |
| 10 | **Pawns or players** | Team controls their destiny | Some autonomy | Team has no say in decisions |
| 11 | **Health of codebase** | Tests pass, CI green, docs current | Some gaps | Flaky tests, broken CI, no docs |

### Health Check Facilitation

```markdown
## Squad Health Check — Facilitator Guide

### Preparation (15 min)
1. Print one "Health Board" per team (11 rows, 3 columns: green/yellow/red)
2. Prepare sticky notes (3 colors: green, yellow, red)
3. Book 60 minutes per team
4. NO managers in the room (psychological safety)

### Execution (45 min)

[00:00] Introduction (5 min)
  "This is not a performance review. It's a conversation about how we feel.
   There are no right answers. Be honest."

[00:05] Individual Voting (10 min)
  - For each of the 11 indicators:
  - Each person places a sticky in green/yellow/red column
  - Vote simultaneously (no anchoring)

[00:15] Discussion (25 min)
  - For each indicator, look at the spread:
  - All same color → quick consensus, move on
  - Mixed colors → 2 min discussion: "Yellow voter, what concerns you?"
  - Focus on indicators with most red/yellow
  - Don't try to solve everything — identify top 2-3 concerns

[00:40] Action Items (10 min)
  - Pick 1-3 actionable improvements
  - Each gets: owner, deadline, success criteria
  - Document in team wiki / retro tool

### Follow-up
- Run health check every 6-8 weeks
- Track trends over time (is it getting better or worse?)
- Share anonymized trends with leadership (never individual team data)
- Compare across squads — what are green teams doing differently?
```

### Health Check Visualization

```python
# Simple health check tracking script
import json
from datetime import datetime

HEALTH_INDICATORS = [
    "easy_to_release", "suitable_process", "tech_quality",
    "value", "speed", "mission", "fun", "learning",
    "support", "autonomy", "health_of_codebase"
]

def record_health_check(team: str, votes: dict[str, str]) -> dict:
    """Record a squad health check with green/yellow/red votes."""
    score_map = {"green": 2, "yellow": 1, "red": 0}

    result = {
        "team": team,
        "date": datetime.now().isoformat(),
        "scores": {},
        "actions": []
    }

    for indicator in HEALTH_INDICATORS:
        color = votes.get(indicator, "yellow")
        result["scores"][indicator] = {
            "color": color,
            "score": score_map[color]
        }

    # Flag areas needing attention
    red_areas = [k for k, v in result["scores"].items() if v["color"] == "red"]
    yellow_areas = [k for k, v in result["scores"].items() if v["color"] == "yellow"]

    result["priority_focus"] = red_areas[:3]  # Top 3 reds
    result["avg_score"] = sum(v["score"] for v in result["scores"].values()) / len(HEALTH_INDICATORS)

    return result

def compare_teams(checks: list[dict]) -> dict:
    """Compare health across teams to identify patterns."""
    all_teams = {}
    for check in checks:
        team = check["team"]
        all_teams[team] = check["avg_score"]

    # Find what green teams do differently
    sorted_teams = sorted(all_teams.items(), key=lambda x: x[1], reverse=True)
    return {
        "rankings": sorted_teams,
        "healthiest": sorted_teams[0][0],
        "needs_support": sorted_teams[-1][0]
    }
```

---

## Step 38: Observability-Driven Development

Source: Charity Majors (Honeycomb CTO) — https://charity.wtf/

### Philosophy

**Observability-Driven Development (ODD)** argues that instrumentation is not
an afterthought — it's a core part of code quality, as important as tests.

```markdown
Key insight from Charity Majors:
"If you can't understand what your code is doing in production,
 you can't understand it at all. Production is the only place
 where code meets reality."

Traditional testing: "Did I build the thing right?"
Observability: "Is the thing I built doing what I expected in production?"

You need both. Testing catches known unknowns.
Observability catches unknown unknowns.
```

### Wide Structured Events

Instead of metrics (thin counters) or logs (flat strings), use **wide structured
events** — rich, high-cardinality data points that can be queried flexibly.

```python
# ❌ THIN: traditional metrics (low cardinality, hard to debug)
request_counter.inc({"method": "POST", "path": "/api/users", "status": 200})
latency_histogram.observe(0.15, {"method": "POST", "path": "/api/users"})

# ❌ FLAT: traditional logs (parsing nightmares, no structure)
logger.info(f"POST /api/users 200 150ms user_id={user_id}")

# ✅ WIDE STRUCTURED EVENT: one rich event per request
from opentelemetry import trace
import structlog

logger = structlog.get_logger()

def handle_create_user(request):
    span = trace.get_current_span()
    span.set_attributes({
        "user.id": user_id,
        "user.plan": plan,
        "user.region": region,
        "request.idempotency_key": idempotency_key,
        "request.source": "mobile_app",
        "db.query_count": 0,
        "cache.hit": False,
    })

    # ... execute business logic ...

    # Wide event at the END of the request (not scattered log lines)
    logger.info("request.complete",
        method="POST",
        path="/api/users",
        status_code=201,
        duration_ms=duration_ms,
        user_id=user_id,
        user_plan=plan,
        user_region=region,
        idempotency_key=idempotency_key,
        source="mobile_app",
        db_queries=span.get_attribute("db.query_count"),
        cache_hit=span.get_attribute("cache.hit"),
        error_message=None,
        version=get_deploy_version(),
    )
```

### Instrumentation as Code Quality Signal

```markdown
## The Observability Checklist (PR Review)

When reviewing a PR, instrumention quality = code quality:

### Must-have (block merge)
- [ ] New endpoints have a "request.complete" wide event
- [ ] Database queries are traced with query text + duration
- [ ] External service calls are traced with latency + status
- [ ] Error paths emit structured events (not just exceptions)

### Should-have (flag in review)
- [ ] Business-critical values are captured in events (order amount, user plan)
- [ ] Request-scoped context propagates through call stack
- [ ] No PII in events (user email, password, token)
- [ ] High-cardinality fields (user_id, request_id) for debugging

### Nice-to-have (mention)
- [ ] Custom span attributes for domain-specific queries
- [ ] Dashboard/alerting queries documented in PR
- [ ] Trace sampling strategy documented if not default
```

### Observability-Driven Development Workflow

```python
# ODD workflow: instrument BEFORE you test, instrument WITH your tests

def test_new_refund_feature():
    """Test that also verifies observability instrumentation."""
    with capture_telemetry() as telemetry:
        result = process_refund(order_id="ORD-123", amount=50.00, reason="defective")

    # Verify behavior
    assert result.status == "refunded"
    assert result.amount == 50.00

    # Verify observability (instrumentation quality)
    events = telemetry.find_events("request.complete")
    assert len(events) == 1
    event = events[0]
    assert event["refund.order_id"] == "ORD-123"
    assert event["refund.amount"] == 50.00
    assert event["refund.reason"] == "defective"
    assert event["refund.duration_ms"] < 5000
    assert "refund.processing_fee" in event  # Ensure fee is tracked
```

### Production Debugging with Wide Events

```sql
-- Honeycomb-style query: find slow refunds by customer segment
SELECT
  customer_segment,
  COUNT(*) as total_refunds,
  P95(duration_ms) as p95_latency,
  AVG(refund_amount) as avg_amount
FROM request_complete
WHERE path = '/api/refunds'
  AND status_code = 200
  AND start_time > NOW() - INTERVAL '7 days'
GROUP BY customer_segment
ORDER BY p95_latency DESC

-- Debug a specific slow refund
SELECT *
FROM request_complete
WHERE request_id = 'abc-123-def'
-- Wide event gives you EVERYTHING in one query
```

### Charity Majors' Rules for Production

```markdown
## Production Rules (from @mipsytipsy)

1. "If you haven't tested it in production, you haven't tested it."
   → Canaries, synthetic checks, real-user monitoring are mandatory.

2. "Observability is for understanding NEW code, not just monitoring old code."
   → Every deploy should be instrumented for understanding behavior.

3. "The first question is 'what changed?' — deploy frequency answers this."
   → Small, frequent deploys = easier to correlate cause and effect.

4. "Dashboards are for known problems. Explore/debug tools are for unknowns."
   → Invest in tools that let you ask questions you didn't anticipate.

5. "If you can't find out what your code is doing in production,
    your code is not ready to ship."
   → Add observability before merging, not after a production incident.

## Step 39: GraphQL Testing

### Query-Level Snapshot Testing

Capture GraphQL query results as snapshots to detect unexpected response shape changes.

```typescript
// Jest snapshot testing for GraphQL queries
import { renderHook } from '@testing-library/react-hooks';
import { MockedProvider } from '@apollo/client/testing';
import { useUserQuery } from './useUserQuery';

const mocks = [
  {
    request: { query: GET_USER, variables: { id: '1' } },
    result: {
      data: {
        user: { id: '1', name: 'Alice', email: 'alice@example.com', posts: [{ id: 'p1', title: 'Hello' }] },
      },
    },
  },
];

test('GET_USER query returns expected shape', async () => {
  const { result, waitForNextUpdate } = renderHook(() => useUserQuery('1'), {
    wrapper: ({ children }) => <MockedProvider mocks={mocks}>{children}</MockedProvider>,
  });
  await waitForNextUpdate();
  expect(result.current.data).toMatchSnapshot(); // snapshot guards response shape
});
```

```typescript
// Vitest — inline snapshot for query responses
import { executeQuery } from '@apollo/client/testing';

test('user query shape', async () => {
  const result = await executeQuery(GET_USER, { id: '1' }, { mocks });
  expect(result.data).toMatchInlineSnapshot(`
    Object {
      "user": Object {
        "id": "1",
        "name": "Alice",
        "email": "alice@example.com",
      },
    }
  `);
});
```

### Schema Linting (graphql-eslint)

Enforce naming conventions, require descriptions, ban deprecated fields in new code.

```yaml
# .eslintrc.yml — graphql-eslint configuration
extends:
  - plugin:@graphql-eslint/schema-all
overrides:
  - files: ['*.graphql']
    parser: '@graphql-eslint/graphql-parser'
    rules:
      '@graphql-eslint/naming-convention':
        - error
        - FieldDefinition: camelCase
          ObjectTypeDefinition: PascalCase
          EnumValueDefinition: UPPER_CASE
          InputValueDefinition: camelCase
      '@graphql-eslint/description-style': error
      '@graphql-eslint/relay-arguments': error
      '@graphql-eslint/no-deprecated': warn
      '@graphql-eslint/require-deprecation-reason': error
      '@graphql-eslint/unique-field-names': error
      '@graphql-eslint/no-hashtag-description': error
```

```bash
# Run schema linting
npx eslint --ext .graphql schema/

# CI integration
npx @graphql-eslint/eslint-plugin 'src/**/*.graphql' --format json > graphql-lint-report.json
```

### Query Complexity Analysis (graphql-query-complexity)

Prevent expensive queries from DoS-ing your GraphQL server.

```typescript
// Server-side complexity limits
import { getComplexity, simpleEstimator, fieldExtensionsEstimator } from 'graphql-query-complexity';
import { ApolloServer } from '@apollo/server';

const server = new ApolloServer({
  schema,
  plugins: [
    {
      requestDidStart: () => ({
        didResolveOperation({ request, document }) {
          const complexity = getComplexity({
            schema,
            operationName: request.operationName,
            query: document,
            variables: request.variables,
            estimators: [
              fieldExtensionsEstimator(),
              simpleEstimator({ defaultComplexity: 1 }),
            ],
          });
          if (complexity > 1000) {
            throw new Error(`Query too complex: ${complexity}. Max allowed: 1000`);
          }
          console.log('Query complexity:', complexity);
        },
      }),
    },
  ],
});
```

```typescript
// Field-level complexity hints in schema
const typeDefs = `#graphql
  type User {
    id: ID!
    name: String
    posts: [Post!]! @complexity(value: 5, multipliers: ["first"])
    friends(first: Int): [User!]! @complexity(value: 3, multipliers: ["first"])
  }

  type Query {
    users(first: Int): [User!]! @complexity(value: 3, multipliers: ["first"])
    search(query: String!): [Result!]! @complexity(value: 10)
  }
`;
```

### Apollo MockedProvider

```typescript
// Full component test with Apollo MockedProvider
import { MockedProvider } from '@apollo/client/testing';
import { render, screen, waitFor } from '@testing-library/react';
import { UserProfile } from './UserProfile';
import { GET_USER, UPDATE_USER } from './queries';

const mocks = [
  {
    request: { query: GET_USER, variables: { id: '1' } },
    result: { data: { user: { id: '1', name: 'Alice', role: 'admin' } } },
  },
  {
    request: { query: UPDATE_USER, variables: { id: '1', name: 'Bob' } },
    result: { data: { updateUser: { id: '1', name: 'Bob', role: 'admin' } } },
  },
];

test('renders user and allows name update', async () => {
  render(
    <MockedProvider mocks={mocks} addTypename={false}>
      <UserProfile userId="1" />
    </MockedProvider>
  );

  // Wait for loading to finish
  await waitFor(() => expect(screen.getByText('Alice')).toBeInTheDocument());

  // Simulate update
  fireEvent.click(screen.getByText('Edit'));
  fireEvent.change(screen.getByLabelText('Name'), { target: { value: 'Bob' } });
  fireEvent.click(screen.getByText('Save'));

  await waitFor(() => expect(screen.getByText('Bob')).toBeInTheDocument());
});

// Error state testing
const errorMocks = [
  {
    request: { query: GET_USER, variables: { id: '999' } },
    error: new Error('User not found'),
  },
];

test('shows error state', async () => {
  render(
    <MockedProvider mocks={errorMocks} addTypename={false}>
      <UserProfile userId="999" />
    </MockedProvider>
  );
  await waitFor(() => expect(screen.getByText(/error/i)).toBeInTheDocument());
});
```

### Persisted Queries Allowlist

Lock down production GraphQL to only allow pre-registered queries.

```typescript
// Automatic Persisted Queries (APQ) with allowlist
import { ApolloServer } from '@apollo/server';
import { ApolloServerPluginAPQ } from '@apollo/server/plugin/apq';
import { createHash } from 'crypto';

// Pre-compute query hashes at build time
const ALLOWED_QUERIES = new Map<string, string>();

async function loadAllowlist() {
  const queries = await import('./persisted-queries.json');
  for (const [hash, query] of Object.entries(queries)) {
    ALLOWED_QUERIES.set(hash, query as string);
  }
}

const server = new ApolloServer({
  schema,
  plugins: [
    {
      async requestDidStart({ request }) {
        if (request.extensions?.persistedQuery) {
          const { sha256Hash } = request.extensions.persistedQuery;
          if (!ALLOWED_QUERIES.has(sha256Hash)) {
            throw new Error(`Query not in allowlist: ${sha256Hash}`);
          }
        }
      },
    },
  ],
});
```

```bash
# Generate persisted query manifest at build time
npx apollo client:codegen \
  --queries='./src/**/*.graphql' \
  --persistedQueries \
  --outputPersistedQueries=persisted-queries.json

# CI gate: verify all queries are in allowlist
npx graphql-allowlist check \
  --schema schema.graphql \
  --queries 'src/**/*.graphql' \
  --allowlist persisted-queries.json
```

**GraphQL testing checklist:**
- [ ] Schema linted with graphql-eslint (naming, descriptions, deprecation)
- [ ] Query complexity limits enforced (prevent DoS)
- [ ] Snapshot tests for query response shapes
- [ ] MockedProvider tests for all UI components using GraphQL
- [ ] Error/loading states tested with mock errors
- [ ] Persisted queries allowlist for production
- [ ] Subscription tests with `graphql-ws` test client
- [ ] Schema breaking change detection in CI (graphql-inspector)

## Step 40: gRPC Testing

### grpcurl — CLI Testing

Test gRPC services from the command line without writing code.

```bash
# List all services on a gRPC server
grpcurl -plaintext localhost:50051 list

# Describe a service
grpcurl -plaintext localhost:50051 describe mypackage.UserService

# Describe a message type
grpcurl -plaintext localhost:50051 describe mypackage.User

# Call a unary RPC
grpcurl -plaintext -d '{"id": "123"}' \
  localhost:50051 mypackage.UserService/GetUser

# Call with metadata (auth headers)
grpcurl -plaintext \
  -H "authorization: Bearer eyJhbG..." \
  -d '{"name": "Alice"}' \
  localhost:50051 mypackage.UserService/CreateUser

# Call a server-streaming RPC
grpcurl -plaintext -d '{"user_id": "123"}' \
  localhost:50051 mypackage.NotificationService/StreamNotifications

# Call with TLS
grpcurl -cacert /path/to/ca.crt \
  -cert /path/to/client.crt \
  -key /path/to/client.key \
  -d '{"id": "123"}' \
  grpc.example.com:443 mypackage.UserService/GetUser

# Format output as JSON (default) or use specific formatters
grpcurl -plaintext -format json \
  -d '{}' localhost:50051 mypackage.UserService/ListUsers

# Reflection-based: no proto files needed if server has reflection enabled
grpcurl -plaintext localhost:50051 list
grpcurl -plaintext localhost:50051 describe mypackage.UserService.GetUser
```

### ghz — Load Testing

Benchmark gRPC services with configurable load patterns.

```bash
# Basic load test — 100 requests total
ghz --insecure \
  --proto ./proto/user.proto \
  --call mypackage.UserService/GetUser \
  -d '{"id": "123"}' \
  -n 100 \
  localhost:50051

# Sustained load — 50 concurrent for 30 seconds
ghz --insecure \
  --proto ./proto/user.proto \
  --call mypackage.UserService/GetUser \
  -d '{"id": "{{randomString 10}}"}' \
  -c 50 \
  -z 30s \
  localhost:50051

# With metadata and TLS
ghz --proto ./proto/user.proto \
  --call mypackage.UserService/GetUser \
  -d '{"id": "123"}' \
  -M '{"authorization": "Bearer test-token"}' \
  --cacert ./certs/ca.crt \
  -c 100 \
  -n 10000 \
  grpc.example.com:443

# JSON report output
ghz --insecure \
  --proto ./proto/user.proto \
  --call mypackage.UserService/GetUser \
  -d '{"id": "123"}' \
  -n 1000 \
  -o report.json \
  -O json \
  localhost:50051

# Use template data for varied requests
ghz --insecure \
  --proto ./proto/user.proto \
  --call mypackage.UserService/GetUser \
  -D ./test-data.json \
  --load-step=10 \
  --load-step-duration=5s \
  localhost:50051
```

**ghz output metrics:**
```
Summary:
  Count:        1000
  Total:        5.23 s
  Slowest:      89.12 ms
  Fastest:      1.23 ms
  Average:      12.45 ms
  Requests/sec: 191.20

Response time histogram:
  1.230  [1]    |
  10.019 [754]  |∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎∎
  ...

Latency distribution:
  10 % in 5.12 ms
  25 % in 7.34 ms
  50 % in 11.23 ms
  75 % in 16.45 ms
  90 % in 22.67 ms
  99 % in 45.89 ms

Status code distribution:
  [OK]   1000 responses
```

### buf — Proto Contract Testing

Detect breaking changes in protobuf definitions before they ship.

```yaml
# buf.yaml — repository configuration
version: v2
modules:
  - path: proto
deps:
  - buf.build/googleapis/googleapis
lint:
  use:
    - STANDARD
  except:
    - FIELD_NOT_REQUIRED
    - PACKAGE_DIRECTORY_MATCH
  enum_zero_value_suffix: _UNSPECIFIED
  service_suffix: Service
breaking:
  use:
    - FILE
  ignore_unstable_packages: true
```

```bash
# Lint proto files
buf lint

# Check for breaking changes against main branch
buf breaking --against '.git#branch=main'

# Check against a specific commit
buf breaking --against '.git#ref=abc1234'

# Check against a remote BSR (Buf Schema Registry) module
buf breaking --against 'buf.build/myorg/mypackage'

# Generate breaking change report in JSON
buf breaking --against '.git#branch=main' --error-format json

# Generate code from protos
buf generate

# Format proto files
buf format -w
```

```yaml
# buf.gen.yaml — code generation config
version: v2
plugins:
  - remote: buf.build/protocolbuffers/go
    out: gen/go
    opt: paths=source_relative
  - remote: buf.build/connectrpc/go
    out: gen/go
    opt: paths=source_relative
  - remote: buf.build/grpc/go
    out: gen/go
    opt: paths=source_relative
  - remote: buf.build/protocolbuffers/python
    out: gen/python
  - remote: buf.build/grpc/python
    out: gen/python
```

```yaml
# GitHub Actions: proto contract testing
name: Proto Contract Check
on:
  pull_request:
    paths: ['proto/**']

jobs:
  buf-checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: bufbuild/buf-setup-action@v1
      - uses: bufbuild/buf-lint-action@v1
      - uses: bufbuild/buf-breaking-action@v1
        with:
          against: 'https://github.com/${{ github.repository }}.git#branch=main,ref=HEAD~1'
```

### Server Reflection Testing

```bash
# Test server reflection is working
grpcurl -plaintext localhost:50051 list
# Expected: grpc.health.v1.Health
#           grpc.reflection.v1alpha.ServerReflection
#           mypackage.UserService
#           mypackage.OrderService

# Verify specific service methods are exposed
grpcurl -plaintext localhost:50051 describe mypackage.UserService
# Should list: GetUser, CreateUser, ListUsers, UpdateUser, DeleteUser

# Verify method signatures
grpcurl -plaintext localhost:50051 describe mypackage.UserService.GetUser
# Expected:
#   mypackage.UserService.GetUser is a method:
#   rpc GetUser ( .mypackage.GetUserRequest ) returns ( .mypackage.User );

# Test health check
grpcurl -plaintext localhost:50051 grpc.health.v1.Health/Check
# Expected: {"status": "SERVING"}
```

```python
# Python — reflection-based dynamic testing
import grpc
from grpc_reflection.v1alpha import reflection
from grpc_health.v1 import health_pb2, health_pb2_grpc

def test_grpc_reflection_enabled(channel):
    """Verify server reflection is active."""
    stub = reflection.ReflectionServiceStub(channel)
    services = stub.ServerReflectionInfo(
        iter([reflection.ServerReflectionRequest(list_services='')])
    )
    service_names = [s.name for s in next(services).list_services_response.service]
    assert 'mypackage.UserService' in service_names
    assert 'grpc.health.v1.Health' in service_names

def test_grpc_health_check(channel):
    """Verify health check returns SERVING."""
    stub = health_pb2_grpc.HealthStub(channel)
    response = stub.Check(health_pb2.HealthCheckRequest())
    assert response.status == health_pb2.HealthCheckResponse.SERVING
```

### Service Mesh Integration Testing

Test gRPC services through Istio/Linkerd service mesh with mTLS.

```yaml
# Kubernetes Job: gRPC integration test through mesh
apiVersion: batch/v1
kind: Job
metadata:
  name: grpc-integration-test
  namespace: test
spec:
  template:
    metadata:
      annotations:
        sidecar.istio.io/inject: "true"  # inject mesh sidecar
    spec:
      serviceAccountName: test-runner
      containers:
        - name: grpc-test
          image: bufbuild/ghz:latest
          command:
            - ghz
            - --proto
            - /proto/user.proto
            - --call
            - mypackage.UserService/GetUser
            - -d
            - '{"id": "123"}'
            - -n
            - "100"
            - -c
            - "10"
            - --cacert
            - /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
            - user-service.test.svc.cluster.local:50051
          volumeMounts:
            - name: proto-files
              mountPath: /proto
      volumes:
        - name: proto-files
          configMap:
            name: proto-definitions
      restartPolicy: Never
```

```yaml
# Istio PeerAuthentication for mTLS testing
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: grpc-mtls
  namespace: test
spec:
  mtls:
    mode: STRICT
```

```go
// Go — test gRPC through service mesh with mTLS
func TestGRPCThroughMesh(t *testing.T) {
    // Load mesh-provided certs
    creds, err := credentials.NewClientTLSFromFile(
        "/var/run/secrets/kubernetes.io/serviceaccount/ca.crt", "")
    require.NoError(t, err)

    conn, err := grpc.Dial(
        "user-service.test.svc.cluster.local:50051",
        grpc.WithTransportCredentials(creds),
    )
    require.NoError(t, err)
    defer conn.Close()

    client := pb.NewUserServiceClient(conn)
    resp, err := client.GetUser(context.Background(), &pb.GetUserRequest{Id: "123"})
    require.NoError(t, err)
    assert.Equal(t, "123", resp.Id)
}
```

**gRPC testing checklist:**
- [ ] grpcurl smoke tests for all RPCs (unary, streaming, bidirectional)
- [ ] ghz load tests with latency percentiles and error rates
- [ ] buf lint + buf breaking in CI proto pipeline
- [ ] Server reflection verified (list, describe, health check)
- [ ] Service mesh mTLS integration tests
- [ ] Timeout and deadline propagation tested
- [ ] Error code mapping (gRPC status codes ↔ HTTP) verified
- [ ] Load balancing behavior tested (round-robin, consistent hash)

## Step 41: Test Data Management — Expanded

### Synthetic Data Generation

Generate realistic, privacy-safe test data at scale.

```javascript
// Faker.js — comprehensive synthetic data generation
import { faker } from '@faker-js/faker';
import { fakerDE, fakerJA, fakerZH_CN } from '@faker-js/faker';

// Generate complete user profiles
function generateUser(overrides = {}) {
  const sex = faker.person.sexType();
  const firstName = faker.person.firstName(sex);
  const lastName = faker.person.lastName();

  return {
    id: faker.string.uuid(),
    email: faker.internet.email({ firstName, lastName }),
    username: faker.internet.username({ firstName, lastName }),
    avatar: faker.image.avatar(),
    firstName,
    lastName,
    sex,
    phone: faker.phone.number({ style: 'national' }),
    address: {
      street: faker.location.streetAddress(),
      city: faker.location.city(),
      state: faker.location.state({ abbreviated: true }),
      zipCode: faker.location.zipCode(),
      country: faker.location.countryCode(),
    },
    creditCard: {
      number: faker.finance.creditCardNumber(),
      cvv: faker.finance.creditCardCVV(),
      issuer: faker.finance.creditCardIssuer(),
    },
    company: faker.company.name(),
    jobTitle: faker.person.jobTitle(),
    bio: faker.person.bio(),
    createdAt: faker.date.past({ years: 2 }),
    ...overrides,
  };
}

// Generate related data with referential integrity
function generateDataset(count = 1000) {
  const users = Array.from({ length: count }, () => generateUser());
  const orders = users.flatMap(user =>
    Array.from({ length: faker.number.int({ min: 0, max: 10 }) }, () => ({
      id: faker.string.uuid(),
      userId: user.id,
      product: faker.commerce.productName(),
      amount: parseFloat(faker.commerce.price({ min: 5, max: 500 })),
      status: faker.helpers.arrayElement(['pending', 'shipped', 'delivered', 'returned']),
      createdAt: faker.date.between({ from: user.createdAt, to: new Date() }),
    }))
  );
  return { users, orders };
}
```

```python
import sys
# Synthea — synthetic patient healthcare data
# Generates realistic (but not real) FHIR patient records
# https://github.com/synthetichealth/synthea
#
# java -jar synthea-with-dependencies.jar -p 1000 -s 42
# Generates 1000 synthetic patients with seeded randomness
#
# Output: FHIR bundles, CSV, C-CDA formats
# Includes: demographics, conditions, medications, encounters, procedures
#
# Use for: HIPAA-compliant testing without real PHI
# Limitations: US-centric, limited rare disease coverage

# Gretel.ai — synthetic data with differential privacy
# https://gretel.ai/
#
# gretel-synthetics: train model on sensitive data, generate synthetic copies
#
# from gretel_synthetics.column_encoders import *
# from gretel_synthetics.config import LocalConfig
# from gretel_synthetics.generate import generate_text
#
# config = LocalConfig(
#     max_line_len=2048,
#     epochs=15,
#     field_delimiter=",",
#     dp=True,  # differential privacy enabled
#     gen_lines=1000,
# )
# train(df=config, checkpoint_dir=".checkpoints")
# records = list(generate_text(config, checkpoint_dir=".checkpoints"))
#
# Key: privacy-preserving — synthetic data cannot be reverse-engineered to real data

# Tonic.ai — data synthesis for staging/test environments
# https://tonic.ai/
#
# Features: subsetting (smaller copies of prod), referential integrity,
# conditional generation, schema-aware synthesis
# Integrates with: Postgres, MySQL, SQL Server, MongoDB, Snowflake, BigQuery
# Use for: staging environment data that mirrors prod shape without prod PII
```

### Data Masking

**Static vs Dynamic masking:**
```
Static Masking (persistent):
  prod DB → masked copy → test DB (permanently masked)
  Pros: simple, no runtime overhead, one-time cost
  Cons: stale data, large storage, no real-time updates
  Tools: Delphix, SQL Data Masker, DataVeil

Dynamic Masking (on-read):
  prod DB → proxy layer → masked on access → test/consumer
  Pros: always fresh, granular per-role policies, no copies
  Cons: latency, proxy dependency, policy complexity
  Tools: Delphix, Oracle Data Redaction, Snowflake Dynamic Masking
```

```python
import sys
# Differential Privacy — mathematical privacy guarantee
import numpy as np

def laplace_mechanism(true_value, sensitivity, epsilon):
    """Add Laplace noise for differential privacy."""
    scale = sensitivity / epsilon
    noise = np.random.laplace(0, scale)
    return true_value + noise

# Example: count query with DP
true_count = 42  # real count from DB
private_count = laplace_mechanism(true_count, sensitivity=1, epsilon=0.1)
# Higher epsilon = less privacy, more accuracy
# Lower epsilon = more privacy, less accuracy
# Typical: epsilon in [0.1, 10]

# Google's RAPPOR (Randomized Aggregatable Privacy-Preserving Ordinal Response)
# for aggregate statistics without individual data exposure
```

### Test Data as Code

Manage test data declaratively alongside tests.

```xml
<!-- DBUnit — XML dataset for database tests -->
<!-- users-dataset.xml -->
<dataset>
  <users id="1" email="alice@test.com" name="Alice" role="admin"
         created_at="2024-01-15 10:00:00" />
  <users id="2" email="bob@test.com" name="Bob" role="member"
         created_at="2024-02-20 14:30:00" />
  <orders id="101" user_id="1" product="Widget" amount="29.99"
          status="delivered" created_at="2024-03-01 09:00:00" />
</dataset>
```

```java
// DBUnit — Java database testing with datasets
import org.dbunit.IDatabaseTester;
import org.dbunit.dataset.xml.FlatXmlDataSetBuilder;

public class UserRepositoryTest {
    @BeforeEach
    void setUp() throws Exception {
        IDataSet dataSet = new FlatXmlDataSetBuilder()
            .build(getClass().getResourceAsStream("/users-dataset.xml"));
        databaseTester.setDataSet(dataSet);
        databaseTester.onSetup();
    }

    @AfterEach
    void tearDown() throws Exception {
        databaseTester.onTearDown();
    }

    @Test
    void testFindById() {
        User user = repository.findById(1L);
        assertEquals("Alice", user.getName());
        assertEquals("admin", user.getRole());
    }
}
```

```yaml
# Fixtures — YAML-based test data (Rails pattern, portable)
# test/fixtures/users.yml
alice:
  id: 1
  email: alice@test.com
  name: Alice
  role: admin
  created_at: 2024-01-15 10:00:00

bob:
  id: 2
  email: bob@test.com
  name: Bob
  role: member
  created_at: 2024-02-20 14:30:00

# test/fixtures/orders.yml
alice_order:
  id: 101
  user_id: 1  # FK → alice
  product: Widget
  amount: 29.99
  status: delivered
```

```typescript
// Fixtures in TypeScript — custom test data loader
import * as yaml from 'js-yaml';
import { readFileSync } from 'fs';
import { resolve } from 'path';

function loadFixtures<T>(name: string): Record<string, T> {
  const content = readFileSync(
    resolve(__dirname, `fixtures/${name}.yml`), 'utf-8'
  );
  return yaml.load(content) as Record<string, T>;
}

// Usage
const users = loadFixtures<User>('users');
const orders = loadFixtures<Order>('orders');

// Delphix — enterprise test data management
# https://www.delphix.com/
# Features: data virtualization (thin clones), masking, bookmarking,
# branching (git-like for data), self-service provisioning
# Use for: large-scale test environments with prod-like data
# Workflow: prod → Delphix engine → masked virtual copies → test envs
# Storage: virtual copies use ~1/10th space (copy-on-write)
```

### PII Detection

Scan test data for accidentally leaked personally identifiable information.

```python
# Microsoft Presidio — PII detection and anonymization
from presidio_analyzer import AnalyzerEngine
from presidio_anonymizer import AnonymizerEngine
from presidio_anonymizer.entities import OperatorConfig

analyzer = AnalyzerEngine()
anonymizer = AnonymizerEngine()

text = "My name is John Smith, SSN: 123-45-6789, email: john@example.com"

# Detect PII entities
results = analyzer.analyze(
    text=text,
    language='en',
    entities=['PERSON', 'US_SSN', 'EMAIL_ADDRESS', 'PHONE_NUMBER',
              'CREDIT_CARD', 'IP_ADDRESS', 'LOCATION', 'DATE_TIME'],
)

for result in results:
    print(f"Entity: {result.entity_type}, Start: {result.start}, "
          f"End: {result.end}, Score: {result.score:.2f}")
# Output:
# Entity: PERSON, Start: 10, End: 20, Score: 0.85
# Entity: US_SSN, Start: 28, End: 39, Score: 0.85
# Entity: EMAIL_ADDRESS, Start: 48, End: 66, Score: 0.95

# Anonymize detected PII
anonymized = anonymizer.anonymize(
    text=text,
    analyzer_results=results,
    operators={
        'PERSON': OperatorConfig('replace', {'new_value': '<PERSON>'}),
        'US_SSN': OperatorConfig('mask', {'chars_to_mask': 7, 'masking_char': '*'}),
        'EMAIL_ADDRESS': OperatorConfig('replace', {'new_value': '<EMAIL>'}),
    },
)
print(anonymized.text)
# "My name is <PERSON>, SSN: ******789, email: <EMAIL>"

# Presidio in CI — scan test fixtures for PII leaks
def test_no_pii_in_fixtures():
    """Ensure test fixture files contain no real PII."""
    fixture_dir = Path('test/fixtures')
    for fixture_file in fixture_dir.glob('**/*.yml'):
        content = fixture_file.read_text()
        results = analyzer.analyze(text=content, language='en')
        pii_found = [r for r in results if r.score > 0.7]
        assert not pii_found, (
            f"PII detected in {fixture_file}: "
            f"{[(r.entity_type, r.start, r.end) for r in pii_found]}"
        )
```

```bash
# AWS Macie — PII scanning for S3-stored test data
# Enable Macie for S3 bucket containing test datasets
aws macie2 create-classification-job \
  --job-type ONE_TIME \
  --s3-job-definition '{
    "bucketDefinitions": [{
      "accountId": "123456789012",
      "buckets": ["test-data-bucket"]
    }]
  }' \
  --name "PII-scan-test-data"

# Check findings
aws macie2 get-findings --finding-criteria '{
  "criterion": {
    "severity.score": { "gte": 3 }
  }
}'
```

```yaml
# CI pipeline: PII detection gate
name: PII Detection
on:
  pull_request:
    paths: ['test/**', 'fixtures/**', 'seeds/**']

jobs:
  pii-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - run: pip install presidio-analyzer presidio-anonymizer
      - name: Scan for PII in test data
        run: |
          python scripts/scan_pii.py \
            --dirs test/fixtures test/seeds \
            --threshold 0.7 \
            --fail-on PERSON,US_SSN,CREDIT_CARD,EMAIL_ADDRESS
```

**Test data management checklist:**
- [ ] Synthetic data generation for all entity types
- [ ] Seeded/random modes (reproducible in CI, varied in dev)
- [ ] Data masking strategy defined (static vs dynamic)
- [ ] PII detection in CI for all fixture/seed files
- [ ] Test data as code (versioned, reviewable, reproducible)
- [ ] Referential integrity maintained across fixtures
- [ ] Data cleanup between tests (transaction rollback, truncate)
- [ ] Differential privacy for aggregate statistics from prod
- [ ] Subsetting strategy for large prod databases

## Step 42: Accessibility Testing — Expanded

### WCAG Automated Tools

Automated tools catch ~30-40% of a11y issues. Manual testing catches the rest.

```typescript
// axe-core — industry standard accessibility engine
import AxeBuilder from '@axe-core/playwright';
import { test, expect } from '@playwright/test';

test('homepage meets WCAG 2.1 AA', async ({ page }) => {
  await page.goto('/');
  const results = await new AxeBuilder({ page })
    .withTags(['wcag2a', 'wcag2aa', 'wcag21aa'])
    .analyze();

  expect(results.violations).toEqual([]);
});

// Test specific component
test('modal dialog is accessible', async ({ page }) => {
  await page.goto('/settings');
  await page.click('button:text("Open Settings")');
  await page.waitForSelector('[role="dialog"]');

  const results = await new AxeBuilder({ page })
    .include('[role="dialog"]')  // scope to modal
    .withTags(['wcag2a', 'wcag2aa', 'best-practice'])
    .analyze();

  expect(results.violations).toEqual([]);
});

// Exclude known third-party widgets
test('page accessible excluding chat widget', async ({ page }) => {
  await page.goto('/');
  const results = await new AxeBuilder({ page })
    .exclude('#intercom-container')  // third-party chat
    .analyze();

  expect(results.violations).toEqual([]);
});
```

```bash
# Lighthouse CI — accessibility score in pipeline
npx lhci autorun \
  --collect.url=http://localhost:3000 \
  --collect.url=http://localhost:3000/dashboard \
  --assert.assertions.categories:accessibility=["error", {"minScore": 0.95}]

# Programmatic Lighthouse
npx lighthouse http://localhost:3000 \
  --only-categories=accessibility \
  --output=json \
  --output-path=./a11y-report.json
```

```bash
# Pa11y — CLI accessibility testing
npx pa11y http://localhost:3000
npx pa11y --standard WCAG2AA http://localhost:3000
npx pa11y --reporter json http://localhost:3000 > pa11y-report.json

# Pa11y CI — test multiple pages
npx pa11y-ci --config .pa11yci.json
```

```json
// .pa11yci.json
{
  "defaults": {
    "standard": "WCAG2AA",
    "timeout": 10000,
    "wait": 1000,
    "hideElements": ".cookie-banner, #intercom-container"
  },
  "urls": [
    "http://localhost:3000",
    "http://localhost:3000/login",
    "http://localhost:3000/dashboard",
    "http://localhost:3000/settings"
  ]
}
```

```bash
# WAVE — Web Accessibility Evaluation (CLI via wave-api or browser extension)
# Browser extension: https://wave.webaim.org/extension/
# API: https://wave.webaim.org/api/

# IBM Equal Access — accessibility checker
# https://github.com/IBMa/equal-access
npx accessibility-checker http://localhost:3000
# Produces detailed report with: violations, needs review, recommendations
# Config: .achecker.yml
```

```yaml
# .achecker.yml — IBM Equal Access config
ruleServer: "https://localhost:9445/rules"
rulePack: "https://localhost:9445/rules/archives/2024.03.12"
policies:
  - IBM_Accessibility
  - WCAG_2_1
reportLevels:
  - violation
  - potentialviolation
  - recommendation
failLevels:
  - violation
```

### Screen Reader Testing

Automated tools cannot replace screen reader testing. Test key user flows with each reader.

```
Screen Reader Matrix:
| Reader      | Platform | Browser           | Market Share |
|-------------|----------|-------------------|--------------|
| NVDA        | Windows  | Firefox, Chrome   | ~40%         |
| JAWS        | Windows  | Chrome, Edge      | ~35%         |
| VoiceOver   | macOS/iOS| Safari            | ~15%         |
| TalkBack    | Android  | Chrome            | ~10%         |

Priority: NVDA+Firefox > VoiceOver+Safari > JAWS+Chrome > TalkBack+Chrome
```

**Manual screen reader test checklist:**
```markdown
## Screen Reader Test Protocol

### Page Load
- [ ] Page title announced correctly
- [ ] Skip-to-main-content link works
- [ ] Landmark regions announced (banner, navigation, main, contentinfo)
- [ ] Number of links/headers/landmarks announced

### Navigation
- [ ] All interactive elements reachable with Tab/arrow keys
- [ ] Link text is descriptive (not "click here" or "read more")
- [ ] Heading hierarchy is logical (h1 → h2 → h3, no skips)
- [ ] Lists are marked up as <ul>/<ol>/<dl>

### Forms
- [ ] All inputs have associated labels (<label for=""> or aria-label)
- [ ] Required fields announced as required
- [ ] Error messages announced when they appear (aria-live="assertive")
- [ ] Form submission result announced

### Dynamic Content
- [ ] Loading states announced (aria-busy="true", live regions)
- [ ] Modal dialog traps focus and returns focus on close
- [ ] Toast notifications announced via aria-live="polite"
- [ ] Content changes announced via live regions

### Images & Media
- [ ] Meaningful images have descriptive alt text
- [ ] Decorative images have alt="" or role="presentation"
- [ ] Complex images have long descriptions
- [ ] Video has captions and audio description
```

### Keyboard Navigation

```typescript
// Playwright — keyboard navigation tests
import { test, expect } from '@playwright/test';

test('tab order follows visual layout', async ({ page }) => {
  await page.goto('/form');

  // Tab through all interactive elements
  const expectedOrder = [
    'input[name="name"]',
    'input[name="email"]',
    'select[name="country"]',
    'button[type="submit"]',
  ];

  for (const selector of expectedOrder) {
    await page.keyboard.press('Tab');
    const focused = await page.evaluate(() => document.activeElement?.tagName);
    await expect(page.locator(selector)).toBeFocused();
  }
});

test('focus is visible on all interactive elements', async ({ page }) => {
  await page.goto('/');

  // Get all focusable elements
  const focusable = await page.$$eval(
    'a, button, input, select, textarea, [tabindex]',
    elements => elements.map(el => ({
      tag: el.tagName,
      id: el.id,
      className: el.className,
    }))
  );

  for (const el of focusable) {
    await page.keyboard.press('Tab');
    // Verify focus indicator is visible (outline or box-shadow)
    const hasFocusStyle = await page.evaluate(() => {
      const active = document.activeElement;
      if (!active) return false;
      const styles = window.getComputedStyle(active);
      const outline = styles.outline;
      const boxShadow = styles.boxShadow;
      return (outline !== 'none' && outline !== '0px') ||
             (boxShadow !== 'none');
    });
    expect(hasFocusStyle).toBe(true);
  }
});

test('no keyboard traps exist', async ({ page }) => {
  await page.goto('/');

  // Tab 100 times, verify focus never gets stuck
  const visitedElements: string[] = [];
  for (let i = 0; i < 100; i++) {
    await page.keyboard.press('Tab');
    const focused = await page.evaluate(() => {
      const el = document.activeElement;
      return `${el?.tagName}#${el?.id}.${el?.className}`;
    });
    visitedElements.push(focused);
  }

  // Check we visited more than 1 element (not trapped)
  const uniqueElements = new Set(visitedElements);
  expect(uniqueElements.size).toBeGreaterThan(1);

  // Check no single element appears consecutively more than 3 times
  // (allowing for repeat in small pages)
  let maxConsecutive = 1;
  let currentConsecutive = 1;
  for (let i = 1; i < visitedElements.length; i++) {
    if (visitedElements[i] === visitedElements[i - 1]) {
      currentConsecutive++;
      maxConsecutive = Math.max(maxConsecutive, currentConsecutive);
    } else {
      currentConsecutive = 1;
    }
  }
  expect(maxConsecutive).toBeLessThanOrEqual(3);
});

test('Escape closes modals and returns focus', async ({ page }) => {
  await page.goto('/');
  const trigger = page.locator('button:text("Open Modal")');
  await trigger.click();

  const modal = page.locator('[role="dialog"]');
  await expect(modal).toBeVisible();

  // Focus should be trapped in modal
  await page.keyboard.press('Escape');
  await expect(modal).toBeHidden();

  // Focus should return to trigger
  await expect(trigger).toBeFocused();
});

test('Enter and Space activate buttons', async ({ page }) => {
  await page.goto('/');
  const button = page.locator('button:text("Submit")');

  await button.focus();
  await page.keyboard.press('Enter');
  await expect(page.locator('.result')).toBeVisible();

  await page.reload();
  await button.focus();
  await page.keyboard.press('Space');
  await expect(page.locator('.result')).toBeVisible();
});
```

### Accessibility Testing in CI

```typescript
// jest-axe — unit/integration level a11y testing
import { render } from '@testing-library/react';
import { axe, toHaveNoViolations } from 'jest-axe';

expect.extend(toHaveNoViolations);

test('Button has no a11y violations', async () => {
  const { container } = render(<Button variant="primary">Click me</Button>);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});

test('Form has no a11y violations', async () => {
  const { container } = render(
    <form>
      <label htmlFor="email">Email</label>
      <input id="email" type="email" required aria-describedby="email-help" />
      <span id="email-help">We'll never share your email.</span>
      <button type="submit">Subscribe</button>
    </form>
  );
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});

test('Modal has no a11y violations', async () => {
  const { container } = render(
    <Modal isOpen={true} onClose={jest.fn()} aria-labelledby="modal-title">
      <h2 id="modal-title">Confirm Action</h2>
      <p>Are you sure?</p>
      <button>Cancel</button>
      <button>Confirm</button>
    </Modal>
  );
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

```typescript
// cypress-axe — E2E a11y testing
import 'cypress-axe';

describe('Accessibility', () => {
  beforeEach(() => {
    cy.visit('/');
    cy.injectAxe();
  });

  it('homepage has no a11y violations', () => {
    cy.checkA11y();
  });

  it('dashboard has no a11y violations', () => {
    cy.visit('/dashboard');
    cy.injectAxe();
    cy.checkA11y('main', {
      runOnly: {
        type: 'tag',
        values: ['wcag2a', 'wcag2aa'],
      },
    });
  });

  it('modal has no a11y violations', () => {
    cy.get('button:contains("Open")').click();
    cy.get('[role="dialog"]').should('be.visible');
    cy.checkA11y('[role="dialog"]');
  });

  it('form error states are accessible', () => {
    cy.get('button[type="submit"]').click(); // trigger validation
    cy.checkA11y('form', {
      rules: {
        'color-contrast': { enabled: true },
        'label': { enabled: true },
      },
    });
  });
});
```

```typescript
// @axe-core/playwright — comprehensive E2E a11y suite
import AxeBuilder from '@axe-core/playwright';
import { test, expect } from '@playwright/test';

// Audit every page route
const routes = ['/', '/login', '/dashboard', '/settings', '/profile'];

for (const route of routes) {
  test(`${route} has no WCAG 2.1 AA violations`, async ({ page }) => {
    await page.goto(route);
    await page.waitForLoadState('networkidle');

    const results = await new AxeBuilder({ page })
      .withTags(['wcag2a', 'wcag2aa', 'wcag21aa'])
      .analyze();

    // Attach report for debugging
    await test.info().attach('a11y-report', {
      body: JSON.stringify(results, null, 2),
      contentType: 'application/json',
    });

    expect(results.violations).toEqual([]);
  });
}

// Visual snapshot + a11y combined
test('login page — visual + a11y', async ({ page }) => {
  await page.goto('/login');
  await page.waitForLoadState('networkidle');

  // Visual regression
  await expect(page).toHaveScreenshot('login-page.png');

  // Accessibility
  const a11yResults = await new AxeBuilder({ page }).analyze();
  expect(a11yResults.violations).toEqual([]);
});
```

```yaml
# CI pipeline: accessibility gate
name: Accessibility Tests
on: [pull_request]

jobs:
  a11y:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci

      - name: Start app
        run: npm run dev &
        env:
          PORT: 3000

      - name: Wait for app
        run: npx wait-on http://localhost:3000

      - name: jest-axe (unit)
        run: npx jest --testMatch='**/*.a11y.test.{ts,tsx}' --ci

      - name: Playwright a11y (E2E)
        run: npx playwright test --grep 'a11y|accessibility'

      - name: Pa11y CI (pages)
        run: npx pa11y-ci

      - name: Lighthouse a11y score
        run: |
          npx lhci autorun \
            --assert.assertions.categories:accessibility=["error",{"minScore":0.95}]
```

**Accessibility testing checklist:**
- [ ] axe-core integrated in unit tests (jest-axe), E2E (cypress-axe, playwright)
- [ ] Lighthouse CI a11y score ≥ 95% in pipeline
- [ ] Pa11y CI runs on all key routes
- [ ] Manual screen reader testing for critical flows (NVDA, VoiceOver)
- [ ] Keyboard-only navigation verified (tab order, focus visible, no traps)
- [ ] Color contrast ratios meet WCAG AA (4.5:1 normal, 3:1 large text)
- [ ] All images have appropriate alt text
- [ ] ARIA attributes used correctly (roles, states, properties)
- [ ] Form errors announced to screen readers (aria-live)
- [ ] Modal focus trapping and return-focus tested
- [ ] Skip-to-main-content link present and functional
- [ ] Touch target size ≥ 44×44 CSS pixels (WCAG 2.5.8)

