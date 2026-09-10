# PRD: click-analytics

## Problem
Nobody using the shortener can tell if a link is actually being clicked, or
by how much. Need basic analytics per short code.

## Current vs desired behavior
- Current: `links` table only has `code`, `url`, `created_at`.
- Desired: `GET /links/{code}/stats` returns click_count and when it was
  last clicked; every redirect counts as a click.

## Acceptance criteria
- [x] GET /links/{code}/stats on a fresh code returns click_count=0,
  last_clicked_at=null.
- [x] Each GET /{code} redirect increments click_count and sets
  last_clicked_at.
- [x] GET /links/{code}/stats on an unknown code returns 404.

## Non-goals
- No per-click event log (IP, referrer, etc.) — just a running count. A
  full click-events table is a bigger schema change than this session's
  scope.

## Open questions
<!-- none this session -->

## Assumptions
- Adding columns to the existing `links` table (not a separate `clicks`
  table) is enough for a running total — confirmed against the existing
  schema via `query.py file app/storage.py` rather than assumed blind.
