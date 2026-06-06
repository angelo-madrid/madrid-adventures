# stories/rome/FILES.md — Local Structure

Defers to master `FILES.md` for all storage/hosting/secrets rules.

## Files this story owns
- `pages.json` — panel data (caption, casting by character id, bubbles, SFX per panel).
- `index.html` — the assembled comic page (panels + text overlay layer). Built later.
- Cover image — generated; referenced from `stories.json` (`cover`). Lives in Supabase.

## Art
- Panel art is generated in ChatGPT (art only, no text), stored in Supabase, referenced
  by URL. Raw originals + rejected retries stay in the cold archive (Drive/disk).

## Boundaries
- Uses the shared cast in `/characters` (does not redefine Maria/Pepe/Luna).
- The shell links here via the registry `path` (`/stories/rome/`); it never reaches inside.
