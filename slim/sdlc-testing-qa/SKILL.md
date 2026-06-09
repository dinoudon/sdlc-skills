---
name: sdlc-testing-qa
description: "Test pyramid, TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance testing (k6/Locust), security testing (SAST/DAST), accessibility testing, AI-assisted test generation, serverless testing patterns, ML model testing, API contract testing, database testing, concurrency testing, observability-driven testing, visual regression testing, test data management."
version: 4.8.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, testing, tdd, bdd, playwright, pytest, k6, security, accessibility, contract-testing, chaos-engineering, mutation-testing, property-based, ai-test-generation, serverless-testing, ml-testing, visual-regression, performance-testing]
    related_skills: [sdlc-cicd-pipeline, sdlc-deployment, test-driven-development, security-review-owasp]
---

# Testing & Quality Assurance

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

```
        /  E2E  \\        few, expensive, critical flows only
       /---------\\
      / Integration\\     BULK of tests — test components together
     /---------------\\
    /     Unit Tests   \\ many, fast — only for complex logic
   /---------------------\\
       Static Analysis    cheapest — catches most bugs
```

- "Write tests. Not too many. Mostly integration."
- Test behavior, not implementation details

### Test Size Classification (Google)

| Size | Runtime | Resources |
|------|---------|-----------|
| Small | < 1 min | 1 machine, no network |
| Medium | < 5 min | 1 machine, localhost |
| Large | Any | Multiple machines |

### Key Principles

- **Flaky tests worse than no tests** — erode trust
- **Hermetic tests** — no external deps, deterministic
- **Don't mock what you don't own** — wrap external APIs

## Step 2: Unit Tests

### pytest
```python
def test_calculate_discount():
    assert calculate_discount(100, 0.1) == 90.0

@pytest.mark.parametrize("price,discount,expected", [
    (100, 0.1, 90.0), (200, 0.25, 150.0),
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

### Cycle: Red -> Green -> Refactor
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
def test_add_to_cart(): pass

@given('an empty cart')
def empty_cart(cart): cart.clear()

@when(parsers.parse('I add a "{item}" with price {price:f}'))
def add_item(cart, item, price): cart.add(item, price)
```

## Step 6: Property-Based Testing

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

### fast-check (JavaScript/TypeScript)
```typescript
import fc from 'fast-check';

fc.assert(
  fc.property(fc.array(fc.integer()), arr =>
    JSON.parse(JSON.stringify(arr)).length === arr.length
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

## Step 7: Mutation Testing

### Tools

| Tool | Language | Notes |
|------|----------|-------|
| Stryker | JS/TS/C# | First-class Jest/Vitest support, dashboard |
| PIT (pitest) | Java/JVM | Most mature, incremental analysis |
| mutmut | Python | Works with pytest |

### Mutation Score Thresholds

| Score | Interpretation |
|-------|---------------|
| >80% | Excellent |
| 70-80% | Strong |
| 50-70% | Weak — significant gaps |
| <50% | Poor — tests barely verify behavior |

### Stryker Config
```json
{
  "mutate": ["src/**/*.ts"],
  "testRunner": "vitest",
  "coverageAnalysis": "perTest",
  "incremental": true
}
```

Incremental: First run full mutation, subsequent runs only mutate changed files. CI: full on main, incremental on PRs.

### Surviving Mutants — 3 Categories

1. **Weak test** — add assertion for mutated behavior
2. **Equivalent mutant** — identical behavior, safe to ignore
3. **Redundant code** — dead code revealed, remove it

### Key Mutators

| Mutator Type | Example |
|-------------|---------|
| Conditionals | `>` -> `>=`, `==` -> `!=` |
| Return values | `return x` -> `return null` |
| Void method calls | Remove method calls |
| Math | `+` -> `-`, `*` -> `/` |

## Step 8: Contract Testing

### Pact
Source: https://docs.pact.io/

```typescript
// Consumer test
const interaction = {
  state: 'a user exists',
  uponReceiving: 'a request for user',
  withRequest: { method: 'GET', path: '/users/1' },
  willRespondWith: { status: 200, body: { id: 1, name: 'Alice' } }
};
```

### Bi-Directional Contract Testing (Pactflow)

```
Consumer (Pact)  --> Pactflow <--  Provider (OpenAPI spec)
   {GET /users/1       matches       paths:
     response:                           /users/{id}:
       200                                 get:
       body: id, name                        responses: 200
                                            schema: {id, name}
```

Use when: Provider can't write Pact tests (legacy, different lang). Provider already has OpenAPI spec.

### Can-I-Deploy Gate
```bash
npx pact-broker can-i-deploy --pacticipant UserService --version $GIT_SHA --to production
```

## Step 9: Chaos Engineering

Source: https://principlesofchaos.org/

### Principles
1. Build hypothesis around steady state behavior
2. Introduce real-world events (failures, partitions, exhaustion)
3. Run experiments in production (or staging with prod-like traffic)
4. Automate experiments to run continuously
5. Minimize blast radius

### Tools

| Feature | LitmusChaos | Chaos Mesh | AWS FIS |
|---------|-------------|------------|---------|
| CNCF Status | Graduated | Incubating | N/A |
| Cloud targets | AWS/GCP/Azure | Primarily K8s | AWS only |
| Best for | Multi-cloud chaos | K8s-focused chaos | AWS chaos |

### LitmusChaos Example
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

ChaosHub: https://hub.litmuschaos.io/ — 60+ pre-built experiments

### Game Day Metrics

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

### Locust (Python)
```python
from locust import HttpUser, task, between

class APIUser(HttpUser):
    wait_time = between(0.5, 2)

    @task(5)
    def get_users(self):
        self.client.get('/users')

    @task(1)
    def create_user(self):
        self.client.post('/users', json={"name": "test"})
```

### Load Testing Patterns

| Pattern | Duration | Key Metric |
|---------|----------|------------|
| Smoke | 1-2 min, few VUs | Error rate |
| Load | 15-30 min | p95 latency < SLA |
| Stress | 15-30 min | Where does it break? |
| Soak | 4-24 hours | Memory growth, latency drift |
| Spike | Short bursts | Recovery time |
| Breakpoint | Until failure | Max RPS before errors |

### Tool Comparison

| Feature | k6 | Locust | Gatling | Artillery |
|---------|-----|--------|---------|-----------|
| Language | JS (Go engine) | Python | Scala/Kotlin/Java | YAML/JS |
| Distributed | k6-operator (k8s) | Native | Built-in | Fargate plugin |
| Best for | Dev teams, CI | Python shops | Enterprise, JVM | Quick YAML setup |

## Step 11: Security Testing

### SAST / DAST / SCA / Secrets
```bash
# SAST
semgrep --config=auto src/
bandit -r src/ -f json

# DAST
zap-cli quick-scan http://localhost:8080
nuclei -u https://target.com -t cves/

# SCA
snyk test
npm audit --production

# Secrets
trufflehog git file://./ --only-verified
gitleaks detect --source . -v
```

### SAST Tools

| Tool | Type | Strengths |
|------|------|-----------|
| Semgrep | Pattern-based | Custom rules, fast, 2000+ rulesets |
| CodeQL | Semantic analysis | Deep dataflow, zero-config |
| SonarQube | Multi-language | Quality gates, tech debt tracking |
| Bandit | Python SAST | Low false positives |

### API Fuzzing (Schemathesis)
```python
import schemathesis

schema = schemathesis.from_url("https://api.example.com/openapi.json")

@schema.parametrize()
def test_api(case):
    response = case.call()
    case.validate_response(response)
```

### Pipeline Integration
```yaml
security-scan:
  jobs:
    sast:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - uses: returntocorp/semgrep-action@v1
          with:
            config: "p/owasp-top-ten p/security-audit p/secrets"
    dast:
      needs: [deploy-staging]
      runs-on: ubuntu-latest
      steps:
        - uses: zaproxy/action-baseline@v0.12.0
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

### Accessibility Checklist

- [ ] axe-core in unit tests and E2E
- [ ] Lighthouse CI a11y score >= 95%
- [ ] Keyboard navigation verified
- [ ] Color contrast WCAG AA (4.5:1 normal, 3:1 large)
- [ ] Touch target >= 44x44 CSS pixels
- [ ] Screen reader testing for critical flows
- [ ] Heading hierarchy logical (h1->h2->h3)

## Step 13: Test Architecture — Ports & Adapters

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
```

- Domain logic tested via unit tests (pure functions, no I/O)
- Tests use fake adapters — no real DB/network in unit tests
- Integration tests verify real adapters against real infra
- **Humble Object pattern**: thin adapter layer, logic in testable core

## Step 14: Test Coverage

```bash
pytest --cov=src --cov-report=html    # Python
npx jest --coverage                    # JavaScript
go test -coverprofile=coverage.out ./...  # Go
```

Targets: Statements > 80%, Branches > 75%, Functions > 80%.

## Step 15: Verification Loop

1. **Build** — project compiles
2. **Lint** — no violations
3. **Type Check** — no type errors
4. **Unit Tests** — all pass with coverage
5. **Integration/E2E** — critical paths verified

If ANY phase fails -> STOP and fix.

## Step 16: Snapshot Testing

```typescript
expect(component).toMatchSnapshot();
expect(value).toMatchInlineSnapshot();  // Embeds in test file
```

- Keep snapshots small, focused
- Mock non-deterministic data (dates, IDs) before snapshotting
- Treat .snap files as code — review diffs in PRs

## Step 17: Visual Regression Testing

### Tools

| Tool | Best For | Notes |
|------|----------|-------|
| Percy | CI/CD integration | Snapshot diffs in PR |
| Chromatic | Storybook projects | First-class Storybook support |
| Playwright `toHaveScreenshot()` | Minimal setup | Built-in, no external service |

### Playwright Visual Comparison
```typescript
test('homepage visual check', async ({ page }) => {
  await page.goto('/');
  await expect(page).toHaveScreenshot('homepage.png', {
    maxDiffPixelRatio: 0.01,
    animations: 'disabled',
  });
});
```

### Threshold Tuning

| Threshold | Use Case |
|-----------|----------|
| 0 (exact) | Critical UI (medical, financial) |
| 0.1 | High-fidelity apps |
| 0.2 | Standard apps — good balance |
| 0.3 | Tolerant — dynamic fonts, anti-aliasing |

### Masking Dynamic Content
```typescript
await expect(page).toHaveScreenshot('dashboard.png', {
  mask: [page.locator('.timestamp'), page.locator('.user-avatar')],
  maskColor: '#FF00FF',
});
```

## Step 18: Fuzz Testing

| Tool | Type | Notes |
|------|------|-------|
| AFL++ | Coverage-guided | Mutates based on coverage |
| libFuzzer | In-process | Define `LLVMFuzzerTestOneInput()` |
| OSS-Fuzz | Continuous | Combines AFL++, libFuzzer, honggfuzz |

- Provide seed corpus (valid inputs) for faster coverage
- Use sanitizer builds: `-fsanitize=address,undefined`
- Target parsers, decoders, protocol handlers

## Step 19: Test Data Management

### Factory Pattern (Faker)
```typescript
const createUser = (overrides = {}) => ({
  id: faker.string.uuid(),
  email: faker.internet.email(),
  name: faker.person.fullName(),
  ...overrides,
});
```

### Factory Boy (Python)
```python
class UserFactory(factory.Factory):
    class Meta:
        model = User
    name = factory.Faker('name')
    email = factory.LazyAttribute(lambda o: f"{o.name.lower().replace(' ', '.')}@example.com")
    is_active = True

    class Params:
        admin = factory.Trait(role="admin")

user = UserFactory()
admin = UserFactory(admin=True)
batch = UserFactory.create_batch(50)
```

### Transaction Rollback Isolation
```python
@pytest.fixture
def db_session(postgres):
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
| Transaction rollback | Fastest | Per-test | Unit/integration |
| Truncate between tests | Fast | Per-test | Committed data tests |
| Fresh database per test | Slow | Perfect | Migration tests |

### Test Data Checklist
- [ ] Synthetic data for all entity types
- [ ] Seeded mode for CI reproducibility
- [ ] PII detection in CI for fixtures
- [ ] Transaction rollback for cleanup

## Step 20: Flaky Test Management

Google: ~1.5% of tests flaky at any time.

**Workflow:** DETECT (pass rate < 95%) -> QUARANTINE (mark @flaky) -> DIAGNOSE (reproduce 100x) -> FIX (run 1000x) -> DELETE (if unfixed 2 weeks)

**Policy:** >1% flake rate over 1000 runs = "flaky". Removed from presubmit. Owner has 2 weeks to fix or delete. Team gets 2% flakiness budget/quarter.

## Step 21: AI-Assisted Test Generation

### Scaffold-and-Refine Pattern

```
1. SCAFFOLD — LLM generates tests from signature + docstring
2. RUN      — execute, collect failures
3. ANALYZE  — feed failures + errors back to LLM
4. REFINE   — LLM fixes, adds edge cases
5. VALIDATE — human reviews for meaningful assertions
```

Typical LLM first-pass: **70-80% pass rate**. 2-3 rounds to 90%+.

### Anti-Patterns

| Anti-Pattern | Fix |
|---|---|
| Trivial assertions (`assert result is not None`) | Assert specific values |
| Happy-path only | Add boundary, empty, negative cases |
| No adversarial inputs | Add injection, XSS, overflow |
| Overmocking | Mock only external boundaries |

### Prompt Template
```
Write comprehensive {framework} tests for: {function_signature}
Docstring: {docstring} | Examples: {examples}
Requirements: happy path, edge cases, error cases, boundary values,
adversarial inputs (injection, overflow), parametrized where appropriate.
```

### Tools

| Tool | Language | Notes |
|------|----------|-------|
| CodiumAI / Qodo | Multi | Behavior analysis, IDE + CLI |
| Diffblue Cover | Java | Autonomous JUnit generation |
| GitHub Copilot | Multi | `/tests` slash command |
| Hypothesis Ghostwriter | Python | `hypothesis write my_module.my_function` |

## Step 22: Serverless Testing Patterns

### AWS Lambda — SAM Local
```bash
sam local invoke ProcessOrder --event events/order.json
sam local start-api --port 3000
```

```python
@pytest.fixture
def lambda_client():
    return boto3.client('lambda', endpoint_url='http://localhost:3001')

def test_process_order(lambda_client):
    response = lambda_client.invoke(
        FunctionName='ProcessOrder',
        Payload=json.dumps({"orderId": "123"})
    )
    body = json.loads(response['Payload'].read())
    assert body['status'] == 'confirmed'
```

### Testing Strategy

```
Layer 1: UNIT TESTS — handler logic in isolation, mock event/context
Layer 2: LOCAL INTEGRATION — SAM local / Miniflare, real event payloads
Layer 3: CLOUD INTEGRATION — staging deploy, verify IAM, cold starts, timeouts
```

**Pitfalls:**
- Don't test only locally — IAM, VPC, timeouts only in cloud
- Don't skip cold start testing — 10-100x slower first invocation
- Don't assume local == cloud — DynamoDB Local behaves differently

## Step 23: API Contract Testing — Advanced

### Backward Compatibility
```bash
oasdiff breaking openapi-v1.yaml openapi-v2.yaml --fail-on ERR
```

**Rules:**
1. Never remove a field from a response
2. Never add a required field to a request
3. Never change a field type
4. Always add optional fields with defaults
5. Deprecate first, remove after N versions

### CI Integration
```yaml
on:
  pull_request:
    paths: ['openapi/**']
jobs:
  check-breaking:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: |
          git show origin/main:openapi/spec.yaml > /tmp/base.yaml
          oasdiff breaking /tmp/base.yaml openapi/spec.yaml --fail-on ERR
```

## Pitfalls

1. Don't write more E2E than unit tests — E2E slow and brittle
2. Don't mock everything in integration tests — use Testcontainers
3. Don't ignore flaky tests — quarantine and fix
4. Don't skip property-based tests for parsers — find edge cases
5. Don't run k6 against production without coordination
6. Don't rely solely on SAST — combine with DAST
7. Don't test a11y only automated — use screen readers
8. Don't treat 100% coverage as target — focus critical paths
9. Don't skip contract tests for microservices
10. Don't skip mutation testing — coverage != test quality

---
