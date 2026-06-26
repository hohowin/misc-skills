# misc-skills

A Claude Code skill repo with two ready-to-use coaching pipelines: one for resume improvement and job prep, one for interior design and room makeovers.

---

## Pipelines

### Resume Coach

End-to-end resume improvement — from raw paste to mock interview.

| Skill | Invoke | Role |
|-------|--------|------|
| **resume-coach** | `/resume-coach` | Entry point. Collects inputs, routes to stages, orchestrates the full pipeline. |
| **resume-parser** | `/resume-parser` | Cleans messy PDF/DOCX paste. Reconstructs broken lines, columns, bullets. Outputs structured sections. |
| **resume-diagnoser** | `/resume-diagnoser` | ATS audit. Flags formatting killers, weak bullets, missing signals. Ranks top 5 fixes by impact. |
| **resume-recruiter** | `/resume-recruiter` | Keyword intelligence. Top 15 market keywords, what's missing, 2026 trending skills, buzzwords to cut. |
| **resume-rewriter** | `/resume-rewriter` | XYZ bullet rewrite. Applies Google's formula: Accomplished X, measured by Y, by doing Z. Layers in missing keywords. |
| **resume-hiring-manager** | `/resume-hiring-manager` | Mock interview. 8 questions (5 technical + 3 behavioural), scores each answer, delivers hireability score and study plan. |

**Typical flow:**
```
/resume-coach  →  /resume-parser  →  /resume-diagnoser  →  /resume-recruiter  →  /resume-rewriter  →  /resume-hiring-manager
```

Start with `/resume-coach` — it handles input collection and routing.

---

### Room Coach

Room redesign and refresh across three approaches: full redesign, budget refresh, or renter-safe.

| Skill | Invoke | Role |
|-------|--------|------|
| **room-coach** | `/room-coach` | Entry point. Asks renting/owning, scope, budget, style, then routes to the right skill. |
| **interior-design** | `/interior-design` | Full room redesign in a named style. Generates a photo-realistic image via `/banana`. Preserves windows, doors, ceiling height. |
| **room-refresh** | `/room-refresh` | Budget refresh. Furniture stays put. Returns 5 highest-impact changes with prices from IKEA/Kmart/Target/Amazon across $300 / $600 / $1500+ tiers. |
| **rental-refresh** | `/rental-refresh` | Renter-safe redesign. No painting, no drilling, no permanent fixtures. Shopping list with bond-back removal method per item. |

**Routing logic:**
```
/room-coach
    ├── Renting?              → /rental-refresh
    ├── Owning + keep furniture → /room-refresh
    └── Owning + open to changes → /interior-design
```

Start with `/room-coach` — it asks the right questions and routes automatically.

**Image generation:** Room skills use `/banana` (Gemini image generation) when available. Install and configure it separately — see below.

---

## Utility

| Skill | Invoke | Description |
|-------|--------|-------------|
| **q** | `/q` | Quick-loads CLAUDE.md, PROJECT.md, and PERSONA.md at the start of a session. |

---

## Image Generation (Optional)

Room pipeline skills generate visuals via the `/banana` skill (Gemini Nano image generation by [@AgriciDaniel](https://github.com/AgriciDaniel/banana-claude)).

```bash
# Install
git clone https://github.com/AgriciDaniel/banana-claude.git
cd banana-claude && bash install.sh

# Configure (get a free key at https://aistudio.google.com/apikey)
/banana setup
```

If `/banana` is not configured, room skills output a copyable prompt instead.

---

## Style Library (Room Pipeline)

12 interior styles across three moods:

| Mood | Styles |
|------|--------|
| Cosy & Warm | Modern Japandi · Cosy Farmhouse · Warm Minimalism · Coastal Grandmother |
| Bold & Editorial | Dark Academia · Modern Industrial · Maximalist Eclectic · Mid-Century Modern |
| Calm & Feminine | Soft French Apartment · Quiet Luxury · Garden Room · Korean Minimalist |

---

## Usage

1. Run `/q` at the start of each session to load project context.
2. For resume work: start with `/resume-coach`.
3. For room work: start with `/room-coach`.
4. Individual skills can be invoked directly if you already know which one you need.
