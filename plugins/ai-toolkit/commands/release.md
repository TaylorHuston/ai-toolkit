---
tags: ["versioning", "release", "deployment", "git", "tagging"]
description: "Release new version following semantic versioning guidelines"
aliases: ["version", "tag-release"]
allowed-tools: ["Read", "Edit", "Bash", "Grep", "AskUserQuestion"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/versioning-and-releases.md
---

# /release Command

## WHAT
Automate version releases with proper semver compliance, updating CHANGELOG.md, version files, and creating annotated git tags.

## WHY
Ensures consistency across version management following semver 2.0.0 and project versioning guidelines.

## HOW

### Usage
```bash
/release                # Interactive: analyze and suggest version
/release 0.13.0         # Release specific version
/release patch|minor|major  # Release by bump type
```

### Pre-Execution Context

**Read and validate:**
- CHANGELOG.md [Unreleased] section (must have content)
- Git status (must be clean)
- Current branch (develop/main for release type)
- Current version from CHANGELOG/version files
- Project-specific version files (detect from repo)

### Execution Steps

**1. Analyze changes:**
- Parse CHANGELOG [Unreleased] sections
- Detect version bump type:
  - MAJOR: Breaking changes, removed features (pre-1.0.0 → MINOR)
  - MINOR: New features, enhancements
  - PATCH: Bug fixes only
- Suggest version with rationale

**2. Get confirmation:**
```bash
# Interactive mode
grep -A 20 "## \[Unreleased\]" CHANGELOG.md
# Suggest version based on change analysis
# Ask: "Proceed with v{version}? (yes/no/custom)"

# Direct mode with version/keyword
# Validate requested version
# Show changes being released
# Ask: "Confirm release? (yes/no)"
```

**3. Update files:**
```bash
# Transform CHANGELOG [Unreleased] → [version] - date
# Update version in project files (detect which exist):
# - .claude-plugin/marketplace.json (if ai-toolkit)
# - plugins/ai-toolkit/.claude-plugin/plugin.json (if ai-toolkit)
# - package.json (if Node.js)
# - pyproject.toml (if Python)
# - Cargo.toml (if Rust)
# - README.md (version references)
# - CLAUDE.md frontmatter (version, last_updated)
```

**4. Create git tag:**
```bash
# Extract CHANGELOG entries for this version
sed -n '/^## \[{version}\]/,/^## \[/p' CHANGELOG.md | sed '1d;$d'

# Create annotated tag with release notes
git tag -a v{version} -m "Release v{version}

{CHANGELOG_ENTRIES}

🤖 Generated with Claude Code"
```

**5. Display summary:**
- List updated files
- Show created tag
- Provide next steps (push commands)
- Display release notes

### Version Decision Logic

**Pre-1.0.0:**
- Breaking changes → MINOR
- New features → MINOR
- Bug fixes only → PATCH

**Post-1.0.0:**
- Breaking changes → MAJOR
- New features → MINOR
- Bug fixes only → PATCH

### Error Handling

**Uncommitted changes:**
```
Error: Uncommitted changes detected.
Commit or stash changes before releasing.
```

**Empty [Unreleased]:**
```
Error: No changes to release.
Run /changelog to document changes first.
```

**Wrong branch:**
```
Warning: On branch {branch}
Development releases: 'develop'
Production releases: 'main'
Continue anyway? (yes/no)
```

**Version conflict:**
```
Error: Version {version} already exists.
Available: {patch} (patch), {minor} (minor), {major} (major)
```

**Tag creation failure:**
```
Error: Failed to create git tag.
Reason: {error}
Files updated but tag not created.
Manual tag: git tag -a v{version} -m "Release v{version}"
```

### Integration

**Workflow position:**
```
/implement → /changelog → /commit → /release → git push --tags
```

**With CI/CD:**
```
/release → Push tag → Automated deploy
```

### Related
- `/changelog` - Document changes before release
- `/commit` - Commit changes before release
- Guideline: `docs/development/guidelines/versioning-and-releases.md`
