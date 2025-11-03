# Design Assets

This directory contains static design resources and brand assets.

## Directory Structure

### `/assets/`
Brand assets and design resources
- Logo files (SVG, PNG)
- Icon sets
- Typography specimens
- Brand guidelines PDFs
- Canva templates

## Usage Guidelines

**File Naming**: Use descriptive, lowercase-kebab-case names
- Good: `logo-primary-dark.svg`
- Bad: `Logo_Final_v3.svg`

**Organization**: Group related assets in subdirectories
- Example: `assets/logos/`, `assets/icons/`, `assets/fonts/`

**Formats**:
- Prefer vector formats (SVG) for scalability
- Use PNG for raster assets with transparency
- Include source files when possible (AI, Sketch, Figma)

## UI Design Workflow

For **HTML mockups** and **interactive design exploration**, use:
```bash
/ui-design "your design request"
```

This creates AI-generated HTML mockups in:
```
docs/project/ui-designs/
```

See `ui-designs/README.md` for the full design mockup workflow.

## Design Documentation

### Design Overview (Required)

The **source of truth** for approved UI designs and extracted patterns:
```
docs/project/design-overview.md
```

Tracks:
- Approved screen designs (with version references)
- Approved component patterns
- Extracted design tokens (colors, typography, spacing)
- Layout patterns and responsive behavior

**When to update**: After approving designs via `/ui-design`, or when implementing new patterns.

### UI Design Guidelines (Required)

Configuration for AI-generated mockups:
```
docs/development/guidelines/ui-design-guidelines.md
```

Defines:
- Design tokens (colors, typography, spacing, borders)
- Responsive breakpoints
- Accessibility requirements (WCAG AA)
- Component patterns and layout patterns

**When to update**: When establishing or changing design standards.

### Writing & Content (Optional)

For projects with content/copywriting needs:
```
docs/project/writing-style.md
```

Defines:
- Voice and tone guidelines
- Microcopy patterns (buttons, errors, empty states)
- Capitalization and formatting rules
- Grammar and usage conventions

**When to use**: Projects with user-facing content, marketing copy, or specific brand voice requirements. Many technical projects won't need this.

## Strategic Design Decisions

For foundational design decisions that need documentation and rationale, use:
```bash
/adr "design system framework selection"
```

Examples of strategic design decisions:
- Choosing a design framework (Tailwind CSS, Material UI, custom)
- Selecting accessibility standards or tooling
- Design token structure and naming conventions
- Component library architecture

These decisions are:
- Stored as ADRs in `docs/project/adrs/`
- Documented with context, alternatives, and consequences
- Used to update `ui-design-guidelines.md` with the chosen approach

**Tactical design changes** (component specs, color values, spacing) should be updated directly in `ui-design-guidelines.md` and `design-overview.md` by AI assistants as they implement UI.

## Documentation Structure

- **`ui-designs/` directory**: AI-generated HTML mockups (via `/ui-design`)
- **`design-overview.md`**: Approved designs and extracted patterns
- **`design-assets/` directory**: Static brand assets (this folder)
- **`ui-design-guidelines.md`**: Design tokens and configuration
- **`writing-style.md`**: Content guidelines (optional)
- **Component library**: Implementation code (in your codebase)

This separation ensures design exploration, documentation, and assets are properly organized.
