# VulnStat — Vulnerability Reporting Dashboard

A single-file, offline-first HTML dashboard for visualizing vulnerability scan data. Drop in your CSV exports, get an interactive dashboard with sortable tables, division-based filtering, and a polished executive report view — all running entirely in your browser with no backend, no build step, and no data ever leaving your machine.

## Features

- **Two views** — an **Asset View** for day-to-day triage (searchable, sortable, filterable tables) and a **Report View** styled for executive readouts, with a repository ranking table and a large EOL donut chart
- **Zero dependencies** — a single `.html` file. Open it in a browser and go
- **Offline-safe** — no CDN calls, no external fonts, works on locked-down enterprise machines
- **CSV-driven** — reads `Assets.csv` (required) plus optional `Repositories.csv` and `EOL.csv`
- **Flexible CSV parsing** — column order doesn't matter, common header aliases are recognized (`Crit.`/`Critical`, `Owner`/`Group`/`Division`, etc.), and extra/unused columns are ignored
- **Division filtering** — scope the whole dashboard to one business unit/owner via Repositories.csv, with a stat-card breakdown showing all divisions side-by-side
- **Repository click-to-filter** — click one or more repositories in the table to filter the assets list to just those repos
- **Live search** on every table, debounced for large datasets
- **Column visibility toggles** — show/hide High/Medium/Low severity columns, EOL columns, Division column, etc. per table via a settings cog
- **Minimum-asset-count filter** — hide noisy, low-signal repositories from both tables and rankings
- **CSV export** of any table's current (filtered/sorted) view
- **Snapshot export** — bakes the loaded data and your current UI configuration (filters, sort, columns, view, theme) into a new standalone `.html` file you can share with others, with no re-upload required
- **Scoped exports** — exporting while filtered to a division only includes that division's data; nothing else leaks into the file
- **Virtual scrolling** — the assets table stays smooth even with 10,000+ rows
- **Light/dark theme**, remembered across sessions
- **Custom title & security classification banner**, set at setup time
- **Optional Links panel** — add a handful of quick-reference hyperlinks (e.g. a remediation runbook) shown under the tables

## Quick Start

1. Open `vuln-dashboard.html` in any modern browser (Chrome, Edge, Firefox)
2. Set a dashboard title and (optionally) a security classification and report date
3. Upload your `Assets.csv` (required)
4. Optionally upload `Repositories.csv` and/or `EOL.csv`
5. Click **Generate Dashboard**

No installation, no server, no internet connection required after the file is downloaded.

## CSV Formats

### `Assets.csv` (required)

One row per scanned asset. At least one of **DNS Name** or **IP Address** must be present per row.

| Column | Aliases | Required |
|---|---|---|
| `DNS Name` | `Asset`, `Hostname` | One of DNS Name / IP Address |
| `IP Address` | `IP` | One of DNS Name / IP Address |
| `Repository` | `Repo` | Yes |
| `Crit.` | `Critical` | No (defaults to 0) |
| `High` | | No |
| `Med.` | `Medium` | No |
| `Low` | | No |

```csv
IP Address,DNS Name,Repository,Crit.,High,Med.,Low
10.0.1.5,web01.example.com,WEB-FRONTEND,2,1,3,5
10.0.1.6,web02.example.com,WEB-FRONTEND,0,2,4,4
```

### `Repositories.csv` (optional)

Maps repositories to an owning division/group. If omitted, the dashboard runs in single-scope mode with no division filtering.

| Column | Aliases |
|---|---|
| `Owner` | `Group`, `Division` |
| `Repository` | `Repositories` |

Owner values may or may not be wrapped in brackets (`[TEAM-A]` or `TEAM-A`). Repository lists may be a bracketed Python-style list (`['REPO-A', 'REPO-B']`) or a plain comma-separated string — both are accepted, spaces optional.

```csv
Group,Repository
TEAM-A,"['WEB-FRONTEND', 'API-BACKEND']"
TEAM-B,['DATA-PIPELINE']
```

### `EOL.csv` (optional)

Flags assets running an end-of-life operating system. Matched against `Assets.csv` by DNS name or IP address (case-insensitive).

| Column | Aliases |
|---|---|
| `DNS Name` | `Hostname` |
| `IP Address` | `IP` |
| `Plugin Name` | `Plugin` |
| `Repository` | (informational only) |
| `Severity` | (informational only) |

```csv
Plugin Name,DNS Name,IP Address,Repository,Severity
Canonical Ubuntu Linux SEoL (20.04.x),web01.example.com,10.0.1.5,WEB-FRONTEND,Critical
```

## Views

### Asset View (default)

- Four key stat cards: Total Scanned Assets, EOL Assets (with % badge), Critical Vulns, Vulnerability Density
- **Repositories** table — sortable, searchable, click a row to filter the assets table to that repo (multi-select)
- **Assets** table — sortable, searchable, virtualized for large datasets, with a column-visibility cog and CSV export
- Optional **Links** panel beneath the tables

### Report View

A single-screen, non-scrolling layout designed for landscape monitors and executive presentations:

- Four large stat cards on the left (Total Assets, EOL Assets with a centered donut chart, Critical Vulns, Vulnerability Density) with fluid typography that scales to fill the available space
- One ranking table on the right, toggled between **Rank by Vulnerability Density** and **Rank by EOL Density**

## Header Controls

- **Division dropdown** — filter the entire dashboard to one division, and choose which divisions appear as a breakdown on each stat card
- **Settings cog** — switch between Asset/Report view, export a snapshot, toggle light/dark mode, or load a new dataset

## Exporting

**CSV export** (per-table, via each table's cog menu) — exports exactly what's currently visible: active search, sort order, and column selection.

**Snapshot export** (via the settings cog) — produces a new, fully standalone `.html` file with the current dataset and UI state baked in. Recipients can open it directly with no CSV upload step. If you're scoped to a division or have active repository filters when you export, only that data is included in the file — other divisions' assets, repositories, and EOL details are excluded entirely.

## Browser Support

Built with ES5-compatible JavaScript for compatibility with locked-down enterprise browser environments. No external network requests are made at any point — everything, including fonts, is served from system defaults or embedded in the file.

## Privacy & Security

All CSV parsing, filtering, and rendering happens client-side in the browser. No data is transmitted anywhere. This makes the tool suitable for sensitive or classified vulnerability data, provided the exported `.html` files themselves are handled according to your organization's data handling policies.
