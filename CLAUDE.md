# Working in this repo

## Branching

Always merge straight to `main` and push — do not ask first, and do not open a
pull request unless explicitly asked for one. Work can be committed on a feature
branch, but finish the job by merging it into `main` and pushing `main`.
`main` is what GitHub Pages serves, so a change is not delivered until it is on
`main`.

## What this is

A single static page (`index.html`) hosted on GitHub Pages — a phone-first mood
and health tracker. No build step, no dependencies, no server, no accounts. All
data lives in the visitor's `localStorage` under the key `moodtracker.v1` and
must never be sent anywhere.

Keep it that way: no npm packages, no CDN scripts, no analytics, no network
requests. Markup, styles, and logic all stay inline in `index.html`.

## Changing what gets tracked

The log form, history rendering, and CSV export are generated from three lists at
the top of the `<script>` block in `index.html`:

- `SCALES` — the 1–5 tap scales (`key`, `label`, and five `{v, emoji, label}` options)
- `TAGS` — the chip labels
- `SLEEP` — the same shape as one scale, kept separate because it is asked once
  per night rather than once per entry (see below)

Edit those rather than the rendering code. Keep existing `key` values stable —
they are the field names in stored entries, and changing one orphans that field
in everyone's saved data.

## Sleep is per night, not per entry

`SLEEP` is answered at most once per night. A night is keyed by the morning it
ended (`nightKeyNow()`; before 4am that is still the previous morning), and the
answer is stored as `sleep` plus `sleepFor` on whichever entry answered it.
`sleepRecord(nightKey)` is the single check for "do we already have this night" —
it drives whether the card asks, collapses, or edits in place. Answering an
unrecorded night rides along in the draft; changing a recorded one mutates that
stored entry and saves immediately, so a night never gets two answers.

## Conventions

- Plain ES5-style JavaScript in one IIFE, no frameworks or transpilation.
- Build DOM nodes with `textContent` for anything user-supplied; `innerHTML` is
  only for static markup.
- Mobile first: 44px minimum tap targets, `env(safe-area-inset-*)` padding, and
  both colour schemes via `prefers-color-scheme`.
- Every stored entry keeps `id` and `ts` (ISO 8601, UTC); all other fields are
  optional and may be absent on older entries.

## Verifying a change

There is no test suite. Serve the folder and drive it in a mobile viewport
before pushing:

```sh
python3 -m http.server 8000
```

Check at minimum: saving an entry, reload persistence, the History tab, and
JSON/CSV export.
