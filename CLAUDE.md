# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Structuring CLAUDE.md - Use XML

XML tags help Claude parse instructions more accurately, leading to higher-quality outputs. Use tags like `<commands>`, `<architecture>`, and `<workflow>` to clearly separate different sections.

### XML Best Practices for CLAUDE.md

- **Clarity**: Clearly separate different types of information (commands, architecture, workflows)
- **Consistency**: Use the same tag names throughout and refer to them explicitly
- **Nesting**: Use nested tags `<outer><inner></inner></outer>` for hierarchical information
- **Meaningful names**: Tag names should describe the information they contain

---

<project-overview>
This is an experimental repository for working with Architecture Decision Records (ADRs) tooling. ADRs are stored in `docs/adr/` and use the MADR (Markdown Architectural Decision Records) template format, managed using Log4brains.
</project-overview>

<dependency-management>

## Dependency Management

This project uses **uv** for Python dependency management (Python 3.13+) and **npm** for Node.js tools.

<commands>

### Python Dependencies
- `uv add <package>` - Add a new dependency
- `uv run <command>` - Run a command in the project's virtual environment
- `uv sync` - Sync dependencies

### Node.js Dependencies
- `npm install` - Install dependencies from package.json
- `npx <command>` - Run a command from node_modules

</commands>
</dependency-management>

<adr-management>

## ADR Management

The project uses **Log4brains** for ADR management, which provides CLI commands and web visualization using the MADR 2.1.2 template format.

<creating-adrs>

### Creating and Managing ADRs

- `npx log4brains adr new "Title of decision"` - Create a new ADR (interactive mode)
- `npx log4brains adr new --quiet "Title"` - Create a new ADR (non-interactive)
- `npx log4brains adr list` - List all ADRs in a formatted table
- `npx log4brains adr new --from <file> "Title"` - Create ADR from a custom template file

Note: ADRs are created with date-based naming (e.g., `20251107-title.md`) to avoid merge conflicts.

</creating-adrs>

<viewing-adrs>

### Viewing ADRs

- `npx log4brains preview` - Start local web server with hot reload to preview ADRs
- `npx log4brains preview --port <port>` - Specify custom port (default: 4004)
- `npx log4brains preview --no-open` - Start server without opening browser
- `npx log4brains build` - Generate static website for deployment

The web interface includes timeline visualization, search functionality, and automatic metadata extraction from git history.

</viewing-adrs>
</adr-management>

<architecture>

## Architecture

<adr-storage>

### ADR Storage

- All ADRs are markdown files in `docs/adr/`
- Follow naming convention: `YYYYMMDD-title-with-hyphens.md` (date-based to avoid merge conflicts)
- Each ADR uses the MADR 2.1.2 template format with sections:
  - Status, Deciders, Date, Tags (metadata)
  - Context and Problem Statement
  - Decision Drivers
  - Considered Options
  - Decision Outcome (with Positive/Negative Consequences)
  - Pros and Cons of the Options
  - Links
- The `template.md` file in `docs/adr/` defines the structure for new ADRs

</adr-storage>

<tooling-decision>

### Tooling Decision

See `docs/adr/20251107-use-log4brains-for-adr-management.md` for the decision to use Log4brains. Log4brains is actively maintained (1.3k stars) and provides both CLI and rich web visualization features with MADR template support.

</tooling-decision>
</architecture>

<agent-workflow>

## Agent Usage

When working with agents in this repository:

- Delegate small, focused tasks to `focused-task-executor` agents
- Each agent should handle only one specific task
- Use the context7 MCP for retrieving Python package documentation

</agent-workflow>
