---
name: api-documentation
description: "API documentation: OpenAPI 3.1 specs, Swagger UI/Redoc/Scalar rendering, SDK generation, changelog automation, interactive try-it-out, style guides (Spectral), contract testing, versioning docs, error catalogs, rate limit docs, webhook docs, authentication guides. Covers REST, GraphQL, gRPC documentation patterns."
version: 5.0.0
author: Dinoudon
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, api, documentation, openapi, swagger, redoc, sdk-generation, spectral, contract-testing, graphql, grpc, rest, webhooks, changelog]
    related_skills: [sdlc-architecture-design, sdlc-cicd-pipeline, sdlc-testing-qa, sdlc-developer-tooling]
---

# API Documentation (Moderate)

Complete API documentation lifecycle: spec-first design, rendering, SDK generation, testing, changelogs.

## When to Use

Trigger when user:
- Writes or reviews OpenAPI/Swagger/AsyncAPI specs
- Sets up Swagger UI, Redoc, Scalar, or Stoplight
- Generates SDKs from API specs
- Creates API changelogs or deprecation notices
- Implements contract testing for APIs
- Documents GraphQL schemas or gRPC protos
- Builds developer portals

## When NOT to Use

- Internal function/class docstrings (use code docs)
- Database schema docs only (see sdlc-architecture-design)
- Pure CLI tool docs (use man pages / --help)

---

## Step 1: OpenAPI 3.1 Spec-First Design

Spec-first = write spec before code. Contract drives implementation.

### Minimal OpenAPI 3.1 Spec

```yaml
openapi: 3.1.0
info:
  title: Task API
  version: 1.0.0
  description: Task management API
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
  /tasks:
    get:
      operationId: listTasks
      summary: List tasks
      tags: [Tasks]
      parameters:
        - name: status
          in: query
          schema:
            type: string
            enum: [pending, in_progress, done]
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
                $ref: '#/components/schemas/TaskList'
          headers:
            X-Request-Id:
              schema:
                type: string
                format: uuid
        '400':
          $ref: '#/components/responses/BadRequest'
        '429':
          $ref: '#/components/responses/RateLimited'
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
      responses:
        '201':
          description: Task created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Task'
        '422':
          $ref: '#/components/responses/ValidationError'
components:
  schemas:
    Task:
      type: object
      required: [id, title, status, created_at]
      properties:
        id:
          type: string
          format: uuid
        title:
          type: string
          minLength: 1
          maxLength: 200
        description:
          type: string
          maxLength: 2000
        status:
          type: string
          enum: [pending, in_progress, done]
        created_at:
          type: string
          format: date-time
        updated_at:
          type: string
          format: date-time
    CreateTaskRequest:
      type: object
      required: [title]
      properties:
        title:
          type: string
          minLength: 1
          maxLength: 200
        description:
          type: string
          maxLength: 2000
    TaskList:
      type: object
      required: [data, meta]
      properties:
        data:
          type: array
          items:
            $ref: '#/components/schemas/Task'
        meta:
          $ref: '#/components/schemas/PaginationMeta'
    PaginationMeta:
      type: object
      properties:
        cursor:
          type: string
          nullable: true
        has_more:
          type: boolean
    Error:
      type: object
      required: [code, message]
      properties:
        code:
          type: string
        message:
          type: string
        details:
          type: array
          items:
            type: object
            properties:
              field:
                type: string
              message:
                type: string
  responses:
    BadRequest:
      description: Bad request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
    RateLimited:
      description: Rate limited
      headers:
        Retry-After:
          schema:
            type: integer
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
    ValidationError:
      description: Validation error
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

### Spec File Organization

```
api/
├── openapi.yaml          # Root spec
├── paths/
│   ├── tasks.yaml
│   └── users.yaml
├── schemas/
│   ├── Task.yaml
│   ├── User.yaml
│   └── Error.yaml
├── parameters/
│   ├── cursor.yaml
│   └── limit.yaml
└── responses/
    ├── BadRequest.yaml
    └── RateLimited.yaml
```

```yaml
# openapi.yaml — reference split files
paths:
  /tasks:
    $ref: './paths/tasks.yaml'
  /users:
    $ref: './paths/users.yaml'
components:
  schemas:
    Task:
      $ref: './schemas/Task.yaml'
```

### Pitfall: Common Spec Mistakes

| Mistake | Fix |
|---------|-----|
| No `operationId` | Add unique ID per operation — SDKs/codegen need it |
| Missing error responses | Document 400, 401, 403, 404, 422, 429, 500 minimum |
| Inconsistent naming | Pick camelCase or snake_case, stick to it |
| No examples | Add `example` to every schema property |
| Nullable vs optional confusion | `nullable: true` = can be null; omit from `required` = optional |
| No pagination pattern | Use cursor-based, document `cursor` + `has_more` |

---

## Step 2: Rendering & Hosting

### Tool Comparison

| Tool | Self-hosted | Cloud | Interactive | Customization | Cost |
|------|-------------|-------|-------------|---------------|------|
| Swagger UI | Yes | No | Try-it-out | Themes, plugins | Free |
| Redoc | Yes | Yes | Limited | Theming, custom CSS | Free + paid |
| Scalar | Yes | Yes | Full | Modern UI, themes | Free |
| Stoplight Elements | Yes | Yes | Full | Customizable | Free + paid |
| Bump.sh | No | Yes | Diff view | Minimal | Paid |

### Swagger UI

```bash
# Docker
docker run -p 8080:8080 \
  -e SWAGGER_JSON=/spec/openapi.yaml \
  -v $(pwd)/api:/spec \
  swaggerapi/swagger-ui

# npm
npm install swagger-ui-dist
```

```html
<!-- HTML embed -->
<div id="swagger-ui"></div>
<script src="https://unpkg.com/swagger-ui-dist/swagger-ui-bundle.js"></script>
<script>
  SwaggerUIBundle({
    url: '/openapi.yaml',
    dom_id: '#swagger-ui',
    presets: [SwaggerUIBundle.presets.apis, SwaggerUIBundle.SwaggerUIStandalonePreset],
    layout: 'StandaloneLayout',
    tryItOutEnabled: true,
    requestInterceptor: (req) => {
      req.headers['X-API-Key'] = localStorage.getItem('api-key');
      return req;
    }
  });
</script>
```

### Redoc

```html
<!-- Single-file embed -->
<!DOCTYPE html>
<html>
<head>
  <title>API Docs</title>
  <meta charset="utf-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>body { margin: 0; padding: 0; }</style>
</head>
<body>
  <redoc spec-url='/openapi.yaml'
         theme='{
           "colors": { "primary": { "main": "#1976d2" } },
           "typography": { "fontSize": "15px" }
         }'
         expand-responses="200,201"
         hide-download-button="false">
  </redoc>
  <script src="https://cdn.redoc.ly/bundles/redoc/latest/bundles/redoc.standalone.js"></script>
</body>
</html>
```

```bash
# CLI build static HTML
npx @redocly/cli build-docs openapi.yaml -o docs/index.html
```

### Scalar (Modern Alternative)

```bash
npm install @scalar/api-reference
```

```html
<script id="api-reference" data-url="/openapi.yaml"></script>
<script src="https://cdn.jsdelivr.net/npm/@scalar/api-reference"></script>
```

```bash
# Standalone server
npx @scalar/cli serve openapi.yaml --port 3000
```

### Redocly for Multi-page Docs

```yaml
# redocly.yaml
theme:
  openapi:
    generateCodeSamples:
      languages:
        - lang: curl
        - lang: python
          library: requests
        - lang: javascript
          library: fetch
    hideSingleRequestSampleTab: true
  colors:
    primary:
      main: '#1976d2'
  markdown:
    frontMatter: true
```

```bash
npx @redocly/cli preview-docs openapi.yaml
npx @redocly/cli build-docs openapi.yaml -o dist/index.html
```

---

## Step 3: SDK Generation

### Tool Comparison

| Tool | Languages | OpenAPI | GraphQL | gRPC | Style |
|------|-----------|---------|---------|------|-------|
| openapi-generator | 50+ | 3.0, 3.1 | No | No | Configurable |
| ogen (Go) | Go | 3.1 | No | No | Idiomatic |
| openapi-typescript | TS | 3.0, 3.1 | No | No | Type-only |
| quicktype | 20+ | 3.0 | Yes | No | Data classes |
| protoc-gen-go | Go | No | No | Yes | gRPC native |
| graphql-codegen | TS | No | Yes | No | Hooks/types |

### openapi-generator

```bash
# Install
npm install @openapitools/openapi-generator-cli -g

# Generate Python SDK
openapi-generator generate \
  -i openapi.yaml \
  -g python \
  -o ./sdks/python \
  --additional-properties=packageName=task_api,packageVersion=1.0.0

# Generate TypeScript SDK
openapi-generator generate \
  -i openapi.yaml \
  -g typescript-fetch \
  -o ./sdks/typescript \
  --additional-properties=supportsES6=true,npmName=task-api-sdk

# Generate Go SDK
openapi-generator generate \
  -i openapi.yaml \
  -g go \
  -o ./sdks/go \
  --additional-properties=packageName=taskapi,isGoSubmodule=true
```

### openapi-generator Config

```yaml
# openapitools.json
{
  "$schema": "node_modules/@openapitools/openapi-generator-cli/config.schema.json",
  "spaces": 2,
  "generator-cli": {
    "version": "7.5.0",
    "generators": {
      "python-sdk": {
        "generatorName": "python",
        "output": "{{baseDir}}/sdks/python",
        "inputSpec": "{{baseDir}}/openapi.yaml",
        "additionalProperties": {
          "packageName": "task_api"
        }
      },
      "typescript-sdk": {
        "generatorName": "typescript-fetch",
        "output": "{{baseDir}}/sdks/typescript",
        "inputSpec": "{{baseDir}}/openapi.yaml"
      }
    }
  }
}
```

```bash
# Generate all configured SDKs
openapi-generator-cli generate
```

### openapi-typescript (Type-safe)

```bash
npm install openapi-typescript --save-dev
```

```bash
# Generate types from spec
npx openapi-typescript openapi.yaml -o src/api-types.ts

# Generate from URL
npx openapi-typescript https://api.example.com/openapi.yaml -o src/api-types.ts
```

```typescript
// Use generated types
import type { paths, components } from './api-types';
import createClient from 'openapi-fetch';

const client = createClient<paths>({ baseUrl: 'https://api.example.com/v1' });

// Fully typed request/response
const { data, error } = await client.GET('/tasks', {
  params: { query: { status: 'pending', limit: 10 } }
});
```

### SDK Generation Pitfall: Template Customization

```bash
# Extract default templates
openapi-generator author template -g python -o templates/python

# Customize specific templates
# templates/python/api.mustache
# templates/python/model.mustache

# Use custom templates
openapi-generator generate \
  -i openapi.yaml \
  -g python \
  -t templates/python \
  -o ./sdks/python
```

---

## Step 4: AsyncAPI (Event-Driven)

Event-driven API documentation (Kafka, RabbitMQ, WebSockets, SSE).

### AsyncAPI Spec

```yaml
asyncapi: 3.0.0
info:
  title: Task Events
  version: 1.0.0
channels:
  taskCreated:
    address: tasks.created
    messages:
      taskCreated:
        $ref: '#/components/messages/TaskCreated'
    bindings:
      kafka:
        topic: tasks.created
        partitions: 3
  taskCompleted:
    address: tasks.completed
    messages:
      taskCompleted:
        $ref: '#/components/messages/TaskCompleted'
operations:
  onTaskCreated:
    action: receive
    channel:
      $ref: '#/channels/taskCreated'
    summary: React to task creation
  publishTaskCompleted:
    action: send
    channel:
      $ref: '#/channels/taskCompleted'
    summary: Publish task completion
components:
  messages:
    TaskCreated:
      payload:
        type: object
        required: [task_id, title, created_by]
        properties:
          task_id:
            type: string
            format: uuid
          title:
            type: string
          created_by:
            type: string
      headers:
        type: object
        properties:
          correlation-id:
            type: string
            format: uuid
    TaskCompleted:
      payload:
        type: object
        required: [task_id, completed_at]
        properties:
          task_id:
            type: string
            format: uuid
          completed_at:
            type: string
            format: date-time
```

```bash
# Validate AsyncAPI spec
npx @asyncapi/cli validate asyncapi.yaml

# Generate HTML docs
npx @asyncapi/cli generate fromTemplate asyncapi.yaml @asyncapi/html-template -o ./docs-async
```

---

## Step 5: GraphQL Documentation

### Schema Documentation with Descriptions

```graphql
"""Task management queries"""
type Query {
  """Retrieve a task by its unique identifier"""
  task(
    """The unique identifier of the task"""
    id: ID!
  ): Task

  """
  List tasks with optional filtering and pagination.
  Returns up to 100 tasks per page.
  """
  tasks(
    """Filter by task status"""
    status: TaskStatus
    """Maximum number of tasks to return (1-100)"""
    first: Int = 20
    """Cursor for pagination"""
    after: String
  ): TaskConnection!
}

"""Represents a task in the system"""
type Task {
  """Unique identifier"""
  id: ID!
  """Task title (1-200 characters)"""
  title: String!
  """Optional task description"""
  description: String
  """Current status of the task"""
  status: TaskStatus!
  """When the task was created"""
  createdAt: DateTime!
  """When the task was last updated"""
  updatedAt: DateTime!
}

enum TaskStatus {
  """Task is waiting to be started"""
  PENDING
  """Task is currently being worked on"""
  IN_PROGRESS
  """Task has been completed"""
  DONE
}
```

### GraphQL Documentation Tools

| Tool | What it does |
|------|--------------|
| GraphQL Voyager | Interactive graph visualization of schema |
| SpectaQL | Static doc generator from SDL |
| GraphiQL | Interactive explorer with docs sidebar |
| Apollo Explorer | Cloud-based collaborative explorer |

```bash
# SpectaQL — generate static docs
npm install spectaql
npx spectaql config.yml

# config.yml
# spectaql:
#   logo: ./logo.png
#   favicon: ./favicon.ico
#   info:
#     title: Task API GraphQL
#     description: GraphQL API for task management
#   introspection:
#     url: http://localhost:4000/graphql
#     schemaFile: ./schema.graphql
```

---

## Step 6: gRPC Documentation

### Proto Comments as Docs

```protobuf
syntax = "proto3";

package task.v1;

import "google/protobuf/timestamp.proto";
import "google/protobuf/field_mask.proto";

// Task service provides task management operations.
service TaskService {
  // Retrieves a task by ID.
  //
  // Returns NOT_FOUND if task does not exist.
  rpc GetTask(GetTaskRequest) returns (Task) {
    option (google.api.http) = { get: "/v1/tasks/{task_id}" };
  }

  // Lists tasks with pagination support.
  rpc ListTasks(ListTasksRequest) returns (ListTasksResponse) {
    option (google.api.http) = { get: "/v1/tasks" };
  }

  // Creates a new task.
  rpc CreateTask(CreateTaskRequest) returns (Task) {
    option (google.api.http) = {
      post: "/v1/tasks"
      body: "task"
    };
  }
}

// Task resource.
message Task {
  // Unique identifier.
  string task_id = 1;

  // Task title (1-200 characters).
  string title = 2;

  // Optional description.
  string description = 3;

  // Current status.
  TaskStatus status = 4;

  // Creation timestamp.
  google.protobuf.Timestamp created_at = 5;
}

enum TaskStatus {
  TASK_STATUS_UNSPECIFIED = 0;
  TASK_STATUS_PENDING = 1;
  TASK_STATUS_IN_PROGRESS = 2;
  TASK_STATUS_DONE = 3;
}

message GetTaskRequest {
  // Required. Task ID.
  string task_id = 1;
}

message ListTasksRequest {
  // Page size (1-100).
  int32 page_size = 1;

  // Page token for pagination.
  string page_token = 2;

  // Filter by status.
  TaskStatus status = 3;
}

message ListTasksResponse {
  repeated Task tasks = 1;
  string next_page_token = 2;
}
```

### gRPC Documentation Tools

```bash
# protoc-gen-doc — generate docs from proto
protoc \
  --doc_out=./docs \
  --doc_opt=markdown,api.md \
  -I ./protos \
  ./protos/task/v1/*.proto

# protoc-gen-doc with HTML template
protoc \
  --doc_out=./docs \
  --doc_opt=html,index.html:templates \
  -I ./protos \
  ./protos/task/v1/*.proto

# buf — modern proto tooling
buf generate
# buf.gen.yaml
# version: v2
# plugins:
#   - remote: buf.build/community/pseudomuto-doc
#     out: ./docs
#     opt: markdown,api.md
```

---

## Step 7: Linting & Style Guides

### Spectral (OpenAPI Linter)

```bash
npm install -g @stoplight/spectral-cli
```

```yaml
# .spectral.yaml
extends: ["spectral:oas"]
rules:
  # Enforce operation IDs
  operation-operationId: error
  operation-operationId-unique: error

  # Enforce descriptions
  operation-description: warn
  oas3-api-servers: error

  # Naming conventions
  paths-kebab-case:
    description: Paths must be kebab-case
    given: "$.paths[*]~"
    then:
      function: pattern
      functionOptions:
        match: "^(/[a-z0-9\\-{}]+)+$"
    severity: error

  properties-camel-case:
    description: Properties must be camelCase
    given: "$.components.schemas.*.properties[*]~"
    then:
      function: pattern
      functionOptions:
        match: "^[a-z][a-zA-Z0-9]*$"
    severity: warn

  # Require examples
  oas3-examples-in-schemas:
    description: Schema properties must have examples
    given: "$.components.schemas.*.properties.*"
    then:
      field: example
      function: truthy
    severity: warn

  # Error schema must have code + message
  error-schema-required-fields:
    given: "$.components.schemas.Error"
    then:
      - field: code
        function: truthy
      - field: message
        function: truthy
    severity: error
```

```bash
# Run linter
spectral lint openapi.yaml

# CI integration
spectral lint openapi.yaml --format=junit --output=spectral-report.xml
```

### Redocly Lint

```yaml
# .redocly.yaml
rules:
  no-unresolved-refs: error
  operation-operationId: error
  operation-summary: error
  tag-description: warn
  no-ambiguous-paths: error
  no-identical-paths: error
  path-parameters-defined: error
  spec-components-invalid-map-name: error
  security-defined: error
  no-enum-type-mismatch: error
  no-identical-paths: error
  no-path-trailing-slash: error
  path-not-include-query: error
  spec-strict-refs: warn
```

```bash
npx @redocly/cli lint openapi.yaml
```

### Spectral vs Redocly Comparison

| Feature | Spectral | Redocly |
|---------|----------|---------|
| Custom rules | JSONPath + functions | Built-in rules only |
| AsyncAPI | Yes | Yes |
| GraphQL | Limited | No |
| Spectral rulesets | Community (IBM, Vacuum) | N/A |
| Bundle/decorate | No | Yes |
| CI formats | JUnit, JSON, text | JSON, stylish |

---

## Step 8: Contract Testing

### Pact (Consumer-Driven)

```typescript
// Consumer test
import { Pact } from '@pact-foundation/pact';

const provider = new Pact({
  consumer: 'TaskWebApp',
  provider: 'TaskAPI',
  port: 1234,
});

describe('Task API', () => {
  beforeAll(() => provider.setup());
  afterAll(() => provider.finalize());

  it('returns a task', async () => {
    await provider.addInteraction({
      state: 'a task exists',
      uponReceiving: 'a request for a task',
      withRequest: {
        method: 'GET',
        path: '/v1/tasks/abc-123',
        headers: { Authorization: 'Bearer test-token' },
      },
      willRespondWith: {
        status: 200,
        headers: { 'Content-Type': 'application/json' },
        body: {
          id: 'abc-123',
          title: 'Test task',
          status: 'pending',
        },
      },
    });

    const response = await fetch('http://localhost:1234/v1/tasks/abc-123', {
      headers: { Authorization: 'Bearer test-token' },
    });
    const task = await response.json();
    expect(task.id).toBe('abc-123');
  });
});
```

### Schemathesis (Property-Based)

```bash
pip install schemathesis
```

```bash
# Run against live API
schemathesis run \
  --url http://localhost:8000 \
  --checks all \
  openapi.yaml

# Run with auth
schemathesis run \
  --url http://localhost:8000 \
  --header "Authorization: Bearer test-token" \
  --checks status_code_conformance \
  --checks content_type_conformance \
  --checks response_schema_conformance \
  openapi.yaml

# CI mode
schemathesis run \
  --url http://localhost:8000 \
  --junit-xml report.xml \
  openapi.yaml
```

### Prism (Mock Server)

```bash
npm install -g @stoplight/prism-cli

# Start mock server
prism mock openapi.yaml

# Dynamic mode (random data)
prism mock openapi.yaml --dynamic

# Validates requests against spec
curl http://localhost:4010/tasks
curl -X POST http://localhost:4010/tasks \
  -H "Content-Type: application/json" \
  -d '{"title": "Test"}'
```

### Contract Testing Checklist

- [ ] Consumer tests define expected interactions
- [ ] Provider verifies against published pacts
- [ ] Schemathesis validates response schemas
- [ ] Prism mock server used for frontend dev
- [ ] CI pipeline runs contract tests on every PR
- [ ] Breaking changes caught before deploy

---

## Step 9: API Changelog & Versioning

### Changelog from Git + Spec Diff

```bash
# oasdiff — OpenAPI diff tool
go install github.com/tufin/oasdiff@latest

# Compare two specs
oasdiff changelog v1.yaml v2.yaml

# Breaking changes only
oasdiff breaking v1.yaml v2.yaml

# JSON output for CI
oasdiff breaking v1.yaml v2.yaml --format json
```

```yaml
# GitHub Actions — auto-detect breaking changes
name: API Breaking Check
on:
  pull_request:
    paths: ['api/openapi.yaml']
jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0
      - name: Check breaking changes
        run: |
          git show origin/main:api/openapi.yaml > /tmp/base.yaml
          oasdiff breaking /tmp/base.yaml api/openapi.yaml --fail-on ERR
```

### Bump.sh (Automated Changelog)

```bash
npm install -g bump-cli

# Register API
bump deploy openapi.yaml --token $BUMP_TOKEN

# CI: auto-publish on merge
bump deploy openapi.yaml \
  --token $BUMP_TOKEN \
  --doc my-api-doc-id
```

### Version Documentation

```markdown
## Changelog

### v2.0.0 (2025-01-15)
**Breaking Changes:**
- `GET /tasks` response changed from array to `{ data: [], meta: {} }`
- `status` field now uses `in_progress` instead of `in-progress`

**New:**
- `POST /tasks/{id}/complete` endpoint
- Cursor-based pagination (replaces offset)
- `X-Request-Id` response header

**Deprecated:**
- `GET /tasks?offset=N` — use cursor parameter instead

### v1.1.0 (2024-11-01)
**New:**
- `GET /tasks/{id}` endpoint
- Task `description` field
```

### API Deprecation Headers

```http
HTTP/1.1 200 OK
Deprecation: true
Sunset: Sat, 01 Jan 2026 00:00:00 GMT
Link: <https://api.example.com/v2/tasks>; rel="successor-version"
```

```python
# FastAPI deprecation middleware
from fastapi import Request, Response
from datetime import datetime

async def add_deprecation_headers(request: Request, call_next):
    response = await call_next(request)
    if request.url.path.startswith("/v1/"):
        response.headers["Deprecation"] = "true"
        response.headers["Sunset"] = "Sat, 01 Jan 2026 00:00:00 GMT"
        response.headers["Link"] = '</v2' + request.url.path[3:] + '>; rel="successor-version"'
    return response
```

---

## Step 10: Error Documentation

### Standard Error Response

```yaml
components:
  schemas:
    Error:
      type: object
      required: [code, message, request_id]
      properties:
        code:
          type: string
          description: Machine-readable error code
          example: TASK_NOT_FOUND
        message:
          type: string
          description: Human-readable error message
          example: Task with ID abc-123 not found
        request_id:
          type: string
          format: uuid
          description: Unique request identifier for support
        details:
          type: array
          description: Field-level validation errors
          items:
            type: object
            properties:
              field:
                type: string
                example: title
              code:
                type: string
                example: TOO_SHORT
              message:
                type: string
                example: Must be at least 1 character
```

### Error Catalog Table

| HTTP Status | Code | Description | Resolution |
|-------------|------|-------------|------------|
| 400 | INVALID_REQUEST | Malformed request body | Check request format |
| 401 | UNAUTHORIZED | Missing or invalid auth | Provide valid token |
| 403 | FORBIDDEN | Insufficient permissions | Check API key scopes |
| 404 | NOT_FOUND | Resource doesn't exist | Verify resource ID |
| 409 | CONFLICT | Resource state conflict | Retry or refresh state |
| 422 | VALIDATION_ERROR | Invalid field values | Check `details` array |
| 429 | RATE_LIMITED | Too many requests | Respect `Retry-After` |
| 500 | INTERNAL_ERROR | Server error | Retry with backoff |
| 503 | SERVICE_UNAVAILABLE | Temporary outage | Retry after `Retry-After` |

### Error Handling Examples

```python
# Python SDK error handling
from task_api import TaskAPI, APIError, RateLimitError

client = TaskAPI(api_key="...")

try:
    task = client.tasks.get("abc-123")
except APIError as e:
    print(f"Error: {e.code} - {e.message}")
    print(f"Request ID: {e.request_id}")
    if e.details:
        for detail in e.details:
            print(f"  {detail.field}: {detail.message}")
except RateLimitError as e:
    import time
    time.sleep(e.retry_after)
    task = client.tasks.get("abc-123")
```

```typescript
// TypeScript SDK error handling
import { TaskClient, ApiError, RateLimitError } from 'task-api-sdk';

const client = new TaskClient({ apiKey: '...' });

try {
  const task = await client.tasks.get('abc-123');
} catch (e) {
  if (e instanceof RateLimitError) {
    await new Promise(r => setTimeout(r, e.retryAfter * 1000));
    // retry
  } else if (e instanceof ApiError) {
    console.error(`${e.code}: ${e.message}`);
    e.details?.forEach(d => console.error(`  ${d.field}: ${d.message}`));
  }
}
```

---

## Step 11: Rate Limit Documentation

### Rate Limit Headers

```http
HTTP/1.1 200 OK
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1640000000
X-RateLimit-Policy: 1000;w=3600
```

```yaml
# OpenAPI rate limit response
components:
  responses:
    RateLimited:
      description: Rate limit exceeded
      headers:
        X-RateLimit-Limit:
          description: Maximum requests per window
          schema:
            type: integer
        X-RateLimit-Remaining:
          description: Remaining requests in window
          schema:
            type: integer
        X-RateLimit-Reset:
          description: Unix timestamp when window resets
          schema:
            type: integer
        Retry-After:
          description: Seconds to wait before retrying
          schema:
            type: integer
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
          example:
            code: RATE_LIMITED
            message: Rate limit exceeded. Retry after 60 seconds.
```

### Rate Limit Documentation Table

| Plan | Requests/Hour | Burst | Concurrent |
|------|---------------|-------|------------|
| Free | 100 | 10/sec | 5 |
| Pro | 10,000 | 100/sec | 50 |
| Enterprise | 100,000 | 1000/sec | 500 |

---

## Step 12: Webhook Documentation

### AsyncAPI Webhook Spec

```yaml
asyncapi: 3.0.0
info:
  title: Task Webhooks
  version: 1.0.0
channels:
  taskWebhook:
    address: /webhooks/tasks
    messages:
      taskCreated:
        $ref: '#/components/messages/TaskCreated'
      taskCompleted:
        $ref: '#/components/messages/TaskCompleted'
    bindings:
      http:
        method: POST
        headers:
          type: object
          properties:
            X-Webhook-Signature:
              type: string
              description: HMAC-SHA256 signature of payload
            X-Webhook-Timestamp:
              type: integer
              description: Unix timestamp of delivery
components:
  messages:
    TaskCreated:
      name: task.created
      title: Task Created
      payload:
        type: object
        required: [event, data, timestamp]
        properties:
          event:
            type: string
            const: task.created
          data:
            type: object
            properties:
              task_id:
                type: string
              title:
                type: string
          timestamp:
            type: string
            format: date-time
```

### Webhook Security

```python
import hmac
import hashlib

def verify_webhook_signature(payload: bytes, signature: str, secret: str) -> bool:
    """Verify HMAC-SHA256 webhook signature."""
    expected = hmac.new(
        secret.encode(),
        payload,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(f"sha256={expected}", signature)
```

### Webhook Retry Policy

| Attempt | Delay | Cumulative |
|---------|-------|------------|
| 1 | Immediate | 0s |
| 2 | 1 min | 1 min |
| 3 | 5 min | 6 min |
| 4 | 30 min | 36 min |
| 5 | 2 hours | 2h 36min |
| 6 | 8 hours | 10h 36min |

---

## Step 13: Authentication Documentation

### Auth Methods Comparison

| Method | Use Case | Security | Complexity |
|--------|----------|----------|------------|
| API Key | Server-to-server | Medium | Low |
| Bearer JWT | User auth, microservices | High | Medium |
| OAuth 2.0 | Third-party access | High | High |
| mRPC/mTLS | Service mesh | Very High | High |

### OAuth 2.0 Flow Documentation

```yaml
# OpenAPI OAuth2 config
components:
  securitySchemes:
    OAuth2:
      type: oauth2
      flows:
        authorizationCode:
          authorizationUrl: https://auth.example.com/authorize
          tokenUrl: https://auth.example.com/token
          refreshUrl: https://auth.example.com/token
          scopes:
            tasks:read: Read tasks
            tasks:write: Create and update tasks
            tasks:delete: Delete tasks
```

### Auth Quickstart Example

```bash
# API Key
curl -H "X-API-Key: your-key" https://api.example.com/v1/tasks

# Bearer Token
curl -H "Authorization: Bearer eyJhbG..." https://api.example.com/v1/tasks

# OAuth2 — get token
curl -X POST https://auth.example.com/token \
  -d "grant_type=client_credentials" \
  -d "client_id=your-id" \
  -d "client_secret=your-secret" \
  -d "scope=tasks:read tasks:write"
```

---

## Step 14: CI/CD Integration

### GitHub Actions Pipeline

```yaml
name: API Docs CI
on:
  push:
    paths: ['api/**']
  pull_request:
    paths: ['api/**']
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Validate OpenAPI
        run: npx @redocly/cli lint api/openapi.yaml
      - name: Check breaking changes
        if: github.event_name == 'pull_request'
        run: |
          git fetch origin main
          git show origin/main:api/openapi.yaml > /tmp/base.yaml
          npx oasdiff breaking /tmp/base.yaml api/openapi.yaml --fail-on ERR
  build:
    needs: validate
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Generate SDKs
        run: |
          npx @openapitools/openapi-generator-cli generate
      - name: Build docs
        run: npx @redocly/cli build-docs api/openapi.yaml -o dist/index.html
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
  publish-sdk:
    needs: validate
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    strategy:
      matrix:
        sdk: [python, typescript]
    steps:
      - uses: actions/checkout@v4
      - name: Generate SDK
        run: |
          npx @openapitools/openapi-generator-cli generate \
            -i api/openapi.yaml \
            -g ${{ matrix.sdk }} \
            -o ./sdk
      - name: Publish SDK
        run: echo "Publish ${{ matrix.sdk }} SDK"
```

### Pre-commit Hook

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: openapi-lint
        name: Lint OpenAPI spec
        entry: npx @redocly/cli lint
        files: 'api/.*\.ya?ml$'
        language: system
      - id: openapi-validate
        name: Validate OpenAPI spec
        entry: npx @redocly/cli lint --skip-rule=no-unused-components
        files: 'openapi\.ya?ml$'
        language: system
```

---

## Step 15: Developer Portal

### Portal Components Checklist

- [ ] Getting Started guide (auth, first request)
- [ ] Full API reference (auto-generated from spec)
- [ ] Code samples in 3+ languages
- [ ] Error catalog with resolution steps
- [ ] Rate limit docs with plan comparison
- [ ] Webhook setup guide
- [ ] SDKs with installation instructions
- [ ] Changelog with breaking change alerts
- [ ] Interactive playground (try-it-out)
- [ ] Status page link

### Docusaurus + OpenAPI Plugin

```bash
npx create-docusaurus@latest portal classic
cd portal
npm install docusaurus-plugin-openapi-docs
```

```javascript
// docusaurus.config.js
module.exports = {
  plugins: [
    [
      'docusaurus-plugin-openapi-docs',
      {
        docsPluginId: 'api',
        config: {
          taskapi: {
            specPath: 'api/openapi.yaml',
            outputDir: 'docs/api',
            sidebarOptions: { groupPathsBy: 'tag' },
          },
        },
      },
    ],
  ],
  themes: ['docusaurus-theme-openapi-docs'],
};
```

---

## Step 16: API Documentation Checklist

### Spec Quality

- [ ] OpenAPI 3.1 valid (run `redocly lint`)
- [ ] All endpoints have `operationId`
- [ ] All endpoints have `summary` and `description`
- [ ] All schemas have `example` values
- [ ] All error responses documented (400, 401, 403, 404, 422, 429, 500)
- [ ] Pagination pattern documented
- [ ] Authentication schemes defined
- [ ] Server URLs include all environments
- [ ] No `TODO` or placeholder text in spec

### Rendering & Access

- [ ] Docs rendered (Swagger UI, Redoc, or Scalar)
- [ ] Try-it-out enabled with auth
- [ ] Mobile responsive
- [ ] Search works across endpoints
- [ ] Dark mode available

### SDK & Tooling

- [ ] SDKs generated for top 3 languages
- [ ] SDKs published to package registries
- [ ] Postman collection exported
- [ ] Spectral linting in CI
- [ ] Breaking change detection in PR checks
- [ ] Contract tests passing

### Content

- [ ] Getting started / quickstart guide
- [ ] Authentication guide with examples
- [ ] Error catalog with codes and resolution
- [ ] Rate limit docs per plan
- [ ] Webhook setup guide
- [ ] Changelog with semantic versioning
- [ ] Deprecation notices with migration guides
- [ ] Code samples in curl + 2 SDK languages

### Maintenance

- [ ] Spec updated with every API change
- [ ] SDKs regenerated on spec change
- [ ] Changelog auto-generated from spec diffs
- [ ] Breaking changes blocked in CI
- [ ] Deprecated endpoints have sunset dates

---

## Pitfalls

| Pitfall | Impact | Fix |
|---------|--------|-----|
| Spec drift from implementation | Docs lie, devs lose trust | Generate server from spec or use contract testing |
| No examples in spec | Useless try-it-out | Add `example` to every schema property |
| Manual SDK maintenance | Stale SDKs, bugs | Auto-generate from spec, customize templates |
| No breaking change detection | Surprise deprecations | Use `oasdiff` in CI |
| Missing error codes | Devs guess at failures | Document every error with code + message + resolution |
| No versioning strategy | Breaking prod clients | URL path versioning, support 2-3 versions, Sunset header |
| Monolithic spec file | Hard to maintain | Split into paths/, schemas/, components/ with `$ref` |
| No webhook retry docs | Devs implement wrong | Document retry policy, exponential backoff, signature verification |
| Copy-paste code samples | Stale samples | Generate from spec or use Prism mock server |
| No rate limit docs | Devs hit limits blind | Document limits per plan, show headers, include backoff example |
