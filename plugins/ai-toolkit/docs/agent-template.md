# Standardized Agent Template

**Based on**: subagent-best-practices.md research and audit findings
**Target**: 100-300 lines per agent

---

## YAML Frontmatter Structure

```yaml
---
name: agent-name
description: "**[INVOCATION TRIGGER].** [Core purpose in 1-2 sentences]. **Use [when/proactively/immediately]** [specific conditions]. [Focus areas]."
tools: Read, Write, Edit, MultiEdit, Bash, Grep, Glob, TodoWrite, [MCP tools as needed]
model: claude-sonnet-4-5  # or claude-opus-4-1 for critical agents, claude-3-5-haiku-latest for fast agents
color: [color-name]
coordination:
  hands_off_to: [agent1, agent2, agent3]
  receives_from: [agent1, agent2, agent3]
  parallel_with: [agent1, agent2, agent3]
---
```

### Invocation Trigger Patterns
**Keywords**: Comma separated list of keywords that might trigger this agane

Choose ONE based on agent role:

- **Mandatory review agents**: `"**MANDATORY for [context].**"`
  - Example: code-architect

- **Auto-invoked agents**: `"**AUTOMATICALLY INVOKED when [condition].**"`
  - Example: security-auditor, test-engineer, technical-writer

- **Proactive agents**: `"Use PROACTIVELY when [condition].**"`
  - Example: ui-ux-designer, performance-optimizer

- **Ad-hoc specialists**: `"[Domain] specialist. Auto-invoked for [specific tasks]."`
  - Example: data-analyst, migration-specialist, ai-llm-expert

### Tool Permission Guidelines

**Read-only agents** (reviewers, analyzers):
- `Read, Grep, Glob, Bash, TodoWrite`
- Add Serena for semantic analysis
- Example: code-reviewer, security-auditor, context-analyzer

**Implementation agents** (builders):
- `Read, Write, Edit, MultiEdit, Bash, Grep, Glob, TodoWrite`
- Add Serena for code understanding and insertion
- Add Context7 for framework documentation
- Example: frontend-specialist, backend-specialist

**Coordination agents** (orchestrators):
- `Read, Write, Edit, MultiEdit, Bash, Grep, Glob, TodoWrite, Task`
- Add Context7 for general documentation
- Example: project-manager

### Model Selection Guidelines

- **claude-sonnet-4-5** (default): Most agents
- **claude-opus-4-1**: Critical decision-making (security-auditor, brief-strategist, project-manager, ai-llm-expert)
- **claude-3-5-haiku-latest**: Fast analysis (context-analyzer)

### Coordination Block Guidelines

- **hands_off_to**: Agents this one delegates TO (downstream in workflow)
- **receives_from**: Agents that delegate TO this one (upstream in workflow)
- **parallel_with**: Agents that work alongside this one (concurrent execution)

---

## Content Structure

### 1. Purpose Section (Required)

```markdown
## Purpose

[1-2 sentence description of agent's primary responsibility and expertise]

**Development Workflow**: Read `docs/development/guidelines/development-loop.md` for [specific aspect relevant to this agent].

**Agent Coordination**: Read `docs/development/guidelines/agent-coordination.md` for governance patterns and escalation paths.

**[Domain] Guidelines**: Read `docs/development/guidelines/[domain]-guidelines.md` for project-specific [domain] standards.
```

**Guideline Reference Rules**:

1. **development-loop.md** - Include for ALL implementation and review agents
   - Skip for: brief-strategist, project-manager, ai-llm-expert (meta/orchestration agents)

2. **agent-coordination.md** - Include for agents that coordinate with multiple others
   - Include for: code-architect, security-auditor, database-specialist, code-reviewer, api-designer, frontend-specialist, backend-specialist, devops-engineer
   - Skip for: standalone specialists (data-analyst, refactoring-specialist, migration-specialist)

3. **Domain-specific guidelines** - Include only if relevant:
   - `security-guidelines.md` → security-auditor
   - `api-guidelines.md` → api-designer
   - `coding-standards.md` → code-reviewer
   - `documentation-standards.md` → technical-writer

### 2. Universal Rules
Rules that apply to all Agents

1. Read and respect the root CLAUDE.md for all actions.
2. When applicable, always read the latest WORKLOG entries for the given task before starting work to get up to speed.
3. When applicable, always write the results of your actions to the WORKLOG for the given task at the end of your session.

### 3. Invocation Triggers
Clear guidelines on WHEN to invoke this Agent

```markdown
## Invocation Triggers

**Use PROACTIVELY when**:
- [Condition 1]
- [Condition 2]
- [Condition 3]

**AUTOMATICALLY INVOKED when**:
- [Trigger 1]
- [Trigger 2]
- [Trigger 3]

### Context Keywords
- "[keyword1]", "[keyword2]", "[keyword3]"
- "[phrase1]", "[phrase2]"
```

### 4. Core Responsibilities (Required)

```markdown
## Core Responsibilities

### [Expertise Area 1]
- [Capability 1]
- [Capability 2]
- [Capability 3]

### [Expertise Area 2]
- [Capability 1]
- [Capability 2]
```

**Rules**:
- Keep to 3-5 expertise areas
- 3-5 capabilities per area
- Use parallel structure for readability

### 5. High-Level Workflow (Required)
This varies the most based on what the actual AGENT should be doing. Bais towards refencing the applicable guieline files, not hardcoding instructions.

```markdown
## Workflow

### 1. [Phase Name]

**[Sub-phase]**:
- [Step 1]
- [Step 2]
- [Step 3]

### 2. [Phase Name]

[Brief description of phase goals]

**Process**:
1. [Action 1]
2. [Action 2]
3. [Action 3]
```

**Rules**:
- 3-5 major phases maximum
- Each phase: 3-5 steps
- Action-oriented language (verbs: Analyze, Design, Implement, Validate)

### 6. MCP Tool Integration (Required for agents with MCP tools)

```markdown
## Tool Integration

### Serena (Semantic Code Analysis)
**When to use**:
- [Use case 1]
- [Use case 2]

**Key tools**:
- `mcp__serena__find_symbol` - [Purpose]
- `mcp__serena__find_referencing_symbols` - [Purpose]
- `mcp__serena__get_symbols_overview` - [Purpose]

### Context7 (Framework Documentation)
**When to use**:
- [Use case 1]
- [Use case 2]

**Pattern**:
Use `mcp__context7__get-library-docs` for:
- [Framework 1] patterns and best practices
- [Framework 2] specific implementations
- [Library 3] advanced features

### Gemini (Cross-Validation)
**For critical decisions**:
- [Decision type 1]
- [Decision type 2]

**Process**:
1. Primary analysis with full context
2. Cross-validate via `mcp__gemini-cli__prompt`
3. Synthesize perspectives or escalate disagreements
```

**Include only relevant MCP sections**:
- Serena: Agents doing code analysis/modification
- Context7: Agents needing framework docs
- Gemini: Agents making critical decisions (code-architect, security-auditor, database-specialist)

### 7. Best Practices (Optional but recommended)

```markdown
## Best Practices

### [Category 1]
- [Practice 1]
- [Practice 2]
- [Practice 3]

### [Category 2]
- [Practice 1]
- [Practice 2]

### [Category 3]
- [Practice 1]
- [Practice 2]
```

**Keep to**: 3-4 categories max, 3-5 practices per category

### 8. Handoff Protocols (Required)

```markdown
## Handoff Protocols

### To [Agent Name]
- [When to hand off]
- [What to provide]

### To [Agent Name]
- [When to hand off]
- [What to provide]

### From [Agent Name]
- [What to expect]
- [How to integrate input]
```

**Rules**:
- Should align with coordination YAML block
- Focus on 3-5 most common handoffs
- Keep descriptions to 1-2 bullets each

### 9. Success Metrics (Optional but recommended)

```markdown
## Success Metrics

### [Metric Category 1]
- **[Metric Name]**: [Description]
- **[Metric Name]**: [Description]

### [Metric Category 2]
- **[Metric Name]**: [Description]
- **[Metric Name]**: [Description]
```

**Include for**: Agents where quality is measurable (test-engineer, code-reviewer, performance-optimizer)

### 10. Example Usage (Optional)

```markdown
---

**Example Usage**: "[User request that would trigger this agent]"
```

**Rules**:
- Keep to 1 sentence
- Demonstrate typical invocation scenario

---

## Anti-Patterns to Avoid

### ❌ Don't Include

1. **script_integration blocks** - These reference non-existent files
   - ~~primary_scripts, supporting_scripts fields~~
   - Scripts should be documented in workflow sections, not YAML

2. **${CLAUDE_PLUGIN_ROOT} references in agent files** - Variable may not be defined in agent context
   - ✅ Valid in commands: `/toolkit-init` uses it to locate templates
   - ❌ Invalid in agents: Use relative paths instead: `docs/development/guidelines/`

3. **Verbose framework catalogs** - Use Context7 instead
   - ❌ 50+ lines of React patterns, Vue API details
   - ✅ "Use Context7 for React hooks, lifecycle, performance patterns"

4. **Over-detailed implementation steps** - Keep high-level
   - ❌ Step-by-step code implementation tutorials
   - ✅ High-level workflow phases with key decision points

5. **Redundant coordination information** - Don't duplicate YAML
   - ❌ Listing all hands_off_to agents in prose
   - ✅ Describe handoff conditions and protocols

### ✅ Do Include

1. **Action-oriented descriptions** with clear triggers
2. **Guideline references** (development-loop, agent-coordination, domain-specific)
3. **MCP tool integration** patterns (Serena, Context7, Gemini)
4. **Coordination protocols** explaining when/how to hand off
5. **Context7 references** for framework-specific knowledge

---

## Checklist Before Publishing

- [ ] YAML frontmatter complete (name, description, tools, model, color, coordination)
- [ ] Description has clear invocation trigger (MANDATORY/AUTOMATICALLY/PROACTIVELY)
- [ ] Purpose section includes relevant guideline references
- [ ] Core responsibilities clearly defined (3-5 expertise areas)
- [ ] Workflow is high-level (3-5 phases, not implementation details)
- [ ] MCP tool integration documented (if agent uses Serena/Context7/Gemini)
- [ ] Handoff protocols align with coordination YAML
- [ ] No script_integration blocks
- [ ] No ${CLAUDE_PLUGIN_ROOT} references
- [ ] No verbose framework catalogs (use Context7 instead)
- [ ] Agent length: 100-300 lines (up to 350 for complex agents)
- [ ] Single responsibility clearly maintained
- [ ] Tool permissions appropriate for role (read-only vs write)

---

## Length Guidelines

**Target**: 100-300 lines total (including YAML frontmatter)

**Acceptable variations**:
- **Simple specialists**: 100-150 lines (data-analyst, refactoring-specialist)
- **Standard agents**: 150-250 lines (frontend-specialist, backend-specialist)
- **Complex coordinators**: 250-350 lines (code-architect, project-manager, security-auditor)

**If exceeding 350 lines**: Consider splitting expertise areas or reducing implementation details. Focus on high-level patterns, delegate specifics to Context7.

---

## Example Application

See refactored agents in `plugins/ai-toolkit/agents/` for reference implementations following this template.
