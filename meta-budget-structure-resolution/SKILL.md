---
name: meta-budget-structure-resolution
description: Determines whether a Meta Ads campaign is Campaign Budget Optimization (CBO) or Ad Set Budget Optimization (ABO), and resolves the correct "budget entity" (or entities) whose daily budget should be read and adjusted for pacing. Use before running pacing-math on any Meta Ads campaign.
---

# Meta Budget Structure Resolution

## Purpose
Meta stores the adjustable daily budget at different levels depending on campaign
structure. This skill resolves, for each Meta campaign in scope, exactly which
entity/entities pacing-math should treat as its unit of allocation — mirroring the role
`campaign-classification` plays for Google Ads campaign typing.

## 1. Determination Logic
- Query the Campaign object's budget fields (daily budget / lifetime budget).
  - If populated at the campaign level: **CBO**. The budget entity is the campaign
    itself — one daily budget to read and adjust.
  - If null/absent at the campaign level: **ABO**. Query every Ad Set under that
    campaign and check each Ad Set's own budget fields.
    - The budget entity is each Ad Set with a populated daily budget — a single ABO
      campaign can therefore produce MULTIPLE independent budget entities.
- Confirm the exact API field name(s) for campaign-level vs. ad-set-level daily budget
  against your current Meta Marketing API version before wiring this into a live
  query — the check-which-level-is-populated logic above is reliable regardless of
  exact field naming, but the literal field name should be verified.

## 2. Output Contract
For each Meta campaign, resolve to one of the following shapes.

CBO campaign:
```json
{
  "campaign_id": "string",
  "campaign_name": "string",
  "budget_type": "CBO",
  "entities": [
    {
      "entity_level": "CAMPAIGN",
      "entity_id": "string",
      "entity_name": "string",
      "current_daily_budget": 0.00
    }
  ]
}
```

ABO campaign:
```json
{
  "campaign_id": "string",
  "campaign_name": "string",
  "budget_type": "ABO",
  "entities": [
    {
      "entity_level": "ADSET",
      "entity_id": "string",
      "entity_name": "string",
      "current_daily_budget": 0.00
    },
    {
      "entity_level": "ADSET",
      "entity_id": "string",
      "entity_name": "string",
      "current_daily_budget": 0.00
    }
  ]
}
```

## 3. Handoff to pacing-math
Each resolved entity — a Google Ads campaign, a Meta CBO campaign, or a Meta ABO ad
set — is treated as its own independent unit of pacing analysis and budget adjustment.
`pacing-math`'s formulas, classification, and allocation strategies apply identically
regardless of which of these three entity types is being evaluated.

## 4. Out of Scope (flag for manual review, do not process)
Any Meta campaign/ad set using a **lifetime budget** instead of a **daily budget**.
This system's pacing formulas assume daily budgets; lifetime-budget entities need a
different pacing approach not yet defined here.
