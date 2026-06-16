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
    local = draw(st.text(alphabet=st.characters(whitelist_categories=('L', 'N')), min_size=1, max_size=64))
    domain = draw(st.sampled_from(['example.com', 'test.org', 'mail.io']))
    return f"{local}@{domain}"

@given(st.integers(min_value=-1000, max_value=1000))
def test_interesting_edge(n):
    interestingness = -abs(n)
    target(interestingness)
    assert n != 0
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

### fast-check: Model-Based Testing with Commands

```typescript
import fc from 'fast-check';

class AddCommand implements fc.Command<QueueModel, QueueReal> {
  constructor(readonly value: number) {}
  check = (model: QueueModel) => true;
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
```

### Hypothesis vs fast-check

| Feature | Hypothesis (Python) | fast-check (JS/TS) |
|---------|--------------------|--------------------|
| Shrinking | Automatic, very aggressive | Automatic, configurable |
| Stateful testing | `RuleBasedStateMachine` | `@fast-check/ava` model-based |
| Strategy composition | Excellent, composable | Good, `fc.oneof`, `fc.record` |
| Auto-generation | `hypothesis write` ghostwriter | None built-in |
| CI integration | pytest plugin | Jest/AVA/Vitest plugin |
| Performance | Python — slower | JS engine — faster |

## Step 7: Mutation Testing

### Tools

| Tool | Language | Notes |
|------|----------|-------|
| PIT (pitest) | Java/JVM | Most mature. Supports incremental analysis, JUnit 5 |
| Stryker | JS/TS/C# | First-class Jest/Vitest support, dashboard for tracking |
| mutmut | Python | Works with pytest |
| Mull | C/C++ | LLVM-based |

### Mutation Score Thresholds

| Score | Interpretation |
|-------|---------------|
| >80% | Excellent — very strong test suite |
| 70-80% | Strong — acceptable for most projects |
| 50-70% | Weak — significant test gaps exist |
| <50% | Poor — tests barely verify behavior |

### Stryker (JavaScript/TypeScript)
Source: https://stryker-mutator.io/

```json
// stryker.config.json
{
  "mutate": ["src/**/*.ts"],
  "testRunner": "vitest",
  "coverageAnalysis": "perTest",
  "incremental": true,
  "incrementalFile": ".stryker-patches/stryker.incremental.json"
}
```

Incremental mode:
- First run: full mutation, stores surviving mutants
- Subsequent runs: only mutates changed files + re-checks previously surviving mutants
- CI: run full mutation on main, incremental on PRs

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

### Surviving Mutants Analysis

```bash
npx stryker run --reporters html
open reports/mutation.html  # interactive diff of each surviving mutant
```

Categories of surviving mutants:
1. **Weak test** — test doesn't assert the mutated behavior (add assertion)
2. **Equivalent mutant** — code change produces identical behavior (safe to ignore)
3. **Redundant code** — dead code that mutation reveals (remove it)

### Key Mutators

| Mutator Type | Example |
|-------------|---------|
| Conditionals | `>` → `>=`, `==` → `!=` |
| Return values | `return x` → `return null` |
| Void method calls | Remove method calls |
| Math | `+` → `-`, `*` → `/` |

### Equivalent Mutant Problem

Some mutants are semantically identical to original — no test can kill them. Mitigations:
- Auto-detect: static analysis to flag common equivalents (e.g., `i++` → `++i`)
- Timeout: mutants that don't cause test failure within N seconds likely equivalent
- Mark known equivalents manually; exclude from score calculation

## Step 8: Contract Testing

### Pact
Source: https://docs.pact.io/

Verifies API provider and consumer agree on request/response shape without running both services together.

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

### Bi-Directional Contract Testing (Pactflow)

Source: https://docs.pactflow.io/docs/bi-directional-contract-testing/

Matches independently generated contracts — no provider-side verification code needed.

```
Consumer (Pact)  ──► Pactflow ◄──  Provider (OpenAPI spec)
   {GET /users/1       matches       paths:
     response:                           /users/{id}:
       200                                 get:
       body: id, name                        responses: 200
                                            schema: {id, name}
```

When to use bi-directional:
- Provider team cannot write Pact verification tests (legacy, different language)
- Provider already has OpenAPI spec
- Want to decouple provider/consumer release cycles

### Can-I-Deploy Gate
```bash
npx pact-broker can-i-deploy --pacticipant UserService --version $GIT_SHA --to production
# Returns exit code 0 (safe) or 1 (blocked)
```

## Step 9: Chaos Engineering

### Principles
Source: https://principlesofchaos.org/

1. Build hypothesis around steady state behavior
2. Introduce real-world events (server failures, network partitions, resource exhaustion)
3. Run experiments in production (or staging with prod-like traffic)
4. Automate experiments to run continuously
5. Minimize blast radius

### Tools Comparison

| Feature | LitmusChaos | Chaos Mesh | AWS FIS |
|---------|-------------|------------|---------|
| CNCF Status | Graduated | Incubating | N/A |
| Cloud targets | AWS/GCP/Azure | Primarily K8s | AWS only |
| Dashboard | Litmus Portal | Built-in UI | AWS Console |
| JVM chaos | Via plugins | Built-in | N/A |
| Best for | Multi-cloud chaos | K8s-focused chaos | AWS chaos |

### LitmusChaos (CNCF Graduated)
Source: https://litmuschaos.io/

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

ChaosHub: https://hub.litmuschaos.io/ — 60+ pre-built experiments (K8s, AWS, GCP, Azure, VMware)

### Chaos Mesh
Source: https://chaos-mesh.org/

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
      "targets": ["myInstances"]
    }
  }
}
```

### Game Day Format

```
[00:00] Injection begins — inject failure, start timer
[00:02] Detection phase — does alerting fire? MTTD
[00:10] Triage phase — on-call responds, MTTR
[00:30] Mitigation phase — apply fix
[01:00] Recovery verification — steady state restored
[02:00] Blameless Retrospective — what surprised us?
```

| Metric | Target |
|--------|--------|
| MTTD (Mean Time to Detect) | < 5 min |
| MTTR (Mean Time to Resolve) | < 30 min |
| Alert accuracy | > 95% |
| Runbook coverage | 100% of critical services |

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

### k6 Load Profiles

```javascript
// Constant Arrival Rate (requests/sec target)
export const options = {
  scenarios: {
    constant_rate: {
      executor: 'constant-arrival-rate',
      rate: 1000,              // 1000 iterations/sec
      timeUnit: '1s',
      duration: '5m',
      preAllocatedVUs: 200,
      maxVUs: 500,
    },
  },
};
```

### Locust (Python)
```python
from locust import HttpUser, task, between, tag

class APIUser(HttpUser):
    wait_time = between(0.5, 2)

    @tag('read')
    @task(5)
    def get_users(self):
        with self.client.get('/users', catch_response=True) as res:
            if res.status_code != 200:
                res.failure(f"Got {res.status_code}")

    @tag('write')
    @task(1)
    def create_user(self):
        self.client.post('/users', json={"name": "test", "email": "test@test.com"})

    def on_start(self):
        self.client.post('/login', json={"email": "load@test.com", "password": "test"})
```

### Load Testing Patterns

| Pattern | Description | Duration | Key Metric |
|---------|-------------|----------|------------|
| Smoke | Minimal load, verify system works | 1-2 min, few VUs | Error rate |
| Load | Expected concurrent users | 15-30 min | p95 latency < SLA |
| Stress | Push beyond expected load | 15-30 min | Where does it break? |
| Soak | Sustained moderate load | 4-24 hours | Memory growth, latency drift |
| Spike | Sudden traffic surge | Short bursts | Recovery time |
| Breakpoint | Find breaking point | Until failure | Max RPS before errors |

### Tool Comparison

| Feature | k6 | Locust | Gatling | Artillery |
|---------|-----|--------|---------|-----------|
| Language | JS (Go engine) | Python | Scala/Kotlin/Java | YAML/JS |
| Protocol | HTTP/WS/gRPC | HTTP (extensible) | HTTP/JMS/gRPC | HTTP/WS |
| Distributed | k6-operator (k8s) | Native | Built-in | Fargate plugin |
| Best for | Dev teams, CI | Python shops | Enterprise, JVM | Quick YAML setup |

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

### SAST Tools

| Tool | Type | Strengths |
|------|------|-----------|
| Semgrep | Pattern-based | Custom rules, fast, 2000+ rulesets |
| CodeQL | Semantic analysis | Deep dataflow analysis, zero-config |
| SonarQube | Multi-language | Quality gates, technical debt tracking |
| Bandit | Python SAST | Python-specific, low false positives |

### OWASP ZAP Automation Framework

```yaml
# zap-automation.yaml
env:
  contexts:
    - name: "target-app"
      urls: ["https://staging.example.com"]
      excludePaths: [".*logout.*"]
  parameters:
    failOnError: true

jobs:
  - type: spider
    parameters: { context: "target-app", maxDuration: 5 }
  - type: ajaxSpider
    parameters: { context: "target-app", maxDuration: 5 }
  - type: activeScan
    parameters: { context: "target-app", maxScanDurationInMins: 30 }
  - type: report
    parameters: { template: traditional-html, reportFile: zap-report.html }
```

```bash
# Baseline scan (passive only, fast)
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-baseline.py \
  -t https://staging.example.com -r zap-baseline.html

# API scan (OpenAPI spec)
docker run -t ghcr.io/zaproxy/zaproxy:stable zap-api-scan.py \
  -t https://staging.example.com/openapi.json -f openapi -r zap-api.html
```

### API Fuzzing

```python
# Schemathesis — fuzz test from OpenAPI spec
import schemathesis

schema = schemathesis.from_url("https://api.example.com/openapi.json")

@schema.parametrize()
def test_api(case):
    response = case.call()
    case.validate_response(response)
```

| Tool | Approach | Strengths |
|------|----------|-----------|
| Schemathesis | Property-based from OpenAPI | Fast, pytest integration |
| RESTler | Stateful grammar-based | Learns API state transitions |
| Burp Suite headless | Full DAST suite | Deep scanning for CI |

### Pipeline Integration

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
      needs: [deploy-staging]
      runs-on: ubuntu-latest
      steps:
        - name: ZAP baseline scan
          uses: zaproxy/action-baseline@v0.12.0
          with:
            target: 'https://staging.example.com'
            fail_action: true

    dependency-check:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - uses: aquasecurity/trivy-action@master
          with:
            scan-type: 'fs'
            severity: 'CRITICAL,HIGH'
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
npx lhci autorun \
  --assert.assertions.categories:accessibility=["error", {"minScore": 0.95}]
```

### Keyboard Navigation Tests
```typescript
test('tab order follows visual layout', async ({ page }) => {
  await page.goto('/form');
  const expectedOrder = [
    'input[name="name"]', 'input[name="email"]',
    'select[name="country"]', 'button[type="submit"]',
  ];
  for (const selector of expectedOrder) {
    await page.keyboard.press('Tab');
    await expect(page.locator(selector)).toBeFocused();
  }
});

test('no keyboard traps exist', async ({ page }) => {
  await page.goto('/');
  const visited: string[] = [];
  for (let i = 0; i < 100; i++) {
    await page.keyboard.press('Tab');
    const focused = await page.evaluate(() =>
      `${document.activeElement?.tagName}#${document.activeElement?.id}`
    );
    visited.push(focused);
  }
  expect(new Set(visited).size).toBeGreaterThan(1);
});

test('Escape closes modals and returns focus', async ({ page }) => {
  await page.goto('/');
  const trigger = page.locator('button:text("Open Modal")');
  await trigger.click();
  await expect(page.locator('[role="dialog"]')).toBeVisible();
  await page.keyboard.press('Escape');
  await expect(page.locator('[role="dialog"]')).toBeHidden();
  await expect(trigger).toBeFocused();
});
```

### Screen Reader Matrix

| Reader | Platform | Browser | Market Share |
|--------|----------|---------|--------------|
| NVDA | Windows | Firefox, Chrome | ~40% |
| JAWS | Windows | Chrome, Edge | ~35% |
| VoiceOver | macOS/iOS | Safari | ~15% |
| TalkBack | Android | Chrome | ~10% |

Priority: NVDA+Firefox > VoiceOver+Safari > JAWS+Chrome

### Screen Reader Test Checklist

- [ ] Page title announced correctly
- [ ] Skip-to-main-content link works
- [ ] Landmark regions announced (banner, navigation, main, contentinfo)
- [ ] All interactive elements reachable with Tab/arrow keys
- [ ] Heading hierarchy logical (h1 → h2 → h3, no skips)
- [ ] All inputs have associated labels
- [ ] Error messages announced (aria-live="assertive")
- [ ] Modal dialog traps focus and returns focus on close
- [ ] Meaningful images have descriptive alt text
- [ ] Video has captions and audio description

### Accessibility Testing Checklist

- [ ] axe-core in unit tests (jest-axe) and E2E (playwright)
- [ ] Lighthouse CI a11y score ≥ 95% in pipeline
- [ ] Pa11y CI on all key routes
- [ ] Manual screen reader testing for critical flows
- [ ] Keyboard-only navigation verified
- [ ] Color contrast ratios meet WCAG AA (4.5:1 normal, 3:1 large text)
- [ ] ARIA attributes used correctly
- [ ] Touch target size ≥ 44×44 CSS pixels (WCAG 2.5.8)

## Step 13: Test Architecture — Ports & Adapters

Source: "Growing Object-Oriented Software, Guided by Tests" (Freeman & Pryce)

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

**Humble Object pattern** (Martin Fowler): Thin adapter layer, logic in testable core.

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

1. **Build** — project compiles
2. **Lint** — no violations
3. **Type Check** — no type errors
4. **Unit Tests** — all pass with coverage
5. **Integration/E2E** — critical paths verified

If ANY phase fails → STOP and fix.

## Step 16: Snapshot Testing

```typescript
// Jest/Vitest
expect(component).toMatchSnapshot();
expect(value).toMatchInlineSnapshot();  // Embeds in test file
```

Key patterns:
- Keep snapshots small, focused
- Use `toMatchInlineSnapshot()` for simple values
- Mock non-deterministic data (dates, IDs) before snapshotting
- Treat .snap files as code — review diffs in PRs

## Step 17: Visual Regression Testing

### Tools

| Tool | Best For | Notes |
|------|----------|-------|
| Percy (BrowserStack) | CI/CD integration | Snapshot diffs in PR, approve/reject |
| Chromatic | Storybook projects | First-class Storybook integration |
| BackstopJS | Standalone scripts | OnBefore/OnReady scripts |
| Playwright `toHaveScreenshot()` | Minimal setup | Built-in, no external service |

### Playwright Visual Comparison

```typescript
import { test, expect } from '@playwright/test';

test('homepage visual check', async ({ page }) => {
  await page.goto('/');
  await expect(page).toHaveScreenshot('homepage.png', {
    maxDiffPixelRatio: 0.01,   // <1% pixels may differ
    animations: 'disabled',
  });
});
```

### Threshold Tuning

| Threshold | Use Case |
|-----------|----------|
| 0 (exact) | Critical UI (medical, financial dashboards) |
| 0.1 | High-fidelity apps (marketing pages) |
| 0.2 | Standard apps — good balance |
| 0.3 | Tolerant — dynamic fonts, anti-aliasing |

### Masking Dynamic Content

```typescript
await expect(page).toHaveScreenshot('dashboard.png', {
  mask: [
    page.locator('.timestamp'),
    page.locator('.user-avatar'),
    page.locator('[data-testid="random-id"]'),
  ],
  maskColor: '#FF00FF',
});
```

**Baseline workflow:** Run tests → diff against baseline → approve new baseline or fix regression.

## Step 18: Fuzz Testing

### Tools

| Tool | Type | Notes |
|------|------|-------|
| AFL++ | Coverage-guided | Mutates inputs based on coverage feedback |
| libFuzzer | In-process | Define `LLVMFuzzerTestOneInput()` |
| OSS-Fuzz (Google) | Continuous | Combines AFL++, libFuzzer, honggfuzz |

Key patterns:
- Provide seed corpus (valid inputs) for faster coverage
- Use sanitizer builds: `-fsanitize=address,undefined`
- Target parsers, decoders, protocol handlers, file format handlers

## Step 19: Test Data Management

### Factory Pattern (Faker)

```typescript
// Factory pattern
const createUser = (overrides = {}) => ({
  id: faker.string.uuid(),
  email: faker.internet.email(),
  name: faker.person.fullName(),
  ...overrides,
});
```

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
```

### Factory Boy (Python)

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

    class Params:
        admin = factory.Trait(role="admin", email=factory.LazyAttribute(
            lambda o: f"admin{o.id}@test.com"))

# Usage
user = UserFactory()
admin = UserFactory(admin=True)
batch = UserFactory.create_batch(50)
```

### Deterministic Seeds

```typescript
import { faker } from '@faker-js/faker';
beforeEach(() => { faker.seed(42); });

// fast-check seeded runs
fc.assert(property, { seed: 42, numRuns: 100 });

// Hypothesis
from hypothesis import settings, seed
@settings(database=directory(".hypothesis/examples"), derandomize=True)
@given(st.integers())
def test_deterministic(n): ...
```

### Transaction Rollback Isolation

```python
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
```

### Database Testing Patterns

| Pattern | Speed | Isolation | Best For |
|---------|-------|-----------|----------|
| Transaction rollback | Fastest | Per-test | Unit/integration tests |
| Truncate between tests | Fast | Per-test | Tests that need committed data |
| Fresh database per test | Slow | Perfect | Migration tests, schema changes |
| Shared database + cleanup | Medium | Fragile | Only when other patterns fail |

### Data Masking / PII Detection

```python
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
    operators={"DEFAULT": OperatorConfig("replace", {"new_value": "[REDACTED]""})},
)
```

**Test data management checklist:**
- [ ] Synthetic data generation for all entity types
- [ ] Seeded/random modes (reproducible in CI, varied in dev)
- [ ] PII detection in CI for all fixture/seed files
- [ ] Test data as code (versioned, reviewable, reproducible)
- [ ] Data cleanup between tests (transaction rollback, truncate)

## Step 20: Flaky Test Management

Source: https://testing.googleblog.com/2016/05/flaky-tests-at-google-and-how-we.html

**Detection:** Track pass/fail history per test across N runs. Google: ~1.5% of tests flaky at any time.

### Google Deflake Bot Pattern

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

### Quarantine + Fix Workflow

```
Step 1: DETECT — CI fails intermittently, pass rate < 95%

Step 2: QUARANTINE (immediate)
  - Move to quarantine suite (separate job/config)
  - Mark with @flaky annotation/tag

Step 3: DIAGNOSE (within 48h)
  - Reproduce locally 100x: pytest -x --count=100 test_foo.py
  - Root causes: timing, shared state, external deps, non-deterministic

Step 4: FIX — apply fix, run 1000x in CI

Step 5: DELETE (if unfixed within 2 weeks)
  - A test that can't be trusted provides negative value
```

```typescript
// Jest: quarantine pattern
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
      retryTimes: 3,
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

## Step 21: AI-Assisted Test Generation

### Scaffold-and-Refine Pattern

```
1. SCAFFOLD — LLM generates test suite from function signature + docstring
2. RUN      — execute generated tests, collect failures
3. ANALYZE  — feed failures back to LLM with error output
4. REFINE   — LLM fixes failing tests, adds missing edge cases
5. VALIDATE — human reviews for meaningful assertions
```

Typical LLM first-pass achieves **70-80% pass rate**. 2-3 rounds bring to 90%+.

### AI Test Generation Anti-Patterns

| Anti-Pattern | Example | Fix |
|---|---|---|
| **Trivial assertions** | `assert result is not None` | Assert specific values |
| **Happy-path only** | Only tests valid input | Add boundary, empty, negative cases |
| **No adversarial inputs** | Missing injection, XSS | Add security-relevant inputs |
| **Assertion-free tests** | Calls function, no assert | Every test needs meaningful assertion |
| **Snapshot of current behavior** | Asserts current (wrong) output | Verify against spec/docstring |
| **Copy-paste duplication** | 5 tests that differ only in input | Parametrize |
| **Overmocking** | Mocks everything, tests nothing | Mock only external boundaries |

### Test Generation Prompt Template

```
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
```

### AI Test Generation Tools

| Tool | Language | Notes |
|------|----------|-------|
| CodiumAI / Qodo | Multi | Analyzes behavior, generates meaningful tests, IDE + CLI |
| Diffblue Cover | Java | Autonomous JUnit generation, enterprise-grade |
| GitHub Copilot | Multi | `/tests` slash command, supports all major frameworks |
| Hypothesis Ghostwriter | Python | `hypothesis write my_module.my_function` |

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

## Step 22: Serverless Testing Patterns

### AWS Lambda — SAM Local

```bash
pip install aws-sam-cli

# Invoke function locally
sam local invoke ProcessOrder --event events/order.json

# Start local API Gateway
sam local start-api --port 3000

# Run tests against local Lambda
sam local start-lambda --port 3001
```

```python
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

### Cloudflare Workers — Miniflare

Source: https://miniflare.dev/

```bash
npm install -D miniflare wrangler
wrangler dev  # starts on http://localhost:8787
```

```typescript
// Test with Vitest + @cloudflare/vitest-pool-workers
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
npm install -g azure-functions-core-tools@4
func start  # reads local.settings.json, starts on port 7071
curl http://localhost:7071/api/HttpTrigger?name=test
```

### Serverless Testing Strategy

```
Layer 1: UNIT TESTS (fast, no platform)
  - Test handler logic in isolation
  - Mock event/context objects

Layer 2: LOCAL INTEGRATION (medium, platform simulation)
  - SAM local / Miniflare / func start
  - Test with real event payloads

Layer 3: CLOUD INTEGRATION (slow, real platform)
  - Deploy to staging/dev account
  - Verify IAM permissions, cold starts, timeouts
```

**Event fixtures:** `sam local generate-event s3 put > events/s3-put.json`

**Common pitfalls:**
- Don't test only locally — IAM, VPC, timeouts only manifest in cloud
- Don't skip cold start testing — first invocation is 10-100x slower
- Don't ignore timeout limits — test what happens at 29s (Lambda max)
- Don't assume local == cloud — DynamoDB Local behaves differently

## Step 23: ML Model Testing Patterns

### Data Validation

```python
# Great Expectations
import great_expectations as gx

context = gx.get_context()
validator = context.sources.pandas_default.read_csv("train.csv")

validator.expect_column_values_to_not_be_null("feature_1")
validator.expect_column_values_to_be_between("age", min_value=0, max_value=150)
validator.expect_column_values_to_be_in_set("label", ["cat", "dog", "bird"])

results = validator.validate()
assert results.success, f"Data validation failed: {results.results}"
```

```python
# Pandera — DataFrame schema validation
import pandera as pa
from pandera import Column, Check

schema = pa.DataFrameSchema({
    "feature_1": Column(float, Check.in_range(0, 1)),
    "feature_2": Column(float, Check(lambda s: s.std() > 0)),
    "label": Column(str, Check.isin(["positive", "negative"])),
})

@pa.check_types
def train(df: pa.typing.DataFrame[schema]): ...
```

**Data validation checklist:**
- Schema matches expected columns, types, nullable constraints
- No distribution shift between train/test/production (KS test, PSI)
- No data leakage (features that encode the target)
- Label distribution balanced or intentionally skewed

### Model Performance Testing

```python
from sklearn.metrics import accuracy_score, f1_score

def test_accuracy_above_threshold(model, test_data):
    preds = model.predict(test_data.X)
    acc = accuracy_score(test_data.y, preds)
    assert acc >= 0.92, f"Accuracy {acc:.3f} below threshold 0.92"

def test_inference_latency(model, test_data):
    import time
    sample = test_data.X[:1]
    start = time.perf_counter()
    for _ in range(100):
        model.predict(sample)
    avg_ms = (time.perf_counter() - start) / 100 * 1000
    assert avg_ms < 50, f"Avg inference {avg_ms:.1f}ms exceeds 50ms"
```

### Bias Testing

```python
from fairlearn.metrics import MetricFrame, demographic_parity_difference

def test_demographic_parity(model, test_data):
    preds = model.predict(test_data.X)
    metric_frame = MetricFrame(
        metrics=accuracy_score,
        y_true=test_data.y,
        y_pred=preds,
        sensitive_features=test_data.demographics["gender"]
    )
    diff = metric_frame.difference(method="between_groups")
    assert diff < 0.05, f"Gender accuracy gap: {diff:.3f}"
```

**Bias testing checklist:**
- Test accuracy/F1/recall across each protected attribute
- Check for proxy features that encode protected attributes
- Validate fairness metrics: demographic parity, equalized odds, calibration
- Test intersectional groups (e.g., gender x race)

### Drift Detection

| Type | Method | When |
|------|--------|------|
| Data drift | KS test, PSI, Chi-square | Continuous monitoring |
| Concept drift | Performance decay over time | Weekly model eval |
| Prediction drift | Output distribution shift | Per-batch monitoring |
| Feature drift | Per-feature statistical tests | CI pipeline + monitoring |

```python
# Alibi Detect — drift detection
from alibi_detect.cd import KSDrift

cd = KSDrift(test_data.X_train, p_val=0.05)
result = cd.predict(test_data.X_new)
assert not result["data"]["is_drift"], f"Data drift detected: {result['data']['p_val']}"
```

```python
# Evidently AI — comprehensive drift reports
from evidently.report import Report
from evidently.metric_preset import DataDriftPreset

report = Report(metrics=[DataDriftPreset()])
report.run(reference_data=train_df, current_data=prod_df)
result = report.as_dict()
drift_share = result["metrics"][0]["result"]["drift_share"]
assert drift_share < 0.3, f"Drift share {drift_share:.2f} exceeds 30%"
```

## Step 24: API Contract Testing — Advanced

### Schema Validation

```python
# Schemathesis — property-based API testing from OpenAPI spec
import schemathesis

schema = schemathesis.from_url("http://localhost:8080/openapi.json")

@schema.parametrize()
def test_api_conformance(case):
    response = case.call()
    case.validate_response(response)
```

```typescript
// OpenAPI schema validation with Ajv
import Ajv from 'ajv';
import addFormats from 'ajv-formats';

const ajv = new Ajv({ allErrors: true });
addFormats(ajv);

function validateAgainstSchema(data: unknown, schema: object): boolean {
  const validate = ajv.compile(schema);
  return validate(data);
}
```

### Backward Compatibility Testing

```bash
# oasdiff — OpenAPI diff tool
oasdiff breaking openapi-v1.yaml openapi-v2.yaml
oasdiff breaking openapi-v1.yaml openapi-v2.yaml --fail-on ERR
```

**Backward compatibility rules:**
1. Never remove a field from a response
2. Never add a required field to a request
3. Never change a field type
4. Never change a URL path or HTTP method
5. Always add optional fields with defaults
6. Deprecate first, remove after N versions

### Breaking Change Detection in CI

```yaml
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
          git show origin/main:openapi/spec.yaml > /tmp/base.yaml
          oasdiff breaking /tmp/base.yaml openapi/spec.yaml --fail-on ERR
```

| Tool | Language | Method |
|------|----------|--------|
| oasdiff | Go/CLI | Static OpenAPI diff |
| optic | Node/CLI | API governance rules |
| openapi-diff | Java | Semantic diff |
| bump-cli | Python | Version-aware diff |

## Step 25: Database Testing Patterns

### Migration Testing

```python
from alembic.config import Config
from alembic import command
from sqlalchemy import create_engine, inspect, text

def test_migrations_up(db_engine):
    """All migrations apply cleanly."""
    alembic_cfg = Config("alembic.ini")
    alembic_cfg.set_main_option("sqlalchemy.url", str(db_engine.url))
    command.upgrade(alembic_cfg, "head")
    inspector = inspect(db_engine)
    assert "users" in inspector.get_table_names()

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
    command.upgrade(alembic_cfg, "abc123")
    with db_engine.connect() as conn:
        conn.execute(text("INSERT INTO users (email) VALUES ('test@example.com')"))
        conn.commit()
    command.upgrade(alembic_cfg, "head")
    with db_engine.connect() as conn:
        result = conn.execute(text("SELECT email FROM users")).fetchall()
        assert any("test@example.com" in r[0] for r in result)
```

## Step 26: Concurrency Testing

### Race Condition Detection

```python
import threading
from concurrent.futures import ThreadPoolExecutor

def test_concurrent_counter_increment():
    counter = UnsafeCounter()
    iterations = 10000
    with ThreadPoolExecutor(max_workers=10) as pool:
        futures = [pool.submit(counter.increment) for _ in range(iterations)]
        for f in futures:
            f.result()
    assert counter.value == iterations, f"Expected {iterations}, got {counter.value}"
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
        }(i)
    }
    wg.Wait()
}
```

### Deadlock Detection

```python
def test_deadlock_detection():
    lock_a = threading.Lock()
    lock_b = threading.Lock()
    deadlock_detected = threading.Event()

    def worker_1():
        with lock_a:
            time.sleep(0.01)
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
    t1.start(); t2.start()
    t1.join(timeout=5); t2.join(timeout=5)
    assert not deadlock_detected.is_set(), "Deadlock detected"
```

**Concurrency testing checklist:**
- Run Go tests with `-race` flag in CI
- Use thread sanitizers for C/C++ (`-fsanitize=thread`)
- Test with multiple thread counts (1, 2, N, N*2)
- Add timeouts to all lock acquisitions in tests
- Verify idempotency of operations under concurrent access

## Step 27: Observability-Driven Testing

### Testing with Traces

```typescript
import { trace, SpanStatusCode } from '@opentelemetry/api';
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
  expect(spanNames).toContain('payment.charge');

  const orderSpan = spans.find(s => s.name === 'order.create');
  const paymentSpan = spans.find(s => s.name === 'payment.charge');
  expect(paymentSpan.parentSpanId).toBe(orderSpan.spanContext().spanId);
});
```

### Observability-Driven Testing Patterns

| Pattern | Source | Use Case |
|---------|--------|----------|
| Trace replay | Production traces | Validate request flows |
| Traffic shadowing | Prod traffic copy | Test new code with real patterns |
| Log-driven tests | Error logs | Reproduce production bugs |
| Metric assertions | Prometheus/Grafana | Validate SLOs in integration tests |
| Incident replay | Incident timelines | Chaos experiments from past incidents |

**Key practices:**
- Sample production traces weekly, use as regression test suite
- Convert production 5xx responses into test cases automatically
- Derive load test weights from actual traffic distribution
- Build chaos experiments from post-incident reviews

## Step 28: Netflix Testing Culture

### Simian Army

| Monkey | What it does |
|--------|-------------|
| **Chaos Monkey** | Randomly kills production instances during business hours |
| **Chaos Kong** | Simulates entire AWS region failure |
| **Latency Monkey** | Injects network delays between services |
| **Conformity Monkey** | Finds instances not following best practices |
| **Security Monkey** | Finds security violations |
| **Doctor Monkey** | Detects unhealthy instances |
| **Janitor Monkey** | Finds unused resources |

### Chaos Kong Execution

```yaml
experiment:
  name: "chaos-kong-us-east-1"
  type: "region-failure"
  target:
    cloud: aws
    region: us-east-1
  steady_state:
    - metric: "availability"
      threshold: ">99.9%"
  actions:
    - type: "dns-failover"
      config:
        origin: "us-east-1"
        failover_to: "us-west-2"
  rollback:
    automatic: true
    conditions: ["availability < 99.0%"]
```

## Step 29: Google Testing Culture

### TAP (Test Automation Platform)

```
TAP Responsibilities:
- Schedules test execution across distributed fleet
- Aggregates results across all test shards
- Tracks flakiness metrics per test
- Enforces presubmit blocking rules
```

### Presubmit vs Post-Submit

**Presubmit (before merge):**
- Runs SMALL tests (must be < 1 minute total)
- Runs MEDIUM tests for changed components
- LARGE tests run as advisory (not blocking)

**Post-Submit (after merge):**
- Runs full test suite including LARGE tests
- Failures create bugs automatically (owner attribution)
- Rollback if post-submit fails

### Hermetic Tests

Hermetic tests are deterministic and self-contained.

**Hermeticity checklist:**
- No dependence on external network services (use stubs/fakes)
- No dependence on current time (inject clock)
- No dependence on filesystem outside test sandbox
- No shared mutable state between tests
- Tests can run in any order, any parallelism

### Flakiness Policy

- Threshold: >1% flake rate over last 1000 runs = "flaky"
- Flaky test removed from presubmit
- Bug filed automatically, assigned to last modifier
- Owner has 2 weeks to fix or delete
- Team gets 2% flakiness budget per quarter

### Testing on the Toilet (TotT) Principles

- "Test Behaviors, Not Methods" — write tests per behavior, not per method
- "Who Tests the Tests?" — mutation testing verifies test value
- "Don't Mock What You Don't Own" — wrap external APIs, mock your wrapper
- Small tests: 80%, Medium tests: 15%, Large tests: 5%

## Step 30: Spotify Squad Health Check

Source: https://engineering.atspotify.com/2014/09/16/squad-health-check/

### The 11 Health Indicators

| # | Indicator | Green | Yellow | Red |
|---|-----------|-------|--------|-----|
| 1 | **Easy to release** | Simple, push-button | Some coordination | Painful, risky |
| 2 | **Suitable process** | Process fits work | OK but could improve | Heavy, doesn't fit |
| 3 | **Tech quality** | Clean, easy to change | Some tech debt | Slows everything |
| 4 | **Value** | High user value | OK, could be better | Wrong thing |
| 5 | **Speed** | Fast feedback | Reasonable | Slow, waiting |
| 6 | **Mission** | Clear purpose | Somewhat clear | Unclear |
| 7 | **Fun** | Enjoy working | It's OK | Demotivating |
| 8 | **Learning** | Growing skills | Some opportunities | Stagnating |
| 9 | **Support** | Good org support | Some support | Lack of support |
| 10 | **Pawns or players** | Team controls destiny | Some autonomy | No say |
| 11 | **Health of codebase** | CI green, docs current | Some gaps | Flaky tests, no docs |

### Facilitation

- Book 60 minutes per team. NO managers in room.
- Individual voting (10 min) — simultaneous sticky placement
- Discussion (25 min) — focus on red/yellow indicators
- Action items (10 min) — pick 1-3, each with owner + deadline
- Run every 6-8 weeks, track trends over time

## Step 31: Observability-Driven Development

Source: Charity Majors (Honeycomb CTO) — https://charity.wtf/

### Wide Structured Events

Instead of metrics (thin counters) or logs (flat strings), use wide structured events:

```python
# ✅ WIDE STRUCTURED EVENT: one rich event per request
def handle_create_user(request):
    span = trace.get_current_span()
    span.set_attributes({
        "user.id": user_id,
        "user.plan": plan,
        "user.region": region,
        "cache.hit": False,
    })

    # ... execute business logic ...

    logger.info("request.complete",
        method="POST",
        path="/api/users",
        status_code=201,
        duration_ms=duration_ms,
        user_id=user_id,
        user_plan=plan,
        db_queries=span.get_attribute("db.query_count"),
        cache_hit=span.get_attribute("cache.hit"),
        version=get_deploy_version(),
    )
```

### Observability Checklist (PR Review)

**Must-have (block merge):**
- [ ] New endpoints have a "request.complete" wide event
- [ ] Database queries traced with query text + duration
- [ ] External service calls traced with latency + status
- [ ] Error paths emit structured events

**Should-have (flag in review):**
- [ ] Business-critical values captured in events
- [ ] Request-scoped context propagates through call stack
- [ ] No PII in events
- [ ] High-cardinality fields (user_id, request_id) for debugging

### Charity Majors' Rules

1. "If you haven't tested it in production, you haven't tested it."
2. "Observability is for understanding NEW code, not just monitoring old code."
3. "The first question is 'what changed?' — deploy frequency answers this."
4. "Dashboards are for known problems. Explore/debug tools are for unknowns."
5. "If you can't find out what your code is doing in production, your code is not ready to ship."

## Step 32: GraphQL Testing

### Query-Level Snapshot Testing

```typescript
import { MockedProvider } from '@apollo/client/testing';

const mocks = [{
  request: { query: GET_USER, variables: { id: '1' } },
  result: { data: { user: { id: '1', name: 'Alice', email: 'alice@example.com' } } },
}];

test('GET_USER query returns expected shape', async () => {
  const { result, waitForNextUpdate } = renderHook(() => useUserQuery('1'), {
    wrapper: ({ children }) => <MockedProvider mocks={mocks}>{children}</MockedProvider>,
  });
  await waitForNextUpdate();
  expect(result.current.data).toMatchSnapshot();
});
```

### Schema Linting (graphql-eslint)

```yaml
# .eslintrc.yml
overrides:
  - files: ['*.graphql']
    parser: '@graphql-eslint/graphql-parser'
    rules:
      '@graphql-eslint/naming-convention':
        - error
        - FieldDefinition: camelCase
          ObjectTypeDefinition: PascalCase
      '@graphql-eslint/description-style': error
      '@graphql-eslint/no-deprecated': warn
```

### Query Complexity Analysis

```typescript
import { getComplexity, simpleEstimator, fieldExtensionsEstimator } from 'graphql-query-complexity';

// In ApolloServer plugin
const complexity = getComplexity({
  schema,
  operationName: request.operationName,
  query: document,
  variables: request.variables,
  estimators: [fieldExtensionsEstimator(), simpleEstimator({ defaultComplexity: 1 })],
});
if (complexity > 1000) {
  throw new Error(`Query too complex: ${complexity}. Max: 1000`);
}
```

**GraphQL testing checklist:**
- [ ] Schema linted with graphql-eslint
- [ ] Query complexity limits enforced
- [ ] Snapshot tests for query response shapes
- [ ] MockedProvider tests for UI components
- [ ] Error/loading states tested
- [ ] Persisted queries allowlist for production
- [ ] Schema breaking change detection in CI

## Step 33: gRPC Testing

### grpcurl — CLI Testing

```bash
# List all services
grpcurl -plaintext localhost:50051 list

# Describe a service
grpcurl -plaintext localhost:50051 describe mypackage.UserService

# Call a unary RPC
grpcurl -plaintext -d '{"id": "123"}' \
  localhost:50051 mypackage.UserService/GetUser

# Call with metadata (auth headers)
grpcurl -plaintext \
  -H "authorization: Bearer $TOKEN" \
  -d '{"name": "Alice"}' \
  localhost:50051 mypackage.UserService/CreateUser

# Health check
grpcurl -plaintext localhost:50051 grpc.health.v1.Health/Check
# Expected: {"status": "SERVING"}
```

### ghz — Load Testing

```bash
# Basic load test
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
  -c 50 -z 30s \
  localhost:50051
```

### buf — Proto Contract Testing

```yaml
# buf.yaml
version: v2
modules:
  - path: proto
lint:
  use: [STANDARD]
breaking:
  use: [FILE]
  ignore_unstable_packages: true
```

```bash
# Lint proto files
buf lint

# Check for breaking changes against main branch
buf breaking --against '.git#branch=main'

# Generate code from protos
buf generate
```

```yaml
# GitHub Actions: proto contract testing
- uses: bufbuild/buf-setup-action@v1
- uses: bufbuild/buf-lint-action@v1
- uses: bufbuild/buf-breaking-action@v1
  with:
    against: 'https://github.com/${{ github.repository }}.git#branch=main'
```

**gRPC testing checklist:**
- [ ] grpcurl smoke tests for all RPCs
- [ ] ghz load tests with latency percentiles
- [ ] buf lint + buf breaking in CI
- [ ] Server reflection verified
- [ ] Timeout and deadline propagation tested
- [ ] Error code mapping (gRPC ↔ HTTP) verified

---

## Related Skills

  - [sdlc-cicd-pipeline](sdlc-cicd-pipeline): CI/CD pipeline design with GitHub Actions and GitLab CI. Docker multi-stage builds, caching, matrix 
  - [sdlc-observability](sdlc-observability): Observability: OpenTelemetry 2024, GenAI semantic conventions, eBPF (Cilium/Hubble/Tetragon), sideca
  - [sdlc-adversarial-review](sdlc-adversarial-review): Multi-agent PR review: 3 specialized reviewers (architecture, security, quality) run in parallel, or
