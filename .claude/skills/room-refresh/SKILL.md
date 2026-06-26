---
name: room-refresh
description: Refresh a room on a budget without moving furniture. All existing furniture stays in place — only wall colour, bedding, rugs, lamps, and small decor change. Returns the 5 highest-impact changes in priority order with real prices from IKEA, Kmart, Target, or Amazon, across three budget tiers ($300 / $600 / $1500+). Use when the user wants to refresh, redecorate on a budget, or transform a room without buying new furniture.
---

# Skill: room-refresh — Budget Room Refresh (Furniture Stays Put)

## Purpose
Transform a room's feel without moving or replacing any large furniture. Returns prioritised shopping lists with real product examples across three budget tiers.

## When to Trigger
- User says "refresh my room", "redecorate on a budget", "make my room look better without buying furniture"
- User asks "what can I change cheaply", "quick room update", "soft furnishing ideas"
- User wants to improve a room but has a limited budget or rented property constraints
- User asks about rugs, cushions, curtains, wall art, or lamp upgrades

## Hard Constraints (Never Violate)
These rules apply to every recommendation — no exceptions:
- **All large furniture stays exactly where it is** — bed, sofa, desk, shelving, dining table, wardrobe
- **No moving furniture** — not even repositioning
- **Only these items can change:**
  - Wall colour (one accent wall is acceptable; full repaint is a budget call)
  - Bedding, cushions, throws, curtains
  - Rugs
  - Lamps and lampshades
  - Wall art and small decor (vases, books, trays, plants, candles)

## Style Library Reference

### Cosy & Warm
| Style | Soft Furnishing Signature |
|---|---|
| **Modern Japandi** | Linen cushions in oat/sage, cream waffle throw, woven rattan tray, single ceramic vase, bamboo lamp |
| **Cosy Farmhouse** | Cream quilt, woven cushions, terracotta planter, dried pampas grass, gingham or check throw |
| **Warm Minimalism** | Bouclé cushion cover (one), stone-coloured throw, single sculptural vase, neutral linen curtains |
| **Coastal Grandmother** | Blue-and-white cushion, woven basket, gauze curtains, rattan lamp, white ceramic pitcher |

### Bold & Editorial
| Style | Soft Furnishing Signature |
|---|---|
| **Dark Academia** | Deep green or burgundy accent wall, leather-look cushion, stacked hardcovers, brass desk lamp, Persian rug |
| **Modern Industrial** | Charcoal cushions, Edison-style lamp, concrete-look planter, grid-print throw, matte black frames |
| **Maximalist Eclectic** | Jewel-toned velvet cushions, layered patterned rug, gallery wall with mixed frames, trailing plant |
| **Mid-Century Modern** | Mustard or teal cushion, geometric rug, paper pendant lampshade, wooden tray with greenery |

### Calm & Feminine
| Style | Soft Furnishing Signature |
|---|---|
| **Soft French Apartment** | Ruffled linen cushion, fresh or dried florals, gilt-frame print, gauze curtain panel, pale rug |
| **Quiet Luxury** | Tonal throw in one colour family, heavy linen curtains, unbranded ceramic, nothing excessive |
| **Garden Room** | Botanical print cushion, potted fern or monstera, vintage botanical art print, rattan lamp |
| **Korean Minimalist** | White cotton duvet cover, single stoneware bud vase, off-white linen curtains, low minimal lamp |

## Required Inputs
Collect missing ones one at a time:

1. **Room type** — bedroom, living room, home office, dining room
2. **Style** — pick from the Style Library, or describe a vibe in their own words
3. **Current room description** — brief description or photo: existing furniture colours, wall colour, flooring type
4. **Location** — city or country (to recommend the right stores; default to IKEA / Kmart / Target / Amazon if unknown)
5. **Starting budget** — the user's immediate budget (used for Tier 1); Tier 2 and 3 are shown regardless

Optional:
- **What they hate most about the room right now** — used to rank changes by impact
- **One thing they want to keep or love** — preserved in all three tiers

## What to Do

### Step 1 — Confirm the Brief

```
Got it. Here's the brief:

- Room: [room type]
- Style target: [style name]
- Furniture: stays exactly as-is
- Starting budget: $[amount]
- Location: [city/country → store set]

Building your three-tier refresh plan...
```

### Step 2 — Identify the 5 Highest-Impact Changes

For the chosen style and room type, rank the five changes that will most transform the room's feel within the soft-furnishing constraint. Impact is judged by visual surface area and style signal — a rug covers more visual ground than a vase.

**Default impact ranking (adjust for room type):**

1. **Rug** — largest visual anchor; defines the zone and colour story
2. **Curtains / window treatment** — frames the room, controls light quality
3. **Accent wall paint** — single wall colour shift (skip if renting without permission)
4. **Bedding or sofa throw + cushion covers** — the room's focal point textile
5. **Lamp(s) + lampshade** — controls mood lighting; one good lamp beats three bad ones

For living rooms: swap bedding for sofa throw + cushion covers.
For home offices: swap bedding for desk lamp + one piece of wall art.

### Step 3 — Build Three Tiers

For each tier, list every item with:
- Product description (specific enough to find on the store's site)
- Estimated price in local currency
- Store name (IKEA / Kmart / Target / Amazon — pick the best value source per item)
- Why this item has the highest impact at this tier

#### Tier 1 — Budget Refresh ($300 or user's stated budget)
The 5 highest-impact changes only. No compromises on the most visible items — sacrifice quantity for quality. Every dollar goes toward what shows.

```
### Tier 1 — Budget Refresh (~$300)

| Priority | Change | Item | Store | Est. Cost |
|----------|--------|------|-------|-----------|
| 1 | Rug | [specific description, size] | IKEA | $89 |
| 2 | Curtains | [description, 2 panels] | Kmart | $45 |
| 3 | Accent wall | [paint colour name] | Bunnings/Home Depot | $35 |
| 4 | Throw + 2 cushion covers | [description] | Target | $68 |
| 5 | Lamp + shade | [description] | IKEA | $49 |
| | | | **Total** | **~$286** |

**Biggest bang:** [one sentence on which item transforms the room most]
```

#### Tier 2 — Mid-Budget ($600)
Everything from Tier 1, plus the next layer of impact: better quality rug, additional decor, second lamp, wall art.

```
### Tier 2 — Mid-Budget (~$600)
Everything from Tier 1, plus:

| Addition | Item | Store | Est. Cost |
|----------|------|-------|-----------|
| Rug upgrade | [better version of Tier 1 rug] | IKEA | +$60 |
| Wall art | [description: print/frame combo] | Amazon | $45 |
| Plants | [specific variety + pot] | Kmart | $35 |
| Extra cushion covers (2) | [description] | Target | $30 |
| Bedside/floor lamp (2nd) | [description] | IKEA | $55 |
| | | **Tier 2 Total** | **~$590** |
```

#### Tier 3 — Full Glow-Up ($1500+)
Everything from Tier 2, plus upgraded quality on every item, full curtains on all windows, premium rug, styled gallery wall, and a signature statement piece.

```
### Tier 3 — Full Glow-Up (~$1500)
Everything from Tier 2, upgraded:

| Upgrade | Item | Store | Est. Cost |
|---------|------|-------|-----------|
| Premium rug | [quality step-up, e.g. wool blend] | [store] | $299 |
| Full curtain set (all windows) | [description] | [store] | $180 |
| Gallery wall | [5–7 prints + frames] | Amazon/IKEA | $150 |
| Linen duvet cover set | [description] | [store] | $120 |
| Statement lamp | [specific, unique piece] | [store] | $179 |
| Scented candles + tray | [brand/style] | Target | $55 |
| Premium throw (cashmere-feel) | [description] | [store] | $89 |
| | | **Tier 3 Total** | **~$1,450** |
```

### Step 4 — Visualise Each Tier (Optional, with /banana)

If `/banana` is available and the user wants to see the result:

Construct three generation prompts — one per tier — showing the same room at each level of investment. Each prompt:
- Keeps the existing large furniture in the same position
- Reflects only the soft furnishing changes for that tier
- Uses the same camera angle
- Adds the style-appropriate lighting

Invoke `/banana generate` three times with `imageSize: 2K`, aspect ratio `4:3`.

If `/banana` is unavailable, output the three prompts as copyable blocks.

### Step 5 — Quick Wins Note

After the tiers, always add:

```
### Do These First (Free)
Before spending anything:
- [e.g. Declutter: remove 3 items from the nightstand — negative space costs nothing]
- [e.g. Rearrange existing cushions asymmetrically — off-centre looks more intentional]
- [e.g. Move the lamp to [position] for softer ambient light]
```

Free changes that move the needle are always listed first.

## Conflict Handling

- **Renting:** If user mentions renting, skip accent wall paint and replace with peel-and-stick alternatives or gallery wall focus
- **Style mismatch:** If the user's current furniture colour (e.g. dark espresso) clashes with chosen style (e.g. Korean Minimalist), flag it and suggest the closest compatible style or a neutral bridge strategy
- **"I love my [item]":** Lock that item into all three tiers, build the palette around it
