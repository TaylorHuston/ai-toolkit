---
title: UI Design Guidelines
created: "2025-11-02"
last_updated: "2025-11-02"
status: "Active"
purpose: "Configuration for /ui-design command - design tokens, breakpoints, accessibility requirements"
---

# UI Design Guidelines

Configuration and standards for HTML mockup generation via `/ui-design` command.

**Philosophy**: "Orchestrate, don't prescribe" - This guideline provides customizable configuration. Teams adjust tokens/requirements here, `/ui-design` command applies them.

---

## Mockup Configuration

```yaml
# Technical specifications for HTML mockup generation
mockup_format: "single_file_html"
css_approach: "inline"           # All CSS in <style> tags
javascript_approach: "inline"    # All JS in <script> tags (optional)
asset_embedding: "data_urls"     # Images/fonts as base64 data URLs

# File naming pattern
versioning_pattern: "{name}-v{N}.html"          # Single design
variant_pattern: "{name}-v{N}{letter}.html"     # Parallel variants (v1a, v1b, v1c)

# Directory structure
designs_location: "docs/project/ui-designs/"
components_location: "docs/project/ui-designs/components/"
```

---

## Design Tokens

**Customizable by teams** - Adjust to match your brand/project.

### Colors

```yaml
colors:
  # Primary palette
  primary: "#3b82f6"        # Blue - interactive elements, CTAs
  secondary: "#8b5cf6"      # Purple - secondary actions
  accent: "#f59e0b"         # Amber - highlights, notifications

  # Neutral palette (example pattern - define full scale in design-overview.md)
  gray-100: "#f3f4f6"
  gray-300: "#d1d5db"
  gray-500: "#6b7280"
  gray-700: "#374151"
  gray-900: "#111827"
  # ... (50, 200, 400, 600, 800)

  # Semantic colors
  success: "#10b981"        # Green
  error: "#ef4444"          # Red
  # ... (warning, info)

  # Text colors (derived from neutrals)
  text-primary: "#111827"   # gray-900
  text-secondary: "#6b7280" # gray-500
```

### Typography

```yaml
typography:
  # Font families
  font_family_sans: "Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif"
  font_family_mono: "'Fira Code', 'Courier New', monospace"

  # Type scale (example pattern - 1.25 Major Third ratio)
  font_size_sm: "0.8rem"     # 12.8px
  font_size_base: "1rem"     # 16px
  font_size_lg: "1.25rem"    # 20px
  font_size_2xl: "1.953rem"  # 31.25px
  # ... (xs, xl, 3xl, 4xl - calculate using scale ratio)

  # Line heights
  line_height_tight: "1.2"   # Headings
  line_height_normal: "1.5"  # Body text

  # Font weights (example range)
  font_weight_normal: "400"
  font_weight_semibold: "600"
  # ... (medium: 500, bold: 700)
```

### Spacing

```yaml
spacing:
  # Base unit (all spacing multiples of 8px)
  space_unit: "8px"

  # Spacing scale (example pattern)
  space_1: "8px"     # 1 * unit
  space_2: "16px"    # 2 * unit
  space_4: "32px"    # 4 * unit
  space_8: "64px"    # 8 * unit
  space_16: "128px"  # 16 * unit
  # ... (0, 3, 5, 6, 10, 12 - multiply unit by scale number)

  # Semantic spacing (derived from scale)
  padding_input: "12px 16px"      # Form inputs
  padding_button: "12px 24px"     # Buttons
```

### Borders & Radii

```yaml
borders:
  border_width: "1px"
  border_color: "#e5e7eb"   # gray-200

  # Border radius (example pattern)
  radius_none: "0"
  radius_sm: "4px"
  radius_md: "8px"
  radius_full: "9999px"     # Pills/circles
  # ... (lg: 12px, xl: 16px)
```

### Shadows

```yaml
shadows:
  shadow_sm: "0 1px 2px 0 rgba(0, 0, 0, 0.05)"
  shadow_md: "0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06)"
  shadow_lg: "0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05)"
  shadow_xl: "0 20px 25px -5px rgba(0, 0, 0, 0.1), 0 10px 10px -5px rgba(0, 0, 0, 0.04)"
```

---

## Responsive Breakpoints

```yaml
breakpoints:
  # Mobile-first approach (min-width queries)
  mobile: "320px"      # Minimum supported width
  sm: "640px"          # Small tablets, large phones (landscape)
  md: "768px"          # Tablets
  lg: "1024px"         # Desktop
  xl: "1280px"         # Large desktop
  xxl: "1536px"        # Extra large desktop
```

---

## Accessibility Requirements

```yaml
accessibility:
  # WCAG compliance level
  wcag_level: "AA"              # AA (recommended) or AAA

  # Color contrast minimums (WCAG 2.1)
  contrast_normal_text: "4.5:1"     # Normal text (< 18pt)
  contrast_large_text: "3:1"        # Large text (≥ 18pt or 14pt bold)
  contrast_ui_components: "3:1"     # Interactive components

  # Focus indicators
  focus_indicators: "required"
  focus_style: "2px solid primary"  # Visible focus ring
  focus_offset: "2px"               # Space between element and focus ring

  # Keyboard navigation
  keyboard_navigation: "required"
  tab_order_logical: "required"
  skip_links: "recommended"         # Skip to main content

  # Screen reader support
  screen_reader_support: "required"
  aria_labels: "required"           # All interactive elements labeled
  semantic_html: "required"         # Proper heading hierarchy, landmarks

  # Touch targets (mobile)
  min_touch_target: "44px"          # iOS/Android minimum
  touch_spacing: "8px"              # Space between targets

  # Motion & animation
  respect_prefers_reduced_motion: "required"  # Disable animations if user prefers
```

---

## Component Patterns

**Links to approved reusable components** (if design system exists).

```yaml
component_patterns:
  # Reference approved component mockups
  buttons: "components/buttons-v1.html"         # Button variants, states
  forms: "components/forms-v1.html"             # Input fields, labels, validation
  navigation: "components/navigation-v1.html"   # Header, sidebar, mobile menu
  cards: "components/cards-v1.html"             # Content cards, stat cards

  # Component availability
  available: true  # Set to false if no components exist yet

  # Design system maturity
  maturity: "emerging"  # "none", "emerging", "established", "mature"
```

---

## Layout Patterns

Common layout approaches to explore during mockup generation.

```yaml
layout_patterns:
  # Common layouts
  - "centered-single-column"     # Centered content, max-width container
  - "split-screen"               # 50/50 split (content + illustration/sidebar)
  - "sidebar-navigation"         # Fixed sidebar, scrollable main content
  - "full-width-hero"            # Hero section + content below
  - "card-grid"                  # Responsive grid of cards
  - "dashboard-widgets"          # Multi-column widget layout

  # Content widths
  max_width_prose: "65ch"        # Optimal reading line length
  max_width_container: "1280px"  # Max content width (matches xl breakpoint)

  # Grid system
  grid_columns: 12               # 12-column grid
  grid_gutter: "24px"            # Space between columns
```

---

## Asset Handling

```yaml
assets:
  # How to handle referenced assets in mockups
  images_approach: "data_urls"   # Convert to base64 data URLs (self-contained)
  icons_approach: "inline_svg"   # Inline SVG (no external files)
  fonts_approach: "web_fonts"    # CDN links (Google Fonts, etc.) or data URLs

  # Common brand assets (paths relative to project root)
  logo_path: "assets/logo.png"
  logo_dark_path: "assets/logo-dark.png"
  favicon_path: "assets/favicon.ico"

  # Icon library (if using)
  icon_library: "heroicons"      # "heroicons", "font-awesome", "material", "custom"
  icon_style: "outline"          # "outline", "solid", "duotone"
```

---

## Interaction Patterns

```yaml
interactions:
  # Common interactive behaviors to demonstrate in mockups

  # Hover states
  hover_transition_duration: "200ms"
  hover_brightness_increase: "110%"   # For buttons/interactive elements

  # Active states
  active_scale: "0.98"                # Slight scale down on click

  # Transitions
  default_transition: "all 200ms ease"

  # Form validation
  show_inline_validation: true        # Show errors below fields
  validation_timing: "on_blur"        # "on_blur", "on_submit", "on_change"

  # Loading states
  show_loading_spinners: true
  show_skeleton_loaders: true         # For content loading
```

---

## Performance Considerations

```yaml
performance:
  # Image optimization
  max_image_width: "2400px"           # Max width for embedded images
  image_quality: "85"                 # JPEG quality (0-100)

  # Code size
  max_html_size: "500kb"              # Target max size for mockup file
  warn_at: "300kb"                    # Warn if file exceeds this

  # Asset limits
  max_embedded_images: 10             # Limit data URL embeds
  max_font_variants: 4                # Limit font weight/style variants
```

---

## Customization Instructions

**For teams using ai-toolkit:**

1. **Update design tokens** (colors, typography, spacing) to match your brand
2. **Set accessibility requirements** based on your compliance needs
3. **Define component patterns** as your design system matures
4. **Adjust breakpoints** if targeting specific devices
5. **Configure asset handling** based on your asset structure

**ui-ux-designer agent** reads this file for every mockup generation, ensuring consistency.

---

## Example CSS Variables Template

Generated mockups include these CSS variables:

```css
:root {
  /* Colors */
  --primary: #3b82f6;
  --secondary: #8b5cf6;
  --accent: #f59e0b;

  /* Typography */
  --font-family: Inter, system-ui, sans-serif;
  --font-size-base: 16px;
  --line-height: 1.5;

  /* Spacing */
  --space-unit: 8px;
  --space-sm: calc(var(--space-unit) * 2);  /* 16px */
  --space-md: calc(var(--space-unit) * 3);  /* 24px */
  --space-lg: calc(var(--space-unit) * 4);  /* 32px */

  /* Borders */
  --radius-md: 8px;
  --border-color: #e5e7eb;

  /* Shadows */
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);

  /* Breakpoints (for reference in media queries) */
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
}
```

---

## Technology-Specific Integration (Optional)

### Component Library Support

The `/ui-design` command can generate mockups using specific component libraries:

**Supported:**
- **shadcn/ui** - Request with `"using shadcn"` or `"with shadcn/ui"`
- **Chakra UI** - Request with `"using chakra"` or `"with chakra-ui"`
- **Material UI** - Request with `"using material-ui"` or `"with mui"`
- **Vanilla HTML/CSS** - Default (no library mentioned)

**Example:**
```bash
# Compare component libraries
/ui-design "login screen, show me shadcn and chakra versions"

# Generate files:
# - login-screen-v1-shadcn.html (shadcn styling + React code)
# - login-screen-v1-chakra.html (Chakra styling + React code)
```

### shadcn/ui Integration

When generating shadcn mockups, design tokens use shadcn-compatible format:

**Colors:** HSL format (shadcn convention)
```css
:root {
  --primary: 222.2 47.4% 11.2%;           /* HSL without hsl() wrapper */
  --primary-foreground: 210 40% 98%;     /* Text on primary background */
  --radius: 0.5rem;                       /* Border radius */
}
```

**Implementation code:** Embedded in HTML comments as copy-paste ready React code

**Example HTML comment block:**
```html
<!--
=== IMPLEMENTATION CODE ===

File: components/LoginForm.tsx
---
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"

export function LoginForm() {
  return (
    <>
      <Input type="email" placeholder="Email" />
      <Button>Login</Button>
    </>
  )
}
---
-->
```

### Design Token Compatibility

Design tokens are formatted for compatibility across tech stacks:

| Property | Vanilla | shadcn/Tailwind | Notes |
|----------|---------|-----------------|-------|
| Colors | Hex (`#3b82f6`) | HSL (`217 91 60`) | Converted automatically |
| Spacing | px (`24px`) | rem (`1.5rem`) | 8px base unit |
| Fonts | Sans-serif stack | Same | Universal |
| Breakpoints | px (`768px`) | Same | Mobile-first |

**Conversion happens automatically** when generating tech-specific mockups.

---

**Last updated:** 2025-11-03
**Version:** 1.1.0
**Next review:** As needed when design system evolves
