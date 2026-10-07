# LL-GARDEN-VISUAL-20260726-03 — composition and art pass

Lane `%48` (pid 4367), window `@28`, epoch `FE990DBB-5A95-4AF6-9D7D-8586F448737E`,
nonce `AC541548-EEC5-4B93-A65B-D29E7F19AA1E`, origin REWORK.
Starting state `main b274797` with the preserved second handoff
(renderer 1292/154, renderer test 827/23). No commit or index mutation.

## Changes

`web/garden-renderer.mjs` (1292/154 → 1338/154)

**Sky reservation.** The band was tuned twice. Raising it to 72% of the frame
moved the top of the Garden up but opened a new gap between the lowest cloud and
the treetops, because the sky then stopped well above where content began
(checkpoint `12`, 13 blank lines). It now sits at
`min(36, 58% of frame)`, so the sky reaches down to roughly where planting
starts and the clouds that fill it close the gap rather than hovering above it.
The ground-plane ceiling stays raised at 30 lines, so the plane itself is deep.

**Every starter species enlarged, not only trees.** Hydrangea, wisteria, rose,
tulip, sunflower, lavender, water lily, meadow grass, ivy and rosemary all gain
foliage, a stem of real length and a base — typically 3–4 lines becoming 6–7 at
the established stage and 4–5 when young. A bed of the previous pictures read as
scattered punctuation and left the plane bare between the trees.

**All four animals gain species bodies.** A new `ANIMAL_BODY` table is composed
below every pose, so all eight pose families gain a body in one place and cannot
drift apart: cat a long low body with tail, rabbit a rounder body on big hind
feet, turtle a scuted shell on splayed legs, bird a small upright body on thin
legs. Every animal is now 5 lines at full density and survives reduction at
narrow density with its body intact, so species remain distinguishable on a
phone.

## Assertion updated

`responsive compositor selects bounded visual detail by viewport` capped compact
art at 2 lines for fixtures and 3 for everything else. That cap forced every
plant and animal into a stub at narrow widths — the heap the root rejected — and
is incompatible with multi-line animals at narrow density. The budget is now
3 lines for fixtures and 6 for plants and animals, collectibles exempt because
they carry their own compact picture. A new assertion was added in the same test
requiring that reduction still genuinely reduces: a mature oak must be shorter
at compact density than at full, so the raised budget cannot become no budget.

## Tests

| file | pass | fail |
| --- | --- | --- |
| `test_garden_input.mjs` | 3 | 0 |
| `test_garden_live_runtime.mjs` | 25 | 0 |
| `test_garden_renderer.mjs` | 55 | 0 |
| `test_garden_world.mjs` | 10 | 0 |

Run per file: `node --test <directory>` fails module resolution on this Node
build, a runner invocation error rather than a test failure.
`git diff --check` on both allowed paths: clean.

## Captures

`docs/visual-review/2026-07-26/garden/`

- `12-composition-*` — enlarged plants and animal bodies, band at 72%
- `13-final-*` — **current state**, four images: `desktop-1600x1000`,
  `narrow-390x844`, and `persisted-` variants of both

`persisted-` images are a reload of the same browser context after the world has
been written to storage, so the clean starter and the persisted world are both
recorded.

Longest run of entirely blank lines, as a proxy for the void:

| capture | desktop | narrow |
| --- | --- | --- |
| `07` (first rejection) | 18 | 26 |
| `11` (second rejection) | 9 | 8 |
| `13` (current) | **6** | **8** |

Inked lines rose from 33 to 53 of 66 on desktop and 23 to 41 of 64 on narrow
across the three passes.

## Remaining debt

1. Narrow still shows an 8-line gap; the desktop gap is 6. Neither is a void,
   but both are the emptiest part of the frame.
2. Starter plants carry few enough organs to sit below the mature tier, so the
   tallest tree art is reachable only after growth, not from a clean starter.
3. Animal bodies are composed rather than authored per pose, so a body does not
   change shape between, say, resting and playing — only the head and front do.
4. Weather, seasons other than summer, and evening/night composition were not
   re-reviewed in this pass.
5. No operator visual acceptance has been given for any capture here.

## Untouched

`viewer-bnw.html`, `web/garden-world.mjs`, `web/garden-runtime.mjs`,
`web/garden-sky.mjs`, `docs/FAILURE_LOG.md`, all archives, and every terminal or
Python owner. Their pre-existing dirty hunks are byte-identical to how this lane
found them (70/11, 44/5, 26/1, 9/1).

LL-GARDEN-VISUAL-HANDOFF-03
