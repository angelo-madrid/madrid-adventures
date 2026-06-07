# Madrid Adventures — Handoff to New Chat

Paste this at the start of a new Claude chat to resume the project with full context.

---

## What this project is

**The Madrid Adventures** — a GitHub-hosted bookshelf of children's comic books starring three
siblings: **Maria (13), Pepe/Felipe (12), Luna (9)**. Built by Angelo Madrid (GitHub:
`angelo-madrid`). Each comic is a "book" on a dashboard "shelf." Live site:
`https://angelo-madrid.github.io/madrid-adventures/` (confirm exact URL via the repo's Pages
settings).

**Book 1 — *Roman Holiday* — is COMPLETE and PUBLISHED** (8 panels). Next up: **Book 2 —
Hogwarts** (*The Shadow Over Hogwarts*).

---

## The human is the director (this is locked, v1.0)

- **Human (Angelo)** = Creative Director, Art Director, Editor-in-Chief, **Final QA Authority**.
- **Claude chat (you)** = Story Architect: writes story/dialogue, drafts ChatGPT panel prompts,
  QA-assists by checking panels against the bible, does mechanical image ops (crop/resize/
  optimize to webp).
- **ChatGPT (GPT Image)** = Visual Production: generates the complete painted panels INCLUDING
  baked-in lettering (speech bubbles + narration). Does NOT own final QA.
- **Claude Code** = Production Engineer: repo, build, Git, GitHub Pages. (Claude chat CANNOT
  push — it prepares files + writes Claude Code prompts; Angelo runs them.)

Decision authority: narrative → human + Claude; visual direction → human; visual execution →
ChatGPT; engineering → Claude Code; final QA + publish → human.

---

## How a panel gets made (the proven loop)

1. **Claude** writes the panel prompt = reusable header (refs + heights) + scene + actions +
   exact bubble/narration text.
2. **ChatGPT** generates the complete lettered panel (the 3 face refs attached every time).
3. **Human** QAs: faces recognizable? height order right? outfits correct? text spelled right?
4. **Claude** optimizes the approved image to `<id>.webp` (WebP, ~1600px, ~300–520KB) and
   records it in the story's `pages.json`.
5. **Claude Code** commits + pushes.

Big relayouts (moving who's where) → regenerate from scratch; small tweaks → an edit pass is OK.

---

## The cast — consistency rules (hard-won, do not relearn)

**Faces:** Attach the 3 standardized **v3 reference faces** (`characters/refs/maria_ref.png`,
`pepe_ref.png`, `luna_ref.png`) to EVERY panel prompt, framed as character MODEL SHEETS. Goal =
**recognizable & consistent, NOT photographic likeness.** (We tried hard for photographic
likeness — it makes every panel a grind; abandoned. Recognizable-stylized is the target.)

**Heights** (the #1 failure mode — these constraints go in every prompt):
- Render-age trick: prompt the cast as **"about 13, 12, and 12"** (NOT 9) — the model draws "9"
  as tiny. Luna's real/canon age stays 9; the number is only a size dial.
- Percentages + landmarks: **Maria 100% (tallest); Pepe 97% (head at Maria's forehead); Luna 93%
  (head at Maria's eyebrow).** All three heads in a NARROW band — three similarly-tall siblings,
  NOT one big + two small. If unsure, taller not shorter.

**Outfits (default looks — state them explicitly every panel; never "may vary"):**
- Maria: teal t-shirt, dark-blue jeans, teal sneakers, long black hair with a fringe.
- Pepe: orange t-shirt with a cream/white horizontal stripe, green cargo shorts, blue sneakers,
  short tousled dark hair.
- Luna: yellow t-shirt, dark pants, brown shoes, dark hair in a ponytail.
(Outfits can change per scene — e.g. Hogwarts may want robes — but state the change explicitly.)

**Personalities (canon across all books):**
- Maria: responsible, observant, natural leader; curious, adventurous, and proactive — the first
  to lean in and push the group forward. Openly engaged and eager. (v1.1: phone/camera motif retired.)
- Pepe: curious, mischievous, accident-prone, the cause of most comic chaos, always hungry.
- Luna: openly excited, curious, the most expressive, drives discovery moments.

**Optional motifs (not required every panel):** Pepe a guidebook, Luna a small backpack. (Maria's
motif TBD — phone/camera retired in v1.1.)

---

## Lettering & story bar

- Bubbles + narration are **painted into the panel by ChatGPT** (no separate overlay). Keep text
  SHORT and correctly spelled. Distinct speaker (e.g. a talking object) gets a distinct bubble.
- Narration through-line in Rome numbered the stops ("First stop… 2nd Stop…"). Use a similar
  device per book if it fits.
- Story bar: every scene should carry at least one of humor / discovery / character / education /
  a running joke. Not a sequence of postcards.

---

## Repo structure (managed by Claude Code)

```
madrid-adventures/
├── CLAUDE.md            # master operating manual (Production Bible v1.0 LOCKED governance)
├── FILES.md             # storage/endpoint map
├── BACKLOG.md           # infra backlog (open: move bulk images to Supabase; generate covers)
├── LESSONS_LEARNED.md   # evolving craft log (height playbook, identity decision, etc.)
├── stories.json         # registry the shelf reads
├── index.html           # the bookshelf
├── characters/
│   ├── manifest.json     # cast bibles (refs v3, ages, heights, outfits, personalities)
│   ├── refs/             # maria_ref / pepe_ref / luna_ref (v3 standardized faces)
│   └── sources/          # the real source photos (record)
├── dashboard/{SPEC,FILES,BACKLOG}.md + assets/ (header avatars)
├── docs/CHATGPT_CONTEXT.md  # the pasteable ChatGPT session brief
└── stories/
    ├── rome/   index.html (reads panels+stanzas from pages.json; has poem toggle)
    │            + pages.json (panels + per-panel "stanza" = full poem) + poem.md (ref copy)
    │            + panels/p1..p8 (COMPLETE)
    ├── hogwarts/  (NEXT — coming-soon stub exists)
    └── mars/      (coming-soon stub)
```

Storage: images live in-repo for now (small); Supabase migration for bulk art is a backlog item.

---

## Where we are right now

- **Rome: done & published**, including a **"Show the poem" toggle** (storybook mode) on the
  reader page. Faces in the Rome panels use the OLDER refs — **left as-is on purpose** (a
  face-swap was attempted and abandoned; the seam is an acceptable "charming first book").
- **v3 standardized face refs are FINAL & locked**: `characters/refs/{maria,pepe,luna}_ref.png`
  — same blue shirt, white background, straight front pose. Target = recognizable & consistent,
  NOT photographic. (We tried photographic likeness hard, including a P1 face-swap; it makes
  every panel a grind, so we deliberately stopped. Use the refs as MODEL SHEETS.)
- The Rome **poem is now single-source in `stories/rome/pages.json`**: each panel has a
  `stanza` field holding that section's full verse, and `index.html` reads stanzas FROM
  pages.json (no hardcoded copy). To revise the poem, edit the `stanza` fields in pages.json —
  one place, page updates automatically. (`stories/rome/poem.md` is a reference copy of the
  full poem.)
- **Poem-toggle pattern is REUSABLE** — the Hogwarts reader page should inherit the same
  "Show the poem" button + stanza-per-panel structure (stanzas in its own pages.json).
- Note: poem stanzas and panels' baked-in speech bubbles don't word-for-word match (narrator
  verse vs. live dialogue) — intentional, picture-book style.
- **Hogwarts — in progress:** storyboard v2 (reconciled to the poem) + Panel 1 prompt written,
  generated & QA'd; **Maria recast (v1.1)**. Poem: `Three_Siblings_and_the_Shadow_Over_Hogwarts.md`.

## Hogwarts — known constraints

- **IP / copyright:** the poem references Harry, Ron, Hermione by name. In the *art*, do NOT
  depict the trademarked characters — use **generic original young wizards** instead. Avoid
  baking trademarked names/likenesses into images. Keep the kids' adventure original.
- Story beats from the poem: attic portal → land in cursed Hogwarts → meet 3 young wizards →
  Devil's Snare vine → troll fight → Dementors (Luna conjures a fox of pure joy) → giant spider
  (Maria fights with fire) → final serpent guarding the curse → curse broken → feast → home,
  Luna keeps a tiny spark. ~8 panels, same pipeline as Rome.

## First Hogwarts steps

1. Draft the Hogwarts **storyboard** (8-panel blueprint) from the poem.
2. Per panel: Claude writes prompt (model-sheet refs + height block + scene + baked lettering) →
   ChatGPT generates → QA → optimize → Claude Code commits.
3. Generate a Rome cover + Hogwarts cover at some point (backlog).
