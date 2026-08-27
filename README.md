# US Stocks Daily Report — Data

> Auto-generated daily reports + structured JSON for the **DappGo US Stocks** mobile app.

This repo contains only the **published outputs** of the analysis pipeline.
The engine itself is proprietary and lives in a separate private repo.

## Contents

```
reports/YYYY-MM-DD.md          每日美股策略報告 (markdown)
dashboard/data.json            App 與 dashboard 共用的結構化資料
dashboard/index.html           簡易公開瀏覽介面 (GitHub Pages)
schemas/                       JSON Schema 定義
```

## 資料來源

- 股價、新聞、財報日期、機構持股：[yfinance](https://github.com/ranaroussi/yfinance) (Yahoo Finance, free)
- 補充新聞：[Finnhub free tier](https://finnhub.io/)
- 重大公告與估值證據：[SEC EDGAR](https://www.sec.gov/edgar) 8-K / 10-Q / 10-K filings；`dashboard/ticker_data.*.sec_valuation` 為可選的 filed Company Facts 與明確標示的情境估值
- 內部人交易：SEC Form 4
- AI 解讀：Google Gemini

## 涵蓋標的

50 檔，跨 11 GICS 板塊 + ETF（config-driven，未來可 PR `tickers.yaml` 擴充）：

| 板塊 | 代表股 |
|------|------|
| Technology | AAPL, MSFT, NVDA, AVGO, ORCL |
| Communication | GOOGL, META, NFLX, T, VZ |
| Consumer Discretionary | AMZN, TSLA, HD, MCD, NKE |
| Consumer Staples | WMT, PG, KO, COST |
| Financials | JPM, V, MA, BAC, WFC |
| Healthcare | UNH, JNJ, LLY, PFE, ABBV |
| Industrials | CAT, BA, HON, UPS |
| Energy | XOM, CVX |
| Utilities | NEE, DUK |
| Materials | LIN, FCX |
| Real Estate | AMT, PLD |
| ETF | SPY, QQQ, DIA, IWM |

## 排程

每個美股交易日由 Cloud Run Job `dappgo-us-stocks` 產生；GitHub Actions
只保留為需明確設定 `run_fallback=true` 的緊急 fallback。

## CDN 取用方式

App 透過 jsDelivr 公開 CDN 取資料：

```
https://cdn.jsdelivr.net/gh/YanlongLai/us-stocks-daily-report@main/dashboard/data.json
```

## 保留期限

發布引擎在每次提交前會依 TTL 清理舊輸出：`DATA_RETENTION_DAYS` 為 30
天基準，並可用 `REPORT_RETENTION_DAYS`、`AI_RETENTION_DAYS`、
`SNAPSHOT_RETENTION_DAYS` 分別管理報告、AI 結果與 immutable snapshot
（各允許 7–3650 天）。`dashboard/data.json`、`latest.json` 及其指向的
snapshot 永遠保留；若 `latest.json` 損壞或遺失，清理程序會暫停刪除
immutable snapshot，避免誤刪目前資料。請不要直接刪除本資料 repo 的生成檔案。

## 三 repo 協同

```
┌─ us-stocks-core (private) ──────────┐
│  Python 引擎 — Cloud Run Job        │
│  推送輸出 →                         │
└──────────────┬──────────────────────┘
               │
┌──────────────▼──────────────────────┐
│  us-stocks-daily-report (本 repo)   │
│  reports/*.md, dashboard/data.json, │
│  schemas/data.schema.json,          │
│  dashboard/index.html (GH Pages)    │
└──────────────┬──────────────────────┘
               │ jsDelivr CDN
┌──────────────▼──────────────────────┐
│  dappgo-us-stocks-app (private)     │
│  React Native + Expo 行動 App       │
└─────────────────────────────────────┘
```

| Repo | Visibility | 用途 |
|---|---|---|
| [us-stocks-core](https://github.com/YanlongLai/us-stocks-core) | private | 引擎 — 每日跑分析 |
| **us-stocks-daily-report** (本 repo) | public | 已發布報告 + JSON + 公開 viewer |
| [dappgo-us-stocks-app](https://github.com/YanlongLai/dappgo-us-stocks-app) | private | iOS/iPadOS/macOS 行動 App |

姐妹產品：
- **DappGo TW Stocks** ([tw-stocks-core](https://github.com/YanlongLai/tw-stocks-core) · [tw-stocks-daily-report](https://github.com/YanlongLai/tw-stocks-daily-report) · [dappgo-tw-stocks-app](https://github.com/YanlongLai/dappgo-tw-stocks-app))
- **DappGo Options** ([options-core](https://github.com/YanlongLai/options-core) · [options-daily-report](https://github.com/YanlongLai/options-daily-report) · [dappgo-options-app](https://github.com/YanlongLai/dappgo-options-app))

> **Schema 變動**請先在本 repo 更新 `schemas/data.schema.json`（CI 會擋住格式錯誤的 push），再依序部署 engine、app。`sec_valuation` 是可選欄位；缺少或 unavailable 時，舊版 app 必須照常解析其他欄位。

## 授權

報告內容採 [CC BY-NC 4.0](LICENSE) — 個人、研究、教學免費使用，不得商業利用未經授權。
