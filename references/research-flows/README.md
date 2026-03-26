# Research Flows

This directory contains two documented A-share research methodologies that have been migrated from the research backend:

- `stock-analysis/`: single-stock institutional research flow for one mainland China A-share instrument
- `sector-analysis/`: industry / sector / concept / theme research flow for one mainland China A-share sector

These are methodology documents, not executable skill entrypoints yet.

Use them for three things:

1. Preserve the full step-by-step research method without dropping any module.
2. Make the stock-vs-sector boundary explicit.
3. Show which query capabilities already exist in the current skill project and which still need another project to expose.

## Boundary

- `stock-analysis` is for one concrete A-share stock.
- `sector-analysis` is for one A-share industry, concept, theme, or sector.
- Neither flow applies to US stocks, Hong Kong stocks, cryptocurrencies, futures, or forex.

## Source Of Truth

The migrated step modules came from:

- `serverless-tianshan-api/src/handlers/analyse_research/skills/stock_analysis_v2/modules`
- `serverless-tianshan-api/src/handlers/analyse_research/skills/sector_analysis/modules`

The prompt-layer responsibilities came from:

- `serverless-tianshan-api/src/handlers/analyse_research/prompt/skill_stock_prompts.py`
- `serverless-tianshan-api/src/handlers/analyse_research/prompt/skill_sector_prompts.py`
- `serverless-tianshan-api/src/handlers/analyse_research/prompt/skill_debate_prompts.py`
