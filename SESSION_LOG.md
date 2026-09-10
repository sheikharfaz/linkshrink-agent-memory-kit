# Session log — linkshrink-agent-memory-kit

The kit-assisted half of a controlled comparison. Same spec, same four
feature sessions as [linkshrink-baseline](https://github.com/sheikharfaz/linkshrink-baseline),
built with [agent-memory-kit](https://github.com/sheikharfaz/agent-memory-kit)
installed: `codebase-memory` instead of re-reading whole files,
`spec-first` (PRD/TRD) before each feature, `tool-provisioning` for the new
dependency in Session 3, and `dev-recap` after each session. See
[COMPARISON.md](COMPARISON.md) for the full numbers against the baseline
repo. Token counts use the chars/4 heuristic, same as the baseline repo
and `agent-memory-kit`'s own benchmark suite.

Artifacts this repo has that the baseline doesn't, browsable directly:
[`.agent/work/`](.agent/work/) (a `PRD.md` + `TRD.md` per session) and
[`.agent/memory/CODEBASE_MAP.md`](.agent/memory/CODEBASE_MAP.md) (the
committed codebase map). `.agent/memory/session/` and
`.agent/memory/learning/` (dev-recap's quiz/familiarity data) are
gitignored on purpose — that data is explicitly local-only, per the kit's
own privacy stance.

---

## Session 1 — Core: shorten + redirect

**Simulated as:** the first session, repo is empty — same as the baseline
repo's Session 1, nothing to recall or query yet either way. Used
`spec-first` anyway: a PRD/TRD exists for this session even though it's
greenfield, because writing down intent isn't only valuable when there's
prior context to protect.

**Kit tooling used:**
- `spec_first.py scaffold core-shorten-redirect` → [`.agent/work/core-shorten-redirect/PRD.md`](.agent/work/core-shorten-redirect/PRD.md), [`TRD.md`](.agent/work/core-shorten-redirect/TRD.md)
- `index.py build` → first `CODEBASE_MAP.md` (32 files, 17 parsed, 170 symbols)
- `dev-recap/recap_log.py`: `set-familiarity --level new`, `record-recap`, `record-quiz --result understood`

**Context read this session:** none — same as baseline, nothing existed yet.

**Built:** identical application code to the baseline repo's Session 1
(`app/storage.py`, `app/shortcode.py`, `app/main.py`, `tests/test_core.py`)
— the app itself is deliberately the same in both repos, so this
comparison measures *process*, not code quality.

**Verification:** `python3 -m pytest tests/ -v` → 2 passed.
`spec_first.py check core-shorten-redirect` → 4/4 acceptance criteria
checked, TRD approach + testing strategy both written.

**A real finding, not staged:** the PRD's Open Questions section originally
had a `- None — ...` bullet. `spec_first.py check` counted that as an
*unanswered* open question — the heuristic parser doesn't distinguish "a
bullet that says none" from "an actual open item." Leaving the section
empty is the correct way to say "no open questions"; fixed in this repo's
PRD, noted here and in the upstream kit's follow-up list.

---

## Session 2 — Click analytics

**Simulated as:** a new session (`--exclude-session sess2` against the
existing entries). Instead of re-reading files, recovered context via
`session-memory` recall and `codebase-memory` queries.

**Kit tooling used:**
```
memory.py recent --exclude-session sess2 --limit 5
query.py file app/storage.py
query.py file app/main.py
```
plus `spec_first.py scaffold click-analytics`, and after implementing:
`dev-recap/recap_log.py gaps`, `record-recap`, `record-quiz`.

**Context read this session:**

| Source | Chars | ≈ Tokens |
|---|---|---|
| `memory.py recent` (session-memory recall) | 178 | 45 |
| `query.py file app/storage.py` | 228 | 57 |
| `query.py file app/main.py` | 360 | 90 |
| `CODEBASE_MAP.md` (read once, session start) | 4,404 | 1,101 |
| **Total** | **5,170** | **≈ 1,293** |

**An honest result, not the one you'd expect:** this is *more* than the
baseline repo's Session 2 (≈734 tokens, from reading 2 files directly). At
this project's tiny scale, `CODEBASE_MAP.md` describing every file costs
more than just reading the 2 files that actually mattered — the map's
fixed overhead doesn't pay for itself until the codebase is bigger than
"5 source files." See `agent-memory-kit`'s own benchmark suite
(`benchmarks/`, run against `django/django` and `psf/requests`) for where
that crossover actually happens — this repo is deliberately too small to
show it, and that's a real, useful data point in its own right, not a
result to hide.

**A second real bug, found using the tool for real:** `dev-recap/recap_log.py
gaps` reported `app/main.py` and `app/storage.py` as "not in the codebase
index" — immediately after rebuilding that index, which very clearly did
include them. Traced it to `_codebase_memory_indexed()` checking the wrong
JSON field name (`"path"` instead of `codebase-memory`'s real short key,
`"p"`), so the check could never match anything. Fixed upstream in
`agent-memory-kit` (with 3 regression tests), and the fix is already
copied into this repo's `.agent/skills/dev-recap/recap_log.py` — see the
`gaps` output later in this session for the corrected result.

**Built:** identical application changes to the baseline repo's Session 2.

**Verification:** `python3 -m pytest tests/ -v` → 5 passed. `spec_first.py
check click-analytics` → 3/3 acceptance criteria checked.
