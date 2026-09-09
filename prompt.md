# prompt.md — One-shot recreation prompt for MealPlan Calendar

Give this entire document to a capable coding agent to recreate the
application from scratch. It encodes every goal, stack, design, data,
and behavior decision made in the original repository.

---

## Goal

Build **MealPlan Calendar**: a single-file, zero-build web app for
planning meals around three dates — shopping, prep, and cooking. The
month calendar shows Shop/Prep/Cook events per meal with icons; meal
cards hold the full recipe (description, photo, ingredients, steps)
plus per-meal date inputs and a checkbox shopping list; meals whose
shop+prep dates have passed automatically appear in a Past Meals
card; and all data persists by Mavo to a JSON file in a GitHub
repository. The entire application is one `index.html` plus one
`mealplanCalendar-bupcs3.json` data file. No server, no build step,
no framework beyond Mavo and FullCalendar loaded from CDNs.

## Stack (exact)

- **Mavo** — `https://get.mavo.io/mavo.min.css` and
  `https://get.mavo.io/mavo.min.js`; app root attributes:
  `mv-app="mealplanCalendar-bupcs3"`, `mv-plugins="tinymce"`,
  `mv-storage="https://github.com/hsingh23/mealplan-calendar"`,
  `mv-bar="no-login"`, `autosave="3"`, `mv-init="#initial"`,
  `mv-upload-path="pictures"`, `class="mv-autoedit"`.
- **FullCalendar 5.10.2** —
  `https://cdn.jsdelivr.net/npm/fullcalendar@5.10.2/main.min.css`
  and `.../main.min.js`; view `dayGridMonth`.
- **Font Awesome 6.1.1** —
  `https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/all.min.css`.
- **`<base href="https://mealplan-calendar.com/" />`** in `<head>`.
- Plain CSS and vanilla JS; nothing else. `<title property="pageTitle">`
  is a Mavo property.

## Phased build order

1. **Skeleton + styles.** Full head (CDNs, base). CSS: `body`
   (Arial, `#f0f5f9` background, no margin), `.container`
   (max-width 1200px, centered, 20px padding), `h1` (`#1e3a8a`,
   centered), `#calendar` (white card: 20px padding, 10px radius,
   `box-shadow: 0 4px 6px rgba(0,0,0,.1)`, margin-bottom 20px),
   `.meal-card` (same card treatment), `.meal-card h2` `#1e3a8a`,
   `.meal-photo` (max-width 100%, min-width 100px, 5px radius),
   `.ingredients-list`/`.recipe-steps` (no list style, 10px item
   spacing), `.buy-checklist` (no list style, checkboxes with 10px
   right margin), `.meal-grid` (CSS grid, 1 column, 20px gap;
   `@media (min-width: 768px)` → 2 columns), `.current-meal`
   (highlight: `#e6f7ff` background, 2px `#1e90ff` border; its h2
   `#1e90ff`), `.fc-event-title` (0.8em, `white-space: normal`),
   `.fc-event-time` (hidden), `.fc-event` (pointer cursor),
   `.event-icon` (5px right margin), `.past-meal-item` (1px bottom
   border `#e0e0e0`, 10px vertical padding).
2. **Mavo app markup.** The Mavo root div contains `.container` with
   `<h1 property="pageHeader">`, `<div id="calendar">`, then
   `.meal-grid` holding two `.meal-card`s:
   - **Future Meals card:** `<h2 property="futureMealsHeader">`;
     `#future-meals-list` is an `mv-list` whose `mv-list-item="meals"`
     template renders `<h3 property="title">`, description span,
     `<img property="photo" class="meal-photo">`, an ingredients
     `mv-list` of `mv-list-item="ingredients"` with
     `property="ingredient"`, an ordered steps `mv-list`
     (`mv-list-item="steps"`, `property="step"`), three
     `<input type="date">` bound to `shopDate`, `prepDate`,
     `cookDate` (each under an `<h4>` label: Shopping Date,
     Preparation Date, Cooking Date), and a Shopping List
     `.buy-checklist` `mv-list` (`mv-list-item="shoppingList"`)
     of `<input type="checkbox" id="[ingredient]">` +
     `<label for="[ingredient]" property="ingredient">`.
   - **Past Meals card:** `<h2 property="pastMealsHeader">`;
     `#past-meals-list` is an `mv-list` whose
     `mv-list-item="meals"` renders
     `<span mv-if="(date(shopDate) <= $today) && (date(prepDate) <= $today)">`
     containing `<strong property="title">` and
     "— Prep on `<span property="prepDate">`".
3. **Seed data.** `<script type="application/json" id="initial">`
   holding `pageHeader`/`futureMealsHeader`/`pastMealsHeader` labels
   and a `meals` array with two fully-fleshed example meals (e.g.
   Grilled Lemon Herb Salmon and Vegetable Lasagna: description,
   absolute photo URLs under `images/`, 7–14 ingredients, 6 recipe
   steps, three dates each, shopping list mirroring ingredients).
4. **Calendar logic.** A `<script>` block that adds a listener for
   Mavo's **`mv-load`** event (NOT `DOMContentLoaded`), creates a
   `FullCalendar.Calendar` on `#calendar`, and:
   - `events` is a **function** `(fetchInfo, successCallback,
     failureCallback)` reading
     `Mavo.all[0]?.root.liveData.data.meals` and, per meal, pushing
     up to three events: title prefixes `"Shop: "`, `"Prep: "`,
     `"Cook: "` + meal title; `classNames` `shop-event` / `prep-event`
     / `cook-event`; `extendedProps` with `icon`
     (`fas fa-shopping-cart` / `fas fa-utensils` / `fas fa-fire`),
     `mealName`, `type`; the **prep** event additionally carries
     `description`, `photo`, `ingredients`, `recipe` (from steps),
     `shopDate`, `cookDate`. Then `successCallback(events)`.
   - `eventContent(arg)` builds a DOM `<i>` with the icon class +
     a `<span>` reading `Type` capitalized + `": "` + mealName,
     returned as `{ domNodes: [icon, title] }`.
   - `eventClick(info)` calls `displayMealDetails(info.event)` only
     when `extendedProps.type === "prep"`.
   - `displayMealDetails(event)` fills an element `#meal-info` with
     a template string: h3 name, description, photo img, ingredients
     ul, recipe ol, Shopping/Preparation/Cooking date paragraphs
     (prep from `event.start.toISOString().split("T")[0]`), and a
     checkbox shopping list whose ids derive from the ingredient
     text via `ingredient.replace(/\s+/g, "-")`. (In the original
     this `#meal-info` container was later removed — recreate it if
     you want click-through details to work.)
   - Finally `calendar.render()`.

## UI/UX and design decisions (all)

- Calendar on top (full width), meal cards below in a responsive
  two-column grid (single column < 768px).
- Events show icon + "Shop:/Prep:/Cook: Meal name"; event time
  hidden; whole event is a pointer; only Prep events are clickable.
- Autoedit (click any value to edit it in place); TinyMCE available
  for rich text; editing UI visible without login (`mv-bar="no-login"`).
- Autosave 3 seconds after activity; writes to the GitHub repo; new
  meals can be added/removed inline via Mavo list affordances;
  photos upload to `pictures/`.
- Past meals require no manual archiving — derived from dates only.
- Shopping checklist checkboxes are ephemeral (not persisted).
- Look: light blue-gray page, white cards with 10px radius and soft
  shadow, dark blue `#1e3a8a` headings, `#1e90ff` blue accent for
  highlighted meal state, Arial.

## Data model

`mealplanCalendar-bupcs3.json` (and the `#initial` seed):

```json
{
  "pageHeader": "MealPlan Calendar",
  "futureMealsHeader": "Future Meals",
  "pastMealsHeader": "Past Meals",
  "meals": [
    {
      "title": "…", "description": "…", "photo": "https://…",
      "ingredients": [ { "ingredient": "…" } ],
      "steps": [ { "step": "…" } ],
      "shopDate": "YYYY-MM-DD",
      "prepDate": "YYYY-MM-DD",
      "cookDate": "YYYY-MM-DD",
      "shoppingList": [ { "ingredient": "…" } ]
    }
  ]
}
```

Tab indentation, no trailing newline (match existing file style).

## APIs, libraries, and hooks by name

- `Mavo.all[0]` — the page's Mavo instance (positional; single app only).
- `Mavo.all[0].root.liveData.data.meals` — live meals collection.
- Mavo events: `mv-load` (data ready — the only calendar trigger).
- Mavo attributes used: `mv-app`, `mv-plugins`, `mv-storage`,
  `mv-bar`, `autosave`, `mv-init`, `mv-upload-path`, `mv-autoedit`,
  `mv-list`, `mv-list-item`, `mv-if`, `property`; Mavo expressions:
  `date(...) <= $today`.
- FullCalendar v5 API: `FullCalendar.Calendar`, options
  `initialView`, `events` (function form), `eventContent`,
  `eventClick`; `calendar.render()`; event fields `title`, `start`,
  `classNames`, `extendedProps`.
- Font Awesome classes: `fas fa-shopping-cart`, `fas fa-utensils`,
  `fas fa-fire`.

## Acceptance criteria

1. Opening `index.html` via any static server renders the page with
   internet access; no console errors.
2. The calendar shows a month grid; every meal yields Shop, Prep,
   and Cook events on its three dates, each with the correct icon
   and "Type: Meal name" label; event times are not shown.
3. The Future Meals card lists all meals with photo, description,
   ingredients, steps, three date inputs, and a checkbox shopping
   list; values are click-editable in place (autoedit).
4. Meals with `date(shopDate) <= $today && date(prepDate) <= $today`
   render in Past Meals as "Title — Prep on date"; future meals
   do not.
5. With a GitHub login that can write the storage repo, edits
   persist to `mealplanCalendar-bupcs3.json` as commits ~3s after
   activity stop; the calendar reflects changed dates after reload.
6. Layout: two meal-card columns ≥768px wide, one column below.
7. `python3 -m json.tool mealplanCalendar-bupcs3.json` succeeds.
8. No build step, no package.json, no server code — everything is
   `index.html` + the JSON data file (+ image assets).
