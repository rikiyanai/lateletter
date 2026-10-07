# LL-GARDEN-VISUAL-20260726-02 — rework report

Lane `%48` (pid 4367), window `@28`, epoch `FE990DBB-5A95-4AF6-9D7D-8586F448737E`,
nonce `0F6FF7B5-E8DD-4663-B32F-2C1191E09989`, origin REWORK.
Starting state `main b274797` with the preserved rejected changes
(renderer 1205/154, renderer test 807/23). No commit or index mutation.

## Assertions deleted and replaced

1. **`every canonical collectible keeps its semantic glyph at every density`** —
   removed. It asserted an exact one-character picture for all eight identities,
   which guaranteed uniqueness but forbade recognisability and blocked any
   purpose-drawn art. Replaced by
   **`every canonical collectible has recognisable unique art at every density`**,
   which keeps the uniqueness guarantee, adds four viewports instead of three,
   and additionally requires at least two lines and four inked characters per
   picture so a bare mark cannot come back.
2. **`browser renderer uses per-object parallax and projection-hotspot hit
   testing`** — the single assertion `frame.lines[y][x] === '⌇'` was replaced.
   It pinned the legacy one-character feather. The test now checks that the
   collectible's own final art line is painted at its anchor, so it still proves
   parallax placement and hotspot hit testing without pinning a bare glyph.

No other assertion was weakened or removed.

## Art and layout changes

`web/garden-renderer.mjs` (1205/154 → 1292/154)

- `COLLECTIBLE_ART` rewritten from single characters to `{full, compact}`
  purpose-drawn pictures for all eight canonical identities plus five family
  fallbacks. The compact picture is drawn at its own size rather than produced
  by trimming the full one.
- `collectibleArt(object, lod)` resolves catalogue id, then label, then family,
  and selects the picture for the density. A collectible unknown to this
  renderer still receives a drawn fallback rather than a placeholder.
- `presentationLod` excludes collectibles entirely, and its plant and animal
  budgets rise from 3/4 lines to 6/8. A mature oak reduced to three lines is no
  longer a tree.
- Oak, willow and pine gain a mature tier and taller establishing art: oak
  9→11 lines, willow 8→11, pine 8→10 at maturity, each with a real trunk, so
  canopies occupy the headroom above the ground plane.
- `groundRows` ceiling raised 16 → 24. The old ceiling was binding on any large
  display, holding the plane well short of the band and leaving the rows between
  the treetops and the clouds empty.
- Cloud population density raised (one per 26 columns in summer, 20 in winter,
  ceiling 14) with a floor of 4; distant-bird floor raised to 3. A narrow frame
  still reserves a tall sky, and the previous floor of 2 left a phone sky bare.

## Tests

| file | pass | fail |
| --- | --- | --- |
| `test_garden_input.mjs` | 3 | 0 |
| `test_garden_live_runtime.mjs` | 25 | 0 |
| `test_garden_renderer.mjs` | 55 | 0 |
| `test_garden_world.mjs` | 10 | 0 |

Run per file: `node --test <directory>` fails module resolution on this Node
build, which is a runner invocation error rather than a test failure.
`git diff --check` on both allowed paths: clean.

## Captures

`docs/visual-review/2026-07-26/garden/`

- `08-collectibles-and-canopy-*` — collectible art and taller trees
- `09-plane-depth-*` — ground plane ceiling raised
- `10-final-*` — cloud density raised
- `11-final-*` — **current state**, four images: `desktop-1600x1000`,
  `narrow-390x844`, and `persisted-` variants of both

The `persisted-` images are a reload of the same browser context after the world
has been written to storage, so both a clean starter and an existing persisted
world are recorded, as required.

Longest run of entirely blank lines, as a proxy for the void:

| capture | desktop | narrow |
| --- | --- | --- |
| `07` (rejected) | 18 | 26 |
| `11` (current) | 9 | 8 |

## Remaining visual debt

1. A gap of roughly nine lines persists on both viewports between the lowest
   cloud and the treetops. It is half what the rejected capture showed, but it
   is still the emptiest part of the frame.
2. Only oak, willow and pine gained height. The other ten species keep their
   original silhouettes, so a Garden without a tree still reads low.
3. Starter plants carry few enough organs to sit below the mature tier, so the
   tallest art is not reachable from a clean starter world; the mature tier is
   visible only once a plant has grown.
4. Animals were not touched in this rework and remain three-line pictures.
5. No operator visual acceptance has been given for any capture here.

## Untouched

`viewer-bnw.html`, `web/garden-world.mjs`, `web/garden-runtime.mjs`,
`web/garden-sky.mjs`, `docs/FAILURE_LOG.md`, all archives, and every terminal or
Python owner. Their pre-existing dirty hunks are byte-identical to how this lane
found them (70/11, 44/5, 26/1, 9/1 respectively).

LL-GARDEN-VISUAL-HANDOFF-02
