# CLAUDE.md — Master Index & Operating Manual

Master document for the project (The Madrid Adventures). Explains the system (bookshelf +
books), the doc model, the shared cast, and how to make a comic. For component detail it
**points** to that component's own docs. If a conversation and the committed docs disagree,
**the docs win**.

---

## What this project is

**The Madrid Adventures** is a **bookshelf of comic adventures** starring the same three
siblings — Maria (13), Pepe (12), Luna (9). A master **dashboard** (the shelf) lets readers
pick a **story** (a book); each story is its own self-contained comic. Published to GitHub
Pages as one public link, viewable on desktop/mobile or printable as PDF.

- **The bookshelf (dashboard)** — a website product with its own design and feel.
- **The books (stories)** — individual comics, self-contained.
- **The cast** — shared canon running through every book.

---

## Architecture at a glance

```
madrid-adventures/
├── CLAUDE.md            # THIS FILE — master index + how-to-work
├── FILES.md             # MASTER storage/endpoint map (Git / Supabase / Drive / Pages)
├── BACKLOG.md           # project/infra backlog (cross-cutting)
├── stories.json         # REGISTRY — the books the shelf displays
├── index.html           # the bookshelf (reads stories.json)
├── characters/          # SHARED CAST — refs + bibles (the consistency anchor)
├── dashboard/           # SPEC / FILES / BACKLOG for the shell
└── stories/<id>/        # each book: SPEC / FILES / BACKLOG + pages.json + panels/ + index.html
```

---

## 0. Non-negotiable facts (read first)

1. **ChatGPT generates the COMPLETE panel — art AND lettering** (speech bubbles + narration
   baked in), in one image. Claude does NOT paint, retouch, or overlay text onto panels.
2. **Claude (chat) writes the panel prompts** and does consistency QA; **Claude Code**
   uploads/commits. (The old "art-only + Claude text overlay" method is retired.)
3. **Consistency anchor = FACES + HEIGHT/AGE order.** These are the hard locks. (See §4.)
4. **The committed repo is the source of truth.**

---

## 1. Tool roles

| Surface | Role |
|---|---|
| **Claude chat** | Story + storyline; storyboard/blueprint; writes ChatGPT panel prompts; consistency QA on generated panels; mechanical image ops (crop/resize/optimize) |
| **ChatGPT (GPT Image)** | Generates the complete painted panel including all lettering; conversational edits |
| **Claude Code** | Owns Git: name/store panels, commit, push, GitHub Pages |
| **Claude Design** | Optional: explore looks / repackage to PDF/PPTX (research-preview) |

Mechanical image ops (crop/resize/optimize/convert) → Claude code. Painting, retouching,
and lettering → ChatGPT only.

---

## 2. The character-consistency rule (the heart of it)

Each panel is generated independently, so consistency is engineered into every prompt:

- **HARD ANCHORS (never drift):** each sibling's FACE, and the HEIGHT order, calibrated to
  these **proven landmarks against Maria** (she is the tallest yardstick):
  **Pepe's head = Maria's FOREHEAD; Luna's head = Maria's EYEBROW.** All three heads sit in
  a narrow band near the top — three similarly-tall siblings, NOT one big + two small.
- **HEIGHT RENDERING TRICK:** prompt the cast as render-ages **"about 13, 12, and 12"** (not
  9) — the model treats "9" as "much smaller" and shrinks Luna, so we lie about her rendering
  age to hold her height. This is a pixels-only dial; **canonical age stays: Maria 13, Pepe
  12, Luna 9** (story text + pages.json keep the real ages). Use magnitude words ("much
  taller, not slightly") since the model under-applies height edits.
- **OUTFITS are always stated explicitly in the prompt — never left to the model.** The
  default look for each character is in `characters/manifest.json`. To keep them consistent,
  restate those looks every panel. To deliberately change an outfit for a scene, state the
  NEW outfit explicitly. The model must never improvise wardrobe (that caused color drift).
- **References** (`characters/refs/*`) are attached to every panel prompt — primarily for
  FACES and proportions.

### Reusable prompt header (paste at the top of EVERY panel prompt)

```
Use the three attached character references as the on-model designs for the SAME three
siblings — match their FACES and their outfits exactly as described.

AGES & HEIGHTS (render-age trick — say 13/12/12 to stop Luna shrinking; canon age stays 9):
The three children are CLOSE in age, about 13, 12, and 12, and NEARLY THE SAME HEIGHT.
- MARIA — tallest (the height yardstick).
- PEPE — his head reaches MARIA'S FOREHEAD (only a little shorter than Maria).
- LUNA — her head reaches MARIA'S EYEBROW (only a little shorter than Pepe).
All three heads sit in a NARROW band near the top — three similarly-tall siblings, NOT one
big kid and two little ones. If unsure, make them TALLER, not shorter.

LOCKED CHARACTER LOOKS:
- MARIA: teal t-shirt, dark-blue jeans, teal sneakers, long black hair with a fringe.
- PEPE: orange t-shirt with a cream/white horizontal stripe, green cargo shorts, blue
  sneakers, short tousled dark hair.
- LUNA: yellow t-shirt, dark pants, brown shoes, dark hair in a ponytail.
```

If heights still drift, the fallback is mechanical: rescale a character in the final image
via code so proportions are exact.

---

## 3. The per-panel pipeline

```
1. Claude writes the panel prompt = reusable header (§2) + scene + action + lettering.
2. ChatGPT generates the complete lettered panel (refs attached).
3. QA: faces match? height stair-step right (Luna not tiny)? outfits correct? text spelled right?
4. Settle → optimize to <id>.webp → Claude Code names + commits to the repo.
```

Big relayouts (moving who-does-what) → **regenerate from scratch**, don't stack edits;
small additions → an edit pass is fine.

---

## 4. Panel files & storage

- One image per panel, named by panel id: `p1-arrival.webp`, web-optimized (WebP, ~cap 1600px).
- Interim path (Supabase pending): `stories/<id>/panels/<id>.webp`.
- `pages.json` records each panel: `art` path, `caption`, `bubbles`, `sfx`, `status:"final"`,
  `lettering:"baked-in"`.
- Narration through-line: number the stops ("First stop…", "2nd Stop…", "3rd Stop…").
- Raw full-res ChatGPT exports → cold archive, never committed.
- Panels are an in-repo interim store; bulk art migrates to Supabase later (see FILES.md /
  BACKLOG.md). Small refs + header avatars stay in-repo.

---

## 5. The cast (characters/) & adding a story

- Cast is shared canon (refs + bibles in `characters/manifest.json`), referenced by every story.
- Add a story: create `stories/<id>/` (SPEC/FILES/BACKLOG + pages.json), run the §3 pipeline
  per panel, generate a cover, add one entry to `stories.json`. The shelf auto-shows it.

---

## 6. ChatGPT plan / cost

- Hands-on: ChatGPT **Plus ($20/mo ≈ ₱1,230)** — subscribe build-months, drop to Free between.
- Automated: OpenAI image **API**, ~$0.13 (≈₱8)/image. Move to API only once prompts are
  proven and you want volume. Verify current OpenAI pricing before paying.

---

## 7. Privacy & safety

- Minors' photos uploaded to a third party may be retained — check data settings. Published
  art is painted, not photo-identifiable.
- Prompt for the *look*, not a studio by name; original characters only.

---

## Companion docs map

| Doc | Scope | Owns |
|---|---|---|
| `CLAUDE.md` (this) | Master | Architecture, workflow, consistency rule, index |
| `FILES.md` | Master | Storage/endpoints, secrets, immutability |
| `BACKLOG.md` | Master | Cross-cutting/infra tasks |
| `characters/manifest.json` | Master | Cast refs + bibles (default looks) |
| `stories.json` | Master | Registry of books |
| `dashboard/{SPEC,FILES,BACKLOG}.md` | Component | The bookshelf shell |
| `stories/<id>/{SPEC,FILES,BACKLOG}.md` + `pages.json` | Component | One book |
