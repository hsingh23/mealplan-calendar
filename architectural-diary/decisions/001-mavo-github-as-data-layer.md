# Decision 001 — Mavo + GitHub as the data layer

- **Date:** 2024-08-26 (commits 175fb38, ca09047, f2575b7)
- **Status:** accepted, active

## Context

The app needs structured, user-editable data (meals with nested
ingredients, steps, dates, checklists) but has no server and no
desire for a build pipeline or hosted database. The author wanted
the page itself to be the editor.

## Decision

Use [Mavo](https://mavo.io) with its GitHub storage backend:

```html
<div mv-app="mealplanCalendar-bupcs3"
     mv-plugins="tinymce"
     mv-storage="https://github.com/hsingh23/mealplan-calendar"
     mv-bar="no-login" autosave="3"
     mv-init="#initial" mv-upload-path="pictures"
     class="mv-autoedit">
```

- Data persists to `mealplanCalendar-bupcs3.json` in this repository.
- `autosave="3"` commits ~3 seconds after edits stop.
- `mv-init="#initial"` seeds first-run data from an inline JSON
  `<script>` block in `index.html`.
- Uploaded images go to a `pictures/` path.
- The TinyMCE plugin is loaded for rich text properties.

## Consequences

**Positive:** zero infrastructure; the repo *is* the database; full
edit history for free (git); inline editing with no admin UI to build.

**Negative:** every data change is a git commit (history is noisy —
see decision 005); writers need GitHub login with repo access; the
app cannot work offline; `Mavo.all[0]` coupling between the page's
script and the Mavo instance is positional rather than by name.
