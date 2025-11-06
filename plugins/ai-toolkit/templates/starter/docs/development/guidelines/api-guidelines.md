---
# === Metadata ===
template_type: "guideline"
created: "2025-10-30"
last_updated: "2025-10-30"
status: "Optional"
target_audience: ["AI Assistants", "API Designers", "Backend Developers"]
description: "API design patterns, structure, and conventions - fill in via /adr decisions"

# === API Configuration (Machine-readable for AI agents) ===
api_pattern: "TBD"            # REST, GraphQL, tRPC, gRPC
api_location: "TBD"           # src/api/, src/server/api/, etc.
authentication: "TBD"         # JWT, session, OAuth2, etc.
versioning: "TBD"             # URL (/v1/), header, none
error_format: "TBD"           # Standard format or custom
documentation: "TBD"          # OpenAPI, GraphQL schema, tRPC types
---

# API Guidelines

**Referenced by Commands:** _None currently_ (available for future `/api-design` command or custom commands)

## Quick Reference

This guideline defines our API design patterns, structure, and conventions. Update as you make API architecture decisions.

## Our API Approach

**Pattern**: TBD → Run `/adr "API architecture"` to decide

- REST (traditional, widely supported)
- GraphQL (flexible querying, single endpoint)
- tRPC (type-safe, TypeScript-first)
- gRPC (high performance, binary protocol)

## API Structure

### Endpoint Organization
```
TBD - Update based on chosen pattern

Examples:
- REST: src/api/routes/users.ts
- GraphQL: src/api/schema/user.graphql
- tRPC: src/server/api/routers/users.ts
- gRPC: src/api/proto/users.proto
```

### File Structure
- **Location**: TBD
- **Naming Convention**: TBD (kebab-case, camelCase, etc.)
- **Organization**: TBD (by resource, by feature, etc.)

## Authentication & Authorization

- **Strategy**: TBD → Run `/adr "authentication strategy"` to decide
- **Implementation**: TBD
- **Session Management**: TBD

## Error Handling

### Error Format
```json
TBD - Define standard error format

Example:
{
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "User with ID 123 not found",
    "statusCode": 404
  }
}
```

### Error Codes
- TBD - Define standard error codes for your domain

## Versioning Strategy

- **Approach**: TBD
- **Current Version**: TBD
- **Deprecation Policy**: TBD

## Documentation

- **Tool**: TBD (OpenAPI/Swagger, GraphQL introspection, tRPC types)
- **Location**: TBD
- **Auto-generated**: TBD

## Examples

This section will be populated as APIs are built. Agents will reference existing endpoints for consistency.

### Endpoint Example
- TBD - Add link to first API endpoint

### Authentication Example
- TBD - Add link to auth implementation

### Error Handling Example
- TBD - Add link to error handler

## General API Knowledge

For API design best practices, Claude has extensive knowledge of:
- REST principles (resources, verbs, status codes)
- GraphQL schema design and query optimization
- API security (authentication, authorization, rate limiting)
- Versioning strategies and backward compatibility
- Documentation standards (OpenAPI, AsyncAPI)

Ask questions like "What's the best way to design [X] endpoint?" and Claude will provide guidance based on industry standards and your chosen pattern.
