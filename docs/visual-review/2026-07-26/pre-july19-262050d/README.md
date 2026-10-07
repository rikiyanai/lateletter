# Exact pre–July 19 runnable Garden baseline

This folder replaces the false `526ab9e` archive-derived comparison baseline.
It records the last committed runnable tree before July 19:

- tree commit: `262050d25b46fae893c109e2d4cd9aec06b4f2b2`
- commit date: `2026-07-18T00:51:43-04:00`
- commit subject: `fix(pages): restore lowercase project URL`
- exact `viewer-bnw.html` Git blob: `5632ab0c58aa77ff1330d2599d52fcadc625b538`
- exact viewer file SHA-256: `555a9384393157d36226a38c6e0e697d8c6626318bc5c7c5cc315a0f4e017224`
- last commit that changed that viewer: `5b7dae80257f76e3778f309e4a82e23d0649485e`
- historical deployment recorded by the repository: run `29631258927`

## Reproduction

The tree was exported without checkout or worktree:

```bash
mkdir /private/tmp/lateletter-prejuly19-262050d
git archive 262050d25b46fae893c109e2d4cd9aec06b4f2b2 |
  tar -x -C /private/tmp/lateletter-prejuly19-262050d
cd /private/tmp/lateletter-prejuly19-262050d
python3 -m http.server 8879 --bind 127.0.0.1
```

The captured recipient flow was:

1. Open `http://127.0.0.1:8879/viewer-bnw.html`.
2. Activate `[get demo letter]`.
3. Wait 2.5 seconds for the live Garden.
4. Capture the 900×968 browser viewport.

`recipient-garden-900x968.jpg` has SHA-256
`d8b8da2279666b047d14ddd8d0be7900e913639dcc19d98fe50cb21c16184e37`.

`recipient-garden-motion-720w-10s.gif` is a real 100-frame, 10-fps,
10-second capture of that same live HTML at 720×774. It has SHA-256
`3b20b0f9547459e6d9b4a260852c4d46404e23e8a86508208385a12c6672df96`.

`recipient-garden-motion-contact-sheet.jpg` samples five moments from the GIF
and has SHA-256
`1572204e6ead189fc510598956670433f46b9426e6a6572ccbd0e4bb46590820`.

## Evidence boundary

This is the correct historical pre–July 19 comparison baseline. It proves the
exact runnable/deployed lineage; it does not independently prove operator
acceptance.

The historical viewer imports `@chenglou/pretext@0.0.4` from jsDelivr for
letter-body layout. Its Garden is a custom DOM renderer, not PreText. Therefore
this package is historical evidence and cannot be used to claim that the later
all-HTML/PreText Garden requirement was already met.
