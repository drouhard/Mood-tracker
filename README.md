# Mood &amp; Health Tracker

A minimal, phone-first mood and health tracker. One static page, no build step, no
account, no server. Entries are timestamped automatically and stored **only in
your browser's local storage** — nothing is ever sent anywhere.

## Live site

Once GitHub Pages is enabled (see below):

```
https://drouhard.github.io/Mood-tracker/
```

Open it on your phone and use **Share → Add to Home Screen**. It then launches
full-screen like an app and works offline (the page is cached by the browser;
your data is local either way).

## What you can log

Three 1–5 scales, plus tags and an optional note:

| Field  | 1 | 2 | 3 | 4 | 5 |
| ------ | - | - | - | - | - |
| Mood   | Awful | Low | Meh | Good | Great |
| Energy | Drained | Low | Steady | High | Wired |
| Body   | Rough | Achy | OK | Fine | Strong |

Plus **last night's sleep** (Terrible → Restless → OK → Good → Great), which is
asked once per night rather than once per entry — see below.

Tags: Slept well, Poor sleep, Exercised, Outside, Social, Headache, Nausea,
Pain, Anxious, Foggy, Caffeine, Alcohol, Ate well, Meds taken, Work stress.

Every field is optional — tap only what you care about, then **Save entry**.
Minimum log is two taps. The timestamp is added for you.

## Tabs

- **Log** — the tap grid. Tapping a selected option again clears it. Last
  night's sleep sits at the top and collapses to what you recorded (with a
  **Change** button) once that night has been answered.
- **History** — 7-day stats, a 14-day daily-average-mood bar chart, and every
  entry grouped by day (with per-entry delete).
- **Data** — export JSON or CSV, import a JSON backup, delete everything.

## Last night's sleep

Sleep is a property of a night, not of a moment, so it is asked at most once per
night:

- A night is identified by the morning it ended (`sleepFor`, a local `YYYY-MM-DD`
  day key). Before 4am you have not slept tonight yet, so "last night" still
  means the previous morning's night.
- If that night has no answer yet, the scale is shown and the answer is saved on
  whichever entry you save next. Log five more times that day and it is not
  asked again.
- If it does have an answer, the card collapses to *Recorded: 😴 Great*.
  **Change** re-opens the scale and edits that stored answer in place, so a
  night never ends up with two conflicting answers.
- The question comes back on its own once the next night rolls around, without a
  reload.

## Changing what you track

The form is generated from lists at the top of the `<script>` block in
[`index.html`](index.html):

```js
var SCALES = [
  { key: 'mood', label: 'Mood', options: [ { v: 1, emoji: '😞', label: 'Awful' }, ... ] },
  ...
];

var TAGS = ['Slept well', 'Poor sleep', ...];
```

Sleep is defined just above them as `SLEEP` — same shape, kept separate because
it is asked per night rather than per entry. Edit its `options` to relabel the
scale.

Add, remove, or rename entries in those lists and the log grid, history view,
and CSV export all follow. Old entries stay readable: a scale that no longer
exists is simply not displayed, and a new one shows as blank for past entries.
Keep `key` values stable — they are the field names in stored data.

## Your data

- Stored under the local-storage key `moodtracker.v1` as
  `{ version: 1, entries: [...] }`.
- Each entry looks like:

  ```json
  {
    "id": "m1a2b3c4d5",
    "ts": "2026-08-16T14:32:07.881Z",
    "mood": 4,
    "energy": 3,
    "body": 5,
    "sleep": 4,
    "sleepFor": "2026-08-16",
    "tags": ["Exercised", "Slept well"],
    "note": "Long walk after lunch"
  }
  ```

- `sleep`/`sleepFor` appear only on the one entry per night that answered the
  sleep question; every other entry omits them. `sleepFor` is the local day key
  of the morning that night ended.

- Timestamps are stored in UTC (ISO 8601) and displayed in your device's local
  time.
- Local storage is per-browser and per-device. It does **not** sync between your
  phone and laptop, and it is erased if you clear site data or use private
  browsing. **Export a JSON backup periodically** — the Data tab does this in
  one tap, and Import merges a backup back in without creating duplicates.

## Enabling GitHub Pages

In the repository: **Settings → Pages → Build and deployment**, set
*Source* to **Deploy from a branch**, *Branch* to **`main`**, folder **`/ (root)`**,
then Save. The site is live at the URL above within a minute or so.

## Running locally

It is a single static file — open `index.html` in a browser, or serve the
folder if you want the manifest/PWA bits to work:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app — markup, styles, and logic, no dependencies |
| `manifest.json` | Web-app manifest for Add-to-Home-Screen |
| `icon.svg` | App icon |
| `.nojekyll` | Tells GitHub Pages to serve the files as-is |

## Status

MVE (minimum viable everything-you-need). Deliberately small. Likely next
steps as the tracking needs firm up: custom tags you can add from the phone,
editing a past entry, per-tag correlation views, and a reminder nudge.
