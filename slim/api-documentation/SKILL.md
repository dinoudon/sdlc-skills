---
name: api-documentation
description: "API documentation: OpenAPI 3.1, API reference generation, interactive docs (Swagger UI, Redoc, Scalar), SDK docs, documentation-as-code, changelog generation, API style guides, developer portals, testing docs against live APIs."
version: 5.0.0-slim
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [api, documentation, openapi, swagger, redoc, sdk, developer-portal, api-design, rest, graphql]
    related_skills: [sdlc-architecture-design, sdlc-developer-tooling, sdlc-testing-qa, sdlc-cicd-pipeline]
---

# API Documentation (Slim)

OpenAPI 3.1 specs, interactive docs, SDK generation, docs-as-code pipelines, developer portals, doc quality gates.

## When to Use

Trigger when user:
- Writes or reviews API documentation
- Sets up OpenAPI/Swagger specs
- Generates SDK docs or client libraries
- Builds developer portals or API references
- Validates docs against live APIs
- Creates API changelogs or style guides

---

## Step 1: OpenAPI 3.1 Spec

Core structure every API doc needs.

```yaml
openapi: 3.1.0
info:
  title: Task API
  version: 2.0.0
  description: CRUD API for task management
  contact:
    name: API Support
    email: api@example.com
  license:
    name: MIT
servers:
  - url: https://api.example.com/v2
    description: Production
  - url: https://staging-api.example.com/v2
    description: Staging
paths:
  /tasks:
    get:
      operationId: listTasks
      summary: List tasks
      description: Returns paginated list of tasks. Supports filtering by status and assignee.
      tags: [Tasks]
      parameters:
        - name: status
          in: query
          schema:
            $ref: '#/components/schemas/TaskStatus'
        - name: limit
          in: query
          schema:
            type: integer
            minimum: 1
            maximum: 100
            default: 20
        - name: cursor
          in: query
          schema:
            type: string
      responses:
        '200':
          description: Task list
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/TaskListResponse'
              example:
                data:
                  - id: "t_01H8X..."
                    title: "Write docs"
                    status: "in_progress"
                pagination:
                  next_cursor: "eyJpZCI6..."
                  has_more: true
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
    post:
      operationId: createTask
      summary: Create task
      tags: [Tasks]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateTaskRequest'
            example:
              title: "Write API docs"
              priority: "high"
              assignee_id: "u_abc123"
      responses:
        '201':
          description: Task created
          headers:
            Location:
              schema: { type: string, example: /tasks/t_01H8X... }
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '400':
          $ref: '#/components/responses/BadRequest'
components:
  schemas:
    TaskStatus:
      type: string
      enum: [todo, in_progress, done, cancelled]
    Task:
      type: object
      required: [id, title, status, created_at]
      properties:
        id:
          type: string
          readOnly: true
          example: "t_01H8X..."
        title:
          type: string
          minLength: 1
          maxLength: 200
        status:
          $ref: '#/components/schemas/TaskStatus'
        priority:
          type: string
          enum: [low, medium, high, critical]
          default: medium
        assignee_id:
          type: string
          nullable: true
        created_at:
          type: string
          format: date-time
          readOnly: true
    CreateTaskRequest:
      type: object
      required: [title]
      properties:
        title: { type: string, minLength: 1, maxLength: 200 }
        priority: { type: string, enum: [low, medium, high, critical] }
        assignee_id: { type: string }
    TaskListResponse:
      type: object
      properties:
        data: { type: array, items: { $ref: '#/components/schemas/Task' } }
        pagination: { $ref: '#/components/schemas/Pagination' }
    Pagination:
      type: object
      properties:
        next_cursor: { type: string, nullable: true }
        has_more: { type: boolean }
    ProblemDetail:
      type: object
      required: [type, title, status]
      properties:
        type: { type: string, format: uri, example: "https://api.example.com/errors/validation-error" }
        title: { type: string, example: "Validation Error" }
        status: { type: integer, example: 400 }
        detail: { type: string, example: "Field 'title' is required" }
        instance: { type: string, format: uri }
        errors:
          type: array
          items:
            type: object
            properties:
              field: { type: string }
              code: { type: string }
              message: { type: string }
  responses:
    BadRequest:
      description: Invalid request
      content:
        application/json:
          schema: { $ref: '#/components/schemas/ProblemDetail' }
    Unauthorized:
      description: Missing or invalid auth
      content:
        application/json:
          schema: { $ref: '#/components/schemas/ProblemDetail' }
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key
security:
  - BearerAuth: []
  - ApiKeyAuth: []
```

---

## Step 2: Spec Quality Checklist

- [ ] All endpoints have `operationId`, `summary`, `description`
- [ ] Every response has schema + example
- [ ] Error responses use shared `ProblemDetail` schema (RFC 7807)
- [ ] Enums in `components/schemas`, not inline
- [ ] `readOnly`/`writeOnly` on appropriate fields
- [ ] `deprecated: true` paired with sunset date
- [ ] No `additionalProperties: true` without explicit schema
- [ ] Security schemes defined and applied
- [ ] Consistent naming: camelCase JSON fields, kebab-case paths
- [ ] Cursor-based pagination (not offset)

---

## Step 3: Doc Rendering Tools

| Tool | Best For | Interactive | Customizable | Self-host |
|------|----------|-------------|--------------|-----------|
| Swagger UI | Quick dev testing | Try-it-out | Low | Yes |
| Redoc | Clean public docs | No | High | Yes |
| Scalar | Modern DX | Try-it-out | High | Yes |
| Stoplight Elements | Full portal | Try-it-out | High | Yes |
| Bump.sh | Diff + changelog | No | Low | SaaS |
| Mintlify | Full dev portal | Try-it-out | High | SaaS |

**Pick**: Scalar for interactive reference, Redoc for published docs, Mintlify for portals.

---

## Step 4: Redoc Setup

```bash
npx @redocly/cli build-docs openapi.yaml -o docs/index.html --config redoc.yaml
```

```yaml
# redoc.yaml
theme:
  colors:
    primary:
      main: "#1976d2"
  typography:
    fontSize: "15px"
    fontFamily: "Inter, sans-serif"
  sidebar:
    width: "260px"
  rightPanel:
    backgroundColor: "#1a1a2e"
```

---

## Step 5: Scalar Setup

```html
<script id="api-reference" data-url="/openapi.yaml"></script>
<script src="https://cdn.jsdelivr.net/npm/@scalar/api-reference"></script>
```

```js
// Express integration
import { apiReference } from '@scalar/express-api-reference';
app.use('/docs', apiReference({ url: '/openapi.yaml' }));
```

---

## Step 6: Docs-as-Code Pipeline

```yaml
# .github/workflows/api-docs.yml
name: API Docs
on:
  push:
    paths: ['api/**', 'docs/api/**']
    branches: [main]
  pull_request:
    paths: ['api/**']

jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: stoplightio/spectral-action@latest
        with:
          file_glob: api/openapi.yaml
          spectral_ruleset: .spectral.yaml

  diff:
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request'
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - uses: bump-sh/github-action@v1
        with:
          doc: ${{ secrets.BUMP_DOC_ID }}
          token: ${{ secrets.BUMP_TOKEN }}
          file: api/openapi.yaml

  publish:
    runs-on: ubuntu-latest
    needs: validate
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - run: npx @redocly/cli build-docs api/openapi.yaml -o public/api/index.html
      - uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

---

## Step 7: Linting with Spectral

```yaml
# .spectral.yaml
extends: ["spectral:oas"]
rules:
  operation-operationId: error
  operation-description: error
  operation-tags: error
  oas3-api-servers: warn
  no-eval-in-markdown: error
  no-script-tags-in-markdown: error
  path-params: error
  typed-enum: warn
  oas3-schema: error
  examples-required:
    given: "$.paths[*][get,post,put,patch].responses[*].content.application/json.schema"
    then:
      field: "example"
      function: truthy
    severity: warn
  info-contact:
    given: "$.info"
    then:
      field: "contact"
      function: truthy
    severity: error
```

```bash
npx @stoplight/spectral-cli lint api/openapi.yaml --ruleset .spectral.yaml
```

---

## Step 8: SDK & Client Generation

```bash
# TypeScript fetch client
npx @openapitools/openapi-generator-cli generate \
  -i api/openapi.yaml -g typescript-fetch -o sdk/typescript/src \
  --additional-properties=supportsES6=true,typescriptThreePlus=true

# Python client
npx @openapitools/openapi-generator-cli generate \
  -i api/openapi.yaml -g python -o sdk/python \
  --additional-properties=packageName=taskapi
```

```ts
// Orval config (React Query + TypeScript)
import { defineConfig } from 'orval';
export default defineConfig({
  taskapi: {
    input: { target: './api/openapi.yaml' },
    output: {
      target: './src/api/generated.ts',
      client: 'react-query',
      override: {
        mutator: { path: './src/api/client.ts', name: 'customInstance' },
      },
    },
  },
});
```

**SDK Quality Checklist:**
- [ ] Generated code has JSDoc/docstrings from spec descriptions
- [ ] Enums map to native types (not just strings)
- [ ] Error types distinct from success types
- [ ] Pagination helpers included
- [ ] Retry logic configurable

---

## Step 9: Interactive Try-It-Out

```ts
// CORS config for try-it-out panels
import cors from 'cors';
app.use(cors({
  origin: ['https://docs.example.com', 'http://localhost:3000'],
  methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'OPTIONS'],
  allowedHeaders: ['Content-Type', 'Authorization', 'X-API-Key'],
  exposedHeaders: ['X-Request-Id', 'Location'],
}));
```

**Auth in try-it-out:** Document token acquisition in `securitySchemes.description` with curl examples.

---

## Step 10: Changelog Generation

```bash
npx bump-cli deploy api/openapi.yaml --doc my-api-doc-id       # Bump.sh
npx openapi-diff old-api.yaml new-api.yaml --format markdown   # CI diff
```

**Changelog format:**
```markdown
### 2024-03-15 - v2.1.0
- **Added**: `POST /tasks/{id}/comments` - Add comment to task
- **Deprecated**: `GET /tasks?offset=` - Use cursor pagination instead

### Breaking Changes
- `status` field enum renamed `in-progress` → `in_progress`
  - Old values accepted until 2024-06-01
```

---

## Step 11: API Style Guide

```yaml
naming:
  paths: kebab-case          # /task-items not /taskItems
  query_params: snake_case   # ?created_after not ?createdAfter
  json_fields: camelCase     # {"createdAt": ...}
  headers: Title-Case        # X-Request-Id

versioning:
  strategy: url-prefix       # /v2/tasks
  deprecation_policy: 6-months
  sunset_header: true        # Sunset: Sat, 01 Jun 2025 00:00:00 GMT

pagination:
  default: cursor
  cursor_field: cursor
  limit_field: limit
  default_limit: 20
  max_limit: 100

errors:
  format: RFC 7807           # Problem Details
  codes:
    VALIDATION_ERROR: 400
    NOT_FOUND: 404
    CONFLICT: 409
    RATE_LIMITED: 429
```

---

## Step 12: Auth, Rate Limits & Webhooks

**Auth flows** — document in `securitySchemes.description`:
```yaml
BearerAuth:
  type: http
  scheme: bearer
  bearerFormat: JWT
  description: |
    1. Register at https://dashboard.example.com
    2. Create API key in Settings → API Keys
    3. Exchange for JWT:
      curl -X POST https://api.example.com/auth/token \
        -H "Content-Type: application/json" \
        -d '{"api_key": "sk_live_..."}'
    Token expires in 1 hour.
```

**Rate limits** — document in response headers + description:

| Plan | Requests/min | Burst |
|------|-------------|-------|
| Free | 60 | 10 |
| Pro | 600 | 50 |
| Enterprise | 6000 | 200 |

```yaml
# Response headers
'200':
  headers:
    X-RateLimit-Limit: { schema: { type: integer }, example: 600 }
    X-RateLimit-Remaining: { schema: { type: integer }, example: 594 }
    X-RateLimit-Reset: { schema: { type: integer }, example: 1710523200 }
'429':
  description: Rate limit exceeded
  headers:
    Retry-After: { schema: { type: integer }, description: Seconds until reset }
```

**Webhooks** — OpenAPI 3.1 native:
```yaml
webhooks:
  taskCreated:
    post:
      operationId: onTaskCreated
      summary: Task created webhook
      description: |
        At-least-once delivery. Implement idempotency with X-Webhook-Id.
        Verify signature: X-Webhook-Signature: sha256={hmac_hex}
      requestBody:
        content:
          application/json:
            schema:
              type: object
              properties:
                event: { type: string, example: "task.created" }
                timestamp: { type: string, format: date-time }
                data: { $ref: '#/components/schemas/Task' }
      responses:
        '200': { description: Webhook received }
```

**Webhook signature verification (Node.js):**
```js
const crypto = require('crypto');
const sig = crypto.createHmac('sha256', secret).update(rawBody).digest('hex');
if (`sha256=${sig}` !== req.headers['x-webhook-signature']) throw new Error('Invalid');
```

---

## Step 13: GraphQL Doc Patterns

```graphql
"""Represents a task in the system."""
type Task {
  "Unique task identifier" id: ID!
  "Task title, 1-200 characters" title: String!
  "Current status" status: TaskStatus!
  "Task priority level" priority: Priority = MEDIUM
  "Assigned user, null if unassigned" assignee: User
  "When the task was created" createdAt: DateTime!
}

enum TaskStatus { TODO, IN_PROGRESS, DONE, CANCELLED }

type Query {
  "Get a task by ID. Returns null if not found."
  task(id: ID!): Task
  "List tasks with optional filters. Cursor-based pagination."
  tasks(status: TaskStatus, limit: Int = 20, cursor: String): TaskConnection!
}
```

---

## Step 14: Testing Docs Against Live APIs

```ts
// docs-test.spec.ts - verify examples match real API
import { parseOpenAPI } from '@redocly/cli';
import { createServer } from './src/server';

const api = parseOpenAPI('api/openapi.yaml');
const server = createServer();

describe('API documentation accuracy', () => {
  for (const [path, methods] of Object.entries(api.paths)) {
    for (const [method, operation] of Object.entries(methods)) {
      if (!operation.responses?.['200']?.content) continue;
      const example = operation.responses['200'].content['application/json']?.example;
      if (!example) continue;

      it(`${method.toUpperCase()} ${path} response matches docs`, async () => {
        const res = await server.inject({ method, url: path });
        const body = JSON.parse(res.payload);
        for (const key of Object.keys(example)) {
          expect(body).toHaveProperty(key);
          expect(typeof body[key]).toBe(typeof example[key]);
        }
      });
    }
  }
});
```

---

## Step 15: Pitfalls

| Pitfall | Fix |
|---------|-----|
| Outdated docs drift from code | Generate spec from code annotations or CI diff check |
| No examples on schemas | Every response schema needs `example` field |
| Inconsistent error format | Use RFC 7807 Problem Details everywhere |
| Missing pagination docs | Document cursor format, has_more, limit bounds |
| No auth flow examples | Include curl/token-exchange examples |
| Inline enums everywhere | Extract to `components/schemas` for reuse |
| No deprecation timeline | Pair `deprecated: true` with sunset date |
| Version conflicts (3.0 vs 3.1) | Pick one version, document in `openapi` field |
| Missing CORS for try-it-out | Configure CORS or docs panel won't work |
| No webhook signatures | Always HMAC-sign and document verification |
| Stale SDK after spec change | Auto-regenerate in CI, fail if diff detected |
| Too many response examples | Focus on 200, 400, 401, 404, 429 only |

---

## Step 16: Full Documentation Checklist

### Spec Quality
- [ ] Valid OpenAPI 3.1 (passes `spectral lint`)
- [ ] All endpoints have operationId, summary, description
- [ ] All schemas in components (no inline duplicates)
- [ ] Examples on every response
- [ ] Error responses use shared ProblemDetail schema

### Security
- [ ] Auth schemes documented with token acquisition flow
- [ ] Rate limits documented per plan
- [ ] Webhook signatures documented with verification code
- [ ] CORS configured for interactive docs

### Developer Experience
- [ ] Interactive try-it-out works
- [ ] SDK/client generated for primary language
- [ ] Changelog auto-generated from spec diffs
- [ ] Migration guide for breaking changes
- [ ] Quickstart: 5 lines to first API call

### CI/CD
- [ ] Spectral linting in PR checks
- [ ] Breaking change detection on PR
- [ ] Auto-publish docs on merge to main
- [ ] SDK auto-regenerated on spec change
- [ ] Docs tested against live API

### Content
- [ ] API overview (what, auth, versioning, rate limits)
- [ ] Quickstart guide
- [ ] Endpoint reference (auto-generated from spec)
- [ ] Error code reference
- [ ] Webhook reference
- [ ] Changelog / migration guides
- [ ] Code examples in 2+ languages per endpoint

---

## Key Principles

1. **Single source of truth**: OpenAPI spec IS the docs. Generate everything from it.
2. **Examples are contracts**: CI tests that real API matches examples.
3. **Docs are code**: Version control, lint, review, auto-deploy.
4. **Errors deserve docs**: Document every error code with cause + fix.
5. **SDKs extend docs**: Generated clients are the best code examples.
6. **Breaking changes need migration paths**: Deprecate first, remove later with sunset dates.
7. **Interactive > static**: Try-it-out panels beat curl examples.
