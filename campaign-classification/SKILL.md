---
name: campaign-classification
description: Standardizes how Google Ads campaigns are sorted into the 5 categories used throughout the paid media optimization system, and which specialist agent each category routes to. Use whenever campaigns need to be categorized or handed off to a specialist optimizer agent.
---

# Campaign Classification

## Purpose
Standardizes how campaigns are sorted into the 5 categories used throughout the system.

## Rules
First match wins. Only ENABLED campaigns unless told otherwise.

| Category | Match Condition |
|---|---|
| Brand Search | SEARCH type + name contains "Brand" (case-insensitive) |
| Nonbrand Search | SEARCH type + name does NOT contain "Brand" |
| Shopping Ads | SHOPPING type |
| Performance Max | MULTI_CHANNEL or PERFORMANCE_MAX type |
| Display | DISPLAY type |

## Routing Table

| Category | Destination Agent |
|---|---|
| Brand Search | Brand Search Optimizer Agent |
| Nonbrand Search | Nonbrand Search Optimizer Agent |
| Shopping Ads | Shopping Ads Optimizer Agent |
| Performance Max | PMax Optimizer Agent |
| Display | Display Optimizer Agent |

## Notes
- Every enabled campaign must land in exactly one category.
- Flag anything that doesn't cleanly match for manual review rather than guessing.
- Classification only requires campaign metadata (`advertising_channel_type` + campaign name) — no performance metrics are needed to run this skill.
