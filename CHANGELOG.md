# Changelog

All notable changes to this project are documented here, newest first.

> **History-rewrite note (2026-09-08):** The original commit messages on
> `main` were generic ("init", "wip", "changes", repeated "Updated
> mealplanCalendar-bupcs3.json" from Mavo/GitHub auto-saves). On
> 2026-09-08 every commit message was improved via a messages-only
> `git filter-branch` rewrite; file contents, trees, authorship, and
> dates are byte-for-byte unchanged, but all commit hashes changed.
> The entries below use the post-rewrite hashes and messages.

## 2024-08-29

- **06b20e6 — chore: add mealplan screenshot image to images directory**
  - Adds `images/Screenshot 2024-06-26 at 2.45.36 PM.png` (~1.4 MB).
  - Design-reference asset only; no application code touched.

## 2024-08-27

- **3d2aa9a — chore: empty web-UI save of mealplanCalendar-bupcs3.json**
  - No-op commit: tree identical to parent; GitHub web-editor save produced no net change.

- **5d54104 — chore: empty web-UI save of mealplanCalendar-bupcs3.json**
  - No-op commit: tree identical to parent; kept only to preserve history.

- **21f3243 — chore: bump salmon meal dates to 2024-08-27 in calendar data**
  - Shifts salmon `shopDate`/`prepDate`/`cookDate` from 2023-08-29/30 to 2024-08-27.

- **d53f3d2 — refactor: wrap meal JSON list items in objects and add futureMealsHeader**
  - Converts `ingredients`, `steps`, and `shoppingList` in the data file from plain string arrays to object arrays (`{"ingredient": ...}` / `{"step": ...}`).
  - Adds the top-level `futureMealsHeader` field; re-indents the file with tabs.

- **52f437e — chore: bump lasagna meal dates to 2024-08-03..05 in calendar data**
  - Moves lasagna shop/prep/cook dates from 2023-05-03/04/05 to 2024-08-03/04/05.

- **afca642 — fix: correct past-meals date filter and clean up meal JSON schema**
  - Moves the past-meal `mv-if` onto an inner span and wraps dates in `date()` so comparisons against `$today` are true date comparisons, not string compares.
  - Past Meals now shows "Prep on `<prepDate>`" instead of "Cooked on `<cookDate>`".
  - Replaces a malformed Mavo-generated nested JSON structure with a clean flat meal schema.

- **10ce3a4 — chore: shift prep and cook dates to 2024-08-25 in state file**
  - Aligns the current meal's prep/cook dates with its shopping date.

- **5d570b2 — chore: move cookDate to 2024-08-26 in mealplanCalendar-bupcs3.json**
  - Single-field data edit from the app's auto-save flow.

- **69901ea — feat: record cookDate on current meal in mealplanCalendar-bupcs3.json**
  - First appearance of the `cookDate` field in persisted state.

- **76a57f2 — chore: remove test future meals from mealplanCalendar-bupcs3.json**
  - Clears throwaway placeholder entries ("asdf", "sadf") entered while testing the future-meals input flow.

- **780f72d — chore: no-op save of mealplanCalendar-bupcs3.json**
  - Empty commit produced by the Mavo auto-save flow; content identical to previous revision.

- **fbe717c — feat: persist grouped meals and future meals in state file**
  - Restructures `meals` into grouped arrays (current vs. future) with a `futureMealsHeader`.

- **6bc0139 — feat: add future meals, cook dates, and dynamic calendar events**
  - The biggest functional change: replaces the static "Current Meal" card with an editable "Future Meals" list and adds `cookDate`.
  - Calendar initialization moves from `DOMContentLoaded` to `mv-load`, and events are generated dynamically from Mavo live data (`Mavo.all[0]`) as Shop/Prep/Cook entries per meal, replacing hardcoded demo events.
  - Past Meals becomes derived state via `mv-if` date comparison; the persisted JSON migrates to a single `meals` collection.

## 2024-08-26

- **bdd7fa4 — chore(data): reformat mealplanCalendar-bupcs3.json with tab indentation**
  - 2-space to tab reformat plus an experimental `name` field on a past meal; no functional change.

- **3ade34c — feat(ui): make shopping and prep dates editable date inputs**
  - Shop/prep dates become `<input type="date">` bound to Mavo properties.
  - Sample `pastMeals` data upgraded from `{name, date}` stubs to full meal objects.

- **f590e46 — feat(ui): place current and past meals in a responsive card grid**
  - New `.meal-grid`/`.meal-card` layout: two columns at ≥768px, one below.
  - Sets `mv-accepts="currentMeal"` on the past-meals list so meals can be moved into it.

- **1833bfe — chore: no-op save of mealplanCalendar-bupcs3.json**
  - Auto-generated empty commit from the Mavo GitHub storage backend.

- **f2575b7 — feat(ui): make current meal an editable Mavo list**
  - Converts the Current Meal card to `mv-list` markup and enables `mv-autoedit` (click-to-edit).
  - Renames `currentMealIngredients`/`currentMealSteps` to `mealIngredients`/`mealSteps`; removes the dead Meal Details section.

- **ca09047 — feat(data): add mealplanCalendar-bupcs3.json with seed meal data**
  - Creates the Mavo storage file backing the app: header labels, a full salmon meal, and three past meals.

- **175fb38 — feat: scaffold Mavo-backed meal plan calendar app**
  - Initial `index.html`: Mavo app (GitHub storage, TinyMCE plugin, 3s autosave) with a FullCalendar month view, Current Meal / Meal Details / Past Meals sections, custom CSS, seeded sample data, and Font Awesome-decorated shop/prep events with click-to-view meal details.
