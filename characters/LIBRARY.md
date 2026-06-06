# LIBRARY.md — Art Asset Library for the Comic

This is the rulebook for the image library. `manifest.json` is the index; this file
explains how it works and how to add to it. Read this before generating any art.

---

## The one rule that keeps everything consistent

**Lock the reference, then breed from the reference — never from the photo.**

1. From a real photo of the child, generate **one** clean stylized portrait per
   character. Approve it. This is the `referenceImage` (lives in `refs/`).
2. Set that character's `"locked": true` in `manifest.json`.
3. Generate **every** library asset (poses, expressions) using that locked reference
   as the image's character-reference — *not* the original photo.

If you keep going back to the photo, each generation re-interprets the real face
slightly differently and the characters drift. Lock the cartoon, generate from the
cartoon. Every asset records its origin in `derivedFrom` so drift is traceable.

---

## Generation pipeline

```
photo → ONE stylized reference (approve + lock) → refs/<name>_ref.png
      → generate poses/expressions against the locked ref
      → cut out to transparent PNG → assets/<character>/
      → add a tagged entry to manifest.json (status: ready)
```

Reuse beats regeneration: a reused asset is 100% on-model because nothing is
re-drawn. Only generate when a genuinely new pose/expression is needed.

---

## Controlled vocabulary

Tags MUST come from these fixed lists (also in `manifest.json` under `vocabulary`).
Free-typed tags break tag-matching. Add a new value to the list deliberately, not ad hoc.

- **type:** character · background · prop
- **character:** maria · pepe · luna
- **expression:** neutral · happy · excited · scared · sad · crying · surprised · smug · angry · sleepy
- **pose:** standing · walking · running · jumping · falling · sitting · pointing · eating · arms-up
- **facing:** front · back · left · right · 3q-left · 3q-right

`transparent: true` = cutout PNG with alpha, so it composites onto any background.
All **character** assets should be transparent. Backgrounds are full-frame.

---

## Naming convention

- **Asset id & filename:** `<character>_<expression>_<pose-or-facing>`
  e.g. `luna_scared_front`, `pepe_excited_eating`
- **Files live in:** `assets/<character>/`, `assets/bg/`, `assets/props/`
- id and filename (minus extension) must match.

---

## Folder structure

```
madrid-adventures/
├── index.html            # the comic page — canonical output
├── manifest.json         # the library index (source of truth for assets)
├── LIBRARY.md            # this file
├── refs/                 # LOCKED character references (the anchors)
│   ├── maria_ref.png
│   ├── pepe_ref.png
│   ├── luna_ref.png
│   └── prompts/          # the exact prompt used for each reference
└── assets/
    ├── maria/  pepe/  luna/   # transparent character cutouts
    ├── bg/                    # backgrounds
    └── props/                 # gelato, wallet, pigeon, etc.
```

---

## Base library to generate first (tier: "base")

Generate this small high-frequency grid before anything else — it covers most panels.
For **each** of the three characters, as transparent cutouts:

| Expression | Poses | Facings |
|---|---|---|
| neutral | standing | front, 3q-left |
| happy | standing, arms-up | front |
| excited | standing | front |
| scared | standing | front |

That's ~6 cutouts per kid (~18 total) + the 4 backgrounds + 3 props already listed
as `planned`. Everything beyond this is a **top-up**, generated only when a specific
story beat needs it (e.g. `pepe_scared_falling` for the Colosseum trip) and then added
back to the library on-model.

---

## How assets get "pulled"

Code matches a page's requirement to an asset by its vocabulary fields. Start
**deterministic** (a tag→file lookup): "panel needs luna + scared + front" → finds
`luna_scared_front`. Predictable and debuggable. An AI-driven selection step can come
later if useful, but deterministic-first is the saner start for a comic.

A composed panel = one background + one or more transparent character cutouts +
optional props, with caption boxes and speech bubbles layered on top (the same
HTML/SVG layout layer already in `index.html`).

---

## Before you upload kids' photos — read this

- Uploading minors' faces to a third-party generator means those photos pass through
  (and may be retained on) that company's servers. Check the tool's data-retention
  policy first. A quiet upside of stylizing: the published comic ends up as painted
  characters, not photo-identifiable likenesses.
- **Prompt for the *look*, not the studio.** Describe "soft hand-painted anime,
  watercolor light, gentle linework, warm tones" rather than naming a studio. Style
  descriptors are more robust and avoid copying named characters (the aesthetic is
  fair game; specific studio characters are not).
- Generator syntax for character-reference changes often — verify the current flag/
  workflow in the tool's own docs before a batch run.

---

## Workflow recap (which surface does what)

- **Chat** — writes character bibles + per-asset prompts; does vision QA on generated
  art to catch drift; advises which asset fits a panel.
- **Image generator** — makes the locked reference, then the on-model assets.
- **Code + Git** — stores the library, runs the pull/compose logic, hosts the result.
- **Manifest is canonical.** If a conversation and the committed manifest disagree,
  the manifest wins.
