---
name: work-log
description: >-
  Quickly capture ("dump") things the user did at work into their Notion Work
  Log database, so a scheduled bi-weekly task can summarize them before the team
  meeting. Use whenever the user wants to log, jot, record, note, or dump a work
  activity, accomplishment, meeting, decision, blocker, or task — e.g. "log
  this", "add to my work log", "dump: shipped the X feature", "worklog", "note
  for the meeting", "רשמי ליומן העבודה", "תוסיפי ליומן", "מה שעשיתי היום". Also
  use when the user asks what's currently in their work log for a period.
---

# Work Log

Capture short, dated work activities into a Notion **Work Log** database so the
bi-weekly "before team meeting" summary can pull everything from the last two
weeks. This skill handles two jobs: **adding** entries and **listing** them.

## The Notion target

All entries live in one Notion database named **`Work Log`**. It is a database
(not a plain page) on purpose: rows carry a real Date property, so the
summarizer can query a clean date range instead of parsing prose.

Its ID is cached in `references/config.md` after first use. **Always read that
file first** — if it holds a real database ID, skip discovery and use it.

### Schema

| Property   | Type        | Notes                                                        |
|------------|-------------|--------------------------------------------------------------|
| `Name`     | Title       | One-line summary of what was done                            |
| `Date`     | Date        | When it happened — default to today                          |
| `Category` | Select      | Project · Meeting · Support · Admin · Learning · Decision · Blocker · Other |
| `Details`  | Rich text   | Optional longer description / context / links                |
| `Meeting`  | Checkbox    | Flag "raise this at the next team meeting"                   |

## Adding an entry (the common case)

1. **Load the config.** Read `references/config.md`.
2. **Resolve the database.**
   - If config has a real `database_id`, use it directly.
   - Otherwise run `notion-search` for `Work Log`. If a database is found,
     record its ID into `references/config.md` (edit the file) so future runs
     skip this step. **Tell the user you cached it and that they should commit
     the change** (`git add .claude/skills/work-log/references/config.md &&
     git commit && git push`) so scheduled sessions inherit it.
   - If none is found, create it (see "First-time setup" below), then cache the ID.
3. **Parse the dump.** From what the user said, derive:
   - `Name` — a tight one-line summary (rewrite rambly input into something a
     teammate would understand at a glance).
   - `Date` — today unless the user says otherwise ("yesterday", a date, etc.).
     Use the current date from the session environment; never hardcode.
   - `Category` — infer from content; when unsure use `Other`.
   - `Details` — anything extra worth keeping (links, numbers, names).
   - `Meeting` — set true if the user signals it matters for the meeting
     ("for the meeting", "raise this", "worth mentioning"), else false.
4. **Create the page** in the database with `notion-create-pages`, setting the
   properties above.
5. **Confirm briefly** — one line echoing what was logged (Name + Date +
   Category), not a wall of text. The user is dumping fast; keep it snappy.

**Batch dumps:** if the user pastes several activities at once (bullets, lines),
create one row per activity — don't cram them into a single entry.

**Ambiguity:** don't interrogate the user. Make sensible inferences for
Category/Meeting and just log it. Only ask if the entry is genuinely unparseable.

## Listing / reviewing entries

When the user asks what's in the log (e.g. "what's in my work log", "show the
last two weeks", "what did I do since the last meeting"):

1. Resolve the database (step 2 above).
2. `notion-query-data-sources` filtered by `Date` within the requested window
   (default: last 14 days).
3. Present grouped by `Category`, newest first. Surface `Meeting`-flagged items
   in their own "Raise at meeting" section at the top.

## First-time setup (create the database)

Only if no `Work Log` database exists:

1. Ask the user which Notion page/workspace should hold it (or use a sensible
   parent from `notion-search` results, e.g. a personal/work home page).
2. Create the database with the schema above via `notion-create-database`
   (title `Work Log`; the Category select with the options listed).
3. Cache the returned `database_id` into `references/config.md`, tell the user,
   and remind them to commit so scheduled sessions can find it.

## Connecting the bi-weekly summarizer

The summarizer is a separate scheduled task. If the user hasn't set it up, point
them to `references/summarizer-setup.md`, which contains a ready-to-use prompt
and cron schedule that query this exact database over the trailing 14 days.

## Notes

- **Notion connector required.** If Notion tools aren't available, tell the user
  the Notion connector is disconnected and needs reconnecting — don't silently
  fall back to writing files.
- Keep the interaction fast and low-friction: the whole point is frictionless
  dumping. Confirm, don't converse.
