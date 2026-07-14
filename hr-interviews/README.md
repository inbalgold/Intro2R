# HR Interview Manager

A single-file web app for managing candidate interviews. No installation, no
server, no account — just open `index.html` in any modern browser
(double-click it, or drag it into a browser window).

## What it does

- **Candidates** — add candidates by name (position is optional).
- **3 interview steps per candidate** — Screening, Professional, and Final.
  For each step you assign a **date** and an **interviewer**, and tick it off
  when the interview has happened.
- **Interviewer roster** — add everyone in the office who conducts interviews.
- **Cumulative load scale** — a live bar chart showing how many interviews are
  assigned to each person in total. The least-loaded person is flagged
  "next up", and the interviewer dropdown next to every step shows each
  person's current load (e.g. "Dana · load 5") so it's easy to load-balance
  while scheduling. Hover a bar for a per-step breakdown.

## Where the data lives

Data is saved automatically in the browser (localStorage), on the computer
where you use the app. Notes:

- Use the **same browser on the same computer** to see your data again.
- **Export backup** downloads all data as a JSON file — do this regularly.
- **Import** restores from a backup file (also how you move to another
  computer or share state with a colleague).
- Clearing the browser's site data will erase the app's data, so keep backups.
