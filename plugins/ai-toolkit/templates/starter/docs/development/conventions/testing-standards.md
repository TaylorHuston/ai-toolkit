---
last_updated: "2025-10-30"
description: "Testing approach, frameworks, and conventions - fill in via /adr decisions"

# === Testing Configuration ===
testing_framework: "TBD"      # vitest, jest, mocha, etc.
e2e_framework: "TBD"          # playwright, cypress, etc.
test_location: "TBD"          # tests/, src/**/*.test.ts, etc.
test_priority: "TBD"          # unit-first, integration-first, e2e-first
run_command: "npm test"
# Note: Coverage target is configured in task-workflow.md (test_coverage_target)
---

# Testing Standards

**Referenced by Commands:** `/test-fix`

## Quick Reference

This guideline defines our testing approach, frameworks, and conventions. Update as you make testing decisions.

**Coverage Target**: See `task-workflow.md` for coverage target configuration (default: 95%).

## Our Testing Philosophy

**Priority**: TBD → Run `/adr "testing strategy"` to decide

- Will we focus on unit tests, integration tests, or E2E tests?
- What's our coverage target?
- How do we balance speed vs confidence?

## Framework Decisions

### Unit/Integration Testing
- **Framework**: TBD
- **Runner**: TBD
- Run `/adr "unit testing framework"` to decide

### E2E Testing
- **Framework**: TBD
- Run `/adr "E2E testing framework"` to decide

### Component Testing (if frontend)
- **Framework**: TBD

## Test Structure

```
TBD - Update once you've written first tests

Example patterns:
- tests/ mirrors src/ structure
- Co-located: src/**/*.test.ts
- Separate: tests/** matching src/**
```

## Error Scenario Enumeration

For comprehensive test coverage, explicitly enumerate expected error types with specific error codes/classes for each operation.

### Format

**Pattern:** "Test [operation] [error condition] ([specific error code/type])"

### Why This Matters

- **Ensures comprehensive error handling coverage** - Forces thinking through all failure modes
- **Makes test intent crystal clear** - Reviewers immediately understand what's being validated
- **Prevents missed edge cases** - Explicit enumeration reveals gaps in error handling
- **Documents expected behavior** - Tests serve as specification for error handling

### Examples by Error Type

**Database Errors (Prisma):**
- "Test invalid workspaceId (Prisma P2003 foreign key constraint error)"
- "Test update non-existent ADR (Prisma P2025 record not found)"
- "Test duplicate unique field (Prisma P2002 unique constraint violation)"

**Validation Errors:**
- "Test invalid title (ZodError validation failure - empty string)"
- "Test invalid email format (ValidationError - malformed email)"
- "Test missing required field (ValidationError - workspaceId required)"

**Cascade Behavior:**
- "Test CASCADE DELETE from workspace (workspace deletion removes all ADRs)"
- "Test CASCADE DELETE from parent (parent deletion removes children)"
- "Test SET NULL on foreign key (deletion sets reference to null)"

**Authentication/Authorization:**
- "Test unauthenticated access (401 Unauthorized)"
- "Test unauthorized access (403 Forbidden - insufficient permissions)"
- "Test expired token (401 Unauthorized - token expired)"

**Business Logic:**
- "Test status transition validation (cannot transition from DEPRECATED to PROPOSED)"
- "Test workspace quota exceeded (BusinessRuleError - max ADRs reached)"

### Integration with Plans

When creating PLAN.md files, test phases should enumerate specific error scenarios:

```markdown
### Phase 2 - Create Mutation
- [ ] 2.1 Write tests for ADR creation
  - [ ] 2.1.1 Test successful creation with valid workspaceId
  - [ ] 2.1.2 Test creation with minimal fields
  - [ ] 2.1.3 Test invalid workspaceId (Prisma P2003 foreign key error)
  - [ ] 2.1.4 Test invalid title (ZodError - empty string)
  - [ ] 2.1.5 Test missing required fields (ZodError - workspaceId required)
```

**See also:** `pm-workflows.md` "Test Description Quality Standards" for more on writing effective test descriptions.

## Running Tests

```bash
npm test              # Run all tests
npm test:unit         # Unit tests only
npm test:integration  # Integration tests only
npm test:e2e          # E2E tests only
npm test:coverage     # With coverage report
```

Update these commands based on your actual test scripts.

## Quality Gates

**Test-related quality gates** are defined in `task-workflow.md`:
- **Coverage target**: Configured in task-workflow.md frontmatter (default: 95%)
- **Test passage**: All tests must pass before phase completion
- **Test types**: Unit, integration, and E2E requirements per phase

See `task-workflow.md` for complete quality gate configuration.

## Examples

This section will be populated as tests are written. Agents will reference existing tests as patterns for consistency.

### Unit Test Example
- TBD - Add link to first unit test

### Integration Test Example
- TBD - Add link to first integration test

### E2E Test Example
- TBD - Add link to first E2E test

## General Testing Knowledge

For testing best practices, patterns, and techniques, Claude has extensive knowledge of:
- Test-Driven Development (TDD) and Behavior-Driven Development (BDD)
- Unit testing, integration testing, E2E testing strategies
- Mocking, stubbing, and test doubles
- Test organization and maintainability
- Coverage analysis and interpretation

Ask questions like "What's the best way to test [X]?" and Claude will provide guidance based on industry standards and your chosen frameworks.
