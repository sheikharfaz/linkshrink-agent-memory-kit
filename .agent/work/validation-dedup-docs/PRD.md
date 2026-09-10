# PRD: validation-dedup-docs

## Problem
Two rough edges before this is presentable: shortening the same URL twice
creates two different codes for it (wasteful, confusing), and there's no
README explaining how to run any of this.

## Current vs desired behavior
- Current: every POST /links call mints a new code, even for a URL already
  shortened. Invalid URLs — checked via `query.py file app/main.py`, which
  showed `ShortenRequest.url: HttpUrl` — pydantic already rejects those
  with a 422, so that acceptance criterion is "verify it's true," not
  "build it."
- Desired: shortening the same URL twice returns the existing code.
  Documented setup/run/test instructions exist.

## Acceptance criteria
- [x] POST /links with an invalid URL returns 422 (verified existing
  behavior, not new code).
- [x] Shortening the same URL twice returns the same code both times.
- [x] README documents install/run/test/endpoints.

## Non-goals
- No auth, no custom codes, no per-click event log — still out of scope,
  same as Sessions 1–3's non-goals.

## Open questions
<!-- none this session -->

## Assumptions
- None needed — `query.py file app/main.py` answered the "is validation
  already there" question directly instead of requiring a guess.
