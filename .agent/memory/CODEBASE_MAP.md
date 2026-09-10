# CODEBASE MAP — linkshrink-agent-memory-kit

> GENERATED FILE. Do not edit by hand; rerun the codebase-memory skill.
> generation `a158563756` · 2026-09-10T13:56:12Z · 27 files (9 parsed) · 27 symbols · 69 edges

## How to use this file

Map → module shard (`modules/<slug>.md`) → `query.py` verb → a source line range, in that order. Absence here is **not** proof of absence — see Coverage.

## Stack

- `python` — 275 LOC (100%)

## Likely entry points

- `app/main.py`

## HTTP surface (3 detected)

- `GET /links/{code}/stats` → `app/main.py`
- `GET /{code}` → `app/main.py`
- `POST /links` → `app/main.py`

## Modules (6) — largest first

| module | files | LOC | symbols | shard |
|---|---:|---:|---:|---|
| `app` | 4 | 144 | 14 | [`app.md`](modules/app.md) |
| `tests` | 5 | 131 | 13 | [`tests.md`](modules/tests.md) |

_+4 module(s) with no parsed code (shard in `modules/` has the file list)._

## Most-imported dependencies

`app` (5), `pytest` (4), `fastapi.testclient` (4), `app.main` (4)

## Coverage and limits

- Discovery: `git`. Parsed 9 of 27 files (skipped non-code=16, unknown-type=2).
- Call edges (`on`): 9 resolved, 4 unresolved (ambiguous name).
- Pattern-matched, not compiler-accurate: dynamic dispatch, macros, reflection, codegen, and string-built calls are invisible to it.
- **Clean ≠ proof of absence.** Confirm with a direct search over the relevant paths before any "there is no X" claim.
