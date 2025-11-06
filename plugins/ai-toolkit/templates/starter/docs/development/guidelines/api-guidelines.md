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

This guideline defines our API design patterns, structure, and conventions. Update the YAML frontmatter as you make API architecture decisions.

## Configuration Process

**When starting API development:**

1. Run `/adr "API architecture"` to decide pattern (REST, GraphQL, tRPC, gRPC)
2. Update YAML frontmatter with decisions (`api_pattern`, `api_location`, `authentication`, etc.)
3. Add examples section below with links to actual implementations

**Agents read the frontmatter** to understand your API conventions and apply them during implementation.

## General API Knowledge

Claude has extensive knowledge of REST, GraphQL, tRPC, gRPC, API security, versioning strategies, and documentation standards. Ask questions like "What's the best way to design [X] endpoint?" for guidance based on industry standards.
