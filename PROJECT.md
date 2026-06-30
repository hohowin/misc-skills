# PROJECT.md

## Purpose

This repo holds three Claude Code skill pipelines:
1. **Resume coaching** — from raw resume paste to mock interview
2. **Room makeover** — from intent capture to visual output and shopping list
3. **LinkedIn** — from writing posts to engagement analytics to profile optimization

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
      linkedin-coach/           ← linkedin pipeline entry point
      linkedin-post-writer/
      linkedin-content-planner/
      linkedin-comment-drafter/
      linkedin-reply-handler/
      linkedin-engager-analytics/
      linkedin-thread-monitor/
      linkedin-hook-extractor/
      linkedin-humanizer/
      linkedin-profile-optimizer/
      linkedin-employee-advocacy/
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

### LinkedIn Pipeline

Entry point: `/linkedin-coach`

```
/linkedin-post-writer        draft a new post using 16 2026 hook formulas, goal-matched
/linkedin-content-planner    7-day calendar → pillars, hooks, posting times, daily comment targets
/linkedin-comment-drafter    top-level comment on any post URL → 1-3 variants, reaction pick
/linkedin-reply-handler      reply to an existing comment thread from its URL
/linkedin-engager-analytics  who liked/commented on a post → ICP-tiered roster + DM openers
/linkedin-thread-monitor     which of your comments got author replies → hot/warm/cool/dormant
/linkedin-hook-extractor     reverse-engineer a viral post → formula ID, template, audit
/linkedin-humanizer          scrub AI tells from any draft; --mode audit for pass/fail review
/linkedin-profile-optimizer  full profile audit + rewrite: headline, About, Featured, 6 more
/linkedin-employee-advocacy  14-day team advocacy launch + governance + ROI tracking
```

`/linkedin-coach` routes based on what the user wants to do — write, plan, engage, analyse, audit, profile, or advocacy. Passes all collected context forward so the user never answers the same question twice.

External integration: several skills use **Apify** to fetch LinkedIn post/comment data without a LinkedIn login. Manual URL-paste fallback available when no Apify token is set.

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

# LinkedIn work
/linkedin-coach           # routing entry point for all LinkedIn skills

# Image generation (requires /banana installed and configured)
/banana setup             # configure API key
/banana generate "..."    # generate standalone image
```

---

## Conventions

- Each skill is a single `SKILL.md` file inside `.claude/skills/<skill-name>/`
- Skills define: purpose, trigger conditions, required inputs, what to do, output format
- Entry-point skills (`resume-coach`, `room-coach`, `linkedin-coach`) handle input collection and invoke downstream skills — users should only need to know the entry point
- PERSONA.md governs tone throughout all skills (Cantonese, 小喬 persona, 主公 address)
