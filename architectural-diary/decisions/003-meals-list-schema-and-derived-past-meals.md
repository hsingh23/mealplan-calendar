# Decision 003 — From single currentMeal to a meals list with derived past meals

- **Date:** 2024-08-27 (commits 6bc0139, afca642; precursors f590e46, 3ade34c)
- **Status:** accepted, active — this is the app's current model

## Context

Day 1 modeled a single "Current Meal" object plus a manually
maintained `pastMeals` array of `{name, date}` stubs. That cannot
represent a plan of several upcoming meals, and archiving past meals
was manual busywork.

## Decision

Collapse to **one `meals[]` collection** where each meal carries:

```
title, description, photo
ingredients[] {ingredient}
steps[]       {step}
shopDate, prepDate, cookDate      ← third date added here
shoppingList[] {ingredient}
```

- The Future Meals card is an `mv-list` over `meals` — add/remove
  meals inline.
- **Past Meals is derived, not stored**: a list item renders only
  when `date(shopDate) <= $today && date(prepDate) <= $today`.
- The calendar's event-source reads the same collection, so UI
  cards and calendar can never disagree.

`afca642` hardened the derivation: raw string comparison of dates
was replaced by `date()`-wrapped comparison against `$today`, and
the visible label became "Prep on `<prepDate>`".

## Consequences

**Positive:** one source of truth; adding a meal instantly yields
calendar events, checklist, and eventual past-meal entry; no
archiving step.

**Negative:** the transition produced intermediate malformed data
(Mavo saved nested arrays during the schema swap, cleaned up again
in afca642); "past" is defined by shop+prep dates only — `cookDate`
does not participate in the past-meal condition, which may or may
not be intended.
