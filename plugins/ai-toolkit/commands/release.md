---
tags: ["versioning", "release", "deployment", "git", "tagging"]
description: "Release new version following semantic versioning guidelines"
aliases: ["version", "tag-release"]
allowed-tools: ["Read", "Edit", "Bash", "Grep", "AskUserQuestion"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/conventions/versioning-and-releases.md  # Version bump rules, file update list, semver strategy
---

# /release Command

**WHAT**: Automate version releases with CHANGELOG updates, version file synchronization, and annotated git tags.

**WHY**: Ensure consistent version management across all project files following semantic versioning and project-specific guidelines.

**HOW**: Read versioning convention, analyze changes, suggest version bump, update files, create git tag.

## Usage

```bash
/release                    # Interactive: analyze changes and suggest version
/release 0.13.0             # Release specific version
/release patch              # Release patch version (bug fixes only)
/release minor              # Release minor version (new features)
/release major              # Release major version (breaking changes)
```

## Workflow

1. **Read Convention** - Load version bump rules from `docs/development/conventions/versioning-and-releases.md`
2. **Validate Preconditions**
   - Git working directory must be clean
   - CHANGELOG.md [Unreleased] section must have content
   - Must be on appropriate branch (defined in convention)
3. **Analyze Changes** - Parse CHANGELOG [Unreleased] to determine appropriate version bump
4. **Get Confirmation** - Show suggested version with rationale, ask user to proceed
5. **Update Files** - Synchronize version across all project files (list from convention)
6. **Create Git Tag** - Annotated tag with release notes from CHANGELOG
7. **Display Summary** - Show updated files, created tag, and next steps

## What Gets Updated

The command reads `versioning-and-releases.md` to determine:
- Which files contain version numbers
- Version bump rules (pre-1.0.0 vs post-1.0.0)
- Git tag format and content
- Branch requirements for releases

**Common version files:**
- CHANGELOG.md (transform [Unreleased] → [version] - date)
- package.json, pyproject.toml, Cargo.toml (language-specific)
- CLAUDE.md frontmatter (version, last_updated)
- Plugin metadata files (for plugin projects)

## Example Output

```
Release Analysis
================
Current version: 0.30.0

Changes in [Unreleased]:
- Added: 2 new features
- Changed: 1 breaking change
- Fixed: 1 bug fix

Suggested version: 0.31.0 (MINOR)
Reason: Breaking changes increment MINOR in pre-1.0.0

Proceed with v0.31.0? (yes/no/custom): yes

Updating files...
✓ CHANGELOG.md
✓ .claude-plugin/marketplace.json
✓ plugins/ai-toolkit/.claude-plugin/plugin.json
✓ CLAUDE.md

Creating git tag...
✓ Created annotated tag v0.31.0

Next steps:
  git push origin main --tags
```

## Error Prevention

**Blocks release if:**
- Uncommitted changes exist → Suggests `/commit` first
- CHANGELOG.md [Unreleased] is empty → Suggests `/changelog` first
- Version already exists → Suggests next available versions
- Wrong branch for release type → Warns and asks for confirmation

## Integration

**Typical workflow:**
```bash
/implement TASK-001 --full    # Complete implementation
/quality                       # Verify quality
/changelog                     # Document changes
/commit                        # Commit changes
/release                       # Create release
git push origin main --tags    # Push to remote
```

**With CI/CD:**
Pushing the tag triggers automated deployment (if configured).

## Related Commands

- `/changelog` - Document changes before release
- `/commit` - Commit changes before release
- `/project-status` - Review overall project state

## Configuration

All release rules, version bump logic, and file lists are defined in:
`docs/development/conventions/versioning-and-releases.md`

Edit that file to customize your project's versioning strategy.
