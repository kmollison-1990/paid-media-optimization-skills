# Skill: shopping-product-feed-health-audit

## Purpose
Scans the merchant product feed for missing or under-optimized fields and
recommends concrete title/description/attribute fixes to improve match
relevance and Shopping ad eligibility. Used exclusively by Shopping Ads
Optimizer Agent.

## 1. Data Retrieval
- Pull Merchant Center feed data for all active products advertised in scope:
  title, description, image_link, availability, price, gtin/mpn/brand
  identifiers, product_type, custom_labels, item_id.
- Cross-reference against the spend/performance data already pulled in
  `shopping-product-id-pruning-audit` to prioritize fixes on products
  receiving meaningful spend — feed issues on zero-spend products are lower
  priority.
- Where the calling agent has landing page crawl capability (per the pattern
  established in `ad-copy-asset-refresh-workflow`), cross-check the
  availability/price fields against the actual product landing page.

## 2. Field-by-Field Checklist (explicit — replaces "scan for missing or
poorly optimized fields")
- **Title:** flag if missing, under 40 characters, or missing at least 2 of:
  brand name, product type/category term, a key differentiating attribute
  (color/size/material/model).
- **Description:** flag if missing or under 100 characters.
- **Image:** flag if image_link is missing, or Merchant Center reports a
  non-zero image issue count (low-resolution, watermark, placeholder warnings).
- **Identifiers:** flag if gtin/mpn/brand are all missing for a product
  category that typically requires at least one for full Shopping eligibility.
- **Availability/Price Mismatch:** flag if the feed's availability or price
  disagrees with what the landing page actually shows.

## 3. Recommendation Format
- `item_id`, `product_title`
- `issue_type`: Title / Description / Image / Identifier /
  Availability-Price Mismatch
- `recommended_fix`: the specific rewritten title/description text, the
  specific missing identifier to add if determinable, or the specific
  Merchant Center diagnostic code to resolve
- `reason`

## 4. Output Aggregation and Batching
Apply the identical batching logic from `search-term-negative-keyword-workflow`
Section 5 (batches of 25, sorted by spend descending). If more than 8 batches
would be required (500+ flagged items), recommend a full feed cleanup project
rather than one-by-one AI-driven rewrites, and note this explicitly in the
Strategic Justification.
