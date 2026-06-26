---
name: interior-design
description: Redesign a room in a specific interior style. Collects room type, style, what to change, lighting mood, and vibe, then constructs a Gemini-ready image generation prompt. Works with /banana for generation. Use when the user wants to redesign a room, visualise an interior style, redecorate, or reimagine a space.
---

# Skill: interior-design — Room Redesign by Style

## Purpose
Take a room and redesign it in a named interior style. Constructs a precise, photo-realistic image generation prompt based on what the user wants to change and how they want the room to feel.

## When to Trigger
- User says "redesign my room", "redecorate", "what would this room look like in [style]"
- User shares a room photo and wants to visualise a new style
- User wants to reimagine a space — living room, bedroom, kitchen, bathroom, home office

## Style Library

### Cosy & Warm
| Style | Key Signature |
|---|---|
| **Modern Japandi** | Neutral palette, light woods, soft linen, low-profile furniture, minimal but warm |
| **Cosy Farmhouse** | Cream walls, woven textures, vintage finds, dried flowers, terracotta accents |
| **Warm Minimalism** | Beige + ivory + soft brown, one statement piece, lots of negative space |
| **Coastal Grandmother** | Slipcovered linen sofa, blue-and-white ceramics, woven baskets, soft natural light |

### Bold & Editorial
| Style | Key Signature |
|---|---|
| **Dark Academia** | Deep green or burgundy walls, brass fixtures, leather, books as decor, moody lighting |
| **Modern Industrial** | Exposed materials, matte black metal, concrete textures, leather sofa, Edison-style bulbs |
| **Maximalist Eclectic** | Layered patterns, jewel tones, gallery wall, mixed-era furniture, plants everywhere |
| **Mid-Century Modern** | Walnut wood, mustard + teal accents, tapered legs, geometric rug |

### Calm & Feminine
| Style | Key Signature |
|---|---|
| **Soft French Apartment** | Cream walls, antique mirror, ruffled linen, fresh florals, pale wood floors |
| **Quiet Luxury** | Tonal palette (one colour family), high-quality textures, no logos, restrained styling |
| **Garden Room** | Botanical wallpaper or murals, rattan, ferns and fiddle-leaf figs, vintage botanical prints |
| **Korean Minimalist** | Pale oak, off-white walls, single ceramic vase, soft cotton bedding, low bed frame |

## Required Inputs
Collect missing ones one at a time before generating:

1. **Room type** — living room, bedroom, kitchen, home office, bathroom, dining room
2. **Style name** — from the Style Library above, or describe a custom style
3. **What to change** — furniture / paint / textiles / decor (can be one or all)
4. **Lighting mood** — warm and cosy / bright and airy / moody and intimate / describe their own
5. **Vibe sentence** — one sentence about how they want to feel in the room
6. **Original photo** — optional but strongly recommended; if provided, the output matches the same camera angle and preserves windows, doors, and ceiling height

If the user provides a photo, ask which elements to preserve (structural: windows, doors, ceiling height are always kept).

## What to Do

### Step 1 — Confirm the Brief
Once all inputs collected, confirm back:

```
Here's your redesign brief:

- Room: [room type]
- Style: [style name]
- Changing: [furniture / paint / textiles / decor]
- Lighting: [lighting mood]
- Vibe: "[vibe sentence]"
[- Photo: [yes — matching original angle] / [no — fresh composition]]

Generating prompt now...
```

### Step 2 — Construct the Generation Prompt

Map the chosen style to its visual signature from the Style Library. Build a precise, photo-realistic interior photography prompt using this structure:

```
[Room type] interior redesigned in [style name] style.
[Style-specific elements: wall treatment, flooring, key furniture pieces with material/finish,
textiles, decorative objects — all matching the style signature.]
[What was NOT changed: original windows, doors, and ceiling height preserved.]
[Lighting: specific light quality, direction, and mood — e.g. "warm afternoon light
streaming through existing windows, casting long soft shadows."]
[Atmosphere sentence: how the room feels, not just looks.]
[If photo provided: "Photographed from the same angle as the original photo."
Otherwise: "Interior photography, wide-angle architectural shot, natural perspective."]
Shot on Sony A7R IV, 24mm lens, f/4. Editorial interior photography.
```

**Style-to-prompt translations (use these as base, expand with specific details):**

| Style | Prompt Anchors |
|---|---|
| Modern Japandi | "white oak floating shelves, linen cushion covers, low-profile platform bed/sofa, neutral plaster walls, wabi-sabi ceramic accents" |
| Cosy Farmhouse | "shiplap accent wall, cream linen slipcover, galvanised metal accents, dried pampas grass, terracotta planter, distressed wood side table" |
| Warm Minimalism | "warm greige walls, single bouclé armchair, travertine side table, hidden storage, bare but deliberate arrangement" |
| Coastal Grandmother | "slipcovered sofa in oatmeal linen, blue-and-white ceramic vase, rattan tray, woven jute rug, gauze curtains" |
| Dark Academia | "forest green or burgundy wall paint, antique brass floor lamp, cognac leather reading chair, stacked hardcover books, Persian-style rug, moody warm light" |
| Modern Industrial | "exposed concrete texture wall, matte black steel shelving, cognac leather sofa, Edison pendant bulbs, raw wood dining table" |
| Maximalist Eclectic | "gallery wall with mixed frames, jewel-toned velvet sofa, layered Moroccan rug, sculptural floor lamp, trailing pothos and monstera" |
| Mid-Century Modern | "walnut credenza with tapered legs, mustard accent chair, geometric wool rug in teal and amber, tulip side table, Noguchi-style paper pendant" |
| Soft French Apartment | "cream limewash walls, gilt-edged antique mirror, ruffled white linen curtains, fresh peonies in earthenware, pale herringbone parquet floor" |
| Quiet Luxury | "tonal palette in one colour family, cashmere throw, unbranded ceramic, heavy linen drapes, nothing superfluous" |
| Garden Room | "botanical print wallpaper, curved rattan armchair, potted fiddle-leaf fig and ferns, vintage botanical print in simple frame, natural light through tall windows" |
| Korean Minimalist | "pale oak bed frame close to floor, off-white walls, single stoneware vase, folded cotton bedding in soft cream, no visual clutter" |

### Step 3 — Generate or Hand Off

**If `/banana` is available (preferred):** Invoke `/banana generate` with the constructed prompt. Pass `imageSize: 2K` and aspect ratio `4:3` (standard interior photo) or `16:9` (wide room shot).

**If no image tool available:** Output the prompt in a copyable block so the user can paste it into their preferred image generator (Midjourney, DALL-E, Gemini, etc.).

```
Ready to generate. Here's your prompt:

---
[Full constructed prompt]
---

Paste this into your image generator, or run /banana generate with it.
```

### Step 4 — Offer Refinements
After output, offer three quick-adjust options:

```
Want to tweak it?
- "lighter" — shift lighting brighter and airier
- "darker" — push toward moody and intimate
- "more [style name]" — amplify the signature elements
- "show me [different style]" — regenerate in a different style from the library
```

## Output Notes
- Always preserve windows, doors, and ceiling height in the prompt — these are structural and non-negotiable
- Never change the room type (a bedroom stays a bedroom)
- If the user's vibe sentence conflicts with the style (e.g. "cosy and romantic" but chose Modern Industrial), flag it and suggest the closest match from the Style Library
