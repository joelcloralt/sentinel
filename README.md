# Sentinel

Sentinel is a Claude Code skill that acts as a project guardian. It intercepts every work request before Claude takes action, scans the project for full context, identifies risks and dependencies, and delivers calibrated guidance -- ensuring nothing ships without proper assessment.

## Install

### Via skills CLI

```bash
npx skills add joelcloralt/sentinel
```

### Manual

Clone the repo and copy the skill into your Claude Code skills directory:

```bash
git clone https://github.com/joelcloralt/sentinel.git
cp -r sentinel/skills/sentinel ~/.claude/skills/sentinel
```

This installs Sentinel globally so it activates in every project.

## What It Does

Sentinel fires on every action request (build, fix, add, change, refactor, deploy, etc.) and runs a structured workflow before any code is written:

1. **Intercept** -- Catch the request before any action is taken
2. **Discover** -- Scan the project to understand the full context (code, infrastructure, brand, design, testing, business model, compliance)
3. **Assess** -- Determine what is affected, what is at risk, and what decisions need to be made
4. **Guide** -- Deliver inline guidance for small tasks or a structured brief for larger ones
5. **Hand Off** -- Let Claude execute with full context, course-correcting if needed
6. **Learn** -- Capture conventions, gotchas, and decisions in a `.sentinel/` directory for future sessions

## Key Features

- **State flow tracing**: Maps every read and write path for application state before approving fixes, catching bugs that survive surface-level patches
- **Scope awareness**: Thinks about second and third-order effects of every change
- **Calibrated guidance**: Small tasks get inline advice; larger tasks get structured briefs with tickets, testing plans, and verification checklists
- **Project memory**: Maintains a `.sentinel/` directory that accumulates conventions, gotchas, architecture decisions, and inventory over time
- **Gap detection**: Flags missing documentation (CLAUDE.md, ARCHITECTURE.md, brand guidelines, etc.) and offers to generate it

## Included References

The `skills/sentinel/references/` directory contains detailed methodologies that Sentinel loads as needed:

- **state-flow-tracing.md** -- The core analytical technique for mapping complete data lifecycles and catching inconsistent write paths
- **doc-templates.md** -- Starting templates for project documentation (CLAUDE.md, ARCHITECTURE.md, CONTRIBUTING.md, and more)

## How It Works in Practice

When you ask Claude to fix a bug, add a feature, or make any change, Sentinel activates first. It reads the project's configuration files, documentation, design system, test setup, deployment config, and any previously captured knowledge. It then delivers guidance that accounts for the full blast radius of the change -- not just the file you mentioned, but every downstream consumer, every related test, every brand guideline, and every deployment consideration.

For small changes, this takes seconds and appears as a brief note before Claude proceeds. For larger changes, Sentinel produces a structured brief with tickets, acceptance criteria, and an agent execution plan.

## Troubleshooting

### Sentinel doesn't appear in the `/` menu or available skills

The `npx skills add` command installs to `.agents/skills/sentinel/` (universal format) and creates a symlink at `.claude/skills/sentinel`. If the symlink wasn't created, Claude Code won't see the skill.

**Fix:** Verify the symlink exists in your project:

```bash
ls -la .claude/skills/sentinel
```

If it's missing, create it manually:

```bash
mkdir -p .claude/skills
ln -s ../../.agents/skills/sentinel .claude/skills/sentinel
```

Or install directly to the Claude Code skills directory:

```bash
# Project-level (this project only)
cp -r .agents/skills/sentinel .claude/skills/sentinel

# Global (all projects)
cp -r .agents/skills/sentinel ~/.claude/skills/sentinel
```

### Sentinel activates but doesn't remember anything between sessions

Sentinel stores project knowledge in a `.sentinel/` directory. As of v1.1, this directory is auto-created on first run. If you're on an older version, update the skill:

```bash
npx skills add joelcloralt/sentinel
```

## License

MIT
