# BACKLOG.md — Project / Infrastructure

Cross-cutting tasks that span the whole project. Component-specific tasks live in each
component's own BACKLOG (`dashboard/`, `stories/<id>/`). Master/infra items live here.

---

## 🔼 Storage — move ALL images to Supabase

The repo holds code + text + pointers only; every image binary lives in Supabase Storage
(see `FILES.md`). Establish this before generating covers/panels so nothing ever lands in Git.

- [ ] Create/reuse a Supabase project; add a **public-read** bucket `comic-assets`.
- [ ] Put real keys in local `.env` (`SUPABASE_URL`, `SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_KEY`)
      — never commit; published site uses the **anon** key only.
- [ ] Set `characters/manifest.json` → `storage.baseUrl` to the bucket URL.
- [ ] **Scope note:** the 3 character references + the 3 header avatars are the small,
      foundational **in-repo exception** (used by the shell, rarely change). Supabase is
      for the *bulk/growing* art — story covers and panels. Keep refs/avatars in the repo;
      `characters[].referenceImage` may stay repo-relative or move to Supabase later — not urgent.
- [ ] As covers + panels are generated: upload to Supabase, then set `stories.json` `cover`
      URLs and each story's panel image URLs.
- [ ] Confirm the repo contains no **bulk** image binaries (covers/panels); the small
      refs/avatars exception is allowed.
- [ ] (Optional) `scripts/upload.js` so Claude Code can push a local `/staging` folder →
      Supabase and print the public URLs.

---

## Captured from the dashboard build review

- [ ] **Document the path handling** in `dashboard/FILES.md`: the registry stores absolute
      paths (`/stories/rome/`) while the live shelf fetches `stories.json` *relatively* and
      strips the leading slash, so it works under the `/madrid-adventures/` Pages sub-path
      (and still works at a custom-domain root). Sound + robust — just record it so it isn't
      later mistaken for a bug.
- [ ] **Your own visual QA pass** on the live shelf — desktop, mobile, and print-to-PDF.
      (Claude Code can't truly judge the look; that pass is yours.)
- [ ] **Character refs**: currently not committed anywhere (only `refs/.gitkeep`). Resolved
      by the Supabase task above — they go to the bucket, manifest points to URLs.

---

## Methodology reframing — DONE (folded into CLAUDE.md + manifest)

Decided: ChatGPT generates the complete panel including lettering; Claude writes the
prompts; Claude Code uploads. The separate text-overlay layer is retired for panels.
Consistency anchor = **faces + height/age order** (outfits, hair, styling may vary per panel).

- [x] **CLAUDE.md:** flip the core principle — "ChatGPT generates the full panel (art +
      bubbles + narration); Claude writes prompts." Remove the overlay/text-layer machinery.
- [x] **Consistency rule:** rewrite to "faces + height/age order are the anchor; wardrobe/
      hair/styling are free to vary per panel." Refs are attached for FACE + proportions.
- [x] **manifest bibles:** demote fixed outfits from "canon" to "default/origin look";
      note wardrobe is per-scene.
- [ ] **Update Luna's character entry** to match her ChatGPT arrival look (orange tee,
      ponytail, dark pants) as her default look — faces + height stay the locked part.
- [x] **pages.json:** `bubbles`/`sfx` fields become *prompt content* (what ChatGPT paints),
      not an overlay spec. Drop the empty-bubble approach.

## Notes
- Repo is **live** (`angelo-madrid/madrid-adventures`); the committed repo is now canonical.
  Future doc/code edits flow through Claude Code → Git, not the local sandbox.
- Covers are intentionally placeholder tiles until each story's cover image is generated.
