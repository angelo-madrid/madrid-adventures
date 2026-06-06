# CLAUDE.md — Master Index & Operating Manual

This is the **master document** for the project. It explains the whole system (the
bookshelf and the books), the doc model, the shared cast, and how to make a comic.
For component detail it **points** to that component's own docs — it does not duplicate
them. If a conversation and the committed docs disagree, **the docs win**.

---

## What this project is

**The Madrid Adventures** is a **bookshelf of comic adventures** starring the same three siblings — Maria (13),
Pepe (12), Luna (9). A master **dashboard** (the shelf) lets readers pick a **story**
(a book); each story is its own self-contained comic. Think of browsing different
Asterix or Tintin albums from one shelf. Published to GitHub Pages as one public link
for family and friends, on desktop, mobile, or printable PDF.

Two kinds of thing, kept separate on purpose:
- **The bookshelf (dashboard)** — a website product with its own design, navigation, and feel.
- **The books (stories)** — individual comics. Self-contained.
- **The cast** — shared canon running through every book.

---

## Architecture at a glance

```
madrid-adventures/
├── CLAUDE.md            # THIS FILE — master index + how-to-work (governs everything)
├── FILES.md             # MASTER storage/endpoint map (Git / Supabase / Drive / Pages)
├── stories.json         # REGISTRY — the list of books the shelf displays
├── characters/          # SHARED CAST — the through-line across all stories
│   ├── manifest.json    #   locked references + bibles (Maria / Pepe / Luna)
│   └── refs/            #   (or Supabase URLs)
├── dashboard/           # THE BOOKSHELF (the website shell)
│   ├── SPEC.md  FILES.md  BACKLOG.md
│   └── index.html       #   the master dashboard panel
└── stories/             # THE BOOKS
    ├── rome/
    │   ├── SPEC.md  FILES.md  BACKLOG.md
    │   ├── pages.json    #   Rome's panel data (captions, casting, bubbles)
    │   └── index.html    #   Rome's comic page
    └── hogwarts/ …
```

---

## The doc model — two levels

**Master level (root):** governs the whole project.
- `CLAUDE.md` — index + how-to-work + architecture (this file).
- `FILES.md` — project-wide storage & endpoints: what lives in Git vs Supabase vs Drive,
  the immutability rule, secrets handling. Cross-cutting; every component defers to it.
- `characters/` — the shared cast (locked refs + bibles). Shared by all stories.
- `stories.json` — the registry the dashboard reads to build its tabs.

**Component level:** each of `dashboard/` and `stories/<name>/` owns its own trio:
- `SPEC.md` — what this component is and how it should look/behave.
- `FILES.md` — this component's *local* structure and inputs (defers to master FILES.md
  for storage/hosting rules — does not restate them).
- `BACKLOG.md` — this component's running edit queue.

**Defer, don't duplicate.** A shared fact (e.g., "images live in Supabase") is explained
once in master `FILES.md`; everything else points to it in one line. The dashboard's
concerns (nav, feel) never mix with a story's concerns (plot, panels), and neither mixes
with the cast.

---

## 0. Non-negotiable facts (read first)

1. **No Claude surface generates painted/raster art.** Not chat, not Claude Design.
   All illustration happens in an external generator (ChatGPT / GPT Image chosen).
2. **Painting happens only in the generator. Text is a separate layer Claude builds.**
   Panels are generated as **art only**; bubbles/captions/SFX are an editable HTML/SVG
   overlay on top.
3. **Character consistency** is managed by locking one reference per character and
   attaching it to every generation.
4. **The committed repo is the source of truth.**

---

## 1. Tool roles

| Surface | Role | Does NOT do |
|---|---|---|
| **Claude chat** | Story + storyline; storyboard mock; generator prompts; consistency QA; text/bubble layer; mechanical image ops (crop/resize/composite) | Cannot paint or retouch raster art |
| **ChatGPT (GPT Image)** | All painted art: references, panels, backgrounds; conversational edits to fix art | — |
| **Claude Design** | Optional: compose finished panels into a page; explore looks; export PDF/PPTX/Canva | Cannot generate/retouch art; research-preview |
| **Claude Code** | Owns Git: commit, push, GitHub Pages; writes/runs automation scripts | — |

**Edit line:** artistic edits → ChatGPT. Mechanical edits (crop/resize/composite/convert)
→ Claude code. Text & layout (bubbles/captions/SFX/borders/page) → Claude code.

---

## 2. Two approaches — pick one per story

- **Panel-per-image (CHOSEN):** each panel is a full painted scene. Richer; no library needed.
- **Asset library (reuse across stories):** generate tagged cutouts/backgrounds once, compose
  from parts. More setup; only worth it across many stories. (See `LIBRARY.md`/`manifest.json`.)

---

## 3. The pipeline (applies to each book)

```
1. Chat        → story + storyline
2. Chat (SVG)  → flat storyboard mock = blueprint. LOCK panels/composition/casting/bubbles here.
3. Chat        → one ChatGPT prompt per panel from the storyboard, ART ONLY
                 ("no text, no speech bubbles, full-bleed scene").
4. ChatGPT     → generate each panel; attach locked refs; edit to fix art.
5. Chat        → consistency QA across ALL panels before assembly.
6. Compose     → Claude builds text/bubble layer + page (HTML/SVG) [or Claude Design].
7. Claude Code → commit + publish; add the story to stories.json so the shelf shows it.
```

Storyboard (2) and QA (5) are what stop late-stage consistency surprises.

---

## 4. The shared cast (characters/)

- One **locked reference** per character (neutral, plain background). The consistency anchor.
- **Identical style sentence** across characters so they read as one cast.
- Attach the locked ref to every panel prompt — the biggest lever on retry rate.
- The cast is shared canon: defined once in `characters/`, referenced by every story.
- A **recurring** new character graduates into `characters/`; a true one-off villain can
  live in that story's own folder.

---

## 5. Generating panels

- Art only, no text. Attach references. Budget ~4–5 generations per finished panel.
- Rome volume: 8 panels + remaining refs ≈ **45–50 generations** (~one session).

---

## 6. Text / bubble layer (Claude)

- Bubbles, captions, SFX, borders = HTML/SVG/CSS layer on top of the art; fully editable.
- Content comes from the story's `pages.json`. Art and words stay independent.

---

## 7. Compose, publish & the shelf

- Build each comic as a responsive HTML page with **print CSS** (desktop + mobile + PDF).
- **Hosting:** static linked pages on GitHub Pages — dashboard at `/`, stories at
  `/rome/`, `/hogwarts/`. Avoid a SPA framework unless you deliberately want transitions.
- **The shelf auto-discovers books via `stories.json`.** Publishing a new comic = adding
  one registry entry, NOT editing the dashboard shell. (Registry owned by `dashboard/FILES.md`.)
- Images live in Supabase (see master `FILES.md`); raw originals stay in the cold archive.

---

## 8. Adding a new story (repeatable procedure)

1. `stories/<id>/` → create `SPEC.md`, `FILES.md`, `BACKLOG.md`, `pages.json`.
2. Run the §3 pipeline for that story (reuse the locked cast in `characters/`).
3. Generate one **cover image** for the story tab (belongs in that story's SPEC).
4. Add one entry to `stories.json` (title, cover URL, path, status).
5. Claude Code commits + deploys. The shelf shows the new book automatically.

---

## 9. ChatGPT plan / cost

- **Hands-on:** ChatGPT **Plus ($20/mo ≈ ₱1,230)**; subscribe build-months, drop to Free between.
- **Automated:** OpenAI image **API**, ~$0.13 (≈₱8)/image; a Rome comic ≈ ₱350–400.
- Stay on the **app** during exploration (you need to see/pick). Move to **API** only after
  refs are locked and prompts proven. Verify current OpenAI pricing before paying.

---

## 10. Privacy & safety

- Uploading minors' photos to a third party may retain them on their servers — check
  data/retention settings first. Upside: published art is painted, not photo-identifiable.
- Prompt for the *look*, not a studio by name; original characters only.

---

## Companion docs map

| Doc | Scope | Owns |
|---|---|---|
| `CLAUDE.md` (this) | Master | Architecture, workflow, the index |
| `FILES.md` | Master | Storage/endpoints (Git/Supabase/Drive/Pages), secrets, immutability |
| `characters/manifest.json` | Master | Locked refs + bibles |
| `stories.json` | Master | The registry of books |
| `dashboard/{SPEC,FILES,BACKLOG}.md` | Component | The bookshelf shell |
| `stories/<id>/{SPEC,FILES,BACKLOG}.md` + `pages.json` | Component | One book |

---

## Quick reference — can Claude…?

| Ask | Answer |
|---|---|
| Generate / retouch the painted art? | No → ChatGPT |
| Crop / resize / composite / convert an image? | **Yes** (code) |
| Create/edit speech bubbles, captions, SFX? | **Yes** (overlay) |
| Lay out panels into a page / build the dashboard? | **Yes** (HTML/SVG) |
| QA consistency across panels? | **Yes** (vision) |
| Publish to GitHub with a public link? | **Yes** (Claude Code) |
| Drive ChatGPT-the-app? | No — only call the OpenAI **API** via script |
