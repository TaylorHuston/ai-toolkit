---
tags: ["versioning", "release", "deployment", "git", "tagging"]
description: "Release new version following semantic versioning guidelines"
aliases: ["version", "tag-release"]
allowed-tools: ["Read", "Edit", "Bash", "Grep", "AskUserQuestion"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/versioning-and-releases.md  # Semver rules, release process, git tagging
---

# /release Command

Release a new version of the project following semantic versioning guidelines.

## Purpose

Automate version releases with proper semver compliance:
- Determine appropriate version number (MAJOR.MINOR.PATCH)
- Update CHANGELOG.md with release date
- Update version in all project files
- Create annotated git tag
- Generate release summary

**Ensures consistency** - Follows versioning-and-releases.md guidelines for all releases.

## Usage

```bash
/release                # Interactive: analyze changes and suggest version
/release 0.13.0         # Release specific version
/release patch          # Release patch version (0.12.0 → 0.12.1)
/release minor          # Release minor version (0.12.0 → 0.13.0)
/release major          # Release major version (0.12.0 → 1.0.0)
```

## How It Works

1. **Read current version**:
   - Parse CHANGELOG.md to find last release
   - Read version from version files (package.json, plugin.json, etc.)

2. **Analyze changes in [Unreleased]**:
   - Parse CHANGELOG [Unreleased] section
   - Categorize changes (Added, Changed, Fixed, etc.)
   - Detect breaking changes

3. **Determine version bump**:
   - **MAJOR**: Breaking changes, removed features
   - **MINOR**: New features, enhancements (pre-1.0.0: also breaking changes)
   - **PATCH**: Bug fixes only, no new features

4. **Suggest version number**:
   - Apply semver rules
   - Consider pre-1.0.0 phase
   - Present recommendation with rationale

5. **Update project files**:
   - Move [Unreleased] to new version in CHANGELOG
   - Update version in metadata files
   - Update last_updated timestamps

6. **Create git tag**:
   - Generate annotated tag with release notes
   - Include CHANGELOG entries in tag message

7. **Display summary**:
   - Show what was updated
   - Provide next steps (push tags, deploy, etc.)

## Command Flow

### 1. Pre-Flight Checks

**Verify prerequisites:**
```bash
# Check git status is clean
git status --porcelain

# Check on correct branch (develop for pre-releases, main for production)
git branch --show-current

# Check [Unreleased] has content
grep -A 5 "## \[Unreleased\]" CHANGELOG.md
```

**Failure conditions:**
- Uncommitted changes → "Commit or stash changes first"
- Empty [Unreleased] section → "No changes to release, run /changelog first"
- Wrong branch for release type → "Development releases on develop, production on main"

### 2. Read Current State

**Parse current version:**
```bash
# From CHANGELOG.md
grep -m 1 "^## \[" CHANGELOG.md | grep -oP '\d+\.\d+\.\d+'

# Example output: 0.12.0
```

**Read [Unreleased] changes:**
```markdown
## [Unreleased]

### Added
- New feature 1
- New feature 2

### Changed
- Breaking change
- Improvement

### Fixed
- Bug fix
```

### 3. Analyze Changes

**Categorize by type:**

- **MAJOR indicators**:
  - "BREAKING" keyword in entries
  - Removed features (### Removed section)
  - API incompatibilities
  - **Exception**: Pre-1.0.0 uses MINOR for breaking changes

- **MINOR indicators**:
  - New features (### Added section)
  - Enhancements (### Changed without breaking)
  - New functionality
  - **Pre-1.0.0**: Also includes breaking changes

- **PATCH indicators**:
  - Only bug fixes (### Fixed section)
  - Documentation updates
  - No new features

### 4. Determine Version

**Apply semver rules:**

```
Current: 0.12.0

Changes detected:
- [Added] 2 new commands
- [Changed] 2 command improvements
- [Fixed] 0 bugs

Pre-1.0.0 rule: New features → MINOR bump
Suggested: 0.13.0
```

**Pre-1.0.0 special rules:**
- MAJOR stays at 0
- MINOR for features/breaking changes
- PATCH for bug fixes only

**Ask for confirmation:**
```
Suggested version: 0.13.0 (MINOR - new features)

Proceed with 0.13.0? (yes/no/custom)
```

### 5. Update CHANGELOG

**Transform [Unreleased] to versioned release:**

```markdown
Before:
## [Unreleased]

### Added
- New command

After:
## [Unreleased]

## [0.13.0] - 2025-11-03

### Added
- New command
```

**Add empty [Unreleased] section at top if not present.**

### 6. Update Version Files

**This project (ai-toolkit plugin):**

Update version in:
1. `.claude-plugin/marketplace.json` - marketplace and plugin version
2. `plugins/ai-toolkit/.claude-plugin/plugin.json` - plugin version
3. Root `README.md` - version reference
4. Root `CLAUDE.md` - version and last_updated in frontmatter

**Other projects (detected from files):**

- `package.json` - version field
- `pyproject.toml` - version field
- `Cargo.toml` - version field
- Project-specific version files

### 7. Create Git Tag

**Generate annotated tag:**

```bash
# Extract CHANGELOG entries for this version
CHANGELOG_ENTRIES=$(sed -n '/^## \[0.13.0\]/,/^## \[/p' CHANGELOG.md | sed '1d;$d')

# Create annotated tag with release notes
git tag -a v0.13.0 -m "Release v0.13.0

$(cat <<EOF
$CHANGELOG_ENTRIES
EOF
)

🤖 Generated with [Claude Code](https://claude.com/claude-code)
"
```

**Tag naming:**
- Format: `v{MAJOR}.{MINOR}.{PATCH}`
- Example: `v0.13.0`
- Lowercase 'v' prefix

### 8. Display Summary

```
✓ Released v0.13.0

Updated files:
- CHANGELOG.md (moved [Unreleased] to [0.13.0])
- .claude-plugin/marketplace.json (version: 0.13.0)
- plugins/ai-toolkit/.claude-plugin/plugin.json (version: 0.13.0)
- README.md (version: 0.13.0)
- CLAUDE.md (version: 0.13.0, last_updated: 2025-11-03)

Created tag: v0.13.0

Next steps:
1. Review changes: git show v0.13.0
2. Push commit: git push origin develop
3. Push tag: git push origin v0.13.0
4. [Optional] Merge to main for production release

Release notes:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[CHANGELOG entries displayed]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Example Sessions

### Example 1: Interactive Release

```
User: /release

AI: Analyzing changes for release...

Current version: 0.12.0
Branch: develop ✓

[Unreleased] changes:
- Added: 2 new commands (/changelog, /release)
- Changed: 2 command improvements
- Total: 4 user-facing changes

Version analysis:
- Has new features → MINOR bump recommended
- No breaking changes
- Pre-1.0.0 rules apply

Suggested version: 0.13.0

Rationale:
- New commands added (MINOR)
- Pre-1.0.0 phase (MAJOR stays 0)
- Following semver 2.0.0

Proceed with v0.13.0? (yes/no/custom)

User: yes

AI: Creating release v0.13.0...

✓ Updated CHANGELOG.md
✓ Updated .claude-plugin/marketplace.json
✓ Updated plugins/ai-toolkit/.claude-plugin/plugin.json
✓ Updated README.md
✓ Updated CLAUDE.md
✓ Created git tag v0.13.0

Release v0.13.0 complete!

Next: git push origin develop --tags
```

### Example 2: Explicit Version

```
User: /release 0.13.0

AI: Releasing v0.13.0...

Current version: 0.12.0
Requested: 0.13.0 (MINOR bump) ✓

Validating...
- [Unreleased] has content ✓
- Version follows semver ✓
- Git status clean ✓

Proceeding with v0.13.0...

[Updates and creates tag]

✓ Released v0.13.0
```

### Example 3: Version Keyword

```
User: /release minor

AI: Current version: 0.12.0
    Bumping MINOR: 0.12.0 → 0.13.0

    Confirm release v0.13.0? (yes/no)

User: yes

[Proceeds with release]
```

### Example 4: Breaking Change (Pre-1.0.0)

```
User: /release

AI: Analyzing changes...

⚠️  Breaking change detected:
    - Removed `/old-command` (documented in [Unreleased])

Version analysis:
- Breaking change normally → MAJOR
- But pre-1.0.0 → use MINOR
- Current: 0.12.0
- Suggested: 0.13.0 (MINOR)

Note: After 1.0.0, this would be 1.0.0 (MAJOR)

Proceed with v0.13.0? (yes/no)
```

### Example 5: Patch Release (Bug Fixes Only)

```
User: /release

AI: Analyzing changes...

[Unreleased] changes:
- Fixed: 2 bug fixes
- No new features
- No breaking changes

Version analysis:
- Only bug fixes → PATCH bump
- Current: 0.12.0
- Suggested: 0.12.1 (PATCH)

Proceed with v0.12.1? (yes/no)

User: yes

[Proceeds with patch release]
```

## Edge Cases

### Empty [Unreleased] Section

```
AI: Error: [Unreleased] section is empty.

No changes to release.

Suggested actions:
1. Run /changelog to detect undocumented changes
2. Add entries manually to CHANGELOG.md
3. Then run /release
```

### Dirty Git Status

```
AI: Error: Uncommitted changes detected.

Changed files:
- commands/changelog.md
- CHANGELOG.md

Commit or stash changes before releasing.
```

### Wrong Branch

```
AI: Warning: On branch feature/new-command

Development releases should be on 'develop' branch.
Production releases should be on 'main' branch.

Continue anyway? (yes/no)
```

### Version Conflict

```
AI: Error: Requested version 0.12.0 already exists.

Current version: 0.12.0
Available bumps: 0.12.1 (patch), 0.13.0 (minor), 1.0.0 (major)

Please specify a valid version.
```

## Version Decision Matrix

| Changes | Pre-1.0.0 | Post-1.0.0 | Example |
|---------|-----------|------------|---------|
| **New features** | MINOR | MINOR | 0.12.0 → 0.13.0 or 1.0.0 → 1.1.0 |
| **Bug fixes only** | PATCH | PATCH | 0.12.0 → 0.12.1 or 1.0.0 → 1.0.1 |
| **Breaking changes** | MINOR | MAJOR | 0.12.0 → 0.13.0 or 1.0.0 → 2.0.0 |
| **Removed features** | MINOR | MAJOR | 0.12.0 → 0.13.0 or 1.0.0 → 2.0.0 |
| **First stable** | MAJOR | N/A | 0.12.0 → 1.0.0 |

## Integration with Workflow

**Development cycle:**
```
/implement → /test-fix → /changelog → /commit → /release → Push
```

**With CI/CD:**
```
/release → Push tag → GitHub Actions → Automated deploy
```

**Multi-branch:**
```
develop: /release 0.13.0 (development release)
main: /release 1.0.0 (production release)
```

## Pre-Release Versions (Advanced)

**Beta releases:**
```bash
/release 0.13.0-beta.1
```

**Release candidates:**
```bash
/release 1.0.0-rc.1
```

**Development snapshots:**
```bash
/release 0.13.0-dev
```

## Related Commands

- `/changelog` - Check/update CHANGELOG before releasing
- `/commit` - Commit changes before releasing
- `/branch` - Manage branches for releases

## Related Guidelines

- `docs/development/guidelines/versioning-and-releases.md` - Complete semver rules, release process
- Root `CHANGELOG.md` - Version history

## Implementation Notes

**Version Parsing:**
- Use regex to extract version numbers
- Parse CHANGELOG for current version
- Validate semver format (MAJOR.MINOR.PATCH)

**Change Analysis:**
- Parse CHANGELOG [Unreleased] section
- Categorize by semver impact
- Detect breaking changes via keywords

**File Updates:**
- Use Edit tool for CHANGELOG
- Update all version references
- Maintain file formatting

**Git Tagging:**
- Use `git tag -a` for annotated tags
- Include CHANGELOG entries in message
- Follow naming convention (v prefix)

**Validation:**
- Check git status before proceeding
- Verify branch for release type
- Validate version numbers
- Confirm [Unreleased] has content

## Notes

- **Always run /changelog first** - Ensure documentation is complete
- **Pre-1.0.0 rules different** - Breaking changes use MINOR, not MAJOR
- **Annotated tags required** - Include metadata and release notes
- **Branch matters** - develop for dev releases, main for production
- **User confirms** - Always ask before creating release

---

**Best Practice**: Run `/changelog` before `/release` to ensure all changes are documented and release notes are complete.
