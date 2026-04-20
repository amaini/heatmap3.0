Stocks Heatmap (Django)

Quickstart

- Create a virtualenv and install Django 4.x
- Run migrations and start the server

Commands

1. python -m venv .venv
2. .venv/Scripts/Activate.ps1  # on Windows PowerShell
3. pip install django
4. python manage.py migrate
5. python manage.py seed_demo   # add demo sectors/tickers/lots and cached quotes
6. python manage.py runserver

Open http://127.0.0.1:8000/

Features

- CRUD: Sectors, Tickers, Purchased Lots (modals)
- Live data via Finnhub (quote, search, profile fallback)
- Debounced search with autocomplete (symbol + company)
- Heatmap grouped by sector; tile color by % change, progress bar between day low/high
- Manual refresh + auto-refresh (Off/1/5/15/30 min) with countdown and last refresh time
- Loading spinners, skeletons, error handling, retries, and timeouts
- Connection status indicator; persists last-good data in localStorage + DB cache
- Responsive, keyboard-friendly, with tooltips

Version 3.0 Additions (Heatmap 3.0)
------------------------------------

The 3.0 version of the heatmap introduces optional metrics to help traders
quickly gauge sector-level strength and weakness. These new features are
available at the `/api/sector-metrics` endpoint and leave the existing 2.0
behaviour unchanged. Key additions include:

- **Breadth statistics:** For each sector the API reports the number of
  advancing, declining and unchanged tickers along with the total count.
- **Average percent change:** The average daily percent change (`dp`) of all
  tickers in the sector.
- **Relative strength:** Compares the sector’s average change to a benchmark
  (SPY by default) using the formula `avg_sector_change / benchmark_change`.
- **Leaders and laggards:** Lists the top and bottom performers in each
  sector (three by default), sorted by percent change.
- **Panic signal:** Flags a sector if its average change is below a
  configurable negative threshold and the percentage of declining tickers
  exceeds a configurable fraction. These defaults can be overridden via
  query parameters `threshold_avg` and `threshold_pct`.

Example request:

```
GET /api/sector-metrics?threshold_avg=-3&threshold_pct=0.8&benchmark=QQQ&top_n=5
```

This will return per-sector metrics using QQQ as the benchmark, triggering
the panic signal when the average change falls below –3% and at least 80%
of tickers are down, and will list the top 5 leaders and laggards per
sector.

Backward compatibility
----------------------

To support easy rollbacks, the original 2.0 API and UI remain untouched in
this repository. Only new endpoints have been added. Deploying version 3.0
side-by-side with 2.0 is as simple as running the 3.0 codebase on a
separate branch or container. Should you decide to revert, deploy the
previous 2.0 image or branch without these changes.
