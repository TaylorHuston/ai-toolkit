# AI Agent Index and Directory

**Total Agents**: 21 | **Coverage**: Complete development workflow

Comprehensive catalog of specialized AI agents optimized for modern development workflows.

## Understanding the Agent System

AI agents are specialized experts that automatically activate based on your work. Instead of generic AI responses, you get domain-specific expertise tailored to specific tasks (frontend development, security audits, database design, API architecture, etc.).

### How Agents Work

**Automatic Activation**: When you use commands like `/implement` or `/adr`, the appropriate agents activate based on the task. For example, `/implement TASK-001 1.1` might activate the frontend-specialist and test-engineer agents.

**Direct Invocation**: You can also invoke specific agents directly when you need specialized guidance: "Use the security-auditor agent to review this authentication flow."

**Agent Coordination**: Commands orchestrate agents, and agents communicate through WORKLOG.md entries. The `/implement` command selects specialists based on phase domain, invokes them with context, and manages the quality feedback loop. Each agent reads WORKLOG.md before starting work to understand what's been accomplished, then writes a WORKLOG.md entry after completing their phase. For example, during `/implement TASK-001 1.2`, the backend-specialist reads WORKLOG.md to see what the database-specialist accomplished in phase 1.1, implements the current phase, then documents their work for the next phase/agent.

**Coordination metadata** in agent files (`hands_off_to`, `receives_from`, `parallel_with`) is **documentation only** - it helps humans understand typical agent relationships but is not read programmatically. Commands make orchestration decisions based on task requirements.

### The Value

- **Domain Expertise**: Each agent has deep knowledge in their specialty
- **Consistent Quality**: Agents follow domain-specific best practices
- **Efficient Workflows**: Automatic activation means you don't have to think about which expert to consult
- **Coordinated Work**: Complex tasks get multi-agent collaboration without manual coordination

## Agent Classification System

### By Domain Expertise

#### **Architecture & Planning**

- **[brief-strategist](../agents/brief-strategist.md)** - Product brief and strategic planning

  - _Capabilities_: Brief creation, strategic analysis, business model design, success metrics
  - _Best For_: Product brief development, strategic decisions, market positioning
  - _Model_: opus | _Color_: purple | _Auto-Invoked_: Brief and strategic planning tasks

- **[code-architect](../agents/code-architect.md)** - System design and long-term architecture

  - _Capabilities_: System architecture, feature-level design patterns, API design
  - _Best For_: Feature architecture decisions, implementation patterns, technical design
  - _Model_: opus | _Color_: purple | _Auto-Invoked_: Feature architectural tasks

- **[project-manager](../agents/project-manager.md)** - Project coordination and orchestration

  - _Capabilities_: Multi-agent coordination, task orchestration, resource allocation
  - _Best For_: Complex project coordination, multi-phase implementations, team coordination
  - _Model_: opus | _Color_: blue | _Auto-Invoked_: Multi-domain tasks

- **[context-analyzer](../agents/context-analyzer.md)** - Project context analysis and investigation
  - _Capabilities_: Root cause analysis, systematic investigation, evidence-based reasoning
  - _Best For_: Bug investigation, system analysis, problem diagnosis
  - _Model_: sonnet | _Color_: yellow | _Auto-Invoked_: Investigation tasks

- **[ai-llm-expert](../agents/ai-llm-expert.md)** - AI/LLM architecture, implementation, and optimization
  - _Capabilities_: LLM architecture design, prompt engineering, context management, AI integration patterns
  - _Best For_: AI/ML decision analysis, LLM implementation guidance, AI architecture optimization
  - _Model_: opus | _Color_: green | _Auto-Invoked_: AI/ML architecture decisions

#### **Cloud Platform Experts**

- **[aws-expert](../agents/aws-expert.md)** - AWS cloud architecture and implementation
  - _Capabilities_: AWS service selection, architecture design, cost optimization, security best practices, Well-Architected Framework
  - _Best For_: AWS solution design, cloud migration to AWS, multi-cloud comparison, AWS cost optimization
  - _Model_: opus | _Color_: orange | _Auto-Invoked_: AWS architecture and implementation tasks

- **[azure-expert](../agents/azure-expert.md)** - Azure cloud architecture and implementation
  - _Capabilities_: Azure service selection, architecture design, cost optimization, Microsoft ecosystem integration, Azure Well-Architected Framework
  - _Best For_: Azure solution design, hybrid cloud scenarios, Microsoft ecosystem integration, Azure cost optimization
  - _Model_: opus | _Color_: blue | _Auto-Invoked_: Azure architecture and implementation tasks

- **[gcp-expert](../agents/gcp-expert.md)** - Google Cloud architecture and implementation
  - _Capabilities_: GCP service selection, architecture design, data analytics (BigQuery), AI/ML (Vertex AI), Kubernetes (GKE), cost optimization
  - _Best For_: GCP solution design, data-intensive workloads, AI/ML on GCP, Kubernetes-native applications
  - _Model_: opus | _Color_: green | _Auto-Invoked_: GCP architecture and implementation tasks

#### **Design & User Experience**

- **[ui-ux-designer](../agents/ui-ux-designer.md)** - UI/UX design specialist for strategic and tactical design decisions
  - _Capabilities_: Visual design, color theory, typography, accessibility (WCAG), design systems, Figma/Sketch, user flows
  - _Best For_: Design decisions, mockups, color schemes, accessibility planning, design system architecture
  - _Model_: sonnet | _Color_: pink | _Auto-Invoked_: Design decision requests

#### **Development & Implementation**

- **[frontend-specialist](../agents/frontend-specialist.md)** - UI/UX focused development

  - _Capabilities_: React/Vue/Angular, responsive design, accessibility, Core Web Vitals
  - _Best For_: Component development, user interface optimization, performance
  - _Model_: sonnet | _Color_: blue | _Auto-Invoked_: UI/UX development tasks

- **[backend-specialist](../agents/backend-specialist.md)** - Server-side implementation and business logic

  - _Capabilities_: Server frameworks, business logic, authentication, real-time features
  - _Best For_: API implementation, business logic, authentication, background processing
  - _Model_: sonnet | _Color_: green | _Auto-Invoked_: Server-side implementation tasks

- **[database-specialist](../agents/database-specialist.md)** - Database design and optimization

  - _Capabilities_: Schema design, query optimization, performance tuning, migrations
  - _Best For_: Database architecture, query performance, data modeling
  - _Model_: sonnet | _Color_: magenta | _Auto-Invoked_: All database work

- **[api-designer](../agents/api-designer.md)** - API design and endpoint architecture
  - _Capabilities_: REST/GraphQL design, API contracts, data validation, error handling
  - _Best For_: API architecture, endpoint design, service contracts
  - _Model_: sonnet | _Color_: orange | _Auto-Invoked_: API development tasks

#### **Quality & Testing**

- **[test-engineer](../agents/test-engineer.md)** - Comprehensive test creation and strategy

  - _Capabilities_: TDD/BDD workflows, unit/integration/E2E tests, test automation
  - _Best For_: Test strategy development, test creation, quality assurance
  - _Model_: sonnet | _Color_: green | _Auto-Invoked_: Test creation requests

- **[code-reviewer](../agents/code-reviewer.md)** - Code quality assessment and reviews

  - _Capabilities_: Quality analysis, best practices, maintainability assessment
  - _Best For_: Post-implementation reviews, refactoring guidance, quality improvement
  - _Model_: sonnet | _Color_: yellow | _Auto-Invoked_: After code implementation

- **[security-auditor](../agents/security-auditor.md)** - Security assessment and compliance
  - _Capabilities_: Vulnerability detection, OWASP compliance, threat modeling
  - _Best For_: Security reviews, compliance validation, vulnerability assessment
  - _Model_: opus | _Color_: red | _Auto-Invoked_: Security-critical changes

- **[refactoring-specialist](../agents/refactoring-specialist.md)** - Code improvement and technical debt reduction
  - _Capabilities_: Code quality improvement, technical debt management, pattern refactoring
  - _Best For_: Code cleanup, maintainability improvement, complexity reduction
  - _Model_: sonnet | _Color_: yellow | _Auto-Invoked_: On-demand

#### **Operations & Performance**

- **[devops-engineer](../agents/devops-engineer.md)** - Infrastructure and deployment automation

  - _Capabilities_: CI/CD, containerization, cloud platforms, monitoring
  - _Best For_: Deployment automation, infrastructure setup, environment management
  - _Model_: sonnet | _Color_: cyan | _Auto-Invoked_: Infrastructure tasks

- **[performance-optimizer](../agents/performance-optimizer.md)** - Performance analysis and optimization
  - _Capabilities_: Bottleneck identification, optimization strategies, monitoring
  - _Best For_: Performance audits, optimization recommendations, scalability planning
  - _Model_: sonnet | _Color_: orange | _Auto-Invoked_: Performance issues

- **[migration-specialist](../agents/migration-specialist.md)** - Version upgrades and framework migrations
  - _Capabilities_: Safe migrations, compatibility assessment, incremental modernization
  - _Best For_: Framework upgrades, dependency updates, legacy modernization
  - _Model_: sonnet | _Color_: purple | _Auto-Invoked_: On-demand


#### **Documentation & Communication**

- **[technical-writer](../agents/technical-writer.md)** - Documentation creation and maintenance specialist

  - _Capabilities_: Documentation creation, updates, synchronization, maintenance, link validation, technical writing
  - _Best For_: All documentation needs - creation, updates, maintenance, API docs, user guides, tutorials
  - _Model_: opus | _Color_: blue | _Auto-Invoked_: After code changes affecting documentation and documentation creation requests

## Agent Usage Patterns

### **Automatic Invocation Agents** (8 agents)

These agents activate automatically based on context and task requirements:

1. **project-manager** - Complex/multi-domain tasks requiring coordination
2. **context-analyzer** - Investigation and root cause analysis tasks
3. **frontend-specialist** - UI/UX development and component work
4. **backend-specialist** - Server-side implementation and business logic
5. **database-specialist** - All database-related operations
6. **test-engineer** - Test creation and strategy development
7. **code-reviewer** - Post-implementation quality reviews
8. **technical-writer** - After code changes affecting documentation

### **On-Demand Specialists** (13 agents)

These are invoked for specific domains or specialized work:

1. **brief-strategist** - Product brief and strategic planning
2. **code-architect** - Architectural decisions and system design
3. **ai-llm-expert** - AI/LLM architecture and implementation guidance
4. **aws-expert** - AWS cloud architecture and implementation
5. **azure-expert** - Azure cloud architecture and implementation
6. **gcp-expert** - Google Cloud architecture and implementation
7. **ui-ux-designer** - Design decisions, mockups, and design systems
8. **api-designer** - API architecture and endpoint design
9. **security-auditor** - Security audits and compliance validation
10. **devops-engineer** - Infrastructure and deployment automation
11. **performance-optimizer** - Performance analysis and optimization
12. **refactoring-specialist** - Code improvement and technical debt reduction
13. **migration-specialist** - Version upgrades and framework migrations

### By Task Complexity

#### **High Complexity (Opus Model)**

- **code-architect** - System architecture and technology decisions
- **ai-llm-expert** - AI/LLM architecture and implementation optimization
- **aws-expert** - AWS cloud architecture and cost optimization
- **azure-expert** - Azure cloud architecture and Microsoft ecosystem integration
- **gcp-expert** - Google Cloud architecture and data/AI workloads
- **project-manager** - Multi-agent orchestration and complex coordination
- **security-auditor** - Critical security analysis and compliance
- **technical-writer** - Comprehensive documentation creation and maintenance

#### **Medium Complexity (Sonnet Model)**

- **ui-ux-designer** - UI/UX design and design system architecture
- **frontend-specialist** - Modern frontend development and optimization
- **backend-specialist** - Server-side implementation and business logic
- **database-specialist** - Database design and performance optimization
- **api-designer** - API architecture and service design
- **test-engineer** - Comprehensive testing strategies
- **code-reviewer** - Quality analysis and best practices
- **context-analyzer** - Systematic investigation and analysis
- **devops-engineer** - Infrastructure automation and deployment
- **performance-optimizer** - Performance analysis and optimization
- **refactoring-specialist** - Code improvement and technical debt reduction
- **migration-specialist** - Version upgrades and framework migrations

#### **Low Complexity (Haiku Model)**

- None currently - all agents upgraded to Sonnet or Opus models for better quality

### By Usage Frequency

#### **Core Development Agents** (Daily use)

1. **frontend-specialist** - UI development and user experience
2. **backend-specialist** - Server-side implementation and business logic
3. **database-specialist** - Data modeling and query optimization
4. **code-reviewer** - Quality assurance and best practices
5. **technical-writer** - Documentation creation and maintenance

#### **Strategic Agents** (Weekly/project milestones)

1. **code-architect** - System design and architectural decisions
2. **security-auditor** - Security reviews and compliance
3. **performance-optimizer** - Performance analysis and optimization
4. **devops-engineer** - Infrastructure and deployment automation

#### **Specialized Agents** (As-needed basis)

1. **project-manager** - Complex multi-domain coordination
2. **context-analyzer** - Investigation and troubleshooting
3. **ui-ux-designer** - Design decisions, mockups, and design systems
4. **api-designer** - API architecture and design
5. **test-engineer** - Test strategy and comprehensive testing
6. **refactoring-specialist** - Code quality improvement and technical debt
7. **migration-specialist** - Framework upgrades and system modernization

## Agent Selection Guidelines

### Decision Matrix

```yaml
task_type_mapping:
  design_decisions:
    strategic_design: [ui-ux-designer → /adr for ADRs]
    mockups_wireframes: [ui-ux-designer]
    color_schemes: [ui-ux-designer]
    design_systems: [ui-ux-designer, code-architect]
    accessibility_planning: [ui-ux-designer]

  feature_development:
    frontend_work: [frontend-specialist]
    backend_work: [backend-specialist, database-specialist]
    api_development: [api-designer, backend-specialist]
    full_stack: [frontend-specialist + backend-specialist + database-specialist]
    complex_features: [project-manager → coordinates specialists]

  bug_fixing:
    ui_bugs: [frontend-specialist]
    backend_bugs: [backend-specialist, database-specialist]
    api_bugs: [api-designer, backend-specialist]
    performance_bugs: [performance-optimizer]
    security_bugs: [security-auditor]
    investigation_needed: [context-analyzer]

  code_quality:
    post_implementation: [code-reviewer]
    architecture_review: [code-architect]
    refactoring: [code-reviewer + relevant specialists]
    optimization: [performance-optimizer]

  infrastructure:
    deployment: [devops-engineer]
    ci_cd_setup: [devops-engineer]
    monitoring: [devops-engineer, performance-optimizer]
    security_hardening: [security-auditor, devops-engineer]

  documentation:
    sync_with_code: [technical-writer] # auto-invoked
    new_documentation: [technical-writer]
    api_docs: [technical-writer, api-designer]
    architecture_docs: [technical-writer, code-architect]

  data_management:
    schema_design: [database-specialist]
    query_optimization: [database-specialist, performance-optimizer]
    migrations: [database-specialist]
    performance_tuning: [performance-optimizer, database-specialist]

  testing:
    test_strategy: [test-engineer]
    test_creation: [test-engineer]
    test_automation: [test-engineer, devops-engineer]
```

### Selection Criteria

#### **By Project Phase**

- **Strategy**: brief-strategist, project-manager (product brief + strategic planning)
- **Design**: ui-ux-designer (design decisions, mockups, design systems)
- **Architecture**: code-architect, ui-ux-designer, devops-engineer, database-specialist, api-designer, security-auditor (tech stack + design system + feature architecture)
- **Planning**: project-manager, context-analyzer
- **Development**: frontend-specialist, backend-specialist, database-specialist
- **Quality Assurance**: code-reviewer, test-engineer, security-auditor
- **Deployment**: devops-engineer
- **Maintenance**: performance-optimizer, technical-writer, security-auditor

#### **By Team Size**

- **Solo Developer**: Use 3-4 core agents (frontend-specialist, database-specialist, code-reviewer, technical-writer)
- **Small Team (2-5)**: Add specialists as needed (devops-engineer, security-auditor, performance-optimizer)
- **Large Team (5+)**: Full agent suite with project-manager for coordination

#### **By Risk Profile**

- **High Risk**: security-auditor, code-architect, project-manager (coordination)
- **Medium Risk**: code-reviewer, performance-optimizer, test-engineer
- **Low Risk**: Domain specialists with standard quality review

## Multi-Agent Coordination Patterns

### Sequential Workflows

#### **Development Pipeline**

1. project-manager (planning)
2. frontend-specialist + backend-specialist (implementation)
3. code-reviewer (quality check)
4. security-auditor (security review)
5. devops-engineer (deployment)

#### **Quality Assurance Pipeline**

1. code-reviewer (static analysis)
2. security-auditor (security scan)
3. performance-optimizer (performance test)
4. technical-writer (documentation review)

### Parallel Workflows

#### **Comprehensive Analysis**

- security-auditor (security assessment)
- performance-optimizer (performance analysis)
- code-reviewer (quality analysis)
- database-specialist (data analysis)

#### **Feature Development**

- frontend-specialist (UI components)
- backend-engineer (API endpoints)
- database-specialist (data models)
- technical-writer (documentation)

## Agent Communication Protocols

### Handoff Procedures

```yaml
agent_handoff:
  context_preservation:
    - current_state_summary
    - completed_tasks_list
    - pending_issues_log
    - relevant_file_changes

  quality_gates:
    - deliverable_validation
    - documentation_completeness
    - test_coverage_verification
    - security_compliance_check

  next_agent_briefing:
    - task_objectives
    - context_summary
    - expected_deliverables
    - success_criteria
```

### Coordination Mechanisms

#### **Shared Context**

- Centralized task tracking
- Shared documentation
- Common code standards
- Unified quality metrics

#### **Communication Channels**

- Status updates in shared documents
- Code comments for technical coordination
- Documentation for knowledge transfer
- Test results for quality validation

## Usage Examples

### Example 1: Feature Development

```yaml
scenario: "Implement user authentication system"
agent_sequence:
  1. project-manager: "Break down requirements and create task plan"
  2. security-auditor: "Define security requirements and threat model"
  3. database-specialist: "Design user data schema"
  4. backend-engineer: "Implement authentication API"
  5. frontend-specialist: "Create login/signup UI components"
  6. code-reviewer: "Review implementation quality"
  7. technical-writer: "Document authentication flow"
```

### Example 2: Performance Optimization

```yaml
scenario: "Optimize application performance"
agent_sequence:
  1. performance-optimizer: "Identify bottlenecks and performance issues"
  2. database-specialist: "Optimize database queries and indexing"
  3. backend-engineer: "Implement caching and API optimizations"
  4. frontend-specialist: "Optimize bundle size and rendering"
  5. devops-engineer: "Configure production performance monitoring"
  6. code-reviewer: "Validate optimization implementations"
```

### Example 3: Security Audit

```yaml
scenario: "Comprehensive security assessment"
agent_parallel:
  - security-auditor: "Vulnerability assessment and threat modeling"
  - code-reviewer: "Static analysis for security patterns"
  - devops-engineer: "Infrastructure security configuration"
  - database-specialist: "Data security and access controls"
agent_coordination:
  - project-manager: "Aggregate findings and prioritize remediation"
  - technical-writer: "Document security procedures and guidelines"
```

## Agent Performance Metrics

### Quality Indicators

```yaml
performance_metrics:
  task_completion_rate:
    excellent: ">95%"
    good: "90-95%"
    needs_improvement: "<90%"

  code_quality_score:
    excellent: ">9.0/10"
    good: "8.0-9.0/10"
    needs_improvement: "<8.0/10"

  documentation_completeness:
    excellent: ">90%"
    good: "80-90%"
    needs_improvement: "<80%"

  security_compliance:
    excellent: "100% compliance"
    good: "95-99% compliance"
    needs_improvement: "<95% compliance"
```

### Optimization Opportunities

#### **Agent Specialization**

- Monitor task success rates by agent type
- Identify optimization opportunities for specific domains
- Adjust agent selection based on performance data

#### **Workflow Efficiency**

- Measure handoff effectiveness between agents
- Identify bottlenecks in multi-agent workflows
- Optimize coordination patterns based on results

## Agent Capability Matrix

### File Type Handling by Agent

| Agent | JavaScript/TS | Python | Database | Config | Docs | Tests | Infrastructure | Design Assets |
|-------|-------------|---------|----------|--------|------|-------|----------------|---------------|
| **ui-ux-designer** | ❌ | ❌ | ❌ | ❌ | ✅ Design Docs | ❌ | ❌ | ✅ Primary |
| **frontend-specialist** | ✅ Primary | ❌ | ❌ | ⚠️ Frontend | ⚠️ Component | ✅ Frontend | ❌ | ⚠️ Implementation |
| **backend-specialist** | ✅ Node.js | ✅ Primary | ⚠️ Integration | ✅ Server | ⚠️ API | ✅ Backend | ⚠️ App | ❌ |
| **database-specialist** | ⚠️ Queries | ⚠️ Queries | ✅ Primary | ✅ DB Config | ⚠️ Schema | ✅ DB Tests | ⚠️ DB | ❌ |
| **api-designer** | ✅ Contracts | ✅ Contracts | ❌ | ✅ API | ✅ API Docs | ⚠️ API Tests | ❌ | ❌ |
| **test-engineer** | ✅ Tests | ✅ Tests | ⚠️ Test Data | ✅ Test Config | ⚠️ Test Docs | ✅ Primary | ⚠️ Test Env | ❌ |
| **code-reviewer** | ✅ All | ✅ All | ✅ All | ✅ All | ⚠️ Review | ✅ All | ✅ All | ❌ |
| **security-auditor** | ✅ Security | ✅ Security | ✅ Security | ✅ Security | ⚠️ Security | ⚠️ Security | ✅ Security | ❌ |
| **devops-engineer** | ⚠️ Build | ⚠️ Build | ❌ | ✅ Primary | ⚠️ Ops | ⚠️ E2E | ✅ Primary | ❌ |
| **performance-optimizer** | ✅ Perf | ✅ Perf | ✅ Queries | ⚠️ Perf | ❌ | ✅ Perf Tests | ⚠️ Perf | ❌ |
| **technical-writer** | ❌ | ❌ | ❌ | ❌ | ✅ Primary | ❌ | ❌ | ⚠️ Docs |
| **refactoring-specialist** | ✅ Refactor | ✅ Refactor | ⚠️ Schema | ⚠️ Config | ❌ | ⚠️ Test Refactor | ❌ | ❌ |
| **migration-specialist** | ✅ Migrations | ✅ Migrations | ✅ Migrations | ✅ Migrations | ⚠️ Migration | ⚠️ Migration | ✅ Migrations | ❌ |

**Legend**: ✅ Primary expertise | ⚠️ Secondary/Supporting | ❌ Not applicable

### Domain Expertise Matrix

| Domain | Primary Agents | Supporting Agents | Typical Workflow |
|--------|----------------|-------------------|------------------|
| **UI/UX Design** | ui-ux-designer | code-architect, frontend-specialist, technical-writer | ui-ux-designer → /adr (strategic) → frontend (implementation) → technical-writer (docs) |
| **Frontend Development** | frontend-specialist | ui-ux-designer, api-designer, test-engineer, code-reviewer | ui-ux-designer → frontend → api-designer → test-engineer → code-reviewer |
| **Backend Development** | backend-specialist | database-specialist, api-designer, security-auditor | backend → database → api-designer → security-auditor |
| **Database Management** | database-specialist | backend-specialist, performance-optimizer, migration-specialist | database → backend → performance → migration |
| **API Development** | api-designer | backend-specialist, frontend-specialist, test-engineer | api-designer → backend → frontend → test-engineer |
| **Quality Assurance** | test-engineer, code-reviewer | security-auditor, performance-optimizer | test-engineer → code-reviewer → security → performance |
| **Infrastructure** | devops-engineer | security-auditor, performance-optimizer, migration-specialist | devops → security → performance → migration |
| **Documentation** | technical-writer | All domain specialists | technical-writer → domain-specialist |
| **Code Quality** | refactoring-specialist, code-reviewer | performance-optimizer, security-auditor | code-reviewer → refactoring → performance → security |
| **System Migration** | migration-specialist | code-architect, devops-engineer, database-specialist | migration → architect → devops → database |

### Tool Usage Patterns

| Tool Category | Primary Users | Secondary Users | Use Cases |
|---------------|---------------|----------------|-----------|
| **Read/Write/Edit** | All agents | - | Core file operations |
| **Bash** | devops-engineer, migration-specialist | backend-specialist, test-engineer | Infrastructure, testing, migration scripts |
| **Grep/Glob** | All agents | - | Code search and file discovery |
| **TodoWrite** | All agents | - | Task tracking and coordination |
| **MultiEdit** | refactoring-specialist, migration-specialist | backend-specialist, frontend-specialist | Bulk code changes |
| **MCP Context7** | frontend-specialist, backend-specialist, api-designer, database-specialist, ui-ux-designer | code-reviewer, code-architect, security-auditor, performance-optimizer | Framework documentation lookup, library research, design system docs |
| **MCP Sequential Thinking** | ui-ux-designer, project-manager, code-reviewer, code-architect, security-auditor, performance-optimizer | - | Complex design analysis, systematic reasoning, decision-making |

### Model Usage Justification

| Model | Agents | Justification |
|-------|--------|---------------|
| **Opus 4.5** | project-manager, security-auditor, brief-strategist, ai-llm-expert, aws-expert, azure-expert, gcp-expert | Extended reasoning, strategic orchestration, safety-critical decisions, cloud architecture, meta-reasoning |
| **Sonnet 4.5** | code-architect, technical-writer, ui-ux-designer, frontend-specialist, backend-specialist, database-specialist, api-designer, test-engineer, code-reviewer, devops-engineer, performance-optimizer, refactoring-specialist, migration-specialist | Best coding model (77.2% SWE-bench), superior documentation, cost-effective execution |
| **Haiku** | context-analyzer | Fast analysis and investigation, optimized for speed |

## Best Practices for Agent Management

### Selection Guidelines

1. **Match Expertise to Task**: Choose agents with domain-specific knowledge
2. **Consider Complexity**: Use appropriate model (haiku/sonnet/opus) for task complexity
3. **Plan Coordination**: Design handoff procedures for multi-agent workflows
4. **Monitor Performance**: Track agent effectiveness and optimize selection

### Quality Assurance

1. **Clear Objectives**: Define specific, measurable outcomes for each agent
2. **Quality Gates**: Implement checkpoints between agent handoffs
3. **Documentation**: Maintain comprehensive records of agent activities
4. **Continuous Improvement**: Regularly evaluate and improve agent performance

### Resource Optimization

1. **Cost Management**: Balance model selection with task requirements
2. **Time Efficiency**: Optimize agent coordination to minimize delays
3. **Context Preservation**: Maintain continuity across agent handoffs
4. **Parallel Processing**: Leverage parallel workflows when possible

---

_This agent index provides comprehensive guidance for selecting, coordinating, and optimizing AI agent usage across development projects._
