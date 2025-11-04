# Claude Code Subagent Best Practices Guide

**Official Anthropic documentation and production implementations strongly favor narrow, specialized subagents over broad generalist roles.** While no explicit guidance exists on markdown file length, the single-responsibility principle emerges as the most critical design pattern, with typical files ranging 50-200 lines and prioritizing clarity over brevity.

## The specificity question: When narrow beats broad

Official Anthropic guidance and production implementations at companies like PubNub provide a clear answer to your granularity question: **create framework-specific and platform-specific subagents** rather than broad categorical roles.

**For your frontend scenario**, the recommendation is unambiguous: separate `react-specialist`, `nextjs-developer`, and `vue-expert` subagents outperform a generic `frontend-developer`. Real-world repositories with 100+ production subagents consistently follow this pattern. The official documentation examples emphasize task-specific agents like `code-reviewer` and `test-runner` rather than broad roles like `developer` or `programmer`.

**For your database scenario**, apply the same principle: create distinct subagents for `supabase-specialist`, `firebase-expert`, and `convex-developer` rather than a single `database-designer`. Each platform has unique patterns, APIs, and best practices that warrant specialized expertise. A Supabase agent would focus on PostgreSQL-based patterns, RLS policies, and Edge Functions, while a Firebase agent would emphasize Firestore security rules, Cloud Functions, and real-time listeners—fundamentally different mental models.

The underlying principle is **single-responsibility architecture**. Official sources emphasize that each subagent should have "one clear goal, input, output, and handoff rule." This doesn't mean creating overly narrow agents—avoid splitting `react-hooks-expert` and `react-context-expert` separately when a comprehensive `react-specialist` covering all React patterns makes more sense. The sweet spot is **framework or platform-level granularity**, where an agent handles 80% of common tasks within its domain.

## File length: Practical guidance from real implementations

No authoritative source specifies optimal markdown file length, but analysis of production subagents across multiple repositories reveals consistent patterns. **Most effective subagents fall between 50-200 lines** including YAML frontmatter, with simple agents as short as 20 lines and complex specialists extending to 300+ lines when detailed workflows justify the length.

The guiding principle is **complete but focused**. Since subagents start with a clean slate each invocation (no memory between uses), every file must be self-contained with all necessary context. However, token efficiency matters—include specific instructions, examples, and constraints relevant to the role while eliminating unnecessary verbosity. A code reviewer might need 150 lines to cover security checks, performance patterns, and best practices across multiple languages, while a test runner accomplishes its mission in 75 lines.

Structure matters more than length. The official format combines YAML frontmatter with a system prompt that typically includes: role definition, context discovery instructions, step-by-step workflows, output specifications, and relevant constraints. **Front-load the most critical information**—subagents should understand their primary objective within the first paragraph, then dive into specifics.

## Essential architectural patterns for effective subagents

**Tool permission hygiene** represents the most critical security practice. Official documentation emphasizes "scope tools per agent" with explicit whitelisting. Read-heavy agents like product managers and architects receive only `Read, Grep, Glob` access, while implementation agents get `Edit, Write, Bash` permissions. If you omit the `tools` field, the subagent inherits ALL available tools including MCP servers—be intentional about this decision. The PubNub production implementation demonstrates progressive permission escalation: PM agents (read-only) → Architect agents (read + MCP) → Implementation agents (full write access).

**Context isolation** provides the architectural benefit that makes subagents powerful. Each agent operates in its own context window, preventing pollution of the main conversation and preserving context for longer sessions. When the main agent needs extensive research, delegating to a subagent allows thorough investigation with only relevant findings returned. This architectural choice does introduce latency since subagents must gather context from scratch each time, reinforcing why self-contained prompts matter.

**Action-oriented descriptions** determine whether Claude automatically selects your subagent at the right moment. Make description fields specific about when to invoke the agent, not just what it does. Compare these examples from official documentation: ✗ "Code reviewer" versus ✅ "Expert code review specialist. Proactively reviews code for quality, security, and maintainability. **Use immediately after writing or modifying code.**" Include trigger phrases like "MUST BE USED for," "Use PROACTIVELY when," and "Use immediately after" to help Claude's automatic delegation system.

**Start with Claude-generated agents** using the `/agents` command—this is the official recommendation. Let Claude create the initial structure, then iterate to customize for your specific needs. This provides the best foundation and ensures proper formatting. Version control all project-level subagents in `.claude/agents/` and commit to git so your team automatically inherits agents when cloning the repository.

## Production-tested patterns: Three-agent pipeline

The most comprehensive production implementation comes from PubNub's engineering team, demonstrating a three-stage pipeline with clear handoff rules:

**Stage 1: pm-spec** reads enhancement requests, writes specifications, asks clarifying questions. Tools: `Read, Grep, Glob` only. Sets status `READY_FOR_ARCH` when complete.

**Stage 2: architect-review** validates design against platform constraints, produces Architecture Decision Records (ADRs), ensures alignment with system capabilities. Tools: `Read, Grep, Glob` plus MCP servers for current documentation. Sets status `READY_FOR_BUILD`.

**Stage 3: implementer-tester** implements code, runs tests (unit and UI), updates documentation. Tools: Full `Read, Write, Edit, Bash` access. Sets status `DONE`.

Each agent has distinct input/output boundaries, single responsibility, and explicit handoff rules. Hooks register `SubagentStop` events to automatically suggest the next step in the workflow, keeping humans in the loop for approval while eliminating manual prompting overhead. This pattern demonstrates how narrow scopes with clear transitions outperform broad "full-stack developer" agents.

## Well-scoped examples versus anti-patterns

**Effective subagent scoping** from official documentation and production repositories:

The `code-reviewer` agent from Anthropic documentation exemplifies proper scope: description specifies "Use immediately after writing or modifying code," tools limited to `Read, Grep, Glob, Bash`, system prompt details a four-step workflow (run git diff, focus on modified files, check security vulnerabilities, provide actionable feedback). This agent has one job—reviewing code—with clear activation criteria and appropriate permissions.

A `security-auditor` from production repositories demonstrates domain specialization: focuses exclusively on vulnerability detection (authentication flaws, SQL injection, XSS, secrets in code), uses higher-capability model (opus) for critical security work, provides structured output (severity rating, proof of concept, remediation steps), maintains read-only access preventing accidental modifications during security scans.

**Anti-patterns to avoid**:

Overly broad roles like `frontend-developer` or `backend-engineer` lack the specificity needed for automatic delegation and dilute expertise across too many domains. Claude can't reliably determine when to invoke a generic developer versus specialized alternatives.

Overly narrow fragmentation like separate `react-hooks-expert`, `react-context-expert`, and `react-routing-expert` agents creates unnecessary overhead and forces rigid workflows. A single `react-specialist` covering all React patterns serves better.

**The contrarian perspective** from practitioners managing "several billion tokens per month" warns against over-relying on custom subagents that gatekeep context from the main agent. Consider letting the main agent use the Task() feature to spawn clones with full CLAUDE.md context for dynamic delegation decisions. Custom subagents make most sense for strict permission boundaries, token-heavy research with clear summarization, team-wide standardization, and security-sensitive operations requiring tool restrictions.

## Framework for your specific decisions

**For frontend specialization**: Create separate `react-specialist`, `nextjs-developer`, `vue-expert`, and `angular-specialist` subagents. Each framework has distinct patterns—React's hooks and composition, Next.js's server components and App Router conventions, Vue's Composition API and reactivity system. A React specialist provides expertise on component lifecycle, state management patterns, performance optimization with useMemo/useCallback, and React-specific testing approaches. A Next.js specialist additionally understands file-based routing, Server Actions, middleware, and deployment to Vercel. These are complementary specializations, not redundant ones.

**For database/BaaS platforms**: Create platform-specific subagents. A `supabase-specialist` focuses on PostgreSQL patterns, row-level security policies, Edge Functions, real-time subscriptions, and storage buckets. A `firebase-expert` emphasizes Firestore document modeling, security rules language, Cloud Functions deployment, and Firebase Authentication. A `convex-developer` understands reactive queries, server functions in TypeScript, and Convex's consistency model. Each platform represents a distinct development paradigm worthy of specialized expertise.

**For role-based agents**: Create task-specific rather than role-based agents. Instead of `senior-engineer`, create `code-reviewer`, `performance-optimizer`, `security-auditor`, and `test-runner`. Instead of `technical-writer`, create `api-documenter`, `readme-generator`, and `changelog-compiler`. Task-specificity enables better automatic delegation and clearer success criteria.

## Implementation checklist

Start with 3-5 core agents covering your most common workflows—typically a reviewer, tester, and implementer pattern. Use the official `/agents` command to generate initial versions, then customize based on your team's specific patterns and constraints. Document usage in CLAUDE.md explaining when to invoke each agent and how they fit into your development workflow.

Explicitly scope tools for each agent—grant minimum necessary permissions and whitelist tools deliberately. Read-only agents receive `Read, Grep, Glob`; implementation agents add `Write, Edit, Bash`; testing agents may need additional tool access for test frameworks. Never rely on implicit inheritance of all tools unless specifically intended.

Version control everything in `.claude/agents/` for team sharing. Treat subagent configurations as production code—validate with proper tools, make hooks idempotent, and maintain auditable history of refinements. Test both automatic delegation (where Claude chooses based on description) and explicit invocation (where you manually specify the subagent).

Consider MCP server integration to ground agents in current documentation. Connect to real testing frameworks via Playwright MCP, domain-specific knowledge bases, or internal documentation systems. This combination of specialized subagents with rich context sources creates the most capable development environment.

Iterate based on actual usage patterns. Start narrow and expand scope only when repeated similar tasks reveal the need for broader coverage. Monitor which agents get invoked most frequently, where handoffs break down, and what permissions prove insufficient. The best subagent architectures emerge from real-world usage, not upfront speculation.

## Conclusion: Specialized expertise over general capability

The research reveals unanimous consensus: **narrow, specialized subagents with single responsibilities dramatically outperform broad generalist agents**. For your specific questions, create framework-specific frontend agents and platform-specific database agents rather than categorical roles. Keep files focused but complete (typically 50-200 lines), enforce strict tool permissions, and start with Claude-generated templates you refine through iteration.

The most surprising finding comes from experienced practitioners who caution against over-specialization that gatekeeps context—use custom subagents primarily for permission boundaries, team standardization, and security-sensitive operations, while considering the Task() feature for dynamic workflows where the main agent's full context provides value. This nuanced view suggests the optimal architecture balances specialized expertise with contextual flexibility, achieved through framework-level granularity rather than either extreme of overly broad or overly narrow scoping.