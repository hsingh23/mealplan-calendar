# MealPlan Calendar

A zero-build, single-file meal planning calendar. Plan meals, track
shopping/prep/cooking dates on a month calendar, keep recipes and
shopping lists together, and edit everything in the page itself — the
page is the app and the database.

## Why

Meal planning spreadsheets and notes apps separate the *plan* (dates)
from the *recipe* (ingredients, steps) and the *shopping list*. This
app keeps all three attached to each meal and projects them onto a
month calendar, so "what do I shop for, when do I prep, when do I
cook" is visible at a glance. It is built entirely with declarative
HTML: [Mavo](https://mavo.io) provides inline editing and
GitHub-backed persistence, and [FullCalendar](https://fullcalendar.io)
renders the schedule. No server, no build step, no database to run.

## Features

- **Month calendar** (FullCalendar `dayGridMonth`) with color-coded
  event types per meal: **Shop** (cart icon), **Prep** (utensils icon),
  and **Cook** (fire icon), each rendered with Font Awesome icons.
- **Click a Prep event** to open the full meal details: description,
  photo, ingredients, recipe steps, and all three dates.
- **Future Meals list** — every meal is fully editable inline
  (Mavo `mv-autoedit`): title, description, photo, ingredients, steps,
  and shop/prep/cook dates as date inputs.
- **Per-meal shopping checklist** with checkboxes.
- **Past Meals** derived automatically: meals whose shopping and prep
  dates have passed drop into the Past Meals card — no manual archiving.
- **GitHub as a database**: Mavo reads/writes
  `mealplanCalendar-bupcs3.json` in this repository and autosaves
  after ~3 seconds of inactivity.
- Responsive two-column card grid (single column under 768px).

## Stack

| Layer      | Technology                                      |
| ---------- | ----------------------------------------------- |
| App shell  | Single static `index.html`, plain CSS + JS     |
| Data/edit  | [Mavo](https://get.mavo.io) (GitHub storage, TinyMCE plugin, autosave) |
| Calendar   | FullCalendar 5.10.2 (CDN)                       |
| Icons      | Font Awesome 6.1.1 (CDN)                        |
| Storage    | `mealplanCalendar-bupcs3.json` in this repo    |

## Quickstart

This is a static page — serve it (or open it) and go:

```sh
# from this repository
python3 -m http.server 8000
# open http://localhost:8000/
```

Any static file server works. The page loads Mavo, FullCalendar, and
Font Awesome from CDNs, so it needs internet access on first load.

To **edit data**: open the page, log in to GitHub when Mavo prompts
(the app is configured with `mv-bar="no-login"` so the editing UI is
always visible), click any value to edit it (autoedit mode), and Mavo
commits changes to `mealplanCalendar-bupcs3.json` in this repository
after ~3 seconds of inactivity.

> Note: Mavo's GitHub storage requires a logged-in GitHub session with
> write access to this repo. Read-only visitors still see the calendar
> and meal cards.

## Repository structure

```
index.html                        # the entire app: markup, styles, logic
mealplanCalendar-bupcs3.json      # Mavo data store (meals, dates, lists)
images/                           # screenshot / image assets
CHANGELOG.md                      # commit-by-commit history (newest first)
AGENTS.md                         # guide for coding agents working here
architectural-diary/              # how the app evolved and why
prompt.md                         # one-shot prompt to recreate this app
```

## Data model

`mealplanCalendar-bupcs3.json` (also mirrored in the inline `#initial`
seed inside `index.html`):

```
pageHeader, futureMealsHeader, pastMealsHeader   # UI label strings
meals[]:
  title, description, photo                      # identity
  ingredients[]   { ingredient }                 # recipe ingredients
  steps[]         { step }                       # ordered recipe steps
  shopDate, prepDate, cookDate                   # YYYY-MM-DD schedule
  shoppingList[]  { ingredient }                 # checkbox checklist
```

The calendar derives three FullCalendar events per meal from
`shopDate` / `prepDate` / `cookDate`; Past Meals derives from
`date(shopDate) <= $today && date(prepDate) <= $today`.

## Configuration

The app reads its configuration from attributes on the Mavo root
element in `index.html` (no environment variables):

- `mv-app` — app/data-file name (`mealplanCalendar-bupcs3`)
- `mv-storage` — GitHub repository URL used as the data backend
- `mv-plugins` — Mavo plugins (`tinymce`)
- `autosave` — seconds of inactivity before committing (3)
- `mv-upload-path` — upload folder for images (`pictures`)
- `<base href>` — canonical site URL (mealplan-calendar.com)

## License

Personal project; no license file is present in the repository.
