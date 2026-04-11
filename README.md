<div align="center">

# MySQL & MariaDB EXPLAIN Analyzer

**Free, browser-based query plan visualizer with automatic issue detection and index recommendations.**

[![Live Demo](https://img.shields.io/badge/Live%20Demo-reliadb.com-2980B9?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0iI2ZmZiIgZD0iTTEyIDJDNi40OCAyIDIgNi40OCAyIDEyczQuNDggMTAgMTAgMTAgMTAtNC40OCAxMC0xMFMxNy41MiAyIDEyIDJ6bS0xIDE3LjkzYy0zLjk1LS40OS03LTMuODUtNy03LjkzIDAtLjYyLjA4LTEuMjEuMjEtMS43OUw5IDE1djFjMCAxLjEuOSAyIDIgMnYxLjkzem02LjktMi41NGMtLjI2LS44MS0xLTEuMzktMS45LTEuMzloLTF2LTNjMC0uNTUtLjQ1LTEtMS0xSDh2LTJIMTB2LTJIMTJ2LTJoMmMuNTUgMCAxLS40NSAxLTFWNy41YzIuNSAxLjcxIDQuNDYgNC44NyA0Ljk1IDguNTFIMTd2MWMwIDEuMS0uOSAyLTIgMnoiLz48L3N2Zz4=)](https://reliadb.com/tools/explain/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/Mughees52/mysql-explain-analyzer?style=for-the-badge&color=E67E22)](https://github.com/Mughees52/mysql-explain-analyzer/stargazers)
[![Vue 3](https://img.shields.io/badge/Vue-3-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)](https://vuejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)

[Live Demo](https://reliadb.com/tools/explain/) | [Blog Post](https://reliadb.com/blog/mysql-explain-analyzer-free-query-plan-visualizer.html) | [Report Bug](https://github.com/Mughees52/mysql-explain-analyzer/issues)

</div>

---

## Why This Tool?

The PostgreSQL community has [explain.dalibo.com](https://explain.dalibo.com) and [PEV2](https://github.com/dalibo/pev2). MySQL had nothing comparable — until now.

MySQL's raw EXPLAIN output tells you everything about query performance, but reading nested tree output or JSON plans isn't intuitive. This tool does the reading for you: paste your EXPLAIN output, get a visual tree with automatic issue detection, smart index recommendations, and executable query rewrites.

**100% client-side** — your query plans never leave your browser. No backend, no API calls, no account required.

## Features

### Analysis Engine
| Feature | Details |
|---------|---------|
| Detection Rules | 49 rules (8 critical, 20 warning, 5 info, 7 good, 4 MariaDB-specific, 5 MySQL 8.0+) |
| Index Advisor | Query-aware, resolves table aliases, suggests composite covering indexes, PK-aware |
| Query Rewrites | 7 patterns: YEAR()→range, subquery→JOIN, NOT IN→LEFT JOIN, OFFSET→keyset, and more |
| Impact Simulator | Predicts structural plan changes per index (access type, row reduction, covering scan) |
| Scoring | Weighted 0-100 score with severity breakdown |

### Visualization
- **Interactive tree** — edge thickness = row count, color-coded node badges (Slow/Costly/Bad Estimate/Filter)
- **Highlight modes** — switch between duration, rows, and cost heat maps
- **4 view tabs** — Tree, Table, Cost Chart, Estimate vs Actual
- **Before/After** — compare two plans side by side

### Supported Formats

| Format | Engine | Example |
|--------|--------|---------|
| `EXPLAIN ANALYZE` (tree) | MySQL 8.0+ | `-> Nested loop inner join (cost=7.45 rows=16) (actual time=0.08..0.16 rows=16 loops=1)` |
| `EXPLAIN FORMAT=JSON` | MySQL / MariaDB | `{ "query_block": { "select_id": 1, ... } }` |
| Traditional table | MySQL / MariaDB | `+----+------+-------+------+------+-------+` |
| `ANALYZE` table | MariaDB 10.1+ | Includes `r_rows` and `r_filtered` columns |
| `ANALYZE FORMAT=JSON` | MariaDB 10.1+ | Includes `r_total_time_ms`, filesort nesting |

Paste directly from your terminal — the tool auto-strips `mysql>` prompts, `| ... |` borders, `+---+` separators, SQL continuation lines (`-> SELECT`, `-> GROUP BY`), and `N rows in set` footers.

## Quick Start

### Use the hosted version (recommended)

**[reliadb.com/tools/explain/](https://reliadb.com/tools/explain/)** — zero setup, always up to date.

### Run locally

```bash
git clone https://github.com/Mughees52/mysql-explain-analyzer.git
cd mysql-explain-analyzer
npm install
npm run dev       # Dev server at localhost:5173
```

### Build for production

```bash
npm run build     # Output in dist/
npm run preview   # Preview the build
```

## How to Use

1. **Run EXPLAIN ANALYZE** on your slow query:
   ```sql
   EXPLAIN ANALYZE SELECT * FROM orders WHERE status = 'pending';
   ```

2. **Paste the output** into the EXPLAIN Output box — paste it exactly as your terminal prints it, borders and all.

3. **Add your SQL query** (optional) in the "SQL Query" tab — unlocks query rewrite suggestions.

4. **Add table DDL** (optional) in the "Table Schema" tab — unlocks composite index recommendations and schema analysis.

## What It Catches

```
Full table scan on `orders` (50,529 rows)
├── Critical: No index on WHERE columns
├── Recommendation: ALTER TABLE orders ADD INDEX idx_status (status);
└── Impact: Full table scan (ALL) → Index lookup (ref)

Filesort on 10,000 rows
├── Warning: ORDER BY not covered by index
├── Recommendation: ALTER TABLE orders ADD INDEX idx_status_amount (status, total_amount);
└── Impact: Eliminates filesort, reads in index order

Dependent subquery (executes once per outer row)
├── Critical: O(n*m) complexity
├── Rewrite: Convert to JOIN
└── Generated SQL provided
```

## Detection Rules Summary

| Severity | Count | Examples |
|----------|-------|---------|
| **Critical** | 8 | Full table scan (large), filesort >1K rows, temp table, Cartesian join, dependent subquery, unindexed nested loop, massive row mismatch |
| **Warning** | 20 | Row estimate mismatch >10x, no index available, full index scan, high loop count, join buffer, backward scan |
| **Info** | 5 | Small table scan, ICP used, subquery materialized, hash join, covering index |
| **Good** | 7 | Optimal eq_ref, covering index, efficient range scan, PK lookup |
| **MariaDB** | 4 | Rowid filter, FirstMatch/LooseScan semi-join, hash join, low r_filtered |
| **MySQL 8.0+** | 5 | Hash join, parallel scan, skip scan, index merge, antijoin |

## Index Advisor Intelligence

Not a simple "column in WHERE → add index" tool. The advisor:

- **Merges cross-clause columns** — WHERE + JOIN + GROUP BY + ORDER BY + aggregates combined into one optimal composite index
- **PK-aware** — never suggests indexes starting with primary key columns
- **DDL-validated** — confirms recommended columns actually exist in the table schema
- **Deduplicates** — removes subset indexes in favor of wider covering indexes
- **Existing index aware** — skips recommendations that duplicate existing indexes

## Sharing & Embedding

- **Share Link** — generates a URL with the plan compressed in the hash. Anyone with the link sees your analysis.
- **Embed** — generates an `<iframe>` snippet for blog posts, runbooks, or internal docs.

## Tech Stack

| Technology | Purpose |
|-----------|---------|
| [Vue 3](https://vuejs.org/) | UI framework |
| [TypeScript](https://www.typescriptlang.org/) | Type-safe analysis engine |
| [Vite](https://vitejs.dev/) | Build tool |
| [Tailwind CSS](https://tailwindcss.com/) | Styling |
| [D3.js](https://d3js.org/) | Tree visualization |
| [CodeMirror 6](https://codemirror.net/) | SQL input editor |
| [Pako](https://github.com/nicjansma/pako) | URL compression for sharing |

## Testing

Tested against 50 real queries on a 680K-row MySQL 8.0.45 database (100K orders, 50K users, 250K order items, 80K reviews) and a 530K-row MariaDB 10.11.14 database.

```bash
# Set up test database
mysql -u root < tests/setup-test-db.sql

# Run analysis tests (50 queries)
npx tsx tests/run-tests.ts
```

### Test Results

| Metric | Result |
|--------|--------|
| Queries tested | 50 |
| Issues detected | 102 |
| Index recommendations | 42 |
| Impact simulations | 23 |
| Query hints | 40 |
| Query rewrites | 8 |
| Average score | 86/100 |
| Errors | 0 |

Compared against independent senior DBA analysis — matches on 90% of findings. Full comparison in `tests/ai-comparison.md`.

## Contributing

Issues and pull requests are welcome. Please open an issue first to discuss what you'd like to change.

## Related

- [explain.dalibo.com](https://explain.dalibo.com) — PostgreSQL EXPLAIN visualizer
- [PEV2](https://github.com/dalibo/pev2) — PostgreSQL EXPLAIN visualizer (Vue)
- [awesome-mysql](https://github.com/shlomi-noach/awesome-mysql) — Curated MySQL tools list

## Built By

[**ReliaDB**](https://reliadb.com) — PostgreSQL & MySQL DBA consulting for growing SaaS and tech companies.

Based in Valencia, Spain. Supporting teams globally.

## License

[MIT](LICENSE)
