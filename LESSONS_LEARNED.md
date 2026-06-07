# Madrid Adventures — Lessons Learned

The **evolving** companion to the Production Bible (v1.0, LOCKED). New techniques and
discoveries go here, NOT in the Bible — so the canon stays stable while the craft improves.
Add a dated entry whenever something works (or fails) worth remembering.

---

## Height control (the #1 failure mode)

- **Percentages + landmarks together** beat either alone: Maria 100% / Pepe 97% / Luna 93%,
  AND Pepe's head at Maria's forehead, Luna's at her eyebrow. *(Rome)*
- **Render-age trick:** prompt the cast as "about 13, 12, and 12" — the model reads "9" as
  "much smaller" and shrinks the youngest. The number is a size dial only; Luna's canon age
  stays 9. *(Rome — solved repeated Luna-shrinking)*
- **Magnitude language:** the model under-applies height edits. Say "MUCH taller, not
  slightly" / "significantly." Polite "taller" produces marginal nudges. *(Rome, Panel 4)*
- **"Narrow band of heads, NOT one big + two small"** gives a holistic target the model can
  self-check against. *(Rome)*
- **Photo references fight height:** when a real photo is attached for posing, it pulls the
  youngest small (real kids' real ages). The render-age block must override it. *(Rome, Panel 6)*
- **Mechanical rescale is NOT reliable in this setup:** background-removal (rembg) is network-
  blocked and grabCut can't cleanly cut a figure off a busy/same-toned background. Fix height
  in-generation or via a ChatGPT edit, not by code compositing. *(Rome, Panel 4)*

## Editing vs regenerating

- **Small additions / single tweaks** → an edit pass is fine.
- **Big relayouts** (moving who's where, swapping actions, repositioning a character) →
  regenerate from scratch; stacked edits get muddy. *(Rome, Panels 3 & 4)*
- **Edit ripple:** edits sometimes redraw faces or move unrelated elements. Always QA that
  only the requested thing changed. *(Rome)*
- **Two opposite changes in one pass** (one figure up, one down) is doable but riskier — name
  each one's current error explicitly ("too tall" / "too short"). *(Rome, Panel 4)*

## Lettering

- Baked-in (ChatGPT-painted) bubbles look far more cohesive than overlaid vector bubbles —
  worth the tradeoff that text is fixed in pixels. *(Rome — methodology pivot)*
- Keep each line **short**; long lines garble more. Verify every word on return.
- A distinct speaker (e.g. a talking statue) needs a visually distinct bubble (grey, jagged
  "stone echo") — must be stated explicitly or the model defaults to a normal white bubble.
  *(Rome, Panel 6)*

## Outfits / wardrobe

- Never say "clothes may vary" — that's a license the model uses, and it caused Maria's shirt
  to drift color. **State each outfit explicitly every panel.** *(Rome, Panel 4)*

## Workflow / file ops

- Per-panel: prompt → generate → human QA → settle → optimize to `<id>.webp` → Claude Code
  commits. WebP at ~1600px keeps panels ~300–520 KB.
- Same-filename replacements (revising a published panel) need a hard refresh / cache-bust to
  show — the CDN and browser cache the old image. *(Rome, Panel 6 revision)*

## IP

- Don't bake trademarked names/characters into art by default; prefer generic/original. Frame
  trivia *about* a franchise (e.g. "how many types are there") rather than naming characters.
  *(Rome, Panel 6 oracle gag)*

---

*Add new entries below as the system evolves (new prompt techniques, bubble placement,
publishing improvements, cross-book continuity discoveries).*

## Face likeness — the Identity Lock protocol (added after the v1 face refresh)

The original refs went through a "ghiblify" pass that prioritized style over resemblance, so
faces drifted toward a generic cute-anime average (Maria too young, Luna's round face slimmed).
Fix discovered:

- **Resemblance must explicitly outrank style.** The single highest-value instruction is:
  *"This is a portrait of a REAL specific child — treat the photo as a likeness target, not
  inspiration. If you find yourself making the face rounder/softer/cuter than the photo, STOP
  and match the photo."*
- **Generate faces SOLO, not as a combined sheet.** A 3-up sheet splits resolution and lets
  the three faces cross-contaminate into a family-average look. One face per generation = full
  detail + no blending.
- **Name each child's specific must-keep features** (Maria: leaner/older 13yo face, almond eyes
  at true size; Pepe: soft tousled hair, calmer eyes; Luna: ROUND full face + soft cheeks).
  List forbidden drift explicitly (don't enlarge eyes, don't slim the face, etc.).
- **"Identity Lock Reference" framing** beats "style reference" — signals to the model that the
  face is a canonical template, not a look to riff on.
- **Priority order** for any Maria/Pepe/Luna image: (1) the locked master face reference,
  (2) pose/expression, (3) outfit, (4) environment, (5) watercolor style. Identity never yields
  to style.
- Practical use: the **locked face image attached to the prompt does most of the work**; keep a
  SHORT identity-lock reminder in the prompt header; the full protocol lives here as the QA bar.
- Likeness has a ceiling — it won't be photographic, but "clearly them, lightly stylized" is the
  target. A close-but-off face can be nudged with a follow-up edit ("longer/more mature face",
  "rounder face, fuller cheeks").

**Status:** Maria refreshed & locked (maria_ref.png v2). Pepe + Luna pending same treatment.
Rome panels 1–8 keep the older faces — acceptable seam; likeness sharpens from here forward.

## Likeness target — RESOLVED to "recognizable, not photographic" (franchise decision)

Tried hard for photographic likeness (v2 refs + Identity Lock protocol + a P1 face-swap edit).
Conclusion: chasing exact real-face likeness fights the model on every panel and makes each one
a multi-round grind — unsustainable across 8 panels × multiple books. **Decision:** the target
is a consistent, appealing stylized (Ghibli/watercolor) look that's **clearly recognizable as
each kid**, anchored by hair + age order + outfits + personality — NOT facial accuracy. Kids
recognize themselves from the whole package, and can focus on the story.
- The heavy "resemblance outranks style / match exact facial structure" Identity Lock protocol
  is **demoted** — kept only as the gentler "attach refs for consistency & recognizability."
- Canonical refs are now **v3**: standardized set (same blue shirt, white background, straight
  front pose) so the face is the only variable across the three.
- **Rome's 8 panels stay as published** (old faces) — not re-rendered. The new look starts from
  the next thing generated (cover / Hogwarts).

## Canon v1.1 — Maria recast (deliberate franchise-level decision)

- **2026-06-07:** Maria recast from "pretends to be unimpressed / phone-camera motif" to
  **curious, adventurous, proactive — the first to lean in and lead; no phone.** This intentionally
  touches the LOCKED Production Bible (character canon), which is exactly the kind of change the
  governance model permits — by deliberate decision, not drift. Manifest, `docs/CHATGPT_CONTEXT.md`,
  and `HANDOFF.md` updated together so no future session regenerates old-Maria. Maria's recurring
  motif is now **TBD** (explorer's notebook is a candidate; no new prop locked yet). *(starts from
  Hogwarts; Rome stays as published.)*
