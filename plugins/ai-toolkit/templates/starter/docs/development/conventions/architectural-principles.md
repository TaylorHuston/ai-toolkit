---
last_updated: "2025-10-30"
description: "Design philosophy, architectural patterns, and decision-making principles"

# === Architecture Configuration ===
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

This guideline defines our design philosophy, architectural patterns, and decision-making principles. Update the YAML frontmatter as you make foundational architecture decisions.

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

## Configuration Process

**When making architectural decisions:**

1. Run `/adr "system architecture"` to document major decisions (technology choices, patterns, boundaries, trade-offs)
2. Update YAML frontmatter with decisions (`architecture_style`, `layer_separation`, `state_management`, etc.)
3. Add examples section below with links to well-structured modules

**Agents read the frontmatter** to understand your architecture and apply patterns consistently.

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

## General Architecture Knowledge

Claude has extensive knowledge of architecture styles, design patterns, SOLID/DRY/KISS/YAGNI principles, Clean/Hexagonal/Onion Architecture, Domain-Driven Design, and system design patterns. Ask questions like "How should I architect [X]?" for guidance based on established principles.
