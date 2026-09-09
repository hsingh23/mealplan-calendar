# Decision 004 — Inline editing and the responsive card grid

- **Date:** 2024-08-26 (commits f2575b7, f590e46, 3ade34c)
- **Status:** accepted, active

## Context

The app should feel like editing a document, not filling in forms,
and it must work on a phone while shopping as well as on a desktop
while planning.

## Decision

1. **`mv-autoedit` on the Mavo root** (f2575b7): every property is
   click-to-edit in place; no edit/save mode toggle. Long-form
   fields get the TinyMCE plugin loaded by `mv-plugins="tinymce"`.
2. **Card grid layout** (f590e46): a `.meal-grid` CSS grid holds
   `.meal-card` panels (calendar above, Future Meals and Past Meals
   side by side); `@media (min-width: 768px)` switches from one
   column to two.
3. **Dates as native date inputs** (3ade34c): `shopDate`/`prepDate`
   (later `cookDate`) render as `<input type="date">` bound to Mavo
   properties — mobile keyboards and date pickers for free.

Visual language: white cards with 10px radius and soft shadows on a
light blue-gray page (`#f0f5f9`), dark blue headings (`#1e3a8a`),
blue accent (`#1e90ff`) for highlighted meal state.

## Consequences

**Positive:** no forms to build; responsive without a framework;
date inputs prevent most malformed date entry.

**Negative:** inline editing requires a logged-in GitHub session —
read-only visitors see the same UI but cannot change data;
checkbox state in the shopping checklist is ephemeral DOM state,
not persisted Mavo data (the checkboxes have no `property`, only
derived ids), so checking items off does not survive reloads.
