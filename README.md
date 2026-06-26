# repo-boilerplate

A starter template with a curated set of Claude Code skills and plugins pre-installed. Copy this repo as the foundation for new projects so every session starts with the right tooling already in place.

---

## Skills

Skills live in `.claude/skills/` (Claude Code) and `.agents/skills/` (agent harness) and are invoked with `/skill-name`.

### Planning & Discovery

| Skill | Invoke | Description |
|-------|--------|-------------|
| **grill-me** | `/grill-me` | Stress-tests a plan by interviewing you relentlessly until every decision branch is resolved. Run before generating any doc. |
| **prd** | `/prd` | Generates a detailed Product Requirements Document — clear, actionable, and ready for implementation. |
| **plan** | `/plan` | Generates `docs/plan.md` — phase plan, locked decisions, KPI summary, risk register, and compliance notes. |
| **architecture** | `/architecture` | Generates `docs/architecture.md` — service architecture, integration patterns, tech stack, and security model. |
| **usecase** | `/usecase` | Generates `docs/use-cases.md` — end-to-end interaction flows with Mermaid sequence diagrams for every actor. |
| **deliverables** | `/deliverables` | Generates `docs/deliverables.md` — phase-by-phase deliverables with step-by-step "how to try it" guides. |

> Typical flow: `/grill-me` → `/prd` → `/plan` → `/architecture` → `/usecase` → `/deliverables`

### UI & Frontend

| Skill | Invoke | Description |
|-------|--------|-------------|
| **impeccable** | `/impeccable [command] [target]` | Full-spectrum frontend design skill (v3.5.0, Apache 2.0). Covers design, redesign, audit, polish, animate, colorize, harden, optimize, and live browser iteration. Commands: `craft`, `shape`, `audit`, `critique`, `animate`, `bolder`, `colorize`, `delight`, `layout`, `overdrive`, `quieter`, `typeset`, `adapt`, `clarify`, `distill`, `harden`, `onboard`, `optimize`, `polish`, `init`, `document`, `extract`, `live`. |
| **ui-ux-pro-max** | `/ui-ux-pro-max` | UI/UX design intelligence database — 67 styles, 96 palettes, 57 font pairings, 25 chart types across 13 stacks (React, Next.js, Vue, Svelte, SwiftUI, React Native, Flutter, Tailwind, shadcn/ui, and more). |

### Tooling & Meta

| Skill | Invoke | Description |
|-------|--------|-------------|
| **q** | `/q` | Quick-loads all core project context files at the start of a session or when context needs refreshing. |
| **skills-required** | `/skills-required` | Audits installed skills against the project tech stack and suggests what's missing. |
| **skill-creator** | `/skill-creator` | Creates new skills from scratch, modifies existing ones, runs evals, benchmarks performance, and optimizes trigger descriptions. |
| **claude-api** | `/claude-api` | Builds, debugs, and optimizes Claude API / Anthropic SDK apps. Handles prompt caching, tool use, streaming, batch, and model migration. |
| **security-review** | `/security-review` | Runs a complete security review of pending changes on the current branch. |

---

## Plugins

Plugins are installed globally at user scope via `claude plugin install`. They extend Claude Code with additional slash commands and integrations.

| Plugin | Invoke | Description |
|--------|--------|-------------|
| **frontend-design** | `/frontend-design` | Generates distinctive, production-grade frontend interfaces — avoids generic AI aesthetics. For websites, landing pages, dashboards, and React/HTML components. |
| **code-simplifier** | `/simplify` | Reviews changed code for reuse, simplification, efficiency, and altitude cleanups, then applies fixes. Quality-focused; use `/code-review` for bug hunting. |
| **skill-creator** | `/skill-creator` | Plugin-level skill creation and optimization (complements the `.agents/skills/skill-creator` skill). |
| **claude-md-management** | `/revise-claude-md` · `/claude-md-improver` | Updates `CLAUDE.md` with session learnings and improves CLAUDE.md quality across projects. |
| **telegram** | — | Telegram bot integration for notifications and messaging within automation workflows. |

---

## Installed via

```bash
# Skills (project-local, via impeccable)
npx impeccable skills install

# Plugins (user-global)
claude plugin install frontend-design@claude-plugins-official
claude plugin install code-simplifier@claude-plugins-official
claude plugin install skill-creator@claude-plugins-official
claude plugin install claude-md-management@claude-plugins-official
claude plugin install telegram@claude-plugins-official
```

---

## Usage

1. Copy this repo as the starting point for a new project.
2. Run `/q` at the start of each session to load project context.
3. Run `/skills-required` to check if the project needs additional skills.
4. Use `/grill-me` before generating any planning or architecture document.
