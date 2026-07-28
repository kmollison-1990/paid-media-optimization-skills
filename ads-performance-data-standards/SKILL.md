---
name: ads-performance-data-standards
description: Ensures all Google Ads performance data is pulled, calculated, and ranked consistently across every agent that touches Ads metrics. Use whenever an agent queries Google Ads performance data, calculates CPA/ROAS, chooses a lookback window, or ranks/compares campaign performance.
---

# Ads Performance Data Standards

## Purpose
Ensures all Google Ads performance data is pulled, calculated, and ranked consistently across every agent that touches Ads metrics.

## 1. Conversion Value API Workaround (mandatory)
Google Ads API has a known bug where standard conversion value fields occasionally report 0 due to attribution anomalies.

- Always retrieve **both**:
  - `metrics.current_model_attributed_conversions_value`
  - `metrics.conversions_value_by_conversion_date`
- **Primary metric:** `current_model_attributed_conversions_value`
- **Fallback** (if null/missing): `conversions_value_by_conversion_date`
- Reject any output from another agent that did not apply this fallback.

## 2. Historical Lookback Window
- Structural/performance audits (keywords, ad copy, asset groups, feed) **MUST** use a trailing 30-day or 60-day window — never 14-day.
- The only exception is **Pacing**, which uses trailing 14-day data (see `pacing-math`).
- Always state the exact **Analysis Date Range** used in every diagnostic output.

## 3. Calculated KPI Formulas
- `CPA = Cost / Conversions` (null if Conversions = 0)
- `ROAS = Conversion Value / Cost` (using the primary value metric above; null if Cost = 0)

## 4. Performance Ranking Hierarchy
Always rank/present metrics in this exact order:
1. **ROAS** — higher is better
2. **CPA** — lower is better (tiebreak)
3. **Conversion Value** — higher is better (tiebreak)
4. **Conversion Volume** — higher is better (tiebreak)
