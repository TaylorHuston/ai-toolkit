---
tags: ["documentation", "versioning", "changelog", "maintenance"]
description: "Check if CHANGELOG is up to date and update as necessary"
aliases: ["update-changelog"]
allowed-tools: ["Read", "Edit", "Bash", "Grep", "Glob"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/versioning-and-releases.md  # CHANGELOG format, categories, writing style
---

# /changelog Command

Check if CHANGELOG.md is up to date with recent work and update it as necessary.

## Purpose

Ensure CHANGELOG.md accurately reflects all user-facing changes by:
- Analyzing recent git commits since last release
- Identifying unreleased changes not yet documented
- Suggesting appropriate CHANGELOG entries
- Updating CHANGELOG.md with proper formatting

**Use proactively** - Run before creating PRs, after completing features, or when preparing releases.

## Usage

```bash
/changelog              # Check and update CHANGELOG
```

## How It Works

1. **Read current CHANGELOG.md**:
   - Parse to find last released version (e.g., `[0.12.0]`)
   - Check `[Unreleased]` section for existing entries
   - Note CHANGELOG format and style

2. **Analyze recent commits**:
   - Get git log since last release tag
   - Parse commit messages (conventional commits if used)
   - Identify file changes and patterns

3. **Detect undocumented changes**:
   - Compare commits to CHANGELOG entries
   - Look for user-facing changes not yet documented:
     - New features (new commands, agents, capabilities)
     - Bug fixes (fixes to existing functionality)
     - Breaking changes (removed features, changed APIs)
     - Improvements (performance, UX, documentation)

4. **Suggest CHANGELOG entries**:
   - Categorize changes (Added, Changed, Fixed, Removed, etc.)
   - Draft entries following Keep a Changelog format
   - Focus on user impact, not implementation details
   - Follow project's writing style

5. **Update CHANGELOG.md**:
   - Add new entries to `[Unreleased]` section
   - Maintain proper formatting
   - Keep entries concise and user-focused

6. **Verify completeness**:
   - Display what was added
   - Ask if anything is missing
   - Suggest additional context if needed

## Command Flow

### 1. Read Current State

**Parse CHANGELOG.md:**
```bash
# Extract last release version
grep -m 1 "^## \[" CHANGELOG.md

# Read [Unreleased] section
sed -n '/^## \[Unreleased\]/,/^## \[/p' CHANGELOG.md
```

**Get last release tag:**
```bash
git describe --tags --abbrev=0
```

### 2. Analyze Git History

**Get commits since last release:**
```bash
# If last tag is v0.12.0
git log v0.12.0..HEAD --oneline --no-merges

# Parse commit messages for patterns
git log v0.12.0..HEAD --format="%s" --no-merges
```

**Analyze file changes:**
```bash
# What files were modified
git diff --name-only v0.12.0..HEAD

# Focus on user-facing files
# - commands/*.md (new/changed commands)
# - agents/*.md (new/changed agents)
# - templates/starter/* (template changes)
# - README.md, docs/* (documentation)
```

### 3. Identify Missing Entries

**User-facing changes to check:**

- **New commands**: Files added to `commands/`
- **New agents**: Files added to `agents/`
- **New templates**: Files added to `templates/starter/`
- **Modified commands/agents**: Significant changes to existing files
- **Bug fixes**: Commits with "fix", "bug", "issue" in message
- **Breaking changes**: Commits with "BREAKING", removed files

**Compare to CHANGELOG:**
- Check if each change is already documented
- Flag undocumented changes

### 4. Draft Suggestions

**Format (Keep a Changelog):**

```markdown
## [Unreleased]

### Added
- **New Feature**: Brief description
  - Detail 1
  - Detail 2
  - Benefit to users

### Changed
- **Improvement**: What changed and why
  - Impact on users
  - Migration notes if needed

### Fixed
- **Bug Fix**: What was broken
  - How it's fixed
  - Issue reference if applicable

### Removed
- **Deprecated Feature**: What was removed
  - Why it was removed
  - Migration path
```

**Writing Style Guidelines:**

✅ **DO** - Focus on user impact:
- "Added `/changelog` command to automatically check and update CHANGELOG"
- "Fixed `/epic` failing to detect Jira configuration"
- "Improved `/ui-design` with automatic Playwright testing"

❌ **DON'T** - Implementation details:
- "Added changelog.md file to commands directory"
- "Fixed bug in line 45 of epic.md"
- "Refactored ui-design command code"

### 5. Update CHANGELOG

**Add entries to [Unreleased]:**

```markdown
## [Unreleased]

### Added
[New entries here]

### Changed
[Updated entries here]

### Fixed
[Bug fix entries here]
```

**Maintain alphabetical/logical order within categories.**

## Example Session

```
User: /changelog

AI: Checking CHANGELOG.md status...

Last release: [0.12.0] - 2025-11-03
Analyzing commits since v0.12.0...

Found 8 commits, 15 files changed.

Undocumented changes detected:

1. NEW COMMAND: commands/changelog.md
   → Not in CHANGELOG [Unreleased] > Added

2. NEW COMMAND: commands/release.md
   → Not in CHANGELOG [Unreleased] > Added

3. MODIFIED: commands/ui-design.md (added Playwright review)
   → Not in CHANGELOG [Unreleased] > Changed

4. MODIFIED: commands/epic.md (improved Jira detection)
   → Not in CHANGELOG [Unreleased] > Changed

Suggested CHANGELOG entries:

### Added

- **`/changelog` Command**: Automatically check and update CHANGELOG.md
  - Analyzes git commits since last release
  - Detects undocumented changes
  - Suggests properly formatted CHANGELOG entries
  - Follows Keep a Changelog format and versioning guidelines

- **`/release` Command**: Release new versions following semantic versioning
  - Automated version bumping (MAJOR.MINOR.PATCH)
  - CHANGELOG release preparation
  - Git tagging with proper annotations
  - Follows versioning-and-releases.md guidelines

### Changed

- **`/ui-design` Playwright review**: Command now validates mockups in browser before presenting
  - Catches broken layouts and JavaScript errors
  - Tests responsive behavior at key breakpoints
  - Ensures users only see working mockups

- **`/epic` Jira detection**: Improved configuration checking to prevent local file creation
  - Explicitly confirms mode (Jira vs local) to user
  - Multiple reminders that Jira-enabled means no local epic files
  - Updated pm-guidelines.md with detection steps

Update CHANGELOG.md with these entries? (yes/no/edit)

User: yes

AI: ✓ Updated CHANGELOG.md
   - Added 2 entries to [Unreleased] > Added
   - Added 2 entries to [Unreleased] > Changed

CHANGELOG is now up to date.

Next steps:
- Continue development, or
- Run /release when ready to release
```

## When to Use

**Run `/changelog` when:**

✅ **Before creating PR** - Ensure changes are documented
✅ **After completing feature** - Document what was added
✅ **After fixing bug** - Document what was fixed
✅ **Before release** - Verify all changes documented
✅ **Periodically** - Check for missing entries

**Skip `/changelog` when:**

❌ **No user-facing changes** - Internal refactoring only
❌ **Already documented** - CHANGELOG is current
❌ **Work in progress** - Not yet ready to document

## Integration with Workflow

```
/implement TASK-001 1.1 → /test-fix → /changelog → /commit → PR
                                          ↑
                              Update CHANGELOG before commit
```

**With /release:**
```
Development → /changelog (check) → /release 0.13.0 → Tagged release
```

## Edge Cases

### No Commits Since Last Release

```
AI: CHANGELOG is up to date.
    No commits since last release (v0.12.0).
```

### CHANGELOG Already Complete

```
AI: Analyzing changes...
    ✓ All changes documented in [Unreleased]
    CHANGELOG is up to date.
```

### Multiple Categories

```
AI: Detected changes across multiple categories:
    - Added: 2 new commands
    - Changed: 1 command improvement
    - Fixed: 1 bug fix

    Updating all categories...
```

### Breaking Changes

```
AI: ⚠️  Breaking change detected:
    - Removed: commands/old-command.md

    Suggested entry:
    ### Removed
    - **`/old-command`**: Removed deprecated command (BREAKING)
      - Use `/new-command` instead
      - Migration: [explain changes needed]

    Add to CHANGELOG? (yes/no)
```

## Implementation Notes

**Reading CHANGELOG:**
- Parse markdown headers to find sections
- Handle both existing and missing [Unreleased] section
- Preserve formatting and existing entries

**Git Analysis:**
- Use `git log` with `--format` for parsing
- Focus on commits since last tag
- Use `git diff --name-only` for file changes

**Change Detection:**
- Pattern match file paths (commands/, agents/, templates/)
- Parse commit messages for keywords
- Compare to existing CHANGELOG entries

**Entry Generation:**
- Follow Keep a Changelog categories
- Use present tense ("Add", not "Added")
- Focus on user benefits
- Include relevant details in sub-bullets

**Updating File:**
- Use Edit tool to add entries to [Unreleased]
- Maintain proper markdown formatting
- Preserve existing content

## Related Commands

- `/release` - Release new version (uses CHANGELOG)
- `/commit` - Create git commit (may update CHANGELOG)
- `/docs` - Documentation updates (may affect CHANGELOG)

## Related Guidelines

- `docs/development/guidelines/versioning-and-releases.md` - CHANGELOG format, writing style, categories
- Root `CHANGELOG.md` - This project's changelog (source of truth)

## Notes

- **Proactive use encouraged** - Catch missing entries early
- **AI suggests, user approves** - Always confirm before updating
- **Follows project style** - Matches existing CHANGELOG format
- **User-facing only** - Internal refactoring doesn't require entries
- **Living document** - CHANGELOG evolves with project

---

**Best Practice**: Run `/changelog` regularly to keep documentation current and reduce pre-release work.
