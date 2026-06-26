---
name: rental-refresh
description: Redesign a rental room with strict landlord-friendly, bond-safe rules. No painting, no drilling, no permanent fixtures, no floor damage. Returns a visualisation, a ranked shopping list with bond-back removal methods, and two cheaper substitutes for the most expensive item. Use when the user rents their home, wants to redecorate without losing their bond, or asks for removable, damage-free, or renter-friendly room ideas.
---

# Skill: rental-refresh — Renter-Friendly Room Redesign

## Purpose
Transform a rental room without risking the bond. Every recommendation is reversible at move-out. No drilling, no painting, no permanent fixtures, no floor damage — ever.

## When to Trigger
- User mentions renting, landlord, bond, deposit, or lease
- User asks for "damage-free", "removable", "renter-friendly", or "no drilling" ideas
- User wants to decorate but says they can't paint or drill
- User is moving into a new rental and wants to make it feel like home

## The Four Hard Rules (Never Violate)

These apply to every single recommendation — no exceptions:

| Rule | What's banned | What's allowed instead |
|------|---------------|------------------------|
| **No painting walls** | Any permanent wall paint | Peel-and-stick wallpaper, removable wall decals, large fabric tapestries, giant framed prints |
| **No drilling holes** | Screws, nails, rawl plugs into walls or ceilings | Command strips (rated weight), leaning art, free-standing pieces, tension rods |
| **No permanent fixtures** | Hardwired lighting, built-in shelves, ceiling fans | Floor lamps, plug-in pendant lights (with hook), free-standing shelving units, tension rod shelves |
| **No floor damage** | Adhesive to floors, furniture dragging on floorboards | Rugs over all floor types, felt pads under furniture legs |

If any recommended item could violate a rule, flag it and substitute immediately.

## Style Library Reference

### Cosy & Warm
| Style | Renter-Friendly Signature |
|---|---|
| **Modern Japandi** | Peel-and-stick rice paper accent panel, linen cushions, floor lamp with natural shade, rattan tray |
| **Cosy Farmhouse** | Fabric tapestry (barn or floral), cream throw, terracotta pots, dried pampas in freestanding vase |
| **Warm Minimalism** | Large leaning mirror, bouclé cushion, single sculptural vase on floor shelf, neutral rug |
| **Coastal Grandmother** | Gauze curtain on tension rod, woven basket shelf, blue-and-white ceramics, rattan floor lamp |

### Bold & Editorial
| Style | Renter-Friendly Signature |
|---|---|
| **Dark Academia** | Dark green tapestry or curtain on tension rod, stacked hardcovers on freestanding shelf, brass floor lamp, Persian rug |
| **Modern Industrial** | Matte black freestanding shelving, Edison-style floor lamp, concrete planter, charcoal cushions |
| **Maximalist Eclectic** | Leaning gallery ledge (no drill), jewel-toned cushions, layered rugs, trailing plants in freestanding planters |
| **Mid-Century Modern** | Geometric rug, mustard floor lamp with arc shade, leaning print, freestanding walnut-effect sideboard |

### Calm & Feminine
| Style | Renter-Friendly Signature |
|---|---|
| **Soft French Apartment** | Sheer curtain on tension rod, leaning gilt-edge mirror, dried florals in floor vase, pale runner rug |
| **Quiet Luxury** | Heavy linen curtains on tension rod, cashmere-feel throw, tonal ceramic, nothing superfluous |
| **Garden Room** | Botanical peel-and-stick panel or tapestry, potted ferns on freestanding shelf, rattan lamp, botanical prints on ledge |
| **Korean Minimalist** | White linen curtains, low freestanding shelf, single stoneware vase, off-white rug, minimal floor lamp |

## Required Inputs
Collect missing ones one at a time:

1. **Room type** — bedroom, living room, home office, studio
2. **Style** — from the Style Library, or describe their own vibe
3. **Current room description** — brief description or photo: existing furniture, wall colour, floor type (carpet / tiles / floorboards)
4. **Budget** — default is $400; accept any amount and scale accordingly
5. **Location** — city/country to recommend local stores; default to IKEA / Kmart / Target / Amazon / AliExpress

Optional:
- **Existing pieces they love** — items to build around, not replace
- **Landlord restrictions beyond the four rules** — e.g. no blu-tack, no tension rods on painted doorframes

## What to Do

### Step 1 — Confirm the Brief

```
Got it. Here's the renter-friendly brief:

- Room: [room type]
- Style: [style name]
- Budget: $[amount]
- Floor type: [carpet / tiles / floorboards]
- Location: [city → store set]
- Bond rules: no paint · no drilling · no permanent fixtures · no floor damage

Building your reversible redesign...
```

### Step 2 — Generate the Visualisation (with /banana)

Construct a photo-realistic interior prompt showing the redesigned room with only renter-safe changes applied. Include:
- Existing furniture in place (unchanged)
- Tapestry or peel-and-stick panel on feature wall (not painted)
- Rug over existing floor
- Floor lamp and plug-in pendant where appropriate
- Style-accurate soft furnishings

Invoke `/banana generate` with `imageSize: 2K`, aspect ratio `4:3`.

If `/banana` is unavailable, output the prompt as a copyable block.

**Prompt structure:**
```
[Room type] interior in [style name] style. Renter-friendly styling: existing furniture unchanged,
[tapestry / peel-and-stick panel / large leaning art] on feature wall instead of paint,
[rug type] over [floor type], [freestanding shelving / floor lamp description],
[soft furnishing details: cushions, throw, curtains on tension rod].
Warm [lighting mood] from floor lamps and natural light. No drilling, no painting, no permanent changes visible.
Shot on Sony A7R IV, 24mm lens, f/4. Editorial interior photography, wide-angle natural perspective.
```

### Step 3 — Shopping List (8–10 Items, Ranked by Impact-per-Dollar)

Rank items by how much visual change they deliver per dollar spent — not by price alone.

For each item, provide:

| # | Item | Description | Store | Est. Price | Bond-Back Removal |
|---|------|-------------|-------|------------|-------------------|
| 1 | Feature wall solution | [Tapestry / peel-and-stick panel / large leaning print — most impact] | [store] | $XX | Pull slowly from corner; use hair dryer on peel-and-stick adhesive to release without residue |
| 2 | Rug | [Size, colour, material matching style] | IKEA / Kmart | $XX | Roll up and remove; check floor underneath for any marks |
| 3 | Curtains on tension rod | [Description; tension rod fits inside window recess — no screws] | [store] | $XX | Collapse tension rod, remove panels; no marks left |
| 4 | Floor lamp | [Description] | IKEA | $XX | Unplug and remove; no installation |
| 5 | Freestanding shelving | [Description] | IKEA / Amazon | $XX | Disassemble and remove; no wall contact |
| 6–10 | [Cushions, throws, plants, art on ledge, tray, vase, etc.] | ... | ... | Simply pack up at move-out |

**Removal method vocabulary to use:**
- Command strips: "Remove tab by stretching slowly downward at 45°; residue lifts cleanly — tested to leave no paint damage"
- Peel-and-stick wallpaper: "Peel from corner with hair dryer on low heat; removes fully without wall damage on most rental-grade paint"
- Tension rods: "Collapse and lift out; zero contact with wall surface"
- Leaning art/mirrors: "Simply lift away; no wall contact"
- Rugs: "Roll up; inspect floor for any marks from furniture feet (use felt pads to prevent)"
- Tapestry: "Remove Command hooks by stretching tab; fold tapestry and pack"

### Step 4 — Two Cheaper Substitutes for the Most Expensive Item

Identify the most expensive item on the shopping list. Provide two alternatives at lower price points that achieve a similar visual effect:

```
### Cheaper Substitutes for [Most Expensive Item] ($XX)

**Option A — ~$[lower price]:**
[Description: achieves 80% of the same effect at half the cost]
Where to find: [store]

**Option B — ~$[lowest price]:**
[Description: the budget version — still renter-safe, lower visual impact]
Where to find: [store]
```

### Step 5 — Bond-Back Checklist

After the shopping list, always include:

```
### Move-Out Checklist (Get Your Bond Back)

Before handing keys back:
- [ ] Peel all Command strips — stretch tab slowly downward, do not pull outward
- [ ] Remove peel-and-stick wallpaper panels with hair dryer on low
- [ ] Collapse tension rods and remove curtain panels
- [ ] Roll up rugs; inspect floors for indentations (most landlords accept normal wear)
- [ ] Check all walls for any accidental marks; wipe with Magic Eraser if needed
- [ ] Disassemble any freestanding shelving; check for any lean marks on wall

Everything on this list was designed to leave the room in its original condition.
```

## Conflict Handling

- **"Can I use blu-tack?"** — Only on white walls with fresh paint; always patch test first; include warning in output
- **"My landlord said no tension rods on door frames"** — Swap to freestanding room divider or curtain panel hung from Command hook on ceiling (weight-rated)
- **Budget under $150** — Focus on rug + one tapestry + two cushion covers only; these three items are the highest impact per dollar in any rental
- **Studio apartment** — Treat the whole room as one zone; use rugs to define areas instead of furniture arrangement changes
- **Carpet floors** — Skip rug recommendation (or keep for texture layer); focus budget on wall and textile elements instead
