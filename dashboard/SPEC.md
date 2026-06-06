# dashboard/SPEC.md — The Bookshelf

What the dashboard is and how it should look and behave. This is the **website shell**,
not any single comic. Storage/hosting rules live in master `FILES.md`; the shell's own
structure lives in `dashboard/FILES.md`; this file is the *what and the feel*.

## Purpose
One landing page — a "bookshelf" — where family and friends pick a story and read it.
The shelf is the product; the comics are the contents.

## Audience & devices
Kids and family, technically non-savvy. Must work on **desktop, mobile, and print to PDF**.
Tap-friendly targets; large covers; minimal text.

## Core experience
- A grid/shelf of **book covers**, one per story, built automatically from `stories.json`.
- Tapping a cover opens that story's comic (`/stories/<id>/`).
- Each cover shows: cover art, title, and a **status badge** (published / in-progress /
  coming-soon). Coming-soon covers are visible but not yet openable.
- A short site title + tagline header (from `stories.json` → `site`).

## Look & feel
- Warm, playful, picture-book — consistent with the kids' comic world (soft hand-painted
  feel, warm palette). The shelf should feel like a children's bookshelf, not a SaaS dashboard.
- Distinctive, not generic-AI: characterful type, gentle texture, cover-forward layout.
- The three siblings can appear as a small mascot/illustration in the header.

## Behavior decisions (flag, then lock)
- **Navigation vs gating:** default is plain navigation — tap opens the story; `status`
  only changes the badge/availability. If real "unlock" mechanics are ever wanted (a kid
  earns a story), that is app logic and gets specified HERE before building.
- **Sort:** by `order` then `added`.

## Out of scope
- No accounts, comments, or backend logic. No SPA framework unless transitions are later wanted.
- The shell never contains comic content — only links to stories.

## Inputs / contracts
- Reads `stories.json` (the registry). Adding a book = a registry entry, never a shell edit.
- Defers to master `FILES.md` for hosting/storage; to `dashboard/FILES.md` for shell structure.
