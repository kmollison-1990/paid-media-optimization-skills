---
name: pacing-math
description: Standardizes budget pacing calculations, pacing status classification, and budget allocation/reallocation logic for Google Ads campaigns. Use whenever an agent runs a pacing check, classifies pacing status, or recommends daily budget changes.
---

# Pacing Math

## Purpose
Standardizes budget pacing calculations and allocation logic.

## 1. Core Formulas
- `Percent Month Elapsed = (Current Day of Month / Total Days in Month) × 100`
- `Percent Budget Spent = (Total MTD Spend / Monthly Budget) × 100`
- `Remaining Budget = Monthly Budget − Total MTD Spend`
- `Remaining Days = (Total Days in Month − Current Day of Month) + 1`
- `Target Daily Spend = Remaining Budget / Remaining Days` (default Remaining Days to 1 if ≤ 0)

## 2. Pacing Status Classification
- **ON_PACE:** Percent Budget Spent within ±2.0% of Percent Month Elapsed
- **UNDER_PACING:** more than 2.0% below
- **OVER_PACING:** more than 2.0% above

## 3. Brand Protection Rule (overrides pacing status)
A brand campaign (name contains "Brand") must have its coverage gap closed immediately, regardless of pacing status, if:
- Absolute Top Impression Share < 85.0%, OR
- Search Budget Lost Impression Share > 5.0%

## 4. Headroom Check
A non-brand campaign is only eligible for a budget increase if Search Budget Lost Impression Share > 10.0%.

## 5. Allocation Strategy by Status
- **UNDER_PACING:** close Brand Protection gaps → scale up highest-ranked non-brand campaigns passing Headroom Check → leave underperformers untouched.
- **OVER_PACING:** hold Brand budgets → bottom-up sweep, cut lowest-ranked non-brand campaigns first → continue until aggregate matches Target Daily Spend.
- **ON_PACE:** hold Brand budgets → cut bottom 25% of non-brand campaigns by exactly 10% → pool savings → reallocate to top 25% passing Headroom Check → net change must equal $0.00.

## 6. Change-Size Guardrail
No single daily budget change may exceed 20.0% of current budget in one step. Changes
≥20% must be staged in 20%-increments every other day until the target is reached.

**Example:** $100 → $150: Day 1 → $120, Day 3 → $140, Day 5 → $150.

**Output contract:** Every individual change produced by this rule is a discrete
"action" — one campaign's budget moving from one dollar figure to another, on a
specific date. This applies whether a campaign requires only one action (change <20%,
no staging) or a multi-step staged schedule. Each action is logged as its own Monday
subitem under the run's single Pacing parent task — see `monday-task-logging` Section 4
for subitem structure and titling. Never collapse multiple actions for the same
campaign into one subitem, and never skip a subitem for a campaign whose change didn't
require staging.

## 7. Data Window
Pacing uses trailing 14-day performance data (the one exception to the 30/60-day rule in `ads-performance-data-standards`) for CTR/CPA/ROAS/Impression Share inputs, plus current MTD spend and monthly budget from Google Sheet ID `1noi0xooahfN94hjT3eVvNMwXr3pKUQj_FlDgnxFdbcs`.
