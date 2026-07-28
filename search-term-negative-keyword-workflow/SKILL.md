# Skill: search-term-negative-keyword-workflow

## Purpose
Defines the mechanical procedure for auditing search terms and producing negative
keyword recommendations. Used by any agent whose audit requires scanning actual
search queries against Google Ads campaigns to find negative keyword candidates:
Brand Search Optimizer Agent, NonBrand Search Optimizer Agent, and Shopping Ads
Optimizer Agent.

This skill defines the HOW. Each calling agent supplies its own trigger criteria
(WHEN to flag a term) — see Section 3.

## 1. Data Retrieval
- Pull the Search Terms report (`search_term_view`) scoped to the campaign(s) in
  scope and the run's `analysis_window` (start_date/end_date) from the
  `inter-agent-payload-schema`.
- **Set the retrieval tool's result limit to its maximum of 5000 per call —
  never rely on the tool's default limit of 500.** The default will silently
  truncate search term volume for any account with meaningful traffic, before
  classification even runs, which undermines the "never drop a flagged term"
  guarantee in Section 5.
- **Paginate if needed:** if a single call returns exactly 5000 rows (the
  maximum), assume more terms exist beyond that page. Repeat the call with the
  tool's pagination mechanism (offset or page token) until a call returns fewer
  than 5000 rows, confirming the full result set for the analysis window has
  been retrieved.
- Required fields: search_term, campaign_id, ad_group_id, impressions, clicks,
  cost, conversions, conversion_value.
- Apply the conversion-value fallback rule from `ads-performance-data-standards`
  before using conversion_value in any calculation.
- Exclude search terms with fewer than 10 impressions in the window (statistical
  noise threshold) unless the calling agent explicitly specifies a different
  threshold.

## 2. Term Classification Passes
Run every retrieved search term through these passes. A term can match multiple
passes; only one match is required to flag it.

- **Pass A — Brand Match Check:** Does the term contain the client's brand name,
  common misspellings, or a known product/trademark name? (Use the client's
  brand-term list if available; otherwise apply the same case-insensitive "Brand"
  logic used in `campaign-classification` as a proxy.)
- **Pass B — Intent/Relevance Check:** Is the term generic, off-topic,
  informational (e.g. "how to," "free," "jobs," "reviews"), or otherwise
  unrelated to the client's actual product/service category?
- **Pass C — Performance Waste Check:** Has the term accumulated spend ≥ 1.5x
  the campaign's (or account's, if no campaign-level target exists) target CPA
  over the analysis window, with zero conversions?
- **Pass D — Close Variant / Broad Match Drift Check:** Was the term matched via
  a broad-match or close-variant expansion of an existing keyword, and does it
  materially diverge in intent from that original keyword?

## 3. Agent-Specific Trigger Mapping
- **Brand Search Optimizer — "Search Terms & Brand Hygiene Audit":** flag terms
  that FAIL Pass A AND match Pass B or D (non-brand/generic/off-target terms
  leaking into a brand campaign).
- **NonBrand Search Optimizer — "Negative Keyword Harvesting Audit":** flag terms
  matching Pass C.
- **NonBrand Search Optimizer — "Brand Leakage Audit":** flag terms that PASS
  Pass A (brand terms appearing in a nonbrand campaign).
- **Shopping Ads Optimizer — "Search Terms Audit":** flag terms matching Pass B
  or Pass C.

## 4. Negative Keyword Recommendation Format
For every flagged term, produce a structured recommendation with these fields:
- `search_term`: the exact term
- `recommended_match_type`: Exact (default, for precision). Use Phrase only if
  the calling agent explicitly justifies a broader block.
- `level`: Ad Group or Campaign (default Campaign) or Shared Negative List
  (recommend this instead if the same term recurs across 3+ ad groups)
- `reason`: which pass(es) matched, plus the supporting metric — e.g.
  "Pass C: $84.20 spend, 0 conversions, target CPA $40.00"

## 5. Output Aggregation and Batching
- Sort all flagged terms for the audit by spend descending (highest-impact
  first). Never drop or silently exclude a flagged term.
- If the total flagged count is ≤ 25, log ONE Monday.com task containing the
  full list, per `monday-task-logging` (Individual mode).
- If the total flagged count is > 25, split into sequential batches of 25 terms
  each (final batch may be smaller than 25). Log ONE Monday.com task PER BATCH —
  every flagged term must appear in exactly one batch task.
  - Title each batch task `[Base Recommendation Title] (Batch X of Y)`, e.g.
    "Performance - Brand Campaign Negative Hygiene (Batch 2 of 4)".
  - All batch tasks for the same audit share identical Client, Campaign,
    Person, Status, and Logged Date values — only the title suffix and the
    term list differ, so Monday's board grouping still clusters them together.
  - In every batch task's Strategic Justification, state the batch number,
    total batch count, and the aggregate scope across ALL batches, e.g.
    "Batch 2 of 4 — 87 terms flagged in total across this audit, representing
    $2,140 in wasted spend over the analysis window."
- **Structural escalation:** If the audit would require more than 8 batches
  (200+ flagged terms), this signals a structural targeting problem, not just
  a keyword-hygiene issue. In addition to logging the batched tactical tasks,
  create one additional standalone task titled
  `Performance - [Campaign Name] Match Type Structural Review` recommending
  the calling agent audit the campaign's overall match-type strategy (e.g.,
  broad match overuse) instead of relying solely on reactive negative-keyword
  additions to control the bleeding.
