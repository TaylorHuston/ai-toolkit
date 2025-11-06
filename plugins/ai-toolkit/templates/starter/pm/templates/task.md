---
# === Metadata ===
template_type: "pm-template"
version: "1.0.0"
created: "2025-10-30"
last_updated: "2025-10-30"
status: "Active"
target_audience: ["AI Assistants", "Project Management"]
description: "Task template with acceptance criteria and technical notes - syncs with external PM tools"

# === Template Configuration ===
type: task
sections:
  - name: Description
    prompt: "What does this task accomplish? What problem does it solve?"
    required: true
    format: paragraph
    hint: "Clear explanation of what will be built and why it's needed."
  - name: Acceptance Criteria
    prompt: "How will we know this task is complete? What must be true?"
    required: true
    format: checklist
    hint: "Bulleted checklist of specific, testable criteria. BDD Given/When/Then format optional."
  - name: Technical Notes
    prompt: "Any technical considerations, constraints, or implementation hints?"
    required: false
    format: paragraph
    hint: "Architecture decisions, technology choices, gotchas, references to ADRs, etc."
---

# TASK-{id}: {name}

## Description
{description}

## Acceptance Criteria
{acceptance_criteria}

**Note**: See plan-structure.md "Agile Plan Updates" if acceptance criteria need refinement during implementation.

## Technical Notes
{technical_notes}

---

**Implementation**: Run `/plan TASK-{id}` to create PLAN.md with phase-based breakdown.
