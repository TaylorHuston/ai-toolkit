---
tags: ["workflow", "git", "branching"]
description: "Unified branch operations with git-workflow enforcement"
argument-hint: "create ISSUE-ID | merge [target] | delete branch-name | switch branch-name | status | \"natural language\""
allowed-tools: ["Bash", "Read", "Edit", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/git-workflow.md  # Source of truth for branching rules, merge validation, naming patterns
---

# /branch Command

**WHAT**: Unified branch operations (create, merge, delete, switch, status) with git-workflow enforcement.

**WHY**: Ensure consistent branch naming, enforce quality gates on merge, prevent workflow violations.

**HOW**: See git-workflow.md for branching rules, merge validation requirements, and naming patterns.

**CRITICAL**: Always read `docs/development/workflows/git-workflow.md` FIRST for project-specific rules.

## Usage

```bash
/branch create TASK-001              # Create work branch
/branch merge [develop|main]         # Merge with validation
/branch delete feature/TASK-001      # Delete merged branch
/branch switch develop               # Switch branches
/branch status                       # Show workflow state

# Natural language
/branch "merge to develop"
/branch "create for TASK-001"
```

## Operations

### Create
1. Read git-workflow.md for branching pattern and base branch
2. Parse issue ID → determine type (TASK → feature, BUG → bugfix)
3. Create branch following pattern (e.g., `feature/TASK-001` from `develop`)
4. Switch to new branch

### Merge
1. Read git-workflow.md for merge rules
2. Apply validation based on target:
   - **To develop**: Run tests (BLOCK if fail)
   - **To main**:
     - Verify source is `develop` (BLOCK if feature/bugfix)
     - Run staging health checks (BLOCK if fail)
     - Hotfixes require explicit justification
3. If pass: merge and push
4. If fail: block with clear error

**Critical Rules:**
- ❌ Work branches (feature/*, bugfix/*) CANNOT merge to main
- ✅ Only develop can merge to main
- ⚠️ Hotfix branches require ADR justification

### Delete
1. Check if fully merged
2. Confirm with user
3. Delete local and remote

### Switch
1. Check uncommitted changes (offer stash)
2. Switch to target
3. Pull latest

### Status
1. Show current branch and issue context
2. Check test/validation status
3. Show merge requirements

## Configuration

Reads `docs/development/workflows/git-workflow.md` YAML frontmatter:

```yaml
---
branching_strategy: "three-branch"
main_branch: "main"
develop_branch: "develop"
work_branch_pattern: "type/ISSUE-ID"
merge_rules:
  to_develop: "tests_pass"
  to_main: "staging_validated"
---
```

**If missing**: Use defaults (three-branch: main ← develop ← work branches)

## Validation Rules

Defined in git-workflow.md, enforced by this command:

**Merge to Develop (Staging)**:
- ✅ Source: Any work branch (feature/*, bugfix/*)
- ✅ Validation: All tests MUST pass
- ❌ BLOCKS if tests fail

**Merge to Main (Production)**:
- ✅ Source: ONLY `develop` branch
- ❌ BLOCKS work branches (feature/*, bugfix/*)
- ✅ Validation: Staging health checks MUST pass
- ⚠️ Exception: hotfix/* (requires ADR justification)

**Branch Naming**: Follow configured pattern, auto-detect test framework

See `git-workflow.md` for complete rules and rationale.

## Agent Coordination

- **devops-engineer** - Health checks, deployment validation
- **test-engineer** - Test execution, result parsing

## Command Instructions

```
Task: "Execute Git branch operation following project workflow rules.

CRITICAL:
1. FIRST: Read docs/development/workflows/git-workflow.md
   - Extract YAML frontmatter
   - Understand strategy, patterns, merge rules
   - Use as source of truth

2. Execute operation:
   - create: Follow pattern, create from base
   - merge: Apply validation, block if fail
   - delete: Check merge status, confirm, delete
   - switch: Handle uncommitted, switch, pull
   - status: Show state and readiness

3. Validation enforcement:
   - Merge to develop: Run tests (auto-detect)
   - Merge to main: Verify source=develop, check staging
   - Block if fail, show clear errors

4. Git commands:
   - git checkout, branch, merge, push
   - Parse output, handle conflicts

5. Natural language:
   - Parse intent from quoted string
   - Map to structured operation
   - Execute with same validation

All rules defined in git-workflow.md - this command enforces them."
```

## Related

- `/implement` - Creates work branches automatically
- `/commit` - Branch-aware commit messages
- `/docs` - Workflow documentation reference
- `git-workflow.md` - **Source of truth** for all branching rules
