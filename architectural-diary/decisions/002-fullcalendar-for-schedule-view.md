# Decision 002 — FullCalendar for the schedule view

- **Date:** 2024-08-26 → 2024-08-27 (commits 175fb38, 6bc0139)
- **Status:** accepted, active

## Context

The core UX is "when do I shop / prep / cook." A month grid is the
natural surface, and it needed custom-rendered events (icons +
labels) and click-through to meal details.

## Decision

Use FullCalendar 5.10.2 (CDN) in `dayGridMonth` view with:

- **A dynamic event-source function** (not a static array) that maps
  each Mavo meal into up to three events — `Shop:`, `Prep:`, `Cook:`
  — carrying Font Awesome icon classes and the full meal payload in
  `extendedProps`.
- **Custom `eventContent`** rendering an `<i>` icon plus a
  "Type: Meal name" label instead of the default title chip; event
  times hidden via CSS.
- **`eventClick`** limited to `type === "prep"` events, calling
  `displayMealDetails()`.
- **Initialization on Mavo's `mv-load` event** instead of
  `DOMContentLoaded`, so live Mavo data (not just the seed) drives
  the calendar.

## Consequences

**Positive:** professional calendar UX for free; the Shop/Prep/Cook
trio makes a meal's timeline legible at a glance.

**Negative:** the calendar only appears if Mavo loads (online-only);
the event payload is duplicated into `extendedProps`; the click
handler targets `#meal-info`, which no longer exists in the markup —
a known dead end left in place (see AGENTS.md gotchas).
