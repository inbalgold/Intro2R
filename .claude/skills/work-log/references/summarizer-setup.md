# Bi-weekly summarizer setup

The `work-log` skill feeds a Notion **Work Log** database. This doc sets up the
scheduled task that summarizes it before the team meeting.

## How they connect

- `work-log` **writes** dated rows into the Work Log database as you dump things.
- The scheduled summarizer **reads** the trailing 14 days from that same
  database and produces a meeting-ready summary.

The only shared contract is the database and its `Date` property — nothing else
needs wiring.

## Creating the schedule (Routine / trigger)

Create a recurring trigger that fires a fresh session with the prompt below.
Cron can't express "every 2 weeks" directly; pick one:

- **Aligned to the meeting** — if the meeting is, say, every other Thursday,
  set the trigger to the morning before it. Because the prompt covers a rolling
  14-day window, a slightly-off cadence still captures everything.
- **1st & 15th of the month** — cron `0 6 1,15 * *` (06:00 UTC — adjust to your
  timezone). Simple and predictable, ~bi-weekly.
- **Weekly, self-skipping** — run weekly and let the prompt no-op on off weeks.

## Summarizer prompt

Use this as the trigger's prompt (create-new-session-on-fire, so each run is
clean):

```
Summarize my work activity for the upcoming team meeting.

1. Open the Notion database named "Work Log".
2. Query all entries with Date in the last 14 days.
3. Produce a concise summary, grouped by Category, that I can read aloud or
   paste into the meeting notes:
   - Lead with a "⭐ Raise at meeting" section listing every entry whose
     Meeting checkbox is true.
   - Then one short section per Category with bullet points (Name + any key
     Details).
   - End with a 2-3 line "Headline" — the most important things I did this cycle.
4. Keep it tight and skimmable. If there are no entries, say so plainly.
```

If the summary should land somewhere durable (a Notion "Meeting Prep" page, an
email, Slack), add that as a final step in the prompt.

## Notes

- Scheduled sessions run in fresh containers, so the Notion connector must be
  authorized for them, and `references/config.md` must be committed with the
  real `database_id` for fast lookup.
- Keep the 14-day window even if runs drift by a day or two — a little overlap
  is better than a gap that drops entries.
