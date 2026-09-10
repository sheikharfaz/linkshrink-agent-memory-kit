# PRD: rate-limiting

## Problem
`POST /links` has no abuse protection — a script could mint unlimited
short codes for the same or arbitrary URLs.

## Current vs desired behavior
- Current: unlimited requests to POST /links.
- Desired: capped at 5 requests/minute per client IP; further requests get
  429.

## Acceptance criteria
- [x] Up to 5 POST /links requests per minute succeed.
- [x] The 6th request within that window returns 429.
- [x] Only the shorten endpoint is limited — redirect and stats stay
  unaffected.

## Non-goals
- No per-user/API-key limits (there's no auth yet) — IP-based only.
- No distributed rate limiting (single-process in-memory store is fine at
  this scale).

## Open questions
<!-- none this session -->

## Assumptions
- A third-party rate-limiting library is the right call rather than
  hand-rolling one — see TRD for what was actually considered before
  picking `slowapi` via tool-provisioning.
