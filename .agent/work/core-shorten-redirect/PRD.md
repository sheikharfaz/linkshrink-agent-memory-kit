# PRD: core-shorten-redirect

## Problem
Need a minimal URL shortener: given a long URL, return a short code that
redirects to it. This is the foundation every later feature (analytics,
rate limiting, validation) builds on.

## Current vs desired behavior
- Current: nothing exists yet.
- Desired: `POST /links {"url": ...}` returns a short code; `GET /{code}`
  redirects to the original URL.

## Acceptance criteria
- [x] POST /links with a valid URL returns a 7-character code and a
  short_url path.
- [x] GET /{code} for a known code returns a 307 redirect to the original
  URL.
- [x] GET /{code} for an unknown code returns 404.
- [x] Generated codes never collide with an existing one.

## Non-goals
- No analytics, no rate limiting, no auth in this session — later
  sessions, tracked separately.
- No custom/vanity codes — random only, for now.

## Open questions
<!-- none this session -- the shape here was unambiguous enough not to need
     asking. Leaving this section empty is the correct way to say "none";
     a "- None" bullet gets counted as an unanswered question by
     spec_first.py's heuristic parser, found the hard way while building
     this repo. -->

## Assumptions
- SQLite is enough for this scale; no need for a heavier database this
  early. Revisit only if a later session's requirements demand it.
