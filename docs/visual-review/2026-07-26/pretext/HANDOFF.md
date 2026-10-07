# LL-PRETEXT-VISUAL-20260726-02 — verified handoff

Rework of the cancelled lane LL-PRETEXT-VISUAL-20260726-01. The partial hunk
that lane left in `viewer-bnw.html` (+70/−11) was **preserved, audited and
built on** — not rewritten. Tree state at start and end: `main` `b274797`,
no commit, push, branch, worktree, stash, install, or deletion.

## What changed

`viewer-bnw.html` — unchanged since the preserved hunk; audit only.

- CSS `.letter-body .ll:empty::after { content: "\200B"; }` gives a
  paragraph-break line one line box. A blank line reaches the renderer as an
  empty string, and an empty block box generates no line box at all.
- JS: `LETTER_FONT` / `LETTER_LH` deleted; `letterMetrics(el)` derives the
  canvas font shorthand, the pixel line height, and the padding-corrected
  content width from `getComputedStyle` on the painted element. Consumed by
  `renderBodyFallback`, `renderBodyWithPretext`, and the width handed to
  `buildJustifiedLine`. `_preparedFont` joins `_preparedText` as a cache key so
  a breakpoint crossing re-prepares instead of reusing stale widths; reset in
  `disablePretext` and `teardownResize`. `buildJustifiedLine` itself untouched.

`tests/test_viewer_contract.py` — this lane's only source edit.

- `test_letter_justification_gap_kinds_match_the_prepared_whitespace_profile`:
  the locator regex now matches `prepareWithSegments(text,font,…)`. It was
  stale against the derived-font call and would have failed.
- **New** `test_letter_measurement_derives_from_the_painted_computed_styles`:
  requires both constants gone, the derivation to read `getComputedStyle`, and
  both library calls plus the cache key to consume the derived values.
- **New** `test_paragraph_break_rows_are_empty_and_the_stylesheet_gives_them_a_line_box`:
  half source contract, half behavioural. It drives the vendored library
  through Node with the viewer's own options and requires a genuinely empty
  line for a blank line **before** checking the `:empty` rule — so the rule
  cannot go silently inert if PreText ever starts emitting a space instead.

## Tests

- `python3 -m pytest -q tests/test_viewer_contract.py` — 18 of 18 succeed.
- Regression bite check: all three contracts above were run against a pristine
  `HEAD` copy of the viewer and each fails there
  (`LETTER_FONT` present; `no rule gives an empty .ll row a line box`;
  `could not locate the prepareWithSegments call`). They test behaviour, not
  spelling of code that already exists.
- `git diff --check` on both allowed source paths — clean.

## Browser evidence

Real sealed-demo recipient flow, every time: `#btn-demo-sealed` → garden →
`open letters` → passphrase `garden-biscuit-2026` → inbox → reading. The
before run was driven against a pristine `HEAD` viewer served from a scratchpad
overlay, so the comparison is genuinely pre/post with one identical probe.

The two acceptance viewports are the first two entries, in bold.

| width | font / line-height | column | before: break line | before: justified shortfall | after: break line | after: justified shortfall | justified lines |
|---:|---|---:|---:|---|---:|---|---:|
| **1280** | 13px / 21.45px | 460px | 0.00px | −0.094 | **21.44px** | **−0.094** | 1 |
| **390** | 12px / 19.80px | 337px | 0.00px | +21.266 … +22.953 | **19.80px** | **−0.047 … −0.016** | 2 |
| 1024 | 13px / 21.45px | 460px | 0.00px | −0.094 | 21.44px | −0.094 | 1 |
| 834 | 13px / 21.45px | 460px | 0.00px | −0.094 | 21.44px | −0.094 | 1 |
| 768 | 13px / 21.45px | 460px | 0.00px | −0.094 | 21.44px | −0.094 | 1 |
| 600 | 13px / 21.45px | 460px | 0.00px | −0.094 | 21.44px | −0.094 | 1 |
| 481 | 13px / 21.45px | 363px | 0.00px | −0.078 | 21.44px | −0.078 | 2 |
| 480 | 12px / 19.80px | 423px | 0.00px | +26.766 … +28.656 | 19.80px | −0.109 | 1 |
| 430 | 12px / 19.80px | 375px | 0.00px | +25.297 … +25.531 | 19.80px | −0.078 … −0.016 | 2 |
| 360 | 12px / 19.80px | 316px | 0.00px | +19.578 … +21.562 | 19.80px | −0.016 | 2 |
| 320 | 12px / 19.80px | 277px | 0.00px | +18.281 … +18.812 | 19.80px | −0.016 … +0.031 | 2 |

Shortfall is the gap from the last painted glyph to the column's right edge, in
CSS pixels. The line's own box is deliberately not measured: a justified line
is a flex container that always spans the column, so it would always appear
flush no matter what the text inside it does.

Against each acceptance criterion, at 1280×800 and 390×844:

- A blank paragraph line measures one line box — 21.44px against a 21.45px
  line height, and 19.80px against 19.80px.
- Every justified non-final line lands within 0.1px of the right edge, well
  inside the 1px allowance, at all eleven widths.
- Final lines stay ragged: `Demo Author` (the last line) and every
  paragraph-ending line — `…before sealing a real letter.`, `With care,` — are
  unjustified.
- Layout mode is `pretext` at every width; no `fallback` class appears.
- Zero console errors and zero page errors at every width.

Files, all in `docs/visual-review/2026-07-26/pretext/`:

- `measurements-before.json`, `measurements-after.json` — per-line DOM geometry
  at eleven widths, with the console log for each.
- `letter-desktop-{before,after}.png` and `-card.png` (1280×800)
- `letter-narrow-{before,after}.png` and `-card.png` (390×844)
- `CANCELLED-HANDOFF.md` — the prior lane's record, left in place.

Harness lives outside the repository at `…/scratchpad/capture_letter.py`, with
the pristine tree at `…/scratchpad/head-overlay`. It is not part of the diff,
so re-running the evidence later means re-creating it.

## Remaining typography debt

1. **Operator visual sign-off has not been given.** The numbers meet the stated
   acceptance; whether the typeset column *looks* right is still the
   operator's call, and the failure-log entry stays open until then.
2. **The narrow-column policy is still undecided.** The failure log asks
   whether sub-480px columns should hyphenate, widen, or drop justification.
   This work only makes the existing justification measure the painted font;
   inter-word gaps at 277–337px still stretch noticeably on a sparse line,
   which is the texture of justification without hyphenation.
3. **The sealed demo is thin evidence by nature** — three short paragraphs
   yield one to three justified lines per width. The eleven-width sweep is the
   compensation for that, not a substitute for a long letter.
4. **The harness is not a repository test.** Nothing in CI re-measures painted
   geometry; the two new contracts guard the *shape* of the repair, and a
   future regression in painted output would need this harness re-run.
5. **Content defect, unowned by this lane:** the demo fixture contains
   `groomed ,` with a space before the comma.
6. **`docs/FAILURE_LOG.md` was not touched** — forbidden to this lane. The
   entry *"Letter typography measures with constants that contradict the
   stylesheet, and paragraph breaks occupy no space"* still reads
   `Status: OPEN. No fix has been attempted.` and needs the root's update.
