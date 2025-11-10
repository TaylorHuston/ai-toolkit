---
# === Metadata ===
template_type: "pm-template"
version: "2.0.0"
created: "2025-10-30"
last_updated: "2025-11-07"
status: "Active"
target_audience: ["AI Assistants", "Project Management"]
description: "Feature Spec template for documenting feature requirements and organizing related tasks"

# === Template Configuration ===
type: spec
sections:
  - name: Name
    prompt: "What is the feature spec name? (concise, kebab-case friendly)"
    required: true
    format: text
  - name: Description
    prompt: "Describe the feature. What problem does it solve? What value does it provide?"
    required: true
    format: paragraph
    hint: "Write a clear paragraph explaining the purpose, context, and value of this feature."
  - name: Definition of Done
    prompt: "How will we know this feature is complete? What are the concrete completion criteria?"
    required: true
    format: flexible
    hint: "Can be prose paragraph or bulleted checklist. Make it concrete to prevent scope creep."
  - name: Dependencies
    prompt: "What must exist before this feature can be completed?"
    required: false
    format: list
    hint: "Include ADR references (ADR-001), other specs (SPEC-002), external factors (OAuth setup), etc."
  - name: Tasks
    prompt: "What tasks make up this feature?"
    required: true
    format: checklist
    hint: "Bulleted checkbox list with global IDs in creation order: [ ] TASK-001, [ ] BUG-003, etc."
---

# SPEC-{id}: {name}

## Description
{description}

## Definition of Done
{definition_of_done}

## Dependencies
{dependencies}

## Tasks
{tasks}
