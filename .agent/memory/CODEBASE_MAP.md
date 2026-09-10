# CODEBASE MAP — linkshrink-agent-memory-kit

> GENERATED FILE. Do not edit by hand; rerun the codebase-memory skill.
> generation `64a25da1ac` · 2026-09-10T13:30:51Z · 36 files (18 parsed) · 177 symbols · 393 edges

## How to use this file

1. Read this map first. It is the cheapest view of the repo.
2. Narrow to a module, then open `.agent/memory/modules/<slug>.md`.
3. For a symbol, run `query.py def|who-calls|calls-of <name>` instead of grepping.
4. Only then open source files, and only the line ranges you need.

Absence of a symbol here is **not** proof it does not exist — see Coverage.

## Stack

- `python` — 3,736 LOC (100%)

## Likely entry points

- `.agent/skills/codebase-memory/index.py`
- `app/main.py`

## HTTP surface (3 detected)

- `GET /links/{code}/stats` → `app/main.py`
- `GET /{code}` → `app/main.py`
- `POST /links` → `app/main.py`

## Modules (7) — largest first

| module | files | LOC | symbols | shard |
|---|---:|---:|---:|---|
| `.agent/skills` | 17 | 3,536 | 157 | [`agent__skills.md`](modules/agent__skills.md) |
| `app` | 4 | 125 | 13 | [`app.md`](modules/app.md) |
| `tests` | 3 | 75 | 7 | [`tests.md`](modules/tests.md) |
| `.agent/work` | 4 | 0 | 0 | [`agent__work.md`](modules/agent__work.md) |
| `.claude` | 1 | 0 | 0 | [`claude.md`](modules/claude.md) |
| `.github` | 1 | 0 | 0 | [`github.md`](modules/github.md) |
| `(root)` | 6 | 0 | 0 | [`-root-.md`](modules/-root-.md) |

## Hubs — most-called symbols (change these carefully)

- `safe_main` (function) ← 4 callers · `.agent/skills/session-memory/hooks/_common.py:49`
- `append_entry` (function) ← 4 callers · `.agent/skills/session-memory/memory.py:119`
- `search` (function) ← 4 callers · `.agent/skills/tool-provisioning/toolkit.py:174`
- `files` (function) ← 3 callers · `.agent/skills/codebase-memory/query.py:71`
- `add` (function) ← 3 callers · `.agent/skills/codebase-memory/query.py:415`
- `session_memory_disabled` (function) ← 3 callers · `.agent/skills/session-memory/hooks/_common.py:16`
- `read_hook_input` (function) ← 3 callers · `.agent/skills/session-memory/hooks/_common.py:23`
- `symbols` (function) ← 2 callers · `.agent/skills/codebase-memory/query.py:68`
- `edges` (function) ← 2 callers · `.agent/skills/codebase-memory/query.py:74`
- `snippet` (function) ← 2 callers · `.agent/skills/session-memory/hooks/_common.py:44`
- `recall` (function) ← 2 callers · `.agent/skills/session-memory/memory.py:192`
- `recent` (function) ← 2 callers · `.agent/skills/session-memory/memory.py:226`
- `prune` (function) ← 2 callers · `.agent/skills/session-memory/memory.py:270`
- `is_opaque` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:151`
- `lang_of` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:156`
- `load_agentignore` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:166`
- `matches_any` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:178`
- `git_files` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:188`
- `_under_agent_memory` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:199`
- `walk_files` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:203`
- `keep_dir` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:208`
- `discover` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:225`
- `_rx` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:255`
- `read_text` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:408`
- `extract` (function) ← 1 callers · `.agent/skills/codebase-memory/index.py:428`

## Most-imported dependencies

`sys` (11), `os` (11), `json` (9), `argparse` (6), `time` (4), `subprocess` (4), `re` (4), `memory` (4), `app` (3), `_common` (3)

## Coverage and limits

- Discovery: `git`. Parsed 18 of 36 files.
- Skipped: non-code=16, unknown-type=2
- Call edges: `on`. 129 resolved, 36 call sites left unresolved because the name was ambiguous across files.
- Symbols come from language-aware pattern matching, not a full compiler front end. Dynamic dispatch, macros, reflection, code generation and string-built calls are invisible to it.
- **A clean result means "no recorded gap", never "proven complete".** Before any claim that something does not exist, confirm with a direct search over the relevant paths and say which paths you covered.
