---
tags: ["workflow", "project-status", "analysis", "context", "dashboard"]
description: "Enhanced project status dashboard with intelligent context analysis"
argument-hint: "[--format FORMAT] [--scope SCOPE] [--ai-format] [--detailed]"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob", "TodoWrite", "Task"]
model: claude-sonnet-4-5
references_guidelines:
  - pm/README.md  # PM structure for parsing epics and issues
---

# /project-status Command

Enhanced project status dashboard with intelligent analysis using context-analyzer agent.

**Note**: Renamed from `/status` to avoid conflict with built-in Claude Code `/status` command.

## Usage

```bash
# Basic intelligent status report
/project-status

# AI-optimized format for assistants
/project-status --ai-format

# Enhanced with memory insights and personalization
/project-status --with-memory

# Detailed analysis with recommendations
/project-status --detailed

# Specific scope analysis
/project-status --scope git
/project-status --scope project
/project-status --scope environment

# JSON output for programmatic use
/project-status --format json
```

## Agent Coordination

**Primary**: context-analyzer (intelligent analysis and pattern recognition beyond basic status)
**Supporting**: data-analyst (metrics and trend analysis), technical-writer (status report formatting)
**Integration**: Provides comprehensive project status through intelligent agent analysis

## Features

### Intelligent Context Analysis

Uses **context-analyzer** agent to provide deep insights beyond basic status:

- **Git Repository Analysis**: Branch strategy assessment, commit patterns, merge conflicts
- **Project Health Assessment**: Code quality trends, technical debt indicators
- **Workflow State Analysis**: Current workflow phase, task progress, blockers
- **Development Patterns**: Team productivity metrics, code review patterns
- **Environment Consistency**: Development environment validation, dependency issues

### Enhanced Status Dimensions

```yaml
status_dimensions:
  git_intelligence:
    basic: [branch, commits, status]
    enhanced: [branch_strategy_analysis, commit_pattern_insights, merge_conflict_prediction]
    agent: context-analyzer

  project_health:
    basic: [file_counts, directory_structure]
    enhanced: [code_quality_trends, technical_debt_assessment, architecture_health]
    agent: context-analyzer

  workflow_state:
    basic: [current_files, recent_changes]
    enhanced: [workflow_phase_analysis, task_progress_insights, blocker_identification]
    agent: context-analyzer

  environment_analysis:
    basic: [tools, dependencies]
    enhanced: [environment_consistency, dependency_vulnerability_assessment, toolchain_optimization]
    agent: context-analyzer

  development_insights:
    basic: [recent_activity]
    enhanced: [productivity_patterns, collaboration_insights, quality_trends]
    agent: context-analyzer
```

## Status Formats

### Standard Format (Human-Readable)
- Clean, colorized output with visual indicators
- Key metrics highlighted with emojis
- Actionable recommendations
- Priority issue flagging

### AI-Optimized Format
- Structured text format optimized for AI consumption
- Complete context preservation
- Machine-readable status indicators
- Integration-ready data structure

### Detailed Analysis Format
- Comprehensive analysis with trend data
- Historical context and patterns
- Detailed recommendations with rationale
- Risk assessment and mitigation suggestions

### JSON Format
- Complete programmatic access to all status data
- Integration with CI/CD pipelines
- API-ready data structure
- Historical data preservation

## Jira Integration

**Hybrid Status Display**: When Jira is enabled, show both Jira and local issues side-by-side.

### Configuration Check

**Read CLAUDE.md at start:**
```yaml
## Jira Integration
- **Enabled**: true/false
- **Project Key**: PROJ
```

### Status Display Modes

**Local-Only Mode (jira.enabled: false):**
```
## Epics
- EPIC-001: User Authentication (3/5 tasks complete)
- EPIC-002: Data Management (1/3 tasks complete)

## Issues
- TASK-001: Login form [COMPLETED]
- TASK-002: Password reset [IN PROGRESS]
- BUG-001: Session timeout [PLANNED]
```

**Jira-Enabled Mode (jira.enabled: true):**
```
## Epics (from Jira)
- PROJ-100: User Authentication (Jira: In Progress)
  - 3/5 issues complete locally

## Issues

Jira Issues:
- PROJ-123: User registration [PLANNED] (Jira: To Do)
- PROJ-124: Password reset [IN PROGRESS] (Jira: In Progress)
- PROJ-125: OAuth integration [COMPLETED] (Jira: Done)

Local Exploration:
- TASK-001: Auth spike [COMPLETED]
- TASK-002: Session experiments [IN PROGRESS]

Legend:
- [PLANNED] = PLAN.md exists
- [IN PROGRESS] = WORKLOG.md exists
- [COMPLETED] = All PLAN.md phases checked
- (Jira: X) = Current status in Jira (read-only display)
```

### Jira Data Collection

**When Jira is enabled:**

1. **Query Jira for project issues**:
   - Use Atlassian MCP to list issues in project
   - Filter: Issues linked to configured project key
   - Get: Issue key, summary, status, epic link

2. **Match with local artifacts**:
   - Check for `pm/issues/PROJ-123-*/` directory
   - Check for PLAN.md (planned)
   - Check for WORKLOG.md (in progress)
   - Check phase completion in PLAN.md (completed)

3. **Show both sources**:
   - Jira issues with local implementation status
   - Local issues (TASK-###) for exploration/spikes
   - Clear visual separation

4. **Highlight divergence**:
   - ⚠️ Local shows COMPLETED but Jira shows "In Progress"
   - Suggests: "Update Jira status manually"

### Example Output (Hybrid Mode)

```
/project-status

## 📋 Epics

From Jira:
✓ PROJ-100: User Authentication (Jira: In Progress)
  - 4/6 issues complete (66%)
  - View: https://company.atlassian.net/browse/PROJ-100

## 🎯 Issues

Jira Issues (6 total):
✅ PROJ-121: Database schema [COMPLETED] (Jira: Done)
🔨 PROJ-122: Login endpoint [IN PROGRESS] (Jira: In Progress)
📝 PROJ-123: Registration form [PLANNED] (Jira: To Do)
📝 PROJ-124: Password reset [PLANNED] (Jira: To Do)
⚠️ PROJ-125: OAuth flow [COMPLETED] (Jira: In Progress)
   → Local work complete, update Jira status
❌ PROJ-126: Session management [NOT STARTED] (Jira: To Do)

Local Exploration (2 total):
✅ TASK-001: Auth spike [COMPLETED]
🔨 TASK-002: Session experiments [IN PROGRESS]

Summary:
- Jira: 1 done, 2 in progress, 3 todo
- Local: 4 with PLAN.md, 2 in active development
- Action: Update PROJ-125 status in Jira
```

### Error Handling

**MCP unavailable:**
```
Warning: Jira integration enabled but MCP unavailable.
Showing local issues only.

Configure Atlassian Remote MCP to see Jira status.
```

**Jira query fails:**
```
Warning: Could not fetch Jira issues.
Reason: Network timeout

Showing local issues only.
```

### Implementation Notes

**When generating status:**

1. **Check Jira mode**:
   - Read CLAUDE.md jira.enabled
   - If false: Show local only (current behavior)
   - If true: Query Jira + local (hybrid display)

2. **Query Jira (if enabled)**:
   - Use Atlassian MCP: Search issues in project
   - Get issue list with keys, summaries, statuses
   - Handle MCP unavailable gracefully

3. **Scan local directories**:
   - Glob: `pm/issues/*/`
   - Detect: PROJ-### (Jira) vs TASK-### (local)
   - Check: PLAN.md, WORKLOG.md existence
   - Parse: Phase completion in PLAN.md

4. **Match and correlate**:
   - For each Jira issue: Check if local directory exists
   - Determine local status: [NOT STARTED], [PLANNED], [IN PROGRESS], [COMPLETED]
   - Compare: Local vs Jira status

5. **Display hybrid view**:
   - Epics from Jira (if any)
   - Jira issues with local status
   - Local exploration issues
   - Highlight divergence (local done, Jira in progress)

## AI-Driven Status Analysis

Pure AI agent coordination for comprehensive project status:

```yaml
agent_analysis:
  data_collection:
    approach: AI-driven repository analysis
    purpose: "Gather comprehensive project status through intelligent inspection"

  pattern_recognition:
    agent: context-analyzer
    purpose: "Analyze patterns and provide actionable insights"

  intelligent_reporting:
    coordination: "Agents synthesize status with context-aware recommendations"
    format_options: [human, ai-optimized, detailed, json]
```

## Status Categories

### 🏗️ Project Architecture Status
- Architecture health assessment
- Design pattern consistency analysis
- Technical debt identification
- Scalability risk assessment

### 🔧 Development Environment Status
- Tool compatibility analysis
- Dependency security assessment
- Environment consistency validation
- Performance optimization opportunities

### 📊 Workflow Efficiency Status
- Current workflow phase analysis
- Task completion rate trends
- Quality gate effectiveness
- Collaboration pattern insights

### 🚀 Deployment Readiness Status
- Code quality gate validation
- Security compliance assessment
- Performance benchmark status
- Documentation completeness check

## Examples

### Basic Intelligent Status
```bash
/project-status
# → context-analyzer performs comprehensive project analysis
# → Provides intelligent insights on project health
# → Highlights priority issues and recommendations
# → Shows workflow state and next suggested actions
```

### AI Assistant Context Refresh
```bash
/project-status --ai-format
# → Optimized format for AI context understanding
# → Complete project state for session continuity
# → Integration-ready status information
# → Context preservation for workflow commands
```

### Memory-Enhanced Status
```bash
/project-status --with-memory
# → Includes user preferences and historical patterns
# → Agent effectiveness analytics for optimal selection
# → Cross-project insights and successful patterns
# → Personalized recommendations based on past outcomes
```

### Detailed Project Analysis
```bash
/project-status --detailed
# → Comprehensive analysis with historical trends
# → Risk assessment with mitigation strategies
# → Productivity insights and optimization suggestions
# → Quality trend analysis with predictions
```

### Scope-Specific Analysis
```bash
/project-status --scope git --detailed
# → Deep git repository analysis
# → Branch strategy assessment
# → Commit pattern insights
# → Merge conflict risk analysis
```

## Intelligent Insights

The context-analyzer agent provides enhanced insights:

### 🎯 Predictive Analysis
- Risk identification before issues occur
- Quality trend prediction
- Resource bottleneck forecasting
- Technical debt accumulation patterns

### 📈 Pattern Recognition
- Development velocity patterns
- Code quality correlation analysis
- Team collaboration effectiveness
- Workflow optimization opportunities

### 🛠️ Actionable Recommendations
- Priority-ranked improvement suggestions
- Automated fix opportunities identification
- Workflow optimization recommendations
- Tool and process enhancement suggestions

### 🔍 Context-Aware Insights
- Project phase-appropriate recommendations
- Team size and experience considerations
- Technology stack optimization suggestions
- Business impact assessment of technical issues

## Integration with Core Workflow

### During `/adr` Phase
- Architecture readiness assessment
- Technical feasibility insights
- Risk factor identification

### During `/plan` Phase
- Resource availability analysis
- Complexity assessment validation
- Implementation readiness check

### During `/implement` Phase
- Progress tracking with velocity insights
- Quality gate effectiveness monitoring
- Blocker identification and resolution suggestions

## Status Report Structure

```yaml
report_structure:
  executive_summary:
    overall_health: [score, trend, key_issues]
    priority_actions: [immediate, short_term, long_term]

  technical_details:
    git_status: [branch_analysis, commit_insights, merge_readiness]
    project_health: [code_quality, architecture, dependencies]
    environment: [tools, consistency, optimization]

  insights_and_recommendations:
    patterns: [development_velocity, quality_trends, collaboration]
    predictions: [risk_assessment, trend_forecasting]
    actions: [automated_fixes, process_improvements, tool_optimization]

  workflow_context:
    current_phase: [active_tasks, completion_status, next_steps]
    context_preservation: [session_state, workflow_continuity]
```

## Benefits Over Basic Status

✅ **Intelligent Analysis**: Context-analyzer provides deep insights beyond raw data
✅ **Predictive Capabilities**: Identifies issues before they become problems
✅ **Actionable Recommendations**: Specific, prioritized improvement suggestions
✅ **Context Preservation**: Perfect integration with AI workflow commands
✅ **Pattern Recognition**: Identifies trends and optimization opportunities
✅ **Risk Assessment**: Proactive identification of potential blockers and issues