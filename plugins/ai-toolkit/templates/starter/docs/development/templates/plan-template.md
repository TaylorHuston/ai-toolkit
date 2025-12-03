---
last_updated: "2025-11-26"
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
    hint: "Reference similar completed tasks. Compare complexity (simpler/more complex due to X). List patterns to reuse (### WORKLOG). Note key differences. Example: 'Simpler than 003 (no child relationships)', 'Similar to 003 Pattern (single foreign key)', 'Agent will leverage 003 WORKLOG for patterns'."
  - name: Domain/Business Context
    prompt: "Domain-specific considerations and business rules"
    required: false
    format: list
    hint: "Business rules affecting implementation (e.g., 'ADRs rarely deleted, use DEPRECATED status'). Domain patterns. Historical importance. Document focus. These inform implementation decisions beyond acceptance criteria."
  - name: Phases
    prompt: "Phase-based breakdown with TDD structure (RED/GREEN/REFACTOR) or simple phases"
    required: true
    format: structured-checklist
    hint: "Choose structure based on task type. TDD Phases (feature work): Phase = One Behavior = One RED/GREEN/REFACTOR cycle. Each phase has X.RED (write failing tests), X.GREEN (implement to pass), X.REFACTOR (clean up, loop until review ≥90). Simple Phases (infra/scaffolding): No TDD structure needed. Test descriptions should be strategically tactical: ✅ 'Test workspace scoping (ADRs from workspace A not in workspace B)' ❌ 'Test workspace scoping' (too vague) ❌ 'Use beforeEach to create workspaces...' (too prescriptive). Include helpful hints when gotchas exist. Enumerate error scenarios with specific error codes."
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

**Choose structure based on task type:**
- **TDD Phases** (X.RED/X.GREEN/X.REFACTOR) - For feature work with testable behavior
- **Simple Phases** - For infrastructure/scaffolding with no testable behavior

---

### Pattern A: TDD Phases (Feature Work)

**Phase = One Behavior = One RED/GREEN/REFACTOR cycle**

### Phase 1 - {behavior: e.g., "User can log in"}

#### 1.RED - Write Failing Tests
- [ ] 1.1 Write tests for {behavior}
  - [ ] 1.1.1 Test happy path
  - [ ] 1.1.2 Test error cases ({specific_error_code})
- [ ] 1.2 [CHECKPOINT] Verify all tests FAIL

#### 1.GREEN - Implement to Pass Tests
- [ ] 1.3 Implement {behavior}
  - [ ] 1.3.1 {specific_implementation}
- [ ] 1.4 [CHECKPOINT] Verify all tests PASS

#### 1.REFACTOR - Clean Up (loops until review >= 90)
- [ ] 1.5 Refactor for maintainability
- [ ] 1.6 [CHECKPOINT] Tests still pass (no regressions)
- [ ] 1.7 Code review → if < 90, repeat 1.5-1.7
- [ ] 1.8 [EXIT GATE] Review >= 90, commit phase

### Phase 2 - {next behavior: e.g., "User can log out"}

#### 2.RED - Write Failing Tests
- [ ] 2.1 Write tests for {behavior}
- [ ] 2.2 [CHECKPOINT] Verify all tests FAIL

#### 2.GREEN - Implement to Pass Tests
- [ ] 2.3 Implement {behavior}
- [ ] 2.4 [CHECKPOINT] Verify all tests PASS

#### 2.REFACTOR - Clean Up
- [ ] 2.5 Refactor for maintainability
- [ ] 2.6 [CHECKPOINT] Tests pass, review >= 90, commit

---

### Pattern B: Simple Phases (Infrastructure/Scaffolding)

**For tasks with no testable behavior (infra, config, docs)**

### Phase 1 - {infrastructure_objective}
- [ ] 1.1 Set up {component}
- [ ] 1.2 Configure {settings}
- [ ] 1.3 Verify {component} works

### Phase 2 - {infrastructure_objective}
- [ ] 2.1 {step}
- [ ] 2.2 {step}

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

**Comparison**: Similar to {similar_task_id} ({similar_test_count} tests) but [simpler/more complex] due to {reason}

**Recommendation**: {decomposition_recommendation}
