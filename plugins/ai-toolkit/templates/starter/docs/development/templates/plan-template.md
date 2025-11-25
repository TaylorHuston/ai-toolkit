---
last_updated: "2025-11-09"
description: "AI-managed implementation plan separate from PM-tool-synced TASK.md/BUG.md"

# === Template Configuration ===
type: plan
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
  - name: Comparative Context
    prompt: "How this task relates to previous similar work"
    required: false
    format: structured-list
    hint: "Reference similar completed tasks. Compare complexity (simpler/more complex due to X). List patterns to reuse (TASK-### WORKLOG). Note key differences. Example: 'Simpler than TASK-003 (no child relationships)', 'Similar to TASK-003 Pattern (single foreign key)', 'Agent will leverage TASK-003 WORKLOG for patterns'."
  - name: Domain/Business Context
    prompt: "Domain-specific considerations and business rules"
    required: false
    format: list
    hint: "Business rules affecting implementation (e.g., 'ADRs rarely deleted, use DEPRECATED status'). Domain patterns. Historical importance. Document focus. These inform implementation decisions beyond acceptance criteria."
  - name: Phases
    prompt: "Phase-based breakdown with checkboxed implementation steps"
    required: true
    format: structured-checklist
    hint: "3-level hierarchy: Phase (objective) → Major Steps (2-4 per phase) → Specific Validations (3-6 per step). Test descriptions should be strategically tactical: ✅ 'Test workspace scoping (ADRs from workspace A not in workspace B)' ❌ 'Test workspace scoping' (too vague) ❌ 'Use beforeEach to create workspaces...' (too prescriptive). Include helpful hints: '(note: lowercase model names in Prisma)' when gotchas exist. Enumerate error scenarios: 'Test invalid workspaceId (Prisma P2003 foreign key error)'. Each phase MUST follow mandatory test-first loop: tests → code → review → commit → worklog → next phase (see pm-guide.md)."
  - name: Scenario Coverage
    prompt: "Mapping of which phases validate which acceptance scenarios from parent spec"
    required: false
    format: structured-list
    hint: "Only present when TASK references parent SPEC with acceptance scenarios. For each scenario: (1) Which phases cover it, (2) HOW those phases validate it (specific tests), (3) WHY that structure was chosen. The explanatory 'because' clause connects requirements to implementation."
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

## Comparative Context

### Similar Tasks
{comparative_context}

### Key Differences
{key_differences}

### Patterns to Reuse
{patterns_to_reuse}

## Domain/Business Context

{domain_context}

---

## Phases

### Phase 1 - {phase_1_name}
- [ ] 1.1 {major_step_description}
  - [ ] 1.1.1 {specific_validation}
  - [ ] 1.1.2 {specific_validation}
- [ ] 1.2 {major_step_description}
  - [ ] 1.2.1 {specific_validation}

### Phase 2 - {phase_2_name}
- [ ] 2.1 {major_step_description}
  - [ ] 2.1.1 {specific_validation}
- [ ] 2.2 {major_step_description}

### Phase 3 - {phase_3_name}
- [ ] 3.1 {major_step_description}
- [ ] 3.2 {major_step_description}

## Scenario Coverage

### SPEC-{spec_id} Scenario {n}: {scenario_title}
- **Given/When/Then**: {scenario_summary}
- **Coverage Mapping**: Phase {x.y} {implements_or_validates} {what} **because** {explanation}
- **Test Strategy**: {which_tests_validate}

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

**Test Count Estimate**: ~{min}-{max} total tests
- Phase 1: ~{count} tests ({test_types})
- Phase 2: ~{count} tests ({test_types})
- Phase 3: ~{count} tests ({test_types})

**Comparison**: Similar to TASK-{similar_task_id} ({similar_test_count} tests) but [simpler/more complex] due to {reason}

**Recommendation**: {decomposition_recommendation}
