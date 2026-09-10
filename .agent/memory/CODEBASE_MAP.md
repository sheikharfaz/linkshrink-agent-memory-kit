# CODEBASE MAP — linkshrink-agent-memory-kit

> GENERATED FILE. Do not edit by hand; rerun the codebase-memory skill.
> generation `190b1267f8` · 2026-09-10T14:33:27Z · 27 files (9 parsed) · 27 symbols · 69 edges

## How to use this file

Map → module shard (`modules/<slug>.md`) → `query.py` verb → a source line range, in that order. Absence here is **not** proof of absence — see Coverage.

## Overview

- Stack: `python` 275 LOC (100%)
- Entry point(s): `app/main.py`
- Routes: `GET /links/{code}/stats`→`app/main.py`; `GET /{code}`→`app/main.py`; `POST /links`→`app/main.py`

## Modules (6) — largest first

- `app`: 4f/144L/14s → [`app.md`](modules/app.md)
- `tests`: 5f/131L/13s → [`tests.md`](modules/tests.md)
_+4 module(s) with no parsed code (shard in `modules/` has the file list)._

## Most-imported dependencies

`app` (5), `pytest` (4), `fastapi.testclient` (4), `app.main` (4)

## Coverage and limits

- git discovery, 9/27 files parsed, skipped non-code=16, unknown-type=2; calls=`on`, 9 resolved, 4 ambiguous.
- Pattern-matched, not compiler-accurate (dynamic dispatch, macros, reflection, codegen, string-built calls are invisible). **Clean ≠ proof of absence** — confirm with a direct search before any "there is no X" claim.
