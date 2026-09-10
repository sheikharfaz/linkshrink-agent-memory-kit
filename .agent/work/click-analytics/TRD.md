# TRD: click-analytics

## Approach
Add `click_count`/`last_clicked_at` columns to the existing `links` table
(confirmed via `query.py file app/storage.py` — 4 functions, no columns
beyond code/url/created_at, so this is additive, not a migration of
existing data since there is none in production yet). Add
`storage.record_click()`, call it from the redirect handler, and add a
`GET /links/{code}/stats` route reusing the existing `get_link()` shape.

## Alternatives considered
- A separate `clicks` table (one row per click): rejected for this
  session — the PRD's non-goals explicitly excludes per-click event
  logging; a running counter is enough and is a smaller change.

## Design
- `app/storage.py`: `init_db()` schema gets two new columns
  (`app/storage.py:17`, confirmed via `query.py def init_db`); new
  `record_click(code, clicked_at, db_path=None)`.
- `app/main.py`: new `StatsResponse` model; new `GET /links/{code}/stats`
  (`app/main.py:31`, confirmed via `query.py file app/main.py` — existing
  routes are `POST /links` and `GET /{code}`, so `/links/{code}/stats`
  doesn't collide with the catch-all `/{code}` since it has more path
  segments); `redirect()` now calls `storage.record_click()`.

## Testing strategy
- "fresh code → 0 clicks" → `test_stats_start_at_zero_clicks`
- "redirect increments count" → `test_redirect_increments_click_count`
- "unknown code → 404" → `test_stats_for_unknown_code_is_404`

## Rollback plan
Revert the commit; the new columns have `DEFAULT 0`/nullable so no data
migration is needed either direction.

## Risks & blast radius
`query.py impact app/storage.py` → 2 files call into it: `app/main.py` and
`app/storage.py` itself (intra-module calls between its own functions, not
a real dependency). Low risk: no other module reaches into storage
directly.

## Phases
- [x] Phase 1: schema + record_click + stats route + tests. Verify:
  `pytest tests/ -v`. Rollback: revert commit.
