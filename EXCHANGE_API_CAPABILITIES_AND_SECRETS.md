# Exchange API Capabilities And Secrets

Source of truth:

- Implementation repo: `tradingview-codex-onboarding-agent`
- Capability matrix: `tradingview-codex-onboarding-agent/docs/EXCHANGE_API_CAPABILITY_MATRIX.md`
- Machine-readable matrix: `tradingview-codex-onboarding-agent/config/exchange_api_capabilities.json`
- Demo verifier: `tradingview-codex-onboarding-agent/scripts/verify_exchange_demo_capabilities.py`
- Cloudflare sync: `tradingview-codex-onboarding-agent/scripts/sync_exchange_api_secrets_store.ps1`

Current policy:

- Bybit demo uses `https://api-demo.bybit.com`.
- Bybit live execution remains disabled until eligibility, Oracle regional node, and exact approval gate are wired.
- MEXC public data works locally/VPN and through the Frankfurt Worker.
- MEXC signed account proof is read-only first; do not enable withdrawal permission.
- MEXC demo/sandbox must be explicitly provided by account/base URL before signed demo tests run.

Secret handling:

- Real values live only in ignored `.env` files or Cloudflare Secrets Store.
- This repo may contain placeholders only.
- Do not commit API keys, API secrets, or passphrases.
