# module: `app`

> GENERATED · generation `a158563756` · 4 files · 144 LOC · 14 symbols

Languages: `python`×4

## Routes

- `GET /links/{code}/stats` → `app/main.py`
- `GET /{code}` → `app/main.py`
- `POST /links` → `app/main.py`

## Files

- `app/__init__.py` — python, 1 LOC, 0 symbols
- `app/main.py` — python, 75 LOC, 7 symbols
- `app/shortcode.py` — python, 9 LOC, 1 symbols
- `app/storage.py` — python, 59 LOC, 6 symbols

## Symbols

- `ShortenResponse` class `app/main.py:32` ←1
- `StatsResponse` class `app/main.py:37` ←1
- `generate_code` function `app/shortcode.py:7` ←1
- `get_conn` function `app/storage.py:8` ←1
- `init_db` function `app/storage.py:17` ←1
- `insert_link` function `app/storage.py:31` ←1
- `get_link` function `app/storage.py:40` ←1
- `get_link_by_url` function `app/storage.py:46` ←1
- `record_click` function `app/storage.py:52` ←1
- `lifespan` function `app/main.py:16`
- `ShortenRequest` class `app/main.py:28`
- `shorten` function `app/main.py:47`
- `stats` function `app/main.py:61`
- `redirect` function `app/main.py:69`
