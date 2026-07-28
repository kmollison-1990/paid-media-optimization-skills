# Skill: ad-copy-asset-refresh-workflow

## Purpose
Defines the mechanical procedure for evaluating Responsive Search Ad (RSA) asset
performance and producing concrete headline/description/extension refresh
recommendations. Used by Brand Search Optimizer Agent and NonBrand Search
Optimizer Agent.

This skill defines the HOW. Each calling agent applies its own messaging intent
(brand-defense copy vs. non-brand value-proposition copy) when drafting
replacement text — see Section 4.

## 1. Data Retrieval
- Pull ad-level and asset-level performance for all active RSAs in the campaign(s)
  in scope, over the run's `analysis_window`.
- Required ad-level fields: ad_group_id, ctr, impressions, clicks, ad_strength,
  final_urls.
- Required asset-level fields: asset_text, asset_type (HEADLINE / DESCRIPTION),
  asset_performance_label (LOW / GOOD / BEST / PENDING / LEARNING), pinned_field
  (if the asset is pinned to a specific position).
- Required extension fields: sitelink, callout, and structured snippet assets
  currently linked and their enabled/serving status.
- **Landing Page Retrieval (required):** For each ad group, retrieve the Final
  URL(s) used by its RSAs. Crawl/fetch each unique landing page and extract:
  page title/meta title, H1/H2 headings, primary value proposition statements,
  key product/service details, pricing or offer language, and CTA language. If
  an ad group serves multiple distinct final URLs, crawl each one. This
  extracted content is the reference source of truth for Pass E (Section 2)
  and for drafting `recommended_text` (Section 4) — copy recommendations must
  be written to match what the landing page actually says, not invented
  independently.
- Exclude ad groups with fewer than 100 impressions in the window (noise
  threshold) unless the calling agent specifies otherwise.

## 2. Evaluation Passes
- **Pass A — Asset Performance Label Check (primary trigger):** Any HEADLINE or
  DESCRIPTION asset rated LOW by Google's asset performance reporting.
- **Pass B — Ad Strength Check (informational only — NOT a trigger):** Google's
  Ad Strength score primarily rewards asset quantity and diversity, not actual
  relevance or real performance, and is not a reliable standalone signal of ad
  quality. Record the current Ad Strength rating in the diagnostics for
  context only. Never flag or deprioritize an ad group based on Ad Strength
  alone, and never let a GOOD/EXCELLENT Ad Strength rating suppress a flag
  raised by Pass A, C, D, or E.
- **Pass C — CTR Benchmark Check (primary trigger):** Ad group CTR over the
  analysis window falls below the account's trailing-window average CTR for
  the same campaign type.
- **Pass D — Extension Coverage Check (run independently, always):** Sitelinks,
  callouts, or structured snippets are missing, disabled, or below the
  platform-recommended minimum count (4 sitelinks, 4 callouts, 4 structured
  snippet values).
- **Pass E — Landing Page Message Parity Check (primary trigger):** Compare the
  ad group's current headline/description asset text against the landing page
  content extracted in Section 1. Flag if the ad's core value proposition,
  product/service terminology, or offer details are not reflected in at least
  half of the active headlines, or if the ad promotes a claim/offer not present
  on the landing page (relevance/compliance risk), or if the landing page's
  primary offer is not mentioned in any live headline or description.

## 3. Trigger Mapping
Both calling agents use the same trigger logic for this audit ("Ad Copy &
Extension Audit"): flag the ad group if it matches Pass A, C, or E. Pass B (Ad
Strength) is recorded for context only and must never be used to flag or to
suppress a flag raised by another pass. Always run Pass D independently of
whether the ad group is otherwise flagged.

## 4. Refresh Recommendation Format
For every flagged item, produce a structured recommendation:
- `asset_type`: Headline / Description / Sitelink / Callout / Structured Snippet
- `current_text`: the existing asset text being replaced (omit if adding new,
  e.g. filling a missing extension slot)
- `recommended_text`: the exact replacement copy. **Hard character limits —
  not stylistic guidelines:**
  - Headlines: 30 characters maximum, counted exactly (including spaces and
    punctuation).
  - Descriptions: 90 characters maximum, counted exactly (including spaces and
    punctuation).
  - Validate the character count before finalizing any recommendation. If a
    draft exceeds the limit, rewrite and shorten it — never submit an
    over-limit recommendation for a human to trim later.
- `landing_page_alignment`: brief note on how this recommendation ties to the
  specific landing page content identified in Section 1, e.g. "Mirrors landing
  page H1: 'Free 3-Day Shipping on All Orders'"
- `messaging_intent`: state whether the copy reflects brand-defense messaging
  (Brand Search Optimizer) or value-proposition/CTA messaging (NonBrand Search
  Optimizer) — this is the one place the two calling agents diverge
- `reason`: which pass triggered the flag, with the supporting metric

## 5. Copywriting Guardrails
- Every Headline recommendation must be ≤ 30 characters; every Description
  recommendation must be ≤ 90 characters. Count exactly — do not estimate.
- All `recommended_text` must be grounded in the landing page content retrieved
  in Section 1. Do not invent claims, offers, product details, or pricing not
  actually present on the landing page.
- Maintain at least 8 headlines and 3 descriptions per ad group after refresh
  (Google's recommended minimum for full RSA optimization) — never recommend
  dropping below 3 headlines / 2 descriptions.
- No ALL CAPS, no excessive punctuation ("!!!"), no unsubstantiated superlatives
  ("best," "#1") unless the client has existing approved claims language.
- Avoid recommending near-duplicate headlines (same claim reworded) — each
  headline should carry a distinct value point.
- Only recommend pinning a headline/description to a fixed position if there is
  a compliance, legal, or brand-mandatory reason — unpinned RSAs generally
  perform better since Google can test combinations freely.

## 6. Output Aggregation
- Group all flagged assets/extensions for a single ad group into ONE Monday.com
  task update, per `monday-task-logging` (Individual mode). Do not create one
  task per individual headline or extension.
- If multiple ad groups within the same campaign are flagged, create one task
  per ad group (not one per campaign) so each remains independently actionable.
