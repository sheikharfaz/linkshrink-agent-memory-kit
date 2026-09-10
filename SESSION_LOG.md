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

---

## Session 3 — Rate limiting (new dependency, via tool-provisioning)

**Simulated as:** a new session. Recovered context the same way as
Session 2; the new part is acquiring a dependency that isn't in the kit's
shipped registry.

**Context read this session:**

| Source | Chars | ≈ Tokens |
|---|---|---|
| `memory.py recent` (session-memory recall) | 178 | 44 |
| `query.py file app/main.py` | 445 | 111 |
| `CODEBASE_MAP.md` (read once, session start) | 4,462 | 1,115 |
| **Total** | **5,085** | **≈ 1,271** |

Baseline's Session 3 re-read only `app/main.py` in full (1,562 chars,
≈391 tokens) — cheaper than this session, for the same reason as
Session 2: the map's fixed cost doesn't pay for itself at this scale. The
gap would flip with a bigger codebase; see `agent-memory-kit`'s own
benchmark suite for where.

**Dependency acquisition (the part this session is actually about):**
`slowapi` isn't in the kit's shipped registry, so a project-local entry
was added first (`.agent/memory/tools/registry.local.json`), then the real
flow:

```
toolkit.py search "rate limit"               # found the new local entry
toolkit.py plan rate-limit-fastapi           # printed install/uninstall + risk, ran nothing
                                              # <- approved in chat here, same as a human would
toolkit.py install rate-limit-fastapi --session sess3
                                              # ran pip install, logged to tool-ledger.jsonl
```

**Deliberately left open in the ledger:** `slowapi` becomes a permanent
runtime dependency (`requirements.txt`), not a one-off task tool —
`toolkit.py list-installed` still shows it after this session, on purpose.
Sweeping it would break the app. This is the honest boundary of
tool-provisioning's uninstall discipline: it's for task-scoped tools, not
things the shipped product now depends on. `requirements.txt` is the
record of *that* it's a dependency; the ledger is the record of *when and
how* it was approved and added.

**Built:** identical application changes to the baseline repo's Session 3.

**Verification:** `python3 -m pytest tests/ -v` → 7 passed. `spec_first.py
check rate-limiting` → 3/3 acceptance criteria checked.

---

## Session 4 — Validation, dedup, docs

**Simulated as:** a new session. `memory.py recent` (one-per-session
dedup) only surfaced the latest note, since every session in this demo
ran under the CLI's default `cli` session id rather than a distinct id per
session — a real Claude Code session gets a genuinely unique id
automatically, so this is an artifact of how this demo was driven by
hand, not of the tool. Used `memory.py recall` with a topical query
instead, which isn't affected by that and surfaced 2 of the 3 prior
sessions' notes by similarity.

**Context read this session:**

| Source | Chars | ≈ Tokens |
|---|---|---|
| `memory.py recall "what features have been built..."` | 358 | 89 |
| `query.py file app/main.py` | 484 | 121 |
| `query.py file app/storage.py` | 261 | 65 |
| `query.py file app/shortcode.py` | 140 | 35 |
| `CODEBASE_MAP.md` (read once, session start) | 4,520 | 1,130 |
| **Total** | **5,763** | **≈ 1,440** |

Baseline's Session 4 re-read 3 files in full for 3,470 chars (≈868
tokens) — again cheaper here, again because of the map's fixed cost at
this project's small scale (see Sessions 2–3's notes on the same pattern).

**Built:** identical application changes to the baseline repo's Session 4
(`get_link_by_url`, dedup check in `shorten()`, `tests/test_validation.py`,
this README).

**Verification:** `python3 -m pytest tests/ -v` → 9 passed. `spec_first.py
check validation-dedup-docs` → 3/3 acceptance criteria checked.

**A methodology note, for completeness:** no session in this repo wrote a
separate `research.md` (the `AGENTS.md` §5 "map what exists" artifact) —
`codebase-memory` queries served that role directly each time, which is a
reasonable substitution for a project this size but wouldn't necessarily
hold at a scale where research.md's free-form synthesis earns its keep.
`spec_first.py list` reflects this honestly: every session shows a PRD and
TRD, none shows research.md.

---

## Totals across all 4 sessions

| | |
|---|---|
| Context read/recalled via kit tooling (Sessions 2–4) | **≈ 4,004 tokens** (1,293 + 1,271 + 1,440) |
| New dependencies added | 1 (`slowapi`) — proposed, approved, installed, ledgered |
| PRD/TRD artifacts produced | 4 (one pair per session) — [`.agent/work/`](.agent/work/) |
| dev-recap entries produced | 4 (kept local, per the kit's privacy stance) |
| Final test count | 9, all passing |
| Final app code | 4 files, 140 lines (`app/`) — byte-for-byte identical to `linkshrink-baseline`'s (`diff` confirms it), on purpose: this comparison is about process, not code quality |
| Real bugs in the kit found while building this | 1 ([fixed upstream](https://github.com/sheikharfaz/agent-memory-kit) — `dev-recap`'s `gaps` checked the wrong JSON field against the codebase index) |

**The honest headline number: this repo used *more* tokens than the
baseline (≈4,004 vs. ≈1,993) for context/recall across Sessions 2–4.**
That's not a typo, and it's not buried here. At this project's scale — 4–5
source files — `CODEBASE_MAP.md`'s fixed cost (read once per session,
~1,100–1,200 tokens each time) outweighs what re-reading 1–3 small files
directly would have cost. The map is built to summarize a whole codebase;
a codebase this small barely needs summarizing.

That is *not* the result `agent-memory-kit`'s own benchmark suite shows
against real, larger repositories — `django/django` (2,979 parsed files)
comes back at a 35.6x *reduction*, not increase — see
[`agent-memory-kit/benchmarks/`](https://github.com/sheikharfaz/agent-memory-kit/tree/main/benchmarks).
Read together, the two results say the same honest thing: the map's fixed
cost has to amortize over enough code to be worth paying, and this project
was deliberately too small to clear that bar. What this repo *does* show
regardless of that number — visible directly in its git history and
`.agent/work/`, not asserted — is a PRD and TRD per feature, a real
tool-acquisition audit trail, and cross-session continuity that survives
closing the chat. See [COMPARISON.md](COMPARISON.md) for the full
side-by-side against the baseline repo.

---

## Update — agent-memory-kit was improved based on this finding

This number was reported to the kit's maintainer, who traced it to a real,
fixable cause rather than accepting "small repos just lose": the map's
`CODEBASE_MAP.md` was including `.agent/skills/` — this kit's own *vendored*
scripts, copied into every consumer repo by the installer — as if it were
this project's own source. 93% of what the map was summarizing was
kit-internal code, not LinkShrink. Fixed upstream in
[`agent-memory-kit@55f7b3f`](https://github.com/sheikharfaz/agent-memory-kit/commit/55f7b3f)
— `.agent/skills/` excluded by default, plus three rounds of tightening
`CODEBASE_MAP.md`'s fixed-overhead sections without dropping any of the
information they carry (denser prose, zero-symbol modules summarized
instead of tabled, "Hubs" requiring 2+ callers instead of 1+).

Recomputing Sessions 2–4 with the fixed kit, same methodology, same real
commands re-run against this repo's own history:

| Session | Old kit-assisted | New kit-assisted | Baseline |
|---|---|---|---|
| 2 (click analytics) | 1,293 | 511 | 734 |
| 3 (rate limiting) | 1,271 | 498 | 391 |
| 4 (validation/dedup/docs) | 1,440 | 667 | 868 |
| **Total** | **≈4,004** | **≈1,676** | **≈1,993** |

**The kit-assisted total is now below the baseline — ≈1,676 vs. ≈1,993,
about 16% fewer tokens** — not the 2x-worse result originally measured,
and also honestly not "half of baseline." Two of three sessions (2 and 3)
individually beat their baseline counterpart; Session 4 (four separate
queries plus a larger recall) still costs more than baseline's single
three-file re-read, because a `query.py file` call per file plus one map
read doesn't beat reading those same 3 small files directly once you're
querying enough of them in one session — the crossover depends on how
many distinct things a session touches, not just repo size.

Going further would mean either shrinking `CODEBASE_MAP.md` below what a
real multi-file Python project's structure actually needs to say (stack,
entry points, HTTP surface, hubs, coverage), or dropping "read the map
every session" as a hard rule — both would trade away the actual thing
`codebase-memory` is for. The honest conclusion: real, substantial,
tested improvement (128 passing tests, upstream), not a forced number.
