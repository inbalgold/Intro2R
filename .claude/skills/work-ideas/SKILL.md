---
name: work-ideas
description: >-
  Capture a work idea into the user's "Work Ideas" Notion database (an inbox of
  things they might do later). Use this whenever the user is dumping, jotting,
  parking, or stashing an idea, thought, TODO, or "note to self" about their
  work — teaching, research, tooling, writing, admin, anything. Trigger on
  phrasings like "idea:", "I have an idea", "dump this", "jot this down", "note
  this for later", "add to my ideas", "park this", "brain dump", or "remind me I
  wanted to…", even when they don't say the word "Notion" or "skill". Also use it
  when they ask what's in their ideas list or want to review/tag/update captured
  ideas. Err on the side of using it: capturing an idea should feel frictionless,
  and a missed capture is worse than an extra row.
---

# Work Ideas

This skill is a low-friction inbox. The user is mid-work, a thought strikes them,
and they want it *out of their head and safely stored* so they can get back to
what they were doing. The whole value is speed and trust: they should be able to
throw a half-formed sentence at you and trust that it landed, correctly, without
a back-and-forth. Interrogating them defeats the purpose — every question you ask
is friction that makes them less likely to dump the next idea.

So the default posture is **capture first, ask almost never**.

## Where ideas live

All ideas go into one Notion database, "Work Ideas":

- **Data source ID**: `84cbcb10-9d4e-453d-bc0e-a06ee7e56248`
- **Database URL**: https://app.notion.com/p/9ffd50ecbaaa40bf89704344650dfa11

Create each idea with the `notion-create-pages` tool, using
`{"type": "data_source_id", "data_source_id": "84cbcb10-9d4e-453d-bc0e-a06ee7e56248"}`
as the parent.

If that data source ever fails (not found, wrong workspace), don't lose the
idea — search Notion for a database named "Work Ideas" and use it. If there
genuinely isn't one, recreate it with this schema before capturing:
`Name` (title), `Tags` (multi-select), `Status` (select: Inbox / Exploring /
Doing / Done), `Captured` (created_time). Then tell the user you had to recreate
it so they know why the old ideas aren't there.

## Capturing an idea

For each idea the user gives you, create one page:

- **Name (title)** — a short, skimmable summary of the idea, roughly 3–8 words.
  This is what they'll scan later, so make it a real handle, not a truncation.
  E.g. from "I keep thinking we should give students a cheat sheet of dplyr verbs
  before the join lecture" → title `dplyr verb cheat sheet before joins`.
- **Page body** — the idea in the user's own words. Preserve their phrasing;
  don't over-polish or editorialize. If they said two sentences, store two
  sentences. Their future self wants the original thought, not your paraphrase.
- **Tags** — pick 0–2 from the existing set: `teaching`, `research`, `tooling`,
  `admin`, `writing`, `misc`. Only tag when a category clearly fits — a wrong tag
  is worse than none. If nothing fits and the idea clearly belongs to a recurring
  new theme, you may introduce one new tag, but prefer the existing set; tag
  sprawl makes the list less useful, not more.
- **Status** — always `Inbox` for a fresh capture. That's the "not yet triaged"
  state; the user promotes ideas to Exploring/Doing/Done themselves later.

Leave `Captured` alone — Notion sets it automatically.

### Multiple ideas at once

People brain-dump in bursts. If the message contains several distinct ideas
("oh and also…", a bulleted list, clearly separate thoughts), create one row per
idea rather than cramming them into a single page — separately titled and tagged,
they're each actionable later. But don't over-split a single connected thought
just because it spans a few sentences. Use judgment: one idea, one row.

## Confirming back

After capturing, give a **one-line** confirmation so they know it landed, then
get out of the way. Show the title(s) and, ideally, a link. For example:

> Captured **dplyr verb cheat sheet before joins** (`teaching`) → [Work Ideas](https://app.notion.com/p/9ffd50ecbaaa40bf89704344650dfa11)

For several at once, a short bulleted list of the titles is fine. Don't write a
summary paragraph or suggest next steps unless the user asks — this is an inbox,
not a planning session.

## When to actually ask

Only ask a question when capturing would otherwise store something wrong or
useless — and even then, keep it to a single question. Legitimate cases:

- The "idea" is genuinely unintelligible (a stray fragment with no discernible
  meaning). Better to ask than to store noise.
- It's ambiguous whether they even want it captured vs. they're asking you to
  *act* on it now (e.g. "idea: rewrite the intro" — do they want it parked, or
  done right now?). A quick "want me to just park that, or do it now?" is worth it.

Everything else — a vague tag, a rough title, an incomplete thought — just
capture with your best guess. They can refine in Notion, and the point is that
the thought is now safe.

## Reviewing and updating ideas

If the user wants to see what's captured ("what's in my ideas?", "show me my
research ideas"), query the data source (optionally filter by tag or status) and
show a compact list: title, tags, status. Link to the database for the full view.

If they want to change something — retag, mark an idea as Doing/Done, edit the
text — find the matching page and update it. Match on their description of the
idea; if two ideas plausibly match, show both and let them pick.
