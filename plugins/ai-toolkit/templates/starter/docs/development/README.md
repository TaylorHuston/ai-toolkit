# Development Guidelines

This directory contains **project-specific development guidelines** that configure how AI agents and commands behave in your project.

## How Guidelines Work

Guidelines are **configuration files** (like package.json or tsconfig.json) that tell AI agents how your project works. They use YAML frontmatter for machine-readable settings and markdown for human-readable explanations.

Think of them like the ADR template:
- `/adr` reads `adr-template.md` to know what ADR sections to create
- `/implement` reads `testing-standards.md` to know your testing approach
- All agents read relevant guidelines to adapt to your project's patterns

## Template-Driven Configuration

Each guideline has 3 parts:

### 1. YAML Configuration (for AI agents)
```yaml
---
testing_framework: "vitest"
test_location: "tests/"
# Coverage target configured in development-loop.md
---
```

### 2. Project Decisions (for humans + AI)
```markdown
## Our Testing Philosophy
We prioritize E2E tests over unit tests...

## Examples
See tests/e2e/user-flow.test.ts for our testing pattern
```

### 3. Reference to Generic Knowledge
```markdown
For general testing best practices, Claude has extensive knowledge...
```

## Your Guidelines

### Core Guidelines (6 files)

1. **api-guidelines.md** - API patterns (REST/GraphQL/tRPC), structure, auth
2. **testing-standards.md** - Testing frameworks, coverage, strategy
3. **git-workflow.md** - Branching, commits, PRs, releases
4. **coding-standards.md** - Naming, formatting, file organization
5. **security-guidelines.md** - Auth, data protection, vulnerabilities
6. **architectural-principles.md** - Design philosophy (DRY, SOLID, etc.)

Each guideline starts with TBD placeholders and gets filled in as you make decisions.

## How Guidelines Evolve

### Phase 1: Initialization
After `/toolkit-init`, guidelines contain mostly TBD placeholders:
```yaml
---
api_pattern: "TBD"
testing_framework: "TBD"
---
```

### Phase 2: Architecture Decisions
When you run `/adr`, it updates guidelines:
```
User: "/adr Should we use REST or tRPC?"

/adr:
1. Analyzes options
2. Creates ADR-001-use-trpc.md
3. Updates api-guidelines.md:
   - Changes `api_pattern: "TBD"` → `api_pattern: "trpc"`
   - Adds project-specific patterns
```

### Phase 3: Implementation
As you build, agents read guidelines to match your patterns:
```
api-designer agent:
1. Reads api-guidelines.md
2. Sees `api_pattern: "trpc"`
3. Creates tRPC router in correct location
4. Follows existing project patterns
```

### Phase 4: Enrichment
Guidelines become living documentation:
```markdown
## Examples
- User API: src/server/api/routers/users.ts
- Auth flow: src/server/api/routers/auth.ts
```

## Customizing Guidelines

Guidelines are **yours to edit**. Customize them to fit your project:

### Add Project-Specific Rules
```markdown
## Our API Conventions
- All endpoints return standardized error format (see src/lib/errors.ts)
- Use Zod for validation (see src/lib/validators/)
- Rate limit: 100 req/min per user
```

### Update YAML Configuration
```yaml
---
api_pattern: "trpc"
api_location: "src/server/api/routers/"
authentication: "nextauth"
---
```

### Add Examples from Your Code
```markdown
## Examples
Good API router: src/server/api/routers/users.ts
Auth middleware: src/server/middleware/auth.ts
```

## How Commands Use Guidelines

Commands read guidelines before executing:

**`/implement TASK-001 1.1`**
1. Reads `testing-standards.md` → knows to use Vitest
2. Reads `coding-standards.md` → knows file naming conventions
3. Reads `api-guidelines.md` → knows API patterns (if building API)
4. Implements following your project's patterns

**`/quality`**
1. Reads `quality-standards.md` → knows your quality bar
2. Reads `security-guidelines.md` → checks security requirements
3. Reads `testing-standards.md` → verifies test coverage

**`/commit`**
1. Reads `git-workflow.md` → knows commit convention
2. Formats commit message accordingly

## Guideline Versioning

**Guidelines use date-based tracking, not version numbers.**

### Metadata Fields

Each guideline has three metadata fields:
```yaml
---
created: "2025-10-30"        # When guideline was first created
last_updated: "2025-11-06"   # When last significantly changed
status: "Active"             # Active, Optional, Deprecated
---
```

### When to Update `last_updated`

**DO update** when making:
- ✅ Structural changes (added/removed sections)
- ✅ Configuration changes (frontmatter updates)
- ✅ Workflow changes (changed steps/requirements)
- ✅ Behavior changes (how agents should interpret this)

**DON'T update** for:
- ❌ Typo fixes (too minor)
- ❌ Grammar improvements (cosmetic)
- ❌ Example additions (unless they change behavior)

### Why No Version Numbers?

Guidelines are **living documentation**, not APIs:
- They evolve continuously as your project matures
- Version numbers (1.0, 2.0) imply discrete releases that don't match reality
- `last_updated` date tells you freshness without version number overhead
- CHANGELOG.md tracks what changed (more useful than version numbers)

**Example workflow:**
```bash
# Make significant change to testing-standards.md
# Update last_updated: "2025-11-06"
# Add entry to CHANGELOG.md under "Documentation"
```

### Status Field

- **Active**: Used by commands/agents, kept current
- **Optional**: Template, fill in when needed
- **Deprecated**: No longer used, kept for reference

---

## Best Practices

### 1. Start Minimal
Don't fill in everything upfront. Let guidelines evolve:
- Init: Mostly TBDs
- First decision: Fill in one section
- As you build: Add examples and patterns

### 2. Be Specific
Don't write generic advice Claude already knows:
```markdown
❌ "Write meaningful variable names"
✅ "Component files: kebab-case (user-profile.tsx)"
✅ "See src/components/ui/ for our naming pattern"
```

### 3. Link to Your Code
Guidelines are most useful with concrete examples:
```markdown
## Good Test Example
See tests/api/users.test.ts - shows our:
- Test structure
- Mocking strategy
- Assertion style
```

### 4. Update When Patterns Change
If you change approach, update guidelines:
- Switching from Jest to Vitest? Update testing-standards.md
- New API pattern? Update api-guidelines.md
- AI and humans stay in sync

### 5. Use for Onboarding
New team members (human or AI) read guidelines to understand:
- Why we made certain choices
- Where things go in the codebase
- What patterns to follow

## Examples from This Project

The AI Toolkit itself follows these guidelines:
- **File naming**: kebab-case (testing-standards.md, not TestingStandards.md)
- **Documentation**: Three-tier structure (docs/project/, docs/development/, plugin docs)
- **Templates**: YAML frontmatter + markdown (spec.md, task.md, adr-template.md)
- **Commands**: Read templates/guidelines at runtime to adapt behavior

## Getting Help

**Filling in guidelines**:
```bash
/adr "testing strategy"     # Helps decide testing approach
/adr "API architecture"     # Helps choose API pattern
/adr "git workflow"         # Helps establish branching strategy
```

**Understanding patterns**:
Ask Claude questions like:
- "What should go in api-guidelines.md?"
- "How should I structure testing-standards.md?"
- "What's a good git workflow for our team size?"

Claude will provide guidance based on your project context.

---

**Remember**: Guidelines are configuration files that make AI adapt to YOUR project, not the other way around. Customize them to fit your workflow!
