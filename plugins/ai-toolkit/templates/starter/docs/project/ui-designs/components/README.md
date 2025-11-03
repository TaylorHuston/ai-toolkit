# UI Components

Reusable component pattern mockups.

## What's Here

HTML mockups showcasing reusable UI component patterns:
- Button variants (primary, secondary, states)
- Form patterns (inputs, validation, layouts)
- Navigation patterns (header, sidebar, mobile)
- Card patterns (content cards, stat cards, feature cards)
- And more as your design system evolves

## Current Components

*(Add approved components here as they're created)*

**Example:**
- **Buttons**: `buttons-v2.html` (approved 2025-10-28)
  - 8 button variants with all states (hover, active, disabled, loading)

- **Forms**: `forms-v1.html` (approved 2025-10-28)
  - Text inputs, textareas, selects, checkboxes, radios
  - Validation states (error, success, warning)
  - Label positions, helper text, required indicators

- **Navigation**: `navigation-v1.html` (approved 2025-10-29)
  - Desktop header with logo, nav links, user menu
  - Sidebar navigation for dashboard layouts
  - Mobile menu (hamburger → drawer)

- **Cards**: `cards-v1.html` (approved 2025-10-29)
  - Content card (image + title + description + CTA)
  - Stat card (number + label + trend indicator)
  - Feature card (icon + title + description)

See `../design-overview.md` for full component catalog and currently approved versions.

## How to Use

### During Design Phase

```bash
/ui-design "button component with all our button variants"
```

Command creates `components/buttons-v1.html` showcasing all button types in one mockup.

### During Implementation

1. **Open component mockup** in browser:
   ```bash
   open components/buttons-v2.html
   ```

2. **Inspect HTML/CSS** for implementation details

3. **Copy patterns** to production code:
   ```jsx
   // React example
   <button className="btn-primary">
     {children}
   </button>
   ```

4. **Maintain consistency** across application by referencing approved patterns

## Component Mockup Structure

Each component mockup shows:
- ✅ **All variants** (primary, secondary, tertiary, etc.)
- ✅ **All states** (default, hover, active, disabled, loading)
- ✅ **All sizes** (small, medium, large)
- ✅ **Usage examples** in context
- ✅ **Accessibility notes** (ARIA labels, keyboard support)
- ✅ **Responsive behavior** if applicable

**Example structure for buttons-v2.html:**
```html
<section>
  <h2>Primary Buttons</h2>
  <button class="btn-primary">Default</button>
  <button class="btn-primary" style="filter: brightness(110%)">Hover</button>
  <button class="btn-primary" disabled>Disabled</button>
  <button class="btn-primary loading">Loading...</button>
</section>

<section>
  <h2>Secondary Buttons</h2>
  <!-- variants -->
</section>

<!-- etc -->
```

## Creating New Components

### 1. Identify Need

During implementation, notice repeated patterns:
- "We need a consistent card component"
- "Form inputs look different across pages"
- "Button styles are inconsistent"

### 2. Generate Component Mockup

```bash
/ui-design "card component showing content card, stat card, and feature card variants"
```

### 3. Iterate & Approve

Review generated mockup, provide feedback, approve final version.

### 4. Implement in Production

Extract approved patterns to production code (React components, Vue components, CSS classes, etc.)

### 5. Document

Component gets referenced in `design-overview.md` under Components section.

## Component vs. Screen Designs

**Component designs** (`components/` directory):
- ✅ Small, reusable UI elements
- ✅ Show all variants/states in one file
- ✅ Referenced across multiple screen designs
- ✅ Building blocks of the design system

**Screen designs** (root `ui-designs/` directory):
- ✅ Full page/screen mockups
- ✅ Compose multiple components together
- ✅ Show real-world usage in context
- ✅ Define layout patterns

**Example:**
- `components/buttons-v2.html` - All button types
- `login-screen-v2b.html` - Uses primary button from buttons-v2

## Versioning

Components follow same versioning as screen designs:
- `buttons-v1.html` - Initial version
- `buttons-v2.html` - Updated with feedback
- `buttons-v2a.html`, `buttons-v2b.html` - Parallel variants if exploring options

## Design System Maturity

As your component library grows:

**Emerging** (0-5 components):
- Creating components as needed
- Documenting patterns in design-overview.md
- No formal component library yet

**Established** (5-15 components):
- Core components defined (buttons, forms, cards, nav)
- Patterns reused across screens
- design-overview.md has substantial Components section

**Mature** (15+ components):
- Comprehensive component catalog
- Consider extracting to dedicated design system
- May want separate component documentation site

## Why Component Mockups?

**Benefits:**
- ✅ **Consistency** - Single source of truth for component appearance
- ✅ **Reference** - Easy to check "what does a primary button look like?"
- ✅ **Exploration** - Try variations before implementing
- ✅ **Documentation** - Self-documenting via HTML/CSS
- ✅ **Testing** - Visual regression testing possible (screenshot comparison)

**Alternative approaches:**
- Storybook (React, Vue component showcase)
- Pattern library sites (dedicated documentation)
- Figma component libraries (design tool approach)

HTML component mockups work well for small teams doing design-in-code. As you scale, consider dedicated tools.

## Quick Reference

| Need | Command |
|------|---------|
| Create component | `/ui-design "component description"` |
| View component | `open components/{name}.html` |
| List components | `/ui-design list` (shows all designs) |
| Find approved | Check `design-overview.md` Components section |

---

**Empty directory?** Components are created as needed via `/ui-design` command. Start by generating your first component!
