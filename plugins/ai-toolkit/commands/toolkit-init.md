---
tags: ["workflow", "initialization", "setup", "scaffolding", "update", "sync"]
description: "Initialize project with ai-toolkit structure - minimal questions, fast setup"
argument-hint: "[--force | --dry-run]"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Glob", "AskUserQuestion"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/workflows/pm-workflows.md  # PM structure and directory organization
  - docs/development/workflows/task-workflow.md  # Development workflow structure
---

# /toolkit-init

**WHAT**: Initialize project with complete ai-toolkit structure (53 files) or intelligently sync updates after plugin upgrades.

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

### 3. Copy Template Files (50 Files)

**Copy all template files**:
```bash
TEMPLATE_DIR="${CLAUDE_PLUGIN_ROOT}/templates/starter"

# Use rsync if available (handles hidden files, nested dirs)
rsync -av "$TEMPLATE_DIR"/ . --exclude='.git'

# OR use cp with explicit hidden file handling
cp -r "$TEMPLATE_DIR"/* .
cp "$TEMPLATE_DIR"/.gitignore .
```

**CRITICAL**: Must copy ALL 50 template files including:
- `.gitkeep` files (preserve empty dirs in pm/specs/, pm/issues/, pm/notes/)
- `.gitignore` (hidden file)
- All nested subdirectories

### 4. Generate Project Docs from Templates

**Create 3 project-specific files from templates**:

| Generated File | Source Template | Customization |
|----------------|-----------------|---------------|
| `docs/project/architecture-overview.md` | `docs/development/templates/architecture-overview-template.md` | Copy as-is |
| `docs/project/design-overview.md` | `docs/development/templates/design-overview-template.md` | Copy as-is |
| `docs/project/writing-style.md` | `docs/development/templates/writing-style-template.md` | Copy as-is |

These provide starter structure for project-specific documentation that users fill in as the project develops.

**Note**: `docs/project-brief.md` is created by `/project-brief` command on first run, not by `/toolkit-init`.

### 5. Customize Existing Files

**Get plugin metadata**:
```bash
PLUGIN_VERSION=$(grep '"version"' "${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json" | sed 's/.*: *"\([^"]*\)".*/\1/')
CURRENT_DATE=$(date '+%Y-%m-%d')
```

**Update files with placeholders**:
- **CLAUDE.md**: Replace {app-name}, {description}, {toolkit-version}, {last-updated}
- **README.md**: Replace {app-name}, {description}

### 6. Verification

```bash
# Count files (should be 53: 50 template + 3 generated)
find . -type f | grep -v ".git" | wc -l

# Verify critical directories exist
[ -d "docs/development/conventions" ]
[ -d "docs/development/workflows" ]
[ -d "docs/development/templates" ]
[ -d "docs/project/adrs" ]
[ -d "docs/project/design-assets" ]
[ -d "pm/specs" ]
[ -d "pm/issues" ]
```

### 7. Success Output

```
✓ Created 53 files

Structure:
├── pm/ (4 files)
├── docs/development/ (33 files: conventions, workflows, templates, misc)
├── docs/project/ (6 files: READMEs + generated docs)
└── root (10 files: CLAUDE.md, README.md, etc.)

Generated from templates:
├── docs/project/architecture-overview.md
├── docs/project/design-overview.md
└── docs/project/writing-style.md

Next steps:
1. /project-brief (complete your project vision)
2. /spec (create your first feature spec)
```

## Update/Sync Flow (Existing Projects)

### 1. Version Detection

```bash
# Extract versions
PROJECT_VERSION=$(grep "Plugin Version" CLAUDE.md | sed 's/.*: *//')
PLUGIN_VERSION=$(grep '"version"' "${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json" | sed 's/.*: *"\([^"]*\)".*/\1/')
```

**Display version comparison**:
```
Your project: v0.37.0
Current plugin: v0.38.0

Checking for template changes...
```

### 2. Scan & Categorize Files

**Compare each template file with project file**:

| Status | Meaning |
|--------|---------|
| ✅ Identical | No changes needed |
| 🔧 Customized | User modified (offer merge options) |
| ❌ Missing | Template file not in project (will add) |
| ➕ New in Plugin | New template file (will add) |

**Example drift report**:
```
✅ Identical (40): .gitignore, most workflow files...
🔧 Customized (5): CLAUDE.md, README.md, docs/project-brief.md, testing-standards.md, git-workflow.md
❌ Missing (1): docs/development/workflows/spike-workflow.md
➕ New in Plugin (2): docs/development/misc/new-feature.md
```

### 3. Interactive Decisions

**For each customized file**:
```
CLAUDE.md is customized.

Options:
  1. Keep your version (no changes)
  2. Replace with template (lose customizations)
  3. Smart merge (keep your content, add new sections)
  4. Show diff
  5. Skip for now

Choose (1-5): _
```

**Smart merge strategy**:
- Parse markdown by headers (## sections)
- Keep user-added content in existing sections
- Add new template sections that don't exist
- Preserve YAML frontmatter customizations

### 4. Handle Generated Project Docs

**For docs/project/ files (architecture-overview.md, design-overview.md, writing-style.md)**:
- If file exists and is customized → Keep (these are user content)
- If file is missing → Offer to generate from template
- If template changed significantly → Notify user (don't auto-update)

### 5. Apply Updates

```bash
# Execute user choices
# Track results: UPDATED=(), KEPT=(), MERGED=(), ADDED=()
```

**Summary output**:
```
✅ Updated (3): spike-workflow.md, new-feature.md, agent-coordination.md
🔀 Merged (1): CLAUDE.md
⏭️  Kept (4): README.md, testing-standards.md, git-workflow.md, project-brief.md
➕ Added (1): docs/development/misc/new-feature.md
```

### 6. Update Version Tracking

```bash
# Update CLAUDE.md with new plugin version
sed -i "s/Plugin Version\*\*: .*/Plugin Version**: $PLUGIN_VERSION/" CLAUDE.md
sed -i "s/Last Updated\*\*: .*/Last Updated**: $(date '+%Y-%m-%d')/" CLAUDE.md
```

## Error Handling

| Error | Message | Resolution |
|-------|---------|------------|
| Template not found | "Cannot find template at {path}" | Reinstall plugin |
| Copy fails | "Failed to copy templates" | Check permissions |
| Diff unavailable | - | Treat as customized, prompt for review |
| Merge conflict | - | Show both versions, ask user to choose |

## Integration

**Workflow position**:
```
/toolkit-init → /project-brief → /spec → /plan → /implement
```

**Update cycle**:
```
/plugin update ai-toolkit → /toolkit-init (sync) → Review changes
```

**When to run update/sync**:
- After plugin updates
- Before major releases (ensure template alignment)
- Periodically to detect drift

## Notes

- **53 files total**: 50 template files + 3 generated from templates
- **2 questions only**: App name + description
- **Smart merge**: Preserves user customizations while adding new content
- **Dry run**: Use `--dry-run` to preview all changes safely
- **Force mode**: Use `--force` only for complete reset (loses customizations)
- **Version tracking**: CLAUDE.md automatically tracks plugin version
