# 📖 Instant-Dictionary-API

> A minimal **JSON dictionary REST API** built with `justpy`, backed by a CSV word/definition dataset. My first hands-on API project.

[![Python](https://img.shields.io/badge/python-3.7%2B-blue)]()
[![JustPy](https://img.shields.io/badge/framework-justpy-purple)]()
[![Pandas](https://img.shields.io/badge/data-pandas-yellow)]()

---

## 📌 Overview

A small two-route web service that exposes a programmatic dictionary lookup over HTTP. Users (or other apps) can hit a single endpoint with a `?w=word` query parameter and receive a JSON response containing all known definitions of that term.

Both human-readable documentation and the JSON API live in the same `justpy` app, making it a self-contained learning project around:
- HTTP request handling
- JSON response formatting
- CSV-based "database" lookups with `pandas`
- Building a public-facing API

## 🛣️ Routes

| Path | Handler | Returns |
|---|---|---|
| `/` | `documentation.Doc.serve` | HTML documentation page |
| `/api?w={word}` | `api.Api.serve` | JSON `{ "word": ..., "definition": [...] }` |

## 🧱 Architecture

```
HTTP GET /api?w=beach
        │
        ▼
┌────────────────────┐
│   Api.serve(req)   │   ── reads ?w= query param
└────────────────────┘
        │
        ▼
┌────────────────────┐
│  Definition(term)  │   ── filters data.csv with pandas
└────────────────────┘
        │
        ▼  JSON response
{ "word": "beach",
  "definition": ["A sandy or pebbly shore..."] }
```

## 🗂️ Project Structure

```
Instant-Dictionary-API/
├── main.py            # Routes + JustPy entry point
├── api.py             # /api endpoint — returns JSON
├── definition.py      # Definition class — pandas CSV lookup
├── documentation.py   # / endpoint — HTML docs
└── data.csv           # word,definition CSV "database"
```

## 🚀 Setup & Run

### 1. Install dependencies
```bash
pip install justpy pandas
```

### 2. Start the server
```bash
python main.py
```
By default JustPy serves on **http://localhost:8000**.

### 3. Try it
```bash
# Browser-friendly docs
open http://localhost:8000/

# API call
curl "http://localhost:8000/api?w=beach"
```

**Sample response:**
```json
{
  "word": "beach",
  "definition": [
    "A pebbly or sandy shore, especially by the ocean between high- and low-water marks."
  ]
}
```

## 🧩 Core Components

### `Definition` class (`definition.py`)
```python
class Definition:
    def __init__(self, term):
        self.term = term

    def get(self):
        df = pandas.read_csv('data.csv')
        return tuple(df.loc[df['word'] == self.term]['definition'])
```
Loads the CSV into a DataFrame on every call and filters by the `word` column. Returns a tuple so multiple definitions per term are supported.

### `Api` class (`api.py`)
A `justpy.WebPage`-returning classmethod that wraps the `Definition` lookup in a JSON response.

## 💡 Possible Extensions

- Cache `data.csv` in memory instead of re-reading on every request
- Migrate from CSV to **SQLite/PostgreSQL** for performance
- Add `POST /api` to allow definition submissions
- Wrap with a real REST framework (FastAPI / Flask) for production
- Add input validation, rate limiting, and OpenAPI docs

## 📚 Related Project

See [Instant-Dictionary](https://github.com/denisvreshtazi/Instant-Dictionary) — the full web-app version of the same idea (multi-page UI with home/about/dictionary).

## 👤 Author

**Denis Vreshtazi** — [GitHub](https://github.com/denisvreshtazi)
