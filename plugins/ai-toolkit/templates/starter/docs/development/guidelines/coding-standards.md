---
# === Metadata ===
template_type: "guideline"
created: "2025-10-30"
last_updated: "2025-11-06"
status: "Active"
enforcement: "automated"
target_audience: ["AI Assistants", "Development Team"]
description: "Code style, naming conventions, and file organization standards - enforced during implementation"

# === Coding Configuration (Machine-readable for AI agents) ===
language: "TBD"                # javascript, typescript, python, go, etc.
file_naming: "kebab-case"      # kebab-case, camelCase, snake_case, PascalCase
directory_structure: "TBD"      # feature-based, layer-based, etc.
formatter: "TBD"               # prettier, black, gofmt, etc.
linter: "TBD"                  # eslint, pylint, golangci-lint, etc.
max_line_length: 100
indent_style: "spaces"         # spaces or tabs
indent_size: 2
---

# Coding Standards

**Status:** Active - Enforced proactively during implementation

**Referenced by Commands:**
- `/implement` - Implementation agents read before code generation
- `/quality` - Quality assessment validates adherence

**Referenced by Agents:**
- frontend-specialist, backend-specialist, etc. - Read before implementation
- code-reviewer - Validates compliance during review

## Quick Reference

This guideline defines our code style, naming conventions, and file organization. Update as you establish project conventions.

**Enforcement:** Implementation agents consult this guideline BEFORE writing code. code-reviewer validates compliance during review.

---

## Getting Started

**When starting your project, configure these settings:**

**Frontmatter Configuration:**
- [ ] Set `language`, `formatter`, `linter` in YAML frontmatter
- [ ] Set `file_naming`, `directory_structure`, indentation preferences

**Define in Sections Below:**
- [ ] Naming conventions (variables, functions, classes, constants, types)
- [ ] Directory structure approach (feature-based, layer-based, module-based)
- [ ] Import ordering (external → internal → relative → types → styles)
- [ ] Function guidelines (max parameters, comment policy)
- [ ] Link to formatter/linter config files (if exists)
- [ ] Add code examples from codebase (optional, add as patterns establish)

**Tip:** Complete incrementally as you establish patterns. Start with frontmatter, then add conventions as you go.

---

## Core Principals

- DRY
- SOLID
- YAGNI
- KISS
- Adhere to known good patterns and documented examples, don't try and reinvent the wheel

## Language & Tooling

**Primary Language**: TBD → Update when decided

- **Version**: TBD
- **Type System**: TBD (TypeScript, static typing, dynamic typing)
- **Runtime**: TBD (Node.js, Deno, Python, etc.)

### Code Formatting

- **Formatter**: TBD (Prettier, Black, gofmt, etc.)
- **Config**: TBD - Add link to formatter config file
- **Auto-format on save**: TBD (recommended: yes)

### Linting

- **Linter**: TBD (ESLint, Pylint, golangci-lint, etc.)
- **Config**: TBD - Add link to linter config file
- **Rules**: TBD (strict, recommended, custom)

## Naming Conventions

### Files & Directories

- **Files**: kebab-case (already configured)
  - Components: `user-profile.tsx`
  - Utils: `format-date.ts`
  - Tests: `user-profile.test.ts`

- **Directories**: kebab-case
  - Features: `user-management/`
  - Components: `ui-components/`

### Code Identifiers

```
TBD - Define naming for variables, functions, classes, etc.

Examples:
- Variables: camelCase (userData, isLoading)
- Functions: camelCase (getUserData, formatDate)
- Classes: PascalCase (UserProfile, ApiClient)
- Constants: UPPER_SNAKE_CASE (MAX_RETRIES, API_URL)
- Types/Interfaces: PascalCase (User, ApiResponse)
```

## File Organization

### Directory Structure

```
TBD - Define after initial project structure is established

Examples:
- Feature-based: src/features/users/
- Layer-based: src/controllers/, src/services/, src/models/
- Module-based: src/modules/user/
```

### File Size Guidelines

- **Max lines**: TBD (recommended: 200-300)
- **Single Responsibility**: Each file should have one clear purpose
- **Splitting**: TBD (when to split files)

### Import Organization

```
TBD - Define import ordering

Example:
1. External dependencies
2. Internal modules
3. Relative imports
4. Types/interfaces
5. Styles/assets
```

## Code Style

### Line Length

- **Max**: 100 characters (already configured)
- **Wrap**: TBD (how to handle long lines)

### Indentation

- **Style**: spaces (already configured)
- **Size**: 2 spaces (already configured)

### Comments

- **When to comment**: TBD
  - Always: Complex algorithms, non-obvious logic
  - Rarely: Self-explanatory code
  - Never: Obvious code, commented-out code

- **Style**: TBD
  - JSDoc for functions?
  - Inline for clarification?

### Function Guidelines

- **Max parameters**: TBD (recommended: 3-4)
- **Max complexity**: TBD
- **Naming**: TBD (verb-first for actions, noun-first for getters)

## Examples

This section will be populated with examples from the codebase showing good practices.

### Well-Structured File
- TBD - Add link to exemplar file

### Good Function Example
- TBD - Add link to well-written function

### Import Organization Example
- TBD - Add example from codebase

---

## Common Violations and Fixes

**Purpose:** Clear examples reduce ambiguity and help AI agents apply standards correctly.

### File Naming

❌ **Wrong:** `UserProfile.tsx` (PascalCase)
✅ **Correct:** `user-profile.tsx` (kebab-case)

❌ **Wrong:** `getUserData.ts` (camelCase)
✅ **Correct:** `get-user-data.ts` (kebab-case)

❌ **Wrong:** `user_service.py` (snake_case when kebab-case configured)
✅ **Correct:** `user-service.py` (kebab-case)

### Identifier Naming (update examples after Phase 2)

❌ **Wrong:** `const UserName = "john"` (PascalCase for variable)
✅ **Correct:** `const userName = "john"` (camelCase for variable)

❌ **Wrong:** `function ApiCall()` (PascalCase for function)
✅ **Correct:** `function apiCall()` (camelCase for function)

❌ **Wrong:** `const max_retries = 3` (snake_case for constant)
✅ **Correct:** `const MAX_RETRIES = 3` (UPPER_SNAKE_CASE for constant)

### Import Organization (update after Phase 3)

❌ **Wrong order:**
```javascript
import './styles.css'
import { formatDate } from '../utils'
import React from 'react'
import type { User } from './types'
```

✅ **Correct order:**
```javascript
// 1. External dependencies
import React from 'react'

// 2. Internal modules
import { formatDate } from '../utils'

// 3. Relative imports
import { UserProfile } from './user-profile'

// 4. Types
import type { User } from './types'

// 5. Styles
import './styles.css'
```

### Line Length

❌ **Wrong:** `const result = someFunction(param1, param2, param3, param4, param5, param6, param7, param8, param9);` (132 chars)

✅ **Correct:**
```javascript
const result = someFunction(
  param1, param2, param3,
  param4, param5, param6,
  param7, param8, param9
);
```

### Function Parameters (update after Phase 4)

❌ **Wrong:** Too many parameters
```javascript
function createUser(name, email, age, address, phone, role, department, manager) {
  // 8 parameters is too many
}
```

✅ **Correct:** Use object parameter
```javascript
function createUser({ name, email, age, address, phone, role, department, manager }) {
  // Single object parameter, much cleaner
}
```

---

## Auto-Detection from Config Files

**If you have existing config files, AI agents can extract standards automatically:**

### From `.prettierrc` / `prettier.config.js`

AI can auto-detect:
- `printWidth` → `max_line_length`
- `useTabs` → `indent_style` (tabs vs spaces)
- `tabWidth` → `indent_size`
- `singleQuote` / `quoteProps` → quote style preferences

### From `.eslintrc` / `eslint.config.js`

AI can auto-detect:
- `naming-convention` rule → variable/function/class naming patterns
- `max-params` rule → max function parameters
- `complexity` rule → cyclomatic complexity limits
- `max-lines` / `max-lines-per-function` → file/function size limits

### From `tsconfig.json` / `jsconfig.json`

AI can auto-detect:
- `strict` mode configuration
- `paths` → import alias configuration
- `moduleResolution` → import style

### From `pyproject.toml` / `setup.cfg` (Python)

AI can auto-detect:
- `[tool.black]` → line length, target versions
- `[tool.pylint]` → max line length, naming conventions
- `[tool.mypy]` → type checking configuration

**Recommendation:** Keep coding-standards.md in sync with config files as SINGLE SOURCE OF TRUTH. When config files exist, reference them here and extract their values into the frontmatter for AI consumption.

---

## Automated Quality Checks

**These are auto-validated by implementation agents and code-reviewer:**

### File-Level Checks (Enforced Before Phase Complete)

- [ ] **File name** matches convention (kebab-case by default)
- [ ] **File size** within limits (if max_file_size configured)
- [ ] **Imports organized** according to defined order
- [ ] **No commented-out code** (unless justified with explanation)
- [ ] **No console.log** in production code (unless logger-wrapped)

### Code-Level Checks (Enforced Before Phase Complete)

- [ ] **Identifiers follow naming** conventions (variables, functions, classes, constants)
- [ ] **Line length** within configured limit (100 chars by default)
- [ ] **Indentation consistent** (2 spaces by default)
- [ ] **Functions have** <= max parameters (if configured)
- [ ] **No magic numbers** (extract to named constants)

### Style Checks (Enforced Before Code Review)

- [ ] **Formatter passes** (prettier/black/gofmt runs clean)
- [ ] **Linter passes** with zero warnings (or documented exceptions)
- [ ] **Type checking passes** (if using TypeScript/mypy/etc.)
- [ ] **No duplicate code** detected (DRY principle)

### Enforcement Protocol

**During Implementation** (`/implement`):
1. Implementation agent reads coding-standards.md before writing code
2. Agent applies standards proactively while coding
3. Agent self-validates before marking phase complete
4. Auto-detectable violations BLOCK phase completion

**During Code Review** (code-reviewer agent):
1. Validates all automated checks passed
2. Flags style violations missed during implementation
3. Checks for consistency across codebase
4. BLOCKS approval if critical violations found

**Rationale for "Why?"**:
- Simple decisions (naming, formatting) → WORKLOG entry with brief note
- Complex decisions (architecture, patterns) → ADR via `/adr`
- Agent documents WHY deviations were necessary (if justified)

---

## General Coding Knowledge

For coding best practices, Claude has extensive knowledge of:
- Clean Code principles (naming, functions, comments)
- SOLID principles (single responsibility, open-closed, etc.)
- Design patterns (factory, strategy, observer, etc.)
- Code smells and refactoring techniques
- Language-specific idioms and conventions

Ask questions like "What's the best way to structure [X]?" and Claude will provide guidance based on industry standards and your chosen language/framework.
