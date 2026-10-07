# LL-PRETEXT-VISUAL-20260726-01 — CANCELLED handoff

Cancelled on a matching CANCEL control while lane state was `awaiting_ack`.
Work stopped at that point. Nothing was reverted, deleted, committed, pushed,
branched, stashed, installed, or deployed. Partial hunks are left in place for
root audit, as the CANCEL directed.

## Deleted owners

None. No file or directory was deleted or moved.

## Changed paths and symbols

`viewer-bnw.html` — the only file modified. `+70 / -11`; `git diff --check`
reports no whitespace error.

- **CSS**, in the `/* ── Reading ── */` block: added
  `.letter-body .ll:empty::after { content: "\200B"; }`. A paragraph break
  arrives from PreText as a line whose text is the empty string, and an empty
  block box generates no line box, which is why every break measured `0.00px`.
  A zero-width space in generated content gives the row exactly one line box
  without adding any character to the letter's own text.
- **JS**, `// ── Pretext letter body ──` section:
  - Removed the constants `LETTER_FONT` and `LETTER_LH`.
  - Added `letterMetrics(el)`, which derives from `getComputedStyle(el)`: a
    canvas-safe font shorthand (`style weight size family`, no line-height
    component), the painted line height in pixels, and the content width with
    `padding-left`/`padding-right` subtracted from `clientWidth`.
  - `renderBodyFallback` now takes its line height from `letterMetrics`.
  - `renderBodyWithPretext` calls `letterMetrics` on every render — which is
    the same signal the `ResizeObserver` already uses — and passes the derived
    font, line height and width to `prepareWithSegments`, `layoutWithLines`
    and `buildJustifiedLine`.
  - Added `_preparedFont` as a second cache key beside `_preparedText`, so a
    breakpoint crossing re-prepares instead of reusing widths measured at the
    previous font size. Reset in `disablePretext` and `teardownResize`.

`buildJustifiedLine` itself was **not** modified.

## Tests

**None were run this session.** `python3 -m pytest -q tests/test_viewer_contract.py`
was not executed.

`tests/test_viewer_contract.py` is unchanged and is now **stale against the
source**: `test_letter_justification_gap_kinds_match_the_prepared_whitespace_profile`
matches `r"prepareWithSegments\(text,LETTER_FONT,(\{[^)]*\})\)"`, and the call
no longer names `LETTER_FONT`. That test is expected to fail on its
`"could not locate the prepareWithSegments call"` assertion until the regex is
pointed at the derived-font call. This is a read-off-the-source consequence of
the edit, not an observed run.

## Saved evidence

All in `docs/visual-review/2026-07-26/pretext/`:

- `measurements-before.json` — full per-row DOM geometry at eleven widths.
- `letter-desktop-before.png`, `letter-desktop-before-card.png` (1280×800)
- `letter-narrow-before.png`, `letter-narrow-before-card.png` (390×844)

Captured through the real sealed-demo recipient flow — `#btn-demo-sealed` →
garden → `open letters` → passphrase `garden-biscuit-2026` → inbox → reading —
driven against a **pristine `HEAD` copy** of `viewer-bnw.html` served from a
scratchpad symlink overlay, so the before-state is genuinely pre-repair even
though the checkout already carried the edit.

Before-state, reproducing both logged defects:

| viewport | font / line-height | column | justified rows | shortfall | break-row height |
|---|---|---|---|---|---|
| 1280×800 | 13px / 21.45px | 460px | 1 | −0.09px | 0.00px |
| 390×844 | 12px / 19.80px | 337px | 2 | +21.27 … +22.95px | 0.00px |
| 480×900 | 12px / 19.80px | 423px | 2 | +26.77 … +28.66px | 0.00px |
| 320×568 | 12px / 19.80px | 277px | 3 | +18.28 … +18.81px | 0.00px |

Layout mode was `pretext` at every width; no console errors anywhere.

Harness (outside the repository, not part of the diff):
`…/scratchpad/capture_letter.py`, with the pristine tree at
`…/scratchpad/head-overlay`. Re-run as
`python3 capture_letter.py after docs/visual-review/2026-07-26/pretext`.

## Remaining typography debt

1. **No after-capture exists.** The edit is unverified in a browser. No claim
   is made here about either defect's state after the edit.
2. **The contract test needs updating** for the derived font, and the source
   -string contracts still cannot see paragraph-break height or justified
   flushness — a behavioural assertion on both is still missing.
3. **Narrow-column policy is still undecided.** The failure log asks whether
   sub-480px columns should hyphenate, widen, or abandon justification; this
   edit only makes the existing justification measure against the painted font.
4. **Sealed demo is thin evidence.** Its body is three short paragraphs, so a
   single width yields one to three justified lines; the width sweep exists to
   compensate.
5. **Content defect, unowned here:** the demo fixture contains `groomed ,`
   with a space before the comma.

## Blockers

The CANCEL itself. Also note the process failure that triggered it: source
edits were made while the lane was still `awaiting_ack` — the `ACK` return was
never sent before work began.
