# LL-GARDEN-VISUAL-20260726-01 — lane report

Lane `%48` (pid 4367), window `@28`, epoch `FE990DBB-5A95-4AF6-9D7D-8586F448737E`.
Starting state `main b274797`, renderer 732/136 and renderer test 635/23 dirty.
No commit, branch, worktree, stash, or shared-index mutation was made.

## Deleted owners

None. No ownership moved.

## Changed paths and symbols

`web/garden-renderer.mjs` (732/136 → 1205/154 against `HEAD`)

- `gardenPresentationProfile` — gains `groundRows`, `groundBack`, `groundFront`,
  `groundSpan`; `yScale` and `centerY` now span the ground plane rather than the
  whole band; the band cap moves from a constant 22 lines to
  `min(34, 55% of frame)` so reserved sky stays proportionate to the display.
- New `maximumFlightLift(object)` — only a bird on an active intent leaves the
  ground, bounded to 6 lines and further capped by available headroom.
- New `stableArtFootprint(object, lod)` and `footprintRect(anchor, size)` —
  placement geometry is independent of both animation frame and focus.
- New `CLOUD_SHAPES`, `skyCloudPresentation`, `ambientBirdPresentation`, and
  `CanonicalGardenRenderer._drawSkyLife` — stratified, continuous,
  presentation-only sky life that never enters the layout array.
- `ambientEntityPosition` — optional `band` argument so small ambient life sits
  among the planting instead of anywhere in the airspace.
- `layoutGardenObjects` — feet land on soil lines; the vertical search is
  restricted to the ground plane; depth changes are charged 12× against 2×
  horizontal; the hit rectangle is carried by the same lift as the art and is
  still derived entirely from the projection-owned hotspot.
- `_drawGround` rewritten to paint the receding plane with a density gradient;
  `_drawGroundCover` jittered and distributed through the plane's depth;
  `_drawSky` projects the star catalogue into the sky region only;
  `_drawAmbient` takes the profile and animates a stable subset of butterflies.
- `FIXTURE_DECOR` rewritten: larger pictures, no duplicate pictures, no
  duplicate first lines, creature vocabulary (`v`, `>o<`, `{}`) avoided.

`tests/garden_adapters/test_garden_renderer.mjs` (635/23 → 807/23)

Six added tests: ground-rest geometry across three viewports; bounded flight and
lifted hit rectangle; layout stability across frames and focus; fixture picture
recognisability and uniqueness at three densities; sky-life continuity and
one-character distinctness; daylight sky occupancy and band proportion.

## Tests

Run per file, because `node --test <directory>` fails module resolution on this
Node build — a runner invocation error, not a test failure.

| file | pass | fail |
| --- | --- | --- |
| `test_garden_input.mjs` | 3 | 0 |
| `test_garden_live_runtime.mjs` | 25 | 0 |
| `test_garden_renderer.mjs` | 55 | 0 |
| `test_garden_world.mjs` | 10 | 0 |

`git diff --check` on both allowed paths: clean.

## Captures

`docs/visual-review/2026-07-26/garden/`, each at `desktop-1600x1000` and
`narrow-390x844`:

`01-ground-contract`, `02-sky-life`, `03-sky-density`, `04-band-proportion`,
`05-sky-region`, `06-fixture-art`, `07-ground-legibility`.

`07` is the current state.

## Visual debt

1. **Collectibles remain single characters.** Making them recognisable would
   delete the pre-existing contract *every canonical collectible keeps its
   semantic glyph at every density*, which asserts an exact one-character
   picture for all eight identities. That contract is part of the pre-existing
   hunks this lane was told to preserve, so the decision belongs to the root.
2. **The sky's middle band is still sparse.** Clouds and distant birds inhabit
   it, but roughly rows 8–44 of a desktop frame remain thin.
3. **Plant art is too short to reach the band above the plane.** The
   specification calls for trees extending several lines into the sky; current
   plant pictures stop a few lines above their roots, so the headroom reserved
   for canopies is unused.
4. **No operator visual acceptance has been given** for any capture here.

## Untouched

`viewer-bnw.html`, `web/garden-world.mjs`, `web/garden-runtime.mjs`,
`web/garden-sky.mjs`, `docs/FAILURE_LOG.md`, every archive, and every terminal
or Python owner. Their pre-existing dirty hunks are exactly as this lane found
them; they belong to the user or to other lanes.

LL-GARDEN-VISUAL-HANDOFF
