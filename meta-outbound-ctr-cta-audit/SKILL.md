---
name: meta-outbound-ctr-cta-audit
description: Flags Meta Ads ads whose outbound CTR (link clicks / impressions) is underperforming a fixed floor and recommends strengthening the ad's call-to-action. Use whenever an agent runs a Meta Ads creative performance audit — applies to all ad formats, not just video.
---

# Meta Outbound CTR & CTA Audit

## Purpose
Low outbound click-through despite adequate impression volume is typically a call-to-action problem rather than a targeting or delivery problem. This skill isolates that signal and produces a CTA-focused creative recommendation. Applies to every active ad format (image, video, carousel) — it is not limited to video creatives.

## 1. Data Retrieval
Pull, per active ad in scope, over the run's analysis window:
- Impressions
- Outbound Clicks (link clicks to the destination URL — Meta's `link_click` metric, not the broader `clicks` metric which also counts on-platform engagement like likes/comments/expands)
- ad_id, ad_name, ad_set_id, campaign_id, creative format

## 2. Calculated Metric
`Outbound CTR = Outbound Clicks / Impressions`

## 3. Noise Guardrail
Exclude ads with fewer than 100 impressions in the analysis window (mirrors the noise-floor convention used in `ad-copy-asset-refresh-workflow`) unless the calling agent specifies otherwise.

## 4. Trigger Threshold
Flag the ad if Outbound CTR < 0.8%.

## 5. Recommendation Format
For every flagged ad, produce:
- `ad_id`, `ad_name`, `ad_set_id`, `campaign_id`, `creative_format`
- `outbound_ctr`
- `recommended_action`: "Optimize the creative's call-to-action — strengthen or clarify the CTA copy/button to convert impressions into outbound clicks more effectively."
- `reason`: the exact Outbound CTR value versus the 0.8% floor

## 6. Output Aggregation
One task per flagged ad, per `monday-task-logging` — this audit does not require batching.
