# Skill: pmax-asset-group-strength-audit

## Purpose
Identifies "Low"-rated PMax asset groups and drafts concrete replacement
headline/description/image asset recommendations. Used exclusively by PMax
Optimizer Agent.

## 1. Data Retrieval
- Pull asset group-level performance and Google's asset_group_asset
  `performance_label` (LOW / GOOD / BEST) for all active asset groups in scope,
  over the run's `analysis_window`: asset_group_id, asset_type (HEADLINE /
  LONG_HEADLINE / DESCRIPTION / IMAGE / LOGO / VIDEO), asset text or reference,
  performance_label.
- Reuse the landing page crawl step from `ad-copy-asset-refresh-workflow`
  Section 1: retrieve the asset group's Final URL, crawl it, and extract
  messaging/value props to ground any replacement text copy.

## 2. Decision Criteria
Flag any asset group with one or more assets (any type) rated LOW. Unlike
`ad-copy-asset-refresh-workflow`'s treatment of Ad Strength, PMax's
`performance_label` is asset-specific serving data (not a diversity/coverage
score) and can be trusted directly as a primary trigger — no de-weighting
needed here.

## 3. Recommendation Format
- `asset_group_id`, `asset_type`
- `current_asset`: existing text, or a description of the existing image/video
- `recommended_asset`: for text assets, the exact replacement copy — reuse the
  identical hard character limits from `ad-copy-asset-refresh-workflow`
  (Headlines ≤ 30 characters, Long Headlines ≤ 90 characters, Descriptions ≤
  90 characters). For IMAGE or VIDEO assets, do NOT generate replacement
  creative directly — instead produce a creative brief (dimensions, subject
  matter grounded in the landing page's hero imagery) and route it to the
  Design Agent rather than attempting to author visual assets in this audit.
- `landing_page_alignment`: how the recommendation ties to specific landing
  page content
- `reason`

## 4. Output Aggregation
One task per asset group. Apply the standard >25-recommendation batching rule
for consistency if unusually many asset groups are flagged in a single run.
