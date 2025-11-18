---
# === Metadata ===
template_type: "pm-template"
created: "2025-10-30"
last_updated: "2025-11-09"
status: "Active"
target_audience: ["AI Assistants"]
description: "AI-managed implementation plan separate from PM-tool-synced TASK.md/BUG.md"

# === Template Configuration ===
type: plan
purpose: "AI-managed implementation breakdown that stays separate from PM-tool-synced TASK.md/BUG.md"
auto_generated: true
created_by: "/plan command"
sections:
  - name: Metadata
    prompt: "Frontmatter with task reference, complexity score, timestamps"
    required: true
    format: yaml
    hint: "Links to parent TASK/BUG, tracks when plan was created/updated, stores complexity analysis."
  - name: Overview
    prompt: "Brief summary of implementation approach and strategy"
    required: false
    format: paragraph
    hint: "High-level context for the phases below. 1-2 sentences explaining the approach."
  - name: Phases
    prompt: "Phase-based breakdown with checkboxed implementation steps"
    required: true
    format: structured-checklist
    hint: "Organized phases with numbered checkboxes (e.g., '- [ ] 1.1 Write user model tests'). STRATEGIC, NOT TACTICAL: Describe WHAT to build, not HOW. Implementation details decided by specialist agents based on current codebase state and WORKLOG lessons. Each phase MUST follow mandatory test-first loop: tests → code → review → commit → worklog → next phase (see pm-guide.md)."
  - name: Scenario Coverage
    prompt: "Mapping of which phases validate which acceptance scenarios from parent spec"
    required: false
    format: structured-list
    hint: "Only present when TASK references parent SPEC with acceptance scenarios. Shows traceability from spec scenarios to plan phases."
  - name: Alternative Patterns
    prompt: "Optional testing/development patterns that could be used"
    required: false
    format: list
    hint: "TDD, BDD, Test Pyramid, Pragmatic approaches as options."
  - name: Notes
    prompt: "Implementation guidance, warnings, and context"
    required: false
    format: paragraph
    hint: "LIVING DOCUMENT: Plans evolve during implementation. Update when discoveries in one phase affect later phases. Complexity notes. Decomposition recommendations. Reference WORKLOG for lessons learned."
---

# Implementation Plan: {task_id} {task_name}

{overview}

## Phases

### Phase 1 - {phase_1_name}
- [ ] 1.1 {step_description}
  - [ ] 1.1.1 {step_description}
- [ ] 1.2 {step_description}

### Phase 2 - {phase_2_name}
- [ ] 2.1 {step_description}
- [ ] 2.2 {step_description}

### Phase 3 - {phase_3_name}
- [ ] 3.1 {step_description}
- [ ] 3.2 {step_description}

## Scenario Coverage

{scenario_coverage}

---

## Implementation Philosophy

**STRATEGIC, NOT TACTICAL:**
- This plan describes **WHAT** to build, not **HOW** to build it
- Specialist agents (backend-specialist, frontend-specialist, etc.) decide implementation details
- Agents leverage WORKLOG to understand what's been done and lessons learned
- Agents adapt to current codebase state, not rigid prescriptive steps

**LIVING DOCUMENT:**
- Update plan when implementation reveals new insights
- If Phase 1 uncovers complexity affecting Phase 2, update Phase 2
- Document significant plan changes in WORKLOG with rationale
- See pm-guide.md "Agile Plan Updates" for update protocol

**Note**: Phases are suggestions. Code reviews, security audits, and implementation discoveries may require plan updates.

**Mandatory Phase Execution**:
Each phase MUST follow strict test-first loop (see pm-guide.md "Test-First Phase Loop"):
1. Write tests (must fail - red)
2. Write code (make tests pass - green)
3. Code review (must pass ≥90)
4. Only when ALL pass: commit, update WORKLOG, move to next phase

No shortcuts. Tests → Code → Review → Commit → Next.

## Complexity Analysis

**Score**: {complexity_score}/10

**Indicators**:
{complexity_indicators}

**Recommendation**: {decomposition_recommendation}
