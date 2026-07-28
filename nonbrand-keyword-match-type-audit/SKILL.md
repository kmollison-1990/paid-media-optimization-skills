# Skill: nonbrand-keyword-match-type-audit

## Purpose
Identifies broad-match keywords in NonBrand Search campaigns generating
low-intent or low-converting traffic, and recommends tightening to phrase or
exact match. Used exclusively by NonBrand Search Optimizer Agent.

## 1. Data Retrieval
- Pull keyword-level performance for all ENABLED keywords in scope, over the
  run's `analysis_window`: keyword text, match_type, campaign_id, ad_group_id,
  impressions, clicks, cost, conversions, conversion_value. Apply the
  conversion-value fallback from `ads-performance-data-standards`.
- For every BROAD match keyword only, pull its associated search_term_view
  rows (the actual queries it triggered on): search_term, cost, conversions.
- Apply the same max-limit + pagination discipline established in
  `search-term-negative-keyword-workflow` (set retrieval limit to the tool's
  maximum per call, paginate until a call returns fewer than the max) — do not
  rely on default page sizes for either pull.

## 2. Decision Criteria
Only BROAD match keywords are eligible for this audit. For each broad keyword:
- Compute its CPA and ROAS per `ads-performance-data-standards` formulas.
- Compute Query Diversity Ratio = distinct triggering search terms / total
  triggering search term occurrences (higher = looser matching).
- **Flag for tightening if either:**
  - (a) CPA is more than 1.3x the ad group's average CPA across its phrase/exact
    keywords, OR
  - (b) More than 30% of triggering search term volume would independently
    fail Pass B (Intent/Relevance Check) from
    `search-term-negative-keyword-workflow`.

## 3. Recommended Action Determination
- If the keyword has ≥ 10 conversions concentrated in a specific subset of
  triggering search terms: recommend adding those top-converting terms as new
  **Phrase match** keywords (retains reach, cuts the worst noise).
- If the keyword has few/no conversions and high diversity: recommend adding
  only the literal converting search terms as new **Exact match** keywords,
  and pausing/removing the original broad-match keyword entirely.

## 4. Recommendation Format
- `original_keyword`, `original_match_type`
- `recommended_new_keywords`: array of `{ text, match_type }`
- `action`: "Add tighter match type keywords" / "Pause original broad keyword"
- `reason`: CPA multiple and/or diversity ratio with supporting numbers

## 5. Output Aggregation
One task per ad group. If more than 25 keyword-level changes are recommended
for a single ad group, apply the same batching rule as
`search-term-negative-keyword-workflow` Section 5 (batches of 25, sorted by
spend descending, same >8-batch structural escalation note).
