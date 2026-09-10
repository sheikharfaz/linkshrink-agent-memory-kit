# module: `app`

> GENERATED · generation `806149ea6f` · 4 files · 133 LOC · 13 symbols

Languages: `python`×4

## Routes

- `GET /links/{code}/stats` → `app/main.py`
- `GET /{code}` → `app/main.py`
- `POST /links` → `app/main.py`

## Files

- `app/__init__.py` — python, 1 LOC, 0 symbols
- `app/main.py` — python, 70 LOC, 7 symbols
- `app/shortcode.py` — python, 9 LOC, 1 symbols
- `app/storage.py` — python, 53 LOC, 5 symbols

## Symbols

- `ShortenResponse` class `app/main.py:32` ←1
- `StatsResponse` class `app/main.py:37` ←1
- `generate_code` function `app/shortcode.py:7` ←1
- `get_conn` function `app/storage.py:8` ←1
- `init_db` function `app/storage.py:17` ←1
- `insert_link` function `app/storage.py:31` ←1
- `get_link` function `app/storage.py:40` ←1
- `record_click` function `app/storage.py:46` ←1
- `lifespan` function `app/main.py:16`
- `ShortenRequest` class `app/main.py:28`
- `shorten` function `app/main.py:47`
- `stats` function `app/main.py:56`
- `redirect` function `app/main.py:64`
