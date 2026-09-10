# TRD: core-shorten-redirect

## Approach
FastAPI app with two routes backed by a thin SQLite storage module. Codes
are generated randomly (7 chars, mixed-case + digits) and retried on
collision rather than derived from the URL, keeping the storage schema and
generation logic fully decoupled.

## Alternatives considered
- Base62-encoding an auto-increment ID: rejected — leaks the total count
  of links and makes codes guessable/sequential.
- A heavier ORM (SQLAlchemy): rejected for this scale — three queries
  total, raw sqlite3 is simpler to read and has zero extra dependencies.

## Design
- `app/storage.py`: `init_db`, `insert_link`, `get_link`. `db_path`
  resolved at call time (`db_path or DB_PATH`), not baked into a default
  argument, so tests can monkeypatch `storage.DB_PATH` after import.
- `app/shortcode.py`: `generate_code(length=7)`, pure function, no I/O.
- `app/main.py`: `POST /links` (mints a code, retries on collision),
  `GET /{code}` (307 redirect or 404).

## Testing strategy
- "returns a code + short_url" / "redirects correctly" → `test_shorten_and_redirect`
- "unknown code is 404" → `test_unknown_code_is_404`
- "no collisions" → implied by the retry loop; not separately tested at
  this scale (collision probability with a 62^7 space is negligible for a
  test suite of this size — flagged here rather than silently assumed).

## Rollback plan
Single new app, nothing to roll back *into* — revert the commit.

## Risks & blast radius
None yet — first commit, nothing depends on this.

## Phases
- [x] Phase 1: storage.py + shortcode.py + main.py + tests. Verify:
  `pytest tests/ -v`. Rollback: revert commit.
