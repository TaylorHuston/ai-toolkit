---
# === Metadata ===
template_type: "pm-template"
version: "1.0.0"
created: "2025-10-30"
last_updated: "2025-10-30"
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
    hint: "Organized phases with numbered checkboxes (e.g., '- [ ] 1.1 Write user model tests'). Test-first patterns embedded."
  - name: Alternative Patterns
    prompt: "Optional testing/development patterns that could be used"
    required: false
    format: list
    hint: "TDD, BDD, Test Pyramid, Pragmatic approaches as options."
  - name: Notes
    prompt: "Implementation guidance, warnings, and context"
    required: false
    format: paragraph
    hint: "Phases are suggestions. Complexity notes. Decomposition recommendations."
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

---

**Note**: Phases are suggestions. See plan-structure.md "Agile Plan Updates" for guidance on updating phases during implementation based on code reviews and security audits.

**Alternative Patterns**:
- **Strict TDD**: Red-Green-Refactor cycle visible in every step
- **BDD Scenarios**: Define Given/When/Then → Implement tests → Build features
- **Test Pyramid**: Heavy unit, moderate integration, light E2E
- **Pragmatic**: Spike/explore → Write tests → Implement production code

## Complexity Analysis

**Score**: {complexity_score}/10

**Indicators**:
{complexity_indicators}

**Recommendation**: {decomposition_recommendation}
