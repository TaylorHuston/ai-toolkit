# Optional MCP Servers

This plugin automatically bundles essential MCP servers (context7, sequential-thinking, playwright). This document covers additional MCP servers you may want to add as your project grows.

## Serena - Semantic Code Analysis

**When to Add**: Projects with 20+ implementation files

**What it Does**: Serena provides semantic code analysis and understanding for larger codebases, enabling Claude to better understand code relationships, patterns, and architecture.

**Benefits**:
- Semantic search across your codebase
- Better code understanding for refactoring
- Architecture analysis and suggestions
- Pattern detection and consistency checking

### Installation

Add to your **user-level** MCP configuration (`~/.claude/settings.json`) or **project-level** (`.claude/settings.json`):

```json
{
  "mcpServers": {
    "serena": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/oraios/serena",
        "serena",
        "start-mcp-server",
        "--context",
        "ide-assistant"
      ],
      "env": {}
    }
  }
}
```

### Requirements

- Python/uvx installed
- Git access to clone the repository
- Suitable for projects with established codebase structure

### When NOT to Use

- ❌ New projects with minimal code
- ❌ Projects with < 20 implementation files
- ❌ Simple scripts or utilities
- ❌ Projects where you need fast initialization

Serena adds overhead that's only valuable when you have a substantial codebase to analyze.

## Adding Other MCP Servers

See the [MCP documentation](https://docs.claude.com/en/docs/claude-code/mcp) for other available MCP servers and how to configure them.

Common additions:
- **Database MCP servers** - For database schema inspection
- **API testing tools** - For API development workflows
- **Git helpers** - For advanced git operations
- **File system tools** - For complex file operations

Add these to your user or project settings as needed.
