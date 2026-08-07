# Work Log config

Cached identifiers for the `work-log` skill. The skill reads this first to avoid
re-discovering the Notion database on every run.

## database_id

<!-- Not set yet. On first successful discovery/creation, replace the line below
with the real Notion database ID, then commit & push so scheduled sessions
inherit it. -->

database_id: (unset)

## Notes

- After setting `database_id`, run:
  `git add .claude/skills/work-log/references/config.md && git commit -m "chore(work-log): cache Notion database id" && git push`
