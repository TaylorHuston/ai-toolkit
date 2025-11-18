---
# === Metadata ===
template_type: "pm-template"
version: "1.0.0"
created: "2025-10-30"
last_updated: "2025-10-30"
status: "Active"
target_audience: ["AI Assistants", "Code Architects"]
description: "Architecture Decision Record template following ADR format"

# === Template Configuration ===
type: adr
sections:
  - name: Status
    prompt: "What is the status of this decision?"
    required: true
    format: text
    hint: "Options: Proposed, Accepted, Deprecated, Superseded"

  - name: Context
    prompt: "What is the issue that we're seeing that is motivating this decision or change?"
    required: true
    format: paragraph
    hint: "Describe the forces at play, including technological, political, social, and project local. These forces are probably in tension, and should be called out as such."

  - name: Decision
    prompt: "What is the change that we're proposing and/or doing?"
    required: true
    format: paragraph
    hint: "State the architecture decision and provide clear rationale. Use complete sentences with active voice."

  - name: Consequences
    prompt: "What becomes easier or more difficult to do because of this change?"
    required: true
    format: structured
    hint: "List both positive and negative consequences. Include impacts on: development velocity, system complexity, maintainability, scalability, security, cost."

  - name: Alternatives Considered
    prompt: "What other options were evaluated?"
    required: false
    format: list
    hint: "List alternatives with brief pros/cons. Explain why they were not chosen."

  - name: References
    prompt: "Any related documentation or resources?"
    required: false
    format: list
    hint: "Links to RFCs, design docs, external articles, related ADRs, or relevant issues/tasks."
---

# ADR-{id}: {title}

**Date**: {date}

## Status

{status}

## Context

{context}

## Decision

{decision}

## Consequences

### Positive

{positive_consequences}

### Negative

{negative_consequences}

### Neutral

{neutral_consequences}

## Alternatives Considered

{alternatives}

## References

{references}

---

**Template Version**: 1.0
**Last Updated**: 2025-10-22
