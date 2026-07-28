# Skill: nonbrand-bid-strategy-target-cpa-audit

## Purpose
Assesses whether a NonBrand campaign's Target CPA bid strategy is constraining
volume (target too tight) or permitting unprofitable spend (target too loose),
and recommends a specific adjusted target. Used exclusively by NonBrand Search
Optimizer Agent.

## 1. Data Retrieval
Pull, per campaign in scope over the run's `analysis_window`: bid strategy
type, current Target CPA value, cost, conversions, conversion_value, Search
Budget Lost Impression Share, Search Rank Lost Impression Share. Apply the
conversion-value fallback from `ads-performance-data-standards`.

## 2. Statistical Significance Gate
If the campaign recorded fewer than 30 conversions in the analysis window, do
NOT recommend a target change. Log the audit result as "insufficient
conversion volume for reliable Target CPA calibration — monitor next cycle"
instead of a recommendation.

## 3. Decision Tree
Compute actual CPA = Cost / Conversions.

- **Target Too Tight** (recommend RAISING Target CPA): Search Rank Lost
  Impression Share > 30% AND actual CPA ≤ 90% of Target CPA AND Search Budget
  Lost Impression Share < 10% (rules out budget as the constraint — the target
  itself is suppressing bids below competitive levels).
- **Target Too Loose** (recommend LOWERING Target CPA): actual CPA > 115% of
  Target CPA sustained across the full analysis window AND ROAS is below the
  account's average ROAS for comparable NonBrand campaigns (or below the
  client's stated minimum ROAS, if on file).
- **No Change:** neither condition met.

## 4. Change-Size Guardrail
Apply the same 20%-max-single-step-change and staged-rollout logic defined in
`pacing-math` Section 6 — Smart Bidding strategies re-enter a learning period
after large target swings, so raise/lower recommendations must be capped and
staged identically to budget changes.

## 5. Recommendation Format
- `campaign_name`, `current_target_cpa`, `actual_cpa`
- `recommended_target_cpa` (staged per the guardrail above if the full change
  exceeds 20%)
- `diagnostic_trigger`: "Target Too Tight" / "Target Too Loose"
- `reason`: the specific metrics that triggered the recommendation

## 6. Output Aggregation
One task per campaign — this audit does not require batching.
