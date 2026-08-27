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
| Brand Search | SEARCH type + name contains "Brand" (case-insensitive) AND name does NOT contain "NonBrand" / "Non-Brand" / "Non Brand" (case-insensitive, ignoring spaces and hyphens) |
| Nonbrand Search | SEARCH type + (name contains "NonBrand" / "Non-Brand" / "Non Brand" (case-insensitive) OR name does not contain "Brand" at all) |
| Shopping Ads | SHOPPING type |
| Performance Max | MULTI_CHANNEL or PERFORMANCE_MAX type |
| Display | DISPLAY type |

## Disambiguation: "Brand" Substring Collisions
Campaign names frequently embed the literal substring "brand" inside "nonbrand"
(e.g. `VTM_US_GGL_Nonbrand Pokemon`). Because Brand Search is evaluated first under
first-match-wins, a naive `contains "Brand"` test on such a name would incorrectly
classify an explicitly non-brand campaign as Brand Search.

Resolve this deterministically, without guessing:
1. Normalize the campaign name for comparison purposes only (do not mutate the
   stored name): lowercase it and strip spaces/hyphens.
2. Check for the token `nonbrand` first. If present, classify as Nonbrand Search —
   Brand Search must NOT be applied, regardless of the literal "brand" substring match.
3. Only if `nonbrand` is absent should the name be tested against the plain "brand"
   substring for Brand Search classification.

**Example:** `VTM_US_GGL_Nonbrand Pokemon` → normalized name contains `nonbrand` →
classify as Nonbrand Search (not Brand Search), with no manual review needed.

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
- Flag anything that doesn't cleanly match for manual review rather than guessing —
  this now only applies to names the Disambiguation normalization above cannot
  resolve (e.g. "brand" appearing as part of an unrelated word, or a name containing
  both "brand" and "nonbrand" as separate tokens).
- Classification only requires campaign metadata (`advertising_channel_type` + campaign name) — no performance metrics are needed to run this skill.
