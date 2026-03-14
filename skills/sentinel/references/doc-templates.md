# Project Documentation Templates

When the spec-writer discovers missing documentation, use these templates as starting points. Adapt them heavily to the actual project — these are skeletons, not fill-in-the-blank forms.

---

## CLAUDE.md Template

This file tells AI agents how to work on the project effectively.

```markdown
# [Project Name] — AI Agent Instructions

## Project Overview
[One paragraph: what this project is, who it's for, what it does]

## Tech Stack
- **Language**: [e.g., TypeScript 5.x]
- **Framework**: [e.g., Next.js 14 (App Router)]
- **Database**: [e.g., PostgreSQL via Prisma]
- **Hosting**: [e.g., Vercel]
- **Key libraries**: [list the important ones]

## Architecture
[Brief description of how the codebase is organized]
- `src/app/` — [what's here]
- `src/lib/` — [what's here]
- `src/components/` — [what's here]
[etc.]

## Development Commands
```bash
[install command]      # Install dependencies
[dev command]          # Start dev server
[test command]         # Run tests
[lint command]         # Lint code
[build command]        # Production build
[migrate command]      # Run database migrations
```

## Conventions
- [Naming conventions]
- [File organization rules]
- [Import ordering]
- [Error handling patterns]
- [Commit message format]

## Gotchas & Caveats
- [Thing that trips people up #1]
- [Thing that trips people up #2]

## Environment Variables
[List required env vars and what they're for, without actual values]

## Testing
- [What test framework is used]
- [What level of testing is expected]
- [How to run specific test suites]

## Deployment
- [How deployments work]
- [Environment differences]
- [How to roll back]
```

---

## ARCHITECTURE.md Template

```markdown
# Architecture Overview

## System Diagram
[Describe or diagram the major components and their relationships]

## Components

### [Component 1]
- **Purpose**: [what it does]
- **Location**: [where in the codebase]
- **Key interfaces**: [how other components interact with it]

### [Component 2]
[repeat pattern]

## Data Flow
[How data moves through the system for key operations]

## Data Model
[Core entities and their relationships — can reference Prisma schema, migrations, etc.]

## External Dependencies
- **[Service]**: [what it's used for, how it's integrated]

## Key Design Decisions
- [Decision 1]: [Why it was made]
- [Decision 2]: [Why it was made]
```

---

## CONTRIBUTING.md Template

```markdown
# Contributing to [Project Name]

## Getting Started

### Prerequisites
- [Required tools and versions]

### Setup
1. [Step-by-step local setup]

## Development Workflow

### Branch Naming
[Convention, e.g., `feature/short-description`, `fix/issue-number`]

### Making Changes
1. Create a branch from `[base branch]`
2. Make your changes
3. Write/update tests
4. Run `[test command]` and `[lint command]`
5. Submit a PR

### PR Process
- [Required reviewers]
- [CI checks that must pass]
- [Any other requirements]

### Commit Messages
[Format, e.g., conventional commits]

## Code Style
[Linter/formatter info, any manual conventions]

## Testing
- [What to test]
- [How to run tests]
- [Coverage expectations]
```
