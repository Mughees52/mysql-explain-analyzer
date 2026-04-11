# MySQL & MariaDB EXPLAIN Analyzer

A free, browser-based tool for visualizing and analyzing MySQL and MariaDB EXPLAIN output. Paste your query plan and get an interactive tree visualization, automatic performance issue detection, index recommendations, query rewrite suggestions, and index impact simulation.

**Live version: [reliadb.com/tools/explain/](https://reliadb.com/tools/explain/)**

## Features

- **49 detection rules** (8 critical, 20 warning, 5 info, 7 good, 4 MariaDB-specific, 5 MySQL 8.0+ specific)
- **Query-aware index advisor** — extracts WHERE/GROUP BY/ORDER BY from SQL, resolves table aliases, suggests composite covering indexes
- **7 query rewrite generators** — YEAR to range, subquery to JOIN, NOT IN to LEFT JOIN, and more
- **Index impact simulator** — predicts structural plan changes per index recommendation
- **Interactive tree visualization** — edge thickness = row count, node badges (Slow/Costly/Bad Estimate/Filter), highlight switcher (duration/rows/cost)
- **4 view tabs** — Tree, Table, Cost Chart, Estimate vs Actual
- **Before/After comparison** — compare two plans side by side
- **URL hash sharing** — share plans via compressed URL
- **LocalStorage history** — access previous analyses

## Supported Formats

| Format | Engine |
|--------|--------|
| EXPLAIN ANALYZE (tree) | MySQL 8.0+ |
| EXPLAIN FORMAT=JSON | MySQL / MariaDB |
| Traditional table | MySQL / MariaDB |
| ANALYZE table format | MariaDB 10.1+ |
| ANALYZE FORMAT=JSON | MariaDB 10.1+ |

Automatically strips MySQL/MariaDB terminal output: `| ... |` borders, `+---+` separators, SQL prompts (`-> SELECT`), `N rows in set` lines.

## Tech Stack

- Vue 3 + TypeScript
- Vite
- Tailwind CSS
- D3.js (tree visualization)
- CodeMirror 6 (input editor)
- Pako (URL compression)

## 100% Client-Side

All analysis runs in your browser. No backend, no API calls, no data sent anywhere. Your query plans never leave your machine.

## Getting Started

```bash
npm install
npm run dev       # Dev server at localhost:5173
npm run build     # Production build to dist/
```

## Testing

Tested against 20 real queries on 680K-row MySQL 8.0 and 530K-row MariaDB 10.11 databases. See the `tests/` directory for test queries, setup scripts, and comparison reports.

```bash
# Set up test database
mysql -u root < tests/setup-test-db.sql

# Run analysis tests
npx tsx tests/run-tests.ts
```

## Built By

[ReliaDB](https://reliadb.com) — PostgreSQL & MySQL DBA consulting for growing SaaS and tech companies.

## License

MIT
