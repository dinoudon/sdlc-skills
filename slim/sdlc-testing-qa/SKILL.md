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

---
name: sdlc-testing-qa
description: "Test pyramid (unit/integration/e2e), TDD/BDD, property-based testing, mutation testing, contract testing, chaos engineering, performance testing (k6/Locust), security testing (SAST/DAST), accessibility testing, AI-assisted test generation, serverless testing patterns, ML model testing, API contract testing, database testing, concurrency testing, observability-driven testing, visual regression testing, test data management. Includes Google testing culture, 
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
## Step 2: Unit Tests
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
## Step 3: Integration Tests
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
## Step 4: E2E Tests
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
## Step 5: TDD
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
```
## Step 6: Property-Based Testing
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
## Step 7: Mutation Testing
| Tool | Language | Notes |
|------|----------|-------|
| PIT (pitest) | Java/JVM | Most mature. Supports incremental analysis, JUnit 5 |
| Stryker | JS/TS/C# | First-class Jest/Vitest support, dashboard for tracking |
| mutmut | Python | Works with pytest |
| Mull | C/C++ | LLVM-based |
| Score | Interpretation |
|-------|---------------|
| >80% | Excellent — very strong test suite |
| 70-80% | Strong — acceptable for most projects |
| 50-70% | Weak — significant test gaps exist |
| <50% | Poor — tests barely verify behavior |
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
## Step 8: Contract Testing
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
## Step 9: Chaos Engineering
Source: https://principlesofchaos.org/
1. Build hypothesis around steady state behavior
2. Introduce real-world events (server failures, network partitions, resource exhaustion)
3. Run experiments in production (or staging with prod-like traffic)
4. Automate experiments to run continuously
5. Minimize blast radius
| Feature | LitmusChaos | Chaos Mesh | AWS FIS |
|---------|-------------|------------|---------|
| CNCF Status | Graduated | Incubating | N/A |
| Cloud targets | AWS/GCP/Azure | Primarily K8s | AWS only |
| Dashboard | Litmus Portal | Built-in UI | AWS Console |
| JVM chaos | Via plugins | Built-in | N/A |
| Best for | Multi-cloud chaos | K8s-focused chaos | AWS chaos |
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
```
## Step 10: Performance Testing
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

```
## Step 11: Security Testing
```bash
semgrep --config=auto src/
bandit -r src/ -f json
```
## Step 12: Accessibility Testing
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
```
## Step 14: Test Coverage
```bash
# Python
pytest --cov=src --cov-report=html

# JavaScript
npx jest --coverage

# Go
go test -coverprofile=coverage.out ./...
```
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
## Step 17: Visual Regression Testing
| Tool | Best For | Notes |
|------|----------|-------|
| Percy (BrowserStack) | CI/CD integration | Snapshot diffs in PR, approve/reject |
| Chromatic | Storybook projects | First-class Storybook integration |
| BackstopJS | Standalone scripts | OnBefore/OnReady scripts |
| Playwright `toHaveScreenshot()` | Minimal setup | Built-in, no external service |
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
## Step 18: Fuzz Testing
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
```typescript
// Factory pattern
const createUser = (overrides = {}) => ({
  id: faker.string.uuid(),
  email: faker.internet.email(),
  name: faker.person.fullName(),
  ...overrides,
});
```
## Step 20: Flaky Test Management
Source: https://testing.googleblog.com/2016/05/flaky-tests-at-google-and-how-we.html
**Detection:** Track pass/fail history per test across N runs. Google: ~1.5% of tests flaky at any time.
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
```
1. SCAFFOLD — LLM generates test suite from function signature + docstring
2. RUN      — execute generated tests, collect failures
3. ANALYZE  — feed failures back to LLM with error output
4. REFINE   — LLM fixes failing tests, adds missing edge cases
5. VALIDATE — human reviews for meaningful assertions
```
## Step 22: Serverless Testing Patterns
```bash
pip install aws-sam-cli

# Invoke function locally
sam local invoke ProcessOrder --event events/order.json

# Start local API Gateway
sam local start-api --port 3000

# Run tests against local Lambda
sam local start-lambda --port 3001
```
## Step 23: ML Model Testing Patterns
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
## Step 24: API Contract Testing — Advanced
```python
# Schemathesis — property-based API testing from OpenAPI spec
import schemathesis

schema = schemathesis.from_url("http://localhost:8080/openapi.json")

@schema.parametrize()
def test_api_conformance(case):
    response = case.call()
    case.validate_response(response)
```
## Step 25: Database Testing Patterns
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
```
## Step 26: Concurrency Testing
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
## Step 27: Observability-Driven Testing
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

```
## Step 28: Netflix Testing Culture
| Monkey | What it does |
|--------|-------------|
| **Chaos Monkey** | Randomly kills production instances during business hours |
| **Chaos Kong** | Simulates entire AWS region failure |
| **Latency Monkey** | Injects network delays between services |
| **Conformity Monkey** | Finds instances not following best practices |
| **Security Monkey** | Finds security violations |
| **Doctor Monkey** | Detects unhealthy instances |
| **Janitor Monkey** | Finds unused resources |
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
```
## Step 29: Google Testing Culture
```
TAP Responsibilities:
- Schedules test execution across distributed fleet
- Aggregates results across all test shards
- Tracks flakiness metrics per test
- Enforces presubmit blocking rules
```
## Step 30: Spotify Squad Health Check
Source: https://engineering.atspotify.com/2014/09/16/squad-health-check/
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
- Book 60 minutes per team. NO managers in room.
- Individual voting (10 min) — simultaneous sticky placement
- Discussion (25 min) — focus on red/yellow indicators
- Action items (10 min) — pick 1-3, each with owner + deadline
- Run every 6-8 weeks, track trends over time
## Step 31: Observability-Driven Development
Source: Charity Majors (Honeycomb CTO) — https://charity.wtf/
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
```
## Step 32: GraphQL Testing
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
## Step 33: gRPC Testing
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
```