---
name: lushan_finance
description: Use when the user asks 庐山院财经 (Lushan Finance) for deterministic China A-share / A股 single-stock data, single-sector data, movement tracking, finance report status, finance basic indicators, valuation detail, profit forecast, industry rank, concept tags, money flow, sector candidate lookup, sector constituents, sector performance, finance-analysis context, unusual-movement context, price snapshots, K-line bar series, finance context, holder changes, chip distribution, resistance levels, or important market news.
user-invocable: true
metadata: {"openclaw":{"skillKey":"lushan_finance","requires":{"bins":["node"]}}}
---

# 庐山院财经 | Lushan Finance

This skill powers 庐山院财经 (Lushan Finance) as a deterministic data-and-analysis wrapper around the Tianshan APIs.
It is only for mainland China A-shares.
It always calls `https://tianshan-api.kungfu-trader.com`.

Use it as an operation manual.
Do not invent APIs, hidden parameters, or unsupported market scopes.

## Core Rules

- China A-share only
- Single stock or single sector only
- No whole-market screening
- Not for US stocks
- Not for Hong Kong stocks
- Not for cryptocurrencies
- Not for futures or forex
- No free-form SQL
- Always use the fixed product names below
- Only use the public parameter contract documented here
- Do not invent extra parameters
- Do not ask for or pass a base URL
- Do not ask for or pass any API key or token
- Do not expose hidden server parameters such as `start_date`, `end_date`, `is_realtime`, `limit`, `days`, or `periods`
- Deterministic HTTP fetch first, analysis later

## Routing Rules

Pick exactly one product or flow based on the user's intent.

If the user asks about non-A-share assets, do not use this skill.

- User wants code or exchange resolution:
  Use `instrument_profile`
- User wants current price status, latest涨跌, or a compact period-change summary:
  Use `price_snapshot`
- User wants recent N-day K-line data or a raw bar sequence:
  Use `bar_series`
- User wants broad finance or valuation context:
  Use `finance_context`
- User wants the prompt-ready backend bundle for finance report analysis:
  Use `finance_analysis_context`
- User wants report publication readiness or a recent report timeline:
  Use `finance_report_status`
- User wants structured quarterly financial indicators:
  Use `finance_basic_indicators`
- User wants detailed valuation percentiles or model-based valuation context:
  Use `finance_valuation_detail`
- User wants analyst profit forecast, target-price range, or recent coverage context:
  Use `finance_profit_forecast`
- User wants industry percentile / rank context for one stock:
  Use `finance_industry_rank`
- User wants top-holder changes:
  Use `holder_context`
- User wants chip distribution / cost-distribution data:
  Use `chip_distribution`
- User wants resistance or key price levels:
  Use `price_levels`
- User wants concept tags for one stock:
  Use `instrument_concepts`
- User wants recent money flow for one stock:
  Use `money_flow`
- User wants the deterministic no-image context bundle for unusual movement analysis:
  Use `unusual_movement_context`
- User gives a fuzzy or ambiguous sector / concept / theme name and you need one best resolved match:
  Use `resolve_sector`
- User gives an inexact sector / concept / theme name and needs deterministic candidates:
  Use `similar_sectors`
- User wants one resolved sector's constituents:
  Use `sector_constituents`
- User wants one resolved sector's performance summary:
  Use `sector_performance`
- User wants important daily news batches:
  Use `important_news`
- User wants intraday high-frequency news around a date/time:
  Use `high_frequency_news`
- User wants unusual movement analysis:
  Use `movement_analysis`
- User wants deep research on one concrete A-share stock:
  Consult `references/research-flows/stock-analysis/` as the methodology source
- User wants deep research on one A-share industry / concept / theme / sector:
  Consult `references/research-flows/sector-analysis/` as the methodology source

## Public Products And Flows

- `instrument_profile`
- `price_snapshot`
- `bar_series`
- `finance_context`
- `finance_analysis_context`
- `finance_report_status`
- `finance_basic_indicators`
- `finance_valuation_detail`
- `finance_profit_forecast`
- `finance_industry_rank`
- `holder_context`
- `chip_distribution`
- `price_levels`
- `instrument_concepts`
- `money_flow`
- `unusual_movement_context`
- `resolve_sector`
- `similar_sectors`
- `sector_constituents`
- `sector_performance`
- `important_news`
- `high_frequency_news`
- `movement_analysis`

## Documented Research Methodologies

These are documentation-only flows stored under `references/research-flows/`.
They are not public executable CLI flows yet.

- `stock-analysis`
  Use as the methodology reference when the user asks for deep research on one concrete mainland China A-share stock.
- `sector-analysis`
  Use as the methodology reference when the user asks for research on one mainland China A-share industry, concept, theme, or sector.

The distinction is strict:

- one stock -> `stock-analysis`
- one industry / concept / sector / theme -> `sector-analysis`

If the user asks for whole-market screening, neither documented methodology applies.

For sector requests, prefer direct `sector_name` first.
Treat `sector_id` as an internal identifier that should only be reused after the backend has already returned it.
Only call `resolve_sector` when the sector name is fuzzy, ambiguous, or direct sector lookup fails.

## Public Input Contract

### Instrument Mode

For single-stock products, use exactly one mode:

- `--instrument-name`
- `--instrument-id` and `--exchange-id`

Never mix both modes in one request.
If the stock code is unknown, prefer `--instrument-name`.
When using code mode, `--exchange-id` must be `SSE` or `SZE`.

### Stock Date Mode

For stock products and flows, use:

- `--target-date`
- optional `--visual-days-len`

### Sector Query Mode

For sector resolving or candidate lookup, use:

- `--query`

For resolved sector products, use exactly one:

- `--sector-name`
- `--sector-id`

### News Date Mode

For high-frequency news, use:

- `--target-date`
- optional `--target-time`

### Hidden Parameters

Do not pass these from the model side:

- `start_date`
- `end_date`
- `is_realtime`
- `limit`
- `days`
- `periods`

They are intentionally hidden to keep the request surface controlled.

## Standard Commands

### Direct Stock Data Request

```bash
node scripts/flows/run_data_request.mjs --product price_snapshot --instrument-name 贵州茅台 --target-date 20260301
```

### Direct Sector Data Request

```bash
node scripts/flows/run_data_request.mjs --product sector_performance --sector-name 算力 --target-date 20260301
```

### Resolve Sector Name

```bash
node scripts/flows/run_data_request.mjs --product resolve_sector --query 算力
```

### Movement Analysis

```bash
node scripts/flows/run_movement_analysis.mjs --instrument-name 贵州茅台 --target-date 20260301 --visual-days-len 100
```

## Product Usage Guide

### `instrument_profile`

Use when:

- The user gives only a stock name
- You need to normalize a stock into `instrument_id + exchange_id`

### `price_snapshot`

Use when:

- The user asks for current price status
- The user asks how much the stock moved from a target date to now
- The user needs a compact price summary, not a full K-line window

Do not use when:

- The user wants a full K-line sequence
- The user wants technical analysis over many bars

### `bar_series`

Use when:

- The user asks for recent N-day K-lines
- The user wants raw bar data for technical analysis
- The user wants a fixed historical window

### `finance_context`

Use when:

- The user wants broad financial data, valuation context, dividend context, or report-related context

### `finance_analysis_context`

Use when:

- The user wants the deterministic backend bundle for finance-report analysis
- A finance-analysis prompt needs prompt-ready `financial_data_text`

### `finance_report_status`

Use when:

- The user asks whether the latest full report is published
- The user needs a recent report timeline

### `finance_basic_indicators`

Use when:

- The user wants structured quarterly indicators
- The user needs single-quarter growth, profitability, or basic Dupont blocks

### `finance_valuation_detail`

Use when:

- The user wants detailed valuation percentiles, Graham / DCF / Dupont context, or valuation tips

### `finance_profit_forecast`

Use when:

- The user wants analyst EPS forecast, target-price range, or latest coverage context

### `finance_industry_rank`

Use when:

- The user wants industry percentile context for one stock
- The user wants dominance, health, or growth scoring inside the stock's industry

### `holder_context`

Use when:

- The user asks about recent holder changes or top-holder comparison

### `chip_distribution`

Use when:

- The user asks where chips are concentrated by distribution
- The user wants chip peaks or cost-distribution structure

Important default:

- Server-side default window is the chip-peak path, built around roughly `120` bars unless explicitly overridden

### `price_levels`

Use when:

- The user asks about resistance levels, key levels, or whether price is near a breakout level

Important default:

- This comes from `chip-concentration`
- Server-side default detection window is roughly `250` trading days unless overridden

### `instrument_concepts`

Use when:

- The user asks which概念板块 a stock belongs to

### `money_flow`

Use when:

- The user asks for recent主力资金流向 or money-flow summary for one stock

### `unusual_movement_context`

Use when:

- The user wants the deterministic context bundle behind unusual movement analysis
- You need recent trading days, recent prices, long-window highs/lows, support/resistance, and market technical status without building the final prompt package yet

### `similar_sectors`

Use when:

- The user gives a fuzzy sector / concept / theme name and you need deterministic candidates first
- The user does not know the internal `sector_id`

### `resolve_sector`

Use when:

- The user gives a fuzzy or ambiguous sector / concept / theme name and direct `sector_name` lookup is not reliable
- You want one best resolved match after a name-based lookup failed

### `sector_constituents`

Use when:

- The user wants one sector's constituents
- The user wants representative stocks inside one resolved sector

Input rule:

- Prefer `sector_name`
- Only use `sector_id` when it was already returned by a previous backend call such as `resolve_sector` or `similar_sectors`

### `sector_performance`

Use when:

- The user wants one sector's aggregated performance, valuation median, money-flow summary, or leader summary

Input rule:

- Prefer `sector_name`
- Only use `sector_id` when it was already returned by a previous backend call such as `resolve_sector` or `similar_sectors`

### `important_news`

Use when:

- The user wants important news batches for a trading date

### `high_frequency_news`

Use when:

- The user wants minute-level high-frequency news around a date/time

### `movement_analysis`

Use when:

- The user wants the final unusual-movement prompt package, not just its context bundle
