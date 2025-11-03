# UI Designs

HTML mockups for design exploration and visual reference during implementation.

## What's Here

This directory contains HTML mockups generated via the `/ui-design` command:

- **Screen designs** - Full page/screen mockups (login, dashboard, onboarding, etc.)
- **Component designs** - Reusable component patterns (`components/` directory)
- **design-overview.md** - Synthesis document tracking approved designs and extracted patterns

## File Organization

**Flat structure:**
```
ui-designs/
  ├── design-overview.md              # Synthesis (tracks approved versions)
  ├── login-screen-v1a.html           # Parallel variants
  ├── login-screen-v1b.html
  ├── login-screen-v2b.html           # ← Approved (noted in design-overview.md)
  ├── dashboard-v1.html
  ├── dashboard-v2.html               # ← Approved
  └── components/                     # Reusable patterns
      ├── buttons-v1.html
      ├── forms-v1.html
      └── README.md
```

**Versioning:**
- `{name}-v{N}.html` - Single design iteration
- `{name}-v{N}{letter}.html` - Parallel variants (v1a, v1b, v1c)

## How Designs Work

### Self-Contained HTML

Each mockup is a **single HTML file** with:
- ✅ **Inline CSS** - All styles in `<style>` tags
- ✅ **Inline JavaScript** - Interactive behaviors in `<script>` tags (optional)
- ✅ **Embedded assets** - Images/fonts as base64 data URLs
- ✅ **Metadata** - Design info in HTML comment block

**Benefits:**
- Version controlled in git (clear diffs)
- Viewable directly in browser (no build step)
- Truly responsive (test real breakpoints)
- Truly interactive (real JavaScript)

### Viewing Designs

**Double-click** any `.html` file to open in your default browser, or:

```bash
open login-screen-v2b.html
# Linux: xdg-open login-screen-v2b.html
# Windows: start login-screen-v2b.html
```

### Design Metadata

Each HTML file includes metadata comment block:

```html
<!--
=== UI DESIGN METADATA ===
Design: login-screen
Version: 2b
Created: 2025-11-02 15:15
Agent: ui-ux-designer

Based on: login-screen-v1b.html
Changes: OAuth moved below form, medium spacing

Status: APPROVED
Approved: 2025-11-02 16:00
Tracked in: design-overview.md
-->
```

## Workflow

### 1. Generate Designs

```bash
/ui-design "login screen with OAuth, show me 4 options"
```

Creates 4 parallel variants exploring different approaches.

### 2. Choose & Iterate

```
User: I like v1b with the split screen
User: Move OAuth below the form, give me 3 spacing options
```

Generates v2{a,b,c} with different spacing.

### 3. Approve

```
User: approve v2b
```

Command updates `design-overview.md` with approved design.

### 4. Reference in Tasks

**TASK.md:**
```markdown
## Design Reference
UI Design: `docs/project/ui-designs/login-screen-v2b.html` (approved 2025-11-02)

## Acceptance Criteria
- [ ] Implement UI matching approved design
```

### 5. Implement

Frontend developer/frontend-specialist:
1. Opens `login-screen-v2b.html` in browser
2. Uses as visual/interaction reference
3. Implements production code matching design
4. References in WORKLOG

## Finding Approved Designs

**Check design-overview.md** - Single source of truth for "which version is final"

```bash
/ui-design list
# Shows all designs with approval status
```

Or search design-overview.md for "APPROVED".

## Components

Reusable component patterns live in `components/` directory:

```
components/
  ├── README.md           # Component index
  ├── buttons-v1.html
  ├── forms-v1.html
  └── navigation-v1.html
```

See `components/README.md` for current approved components.

## Design Guidelines

All designs follow tokens and patterns defined in:
- `docs/development/guidelines/ui-design-guidelines.md` - Design tokens, breakpoints, accessibility

Teams customize this guideline to match their brand/requirements.

## Why HTML Mockups?

**Advantages over traditional design tools:**
- ✅ Version controlled (git diff, blame, history)
- ✅ No external tool dependencies (just a browser)
- ✅ Actually responsive (test real breakpoints, not simulations)
- ✅ Actually interactive (real JavaScript, not prototypes)
- ✅ Code reusable (copy HTML structure to production)
- ✅ Accessibility testable (screen readers work)
- ✅ AI-generated (parallel exploration at speed)

**Best for:**
- Developer-designers comfortable with HTML/CSS
- Small teams without dedicated designers
- Quick responsive/interactive validation
- Design-in-code workflows

**Use traditional tools (Figma, Sketch) when:**
- Complex visual design with pixel-perfect requirements
- Non-technical stakeholders need to edit
- Professional design handoff needed

## Superseded Designs

Designs explored but not approved are kept for reference (documented in design-overview.md).

**Why keep them?**
- Document "roads not taken"
- Reference alternative approaches if requirements change
- Learn from exploration process

No need to delete old versions - disk space is cheap, history is valuable.

---

**Quick Start:**

1. Generate designs: `/ui-design "your design request"`
2. View in browser: `open {filename}.html`
3. Approve when ready: Conversational with `/ui-design`
4. Reference in tasks: Link approved design in TASK.md

**Questions?** See command docs: `plugins/ai-toolkit/commands/ui-design.md`
