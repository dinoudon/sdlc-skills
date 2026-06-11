---
name: api-documentation
version: 5.0.0
description: "API documentation: OpenAPI 3.1, reference generation, SDK generation, docs-as-code, style guides, contract testing, GraphQL, AsyncAPI, developer portals."
author: Dinoudon
tags: [api-documentation, openapi, swagger, redoc, sdk-generation, docs-as-code, spectral, contract-testing, graphql, asyncapi]
related_skills: [api-design, sdlc-architecture-design, sdlc-developer-tooling]
---

# API Documentation

Comprehensive guide to creating, maintaining, and automating API documentation.

## When to Use

- Building REST, GraphQL, gRPC, or WebSocket APIs
- Setting up developer portals or API catalogs
- Automating SDK generation from API specs
- Linting and testing API specifications
- Creating interactive API documentation

## Steps

### 1. OpenAPI 3.1 Spec Authoring

OpenAPI 3.1.1 (Oct 2024) aligns fully with JSON Schema 2020-12.

```yaml
openapi: 3.1.1
info:
  title: User API
  version: 1.0.0
  description: Manage users
  contact:
    name: API Support
    email: api@example.com
  license:
    name: MIT
servers:
  - url: https://api.example.com/v1
    description: Production
  - url: https://staging-api.example.com/v1
    description: Staging
paths:
  /users:
    get:
      operationId: listUsers
      summary: List all users
      tags: [Users]
      parameters:
        - name: limit
          in: query
          schema:
            type: integer
            default: 20
            maximum: 100
        - name: cursor
          in: query
          schema:
            type: string
      responses:
        '200':
          description: User list
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/UserList'
              examples:
                default:
                  value:
                    data:
                      - id: "usr_123"
                        name: "Alice"
                        email: "alice@example.com"
                    has_more: false
        '401':
          $ref: '#/components/responses/Unauthorized'
    post:
      operationId: createUser
      summary: Create a user
      tags: [Users]
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
      responses:
        '201':
          description: User created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
components:
  schemas:
    User:
      type: object
      required: [id, name, email]
      properties:
        id:
          type: string
          example: "usr_123"
        name:
          type: string
          example: "Alice"
        email:
          type: string
          format: email
        created_at:
          type: string
          format: date-time
    CreateUserRequest:
      type: object
      required: [name, email]
      properties:
        name:
          type: string
        email:
          type: string
          format: email
    UserList:
      type: object
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/User'
        has_more:
          type: boolean
        cursor:
          type: string
  responses:
    Unauthorized:
      description: Authentication required
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
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

Key changes from 3.0:
- Full JSON Schema compatibility (no `nullable` keyword; use `type: ["string", "null"]`)
- Webhooks section (top-level)
- `$id` support in schemas

Best practices:
- Use `$ref` to components for reusability
- Tag operations for grouping
- Define security schemes precisely
- Use examples (not just schema) for request/response
- Keep operationIds unique and descriptive
- One resource per path, standard methods (GET/POST/PUT/PATCH/DELETE)

Pitfalls:
- Missing examples → docs show only schema, not real data
- Inconsistent naming (camelCase vs snake_case)
- No error responses documented
- Missing pagination parameters

Source: https://spec.openapis.org/oas/v3.1.1.html

---

### 2. API Reference Generation

| Tool | Type | Strengths | Best For |
|------|------|-----------|----------|
| Redoc | Open-source | Stripe-like 2-3 panel layout, auto code samples | Public APIs |
| Swagger UI | Open-source | Most widely deployed, bundled with Editor | Internal/dev APIs |
| Stoplight Elements | Open-source | Embeddable React component, customizable | API portals |
| ReadMe | SaaS | Markdown guides + API reference + analytics | Developer portals |
| Scalar | Open-source | Modern UI, interactive playground | Quick setup |

Redoc setup:
```html
<!DOCTYPE html>
<html>
<head>
  <title>API Docs</title>
  <redoc spec-url="/openapi.yaml"></redoc>
  <script src="https://cdn.redoc.ly/redoc/latest/bundles/redoc.standalone.js"></script>
</head>
<body><redoc spec-url="/openapi.yaml"></redoc></body>
</html>
```

Scalar setup:
```html
<script src="https://cdn.scalar.com/api-reference.scalar.js"></script>
<script>
  Scalar.createApiReference('#app', { url: '/openapi.yaml' })
</script>
```

Comparison source: https://apisyouwonthate.com/blog/top-5-best-api-docs-tools/

---

### 3. Interactive Docs

**Postman Collections:**
- Import OpenAPI → auto-generates collection with examples
- Pre-request scripts for auth
- Newman CLI for CI: `newman run collection.json`
- URL: https://www.postman.com/

**Bruno:**
- Open-source, Git-native (collections stored as files)
- No cloud sync by default (privacy-first)
- URL: https://github.com/usebruno/bruno

**Insomnia:**
- Open-source by Kong
- Supports OpenAPI, GraphQL, gRPC
- URL: https://insomnia.rest

Best practice: provide both static docs (Redoc) AND interactive (Postman collection).

---

### 4. SDK Generation

| Tool | Languages | Strengths |
|------|-----------|-----------|
| openapi-generator | 50+ (Java, Python, TS, Go, Rust, C#, Ruby, PHP, Swift, Kotlin) | Most languages, community-maintained |
| Kiota (Microsoft) | C#, Go, Java, PHP, Python, Ruby, TS, Swift, Dart | Lightweight, selective generation |
| Fern | TS, Python, Java, Go, Ruby, PHP, C#, Swift, Rust | Idiomatic output, built-in OAuth/retries |
| Swagger Codegen | Java, Python, TS, Go, Ruby, PHP, C# | Original generator (SmartBear) |

openapi-generator:
```bash
# Generate Python client
openapi-generator generate \
  -i openapi.yaml \
  -g python \
  -o ./sdk/python \
  --additional-properties=packageName=example_sdk

# Generate TypeScript client
openapi-generator generate \
  -i openapi.yaml \
  -g typescript-fetch \
  -o ./sdk/typescript
```

Kiota:
```bash
kiota generate \
  -l TypeScript \
  -c UserClient \
  -n Example.Sdk \
  -d openapi.yaml \
  -o ./sdk/typescript
```

Template customization:
```bash
# Extract templates for customization
openapi-generator author template -g python -o ./templates

# Use custom templates
openapi-generator generate \
  -i openapi.yaml \
  -g python \
  -t ./templates \
  -o ./sdk/python
```

URL: https://openapi-generator.tech/docs/generators/

---

### 5. Changelog & Versioning

**Versioning strategies:**
| Strategy | Example | Pros | Cons |
|----------|---------|------|------|
| URI path | `/v1/users` | Simple, cacheable | URL changes break clients |
| Header | `Accept: application/vnd.api.v2+json` | Clean URLs | Harder to test in browser |
| Date-based | `2024-01-01` (Stripe) | Granular, predictable | More versions to maintain |
| Query param | `/users?api-version=2024-01-01` | Easy to test | Messy URLs |

**Deprecation headers:**
```http
HTTP/1.1 200 OK
Deprecation: true
Sunset: Sat, 01 Jun 2025 00:00:00 GMT
Link: <https://api.example.com/v2/users>; rel="successor-version"
```

**Changelog automation with Bump.sh:**
```yaml
# .github/workflows/api-docs.yml
name: API Docs
on:
  push:
    paths: ['api/openapi.yaml']
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: bump-sh/github-action@v1
        with:
          doc: <doc-id>
          token: ${{secrets.BUMP_TOKEN}}
          file: api/openapi.yaml
```

**Breaking change detection:**
```bash
# oasdiff - detect breaking changes
oasdiff breaking openapi-v1.yaml openapi-v2.yaml
```

Source: https://www.gravitee.io/blog/api-versioning-best-practices

---

### 6. Multi-Language Code Examples

**Redocly x-codeSamples:**
```yaml
paths:
  /users:
    get:
      x-codeSamples:
        - lang: curl
          source: |
            curl https://api.example.com/v1/users \
              -H "Authorization: Bearer sk_xxx"
        - lang: python
          source: |
            import requests
            resp = requests.get(
              "https://api.example.com/v1/users",
              headers={"Authorization": "Bearer sk_xxx"}
            )
            print(resp.json())
        - lang: javascript
          source: |
            const resp = await fetch("https://api.example.com/v1/users", {
              headers: { Authorization: "Bearer sk_xxx" }
            });
            const data = await resp.json();
```

**Auto-generation tools:**
| Tool | Approach | Languages |
|------|----------|-----------|
| Redocly | x-codeSamples extension | curl, Python, JS, Go, Java |
| Speakeasy | x-codeSamples + SDK snippets | 10+ |
| Stainless | Decorated OpenAPI | Python, TS, Go, Java |
| Fern | SDK-tied snippets | 9+ |

Source: https://www.speakeasy.com/openapi/guides/x-codesamples

---

### 7. API Style Guides

**Google Cloud API Design Guide:**
- Resource-oriented design (nouns, not verbs)
- Standard methods: List, Get, Create, Update, Delete
- Custom methods via `:action` suffix
- Naming: camelCase for fields, snake_case in URLs
- Pagination: `page_token` + `next_page_token`
- Filtering: `filter` query parameter
- URL: https://docs.cloud.google.com/apis/design

**Microsoft REST API Guidelines:**
- URL: https://github.com/microsoft/api-guidelines
- Covers: naming, collections, errors, versioning, long-running ops, OData
- PascalCase for property names

**Stripe API Design:**
- Resource-oriented REST, form-encoded requests, JSON responses
- Date-based versioning (YYYY-MM-DD)
- Expandable objects: `expand[]=customer`
- Idempotency keys on mutations
- Excellent "one resource per page" doc layout
- URL: https://docs.stripe.com/api

**Enforce with Spectral:**
```yaml
# .spectral.yaml
extends: ["spectral:oas"]
rules:
  operation-operationId: error
  operation-description: warn
  oas3-api-servers: error
  path-params: error
  operation-tags: warn
```

---

### 8. Auth Documentation

**Methods comparison:**
| Method | Security | Complexity | Use Case |
|--------|----------|------------|----------|
| API Key | Low | Simple | Public APIs, rate limiting |
| OAuth2 Auth Code | High | Complex | Web apps with user consent |
| OAuth2 PKCE | High | Medium | SPAs, mobile apps |
| OAuth2 Client Credentials | High | Simple | Service-to-service |
| JWT Bearer | High | Medium | Stateless auth |

**Document per flow:**
1. Overview diagram (sequence)
2. Step-by-step instructions
3. Code examples in multiple languages
4. Error handling (401, 403, token expired)
5. Token refresh flow

**Rate limit documentation:**
```yaml
# In OpenAPI spec
paths:
  /users:
    get:
      x-ratelimit:
        requests: 100
        period: 60s
        burst: 20
      responses:
        '429':
          description: Rate limit exceeded
          headers:
            X-RateLimit-Limit:
              schema: { type: integer }
            X-RateLimit-Remaining:
              schema: { type: integer }
            Retry-After:
              schema: { type: integer }
```

---

### 9. Spectral Linting

```yaml
# .spectral.yaml
extends: ["spectral:oas"]
rules:
  # Enforce operation IDs
  operation-operationId: error
  
  # Require descriptions
  operation-description:
    severity: warn
    given: "$.paths.*[get,post,put,patch,delete]"
    then:
      field: description
      function: truthy
  
  # Enforce naming conventions
  path-kebab-case:
    given: "$.paths"
    then:
      field: "@key"
      function: pattern
      functionOptions:
        match: "^(/[a-z0-9\\-{}]+)+$"
  
  # Require examples
  oas3-valid-media-example: error
  
  # Custom: require contact info
  api-contact:
    given: "$.info"
    then:
      field: contact
      function: truthy
```

CI integration:
```yaml
# .github/workflows/lint-api.yml
name: Lint API Spec
on: [pull_request]
jobs:
  spectral:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: stoplightio/spectral-action@latest
        with:
          file_glob: 'api/openapi.yaml'
```

Source: https://github.com/stoplightio/spectral

---

### 10. Contract Testing

| Tool | Approach | Best For |
|------|----------|----------|
| Pact | Consumer-driven | Microservice contracts |
| Dredd | Spec-first | Implementation matches docs |
| Schemathesis | Property-based/fuzz | Finding edge cases |

**Pact workflow:**
```javascript
// Consumer test
const { Pact } = require('@pact-foundation/pact');

const provider = new Pact({ consumer: 'UserService', provider: 'UserAPI' });

beforeAll(() => provider.setup());
afterAll(() => provider.finalize());

it('gets a user', async () => {
  await provider.addInteraction({
    state: 'user exists',
    uponReceiving: 'a request for a user',
    withRequest: { method: 'GET', path: '/users/usr_123' },
    willRespondWith: {
      status: 200,
      body: { id: 'usr_123', name: 'Alice' }
    }
  });
  
  const user = await getUser('usr_123');
  expect(user.name).toBe('Alice');
});
```

**Schemathesis:**
```bash
# Fuzz test from OpenAPI spec
schemathesis run https://api.example.com/openapi.json \
  --checks all \
  --hypothesis-max-examples=100
```

Source: https://docs.pact.io/ | https://schemathesis.readthedocs.io/

---

### 11. Docs-as-Code

**Platform comparison:**
| Platform | Type | Git Integration | Strengths |
|----------|------|-----------------|-----------|
| Mintlify | SaaS | PR-based | AI-native, auto-generates from OpenAPI |
| Fern | SaaS | Git | SDK + docs from same spec |
| Bump.sh | SaaS | CI integration | Changelog + breaking change detection |
| Docusaurus | Open-source | Git | React-based, Meta-backed |
| MkDocs Material | Open-source | Git | Python ecosystem, fast |
| Redocly | SaaS/OSS | Git | OpenAPI-first |
| Scalar | Open-source | Any | Modern UI, interactive playground |

**Workflow pattern:**
```
repo/
  api/
    openapi.yaml          # API spec
    spectral.yaml         # Linting rules
  docs/
    getting-started.md    # Guides
    authentication.md
    rate-limits.md
  sdk/
    python/               # Generated SDKs
    typescript/
  .github/
    workflows/
      api-docs.yml        # CI: lint + build + deploy
```

**CI pipeline:**
```yaml
name: API Docs
on:
  push:
    branches: [main]
    paths: ['api/**', 'docs/**']
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: stoplightio/spectral-action@latest
        with:
          file_glob: 'api/openapi.yaml'
  build:
    needs: lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npx @mintlify/mintlify build
      - uses: peaceiris/actions-gh-pages@v3
        with:
          publish_dir: ./dist
```

Source: https://buildwithfern.com/post/docs-as-code-solutions-api-teams

---

### 12. API Portals (Backstage)

```yaml
# catalog-info.yaml
apiVersion: backstage.io/v1alpha1
kind: API
metadata:
  name: user-api
  description: User management API
  annotations:
    github.com/project-slug: org/user-api
spec:
  type: openapi
  lifecycle: production
  owner: team-platform
  system: user-service
  definition:
    $text: ./api/openapi.yaml
```

Backstage API catalog features:
- Renders OpenAPI, AsyncAPI, GraphQL, gRPC specs
- Interactive try-it-out via api-docs plugin
- Links APIs to teams, systems, domains
- Searchable inventory of all org APIs

Source: https://backstage.io/docs/features/software-catalog/

---

### 13. GraphQL Documentation

Schema self-documents via introspection:
```graphql
"""A user in the system"""
type User {
  """Unique identifier"""
  id: ID!
  
  """User's display name"""
  name: String!
  
  """User's email address"""
  email: String!
  
  """User's posts"""
  posts(limit: Int = 10): [Post!]!
}
```

Tools:
| Tool | Purpose |
|------|---------|
| Apollo Studio | Schema registry, analytics, explorer |
| GraphQL Playground | Interactive query explorer |
| SpectaQL | Static doc generator from SDL |
| Voyager | Visual schema explorer |

Source: https://wundergraph.com/blog/graphql-schema-design-principles

---

### 14. AsyncAPI / WebSocket Docs

```yaml
asyncapi: 3.0.0
info:
  title: Notifications API
  version: 1.0.0
servers:
  production:
    host: wss://api.example.com
    protocol: wss
channels:
  notifications:
    address: /notifications
    messages:
      notification:
        $ref: '#/components/messages/Notification'
    bindings:
      ws:
        method: GET
components:
  messages:
    Notification:
      payload:
        type: object
        properties:
          type:
            type: string
            enum: [message, alert, update]
          data:
            type: object
          timestamp:
            type: string
            format: date-time
```

Document connection lifecycle:
1. Connect to WebSocket endpoint
2. Subscribe to channels
3. Receive messages
4. Handle reconnection
5. Disconnect

Source: https://www.asyncapi.com/

---

### 15. Rate Limit & Analytics Docs

**Response headers (always include):**
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1640995200
Retry-After: 60
```

**Plan tiers table:**
| Plan | Requests/min | Burst | Endpoints |
|------|-------------|-------|-----------|
| Free | 60 | 10 | Read-only |
| Pro | 600 | 100 | Full access |
| Enterprise | 6000 | 1000 | Full + webhooks |

**Error response:**
```json
{
  "error": {
    "type": "rate_limit_exceeded",
    "message": "Rate limit of 100 requests per minute exceeded",
    "retry_after": 42
  }
}
```

---

## Checklist

### Spec Quality
- [ ] Valid OpenAPI 3.1 syntax
- [ ] All operations have operationIds
- [ ] All operations have descriptions
- [ ] Request/response examples provided
- [ ] Error responses documented (400, 401, 403, 404, 429, 500)
- [ ] Security schemes defined
- [ ] Pagination patterns consistent

### Documentation
- [ ] Getting started / quickstart guide
- [ ] Authentication guide with sequence diagrams
- [ ] Rate limit documentation with headers
- [ ] Error reference with all error types
- [ ] Changelog maintained
- [ ] Code examples in 3+ languages

### Automation
- [ ] Spectral linting in CI
- [ ] Breaking change detection
- [ ] SDK generation automated
- [ ] Docs auto-deploy on spec change
- [ ] Contract tests passing

### Developer Experience
- [ ] Interactive try-it-out available
- [ ] Postman/Bruno collection provided
- [ ] SDK packages published
- [ ] Status page linked
- [ ] Support channel documented

---

## Pitfalls

| Pitfall | Impact | Fix |
|---------|--------|-----|
| No examples in spec | Docs show only schema, not real data | Add examples to all schemas |
| Inconsistent naming | Confuses consumers | Pick camelCase or snake_case, enforce with Spectral |
| Missing error docs | Consumers can't handle errors | Document all 4xx/5xx responses |
| Outdated docs | Mismatch with implementation | Auto-generate from spec, test with Dredd |
| No versioning strategy | Breaking changes break clients | Pick strategy (URI/date-based), stick to it |
| Auth docs without diagrams | Consumers get confused | Include sequence diagrams per flow |
| No rate limit docs | Consumers hit limits unexpectedly | Document limits, headers, retry guidance |
| Monolithic spec file | Hard to maintain | Split into domain-specific files, bundle for rendering |

---

## Quick Reference

**Key URLs:**
- OpenAPI spec: https://spec.openapis.org/oas/v3.1.1.html
- Spectral: https://github.com/stoplightio/spectral
- openapi-generator: https://openapi-generator.tech/
- Redoc: https://redocly.com/
- AsyncAPI: https://www.asyncapi.com/
- Google API design: https://docs.cloud.google.com/apis/design
- Microsoft API guidelines: https://github.com/microsoft/api-guidelines
- Stripe API: https://docs.stripe.com/api
