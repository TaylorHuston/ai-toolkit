---
tags: ["workflow", "initialization", "setup", "scaffolding", "update", "sync"]
description: "Initialize project with ai-toolkit structure - minimal questions, fast setup"
argument-hint: "[--force | --dry-run]"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Glob", "AskUserQuestion"]
model: claude-sonnet-4-5
---

# /toolkit-init Command

**Purpose**: Initialize a new or existing project with ai-toolkit structure through minimal, natural language questions.

## Usage

```bash
/toolkit-init              # Interactive setup (new projects) or update/sync (existing)
/toolkit-init --force      # Force reinit (skip detection, overwrite all)
/toolkit-init --dry-run    # Show what would change without executing
```

## Philosophy

**Fast, minimal, customized**:
- 30 seconds from start to working structure
- 2 natural language questions (new projects)
- Smart defaults for everything else
- Living documents that grow with project

**Update-friendly**:
- Detect template drift after plugin updates
- Intelligent merge/update suggestions
- Preserve customizations
- Selective file updates

## Modes

### 1. **Initial Setup Mode** (new projects)
- Copy template structure
- Minimal customization (app name + description)
- Initialize brief with 6 sections
- No blocking, placeholders for missing info

### 2. **Update/Sync Mode** (existing projects)
- Compare project files vs latest templates
- Detect customizations and drift
- Interactive update decisions
- Preserve user modifications

### 3. **Force Reinit Mode** (`--force`)
- Skip detection
- Overwrite all files with templates
- Use for complete reset

### 4. **Dry Run Mode** (`--dry-run`)
- Show what would change
- No file modifications
- Preview before committing

## Command Flow

### CRITICAL: Complete File Copy Required

**The starter template contains 37 files across multiple nested directories. ALL files must be copied, including:**
- Hidden files (.gitignore)
- Empty directory markers (.gitkeep files)
- All nested subdirectories (guidelines/, adrs/, design/)
- All markdown and configuration files

**Common Issues:**
- `cp -r` may not copy hidden files - use explicit `.gitignore` copy
- May need to create nested directories before copying files
- .gitkeep files are essential for preserving empty directory structure

**Verification is MANDATORY** - Count files after copy to ensure 37 files were created.

### 1. Pre-Flight Check

**Detect Existing Structure**:
```bash
# Check if already initialized
if pm/ exists AND docs/ exists:
    if --force flag:
        → MODE: Force Reinit (skip to Step 3)
    else:
        → MODE: Update/Sync (proceed to Update/Sync Flow)
else:
    → MODE: Initial Setup (proceed to Step 2)
```

**Check Plugin Template Location**:
```bash
# Verify template directory exists
TEMPLATE_DIR="${CLAUDE_PLUGIN_ROOT}/templates/starter"
if ! -d "$TEMPLATE_DIR":
    ERROR: "Cannot find template directory at $TEMPLATE_DIR"
    EXIT
```

### 2. Ask Two Questions

**Question 1: App Name**
```
What's your app called?
> "SolarQuote"
```

**Question 2: Description**
```
What does it do? (1-2 sentences is fine)
> "Helps solar installers create quotes faster by automating
   calculations and pulling real-time pricing."
```

**That's it.** No tech stack questions, no external links, no infrastructure setup.

### 3. Copy Starter Template

**Directory Structure Created**:
```
project-root/
├── pm/
│   ├── README.md
│   ├── epics/.gitkeep
│   ├── issues/.gitkeep
│   └── templates/
│       ├── README.md
│       ├── epic.md
│       ├── task.md
│       └── bug.md
├── docs/
│   ├── README.md
│   ├── project-brief.md  (customized with answers)
│   ├── project/
│   │   ├── README.md
│   │   ├── architecture-overview.md
│   │   ├── adrs/
│   │   │   ├── README.md
│   │   │   └── adr-template.md
│   │   └── design/
│   │       ├── README.md
│   │       ├── mockups/.gitkeep
│   │       ├── screenshots/.gitkeep
│   │       ├── color-schemes/.gitkeep
│   │       └── assets/.gitkeep
│   └── development/
│       ├── README.md
│       └── guidelines/
│           ├── api-guidelines.md
│           ├── architectural-principles.md
│           ├── coding-standards.md
│           ├── git-workflow.md
│           ├── security-guidelines.md
│           └── testing-standards.md
├── .gitignore
├── CHANGELOG.md  (initialized with project setup)
├── CLAUDE.md  (customized with app name)
├── README.md  (customized with app name and description)
└── GETTING-STARTED.md
```

**Copy Operation**:
```bash
# CRITICAL: Must copy ALL files and directories including hidden files
TEMPLATE_DIR="${CLAUDE_PLUGIN_ROOT}/templates/starter"

# Method 1: Recursive copy (preferred if permissions allow)
cp -r "$TEMPLATE_DIR"/* .
cp -r "$TEMPLATE_DIR"/.gitignore .  # Hidden files need explicit copy

# Method 2: Use rsync for reliable copying
rsync -av "$TEMPLATE_DIR"/ . --exclude='.git'

# Method 3: Individual file writes (if bash copy fails)
# Read each file from template and Write to project
# IMPORTANT: Must handle all subdirectories and .gitkeep files
```

**CRITICAL Requirements**:
1. **Copy ALL files** - Including .gitkeep files in empty directories
2. **Preserve directory structure** - All nested directories must be created
3. **Include hidden files** - Especially .gitignore
4. **Verify completion** - Check that all 33 template files were copied

### 4. Customize Files

**Files to Customize with Answers**:

**docs/project-brief.md**:
```markdown
# {app-name} - Project Brief

## Overview
{description}

## Problem


## Solution


## Target Audience


## Key Features


## Success Metrics


---
*Living document - run `/project-brief` to fill in details*
```

**CLAUDE.md** (Multiple sections):
```markdown
## Project Information

- **Project Name**: {app-name}
- **Description**: {description}
- **Tech Stack**: [Update as you decide on technologies]

## External Links
- **Project Management**: [Add URL when available]
- **Wiki**: [Add URL when available]

## AI Toolkit Configuration

- **Plugin Version**: {toolkit-version}  # Current plugin version
- **Last Updated**: {last-updated}       # Init date
- **Template Customizations**: None
```

**README.md**:
```markdown
# {app-name}

{description}

## Getting Started

See [GETTING-STARTED.md](./GETTING-STARTED.md) for AI Toolkit usage.

[Rest of template...]
```

### 5. Verify Copy Completion

**After copying, verify all 37 files were created:**

```bash
# Count files (should be 37)
find . -type f -o -name ".gitkeep" | grep -v ".git" | wc -l

# Verify critical directories exist
[ -d "docs/development/guidelines" ] && echo "✓ Guidelines" || echo "✗ Missing guidelines"
[ -d "docs/project/adrs" ] && echo "✓ ADRs" || echo "✗ Missing ADRs"
[ -d "docs/project/design" ] && echo "✓ Design" || echo "✗ Missing design"
[ -f "docs/project/architecture-overview.md" ] && echo "✓ Architecture" || echo "✗ Missing architecture"
```

**If files are missing:**
```
⚠️  Template copy incomplete. Retrying with alternative method...
```

### 6. Success Report

```
✓ Created 33 template files
✓ Created pm/ directory with templates (5 files)
✓ Created docs/ structure (20 files)
  ├── 6 development guidelines
  ├── 2 ADR files
  ├── 5 design directories
  └── Project brief, architecture overview
✓ Initialized project-brief.md with your description
✓ Initialized CHANGELOG.md with project setup
✓ Customized CLAUDE.md (includes CHANGELOG maintenance instructions)
✓ Customized README.md
✓ Created .gitignore

Your project is ready!

Next steps:
1. Run /project-brief to complete your project brief
2. Run /epic to start creating features
3. Check GETTING-STARTED.md for full workflow guide

Happy building! 🚀
```

## Update/Sync Flow (Existing Projects)

When run in an already-initialized project, `/toolkit-init` enters **Update/Sync Mode**.

### 1. Version Detection and CHANGELOG Analysis

**Read Project's Toolkit Version**:
```bash
# Extract toolkit version from project's CLAUDE.md
PROJECT_VERSION=$(grep "Plugin Version:" CLAUDE.md | sed 's/.*Plugin Version\*\*: //' | sed 's/ .*//')

# Example: "0.11.0"
```

**Get Current Plugin Version**:
```bash
# Read version from plugin.json
PLUGIN_VERSION=$(cat "${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json" | grep "version" | sed 's/.*"version": "//' | sed 's/".*//')

# Example: "0.11.2"
```

**Parse Relevant CHANGELOG Sections**:
```bash
# If versions differ, extract CHANGELOG sections between them
if [ "$PROJECT_VERSION" != "$PLUGIN_VERSION" ]; then
    # Parse CHANGELOG.md to extract versions between PROJECT_VERSION and PLUGIN_VERSION
    # Example: Extract [0.11.1] and [0.11.2] sections if project is on 0.11.0

    # Extract version sections using markdown parsing:
    # - Find "## [0.11.1]" through "## [0.11.0]" (exclusive)
    # - Categorize changes: Added, Changed, Removed, Fixed, Breaking
    # - Identify affected template files mentioned in changelog
fi
```

**Display Version and Changes Summary**:
```
Your project uses ai-toolkit v0.11.0
Current plugin version: v0.11.2

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Changes in v0.11.1:

Added:
  • Automatic Security Reviews - security-auditor sign-off
  • PM Guidelines - New pm-guidelines.md guideline file
  • Agent Coordination Guidelines - agent-coordination.md

Changed:
  • Command Simplification - /epic reduced 42% (see below)
  • Guideline references added to /epic, /project-brief, /docs

Affected template files:
  • docs/development/guidelines/pm-guidelines.md (NEW)
  • docs/development/guidelines/agent-coordination.md (NEW)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Changes in v0.11.2:

Changed:
  • WORKLOG Stream Pattern - Changed to handoff-based entries
  • development-loop.md guideline updated with stream pattern

Affected template files:
  • docs/development/guidelines/development-loop.md (UPDATED)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⚠️  Breaking Changes: None
```

**Benefits of CHANGELOG Integration**:
- **Context-aware updates**: Users see exactly what changed and why
- **Informed decisions**: Understand impact before choosing keep/update/merge
- **Breaking change warnings**: Highlighted before updates proceed
- **File correlation**: Know which template files were affected by changes
- **Version history**: Full context from project version to current

### 2. Scan and Compare

**Get Template File List**:
```bash
# List all files in template directory
find "${CLAUDE_PLUGIN_ROOT}/templates/starter" -type f
```

**For Each Template File, Determine Status**:

| Status | Condition | Visual |
|--------|-----------|--------|
| **Identical** | File exists, content matches template | ✅ |
| **Customized** | File exists, content differs from template | 🔧 |
| **Missing** | Template file not in project | ❌ |
| **New in Plugin** | File added to template since last init | ➕ |
| **Extra** | File in project but not in template | 📄 |

### 2. Display Drift Report

```
Checking project files against latest ai-toolkit templates...

✅ Identical (3 files):
   - .gitignore
   - pm/templates/epic.md
   - pm/templates/task.md

🔧 Customized (4 files):
   - CLAUDE.md (added tech stack, external links)
   - README.md (added custom sections)
   - docs/project-brief.md (completed sections)
   - pm/templates/bug.md (added custom fields)

❌ Missing (1 file):
   - docs/development/guidelines/git-workflow.md

➕ New in Plugin (2 files):
   - docs/project/adrs/README.md
   - pm/README.md (updated with WORKLOG.md info)

📄 Extra in Project (1 file):
   - docs/old-architecture.md (not in template)

Summary: 3 identical, 4 customized, 1 missing, 2 new in plugin, 1 extra
```

### 3. Interactive Update Decisions

**For Each Non-Identical File, Ask**:

```bash
# Example: Customized file
CLAUDE.md is customized.

Template changes since your version:
  + Added: Git workflow section reference
  + Updated: Development command examples
  ~ Changed: Tech stack placeholder format

Your customizations:
  + Added: Tech stack (React, Node.js, PostgreSQL)
  + Added: External links (Jira, Confluence)
  + Modified: Project description

Options:
  1. Keep your version (ignore template updates)
  2. Update to latest template (lose your customizations)
  3. Smart merge (add template sections, keep your content)
  4. Show diff (side-by-side comparison)
  5. Skip for now

Choose (1-5): _
```

**Smart Merge Logic** (Option 3):
- Identify sections in template vs project
- Keep user-added content
- Add new template sections
- Update template placeholders with user values
- Preserve user modifications to existing sections

**Diff Display** (Option 4):
```diff
# CLAUDE.md

## Project Information
- **Project Name**: SolarQuote
+ **Version**: [Add version when available]
  **Description**: Helps solar installers...

## Tech Stack
- **Frontend**: React, TypeScript
- **Backend**: Node.js, Express
- **Database**: PostgreSQL
+ **Infrastructure**: Docker, AWS ECS

+ ## Git Workflow
+
+ See `docs/development/guidelines/git-workflow.md` for complete workflow.
```

### 4. Execute Updates

**Apply User Choices**:
```bash
# For each file based on user decision
case $choice in
    1) # Keep - no action
       ;;
    2) # Update - overwrite with template
       cp "$TEMPLATE_FILE" "$PROJECT_FILE"
       ;;
    3) # Smart merge
       perform_smart_merge "$TEMPLATE_FILE" "$PROJECT_FILE"
       ;;
    5) # Skip - no action
       ;;
esac
```

**Track Changes**:
```bash
# Keep log of what was updated
UPDATED=()
KEPT=()
MERGED=()
SKIPPED=()
```

### 5. Update Summary Report

```
Update Complete!

✅ Updated (2 files):
   - pm/README.md (overwrote with latest)
   - docs/project/adrs/README.md (new file added)

🔀 Merged (1 file):
   - CLAUDE.md (smart merge: kept customizations, added new sections)

⏭️  Kept (3 files):
   - README.md (your version preserved)
   - docs/project-brief.md (your version preserved)
   - pm/templates/bug.md (your version preserved)

⏸️  Skipped (1 file):
   - docs/development/guidelines/git-workflow.md (review later)

Next steps:
- Review merged files for any conflicts
- Check new files added from template
- Run /project-brief if brief structure changed
```

### 6. Update Toolkit Version in CLAUDE.md

**After successful update, automatically update project's toolkit version**:

```bash
# Get current date
CURRENT_DATE=$(date '+%Y-%m-%d')

# Update CLAUDE.md with new plugin version and timestamp
# Replace the toolkit version section
sed -i "s/Plugin Version\*\*: .*/Plugin Version**: $PLUGIN_VERSION/" CLAUDE.md
sed -i "s/Last Updated\*\*: .*/Last Updated**: $CURRENT_DATE/" CLAUDE.md
```

**Result in CLAUDE.md**:
```markdown
## AI Toolkit Configuration

**Toolkit Version Information:**

- **Plugin Version**: 0.11.2
- **Last Updated**: 2025-11-03
- **Template Customizations**: None (update this list when you customize guideline files)
```

**Why auto-update**:
- Tracks when project was last synced with plugin
- Enables accurate version comparison on next update
- Prevents re-showing same changelog entries
- Documents project's toolkit evolution

### 7. Dry Run Mode

With `--dry-run` flag:
```bash
/toolkit-init --dry-run

# Shows drift report and proposed actions
# Does NOT modify any files
# Ends with: "Dry run complete. Run without --dry-run to apply changes."
```

## Implementation Details

### Template Replacement

**Placeholders in starter template files**:
- `{app-name}` - App name from Question 1
- `{description}` - Description from Question 2
- `{toolkit-version}` - Current plugin version from plugin.json
- `{last-updated}` - Current date (YYYY-MM-DD format)

**Replacement logic**:
```bash
# Get plugin version and current date
PLUGIN_VERSION=$(cat "${CLAUDE_PLUGIN_ROOT}/.claude-plugin/plugin.json" | grep "version" | sed 's/.*"version": "//' | sed 's/".*//')
CURRENT_DATE=$(date '+%Y-%m-%d')

# For each copied file containing placeholders
sed -i "s/{app-name}/$app_name/g" filename
sed -i "s/{description}/$description/g" filename
sed -i "s/{toolkit-version}/$PLUGIN_VERSION/g" filename
sed -i "s/{last-updated}/$CURRENT_DATE/g" filename
```

**Files with placeholders**:
- `CLAUDE.md` - All four placeholders
- `README.md` - app-name, description
- `docs/project-brief.md` - app-name, description

### File Comparison and Diff Detection

**Content Comparison**:
```bash
# Compare template file with project file
TEMPLATE_FILE="${CLAUDE_PLUGIN_ROOT}/templates/starter/$RELATIVE_PATH"
PROJECT_FILE="$RELATIVE_PATH"

if [ ! -f "$PROJECT_FILE" ]; then
    STATUS="missing"
elif diff -q "$TEMPLATE_FILE" "$PROJECT_FILE" > /dev/null; then
    STATUS="identical"
else
    STATUS="customized"
fi
```

**Smart Diff Analysis**:
```bash
# Generate unified diff
diff -u "$TEMPLATE_FILE" "$PROJECT_FILE" > /tmp/diff.txt

# Analyze changes
ADDITIONS=$(grep "^+" /tmp/diff.txt | grep -v "^+++" | wc -l)
DELETIONS=$(grep "^-" /tmp/diff.txt | grep -v "^---" | wc -l)
MODIFICATIONS=$(($ADDITIONS + $DELETIONS))
```

### Smart Merge Implementation

**Markdown File Merge Strategy**:

For markdown files (CLAUDE.md, README.md, etc.):

1. **Parse sections**: Split both files by headers (`## Section Name`)
2. **Identify sections**:
   - **Template-only**: New sections in template
   - **Project-only**: Custom sections added by user
   - **Both**: Sections in both (may have different content)
3. **Merge logic**:
   - Keep all project-only sections (user customizations)
   - Add all template-only sections (new features)
   - For sections in both:
     - If content identical → keep as-is
     - If project has more content → keep project version
     - If template has structural changes → show diff, ask user

**YAML/Config File Merge**:

For YAML files (pm/templates/*.md frontmatter, etc.):

1. **Parse YAML**: Extract frontmatter
2. **Merge fields**:
   - Keep user-added fields
   - Add new template fields
   - For conflicting fields → keep project value, note template change
3. **Combine**: Merge frontmatter + keep project content

**Example Smart Merge** (CLAUDE.md):
```markdown
# Template has:
## Project Information
- **Project Name**: {app-name}
- **Version**: [Add version]

## Git Workflow
See docs/development/guidelines/git-workflow.md

# Project has:
## Project Information
- **Project Name**: SolarQuote
- **Tech Stack**: React, Node.js

# Smart Merge Result:
## Project Information
- **Project Name**: SolarQuote
- **Version**: [Add version]          # Added from template
- **Tech Stack**: React, Node.js     # Kept from project

## Git Workflow                        # Added from template
See docs/development/guidelines/git-workflow.md
```

### Error Handling

**If template directory not found**:
```
❌ Cannot find template directory.
   Plugin may not be installed correctly.
   Try: /plugin reinstall ai-toolkit
```

**If copy fails**:
```
❌ Failed to copy template structure.
   Check file permissions and try again.
```

**If diff fails**:
```
⚠️  Cannot compare files (diff tool unavailable).
   Treating as customized - manual review needed.
```

**If smart merge encounters conflict**:
```
⚠️  Cannot auto-merge CLAUDE.md - manual merge needed.

   Conflict: Both template and project modified same section.

   Options:
   1. Keep your version
   2. Update to template version
   3. Show both versions side-by-side

   Choose (1-3): _
```

## Examples

### Example 1: New Project

```bash
/toolkit-init

Initializing ai-toolkit structure...

What's your app called?
> SolarQuote

What does it do? (1-2 sentences is fine)
> Helps solar installers create quotes faster by automating
  calculations and pulling real-time pricing data.

Copying template structure...
✓ pm/ directory with templates
✓ docs/ structure
✓ .gitignore
✓ Core files

Customizing for your project...
✓ docs/project-brief.md (Overview filled, 5 sections ready)
✓ CLAUDE.md (project context added)
✓ README.md (description added)

Done! Your project is ready.

Next: Run /project-brief to complete your brief
```

### Example 2: Update/Sync Mode (Plugin Updated)

```bash
/toolkit-init

Detected existing ai-toolkit structure.
Checking for updates...

Your project uses ai-toolkit v0.11.0
Current plugin version: v0.11.2

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Changes in v0.11.1:

Added:
  • PM Guidelines - pm-guidelines.md for epic/issue management
  • Agent Coordination Guidelines - agent-coordination.md

Changed:
  • Command Simplification - /epic reduced 42%

Affected template files:
  • docs/development/guidelines/pm-guidelines.md (NEW)
  • docs/development/guidelines/agent-coordination.md (NEW)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Changes in v0.11.2:

Changed:
  • WORKLOG Stream Pattern - handoff-based entries

Affected template files:
  • docs/development/guidelines/development-loop.md (UPDATED)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Comparing project files against latest templates...

✅ Identical (8 files)
   - .gitignore, pm/templates/epic.md, pm/templates/task.md, ...

🔧 Customized (3 files)
   - CLAUDE.md (added tech stack)
   - README.md (added sections)
   - docs/project-brief.md (completed)

➕ New in Plugin (2 files)
   - pm/README.md (updated with WORKLOG.md documentation)
   - docs/development/guidelines/git-workflow.md (new guideline)

Reviewing customized files...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
File: CLAUDE.md

Template changes:
  + Added: Git workflow reference
  + Updated: Command examples

Your customizations:
  + Added: Tech stack (React, Node.js, PostgreSQL)
  + Added: External links

Options:
  1. Keep your version
  2. Update to template
  3. Smart merge (recommended)
  4. Show diff
  5. Skip

Choose (1-5): 3

✓ Smart merged CLAUDE.md
  - Kept: Tech stack, external links
  - Added: Git workflow reference, updated commands

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
File: README.md

Your README is heavily customized.
Template has minor updates (formatting).

Options:
  1. Keep your version (recommended)
  2. Update to template
  3. Show diff
  4. Skip

Choose (1-4): 1

✓ Kept your version of README.md

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Adding new files from template...
✓ Created pm/README.md
✓ Created docs/development/guidelines/git-workflow.md

Update Complete!

🔀 Merged (1 file):
   - CLAUDE.md

⏭️  Kept (2 files):
   - README.md
   - docs/project-brief.md

➕ Added (2 files):
   - pm/README.md
   - docs/development/guidelines/git-workflow.md

Next steps:
- Review CLAUDE.md merge
- Check new files in docs/development/guidelines/
```

### Example 3: Dry Run

```bash
/toolkit-init --dry-run

Detected existing ai-toolkit structure.
Running in DRY RUN mode - no files will be modified.

Comparing against ai-toolkit v0.10.0 templates...

Would update 2 files:
  - pm/README.md (new in plugin)
  - docs/development/guidelines/git-workflow.md (new in plugin)

Would prompt for 3 customized files:
  - CLAUDE.md (smart merge recommended)
  - README.md (keep your version recommended)
  - docs/project-brief.md (keep your version recommended)

Dry run complete.
Run /toolkit-init (without --dry-run) to apply changes.
```

## Integration with Workflow

### Initial Setup Flow

**Position**: Entry point for new projects

```
/toolkit-init (setup structure)
    ↓
/project-brief (complete brief through conversation)
    ↓
/epic (create features)
    ↓
/plan TASK-### (plan implementation)
    ↓
/implement TASK-### 1.1 (build it)
```

### Update/Maintenance Flow

**Position**: Periodic sync after plugin updates

```
/plugin update ai-toolkit
    ↓
/toolkit-init (detect drift, sync templates)
    ↓
Review merged files
    ↓
Continue development with updated templates
```

### When to Run Update/Sync

- **After plugin updates**: Check for new template features
- **Periodic review**: Monthly drift detection (optional)
- **Before major releases**: Ensure templates are up-to-date
- **When templates improve**: Benefit from template enhancements
- **Troubleshooting**: Reset specific files to defaults

## Design Decisions

### Why Only 2 Questions?

**App name**: Needed for file customization
**Description**: Initializes project brief meaningfully

**Everything else**:
- Tech stack → Determined later, updated in CLAUDE.md as decided
- External links → Added when available
- Infrastructure → Not needed to start

### Why Initialize Brief with Empty Sections?

**Always 6 sections**:
- Provides structure
- User knows what to fill
- /project-brief can analyze gaps

**Overview pre-filled**:
- Uses description from init
- Gives immediate value
- Starting point for refinement

**Others empty**:
- No forced upfront planning
- Fill through /project-brief conversation
- Grows as understanding grows

### Why Separate Product (brief) from Project (CLAUDE.md)?

**Product vision** (docs/project-brief.md):
- Problem, Solution, Audience, Features, Success
- What you're building and why
- No implementation details

**Project context** (CLAUDE.md):
- Tech stack, external links, team info
- How you're building it
- Implementation context for AI

**Benefit**: Clear separation, can share brief with non-technical stakeholders

### Why Update/Sync Mode?

**Problem**: After plugin updates, projects miss new template improvements

**Solution**: Intelligent drift detection and selective updates

**Benefits**:
- **Preserve customizations**: Smart merge keeps user modifications
- **Get improvements**: Benefit from template enhancements
- **Visibility**: See exactly what changed in templates
- **Control**: Choose what to update, what to keep
- **Safety**: Dry run mode previews changes

**Design choices**:
- **Non-destructive**: Never overwrites without user consent
- **Interactive**: User decides on each file
- **Intelligent**: Smart merge for common cases
- **Transparent**: Show diffs and changes clearly

### Why Smart Merge?

**Problem**: Manual merges are tedious and error-prone

**Solution**: Automated section-based merging

**How it works**:
- Parse markdown by headers
- Identify user vs template sections
- Combine intelligently
- Fallback to manual for conflicts

**When smart merge fails**: User still has full control (keep/update/manual)

## Related Commands

- **`/project-brief`**: Complete the initialized brief through gap-driven conversation
- **`/epic`**: Create feature epics after brief is clear
- **`/plan TASK-###`**: Plan specific task implementation

## Tools

- **Write**: Create customized project files
- **Bash**: Copy template structure efficiently
- **Read**: Check for existing structure
- **Glob**: Detect existing pm/ and docs/ directories
- **AskUserQuestion**: (Optional) For the 2 questions if preferred over natural conversation

---

**Next Steps**: After init, run `/project-brief` to fill in your project brief through natural conversation.
