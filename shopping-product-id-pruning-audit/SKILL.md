# Skill: shopping-product-id-pruning-audit

## Purpose
Identifies individual product IDs generating significant spend with zero
conversions or sub-threshold ROAS, and recommends exclusion or pause. Used
exclusively by Shopping Ads Optimizer Agent.

## 1. Data Retrieval
Pull product-level performance (`shopping_performance_view`) for all products
advertised in scope, over the run's `analysis_window`: product_id,
product_title, campaign_id, ad_group_id, cost, impressions, clicks,
conversions, conversion_value. Apply the conversion-value fallback from
`ads-performance-data-standards`. Set the retrieval limit to the tool's maximum
per call and paginate until fully retrieved — product catalogs can run into
the thousands and must not be silently truncated.

## 2. Profitability Threshold (explicit — replaces the undefined
"client's profitability threshold")
A product ID is eligible for pruning if EITHER:
- **(a) Zero-Conversion Waste:** zero conversions AND cost ≥ 3x the campaign's
  average CPA (if the campaign has enough conversions to compute one
  reliably), OR cost ≥ $50 flat floor if the campaign itself lacks a reliable
  average CPA.
- **(b) Sub-Threshold ROAS:** has conversions, but computed ROAS is less than
  50% of the account's average ROAS across all Shopping campaigns for this
  client.
- If the client has an explicit minimum ROAS/profitability target on file
  (e.g., in the client's budget/goals sheet), use that value in place of the
  50%-of-average default for (b).

## 3. Recommendation Format
- `product_id`, `product_title`, `spend`, `conversions`, `roas` (or "0
  conversions" flag)
- `threshold_triggered`: "Zero-Conversion Waste" / "Sub-Threshold ROAS"
- `recommended_action`: "Exclude from campaign" / "Pause product group"
- `reason`: the specific metrics behind the flag

## 4. Output Aggregation and Batching
Apply the identical batching logic defined in
`search-term-negative-keyword-workflow` Section 5: sort by spend descending,
one task if ≤ 25 flagged products, otherwise batch in groups of 25 titled
`[Base Title] (Batch X of Y)`, and if more than 8 batches would be required
(200+ flagged products), additionally create a standalone
`Performance - [Campaign Name] Feed/Catalog Structural Review` task pointing to
`shopping-product-feed-health-audit` and `shopping-product-group-subdivision-audit`
for a root-cause fix rather than one-by-one product exclusions.
