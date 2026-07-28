---
name: inter-agent-payload-schema
description: Defines the JSON contract for Master/Classifier agent handoffs to specialist sub-agents in the paid media optimization system — routing, identity, and scope only, never metrics. Use whenever the Classifier/Master agent dispatches campaigns to a specialist agent, or a specialist needs to validate an incoming payload.
---

# Inter-Agent Payload Schema

## Purpose
Defines the lightweight JSON contract for Master/Classifier → specialist sub-agent handoffs. Carries only routing, identity, and scope — never metrics. Each receiving specialist is responsible for independently pulling whatever metrics its own audit rules require (via `ads-performance-data-standards`), since it would otherwise duplicate a query the Classifier already ran.

## Rules
- campaign_type must be one of the 5 categories from campaign-classification.
- No metrics travel in this payload. The receiving specialist queries Google Ads itself for exactly the metrics/dimensions its audit rules require (search terms report, product-level data, asset group ratings, etc.) — scoped to google_ads_customer_id, the listed campaigns, and the exact analysis_window given.
- analysis_window.days (30 or 60) is decided once per run by the Classifier (or Master) and passed down — specialists must not independently choose a different window length for the same run.
- Sending agent generates one orchestrator_run_id per full run and reuses it across all payloads in that run for traceability.
- Dispatch one payload per specialist per run containing that specialist's full batch of assigned campaigns — not one payload per campaign.
- Receiving agent should reject/flag a payload missing required fields rather than guessing defaults.

## Schema

```json
{
  "orchestrator_run_id": "string-uuid",
  "run_date": "YYYY-MM-DD",
  "client_name": "string",
  "google_ads_customer_id": "string",
  "analysis_window": {
    "days": 30,
    "start_date": "YYYY-MM-DD",
    "end_date": "YYYY-MM-DD"
  },
  "campaigns": [
    {
      "campaign_id": "string",
      "campaign_name": "string",
      "campaign_type": "string",
      "status": "ENABLED"
    }
  ]
}
