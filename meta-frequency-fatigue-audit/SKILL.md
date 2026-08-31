---
name: meta-frequency-fatigue-audit
description: Flags Meta Ads ads that have crossed audience-fatigue frequency thresholds over the trailing 14-day or 30-day window and recommends pausing them. Use whenever an agent runs a Meta Ads creative/delivery health check, or evaluates whether an active ad should keep serving.
---

# Meta Frequency Fatigue Audit

## Purpose
Detects ad-level audience fatigue on Meta Ads by comparing trailing Frequency against fixed thresholds, and recommends pausing any ad that has crossed them. This is a delivery-health check distinct from `pacing-math`'s Meta Headroom Check (Section 4) — that rule governs budget-increase eligibility, this rule governs whether an ad should keep serving at all.

## 1. Data Retrieval
Pull, per active ad in scope:
- Trailing 14-day Frequency (Impressions / Reach over the trailing 14-day window)
- Trailing 30-day Frequency (Impressions / Reach over the trailing 30-day window)
- Ad status, ad_id, ad_name, ad_set_id, campaign_id

## 2. Trigger Thresholds
Flag the ad for pausing if EITHER condition is true:
- Trailing 14-day Frequency > 4.0, OR
- Trailing 30-day Frequency > 8.0

Both windows must be checked independently every run — an ad can trip the 30-day threshold without having tripped the 14-day one (e.g. steady moderate frequency sustained over a full month), and vice versa (e.g. a frequency spike concentrated in the most recent two weeks).

## 3. Recommendation Format
For every flagged ad, produce:
- `ad_id`, `ad_name`, `ad_set_id`, `campaign_id`
- `frequency_14_day`, `frequency_30_day`
- `triggered_threshold`: "14-DAY" / "30-DAY" / "BOTH"
- `recommended_action`: "PAUSE"
- `reason`: state the exact frequency value(s) that exceeded the threshold

## 4. Output Aggregation
One task per flagged ad, per `monday-task-logging` — this audit does not require batching.
