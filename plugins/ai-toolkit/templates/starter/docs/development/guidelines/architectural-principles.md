---
# === Metadata ===
template_type: "guideline"
created: "2025-10-30"
last_updated: "2025-10-30"
status: "Optional"
target_audience: ["AI Assistants", "Code Architects", "Development Team"]
description: "Design philosophy, architectural patterns, and decision-making principles"

# === Architecture Configuration (Machine-readable for AI agents) ===
architecture_style: "TBD"      # monolith, microservices, serverless, modular-monolith
design_principles: ["DRY", "KISS", "YAGNI", "SOLID"]
layer_separation: "TBD"        # strict layers, loose layers, none
dependency_direction: "inward" # dependencies point inward (clean architecture)
state_management: "TBD"        # redux, zustand, context, none
database_access: "TBD"         # ORM, query builder, raw SQL
---

# Architectural Principles

**Referenced by Commands:** `/adr`

## Quick Reference

This guideline defines our design philosophy, architectural patterns, and decision-making principles. Update as you make foundational architecture decisions.

## Core Design Principles

These principles guide all architectural and code decisions:

### DRY (Don't Repeat Yourself)
- **Goal**: Single source of truth for every piece of knowledge

### KISS (Keep It Simple, Stupid)
- **Goal**: Simplest solution that works

### YAGNI (You Aren't Gonna Need It)
- **Goal**: Don't build features before they're needed

### SOLID Principles
- **Single Responsibility**: One reason to change
- **Open/Closed**: Open for extension, closed for modification
- **Liskov Substitution**: Subtypes must be substitutable
- **Interface Segregation**: Many specific interfaces over one general
- **Dependency Inversion**: Depend on abstractions, not concretions


## Architecture Style

**Pattern**: TBD → Run `/adr "system architecture"` to decide

- **Monolith**: Single deployable unit, shared database
- **Modular Monolith**: Monolith with clear module boundaries
- **Microservices**: Independent services, separate databases
- **Serverless**: Function-as-a-Service, event-driven

### Current Choice
- **Style**: TBD
- **Rationale**: TBD - Link to ADR when decision is made

## System Structure

### Layer Organization

```
TBD - Define layers/modules after architecture decision

Examples:
- 3-tier: Presentation / Business / Data
- Clean Architecture: Entities / Use Cases / Interface Adapters / Frameworks
- Feature-based: features/ with internal layers
```

### Dependency Rules

- **Direction**: Dependencies point inward (outer depends on inner)
- **Boundary**: TBD - How strictly enforced?
- **Violations**: TBD - When can we break the rules?

## Data Management

### Database Strategy

- **Type**: TBD (SQL, NoSQL, hybrid)
- **Access Pattern**: TBD (ORM, query builder, raw SQL)
- **Migration**: TBD (tool and strategy)

### State Management (if applicable)

- **Client State**: TBD (Redux, Zustand, Context, etc.)
- **Server State**: TBD (React Query, SWR, custom)
- **Local State**: TBD (useState, useReducer)

## Decision-Making Framework

### When to Create an ADR

Run `/adr` and create an ADR for:
- Technology choices (framework, database, language)
- Architectural patterns (microservices, event-driven, etc.)
- System boundaries and integration patterns
- Performance/scalability trade-offs
- Security model decisions

### Architecture Review Triggers

Review architecture when:
- **Scale Changes**: 10x traffic/data/users
- **New Domain**: Adding significant new capability
- **Performance Issues**: Current architecture can't meet needs
- **Team Growth**: Architecture doesn't support team structure

## Patterns & Practices

### Preferred Patterns

TBD - Document patterns we use and why

Examples:
- Repository pattern for data access
- Factory pattern for object creation
- Strategy pattern for algorithms
- Observer pattern for events

### Anti-Patterns to Avoid

TBD - Document what we explicitly avoid

Examples:
- God objects (objects that know/do too much)
- Spaghetti code (tangled dependencies)
- Magic numbers/strings (unexplained constants)

## Quality Attributes

### Priorities

Rank these for your project (not all can be #1):

- **Performance**: TBD priority
- **Scalability**: TBD priority
- **Maintainability**: TBD priority
- **Security**: TBD priority
- **Reliability**: TBD priority
- **Usability**: TBD priority

### Trade-offs

Document major architectural trade-offs:
- TBD - What did we sacrifice for what?

## Examples

### Good Architecture Decision
- TBD - Link to ADR that shows good decision-making

### Well-Structured Module
- TBD - Link to code that exemplifies our architecture

### Dependency Management
- TBD - Example of proper dependency direction

## General Architecture Knowledge

For architectural patterns and principles, Claude has extensive knowledge of:
- Architecture styles (monolith, microservices, event-driven, etc.)
- Design patterns (GoF patterns, enterprise patterns)
- SOLID, DRY, KISS, YAGNI principles
- Clean Architecture, Hexagonal Architecture, Onion Architecture
- Domain-Driven Design (DDD)
- System design and scalability patterns

Ask questions like "How should I architect [X]?" and Claude will provide guidance based on established architectural principles and patterns.
