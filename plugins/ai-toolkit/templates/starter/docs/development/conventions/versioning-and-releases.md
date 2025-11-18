---
# === Metadata ===
template_type: "guideline"
created: "2025-11-03"
last_updated: "2025-11-13"
status: "Active"
target_audience: ["Development Team", "AI Assistants"]
description: "Semantic versioning strategy, release process, and CHANGELOG maintenance"

# === Configuration ===
versioning_scheme: "semver"  # Semantic Versioning 2.0.0
pre_release_phase: true      # Currently in 0.x.y (pre-1.0.0)
changelog_format: "keep-a-changelog"
---

# Versioning and Release Guidelines

**Referenced by:** Release process, git workflow, CHANGELOG updates

## Quick Reference

This guideline defines our semantic versioning strategy, release process, and CHANGELOG maintenance following [Semantic Versioning 2.0.0](https://semver.org/) and [Keep a Changelog](https://keepachangelog.com/).

## Semantic Versioning (Semver)

### Version Format: `MAJOR.MINOR.PATCH`

```
1.2.3
│ │ │
│ │ └─ PATCH: Bug fixes (backward-compatible)
│ └─── MINOR: New features (backward-compatible)
└───── MAJOR: Breaking changes (incompatible API changes)
```

### Version Increment Rules

**MAJOR version** (`x.0.0`) - Increment when:
- ❌ Breaking API changes (remove endpoints, change response format)
- ❌ Removing features
- ❌ Incompatible updates (users must change their code)

**MINOR version** (`0.x.0`) - Increment when:
- ✅ New features added (backward-compatible)
- ✅ New API endpoints
- ✅ Functionality improvements
- ✅ Deprecations (with backward compatibility)

**PATCH version** (`0.0.x`) - Increment when:
- 🐛 Bug fixes (no new features)
- 🔧 Performance improvements
- 📝 Documentation updates (no code changes)
- 🔒 Security patches

## Pre-1.0.0 Phase (Current)

**Rule**: Major version `0` indicates "in development, not production-ready"

### Version Strategy

While in `0.x.y`, we use:
- `0.x.0` for **new features** (feature branches merged to develop)
- `0.0.x` for **bug fixes** (patch releases)

**Example version progression:**
- `0.1.0` - Initial setup and core structure (TASK-001)
- `0.2.0` - Database integration (TASK-002)
- `0.3.0` - Third-party service integration (TASK-003)
- `0.4.0` - Authentication system (TASK-004)
- `0.4.1` - Security patch (BUG-001)
- `0.5.0` - Next major feature (TASK-005)

**Breaking changes in 0.x.y:**
- Allowed (users expect instability)
- Increment MINOR version (0.4.0 → 0.5.0)
- Document clearly in CHANGELOG under `### Changed` or `### Breaking`

### When to Release 1.0.0

**1.0.0 = First production-ready release**

Criteria for 1.0.0:
- ✅ MVP feature-complete
- ✅ Stable API (no more breaking changes expected)
- ✅ Production deployment ready
- ✅ Security audit passed
- ✅ Performance acceptable
- ✅ Documentation complete

**From that point forward, follow strict semver for MAJOR.MINOR.PATCH.**

## Git Tagging Strategy

### Where to Tag

| Branch | Tag? | When | Example |
|--------|------|------|---------|
| **Feature branches** | ❌ Never | Work-in-progress | - |
| **Develop** | 🟡 Optional | Pre-release tracking | `v0.5.0-dev`, `v0.5.0-beta.1` |
| **Main** | ✅ Always | Production releases | `v1.0.0`, `v1.1.0` |

### Tag Naming Convention

**Production releases** (on main):
```bash
v1.0.0      # Production release
v1.1.0      # Minor feature release
v1.1.1      # Patch release
```

**Pre-releases** (on develop - optional):
```bash
v0.5.0-dev       # Development snapshot
v1.0.0-beta.1    # Beta release
v1.0.0-rc.1      # Release candidate
```

### Creating Tags

**Annotated tags** (always use for releases):
```bash
# Production release
git tag -a v1.0.0 -m "Release v1.0.0: Production-ready MVP with authentication"

# Pre-release
git tag -a v0.5.0-dev -m "Development release v0.5.0: Quote generation"

# Push tags
git push origin --tags
```

**Lightweight tags** (avoid for releases):
```bash
# Don't use for releases (no metadata)
git tag v1.0.0  # ❌ Missing commit message, date, author
```

## Release Process

### Development Release (0.x.y on develop)

**Workflow:**
1. Complete feature and merge to develop
2. Determine version (new feature → MINOR, bug fix → PATCH)
3. Update CHANGELOG.md (move items from Unreleased to new version)
4. Commit: `git commit -m "chore: release v0.5.0"`
5. Optional tag: `git tag -a v0.5.0-dev -m "Development release v0.5.0"`

**Tip:** Use `/release` command to automate this process.

### Production Release (1.0.0+ on main)

**Workflow:**
1. Ensure develop is stable (tests pass, audit complete, docs updated)
2. Merge develop → main: `git merge develop --no-ff`
3. Update CHANGELOG.md (change Unreleased to version with date)
4. Commit and tag: `git commit -m "chore: release v1.0.0"` + annotated tag
5. Push: `git push origin main --tags`
6. Deploy: `npx convex deploy --prod` (Vercel auto-deploys)

**Tip:** Use `/release` command to automate this process.

7. **Create GitHub release** (optional):
   - Go to GitHub → Releases → Create new release
   - Select tag: `v1.0.0`
   - Copy CHANGELOG content as release notes
   - Publish release

8. **Update develop with version commit**:
   ```bash
   git checkout develop
   git merge main  # Bring version commit back to develop
   git push origin develop
   ```

## CHANGELOG Maintenance

### Format (Keep a Changelog)

```markdown
# Changelog

## [Unreleased]

### Added
- New features not yet released

### Changed
- Changes to existing features

### Deprecated
- Features being phased out

### Removed
- Removed features

### Fixed
- Bug fixes

### Security
- Security improvements

## [1.1.0] - 2025-12-01

### Added
- Twitter OAuth integration
- Quote export to PDF

## [1.0.0] - 2025-11-15

### Added
- Initial production release
- User authentication
- Quote generation
```

### Category Guidelines

**Added** - New features users can now use:
- "User authentication with email/password and Google OAuth"
- "Quote export to PDF and PNG formats"

**Changed** - Modifications to existing features:
- "Updated quote text overlay to use custom fonts"
- "Improved image upload performance (50% faster)"

**Deprecated** - Features marked for removal (but still work):
- "Legacy API endpoint `/api/v1/quotes` (use `/api/v2/quotes` instead)"

**Removed** - Features that no longer exist:
- "Removed deprecated `/api/v1/quotes` endpoint"

**Fixed** - Bug fixes:
- "Fixed login redirect loop when session expires"
- "Fixed quote generation failing with special characters"

**Security** - Security-related changes:
- "Patched XSS vulnerability in quote text input"
- "Updated dependencies to address CVE-2024-12345"

### Writing Style

**DO** - Focus on user impact:
- ✅ "Added Twitter OAuth for one-click login"
- ✅ "Fixed quote images not loading on mobile devices"
- ✅ "Improved quote generation performance (3x faster)"

**DON'T** - Internal implementation details:
- ❌ "Added TwitterAuthProvider.tsx component"
- ❌ "Fixed bug in line 45 of quotes.ts"
- ❌ "Refactored image loading logic"

**Exception**: Security and performance improvements deserve technical details:
- ✅ "Security: Patched SQL injection in quote search (CVE-2024-12345)"
- ✅ "Performance: Reduced quote generation from 2s to 650ms via lazy loading"

### Unreleased Section

**Always maintain an [Unreleased] section** at the top:

```markdown
## [Unreleased]

### Added
- Features merged to develop but not yet released

### Fixed
- Bug fixes on develop awaiting release
```

**When releasing:**
1. Rename `[Unreleased]` to `[1.1.0] - 2025-12-01`
2. Add new empty `[Unreleased]` section at top
3. Commit as part of release process

## Version Decision Workflow

### Quick Decision Tree

```
What changed?
│
├─ Bug fix only → PATCH (1.0.0 → 1.0.1)
│
├─ New feature (backward-compatible) → MINOR (1.0.0 → 1.1.0)
│
└─ Breaking change → MAJOR (1.0.0 → 2.0.0)
    Examples:
    - Removed API endpoint
    - Changed response format
    - Renamed database fields (affects API)
    - Changed authentication flow (breaks existing clients)
```

### Examples from Our Project

**Scenario 1: Add Twitter OAuth (new feature)**
- Current: `1.0.0`
- Change: New authentication method (doesn't break existing email/Google)
- Version: `1.1.0` (MINOR)

**Scenario 2: Fix login redirect bug**
- Current: `1.0.0`
- Change: Bug fix (no new features)
- Version: `1.0.1` (PATCH)

**Scenario 3: Change API response format**
- Current: `1.5.2`
- Change: `/api/quotes` now returns `{ data: [...] }` instead of `[...]`
- Breaking: Existing clients break
- Version: `2.0.0` (MAJOR)

**Scenario 4: Add quote export + fix bug**
- Current: `1.0.0`
- Changes: New feature (export) + bug fix (redirect)
- Version: `1.1.0` (MINOR wins - includes both)
- CHANGELOG documents both under `[1.1.0]`:
  ```markdown
  ### Added
  - Quote export to PDF and PNG

  ### Fixed
  - Login redirect loop
  ```

## Common Questions

### "Should every merge to develop get a version?"

**No.** Version when:
- ✅ Merging feature that adds user-facing value
- ✅ Releasing hotfix to production
- ❌ Internal refactoring with no user impact
- ❌ Documentation-only changes (unless CHANGELOG-worthy)

**Rule of thumb**: If it deserves a CHANGELOG entry, it deserves a version.

### "Can I skip versions?"

**No.** Versions must be sequential:
- ✅ 1.0.0 → 1.1.0 → 1.2.0
- ❌ 1.0.0 → 1.2.0 (skipped 1.1.0)

**Exception**: Pre-releases can have gaps:
- ✅ 1.0.0-beta.1 → 1.0.0-beta.3 (skipped beta.2 is fine)

### "What if I forget to update CHANGELOG before merging?"

**Fix it immediately:**
```bash
# On develop after merge
git add CHANGELOG.md
git commit -m "docs: add CHANGELOG entry for v0.5.0"
git push origin develop
```

### "Should I tag every commit?"

**No.** Only tag releases:
- ✅ Production releases (v1.0.0)
- 🟡 Pre-releases if tracking (v0.5.0-dev)
- ❌ Individual commits (use commit messages)

## Automation Opportunities

### Post-MVP (Consider Adding)

1. **Automated version bumping**:
   ```bash
   npm version minor  # Auto-updates package.json, creates commit + tag
   ```

2. **Conventional Commits** for auto-versioning:
   - `feat: add quote export` → MINOR bump
   - `fix: login redirect` → PATCH bump
   - `BREAKING CHANGE: change API format` → MAJOR bump
   - Tools: [semantic-release](https://github.com/semantic-release/semantic-release)

3. **Auto-generate CHANGELOG** from commits:
   - Tools: [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog)

4. **GitHub Actions** for release automation:
   ```yaml
   # .github/workflows/release.yml
   # Auto-tag and release when CHANGELOG updated on main
   ```

**Our current approach (manual) is fine for MVP.** Add automation when it saves time.

## Related Documentation

- [Git Workflow](./git-workflow.md) - Branch strategy and merge requirements
- [Development Loop](./development-loop.md) - Implementation workflow
- Project [CHANGELOG.md](../../../CHANGELOG.md) - Version history

