---
# === Metadata ===
template_type: "guideline"
created: "2025-10-30"
last_updated: "2025-10-30"
status: "Active"
target_audience: ["AI Assistants", "Development Team"]
description: "Three-branch Git workflow with automated merge validation and production safety rules"

# === Git Configuration (Machine-readable for AI agents) ===
branching_strategy: "three-branch"     # main, develop, work branches
main_branch: "main"                    # production
develop_branch: "develop"              # staging
work_branch_pattern: "type/ISSUE-ID"  # feature/TASK-001, bugfix/BUG-003
commit_convention: "conventional"      # conventional commits
pr_required: true
squash_merge: false
merge_rules:
  to_develop: "tests_pass"             # All tests must pass
  to_main: "staging_validated"         # Staging deployment validated

# Commit Type Inference (for branch-aware commits)
commit_type_inference:
  # File pattern-based type detection
  patterns:
    test_files:
      pattern: ["**/*.test.*", "**/*.spec.*", "tests/**", "test/**", "__tests__/**"]
      type: "test"

    doc_files:
      pattern: ["**/*.md", "docs/**", "documentation/**", "README*"]
      type: "docs"

    config_files:
      pattern: ["**/*.config.*", "**/*.json", "**/*.yaml", "**/*.yml", ".*rc", ".*ignore"]
      type: "chore"

    style_files:
      pattern: ["**/*.css", "**/*.scss", "**/*.sass", "**/*.less"]
      type: "style"

    build_files:
      pattern: ["**/package*.json", "**/Makefile", "**/Dockerfile", "**/*.build.*"]
      type: "build"

  # Keyword-based type detection (from commit message or file content)
  keywords:
    fix: ["fix", "bug", "issue", "error", "crash", "patch"]
    feat: ["feature", "add", "new", "implement"]
    refactor: ["refactor", "restructure", "cleanup", "improve"]
    perf: ["performance", "optimize", "faster", "speed"]
    docs: ["document", "readme", "guide", "tutorial"]
    test: ["test", "spec", "coverage"]
    style: ["style", "format", "lint", "prettier"]
    chore: ["chore", "deps", "dependency", "version", "release"]
    ci: ["ci", "cd", "workflow", "pipeline", "github"]

  # Default type when no pattern matches
  default_type: "feat"  # New files default to feat (new feature)
---

# Git Workflow Guidelines

**Referenced by Commands:** `/branch`, `/commit`, `/implement`

**IMPORTANT:** Never commit without the express permission from the user first.

## Quick Reference

This guideline defines our three-branch Git workflow with automated merge validation. Commands like `/branch` and `/implement` enforce these rules automatically.

## Branching Strategy

**Default**: Three-branch model for production safety and staging validation

The three-branch model provides:
- **Production stability** - `main` branch always represents deployed production code
- **Staging validation** - `develop` branch for pre-production testing
- **Isolated development** - Work branches for feature/bug development

### Branch Structure

```
main (production)
  ↑
  └─ develop (staging)
       ↑
       ├─ feature/TASK-001  (work branch)
       ├─ feature/TASK-002  (work branch)
       └─ bugfix/BUG-001    (work branch)
```

### Branch Naming Convention

**Strict format**: `type/ISSUE-ID`

```
feature/TASK-001    # Feature implementation
bugfix/BUG-003      # Bug fix
```

**Naming rules:**
- Type must be `feature` (for TASK-###) or `bugfix` (for BUG-###)
- Must include issue ID exactly as it appears (TASK-001, BUG-003, etc.)
- No additional description allowed (keeps branches tied to issues)

### Main Branches

- **Production**: `main` - Deployed to production environment
- **Staging**: `develop` - Deployed to staging environment for validation
- **Work branches**: `type/ISSUE-ID` - Short-lived development branches

## Branch Merge Rules

**CRITICAL**: These rules protect production stability and must be followed.

### Normal Development Flow (95% of cases)

```
feature/TASK-001 → develop (tests pass) → main (staging validated)
       ↓              ↓                      ↓
   your work      staging env          production env
```

**Rules:**
1. ✅ **Work branches MUST merge to `develop` only**
   - feature/* → develop (after tests pass)
   - bugfix/* → develop (after tests pass)
   - ❌ **NEVER merge work branches directly to main**

2. ✅ **Only `develop` MUST merge to `main`**
   - develop → main (after staging validation)
   - ❌ **NEVER merge work branches to main**
   - ⚠️ Exception: Emergency hotfixes (see below)

3. ✅ **Environment validation required**
   - Merge to develop: All tests must pass
   - Merge to main: Staging environment must be validated

### Emergency Hotfix Flow (rare, <5% of cases)

**When to use:** Critical production bugs requiring immediate fix (security, data loss, total outage)

```
hotfix/critical-bug → main (emergency only)
                    ↓
                 develop (backport)
```

**Requirements:**
1. Document in ADR why emergency hotfix was needed
2. Use `hotfix/*` branch prefix (not `feature/*` or `bugfix/*`)
3. Backport to develop immediately after main deployment
4. Create post-mortem explaining why normal flow wasn't possible

**This should be rare.** Most "urgent" bugs can wait for staging validation.

## Branch Lifecycle

### Creating Work Branches

**Two methods** - choose based on preference:

**Automatic**: `/implement TASK-001 1.1` prompts to create `feature/TASK-001` (quick iteration)
**Explicit**: `/branch create TASK-001` creates branch before work (manual control)

Both create the same branch from `develop`. Only difference is timing.

### Merging Work Branches

**To develop (staging):**
```bash
/branch merge develop
```
Enforces: All tests pass, no uncommitted changes. Merge blocked if tests fail.

**To main (production):**
```bash
# Must merge to develop first, then:
git checkout develop
/branch merge main
```
Enforces: Source must be `develop` (work branches blocked), staging health checks pass.

### Deleting Work Branches

After merging:
```bash
/branch delete feature/TASK-001
```
Verifies branch is fully merged before deletion.

## Commit Conventions

**Format**: Conventional Commits

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**Types**: feat, fix, docs, style, refactor, test, chore, perf

The `/commit` command automatically includes issue references from your branch name:

```bash
# On branch feature/TASK-001
/commit "implement user authentication"
# → Generates: feat(TASK-001): implement user authentication
```

### Commit Type Inference

**The `/commit` command automatically determines commit type** based on file patterns and keywords, configured in the YAML frontmatter above.

**How It Works:**
1. Analyzes staged files to detect patterns (test files, docs, config, etc.)
2. Checks commit message for type keywords (fix, feature, refactor, etc.)
3. Applies the most specific type match found
4. Falls back to default_type (feat) if no pattern matches

**File Pattern Examples:**

```bash
# Test files → type: test
git add src/**/*.test.js
/commit "add login tests"
# → test(TASK-001): add login tests

# Documentation → type: docs
git add README.md docs/api.md
/commit "update API documentation"
# → docs(TASK-001): update API documentation

# Config files → type: chore
git add package.json .eslintrc
/commit "update dependencies"
# → chore(TASK-001): update dependencies

# Source code → type: feat (default)
git add src/auth.js
/commit "add authentication"
# → feat(TASK-001): add authentication
```

**Keyword Detection** (from commit message):

```bash
/commit "fix login timeout issue"
# → fix(TASK-001): fix login timeout issue

/commit "refactor database connection"
# → refactor(TASK-001): refactor database connection

/commit "optimize query performance"
# → perf(TASK-001): optimize query performance
```

**Customization:**
Edit the `commit_type_inference` section in the YAML frontmatter to:
- Add new file patterns (e.g., infrastructure files → `infra` type)
- Modify existing patterns (e.g., treat JSON as `feat` instead of `chore`)
- Add custom keywords for your team's conventions
- Change default_type for your workflow

**Mixed Changes:**
When staging multiple file types, the command picks the most specific match:
- Tests + source code → `test` (tests are more specific)
- Docs + config → `docs` (documentation changes are notable)
- Multiple source files → Uses keyword detection or defaults to `feat`

### Commit Message Rules

- **Length**: 50 chars for subject, 72 chars for body
- **Tense**: Imperative ("add feature" not "added feature")
- **Capitalization**: Lowercase subject (conventional commits style)
- **Punctuation**: No period at end of subject

## Merge Validation Rules

The `/branch merge` command enforces quality gates based on target branch.

**Note**: These merge gates are a **subset of the quality gates** defined in `development-loop.md`. The full development loop ensures quality at the phase level; these merge rules enforce quality at the branch integration level.

- **Per-Phase Gates** (development-loop.md) → Enforced during `/implement`
- **Per-Task Gates** (development-loop.md) → Enforced by `/branch merge develop` (below)
- **Merge to Production** → Additional staging validation rules (below)

### Merging to `develop` (Staging)

**Enforces Per-Task Gates** from `development-loop.md`. See that file for complete quality gate definitions.

**Technical checks performed by `/branch merge develop`:**
1. ✅ All tests pass (full suite execution)
2. ✅ No uncommitted changes
3. ✅ Branch is up to date with remote
4. ⚠️ Documentation sync validation (see development-loop.md "Documentation Synchronization Checklist")

**Process:**
```bash
/branch merge develop
# 1. Runs full test suite
# 2. Shows test results
# 3. Blocks merge if tests fail
# 4. Executes merge if all pass
# 5. Pushes to remote
```

### Merging to `main` (Production)

⚠️ **CRITICAL**: Source branch MUST be `develop`. Work branches (feature/*, bugfix/*) cannot merge directly to main.

**Required validations:**
1. ✅ Source branch MUST be `develop` (enforced - command will block otherwise)
2. ✅ Staging deployment health checks MUST pass
3. ✅ Automated smoke tests MUST pass
4. ✅ No uncommitted changes

**Process:**
```bash
# Switch to develop first
git checkout develop

# Then merge to main
/branch merge main
# 1. Verifies source is develop (BLOCKS if not)
# 2. Runs health checks against staging
# 3. Validates deployment status
# 4. Blocks merge if checks fail
# 5. Executes merge if validated
# 6. Pushes to remote
```

**Why this matters:**
- Production deploys ONLY from validated staging code
- Catches environment-specific issues before production
- Ensures staging and production stay synchronized
- Prevents untested code from reaching production

**If you need emergency hotfix:** See "Emergency Hotfix Flow" section above for rare exception process.

## Pull Request Workflow

### PR Requirements

- **Code Review**: Pull requests required for all merges
- **Tests**: Must pass before merge (enforced by `/branch merge`)
- **CI/CD**: Tests run automatically on push
- **Conflict Resolution**: Merge commits (no squash by default)

### Merge Strategy

- **Merge Commit**: Default - preserves full history
- **Squash Merge**: Disabled by default (loses granular history)
- **Rebase**: Not recommended for shared branches

**Rationale**: Merge commits maintain the full development history and make it easier to understand feature evolution and revert changes if needed.

## Release Process

### Versioning

- **Scheme**: Semantic Versioning (SemVer) - MAJOR.MINOR.PATCH
- **Tagging**: Tags created on `main` branch after merge
- **Changelog**: Maintained in CHANGELOG.md following Keep a Changelog format

### Deployment

- **Staging**: Automatic deployment from `develop` branch
- **Production**: Automatic deployment from `main` branch
- **Approval**: Merges to `main` gated by staging validation

## Workflow Commands

### `/branch` - Branch operations
```bash
/branch create TASK-001        # Create work branch
/branch merge [target]         # Merge with validation
/branch delete branch-name     # Delete merged branch
/branch switch branch-name     # Switch branches
/branch status                 # Show workflow state
```

### `/implement` - Start work
```bash
/implement TASK-001 1.1
# → Creates feature/TASK-001 if needed
# → Warns if on wrong branch
# → Executes implementation phase
```

### `/commit` - Create commits
```bash
/commit "add user authentication"
# → Includes issue ID from branch name
# → Follows conventional commits format
```

## Examples

### Good Commit Messages

```
feat(TASK-001): implement user registration endpoint
fix(BUG-003): resolve login session timeout issue
docs(TASK-002): update API documentation
test(TASK-001): add integration tests for auth flow
refactor: simplify database connection logic
```

### Complete Branch Workflow

```bash
# 1. Start work on issue (creates feature/TASK-001 from develop)
/implement TASK-001 1.1
# → Creates feature/TASK-001 from develop
# → Executes phase 1.1

# 2. Continue implementation on feature branch
/implement TASK-001 1.2
/implement TASK-001 2.1

# 3. Commit work on feature branch
/commit "implement authentication logic"

# 4. Merge work branch to develop (staging)
#    ✅ feature/* → develop (tests must pass)
/branch merge develop
# → Runs all tests
# → BLOCKS if any fail
# → Merges if tests pass
# → Deploys to staging

# 5. Test in staging environment
#    (Manual testing, QA, smoke tests on staging)

# 6. Promote develop to production
#    ✅ develop → main (staging must be validated)
#    ❌ NEVER feature/* → main
/branch switch develop
/branch merge main
# → Runs staging health checks
# → BLOCKS if checks fail
# → Merges if validated
# → Deploys to production

# 7. Clean up work branch after successful deploy
/branch delete feature/TASK-001
```

## Related Documentation

- [Versioning and Releases](./versioning-and-releases.md) - Semantic versioning, git tagging, and release process
- [Development Loop](./development-loop.md) - AI-assisted development workflow
- Project [CHANGELOG.md](../../../CHANGELOG.md) - Version history

## General Git Knowledge

For Git best practices, Claude has extensive knowledge of:
- Branching strategies (Git Flow, GitHub Flow, trunk-based)
- Commit message conventions (Conventional Commits, semantic commits)
- Merge strategies (merge commits, squash, rebase)
- Conflict resolution techniques
- Git hooks and automation

Ask questions like "How should I handle [X] in Git?" and Claude will provide guidance based on industry standards and your chosen workflow.
