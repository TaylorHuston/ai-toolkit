---
tags: ["workflow", "git", "commit", "quality"]
description: "Create a proper git commit with quality checks and conventional message"
argument-hint: "[message] [--files FILES] [--amend] [--no-verify] [--interactive]"
allowed-tools: ["Bash", "Read", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/git-workflow.md  # Commit conventions, type inference, branch naming
---

# /commit Command

**WHAT**: Create git commit with quality checks and conventional message formatting.

**WHY**: Ensure consistent commit messages, enforce quality gates, enable semantic versioning and automated changelogs.

**HOW**: See git-workflow.md for commit conventions, type inference rules, and branch-based message generation.

## Usage

```bash
# Basic
/commit                           # Interactive with quality checks
/commit "feat: add user auth"     # Direct with message
/commit --files "src/auth.js"     # Commit specific files

# Advanced
/commit --amend                   # Amend last (safety checks)
/commit "fix: typo" --no-verify   # Skip hooks
/commit --interactive             # Interactive staging + commit
/commit "docs: update" --amend --no-verify  # Combine flags
```

## Process

**Argument Parsing**:
- `--amend`: Amend mode with safety checks
- `--no-verify`: Skip pre-commit hooks
- `--interactive`: Interactive staging first
- `--files`: Stage only specified files

**Branch-Aware Message Generation**:
1. Get branch: `git branch --show-current`
2. Read `git-workflow.md` for branch naming pattern
3. Extract issue ID from branch (feature/TASK-001 → TASK-001)
4. Determine commit type from staged changes using `commit_type_inference` config
   - Analyzes file patterns (tests, docs, config, source)
   - Checks message keywords (fix, feature, refactor)
   - See git-workflow.md "Commit Type Inference" for customization
5. If issue ID extracted and not in message: `type(ISSUE-ID): description`
6. Follow conventional commits format from git-workflow.md

**Standard Flow**:
1. Run pre-commit checks (tests, lint, type-check) unless `--no-verify`
2. Review staged changes
3. Draft message (branch-aware, with issue reference)
4. Ask confirmation

**Amend Mode** (`--amend`):
1. Safety: Verify not pushed (`git log @{u}..HEAD`)
2. Authorship: Verify author matches user
3. Warning: If different author, warn and require confirmation
4. Proceed: Allow amending if safe

**No-Verify Mode** (`--no-verify`):
1. Display warning (checks skipped)
2. Explain use cases (emergency fixes, WIP)
3. Note quality checks won't run
4. Execute with `--no-verify`

**Interactive Mode** (`--interactive`):
1. Run `git add -i` or `git add -p`
2. Review selected changes
3. Proceed with standard flow

## Agent Coordination

**Primary**: code-reviewer (change assessment, quality validation)
**Supporting**: test-engineer (test validation), security-auditor (security-sensitive changes)

## Branch-Aware Messages

Auto-includes issue references from branch:

```bash
# On feature/TASK-001
/commit "add user authentication"
# → feat(TASK-001): add user authentication

# On bugfix/BUG-003
/commit "fix login timeout"
# → fix(BUG-003): fix login timeout

# On develop (no issue)
/commit "refactor database connection"
# → refactor: refactor database connection

# Manual override
/commit "feat(TASK-001): add user auth"
# → Uses as-is: feat(TASK-001): add user auth
```

**Benefits**: Auto issue tracking, no need to remember IDs, links commits to tasks

## Examples

**Interactive**: `/commit` → Review → Generate message → Confirm
**Direct**: `/commit "add auth"` → Adds issue → Quality checks → Commit
**Selective**: `/commit --files "src/auth.js"` → Specific files with issue
**Amend**: `/commit --amend` → Safety checks → Amend
**Skip hooks**: `/commit "fix typo" --no-verify` → Fast commit
**Interactive staging**: `/commit --interactive` → Choose hunks → Commit