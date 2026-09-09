# Decision 005 — The data file, its schema churn, and auto-save artifacts

- **Date:** 2024-08-26 → 2024-08-27 (commits bdd7fa4, d53f3d2,
  fbe717c, 76a57f2, and the no-op saves 1833bfe, 780f72d, 5d54104,
  3d2aa9a)
- **Status:** accepted — consequences documented, history kept as-is

## Context

Because Mavo treats the GitHub repo as the database, every UI edit
becomes a commit to `mealplanCalendar-bupcs3.json`, and Mavo
re-serializes the whole file on save.

## What happened

- **Schema churn:** list items flipped between plain string arrays
  and object arrays (`"4 lemons"` ↔ `{"ingredient": "4 lemons"}`)
  as markup bindings changed (d53f3d2), indentation flipped between
  tabs and spaces (bdd7fa4), and one intermediate save produced a
  malformed nested-arrays structure that afca642 later cleaned.
- **No-op commits:** four commits have trees identical to their
  parents — Mavo's storage backend committing with no net diff.
- **Test data:** placeholder meals ("asdf", "sadf") were created
  through the UI and cleared in the next save (76a57f2).

## Decision (standing)

Keep the noisy history rather than squashing it: it is an honest
record of a Mavo-backed app's real workflow. Manual edits to the
data file should preserve tab indentation and the object-array
shape (`{ingredient}` / `{step}`) that the current markup binds to.

## Consequences

**Positive:** complete audit trail of data changes; the file's shape
documents which bindings existed when.

**Negative:** `git log --follow` on the data file is mostly noise;
reformatting the JSON by hand creates large meaningless diffs;
re-basing or cherry-picking across day-2 commits is painful because
consecutive saves re-serialize the file.
