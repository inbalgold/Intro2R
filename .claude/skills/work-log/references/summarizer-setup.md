# Status-update summarizer setup

The `work-log` skill writes into the Notion **Work Log** database. This doc wires
up the scheduled task that summarizes it before the team meeting.

## How they connect

- `work-log` **writes** dated rows into the Work Log database as you dump things.
- The scheduled summarizer **reads** the trailing window (14 days) from that
  same database and produces a meeting-ready summary.

The only shared contract is the database and its `Captured` timestamp.

- **Database:** `Work Log` (`03627364862c4441baaecc0c5239b262`)
- **Data source:** `collection://64032fd2-f607-4045-b1ed-38defd2c1746`

## If you already have a "Weekly status update" scheduled task

Point it at this database by replacing its prompt with the one below. That task
was firing with no data source — this gives it one. (Rename to "bi-weekly" and
adjust the cadence if the meeting is every two weeks.)

## Summarizer prompt

```
Summarize my work activity for the upcoming team meeting.

Read my Notion "Work Log" database (data source
collection://64032fd2-f607-4045-b1ed-38defd2c1746). Query all entries whose
Captured timestamp is within the last 14 days:

  SELECT "Name", "Category", "Details", "Meeting", "Captured"
  FROM "collection://64032fd2-f607-4045-b1ed-38defd2c1746"
  WHERE datetime("Captured") >= datetime('now', '-14 days')
  ORDER BY "Captured" DESC

Then produce a concise, skimmable summary I can paste into the meeting notes:
- Lead with a "⭐ Raise at meeting" section listing every entry whose Meeting
  checkbox is true (Meeting = "__YES__").
- Then one short section per Category with bullet points (Name + key Details).
- End with a 2-3 line "Headline" — the most important things this cycle.
If there are no entries, say so plainly.
```

If the summary should land somewhere durable (a Notion "Meeting Prep" page,
email, Slack), add that as a final step in the prompt.

## Cadence

Cron can't express "every 2 weeks" directly. Options:
- **Aligned to the meeting** — fire the morning before it. The 14-day rolling
  window tolerates a slightly-off cadence.
- **1st & 15th** — `0 6 1,15 * *` (06:00 UTC; adjust to your timezone).
- **Weekly** — the existing task's cadence; a 14-day window just overlaps
  harmlessly week to week.

## Notes

- Scheduled sessions run in fresh containers, so the Notion connector must be
  authorized for them.
- Keep the 14-day window even if runs drift by a day or two — overlap beats a
  gap that drops entries.
