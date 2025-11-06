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

**WHAT**: Update CHANGELOG.md with undocumented changes from git history.

**WHY**: Keep CHANGELOG accurate and reduce pre-release work.

**HOW**: See versioning-and-releases.md for format, categories, and writing style guidelines.

## Usage

```bash
/changelog              # Check and update CHANGELOG
```

## Execution Steps

### 1. Read Current State

```bash
# Parse CHANGELOG.md
Grep: "^## \[" CHANGELOG.md  # Last release version
Read: CHANGELOG.md            # [Unreleased] section

# Get last release tag
Bash: git describe --tags --abbrev=0
```

### 2. Analyze Git History

```bash
# Commits since last release (e.g., v0.12.0)
Bash: git log v0.12.0..HEAD --oneline --no-merges
Bash: git diff --name-only v0.12.0..HEAD
```

**Focus on user-facing files:**
- `commands/*.md` - New/changed commands
- `agents/*.md` - New/changed agents
- `templates/starter/*` - Template changes
- `README.md`, `docs/*` - Documentation

### 3. Detect Undocumented Changes

**Change types:**
- **Added**: New commands, agents, templates
- **Changed**: Modified functionality, improvements
- **Fixed**: Bug fixes (commits with "fix", "bug", "issue")
- **Removed**: Deleted files (BREAKING if user-facing)

**Compare to CHANGELOG [Unreleased]** - Flag missing entries.

### 4. Draft Suggestions

**Format** (per versioning-and-releases.md):

```markdown
## [Unreleased]

### Added
- **Feature Name**: User-facing description
  - Benefit 1
  - Benefit 2

### Changed
- **Improvement**: What changed and impact

### Fixed
- **Bug Fix**: What was broken and how fixed
```

**Writing style**:
- ✅ "Added `/changelog` command to update CHANGELOG automatically"
- ❌ "Added changelog.md file to commands directory"

### 5. Update CHANGELOG

Ask user confirmation:
```
Update CHANGELOG.md with these entries? (yes/no/edit)
```

If yes:
- Add entries to `[Unreleased]` section
- Maintain alphabetical order within categories
- Preserve existing content

## Example Output

```
Checking CHANGELOG.md...

Last release: [0.12.0] - 2025-11-03
Commits since v0.12.0: 8 commits, 15 files changed

Undocumented changes:
1. NEW: commands/changelog.md
2. NEW: commands/release.md
3. MODIFIED: commands/ui-design.md (Playwright review)

Suggested entries:
### Added
- `/changelog` command - Auto-update CHANGELOG from git history
- `/release` command - Release versions with semantic versioning

### Changed
- `/ui-design` validates mockups in browser before presenting

Update CHANGELOG? (yes/no)
```

## When to Use

**Run when:**
- Before creating PR
- After completing feature
- Before release
- Periodically to stay current

**Skip when:**
- No user-facing changes
- Already documented
- Work in progress

## Integration

```
/implement → /changelog → /commit → PR
                              ↑
                    Update before commit
```

## Error Handling

- **No commits**: "CHANGELOG up to date - no commits since v0.12.0"
- **Already complete**: "All changes documented in [Unreleased]"
- **No CHANGELOG**: Create with versioning-and-releases.md format

## Related Commands

- `/release` - Release version (uses CHANGELOG)
- `/commit` - Create commit (may update CHANGELOG)

## Notes

- User approval required before updating
- Follows Keep a Changelog format
- Focus on user impact, not implementation
- Source of truth: versioning-and-releases.md
