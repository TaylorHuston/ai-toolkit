---
tags: ["workflow", "testing", "debugging", "automation"]
description: "Automatic test failure detection and resolution"
argument-hint: "[test-pattern] [--files FILES] [--type TYPE] [--failed-only] [--watch]"
allowed-tools: ["Bash", "Read", "Edit", "MultiEdit", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/development-loop.md  # Test coverage targets, testing approach
  - docs/development/guidelines/testing-standards.md  # Testing methodology and standards
---

# /test-fix Command

**Purpose**: Automatic test failure detection and resolution with intelligent debugging.

## Usage

```bash
# Basic usage
/test-fix                            # Fix all failing tests
/test-fix "auth.test.js"             # Fix specific test file
/test-fix --files "src/auth/"         # Fix tests in directory

# Test type filtering
/test-fix --type unit                # Run only unit tests
/test-fix --type integration         # Run only integration tests
/test-fix --type e2e                 # Run only end-to-end tests

# Optimized workflows
/test-fix --failed-only              # Re-run only previously failed tests
/test-fix --watch "auth"             # Watch mode - re-run on file changes
/test-fix --type unit --failed-only  # Combine filters for faster iterations
```

## Process

**Parse Arguments**:
- If `--type` specified: Run only tests of specified type (unit/integration/e2e)
- If `--failed-only` flag present: Re-run only previously failed tests
- If `--watch` flag present: Enter watch mode (continuous re-run on changes)
- If `--files` specified: Limit scope to specified files/directories

**Standard Test-Fix Flow**:
1. Run test suite with appropriate filters and capture failure details
2. Analyze test failure patterns and error messages
3. Identify root causes of test failures
4. Implement appropriate fixes for failing tests
5. Validate fixes don't break other tests

**Failed-Only Mode** (`--failed-only` flag):
1. Check for previous test run results (cache or .test-results file)
2. Extract list of previously failed tests
3. Run only those specific tests
4. Apply fixes to failures
5. Result: Faster iteration cycle (skip passing tests)

**Watch Mode** (`--watch` flag):
1. Run initial test suite
2. Apply fixes to any failures
3. Enter watch loop: monitor file changes
4. On change detected: re-run affected tests
5. Continue until user stops or all tests pass
6. Result: Continuous feedback during development

**Type Filtering** (`--type` flag):
1. Identify test command for specified type:
   - `unit`: Run unit test command (e.g., `npm run test:unit`)
   - `integration`: Run integration tests (e.g., `npm run test:integration`)
   - `e2e`: Run end-to-end tests (e.g., `npm run test:e2e`)
2. Execute only tests of that type
3. Result: Faster runs, focused debugging

## Agent Coordination

**Primary**: test-engineer (for test analysis and failure resolution)
**Supporting**: Domain specialists (frontend-specialist, backend-specialist, database-specialist) for implementing fixes
**Quality**: code-reviewer (for fix validation)
**Security**: security-auditor (validates security test coverage and security-related test fixes)

## Examples

**Fix all tests**: `/test-fix` → Run tests → Analyze failures → Apply fixes → Validate
**Fix specific test**: `/test-fix "auth.test.js"` → Target specific test → Fix issues
**Fix by directory**: `/test-fix --files "src/auth/"` → Fix all tests in directory
**Unit tests only**: `/test-fix --type unit` → Run unit tests → Fix failures
**Re-run failures**: `/test-fix --failed-only` → Skip passing tests → Fix only failures
**Watch mode**: `/test-fix --watch "auth"` → Continuous testing → Auto-fix on changes
**Fast iteration**: `/test-fix --type unit --failed-only` → Unit tests + failures only