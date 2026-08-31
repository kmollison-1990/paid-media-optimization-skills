---
name: meta-video-hook-hold-rate-audit
description: Diagnoses whether a Meta Ads video creative is failing to earn attention in its first 3 seconds (Hook Rate) or failing to hold attention through to a ThruPlay (Hold Rate), and recommends the corresponding creative fix. Use whenever an agent runs a Meta Ads video creative performance audit.
---

# Meta Video Hook & Hold Rate Audit

## Purpose
Video creatives fail for one of two distinct reasons: viewers never engage past the opening frames, or viewers engage initially but disengage before the video finishes. This skill separates those two failure modes with two independent passes and routes each to its own specific creative recommendation.

## 1. Data Retrieval
Pull, per active video ad in scope, over the run's analysis window:
- Impressions
- 3-Second Video Views (video plays that reached 3 seconds)
- ThruPlays (video played to completion, or to 15 seconds, whichever is shorter — per Meta's definition)
- ad_id, ad_name, ad_set_id, campaign_id, creative format (video only — image/carousel ads are out of scope for this skill)

## 2. Calculated Metrics
- `Hook Rate = 3-Second Video Views / Impressions`
- `Hold Rate = ThruPlays / 3-Second Video Views` (null if 3-Second Video Views = 0 — do not divide by zero; route to Pass A only)

## 3. Noise Guardrail
Exclude video ads with fewer than 100 impressions in the analysis window from both passes (mirrors the noise-floor convention used in `ad-copy-asset-refresh-workflow`) unless the calling agent specifies otherwise.

## 4. Evaluation Passes (run independently, both apply to every in-scope video ad)
- **Pass A — Hook Rate Check:** If Hook Rate < 30%, flag for a hook-optimization recommendation — the opening 3 seconds are not stopping the scroll.
- **Pass B — Hold Rate Check:** If Hold Rate < 30%, flag for a body-content recommendation — viewers are stopping but not staying. Only evaluate Pass B if 3-Second Video Views > 0; if Hold Rate is null (no 3-second views to hold), Pass A already covers the ad and Pass B is not applicable.
- A single ad can fail both passes and receive both recommendations — the two failure modes are not mutually exclusive.

## 5. Recommendation Format
For every flagged pass, produce:
- `ad_id`, `ad_name`, `ad_set_id`, `campaign_id`
- `hook_rate`, `hold_rate` (report both regardless of which pass triggered, for context)
- `diagnostic_trigger`: "LOW_HOOK_RATE" / "LOW_HOLD_RATE" / "BOTH"
- `recommended_action`:
  - LOW_HOOK_RATE → "Optimize the first 3 seconds of the video creative with a stronger visual and/or audio hook to stop the scroll before the viewer disengages."
  - LOW_HOLD_RATE → "Optimize the body content of the video creative to be more engaging and snappy so viewers who stop scrolling stay through to a ThruPlay."
- `reason`: the exact Hook Rate / Hold Rate value(s) that triggered the flag

## 6. Output Aggregation
One task per flagged ad, per `monday-task-logging` — if an ad fails both passes, combine both recommendations into that ad's single task rather than creating two.
