# PROJECT.md

## Purpose

This repo holds two Claude Code skill pipelines:
1. **Resume coaching** — from raw resume paste to mock interview
2. **Room makeover** — from intent capture to visual output and shopping list

Skills are invoked with `/skill-name` inside Claude Code sessions.

---

## Directory Layout

```
misc-skills/
  CLAUDE.md              ← operating order, thinking principles, output style
  PROJECT.md             ← this file
  PERSONA.md             ← 小喬 persona, tone, language
  README.md              ← user-facing overview
  .claude/
    skills/
      q/                 ← context loader
      resume-coach/      ← resume pipeline entry point
      resume-parser/
      resume-diagnoser/
      resume-recruiter/
      resume-rewriter/
      resume-hiring-manager/
      room-coach/        ← room pipeline entry point
      interior-design/
      room-refresh/
      rental-refresh/
```

---

## Skill Pipelines

### Resume Pipeline

Entry point: `/resume-coach`

```
/resume-parser          clean raw PDF/DOCX paste → structured sections
/resume-diagnoser       ATS audit → top 5 fixes ranked by impact
/resume-recruiter       keyword intelligence → missing signals, 2026 trends
/resume-rewriter        XYZ bullet rewrite → quantified, keyword-layered bullets
/resume-hiring-manager  mock interview → scores, hireability rating, study plan
```

`/resume-coach` handles input collection (target role, industry, seniority, company type, resume text) and orchestrates the stages in order.

Context flows forward: every stage after `/resume-parser` uses the cleaned resume. Missing keywords from `/resume-recruiter` are passed directly into `/resume-rewriter`.

---

### Room Pipeline

Entry point: `/room-coach`

```
/interior-design        full redesign in a named style → image via /banana
/room-refresh           budget refresh, furniture stays → tiered shopping list ($300/$600/$1500+)
/rental-refresh         renter-safe → bond-back removal method per item
```

`/room-coach` routes based on three key signals:
- Renting → `/rental-refresh`
- Owning + keeping furniture → `/room-refresh`
- Owning + open to changes → `/interior-design`

Style library: 12 named styles across Cosy & Warm / Bold & Editorial / Calm & Feminine.

---

## External Dependencies

### `/banana` (image generation)
- Repo: [AgriciDaniel/banana-claude](https://github.com/AgriciDaniel/banana-claude)
- Installed globally at: `~/.claude/skills/banana/`
- Configured via: `~/.claude/settings.json` (MCP server `nanobanana-mcp`)
- Required for: `/interior-design`, `/rental-refresh` (optional), `/room-refresh` (optional)
- Fallback: room skills output a copyable Gemini prompt if `/banana` is unavailable

---

## Commands

No build step. Skills are plain Markdown files — no compilation required.

```bash
# Start a session
/q                        # load CLAUDE.md + PROJECT.md + PERSONA.md

# Resume work
/resume-coach             # full pipeline entry point

# Room work
/room-coach               # routing entry point

# Image generation (requires /banana installed and configured)
/banana setup             # configure API key
/banana generate "..."    # generate standalone image
```

---

## Conventions

- Each skill is a single `SKILL.md` file inside `.claude/skills/<skill-name>/`
- Skills define: purpose, trigger conditions, required inputs, what to do, output format
- Entry-point skills (`resume-coach`, `room-coach`) handle input collection and invoke downstream skills — users should only need to know the entry point
- PERSONA.md governs tone throughout all skills (Cantonese, 小喬 persona, 主公 address)
