---
name: ui-design
description: Create and iterate on HTML UI mockups with parallel design exploration
aliases: ["mockup", "prototype"]
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - docs/development/guidelines/ui-design-guidelines.md  # Design tokens, breakpoints, accessibility
  - docs/project/ui-designs/design-overview.md  # Synthesis of approved designs
---

# /ui-design

**WHAT**: Generate HTML mockups with parallel variant exploration, conversational iteration, and tech-specific implementations (vanilla/shadcn/chakra).

**WHY**: Version-controlled, responsive, interactive designs that integrate directly into development workflow.

**HOW**: Parse request → Load design context → Generate parallel variants → Playwright review → Present options → Conversational iteration → Approval & synthesis.

## Usage

```bash
/ui-design "login screen"                            # Single design, ask variant count
/ui-design "show me 4 dashboard concepts"            # Parallel exploration
/ui-design "login using shadcn, 3 options"           # Tech-specific (includes React code)
/ui-design "show me vanilla and shadcn versions"     # Compare libraries
/ui-design list                                       # Show all designs
/ui-design list login-screen                          # Show specific design versions
```

## Execution Steps

### 1. Parse Request

```bash
# Extract from user prompt:
# - Design name ("login screen", "dashboard")
# - Variant count ("show me 4", "3 options") → default: ask user
# - Technology ("using shadcn", "with chakra-ui") → vanilla if not specified
# - Changes (if iteration: "move X", "increase Y")
```

### 2. Load Context

```bash
Read: docs/development/guidelines/ui-design-guidelines.md  # Tokens, breakpoints, a11y
Read: docs/project/ui-designs/design-overview.md           # Existing patterns
Glob: docs/project/ui-designs/{name}-v*.html               # Determine next version
```

**Check for tech-specific MCPs** (shadcn MCP if "shadcn" mentioned).

### 3. Generate Variants (Parallel)

```bash
# If variants > 1: Spawn N ui-ux-designer agents via Task tool (PARALLEL)
# Each agent explores different approach (layout/density/style)
# Share: base requirements + design tokens
# Unique: variant-specific prompt
```

### 4. Playwright Review (CRITICAL)

**Before presenting to user**:
```bash
# Load each generated HTML in Playwright
# Check: broken layouts, missing styles, JS errors, console warnings
# Verify: responsive at 320px, 768px, 1280px breakpoints
# Validate: interactive elements work (buttons, forms, dropdowns)
# Fix issues before presentation
```

### 5. Present & Iterate

**Present options**: Show files + brief descriptions + view instructions.

**Conversational iteration**:
- User selects: "I like option B", "v2b works"
- User changes: "move OAuth below", "increase spacing"
- User approves: "approve v2b", "that's the one"
- **After each iteration**: Playwright review again

### 6. Approval & Synthesis

```bash
# Update HTML metadata: Status: APPROVED
# Update design-overview.md: Add to Approved Designs section
# Extract patterns: Colors, spacing, components → design-overview
# Mark superseded: Note v1{a,b,c} replaced by v2b
```

## File Organization

```
docs/project/ui-designs/
  ├── design-overview.md          # Approved designs + patterns
  ├── login-screen-v1a.html       # Parallel variants
  ├── login-screen-v1b.html       # (user chose this)
  ├── login-screen-v2b.html       # ← APPROVED
  ├── dashboard-v1-shadcn.html    # Tech-specific
  └── components/buttons-v1.html  # Reusable patterns
```

**Naming**:
- Single: `{name}-v{N}.html`
- Variants: `{name}-v{N}{letter}.html`
- Tech-specific: `{name}-v{N}-{tech}.html`
- Components: `components/{name}-v{N}.html`

## HTML Structure

**Vanilla HTML**:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>{design-name} - v{N}</title>
    <style>
        :root {
            --primary: #3b82f6;  /* From ui-design-guidelines.md */
        }
        /* Responsive styles, component styles */
    </style>
</head>
<body>
    <!-- UI Design -->
    <script>/* Interactive behaviors */</script>
</body>
</html>

<!-- METADATA: Design, Version, Status: APPROVED, Design Decisions -->
```

**Tech-specific (e.g., shadcn)**:
```html
<!-- Visual preview with shadcn-matching styles -->
<!-- METADATA includes IMPLEMENTATION CODE section with copy-paste React components -->
```

**Reference**: See ui-design-guidelines.md for design tokens, breakpoints, a11y requirements.

## Technology Support

**Supported techs**:
- `"using shadcn"` → shadcn MCP, React code in HTML comments
- `"with chakra-ui"` → Chakra UI patterns
- `"material-ui"` → Material UI components
- No tech → Vanilla HTML/CSS

**Compare libraries**:
```bash
/ui-design "login, show me vanilla and shadcn versions"
→ login-screen-v1.html, login-screen-v1-shadcn.html
```

## Natural Language Parsing

**Command understands**:
- Selection: "option B", "v1b", "split screen"
- Approval: "approve v2b", "that's the one"
- Iteration: "move X below", "increase spacing", "try 3 more"
- Variant count: "show me 4", "3 options"
- Technology: "using shadcn", "with chakra"

## Asset Handling

**Images/logos**: Read file → base64 data URL → inline embed → self-contained HTML.

## Integration

**TASK.md reference**:
```markdown
Design Reference: docs/project/ui-designs/login-screen-v2b.html (approved)
Acceptance: Implement matching design, responsive 320px-1920px
```

**During /implement**:
- frontend-specialist reads approved HTML
- Tech-specific mockup: Copy React code from HTML comments
- Vanilla mockup: Implement in project's tech stack
- WORKLOG: "Implemented UI per login-screen-v2b"

## Agent Coordination

**Primary**: ui-ux-designer (creates mockups)
**Supporting**: frontend-specialist (feasibility and existing patterns)
**Approval**: User (visual review)

## Error Handling

- **No guidelines**: Create minimal HTML with standard tokens
- **Playwright errors**: Fix before presenting to user
- **Asset not found**: Skip asset, note in metadata

## Notes

**Best for**: Developer-designers, small teams, rapid exploration, design-in-code workflows
**Use Figma when**: Pixel-perfect requirements, non-technical stakeholders, professional handoff
