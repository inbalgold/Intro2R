---
name: work-log
description: >-
  Quickly capture ("dump") things the user did at work into their Notion Work
  Log database, so a scheduled status-update task can summarize them before the
  team meeting. Use whenever the user wants to log, jot, record, note, or dump a
  work activity, accomplishment, meeting, decision, blocker, or task — e.g. "log
  this", "add to my work log", "dump: shipped the X feature", "worklog", "note
  for the meeting", "רשמי ליומן העבודה", "תוסיפי ליומן", "מה שעשיתי היום". Also
  use when the user asks what's currently in their work log for a period.
---

# Work Log

Capture short work activities into a Notion **Work Log** database so the
status-update summarizer can pull everything from the last cycle. Two jobs:
**adding** entries and **listing** them.

Note: this is distinct from the user's **Work Ideas** database (ideas to
explore, with an Inbox→Doing→Done status). Work Log is *things already done*,
for the status update. Don't cross-file between them.

## The Notion target

- **Database:** `Work Log`
- **database_id:** `03627364862c4441baaecc0c5239b262`
- **data source (for queries):** `collection://64032fd2-f607-4045-b1ed-38defd2c1746`

These are cached in `references/config.md`. Use them directly; only fall back to
`notion-search` for `Work Log` if a call reports the IDs are stale/not found
(then update the config file).

### Schema

| Property   | Type          | Notes                                                                 |
|------------|---------------|-----------------------------------------------------------------------|
| `Name`     | Title         | One-line summary of what was done                                     |
| `Captured` | Created time  | Auto-set to now — this is the date the summarizer queries. Read-only. |
| `Category` | Select        | Project · Meeting · Support · Admin · Learning · Decision · Blocker · Other |
| `Details`  | Rich text     | Optional longer description / context / links                         |
| `Meeting`  | Checkbox      | Flag "raise this at the next team meeting"                            |

`Captured` is automatic, so the common case is a one-shot create — the user
dumps, you file it, done.

## Adding an entry (the common case)

1. **Parse the dump.** From what the user said, derive:
   - `Name` — a tight one-line summary. Rewrite rambly input into something a
     teammate would understand at a glance.
   - `Category` — infer from content; must be one of the exact options above.
     When unsure use `Other`. **Never invent a new option** — the create call
     rejects unknown select values.
   - `Details` — anything extra worth keeping (links, numbers, names). Optional.
   - `Meeting` — `"__YES__"` if the user signals it matters for the meeting
     ("for the meeting", "raise this", "worth mentioning"), else `"__NO__"`.
2. **Create the page** with `notion-create-pages`, parent
   `{"data_source_id": "64032fd2-f607-4045-b1ed-38defd2c1746"}`. Don't set
   `Captured` — it's automatic.
3. **Confirm in one line** — echo Name + Category (+ "⭐ meeting" if flagged).
   The user is dumping fast; keep it snappy, don't converse.

**Batch dumps:** if the user pastes several activities at once, create one page
per activity in a single `notion-create-pages` call — don't cram them into one.

**Ambiguity:** don't interrogate. Make sensible inferences for Category/Meeting
and just log it. Only ask if an entry is genuinely unparseable.

## Listing / reviewing entries

When the user asks what's in the log ("what's in my work log", "show the last
two weeks", "what did I do since the last meeting"):

1. Query with `notion-query-data-sources` (SQL mode) against
   `collection://64032fd2-f607-4045-b1ed-38defd2c1746`, filtering on
   `Captured` within the requested window (default: last 14 days). Example:
   ```sql
   SELECT "Name", "Category", "Details", "Meeting", "Captured"
   FROM "collection://64032fd2-f607-4045-b1ed-38defd2c1746"
   WHERE datetime("Captured") >= datetime('now', '-14 days')
   ORDER BY "Captured" DESC
   ```
2. Present grouped by `Category`, newest first. Surface `Meeting`-flagged rows
   (`Meeting = "__YES__"`) in a "⭐ Raise at meeting" section at the top.

## Connecting the summarizer

The status-update summarizer is a separate scheduled task that reads this same
database over the trailing window. Setup prompt + cadence are in
`references/summarizer-setup.md`.

## Notes

- **Notion connector required.** If Notion tools aren't available, tell the user
  the connector is disconnected — don't silently fall back to writing files.
- Keep it frictionless: the whole point is fast dumping. Confirm, don't converse.
