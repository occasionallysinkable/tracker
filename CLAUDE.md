# Day Tracker — project brief

A personal daily habit/points tracker. Self-hosted, no paid dependencies.

## How it's built
- `index.html` — the entire app. Plain HTML + CSS + vanilla JS, one file, no build step, no framework. Hosted on GitHub Pages (repo: `occasionallysinkable/tracker`, branch `main`, root folder). Live at https://occasionallysinkable.github.io/tracker/
- `Code.gs` — Google Apps Script deployed as a Web App from the owner's **personal** Google account. It reads/writes a Google Sheet with two tabs: `Daily` (one row per date) and `Config`. This is the only data store.
- The app talks to the sheet over the Apps Script `/exec` URL. That URL is stored in the browser's localStorage on each device (key `tally_url`) — it is NOT in the code and must never be committed.
- SheetJS is loaded from a CDN for the Excel export.

## Sync model
PC and phone both open the same Pages URL and read/write the same sheet. Sync happens on open, on refresh, and on save — not live. One row per date; re-saving a date overwrites that row rather than adding a new one.

## How it works
- **Items**: each has `id`, `label`, `unit` (points), `type` (`binary` = yes/no, or `count`), optional `max`. Editable in-app via Edit list; saved to the sheet's Config tab.
- **Scoring**: binary scores `unit` if set; count scores `value * unit`. Day total is the sum.
- **Target**: one daily points bar. It is NOT raised by hand on a schedule — when the last 7 logged days average at least `target + 2`, the app offers a bump. Item point values stay fixed; the target is the dial that rises.
- **Streak**: increments when the target is met. On a miss, one grace day per rolling 7 days holds the streak; otherwise it resets to 0. A miss is a soft pause, never a penalty.
- **Reward**: a weekly points threshold (week starts Monday). Crossing it unlocks a reward the user set in advance.
- **What's sticking**: per-item completion rate over the last 30 logged days — the signal for when to re-price or retire an item.
- **Date picker**: on both the home screen and the log screen, so a day can be logged after midnight for the correct date. Capped at today; no future dates.
- **Export**: builds an .xlsx from the full history on demand.

## Design intent — please preserve
- Warm grey theme (`--ink:#262624`, `--card:#30302E`), amber as the single accent reserved for the reward and target-met states. Not black, not white.
- Two-column grid on desktop, collapsing to one column under 640px.
- Tone throughout is calm and non-punitive. Copy must never shame a missed day.
- **Positive framing over punishment.** Score the good choice (`+1 for choosing the book`), not the bad one. Do not add negative-point items or a punishment loop unless the user explicitly asks — and even then it's meant only to protect an already-established habit.
- The "ate mindfully" item is deliberately a yes/no worth 1, not a counter. Do not convert it to a counter or otherwise make "eating less" score more points, even if asked to make items consistent. Every other "more is better" item stacks; this one intentionally does not.

## Rules
- Personal Google account only. Several of the user's other Google accounts belong to clients and must never be used for this project.
- Never commit the `/exec` URL, sheet ID, or any personal data.
- Past entries store the target that was in effect that day (`targetAt`) — changing the target later must not retroactively alter old days' pass/fail.
- Keep it dependency-free and buildless. It must keep working if every paid subscription lapses.

## Workflow
Edit `index.html`, commit, push to `main`. GitHub Pages redeploys in about a minute. Hard-refresh to see changes.
