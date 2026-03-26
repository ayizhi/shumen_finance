# Shumen Finance

Shumen Finance (`shumen_finance`) is a deterministic mainland China A-share skill for:

- stock snapshots
- finance-analysis context
- unusual movement analysis
- sector and theme analysis
- market news lookup

## Runtime Model

- Local runtime: Node.js (`.mjs` scripts)
- Outbound API: `https://tianshan-api.kungfu-trader.com`
- Package model: bundled scripts plus prompt/reference assets

The skill is designed as a thin local wrapper around a fixed upstream API.
It does not download extra code at runtime.
It does not write local output files during normal execution.
It does not use arbitrary user-supplied base URLs.

## Data Handling

This skill sends user requests and locally assembled analysis context to the upstream Tianshan API.

Do not use it with:

- passwords
- API keys
- private documents
- personal data
- non-public trading positions

Use non-sensitive public market queries only unless you trust the upstream operator and data handling policy.

## Scope

Supported:

- Mainland China A-shares
- single-stock analysis
- single-sector or single-theme analysis
- deterministic data lookup

Not supported:

- US stocks
- Hong Kong stocks
- crypto
- futures
- forex
- whole-market screening

## Trust Notes

- Source repository: `https://github.com/ayizhi/shumen_finance`
- Fixed upstream endpoint: `https://tianshan-api.kungfu-trader.com`
- Required runtime: Node.js 22.24.0+

If you are reviewing this skill for installation or registry approval, inspect:

- `scripts/core/runtime.mjs`
- `scripts/core/http_client.mjs`
- `scripts/flows/run_data_request.mjs`
- `scripts/flows/run_movement_analysis.mjs`

Those files define the fixed network surface and the main execution path.
