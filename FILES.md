# FILES.md — Where Everything Lives (Data, Storage & Endpoint Map)

The sustainable principle, one line:
**Text and structure live in Git. Image binaries live in object storage. Raw originals
live off the pipeline entirely.** The manifest is the contract that joins them.

---

## The layers

| Layer | Holds | Tool | Versioned? |
|---|---|---|---|
| **Source of truth (text)** | code/renderer, `manifest.json`, `pages/*.json`, docs, bibles, prompts | **Git / GitHub** | Yes (Git history) |
| **Image binaries** | refs, character cutouts, backgrounds, props | **Supabase Storage** | Via immutable filenames + manifest pointer |
| **Cold archive** | raw generator originals, source photos | **Google Drive / disk** | Manual |
| **Hosting** | the published comic page | **GitHub Pages** | — |

The live site loads images from Supabase public URLs at run time. Build-time
operations (upload, commit, deploy) run on your machine via Claude Code.

---

## Who owns which endpoint (build time)

- **GitHub** — Claude Code, natively (`git`, `gh`). Repo, commits, pushes, Pages. Easy.
- **Supabase** — Claude Code, via Supabase CLI + scripts using YOUR keys from a local
  `.env`. Creates the bucket, sets public-read policy, uploads assets, defines schema/RPCs.
- **Google Drive** — **manual by default.** Automating it needs Google API + OAuth and is
  overkill for a cold archive. Drag raw originals in yourself. Only automate later if you
  truly want hands-off archival (one-time OAuth setup Claude Code can scaffold, you authorize).

---

## Secrets — non-negotiable rules

- All keys (Supabase service key, etc.) live in a local **`.env`** that **you** create.
- `.env` is in `.gitignore` and is **NEVER** committed.
- The published site uses only the Supabase **anon/public** key for read access — never
  the service key.
- Claude Code reads `.env` locally to run build scripts. Credentials are never pasted into
  chat and never enter the repo.

---

## Image versioning without Git bloat

Never overwrite an image. A new version is a **new file**:
`luna_ref.png` → `luna_ref_v2.png`. Update the pointer in `manifest.json` and commit.

Because the manifest is text in Git, **its commit history becomes the art version
history** — every commit records which asset was current, and you can roll a pointer back
anytime. Binaries stay flat in storage; versioning lives in the cheap text layer.

---

## Story as data

`pages/*.json` holds each comic's content (captions, casting, bubbles) referencing
manifest asset ids by **slot** (left/center/right…), not pixel coordinates. `index.html`
is a **renderer**: it reads a page file + the manifest and composes the panel. Result:
new pages and bulk text edits are data edits, never code surgery. New stories = new files
in `pages/`.

---

## Repo shape

```
madrid-adventures/
├── index.html            # renderer (reads manifest + pages, composes panels)
├── manifest.json         # asset index  (source of truth for art)
├── pages/
│   └── rome-holiday.json # story content as data
├── refs/prompts/         # the locked-reference prompts (text)
├── scripts/              # Claude Code build/upload scripts (Supabase, deploy)
├── .env.example          # documents required keys (real .env is git-ignored)
├── .gitignore            # ignores .env, node_modules, raw originals
├── FILES.md  LIBRARY.md  SPEC.md  BACKLOG.md
└── (images NOT here — they live in Supabase Storage)
```

---

## Don't over-build

This is the ceiling of complexity a comic needs. Resist a Postgres asset DB or a CMS until
you go multi-project and want assets queryable across many stories. JSON-manifest-in-Git +
Supabase Storage is correct until then.

## Limits worth remembering

- GitHub: 100 MB hard per-file cap, 50 MiB warning. Pages site ≤ 1 GB, ~100 GB/mo bandwidth.
- Keep raw originals OUT of the repo and OUT of the web bucket. Commit web-optimized images only.
- Supabase Storage carries the binaries, so the repo stays small indefinitely.
