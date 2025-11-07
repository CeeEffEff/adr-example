# ADR Example Repository

[![Log4brains ADRs](https://ceeeffeff.github.io/adr-example/log4brains//badge.svg)](https://ceeeffeff.github.io/adr-example/log4brains/)

An experimental repository for working with **Architecture Decision Records (ADRs)** using modern tooling and best practices.

## What are ADRs?

Architecture Decision Records (ADRs) are documents that capture important architectural decisions made during a project, along with their context and consequences. They help teams:

- **Document the "why"** behind technical decisions, not just the "what"
- **Preserve context** for future team members and your future self
- **Track the evolution** of architectural thinking over time
- **Facilitate better discussions** by making decision drivers explicit
- **Avoid repeating past mistakes** by documenting negative consequences

ADRs are especially valuable for decisions that are:

- Significant (expensive to change)
- High-impact (affect multiple parts of the system)
- Controversial (where multiple valid options exist)

## Project Overview

This repository demonstrates ADR management using **Log4brains**, a modern tool that combines CLI convenience with rich web visualization. ADRs are written in Markdown using the **MADR (Markdown Architectural Decision Records)** template format, which provides a structured approach to documenting decisions.

## Tech Stack

**Note:** This is a Python repository (Python 3.13+) but uses Node.js for the Log4brains ADR management tool.

- **Python 3.13+**: Main application language, managed with `uv`
- **Node.js/npm**: Required for Log4brains CLI and web interface
- **Log4brains**: ADR management tooling with CLI and web UI

## Setup Instructions

### Prerequisites

1. **Python 3.13+** with `uv` installed
   - Install uv: `curl -LsSf https://astral.sh/uv/install.sh | sh`
2. **Node.js and npm** (for Log4brains)
   - Install via [nodejs.org](https://nodejs.org/) or your package manager

### Installation

```bash
# Install Python dependencies
uv sync

# Install Node.js dependencies (for Log4brains)
npm install
```

## Working with ADRs

ADRs are stored in `docs/adr/` and follow a date-based naming convention (`YYYYMMDD-title-with-hyphens.md`) to avoid merge conflicts in team environments.

### Creating ADRs

**Interactive mode** (recommended for first-time use):

```bash
npx log4brains adr new "Title of your decision"
```

This will prompt you for additional metadata (status, deciders, tags, etc.).

**Non-interactive mode** (quick creation):

```bash
npx log4brains adr new --quiet "Use GraphQL for API layer"
```

This creates an ADR with default values that you can edit afterward.

**Create from custom template**:

```bash
npx log4brains adr new --from docs/adr/template.md "Custom decision title"
```

#### Example: Creating an ADR

```bash
# Create a new ADR about database selection
npx log4brains adr new "Use PostgreSQL as primary database"

# This creates: docs/adr/20251107-use-postgresql-as-primary-database.md
# Edit the file to add context, decision drivers, options, and outcome
```

### Listing ADRs

View all ADRs in a formatted table:

```bash
npx log4brains adr list
```

This displays:

- ADR number/date
- Title
- Status (proposed, accepted, rejected, deprecated, superseded)
- File path

### Viewing and Browsing ADRs

**Web interface** (recommended):

```bash
npx log4brains preview
```

This starts a local web server (default: `http://localhost:4004`) with:

- **Timeline visualization**: See decisions chronologically
- **Search functionality**: Find ADRs by keywords
- **Hot reload**: Changes to ADR files automatically refresh in the browser
- **Relationship graphs**: Visualize which ADRs supersede others
- **Automatic metadata**: Extracted from git history (author, dates)

**Custom port**:

```bash
npx log4brains preview --port 8080
```

**Start without opening browser**:

```bash
npx log4brains preview --no-open
```

### Building Static Site

Generate a static website for deployment (e.g., GitHub Pages):

```bash
npx log4brains build
```

The generated site will be in `.log4brains/out/`.

## ADR Format (MADR Template)

This repository uses the **MADR 2.1.2** (Markdown Architectural Decision Records) template, which structures decisions into clear sections:

### Template Structure

```markdown
# Title of Decision

- Status: [proposed | accepted | rejected | deprecated | superseded]
- Date: YYYY-MM-DD
- Tags: tag1, tag2, tag3

## Context and Problem Statement

[Describe the context and problem that requires a decision]

## Decision Drivers

- [driver 1]
- [driver 2]
- [driver 3]

## Considered Options

- [option 1]
- [option 2]
- [option 3]

## Decision Outcome

Chosen option: "[option]", because [justification].

### Positive Consequences

- [positive consequence 1]
- [positive consequence 2]

### Negative Consequences

- [negative consequence 1]
- [negative consequence 2]

## Pros and Cons of the Options

### [option 1]

- Good, because [argument a]
- Good, because [argument b]
- Bad, because [argument c]
- Bad, because [argument d]

[... repeat for other options ...]

## Links

- Related to [ADR-0001](0001-example.md)
- Supersedes [ADR-0002](0002-example.md)
```

### Key Sections Explained

- **Status**: Tracks the lifecycle of the decision (proposed → accepted/rejected)
- **Context and Problem Statement**: The "why" behind needing to make a decision
- **Decision Drivers**: Factors that influence the decision (requirements, constraints, priorities)
- **Considered Options**: All viable alternatives that were evaluated
- **Decision Outcome**: The chosen option and explicit justification
- **Consequences**: Both positive and negative impacts of the decision
- **Pros and Cons**: Detailed evaluation of each option considered
- **Links**: Relationships to other ADRs (supersedes, related to, etc.)

## Architecture and Tooling Decisions

This repository itself uses ADRs to document its own tooling decisions. See:

- **[ADR-0003: Use Log4brains for ADR management](docs/adr/20251107-use-log4brains-for-adr-management.md)** - Documents the decision to adopt Log4brains over Python-native alternatives

This ADR explains why Log4brains was chosen despite requiring a Node.js dependency in a Python project (active maintenance, rich features, MADR support).

## Additional Resources

- [Log4brains Documentation](https://github.com/thomvaill/log4brains)
- [MADR Template Format](https://adr.github.io/madr/)
- [ADR GitHub Organization](https://adr.github.io/) - General ADR resources and tools

## License

This is an experimental repository for learning purposes.
