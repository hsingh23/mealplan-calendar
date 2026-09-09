# AGENTS.md — Guide for coding agents working in this repository

## What this is

A single-file meal planning web app. `index.html` contains ALL code:
markup, CSS, and JavaScript. `mealplanCalendar-bupcs3.json` is the
Mavo data store the page reads and writes. There is no build system,
no package manager, no test suite, no CI, and no server component.
Everything else in the repo is documentation.

## Commands

```sh
# serve locally (any static server works)
python3 -m http.server 8000        # then open http://localhost:8000/

# git basics
git status
git log --oneline

# validate the data file
python3 -m json.tool mealplanCalendar-bupcs3.json > /dev/null && echo OK

# history note: commit messages were rewritten 2026-09-08
# (messages-only filter-branch; hashes before that date are stale)
git log --format='%h %ad %s' --date=short
```

There is nothing to install, lint, or test. Verification is manual:
serve, load, interact (see "Verifying changes" below).

## Architecture map

```
index.html
├── <head>
│   ├── CDN deps: fullcalendar@5.10.2 (css+js), font-awesome 6.1.1,
│   │   mavo.min.css + mavo.min.js
│   └── <style> — all app CSS (cards, grid, calendar tweaks)
├── <body>
│   ├── <div mv-app="mealplanCalendar-bupcs3" ...>   ← Mavo root
│   │   ├── #calendar                                ← FullCalendar mount
│   │   ├── .meal-grid
│   │   │   ├── .meal-card.future-meals  (#future-meals-list, mv-list)
│   │   │   │    └── meal template: title, description, photo,
│   │   │   │        ingredients (mv-list), steps (mv-list),
│   │   │   │        shop/prep/cook date inputs, shopping checklist
│   │   │   └── .meal-card.past-meals    (#past-meals-list, mv-list)
│   │   │        └── item shows only when
│   │   │             date(shopDate) <= $today && date(prepDate) <= $today
│   └── <script type="application/json" id="initial">  ← seed data
│       (used by mv-init="#initial" on first run / when storage is empty)
└── <script>  ← app logic
    ├── document.addEventListener("mv-load", ...)  ← wait for Mavo data
    │   ├── new FullCalendar.Calendar(..., {
    │   │     events: function(...)  reads Mavo.all[0].root.liveData.data.meals
    │   │       → emits Shop:/Prep:/Cook: events with Font Awesome icon props
    │   │     eventContent: custom DOM rendering (icon + "Type: Meal" label)
    │   │     eventClick: only type === "prep" opens details })
    │   └── displayMealDetails(event) → fills #meal-info via template string
└── mealplanCalendar-bupcs3.json   ← persisted Mavo data (the "database")
```

Key coupling: the JS reads meals from `Mavo.all[0].root.liveData.data.meals`.
If the Mavo app name or property names (`meals`, `title`, `shopDate`,
`prepDate`, `cookDate`, `ingredients`, `steps`, `photo`, `description`)
change in markup/data, the FullCalendar event-source function must be
updated in lockstep, and vice versa.

## Conventions

- One file for the app. Keep markup + CSS + JS in `index.html`; do not
  split it without a deliberate decision (recorded in the diary).
- Data lives in `mealplanCalendar-bupcs3.json`; the inline `#initial`
  JSON should stay a valid seed of the same schema.
- Mavo auto-saves produce commits titled by the file; when committing
  by hand, use conventional commits (`feat:`, `fix:`, `chore:`, ...) —
  the 2026-09-08 rewrite established that style for this history.
- IDs in the shopping checklist are derived from ingredient text with
  whitespace replaced by `-` — keep that if you touch the checklist.

## Gotchas

- **`displayMealDetails` targets `#meal-info`**, an element that no
  longer exists in the current markup (the Meal Details card was
  removed in commit f2575b7). Clicking a Prep event silently does
  nothing. If you need event-click details back, re-add a
  `<div id="meal-info">` container.
- **The page requires internet access** — Mavo, FullCalendar, and
  Font Awesome load from CDNs; `<base href="https://mealplan-calendar.com/">`
  can also rewrite relative URLs when hosted elsewhere.
- **Calendar renders only on `mv-load`** — if Mavo fails (offline,
  storage unreachable), the calendar never initializes.
- **Past Meals uses `date()` wrapping** — compare dates as
  `date(x) <= $today`, not raw string comparison (that was a real bug,
  fixed in afca642).
- **`Mavo.all[0]`** relies on this being the only Mavo app on the page.
- **History rewrite (2026-09-08)**: commit hashes changed; any external
  links/notes referencing old hashes are stale. `backup/pre-docs-20260908`
  branch holds the pre-rewrite history locally (not pushed).
- The data file sometimes has no trailing newline and uses tabs —
  preserve existing formatting when editing to avoid noisy diffs.

## Verifying changes

1. `python3 -m json.tool mealplanCalendar-bupcs3.json > /dev/null` —
   data file is valid JSON.
2. Serve the repo and open the page; confirm:
   - calendar renders with Shop/Prep/Cook events having icons;
   - Future Meals cards show with dates and checklists;
   - Past Meals shows only meals with past shop+prep dates;
   - console has no errors (check DevTools).
3. `git status` — only intended files modified.

## Pointers

- `CHANGELOG.md` — every commit, newest first, with bullets.
- `architectural-diary/main.md` — narrative history and decisions.
- `architectural-diary/decisions/` — individual decision records.
- `prompt.md` — full spec to recreate this app from scratch.
- Mavo docs: https://mavo.io/docs ; FullCalendar v5 docs:
  https://fullcalendar.io/docs-v5
