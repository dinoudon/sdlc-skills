---
name: sdlc-testing-qa
description: "Test pyramid (unit/integration/e2e), TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance testing (k6/Locust), security testing (SAST/DAST), accessibility testing, AI-assisted test generation, serverless testing patterns, ML model testing, API contract testing, database testing, concurrency testing, observability-driven testing. Includes Google testing culture and test architecture patterns."
version: 3.2.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, testing, tdd, bdd, playwright, pytest, k6, security, sast, dast, accessibility, google, contract-testing, chaos-engineering, mutation-testing, property-based, ai-test-generation, serverless-testing, ml-testing, api-contract, database-testing, concurrency-testing, observability-testing]
    related_skills: [sdlc-cicd-pipeline, sdlc-deployment, test-driven-development, security-review-owasp]
---

# Testing & Quality Assurance

Test pyramid, TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance, security, accessibility testing, AI-assisted test generation, serverless testing patterns. Includes Google testing culture and test architecture patterns.

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
