# Skill: pmax-listing-group-overlap-audit

## Purpose
Identifies underperforming or non-converting product listing groups within
retail-integrated PMax campaigns and recommends structural segmentation or
exclusion. Used exclusively by PMax Optimizer Agent.

## 1. Data Retrieval
Pull listing group / product partition-level performance for retail PMax
campaigns in scope, over the run's `analysis_window`: listing_group_id (or
product partition identifier), cost, impressions, clicks, conversions,
conversion_value. Apply the conversion-value fallback from
`ads-performance-data-standards`.

## 2. Decision Criteria (explicit — replaces the undefined "underperforming")
Flag a listing group for exclusion if EITHER:
- It meets the same Zero-Conversion Waste or Sub-Threshold ROAS thresholds
  defined in `shopping-product-id-pruning-audit` Section 2 (reused as-is, not
  redefined, to keep retail-asset-group logic consistent with standalone
  Shopping campaign logic for the same client), OR
- **Budget Concentration Risk:** the listing group's spend share exceeds 40%
  of its asset group's total spend while contributing less than 10% of the
  asset group's total conversion value — a PMax-specific risk since blended
  budget pooling lets one bad listing group starve better-performing groups.

## 3. Recommendation Format
- `listing_group_id`, `defining_attribute`, `spend`, `conversions_or_roas`
- `threshold_triggered`: reuse Product ID Pruning categories, or "Budget
  Concentration Risk"
- `recommended_action`: "Exclude" / "Segment into own asset group"
- `reason`

## 4. Output Aggregation
Apply the standard batching logic from `search-term-negative-keyword-workflow`
Section 5 if more than 25 listing groups are flagged in a single run.
