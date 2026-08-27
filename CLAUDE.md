# us-stocks-daily-report

> **Part of the [DappGo Stocks family](https://github.com/YanlongLai/dappgo-stocks-meta) (14 sibling repos).**
> If you've never worked on this family before, read `~/git/dappgo-stocks-meta/CLAUDE.md` first — it's the canonical entry point.

## Family quick links
- Repo map: `~/git/dappgo-stocks-meta/docs/REPO_MAP.md` — what each repo does
- Dependency flow: `~/git/dappgo-stocks-meta/docs/DEPENDENCY_FLOW.md` — when changing X, also touch Y
- Architecture: `~/git/dappgo-stocks-meta/docs/ARCHITECTURE.md` — data flow + diagrams
- Conventions: `~/git/dappgo-stocks-meta/docs/CONVENTIONS.md` — TS / Python / commit standards

## This repo's role
**Tier 2** | **Data-only / machine-emitted** | Public archive of generated artifacts for the DappGo US Stocks mobile app.

- **Inputs**: nightly engine push from `~/git/us-stocks-core` (Tier 1 Python engine).
- **Outputs**: jsDelivr CDN consumption by `~/git/dappgo-us-stocks-app` (Tier 3) and `~/git/dappgo-stocks-mcp` (Tier 4).
- **When to touch**: **NEVER manually**. Reports and `dashboard/data.json` are emitted by the engine. If you find yourself wanting to edit content here, you almost certainly want `~/git/us-stocks-core` instead.
- **Schema note**: `ticker_data.*.sec_valuation` is an optional SEC Company Facts evidence object. It may be absent or `unavailable`; consumers must preserve the existing quote/fundamentals fields and must not treat its scenario range as an SEC forecast.
- **Retention**: the engine publisher applies the 30-day base `DATA_RETENTION_DAYS` plus optional `REPORT_RETENTION_DAYS`, `AI_RETENTION_DAYS`, and `SNAPSHOT_RETENTION_DAYS` overrides before committing. It protects mutable aliases and current pointers; malformed or missing `latest.json` disables immutable-snapshot cleanup for safety.

## Sibling repos commonly edited together
- `~/git/us-stocks-core` — the engine producing the data in this repo.
- `~/git/dappgo-us-stocks-app` — the consumer mobile app.

## What this repo is

A read-mostly archive of **generated artifacts**:
- `reports/YYYY-MM-DD.md` — daily US stock analysis reports
- `dashboard/data.json` — aggregate payload consumed by the mobile app
- `dashboard/index.html` — static web viewer (GitHub Pages)

## What this repo is NOT

- **Not** a place for Python source code. The engine lives in `~/git/us-stocks-core`.
- **Not** for contributors modifying reports. They're auto-generated.

## Before committing

- **Never** add `.py` files — guarded by CI workflow
- **Never** modify files in `reports/` or `dashboard/data.json` by hand —
  next pipeline run overwrites them

The publisher also removes dated reports, AI results, and old immutable
dashboard snapshots past their configured TTL. Do not perform that cleanup by
hand in this data-only repository.

## Language

User's primary language is 繁體中文. Respond in 繁體中文 unless the user
writes in English. Code / docs in English.
