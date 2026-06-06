# dashboard/FILES.md — Shell Structure & Inputs

How the bookshelf is built and what it depends on. Defers to master `FILES.md` for all
storage/hosting/secrets rules — does not restate them.

## Files this component owns
- `index.html` at the **repo root** — the shelf page Pages serves at `/`. (The `dashboard/` folder holds the shell DOCS and any shell-only assets, not the page itself.)
- `stories.json` (at repo root) — the **registry**; this is the shell's primary input/contract.
  The dashboard owns its schema and reads it at load to render covers/tabs.
- Optional `dashboard/assets/` — shell-only art (logo/mascot, fonts/icons). Story art does
  NOT live here.

## How it works
1. `index.html` loads `stories.json`.
2. For each entry it renders a cover card: `cover` image, `title`, `status` badge, linking to `path`.
3. Sorted by `order`. `coming-soon` cards render disabled.

## Hosting model
- **Static linked pages** on GitHub Pages. Dashboard at `/`; each story is its own page at
  its `path` (`/stories/rome/`, `/stories/hogwarts/`). No client-side routing required.
- Responsive layout + print CSS so the shelf works on desktop, mobile, and PDF.

## Boundaries
- Storage of images (Supabase), the cold archive (Drive), secrets, immutability → master `FILES.md`.
- A story's internal structure (its `pages.json`, panels) → that story's `FILES.md`.
- The shell never reaches into a story's internals; it only follows the registry's `path`.
