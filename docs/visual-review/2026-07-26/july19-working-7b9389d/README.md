# July 19 working Garden visual state

This package records the visually functional Garden state the operator meant
by the “July 19 state”: the pre-rewrite working snapshot preserved as orphan
commit `7b9389de21edb67a15b261aae25b2350b53a49a9`.

The preservation commit itself was created on July 22. It identifies the
working pre-rewrite snapshot; it is not the July 18 deployed tree
`262050d`, whose viewer is visibly different and much sparser.

## Exact runnable source

- preserved viewer blob: `59dc49a820d07d1b6a1741e17aafe6d075f6c99d`
- local package: `archive/legacy-repo-7b9389d/`
- exact historical source/code paths: 85 of 89
- excluded compromised paths: four, listed in the archive `PROVENANCE.json`
- synthetic safe v1 fixture substitutions: three, from `143ed5d`
- local URL:
  `http://127.0.0.1:8876/archive/legacy-repo-7b9389d/viewer-bnw.html`
- entry action: `[get demo letter]`

## Visual engine

The Garden is a custom DOM character-grid renderer, not PreText. It measures
the browser’s character cell, derives responsive rows and columns, draws into
a colored `ScreenBuffer`, and blits changed frames into DOM row elements at
about 20 fps.

Its layer order is:

1. Background and ground
2. Procedural plants
3. Weather and click particles
4. Ambient and relationship creatures
5. Post-completion special art

PreText 0.0.4 is loaded separately from jsDelivr for letter-body typography.

## Plants

The generated visual catalog contains seven generator families:

- Pine
- Oak
- Bush
- Flower
- Grass
- Mushroom
- Fern

Flower generation adds five distinct forms:

- Daisy
- Tulip
- Sunflower
- Wildflower
- Rose

That is eleven visible plant identities when flower forms are counted
separately. `willow` appears in two canopy classification sets but has no
generator in this snapshot and must not be claimed as rendered.

The bundle’s stable `garden_seed` regenerates the same non-overlapping planting
layout for the current viewport, season, and seed. Resizing regenerates the
responsive layout.

## Motion and pointer behavior

- The engine advances at roughly 20 fps.
- Gusty wind is the product of two slow oscillators, clamped to `[-0.65, 0.65]`.
- Grass leans with wind and an independent per-blade phase.
- A deterministic subset of canopy cells shimmers without input.
- Moving the pointer within a five-cell radius of foliage locally increases
  rustling. The pointer becomes a hand over the generated collision map.
- Clicking a deciduous canopy emits up to ten leaf particles from the clicked
  plant neighborhood.
- Clicking a pine emits up to six needle fragments.
- Click effects are presentation particles; they do not mutate persistent
  Garden layout or plant identity.

## Seasons, time, and weather

- Spring favors flowers and grass, can flower grass tips, creates butterflies,
  and produces light rain.
- Summer keeps broad vegetation, creates butterflies, and creates three to
  five blinking fireflies during evening.
- Autumn recolors oak and bush foliage, greatly reduces flowers, produces
  heavier rain, and releases rotating falling leaves from real canopy cells.
- Winter favors pine, removes generated flowers, dulls grass, produces snow,
  and accumulates snow on plant top surfaces and the ground.
- Day uses the light palette.
- Evening uses a warm amber sky/ground gradient.
- Night uses a dark palette, deterministic stars, and one of eight
  phase-aware moon drawings calculated from the real lunar cycle.

Rain responds to wind, fragments when it hits a plant collision cell, and
splashes on the ground. Snow settles against the per-column top surface of
plants before accumulating downward.

## Creatures

- Spring and summer create one or two animated butterflies with four-frame
  wing cycles and curved movement.
- Summer evening creates blinking, slowly drifting fireflies.
- Non-winter Gardens periodically spawn ambient birds after roughly 250–600
  engine ticks. Birds cross continuously at 0.42 cells per tick using
  `\v/`, `_v_`, `/v\`, `_v_`; some events are three-to-five-bird flocks.
- The authenticated Garden can reveal one relationship animal: cat, bird,
  rabbit, or turtle.
- Relationship tiers change at 0, 3, 7, and 14 trust actions. Each animal has
  distinct peek, approach, movement, idle, bonded, hover, and feed poses.

## Recipient state

The recipient opens a `.lateletter` or the safe demo fixture. The Garden is
visible before letter decryption. A sealed letter uses PBKDF2-SHA256 and
AES-256-GCM in WebCrypto.

Persistent recipient facts use IndexedDB with localStorage fallback:

- Read letters
- Visit count
- First-run receipt
- Discovered Garden gifts
- Animal trust actions
- Last visit

Gifts can unlock by date, cumulative visits, or reading a specific letter.
Animal appearance is one such gift. Feeding changes persistent trust; simple
foliage hover and click particles do not.

## Live verification

The saved captures verify:

- Night palette, stars, and moon
- Pointer-local oak rustle and pointer cursor
- Click-triggered leaf burst
- Autumn plants and rain
- Winter plants and snow
- Spring flowers and butterflies
- Summer evening palette
- Summer-night ambient bird flight

`08-summer-night-working-motion-960x540-10s.gif` is a real 100-frame,
10-fps, 10-second browser capture with 96 distinct source frames.
