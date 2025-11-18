# Design Overview

Living documentation synthesizing approved UI designs and extracted patterns.

**Similar to `architecture-overview.md`**, this file consolidates design decisions from individual mockups into a cohesive design system reference.

**Last updated:** [Date of last update]

---

## Design Principles

*(Extracted from approved designs)*

1. **Mobile-first responsive** - All designs start at 320px
2. **WCAG AA accessibility** - Minimum contrast 4.5:1, keyboard navigation
3. **Progressive disclosure** - Complex features revealed progressively
4. **Consistency** - Reusable patterns across the application

---

## Approved Designs

### Screens

*(Add approved screen designs here as they're created)*

**Example:**
- **Login Screen**: `login-screen-v2b.html` (approved 2025-11-02)
  - Split-screen layout with brand illustration
  - OAuth positioned below email/password
  - Medium spacing (24px between sections)
  - Mobile: Stacks vertically, illustration at top

- **Dashboard**: `dashboard-v2.html` (approved 2025-11-01)
  - Card-based layout with 3-column responsive grid
  - Quick action sidebar on desktop
  - Collapses to single column on mobile

### Components

*(Add approved component designs here)*

**Example:**
- **Buttons**: `components/buttons-v2.html` (approved 2025-10-28)
- **Forms**: `components/forms-v1.html` (approved 2025-10-28)
- **Navigation**: `components/navigation-v1.html` (approved 2025-10-29)
- **Cards**: `components/cards-v1.html` (approved 2025-10-29)

---

## Design Tokens

**Extracted from approved designs**

*(These tokens should reflect actual usage in approved mockups)*

### Colors

```css
:root {
  /* Primary palette (from login-screen-v2b.html, dashboard-v2.html) */
  --primary: #3b82f6;      /* Blue - interactive elements, CTAs */
  --secondary: #8b5cf6;    /* Purple - secondary actions */
  --accent: #f59e0b;       /* Amber - highlights, notifications */

  /* Neutral palette */
  --gray-50: #f9fafb;
  --gray-100: #f3f4f6;
  --gray-500: #6b7280;
  --gray-900: #111827;

  /* Semantic colors */
  --success: #10b981;
  --error: #ef4444;
  --warning: #f59e0b;
}
```

**Sources:**
- Primary blue: Established in `login-screen-v2b.html` (login button)
- Secondary purple: Introduced in `dashboard-v2.html` (secondary actions)
- Accent amber: Used for notifications in both designs

### Typography

```css
:root {
  --font-family: Inter, system-ui, -apple-system, sans-serif;
  --font-size-base: 16px;
  --font-scale: 1.25;  /* Major Third scale */

  /* Line heights */
  --line-height-tight: 1.2;    /* Headings */
  --line-height-normal: 1.5;   /* Body text */

  /* Weights */
  --font-weight-normal: 400;
  --font-weight-semibold: 600;
  --font-weight-bold: 700;
}
```

**Sources:**
- Font family: Consistent across all approved designs
- Scale: Derived from heading hierarchy in `dashboard-v2.html`

### Spacing

```css
:root {
  --space-unit: 8px;  /* Base spacing unit */

  /* Spacing scale (multiples of 8px) */
  --space-1: 8px;
  --space-2: 16px;
  --space-3: 24px;  /* Most common section spacing */
  --space-4: 32px;
  --space-6: 48px;
  --space-8: 64px;
}
```

**Sources:**
- 24px spacing: Established in `login-screen-v2b.html` between form sections
- 32px spacing: Card gaps in `dashboard-v2.html`

### Borders & Radii

```css
:root {
  --radius-sm: 4px;   /* Input fields */
  --radius-md: 8px;   /* Buttons, cards */
  --radius-lg: 12px;  /* Modal dialogs */

  --border-width: 1px;
  --border-color: #e5e7eb;
}
```

**Sources:**
- 8px radius: Consistent across buttons and cards in all designs
- Border color: Form inputs in `login-screen-v2b.html`

---

## Component Patterns

### Buttons

**Primary Button** *(from buttons-v2.html)*

```css
.btn-primary {
  background: var(--primary);
  color: white;
  padding: 12px 24px;
  border-radius: var(--radius-md);
  font-weight: var(--font-weight-semibold);
  border: none;
  cursor: pointer;
  transition: all 200ms ease;
}

.btn-primary:hover {
  filter: brightness(110%);
}

.btn-primary:active {
  transform: scale(0.98);
}
```

**Secondary Button**

```css
.btn-secondary {
  background: transparent;
  color: var(--primary);
  border: 1px solid var(--border-color);
  /* ... rest same as primary ... */
}
```

**Reference:** `components/buttons-v2.html`

### Form Layout

**Standard Form Pattern** *(from forms-v1.html)*

- Label above input (not inline)
- Input height: 44px (touch-friendly minimum)
- Vertical spacing between fields: 16px
- Error messages below field in red
- Helper text: 14px, muted color

**Example:**
```html
<div class="form-field">
  <label for="email">Email</label>
  <input type="email" id="email" placeholder="you@example.com">
  <span class="helper-text">We'll never share your email</span>
</div>
```

**Reference:** `components/forms-v1.html`

### Card Component

**Card Pattern** *(from dashboard-v2.html)*

- Background: white with subtle shadow
- Padding: 24px
- Border radius: 8px
- Shadow: `0 1px 3px rgba(0, 0, 0, 0.1)`

**Reference:** `components/cards-v1.html`

---

## Layout Patterns

### Split-Screen Layout

**Pattern** *(from login-screen-v2b.html)*

- Desktop (≥1024px): 50/50 split (illustration left, content right)
- Tablet/Mobile (<1024px): Stacked (illustration top, content below)
- Illustration min-height: 400px on mobile
- Content max-width: 480px, centered

**Use for:**
- Authentication screens (login, signup)
- Feature showcases
- Landing pages with hero

**Reference:** `login-screen-v2b.html`

### Card Grid Layout

**Pattern** *(from dashboard-v2.html)*

- Desktop: 3 columns
- Tablet: 2 columns
- Mobile: 1 column
- Gap: 32px (var(--space-4))
- Cards equal height per row

**Use for:**
- Dashboards
- Product listings
- Content galleries

**Reference:** `dashboard-v2.html`

---

## Responsive Breakpoints

**Established breakpoints from approved designs:**

```css
/* Mobile (default) */
@media (min-width: 640px) {
  /* Small tablet / large phone landscape */
}

@media (min-width: 768px) {
  /* Tablet */
}

@media (min-width: 1024px) {
  /* Desktop */
  /* Split-screen layouts activate here */
}

@media (min-width: 1280px) {
  /* Large desktop */
}
```

---

## Accessibility Patterns

**Extracted from approved designs:**

1. **Color Contrast**
   - All text meets WCAG AA (4.5:1 minimum)
   - Interactive elements: 3:1 minimum
   - Tested with WebAIM Contrast Checker

2. **Focus Indicators**
   - 2px solid outline in primary color
   - 2px offset from element
   - Visible on all interactive elements

3. **Keyboard Navigation**
   - Logical tab order
   - All interactive elements keyboard accessible
   - Skip links provided (where applicable)

4. **Touch Targets**
   - Minimum 44x44px (iOS/Android standard)
   - 8px spacing between adjacent targets

**Reference:** All approved designs follow these patterns

---

## Animation & Motion

**Established patterns:**

- **Transition duration**: 200ms default
- **Easing**: `ease` for most transitions
- **Hover effects**: Brightness increase (110%) + shadow lift
- **Active states**: Slight scale down (0.98)

**Respect user preferences:**
```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Superseded Designs

*(Designs explored but not approved - kept for reference)*

**Example:**
- `login-screen-v1a.html` - Centered minimal layout (superseded by v2b split-screen)
- `login-screen-v1c.html` - Full-width background (superseded by v2b split-screen)
- `dashboard-v1.html` - List-based layout (superseded by v2 card grid)

**Why keep superseded designs?**
- Document "roads not taken"
- Reference alternative approaches if requirements change
- Learn from exploration process

---

## Next Steps

As new designs are approved:

1. **Add to Approved Designs section** with description and reference
2. **Extract new patterns** (colors, spacing, components)
3. **Update Design Tokens** if new values introduced
4. **Add to Component Patterns** if reusable
5. **Note superseded designs** that are replaced

---

## Guidelines

See `docs/development/guidelines/ui-design-guidelines.md` for:
- Complete design token configuration
- Responsive breakpoint definitions
- Accessibility requirements
- Asset handling guidelines
- Component library patterns

---

**Maintained by:** Teams using `/ui-design` command
**Update frequency:** After each design approval
**Version:** Living document (no version numbers)
