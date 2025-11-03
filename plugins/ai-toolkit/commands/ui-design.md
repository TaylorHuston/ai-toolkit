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

# /ui-design Command

Create and iterate on HTML UI mockups through conversational design exploration with parallel variant generation.

## Purpose

**Design-in-code exploration** - Generate HTML mockups that integrate with project workflow, support parallel variant exploration, and use conversational iteration until approval.

Unlike traditional design tools, these are:
- ✅ **Version controlled** in git (track changes, diff)
- ✅ **Actually responsive** (test real breakpoints)
- ✅ **Actually interactive** (real JavaScript)
- ✅ **Code-adjacent** (easy to reference during implementation)
- ✅ **AI-generated** (parallel exploration at speed)

## Agent Coordination

**Primary**: ui-ux-designer (creates HTML mockups, applies design principles)
**Supporting**: context-analyzer (gathers existing patterns), frontend-specialist (feasibility checks)
**Approval**: User (visual review, not agent review)

## How It Works

**This command orchestrates conversational design exploration:**

**1. Parse Request**
- Extract design requirements from user prompt
- Detect variant count ("show me 4 options", "3 variants", etc.)
- **Detect technology stack** ("using shadcn", "with chakra-ui", "material-ui", etc.)
- Default: Ask user how many variants (1 for single, 3+ for exploration)

**2. Load Context**
- Read `ui-design-guidelines.md` for design tokens, breakpoints, accessibility requirements
- Read `design-overview.md` for existing patterns
- Scan `ui-designs/` for existing designs (to determine next version number)
- Gather existing brand assets (colors, fonts, logos from codebase)
- **Check available MCPs** (shadcn, if tech stack specified)

**3. Generate Variants (Parallel)**
- If variants > 1: Spawn N ui-ux-designer agents IN PARALLEL using Task tool
- Each agent explores different approach (layout, density, style)
- All agents share base requirements + design tokens
- Each receives variant-specific exploration prompt

**4. Present Options**
- Show all generated files with brief descriptions
- Provide view instructions (open in browser)
- Ask conversational question: "Which do you prefer?" or "What changes?"

**5. Conversational Iteration**
- User selects variant: "I like option B" or "v2b works"
- User requests changes: "move OAuth below, increase spacing"
- User requests more: "show me 2 completely different approaches"
- User approves: "approve v2b" or "that's the one"

**6. Approval & Synthesis**
- Mark approved design in HTML metadata
- Update `design-overview.md` with approved design reference
- Extract patterns (colors, spacing, components) to design-overview
- Mark superseded variants in design-overview

## File Organization

**Flat structure:**
```
docs/project/ui-designs/
  ├── design-overview.md              # Synthesis doc (tracks approved versions)
  │
  ├── login-screen-v1a.html           # Parallel variants
  ├── login-screen-v1b.html           # (user chose this)
  ├── login-screen-v1c.html
  │
  ├── login-screen-v2a.html           # Iterations on chosen
  ├── login-screen-v2b.html           # ← APPROVED (noted in design-overview.md)
  ├── login-screen-v2c.html
  │
  ├── dashboard-v1.html
  ├── dashboard-v2.html               # ← APPROVED
  │
  └── components/                     # Reusable patterns
      ├── buttons-v1.html
      ├── buttons-v2.html             # ← APPROVED
      ├── forms-v1.html               # ← APPROVED
      └── navigation-v1.html
```

**Versioning:**
- Single design: `{name}-v{N}.html` (e.g., `dashboard-v2.html`)
- Parallel variants: `{name}-v{N}{letter}.html` (e.g., `login-screen-v1b.html`)
- **Tech-specific**: `{name}-v{N}-{tech}.html` (e.g., `login-screen-v1-shadcn.html`)
- Components: `components/{name}-v{N}.html`

## HTML Structure

**Tech-agnostic (vanilla HTML/CSS):**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{design-name} - v{N}</title>
    <style>
        /* Design tokens from ui-design-guidelines.md */
        :root {
            --primary: #3b82f6;
            --secondary: #8b5cf6;
            --font-family: Inter, system-ui, sans-serif;
        }

        /* Responsive styles */
        /* Component styles */
    </style>
</head>
<body>
    <!-- UI Design -->

    <script>
        // Interactive behaviors (optional)
    </script>
</body>
</html>

<!--
=== UI DESIGN METADATA ===
Design: login-screen
Version: 2b
Created: 2025-11-02 15:15
Agent: ui-ux-designer

Based on: login-screen-v1b.html
Changes: OAuth moved below form, medium spacing applied

Design Decisions:
- Split-screen layout (50/50 desktop, stacked mobile)
- OAuth below email/password for progressive disclosure
- Medium spacing (24px between sections)
- WCAG AA compliant (tested contrast, keyboard nav)

Status: APPROVED
Approved: 2025-11-02 16:00
Tracked in: design-overview.md
-->
```

**Tech-specific (e.g., shadcn/ui):**
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{design-name} - v{N} - shadcn</title>
    <style>
        /* shadcn/ui design tokens (HSL format) */
        :root {
            --primary: 222.2 47.4% 11.2%;
            --primary-foreground: 210 40% 98%;
            --radius: 0.5rem;
        }

        /* shadcn component styles matching actual components */
        .btn-primary {
            background: hsl(var(--primary));
            color: hsl(var(--primary-foreground));
            border-radius: var(--radius);
            padding: 0.5rem 1rem;
            /* ... full shadcn button styling */
        }
    </style>
</head>
<body>
    <!-- HTML structure mirroring shadcn components -->
    <button class="btn-primary">Login</button>
</body>
</html>

<!--
=== UI DESIGN METADATA ===
Design: login-screen
Version: 1
Technology: shadcn/ui
Created: 2025-11-03 14:30
Agent: ui-ux-designer
MCP Used: shadcn

Design Decisions:
- Uses shadcn/ui component patterns
- Tailwind CSS utility classes
- Radix UI primitives for accessibility

Status: DRAFT
Tracked in: design-overview.md

=== IMPLEMENTATION CODE ===

File: components/LoginForm.tsx
---
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card"

export function LoginForm() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>Login</CardTitle>
      </CardHeader>
      <CardContent>
        <Input type="email" placeholder="Email" />
        <Input type="password" placeholder="Password" />
        <Button>Login</Button>
      </CardContent>
    </Card>
  )
}
---

File: components.json (shadcn config)
---
{
  "style": "default",
  "tailwind": {
    "config": "tailwind.config.js",
    "css": "app/globals.css",
    "baseColor": "slate",
    "cssVariables": true
  }
}
---
-->
```

## Technology-Specific Mockups

**Request mockups using specific component libraries:**

```bash
# shadcn/ui mockup
/ui-design "login screen using shadcn"

# Compare multiple libraries
/ui-design "dashboard, show me shadcn and chakra-ui versions"

# Vanilla HTML (default)
/ui-design "login screen"
```

**Supported technologies:**
- `"using shadcn"` or `"with shadcn/ui"` → Uses shadcn MCP for component-aware generation
- `"using chakra"` or `"with chakra-ui"` → Uses Chakra UI patterns
- `"using material-ui"` or `"with mui"` → Uses Material UI components
- No tech mention → Tech-agnostic vanilla HTML/CSS

**Output format:**
- Filename: `{name}-v{N}-{tech}.html` (e.g., `login-v1-shadcn.html`)
- Visual preview: HTML renders in browser showing design
- Implementation code: Embedded in HTML comments as copy-paste ready React/Vue/etc code
- Design tokens: Match tech stack conventions (HSL for shadcn, etc.)

**Comparing libraries:**
```bash
/ui-design "login screen, show me vanilla, shadcn, and chakra versions"
→ Generates 3 files:
  - login-screen-v1.html (vanilla HTML/CSS)
  - login-screen-v1-shadcn.html (shadcn styling + React code)
  - login-screen-v1-chakra.html (Chakra styling + React code)
→ User compares side-by-side before committing to library
```

## Conversational Patterns

**Initial Request:**
```
/ui-design "login screen with email/password and OAuth"
```

Command asks: `How many design variants? (1 for single, 3+ for exploration)`

User: `4`

Command generates 4 parallel variants, asks: `Which do you prefer?`

**Tech-specific request:**
```
/ui-design "login screen using shadcn, show me 3 options"
```

Command generates 3 shadcn-based variants with implementation code.

**Selection:**
User: `I like v1b with the split screen`

**Iteration:**
User: `Can you move the OAuth buttons below the form and give me 3 spacing options?`

Command generates v2{a,b,c} with different spacing.

**Approval:**
User: `approve v2b`

Command updates design-overview.md, marks design approved.

## Command Intelligence

**Understand natural language:**
- Selection: "option B", "the second one", "v1b", "split screen"
- Approval: "approve b", "that's the one", "use v2b"
- Iteration: "can you...", "make the...", "try..."
- Variant count: "show me 4", "3 options", "give me 2 more"
- Changes: "move X", "increase Y", "change Z to..."
- **Technology**: "using shadcn", "with chakra", "material-ui version"

**Extract from prompt:**
- "show me 4 login screen concepts" → variants: 4, design: login screen
- "3 dashboard layouts" → variants: 3, design: dashboard
- "login screen using shadcn" → tech: shadcn, design: login screen
- "show me vanilla and shadcn versions" → 2 variants, techs: [vanilla, shadcn]
- Single request with no count → ask user

## Asset Handling

**Images, logos, icons:**

User references conversationally:
```
/ui-design "login form with our logo from assets/logo.png"
```

ui-ux-designer:
1. Reads referenced file (assets/logo.png)
2. Converts to base64 data URL
3. Embeds inline: `<img src="data:image/png;base64,..." alt="Logo">`
4. Result: Self-contained single-file HTML

**Icons:** Inline SVG (no encoding needed)
**Fonts:** Use web font CDNs or data URLs
**Trade-off:** Larger files, but acceptable for mockups

## Viewing Designs

**Simple approach:**

Command outputs:
```
✓ Created login-screen-v1b.html

View design:
  open docs/project/ui-designs/login-screen-v1b.html

Or double-click the file in your file browser.
```

## Integration with Tasks

**Reference in requirements:**

TASK.md or acceptance criteria:
```markdown
## Design Reference
UI Design: `docs/project/ui-designs/login-screen-v2b.html` (approved 2025-11-02)

## Acceptance Criteria
- [ ] Implement UI matching approved login-screen-v2b design
- [ ] All interactive elements functional
- [ ] Responsive 320px-1920px
```

**During /implement:**

frontend-specialist:
1. Reads approved design HTML file
2. Uses as visual/interaction reference
3. **If tech-specific mockup** (e.g., `-shadcn.html`): Extracts implementation code from HTML comments
4. **If vanilla mockup**: Implements using project's tech stack
5. References in WORKLOG: "Implemented login UI per login-screen-v2b"

**Tech-specific mockup benefits:**
- Copy-paste ready React/Vue code from HTML comments
- Styling already matches visual preview
- Component library patterns pre-configured
- Faster implementation (reference code provided)

## Workflow Examples

### Example 1: Initial Exploration

```
User: /ui-design "show me 3 different dashboard concepts"

Command:
Generating 3 dashboard variants...
✓ dashboard-v1a.html (card grid layout)
✓ dashboard-v1b.html (sidebar navigation)
✓ dashboard-v1c.html (tabs + widgets)

Which approach works best for your needs?

User: I like the card grid from v1a

Command:
Using card grid layout as base. What changes would you like?

User: Can you make the cards bigger and add a filter bar at the top?

Command:
Creating updated design...
✓ dashboard-v2.html (larger cards, top filter bar)

User: Perfect, approve it

Command:
✓ Approved dashboard-v2.html
✓ Updated design-overview.md:
  - Added "Dashboard: dashboard-v2.html" to Approved Designs
  - Extracted card grid pattern
  - Noted v1{a,b,c} as superseded

Ready to reference in tasks!
```

### Example 2: Component Design

```
User: /ui-design "button component showcasing all our button variants"

Command:
How many design variants? (1 for single, 3+ for exploration)

User: 1

Command:
Creating button component showcase...
✓ components/buttons-v1.html

View: open docs/project/ui-designs/components/buttons-v1.html

User: Looks good, approve it

Command:
✓ Approved components/buttons-v1.html
✓ Updated design-overview.md (Components section)
```

### Example 3: Comparing Component Libraries

```
User: /ui-design "login screen, show me shadcn and chakra-ui versions"

Command:
Generating 2 tech-specific mockups...
✓ login-screen-v1-shadcn.html (shadcn/ui components + React code)
✓ login-screen-v1-chakra.html (Chakra UI components + React code)

Both files include:
- Visual preview (open in browser)
- Implementation code (in HTML comments)
- Component library setup instructions

User: I like the shadcn version better

Command:
Great choice! shadcn/ui is approved as your component library.
Would you like me to generate more screens using shadcn?

User: Yes, show me a dashboard using shadcn

Command:
✓ dashboard-v1-shadcn.html
(includes shadcn Card, Table, Button components with React code)
```

### Example 4: Design-First Flow

```
User: /ui-design "onboarding flow, show me 4 different approaches"

[generates v1{a,b,c,d}, user chooses v1c]
[iterates to v2a, user approves]

User: Now let's create an epic based on this design

User: /epic

Agent: I'll help create an epic. What would you like to name it?

User: User Onboarding Flow

[epic creation conversation references onboarding-v2a.html in scope]
```

## List Command

**Show all designs:**
```
/ui-design list
```

Output:
```
UI Designs (15 total):

login-screen:
  v1a, v1b, v1c, v1d (4 layout explorations)
  v2a, v2b ✓, v2c (spacing variations, v2b approved)

dashboard:
  v1a, v1b, v1c (3 concepts)
  v2 ✓ (v2 approved)

onboarding:
  v1a, v1b (2 flow approaches)

components/:
  buttons: v1 ✓
  forms: v1 ✓
  navigation: v1, v2

See design-overview.md for approved designs and extracted patterns.
```

**Show specific design:**
```
/ui-design list login-screen
```

Output:
```
login-screen designs:

v1a - Centered minimal layout
v1b - Split-screen with illustration ← User chose this
v1c - Full-width background
v1d - Sidebar layout

v2a - OAuth below, tight spacing
v2b - OAuth below, medium spacing ← APPROVED
v2c - OAuth below, generous spacing

Current: v2b (approved 2025-11-02)
```

## Outputs

**Following conversational design exploration:**

**docs/project/ui-designs/{name}-v{N}.html** (or v{N}{letter} for variants):
- ✓ Self-contained HTML with inline CSS/JS
- ✓ Design tokens from ui-design-guidelines.md
- ✓ Metadata in HTML comment block
- ✓ Responsive (tested 320px-1920px)
- ✓ Accessible (WCAG AA compliant)

**docs/project/ui-designs/design-overview.md** (updated on approval):
- ✓ Add approved design to relevant section
- ✓ Extract patterns (colors, spacing, components)
- ✓ Note superseded variants
- ✓ Update design tokens if new patterns introduced

**No separate WORKLOG** - metadata in HTML sufficient for design artifacts

## Guidelines Reference

**Following** `ui-design-guidelines.md`:
- Design tokens (colors, typography, spacing)
- Responsive breakpoints
- Accessibility requirements (WCAG level, focus indicators)
- Component patterns (if design system exists)

## Best Practices

1. **Start with exploration** (3-4 variants) to see options
2. **Iterate based on choice** (2-3 variants for refinement)
3. **Approve when confident** (don't over-iterate)
4. **Update design-overview.md** (synthesis is key)
5. **Reference in tasks** (link approved designs to implementation)
6. **Components for reusability** (extract common patterns)

## Notes

**Positioning:** HTML mockups are **developer-friendly design exploration**, not replacement for professional design tools like Figma.

**Best for:**
- Developer-designers comfortable with HTML/CSS
- Small teams without dedicated designers
- Quick responsive/interactive validation
- Design-in-code workflows
- AI-assisted rapid exploration

**Use traditional tools when:**
- Complex visual design with pixel-perfect requirements
- Non-technical stakeholders need to edit
- Professional design handoff needed
