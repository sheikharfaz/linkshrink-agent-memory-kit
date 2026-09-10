# TRD: rate-limiting

## Approach
Add `slowapi` (Starlette/FastAPI-native rate limiting, IP-keyed, in-memory
store) and apply `@limiter.limit("5/minute")` to the `shorten` route only.

## Alternatives considered
- Hand-rolled in-memory counter dict keyed by IP: rejected — reinvents
  what `slowapi` already does correctly (sliding window, proper 429
  responses, thread-safety), for a well-understood, small, actively
  maintained dependency.
- `fastapi-limiter` (Redis-backed): rejected — needs a Redis instance,
  overkill for a single-process demo app with no existing infra
  dependency.

**Dependency acquisition, via tool-provisioning** (not the shipped
registry — added a project-local entry first):
```
.agent/memory/tools/registry.local.json  (new entry: rate-limit-fastapi -> slowapi)
toolkit.py search "rate limit"           -> found the new local entry
toolkit.py plan rate-limit-fastapi       -> printed install/uninstall commands, risk note
                                             (approved in chat before running install)
toolkit.py install rate-limit-fastapi --session sess3
                                          -> ran `pip install --user slowapi`,
                                             logged to .agent/memory/tools/tool-ledger.jsonl
```
**Deliberately not swept at session end**: `slowapi` becomes a permanent
runtime dependency of the shipped app (added to `requirements.txt`), not a
one-off task tool — sweeping it would break the app. The ledger entry
stays open on purpose; that's the honest record of when and how it was
added, distinct from `requirements.txt` recording *that* it's a dependency.

## Design
- `app/main.py`: `Limiter(key_func=get_remote_address)` on `app.state`;
  `RateLimitExceeded` exception handler; `@limiter.limit("5/minute")` on
  `shorten()` only (`query.py file app/main.py` confirmed it's the only
  route that should carry the decorator — stats/redirect are read-only and
  out of scope per the PRD).

## Testing strategy
- "5 requests succeed" → `test_allows_up_to_the_limit`
- "6th request is 429" → `test_blocks_after_the_limit`
- Limiter state reset in a fixture (module-level `limiter` object persists
  across TestClient instances within one pytest process otherwise).

## Rollback plan
Revert the commit; `pip uninstall slowapi` if nothing else in the app ends
up depending on it (nothing will, after revert).

## Risks & blast radius
`query.py impact app/main.py` — no other module calls into main.py's
routes (it's the entry point), so this is low-risk in isolation. The
actual risk is behavioral: a legitimate burst of 6+ shortens from one IP
now gets rejected — acceptable per the PRD's acceptance criteria.

## Phases
- [x] Phase 1: acquire slowapi via tool-provisioning, wire limiter into
  main.py, tests. Verify: `pytest tests/ -v`. Rollback: revert commit +
  uninstall dependency if nothing else needs it.
