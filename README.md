# LateLetter

LateLetter is a small tool for writing private letters that someone can open
later in a browser.

It is built for messages that should feel cared for after the author is not
there to send them: birthday notes, grief letters, instructions, memories,
apologies, encouragement, or anything else that should reach one person at the
right time.

The recipient experience is intentionally simple: open a link or `.lateletter`
file, enter the passphrase, walk through a quiet ASCII garden, and read the
letters that are available for that date.

## What it looks like

The public viewer is a quiet ASCII garden. Letters open from that garden when
the recipient has the passphrase and the delivery date has arrived.

[![Legacy nighttime garden](docs/lateletter-legacy-night-garden.gif)](docs/lateletter-legacy-night-garden.gif)

The terminal authoring flow is separate. It helps the author draft and package
the letter before anything is sent.

![LateLetter terminal authoring demo](docs/demo.gif)

## How it works

LateLetter has two parts:

- The author tool turns letter drafts, dates, hints, and garden details into a
  sealed `.lateletter` bundle.
- The browser viewer lets the recipient open that bundle. The passphrase opens
  the private letter text; the garden, author name, hint, and delivery dates are
  visible around it.

The point is not just storage. The garden gives the recipient a place to arrive,
wait, and return to, instead of handing them a bare encrypted file.

## Quick start

Open `viewer-bnw.html` in a browser. Click **[get demo letter]** to load the
demo bundle, or drop your own `.lateletter` file.

Demo passphrase: `garden-biscuit-2026`

The passcode-locked demo opens only with that passphrase. A wrong passphrase is
rejected. Sealed bundles use PBKDF2-SHA256 and AES-256-GCM for the private
letter body.

## Send a real letter

```bash
cp letters/letter_source.example.json letters/my_letter.json
# edit letters/my_letter.json — your words, dates, hint, and Garden program
python3 make_letter.py letters/my_letter.json letters/for_them.lateletter
python3 make_letter.py --verify letters/for_them.lateletter   # round-trip check
```

Send `letters/for_them.lateletter` to your friend with a link to the viewer
(https://rikiworld.com/lateletter/). Tell them the passcode in person.
The builder asks for a fresh passphrase twice; it is never stored in the source
JSON or output bundle. Keep the editable source and first sealed output under
ignored `letters/` filenames. The tracked example contains synthetic text only.

### Or send just a link

Build and verify in ignored private storage first. After final copy approval,
copy only the sealed output into `public_letters/` and commit that bundle. A
standard GitHub Pages site is public even when its source repository
is private, so anyone with the URL can download the bundle; only someone with
the passcode can open its private content:

```bash
python3 make_letter.py letters/my_letter.json letters/to-personx.lateletter
python3 make_letter.py --verify letters/to-personx.lateletter
cp letters/to-personx.lateletter public_letters/to-personx.lateletter
git add public_letters/to-personx.lateletter && git commit -m "letter" && git push
```

After the Pages deploy, share `https://rikiworld.com/lateletter/to-personx/`
(passcode gate included — no file handling for the recipient at all).
Heads-up: your author name, the passphrase hint, and delivery dates are
plaintext in the bundle, so anyone with the URL (or browsing the repo)
can see those — the letter body and gift sentiments stay sealed.
Demo link once deployed: https://rikiworld.com/lateletter/to-a-friend/ (passcode `garden-biscuit-2026`).

## Production safety note

`https://rikiworld.com/lateletter/` currently serves the legacy pretext-monolith
viewer. That deployed version is kept as a rollback-safe release:
[`lateletter-legacy-pages-2026-07-28`](https://github.com/rikiyanai/lateletter/releases/tag/lateletter-legacy-pages-2026-07-28).


## Project structure

```
viewer-bnw.html          Single-file browser viewer (garden + letter reader)
test_fixture.lateletter  Demo bundle (Buddy's letter)
src/lateletter/          Python author tools (CLI, intake, bundling)
docs/                    README GIFs, release notes, visual-review receipts
```

## Garden

The viewer renders an ASCII garden that changes with the seasons. Plants grow procedurally from a seed. Rain falls in spring and autumn, snow in winter, butterflies in summer. Clicking plants scatters particles. An animal companion appears after the first visit.

## Dev tools

Load the demo fixture (no HMAC = dev mode) to unlock dev keybindings:

- `,` / `.` cycle seasons + night
- `Shift+B` cycle color modes
- `Shift+G` grid overlay with FPS
- `Shift+A` cycle animal types/tiers
- `f` feed the animal

## License

Private project.
