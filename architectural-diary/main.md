# Architectural Diary — MealPlan Calendar

A narrative history of how this app was built and why it looks the
way it does. Written 2026-09-08 from the git history (commit messages
were rewritten that day; entries cite post-rewrite hashes).

## The project in one paragraph

MealPlan Calendar is a single-file web app for planning meals around
three dates — shopping, prep, and cooking. It renders those dates as
icon-decorated events on a FullCalendar month grid, keeps the full
recipe (ingredients, steps, photo) attached to each meal in editable
cards, derives a "Past Meals" view automatically from the dates, and
persists everything to a JSON file in this GitHub repository via
Mavo. There is no backend, no build step, and no framework — the
entire application is `index.html` plus a data file.

## Timeline

### Day 1 — 2024-08-26: scaffold through first editable cards

The initial commit (`175fb38`) already contained the full stack
decision: Mavo for data + editing, FullCalendar for the calendar,
Font Awesome for icons, all from CDNs. The first version had a
**Current Meal** card, a **Meal Details** panel, a **Past Meals**
list, and a calendar with **hardcoded demo events** (Spaghetti
Bolognese, Chicken Stir Fry) initialized on `DOMContentLoaded`.

The Mavo storage file (`ca09047`) followed as a separate commit, and
`f2575b7` immediately reworked the bindings: the current meal became
a real `mv-list` item, property names were simplified
(`currentMealIngredients` → `mealIngredients`), `mv-autoedit` was
enabled (click-to-edit without an edit/save toggle), and the dead
Meal Details card was dropped.

`f590e46` introduced the visual language that survives today:
`.meal-grid` with `.meal-card` panels, two columns at ≥768px,
single column on phones. `3ade34c` turned shop/prep dates into
`<input type="date">` fields — dates became user data, not display
text.

### Day 2 — 2024-08-27: the big restructure

`6bc0139` is the pivotal commit. It replaced the single
"Current Meal" concept with a **Future Meals list** (`meals[]`),
added a third date (`cookDate`), switched calendar initialization
from `DOMContentLoaded` to Mavo's **`mv-load`** event, and replaced
the hardcoded events with a dynamic event-source function that reads
`Mavo.all[0].root.liveData.data.meals` and emits Shop/Prep/Cook
events per meal. Past Meals stopped being a separately-maintained
collection and became **derived state** via `mv-if` date comparison.

The rest of day 2 is the Mavo auto-save loop writing to the data
file: test entries added and cleared (`fbe717c`, `76a57f2`), date
tweaks (`69901ea`, `5d570b2`, `10ce3a4`), schema re-shapes
(`d53f3d2`), and several empty no-op saves — artifacts of the
GitHub-storage backend committing with no net diff (`780f72d`,
`5d54104`, `3d2aa9a`).

`afca642` fixed a real bug from the restructure: the past-meal
condition compared date strings directly; wrapping the fields in
`date()` made the `$today` comparison correct, and the label changed
to "Prep on `<prepDate>`".

### 2024-08-29 and after

`06b20e6` added a screenshot to `images/` — the last content commit.
The app has been stable since.

## How to read the decisions

Individual decision records live in [`decisions/`](./decisions/):

- [001 — Mavo + GitHub as the data layer](./decisions/001-mavo-github-as-data-layer.md)
- [002 — FullCalendar for the schedule view](./decisions/002-fullcalendar-for-schedule-view.md)
- [003 — From single currentMeal to a derived meals list](./decisions/003-meals-list-schema-and-derived-past-meals.md)
- [004 — Inline editing and the responsive card grid](./decisions/004-inline-editing-and-card-grid.md)
- [005 — The data file, its schema churn, and auto-save artifacts](./decisions/005-data-file-schema-and-autosave.md)

## Known scars and dead ends

- `displayMealDetails()` still writes to `#meal-info`, an element
  deleted with the Meal Details card — Prep-event clicks are wired
  but silently render nowhere. See AGENTS.md gotchas.
- The `#initial` inline JSON seed and the persisted data file
  drifted apart during day 2 (the seed still describes the old
  currentMeal/pastMeals shape at one point) before being reconciled.
- Four empty commits from Mavo's GitHub storage remain in history as
  no-ops; they document how the data flow really worked, so they were
  kept.
