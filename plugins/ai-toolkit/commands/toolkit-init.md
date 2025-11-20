---
tags: ["workflow", "initialization", "setup", "scaffolding", "update", "sync"]
description: "Initialize project with ai-toolkit structure - minimal questions, fast setup"
argument-hint: "[--force | --dry-run]"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Glob", "AskUserQuestion"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md  # PM structure and directory organization
  - docs/development/workflows/development-loop.md  # Development workflow structure
---

# /toolkit-init

**WHAT**: Initialize project with complete ai-toolkit structure (37 files) or intelligently sync updates after plugin upgrades.

**WHY**: 30-second setup for new projects; preserve customizations while benefiting from template improvements in existing projects.

**HOW**: Smart mode detection (initial setup vs update/sync), minimal questions (2), comprehensive file copying with verification.

## Usage

```bash
/toolkit-init              # Smart: Initial setup (new) or update/sync (existing)
/toolkit-init --force      # Force: Overwrite all with templates (complete reset)
/toolkit-init --dry-run    # Preview: Show changes without modifying files
```

## Execution Steps

### 1. Mode Detection

```bash
# Check for existing ai-toolkit structure
if pm/ AND docs/ exist:
    if --force:
        → Force Reinit (skip to step 3)
    else:
        → Update/Sync Mode (jump to Update Flow)
else:
    → Initial Setup (continue to step 2)
```

**Template verification**:
```bash
TEMPLATE_DIR="${CLAUDE_PLUGIN_ROOT}/templates/starter"
if not exists: ERROR "Template directory not found"
```

### 2. Initial Setup Questions (New Projects Only)

**Ask 2 questions**:
1. "What's your app called?" → {app-name}
2. "What does it do? (1-2 sentences)" → {description}

**No other questions**. Tech stack, links, infrastructure = filled in later.

### 3. Copy Template (37 Files)

**Structure created** (pm/, docs/ with nested guidelines/adrs/design/, root files):

```bash
# Copy all template files
TEMPLATE_DIR="${CLAUDE_PLUGIN_ROOT}/templates/starter"

# Prefer rsync (handles hidden files, nested dirs)
rsync -av "$TEMPLATE_DIR"/ . --exclude='.git'

# Fallback: cp with explicit hidden file handling
cp -r "$TEMPLATE_DIR"/* .
cp "$TEMPLATE_DIR"/.gitignore .
```

**CRITICAL**: Must copy ALL 37 files including:
- .gitkeep files (preserve empty dirs)
- .gitignore (hidden file)
- All nested subdirectories (guidelines/, adrs/, design/)

**Verification** (mandatory):
```bash
# Count files (should be 37)
find . -type f | grep -v ".git" | wc -l

# Check critical dirs exist
[ -d "docs/development/guidelines" ]
[ -d "docs/project/adrs" ]
[ -d "docs/project/design" ]
```

### 4. Customize Key Files

**Placeholder replacements** (get plugin version, current date):
```bash
PLUGIN_VERSION=$(grep "version" "${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json")
CURRENT_DATE=$(date '+%Y-%m-%d')
```

**Files customized** (copied from templates and personalized):
- **docs/project-brief.md**: Copy from docs/development/templates/project-brief-template.md, insert {app-name}, {description} in Overview section
- **CLAUDE.md**: Insert {app-name}, {description}, {toolkit-version}, {last-updated}
- **README.md**: Insert {app-name}, {description}

**Brief structure** (6 sections):
```markdown
# {app-name} - Project Brief
## Overview
{description}
## Problem
[Empty - fill via /project-brief]
## Solution / Key Features / Target Audience / Success Metrics
[Empty sections]
```

### 5. Success Output

```
✓ Created 37 files
✓ pm/ (5 files: templates for epic/task/bug)
✓ docs/ (20+ files: guidelines, adrs, design dirs)
✓ Customized: project-brief.md, CLAUDE.md, README.md

Next steps:
1. /project-brief (complete brief)
2. /jira-epic (create features)
```

## Update/Sync Flow (Existing Projects)

### 1. Version Detection & CHANGELOG

```bash
# Extract versions
PROJECT_VERSION=$(grep "Plugin Version:" CLAUDE.md)
PLUGIN_VERSION=$(grep "version" "${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json")

# If versions differ, parse CHANGELOG.md sections between versions
# Display Added/Changed/Removed/Breaking with affected files
```

**Display changes summary**:
```
Your project: v0.11.0
Current plugin: v0.11.2

Changes in v0.11.1:
Added: PM Guidelines, Agent Coordination
Affected: pm-guidelines.md (NEW), agent-coordination.md (NEW)

Changes in v0.11.2:
Changed: WORKLOG Stream Pattern
Affected: development-loop.md (UPDATED)
```

### 2. Scan & Display Drift

```bash
# Compare each template file with project file
find "${CLAUDE_PLUGIN_ROOT}/templates/starter" -type f

# File status: ✅ Identical | 🔧 Customized | ❌ Missing | ➕ New in Plugin | 📄 Extra
```

**Drift report**:
```
✅ Identical (3): .gitignore, docs/development/templates/spec-template.md, task-template.md
🔧 Customized (4): CLAUDE.md, README.md, docs/project-brief.md, docs/development/templates/bug-template.md
❌ Missing (1): docs/development/workflows/git-workflow.md
➕ New in Plugin (2): docs/project/adrs/README.md, pm/README.md
```

### 3. Interactive Decisions

**For each non-identical file**:
```
CLAUDE.md is customized.

Options:
  1. Keep your version
  2. Update to template
  3. Smart merge (recommended)
  4. Show diff
  5. Skip

Choose (1-5): _
```

**Smart merge** (option 3):
- Parse sections (by markdown headers)
- Keep user-added content
- Add new template sections
- Preserve customizations

### 4. Apply Updates & Summary

```bash
# Execute choices (keep/update/merge/skip)
# Track: UPDATED=() KEPT=() MERGED=() SKIPPED=()
```

**Summary output**:
```
✅ Updated (2): pm/README.md, docs/project/adrs/README.md
🔀 Merged (1): CLAUDE.md
⏭️  Kept (3): README.md, docs/project-brief.md, docs/development/templates/bug-template.md
```

### 5. Update Version Tracking

```bash
# Auto-update CLAUDE.md with new plugin version and date
sed -i "s/Plugin Version\*\*: .*/Plugin Version**: $PLUGIN_VERSION/" CLAUDE.md
sed -i "s/Last Updated\*\*: .*/Last Updated**: $(date '+%Y-%m-%d')/" CLAUDE.md
```

## Error Handling

- **Template directory not found**: "Cannot find template at {path} - reinstall plugin"
- **Copy fails**: "Failed to copy templates - check permissions"
- **Diff unavailable**: Treat as customized, prompt for manual review
- **Merge conflict**: Show both versions, ask user to choose

## Integration

**Workflow position**:
```
/toolkit-init → /project-brief → /jira-epic → /plan → /implement
```

**Update cycle**:
```
/plugin update ai-toolkit → /toolkit-init (sync templates) → Review merged files
```

**When to run update/sync**:
- After plugin updates (new features)
- Before major releases (template alignment)
- Periodic drift detection (optional)

## Notes

- **37 files copied**: pm/ (5), docs/ (20+), root files (12)
- **2 questions only**: App name + description (everything else later)
- **Smart merge**: Parse markdown sections, preserve customizations
- **Dry run mode**: Preview changes with --dry-run flag
- **Version tracking**: Auto-updates CLAUDE.md with plugin version/date
