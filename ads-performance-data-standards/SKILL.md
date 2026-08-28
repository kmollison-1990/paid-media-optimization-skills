---
name: ads-performance-data-standards
description: Ensures all Google Ads performance data is pulled, calculated, and ranked consistently across every agent that touches Ads metrics. Use whenever an agent queries Google Ads performance data, calculates CPA/ROAS, chooses a lookback window, or ranks/compares campaign performance.
---

# Ads Performance Data Standards

## Purpose
Ensures all Google Ads performance data is pulled, calculated, and ranked consistently across every agent that touches Ads metrics.

## 1. Conversion Value API Workaround (mandatory)
Google Ads API has a known bug where standard conversion value fields occasionally report 0 due to attribution anomalies.

- **Primary check:** Retrieve the blended `metrics.conversions_value` field first. If it returns non-null, non-zero data, use it directly as the Conversion Value — no further fields need to be pulled.
- **Fallback (only if the blended field is null or reports 0):** Retrieve both:
  - `metrics.current_model_attributed_conversions_value`
  - `metrics.conversions_value_by_conversion_date`
  - Of these two, primary metric: `current_model_attributed_conversions_value`; fallback (if null/missing): `conversions_value_by_conversion_date`.
- State explicitly in every diagnostic whether the blended field was used directly, or whether the dual-field fallback was invoked (and which of the two fallback fields supplied the value).
- Reject any output from another agent that reports a zero Conversion Value without confirming the blended field was actually null/zero (i.e. without ruling out that it simply wasn't checked).

## 2. Historical Lookback Window
- Structural/performance audits (keywords, ad copy, asset groups, feed) **MUST** use a trailing 30-day or 60-day window — never 14-day.
- The only exception is **Pacing**, which uses trailing 14-day data (see `pacing-math`).
- Always state the exact **Analysis Date Range** used in every diagnostic output.

## 3. Calculated KPI Formulas
- `CPA = Cost / Conversions` (null if Conversions = 0)
- `ROAS = Conversion Value / Cost` (using the value metric resolved in Section 1; null if Cost = 0)

## 4. Performance Ranking Hierarchy
Always rank/present metrics in this exact order:
1. **ROAS** — higher is better
2. **CPA** — lower is better (tiebreak)
3. **Conversion Value** — higher is better (tiebreak)
4. **Conversion Volume** — higher is better (tiebreak)
