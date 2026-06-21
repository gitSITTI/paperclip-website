# Bybit Frankfurt Demo Routing Lessons

Date: 2026-06-21
Source repo: `tradingview-codex-onboarding-agent`

## What Was Built

- Temporary Cloudflare Frankfurt Worker path for Bybit public market data and demo-account read-only checks.
- Protected `/signed-fetch` route for read-only signed Bybit GET requests only.
- Protected `/demo-signed-request` route for Bybit demo-only POST tests, restricted to `api-demo.bybit.com` and allowlisted demo endpoints.
- Demo BTCUSDT split TP/SL script:
  - TP1: `0.183 @ 57000`
  - TP2/final: `0.184 @ 47000`
  - paired stop losses initially at `64878.3`
- Local Windows Scheduled Task: `TwoHittz Bybit Demo TP1 BE Monitor`, running every minute to move the remaining stop after TP1 fills.

## What Was Learned

- After VPN removal, direct local laptop egress was AT&T Missouri, US, and direct Bybit public API calls returned HTTP `403`.
- The Frankfurt Cloudflare Worker path returned Bybit `retCode: 0` with `Cf-Placement: remote-FRA`, but `/whereami` still showed Cloudflare outbound as US. Treat this as a temporary data/control path, not proof of fixed regional exchange egress.
- Do not use the Cloudflare Worker for live execution. Oracle Frankfurt A1 remains the intended production regional node once Oracle capacity is available.
- Bybit demo RFQ endpoints returned `10032 Demo trading are not supported`.
- Bybit spread open orders/trade history returned empty lists; spread order history returned `10016 System error. Please try again later`.

## Corrected BE Formula

The first BE formula was defective because it included already-incurred fees but not the pending close fee.

For the short BTCUSDT demo position, the corrected fee-inclusive stop solves:

```text
stop = (entry + be_plus + abs(curRealisedPnl) / size) / (1 - close_fee_rate)
```

Observed inputs:

```text
entry = 64366.6
size = 0.367
abs(curRealisedPnl) = 12.99239821
be_plus = 5
close_fee_rate = 0.00055
corrected_stop = 64442.44
```

The previous `64407.00` stop is not sufficient because it omits the estimated pending taker fee to close the remaining/full stop quantity.

## Safety Rules

- Demo mutation only unless explicitly reworked and approved for eligible live accounts.
- Keep all Bybit secrets local or in approved secret stores; do not commit `.env` or tokens.
- Read-only first for any live key.
- Live execution must stay blocked until the regional Oracle node is actually provisioned and health-checked.
