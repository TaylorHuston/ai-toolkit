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

### UI Design Guidelines (Required)

The **source of truth** for design tokens, approved designs, and UI standards:
```
docs/development/conventions/ui-design-guidelines.md
```

Contains:
- **Approved Designs**: Approved screen and component designs (with version references)
- **Design Tokens**: Colors, typography, spacing, borders
- **Responsive Breakpoints**: Mobile-first approach
- **Accessibility Requirements**: WCAG AA compliance
- **Component and Layout Patterns**

**When to update**: After approving designs via `/ui-design`, when changing design standards, or when implementing new patterns.

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

**Tactical design changes** (component specs, color values, spacing) should be updated directly in `ui-design-guidelines.md` by AI assistants as they implement UI.

## Documentation Structure

- **`ui-designs/` directory**: AI-generated HTML mockups (via `/ui-design`)
- **`design-assets/` directory**: Static brand assets (this folder)
- **`ui-design-guidelines.md`**: Design tokens, approved designs, and configuration
- **`writing-style.md`**: Content guidelines (optional)
- **Component library**: Implementation code (in your codebase)

This separation ensures design exploration, documentation, and assets are properly organized.
