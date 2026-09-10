# TRD: validation-dedup-docs

## Approach
Add `storage.get_link_by_url()` and check it first in `shorten()` before
minting a new code. No schema change needed — `url` is already a plain
column, just not queried by value until now.

## Alternatives considered
- A unique constraint on `url` + catch the IntegrityError: rejected —
  more moving parts than a straight SELECT-then-INSERT for this traffic
  scale, and the check-first version reads more clearly.

## Design
- `app/storage.py`: `get_link_by_url(url, db_path=None)`
  (`query.py file app/storage.py` confirmed the existing `get_link`
  pattern to mirror — same `get_conn`/`SELECT ... WHERE` shape, just
  keyed on `url` instead of `code`).
- `app/main.py`: `shorten()` checks `get_link_by_url` first; only mints +
  inserts a new code on a miss.
- `README.md`: install/run/test/endpoints, and — honestly — what was
  *not* done here (see the companion baseline repo's README for the
  contrast).

## Testing strategy
- "invalid URL → 422" → `test_invalid_url_is_rejected` (verifies existing
  pydantic behavior, not new code — still worth a test so a future change
  to `ShortenRequest` can't silently drop it).
- "same URL twice → same code" → `test_shortening_the_same_url_twice_returns_the_same_code`

## Rollback plan
Revert the commit; no schema/data migration involved either direction.

## Risks & blast radius
`query.py impact app/storage.py` — only `app/main.py` calls into storage
(confirmed already in Session 2's TRD, unchanged since). Low risk.

## Phases
- [x] Phase 1: get_link_by_url + dedup check in shorten() + tests +
  README. Verify: `pytest tests/ -v`. Rollback: revert commit.
