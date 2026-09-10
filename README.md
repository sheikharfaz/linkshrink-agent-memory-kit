# linkshrink-agent-memory-kit

A small URL-shortener API — `POST /links`, `GET /{code}` (redirect),
`GET /links/{code}/stats`, rate limiting on shortening, URL validation and
dedup. FastAPI + SQLite, no ORM.

This is one half of a controlled comparison: the identical spec as
[linkshrink-baseline](https://github.com/sheikharfaz/linkshrink-baseline),
built across the same four feature sessions, but with
[agent-memory-kit](https://github.com/sheikharfaz/agent-memory-kit)
installed. See [SESSION_LOG.md](SESSION_LOG.md) for what tooling ran at
each session (including an honest one: at this project's small scale, the
codebase map sometimes costs *more* tokens than just reading the relevant
file directly — the crossover point is a real-codebase question, not a
free lunch), and [COMPARISON.md](COMPARISON.md) for the full numbers
against the baseline repo.

## Run it

```bash
pip install -r requirements.txt
uvicorn app.main:app --reload
```

```bash
curl -X POST localhost:8000/links -H 'content-type: application/json' \
  -d '{"url": "https://example.com"}'
# {"code": "aB3dE7x", "short_url": "/aB3dE7x"}

curl -i localhost:8000/aB3dE7x            # 307 redirect
curl localhost:8000/links/aB3dE7x/stats   # click_count, last_clicked_at
```

## Test

```bash
python3 -m pytest tests/ -v
```

## Endpoints

| Method | Path | Notes |
|---|---|---|
| POST | `/links` | Rate-limited 5/minute per IP. Shortening the same URL twice returns the same code. |
| GET | `/{code}` | 307 redirect; records a click. |
| GET | `/links/{code}/stats` | click_count, created_at, last_clicked_at. |

## What's different here vs. the baseline repo

Same application code, same endpoints, same tests passing — the
difference is entirely in how it got built:

- [`.agent/work/`](.agent/work/) — a `PRD.md` (what, why, acceptance
  criteria) and `TRD.md` (how, alternatives considered, testing strategy)
  per session, written before/alongside the code.
- [`.agent/memory/CODEBASE_MAP.md`](.agent/memory/CODEBASE_MAP.md) — the
  committed codebase map; existing code was queried (`query.py file`,
  `query.py impact`) instead of re-read in full at the start of Sessions
  2–4.
- `.agent/memory/tools/` — `slowapi` (Session 3's new dependency) went
  through `tool-provisioning`'s propose → approve → install flow, logged
  to a ledger, rather than just being pip-installed.
- Every session ends with a `dev-recap` — a summary, a heuristic gap scan,
  and a self-assessed understanding check (kept local, not in this repo —
  see the kit's own privacy stance).

None of this is free — see [SESSION_LOG.md](SESSION_LOG.md) for the actual
token cost of the map/queries/recall each session, including where it
loses to just reading two small files directly. The comparison is honest
in both directions on purpose.
