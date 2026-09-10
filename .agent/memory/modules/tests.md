# module: `tests`

> GENERATED · generation `190b1267f8` · 5 files · 131 LOC · 13 symbols

Languages: `python`×5

## Files

- `tests/__init__.py` — python, 1 LOC, 0 symbols
- `tests/test_core.py` — python, 30 LOC, 3 symbols
- `tests/test_rate_limit.py` — python, 29 LOC, 3 symbols
- `tests/test_stats.py` — python, 44 LOC, 4 symbols
- `tests/test_validation.py` — python, 27 LOC, 3 symbols

## Symbols

- `temp_db` function `tests/test_core.py:9`
- `test_shorten_and_redirect` function `tests/test_core.py:14`
- `test_unknown_code_is_404` function `tests/test_core.py:26`
- `temp_db` function `tests/test_rate_limit.py:9`
- `test_allows_up_to_the_limit` function `tests/test_rate_limit.py:16`
- `test_blocks_after_the_limit` function `tests/test_rate_limit.py:23`
- `temp_db` function `tests/test_stats.py:9`
- `test_stats_start_at_zero_clicks` function `tests/test_stats.py:14`
- `test_redirect_increments_click_count` function `tests/test_stats.py:26`
- `test_stats_for_unknown_code_is_404` function `tests/test_stats.py:40`
- `temp_db` function `tests/test_validation.py:9`
- `test_invalid_url_is_rejected` function `tests/test_validation.py:16`
- `test_shortening_the_same_url_twice_returns_the_same_code` function `tests/test_validation.py:22`
