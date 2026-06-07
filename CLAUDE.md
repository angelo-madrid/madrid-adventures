# CLAUDE.md — Master Index & Operating Manual

Master document for the project (The Madrid Adventures). Explains the system (bookshelf +
books), the doc model, the shared cast, and how to make a comic. For component detail it
**points** to that component's own docs. If a conversation and the committed docs disagree,
**the docs win**.

---

## Governance — Production Bible v1.0 (LOCKED)

The production canon is **locked at v1.0** (baseline after *Roman Holiday*). **LOCKED** (change
only by deliberate franchise-level decision): character canon, height canon, roles &
responsibilities, production workflow, QA ownership. **EVOLVING** (add freely): new techniques
and discoveries → `LESSONS_LEARNED.md`. The pasteable session version of the Bible for ChatGPT
is `docs/CHATGPT_CONTEXT.md`. This keeps the foundation stable while the craft keeps improving.

## Where it lives (LOCKED — coordinates rarely change)

- **Repo URL:** https://github.com/angelo-madrid/madrid-adventures
- **Live (GitHub Pages) URL:** https://angelo-madrid.github.io/madrid-adventures/
- **Pages source:** `main` branch, `/` (root)

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

## 1. Roles — the human leads; the models are specialized collaborators

**The HUMAN is the Creative Director, Art Director, Editor-in-Chief, QA Lead, and Publisher.**
The models execute within that direction. The objective is not to maximize any one model's
use — it's to maximize story quality, character consistency, reader enjoyment, educational
value, and publishing reliability, by assigning each task to the best-suited tool.

| Party | Role | Owns |
|---|---|---|
| **Human** | Creative Director / Editor-in-Chief / **Final QA Authority** | Story, character, visual, continuity & publishing **approval** — the final decision-maker |
| **Claude chat** | Story Architect | Story bible, character behavior, narrative canon; chapter/scene planning, dialogue, running jokes; **prompt drafting**; QA *assist* (checks panels against the bible) |
| **ChatGPT (GPT Image)** | Visual Production (Production Artist) | Image generation + editing, visual refinement, layout & bubble execution; *assists* drift detection. **Does NOT own final QA.** |
| **Claude Code** | Production Engineer | Repo structure, HTML/CSS/JS, asset management, build automation, Git ops, GitHub Pages deploy |
| **Claude Design** | Optional | Explore looks / repackage to PDF/PPTX (research-preview) |

**Decision authority:** Narrative direction → Human + Claude chat · Visual *direction* →
Human · Visual *execution* → ChatGPT · Engineering → Claude Code · **Final QA & publication
approval → Human.**

Important lesson from the Rome build: the **human caught essentially all the drift** (height,
outfit, face, prop, dialogue, bubble placement). A generator cannot reliably QA its own
output for the consistency problems it creates — so models *assist* QA, they do not *own* it.
Mechanical image ops (crop/resize/optimize/convert) → Claude code. Painting, retouching,
and lettering → ChatGPT only.

---

## 2. The character-consistency rule (the heart of it)

Each panel is generated independently, so consistency is engineered into every prompt:

- **HARD ANCHORS (never drift):** each sibling's FACE, and the HEIGHT order, given two ways
  (use BOTH — percentages are most reliable, landmarks are the visual check):
  **Maria = 100% (tallest yardstick); Pepe = 97% of Maria (head at her FOREHEAD); Luna = 93%
  of Maria (head at her EYEBROW).** All three heads sit in a narrow band near the top — three
  similarly-tall siblings, NOT one big + two small.
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

CHARACTER REFS (attach each child's standardized reference for face/hair CONSISTENCY —
goal is recognizable & consistent across panels, NOT photographic likeness; keep their
hair, age order, and outfits locked):

AGES & HEIGHTS (render-age trick — say 13/12/12 to stop Luna shrinking; canon age stays 9):
The three children are CLOSE in age, about 13, 12, and 12, and NEARLY THE SAME HEIGHT.
- MARIA — tallest; the 100% height reference.
- PEPE — 97% of Maria's height; his head reaches MARIA'S FOREHEAD.
- LUNA — 93% of Maria's height; her head reaches MARIA'S EYEBROW.
All three heads sit in a NARROW band near the top — three similarly-tall siblings, NOT one
big kid and two little ones. Never portray Pepe or Luna as significantly smaller children.

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

### Character canon (personalities — lock across ALL books)
Visual drift gets the attention, but **personality drift is the bigger long-term risk** as the
series grows. Keep these consistent everywhere:
- **Maria** — responsible, observant, often documenting the trip; pretends to be unimpressed but
  secretly loves the adventure.
- **Pepe** — curious, mischievous, accident-prone; creates many of the comic situations;
  enthusiastic explorer.
- **Luna** — openly excited and curious; asks lots of questions; the most expressive of the three.

**Recurring motifs (optional, gentle):** Maria a phone/camera, Pepe a guidebook, Luna a small
backpack. Let them appear often enough that readers associate them with each kid — but they are
NOT required in every panel; don't turn them into a consistency chore.

### Story philosophy
Madrid Adventures is **not a sequence of travel postcards.** Every scene should ideally carry at
least one of: **humor · discovery · character interaction · educational value · a running joke.**
The strongest scenes combine several. Use this as the editorial bar when planning panels.

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
