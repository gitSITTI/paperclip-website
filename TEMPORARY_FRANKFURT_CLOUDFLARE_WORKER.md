# Temporary Frankfurt Cloudflare Market-Data Worker

Status: active temporary read-only Worker while the Oracle Frankfurt A1 VM is pending.

Source of truth:

- Repo: `tradingview-codex-onboarding-agent`
- Node id: `cloudflare-frankfurt-market-data`
- Worker URL: `https://twohittz-edge-computer-frankfurt.edgar-sosa553.workers.dev`
- Main config: `tradingview-codex-onboarding-agent/config/regional_nodes.json`
- Test evidence: `tradingview-codex-onboarding-agent/infra/cloudflare-edge-computer/TEST_RESULTS_2026-06-21.md`

Allowed use:

- Public read-only market-data pulls for Bybit and MEXC.
- Development and smoke tests for the Frankie Candles / strategy pipeline.
- Temporary integration work until Oracle Frankfurt is online.

Do not use it for:

- live order execution;
- authenticated exchange account calls;
- customer-specific account actions;
- proof of fixed Frankfurt or UAE egress.

Known behavior verified on 2026-06-21:

- Bybit direct from the local laptop returned `403` with CloudFront country-block text.
- Bybit public reads through the Frankfurt Worker succeeded for public time/ticker smoke checks.
- Bybit BTCUSDT `linear` 1h OHLCV through `cloudflare-frankfurt-market-data` wrote 25 bars.
- Bybit `spot` kline/ticker still returned `403`; use `category=linear` for the current BTCUSDT levels workflow unless spot is explicitly required.
- MEXC BTCUSDT 1h OHLCV through `cloudflare-frankfurt-market-data` wrote 24 bars.
- Router split is intentional: market data can route to `cloudflare-frankfurt-market-data`, but live execution still routes to `oracle-frankfurt-a1`.

Canonical command from `tradingview-codex-onboarding-agent`:

```powershell
python scripts\fetch_market_data.py --venue bybit --symbol BTCUSDT --interval 1h --category linear --start 2026-06-20 --end 2026-06-21 --regional-node-id cloudflare-frankfurt-market-data --json-out data\market_data\bybit_btcusdt_linear_1h.json
```

When Oracle Frankfurt is online, update the source routing config first, then refresh or remove this notice.
