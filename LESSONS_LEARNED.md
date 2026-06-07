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
