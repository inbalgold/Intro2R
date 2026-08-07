# Work Log config

Cached identifiers for the `work-log` skill so it never has to re-discover the
Notion database.

## Notion

- **Database:** `Work Log`
- **database_id:** `03627364862c4441baaecc0c5239b262`
- **data source (for queries):** `collection://64032fd2-f607-4045-b1ed-38defd2c1746`

Create entries with `notion-create-pages` using
`parent = {"data_source_id": "64032fd2-f607-4045-b1ed-38defd2c1746"}`.
Query with `notion-query-data-sources` against
`collection://64032fd2-f607-4045-b1ed-38defd2c1746`.

## Schema (exact select options matter)

`Category` must be exactly one of:
`Project`, `Meeting`, `Support`, `Admin`, `Learning`, `Decision`, `Blocker`, `Other`.
Unknown values are rejected by the API.

`Meeting` checkbox: `"__YES__"` / `"__NO__"`.
`Captured` is an automatic created-time — do not set it.

## If the IDs ever break

If a call reports the database/data source is missing (e.g. it was moved or
recreated), run `notion-search` for `Work Log`, then update the IDs above.
