# Skill: resume-parser — Resume Cleanup & Section Extractor

## Purpose
Clean and structure messy resume text pasted from a PDF or Word doc. Removes formatting artefacts, reconstructs broken lines, and outputs a clearly labelled plain-text version ready for any resume skill.

## When to Trigger
- User pastes resume text that looks garbled, broken, or weirdly formatted
- User says "here's my resume" and the text has obvious PDF/DOCX paste artefacts
- User asks to "clean up" or "parse" their resume before running another skill
- Text has: random line breaks mid-sentence, columns merged together, dates on wrong lines, bullets replaced with symbols, or repeated headers

## Required Input
Just the raw pasted text — no other inputs needed. Ask for it if not provided:

> "Paste your resume text directly into the chat — copy it straight from your PDF or Word doc, formatting artefacts and all."

## What to Do

### Step 1 — Detect and Flag Artefacts
Before cleaning, identify and note what went wrong in the paste:
- Broken lines (sentence split across two lines with no logical break)
- Column bleed (left and right columns merged into one stream)
- Corrupted bullets (• replaced with ?, â€¢, *, or lost entirely)
- Date misalignment (dates appear on a separate line from the job they belong to)
- Header repetition (name/contact info repeated mid-document)
- Special characters that are garbled (em-dashes, accents, quotes)
- Section labels missing or merged into adjacent text

Note these briefly before outputting the cleaned version.

### Step 2 — Reconstruct and Clean
Apply these fixes:

1. **Merge broken sentences** — if a line ends mid-phrase and the next line continues it, join them.
2. **Restore columns** — if two-column layout bled together, reconstruct left-to-right reading order: contact info block first, then sections in logical order.
3. **Normalise bullets** — replace ?, â€¢, -, *, or missing bullets with a clean `•`.
4. **Anchor dates** — move floating dates to the end of the job title line they belong to.
5. **Remove repeated headers** — deduplicate name/contact info that appeared multiple times.
6. **Fix garbled characters** — restore en-dashes, em-dashes, apostrophes, and quotes.
7. **Preserve all content** — do not rewrite, summarise, or drop any text. Only fix structure.

### Step 3 — Output Structured Sections
Output the cleaned resume in clearly labelled sections:

```
=== CONTACT ===
[Name, email, phone, LinkedIn, location]

=== SUMMARY ===
[Summary or objective paragraph]

=== EXPERIENCE ===
[Job 1 — Company | Title | Dates]
• Bullet
• Bullet

[Job 2 — ...]

=== SKILLS ===
[Skills list]

=== EDUCATION ===
[Degree | Institution | Year]

=== OTHER ===
[Certifications, awards, volunteer work, publications — anything that doesn't fit above]
```

If a section is absent from the resume, omit it from the output — don't add placeholder text.

### Step 4 — Readiness Check
After outputting the cleaned text, tell the user:

- How many sections were detected
- Any content that couldn't be confidently reconstructed (flag it with `[CHECK: ...]`)
- Which skill to run next based on what they said they want to do:
  - Diagnose formatting issues → `/resume-diagnoser`
  - Keyword analysis → `/resume-recruiter`
  - Rewrite bullets → `/resume-rewriter`
  - Mock interview → `/resume-hiring-manager`

## Output Format

```
## Resume Parser

### ⚠️ Artefacts Detected
- [List what was wrong with the paste]

---

### ✅ Cleaned Resume

=== CONTACT ===
...

=== SUMMARY ===
...

=== EXPERIENCE ===
...

=== SKILLS ===
...

=== EDUCATION ===
...

---

### 📋 Readiness Check
Sections detected: [X]
Flags: [CHECK: ...] items if any, otherwise "None — looks clean."

Ready for:
→ Run `/resume-diagnoser` to audit ATS formatting
→ Run `/resume-recruiter` for keyword analysis
→ Run `/resume-rewriter` to strengthen bullets
→ Run `/resume-hiring-manager` for a mock interview
```
