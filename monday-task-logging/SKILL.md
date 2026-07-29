---
name: monday-task-logging
description: Standardizes every Monday.com task created across the paid media optimization system — target board, required fields, task grouping mode, and update formatting. Use whenever an agent needs to log a pacing or performance recommendation to Monday.com.
---

# Monday Task Logging

## Purpose
Standardizes every Monday.com task created across the system — location, fields, and update format.

## 1. Target Location (constant)
- **Workspace:** TEST | Paid Media Optimization Board
- **Board Title:** TEST | Paid Media Optimization Board
- **Monday Host:** vividfront-team.monday.com
- **Target Group:** Google Ads

## 2. Task Grouping Mode (pick one per workflow)
- Pacing → Consolidated Parent + Per-Action Subitems: exactly ONE parent task per client run, titled Pacing - New Campaign Daily Budgets, Campaign Column = "Multiple Campaigns." The parent task's own update is a rolled-up summary only — it does NOT restate individual budget figures as Concrete Action Items (see Section 5a). Every individual budget change action for the run — a single immediate change OR one step of a pacing-math Section 6 staged schedule — is logged as its own Monday subitem under that parent, per Section 4. This lets each action be independently approved and checked off on its own target date without blocking on unrelated actions in the same run.
- **Performance → Individual mode:** ONE standalone task PER recommendation, titled `Performance - [Specific Recommendation Title]`. Never group.

## 3. Required Task Columns
- Task Title (per mode above)
- Client
- Campaign
- Person = Kyle Mollison (kmollison@vividfront.com)
- Status = "Awaiting Approval"
- Logged Date = exact ISO run-date (YYYY-MM-DD)

## 4. Subitem Requirements (Pacing Parent Tasks Only)
Every individual budget change action under a Pacing parent task is created as a
Monday subitem — one subitem per action, never combined.

**Subitem Title:**
- Staged action (one step of a multi-step schedule): `[Campaign Name] Day [N]: $[Current] → $[Target for this step]`
  — e.g. "Campaign A Day 1: $100 → $120"
- Single-step action (no staging required): `[Campaign Name]: $[Current] → $[Target]`
  — e.g. "Campaign B: $100 → $90"

**Subitem Columns:**
- Client
- Campaign = the specific campaign name for this action (not "Multiple Campaigns")
- Person = Kyle Mollison (kmollison@vividfront.com)
- Status = "Awaiting Approval"
- Logged Date = same ISO run-date as the parent (YYYY-MM-DD)
- Scheduled Date = the exact calendar date this specific step should be implemented
  (Day 1 / Day 3 / Day 5, etc., per `pacing-math` Section 6's every-other-day cadence).
  Add this as a Date column on the subitems board if it doesn't already exist.

**Rules:**
- Never combine two steps for the same campaign (e.g. Day 1 and Day 3) into one subitem.
- Never omit a subitem for a campaign whose change didn't require staging — every
  campaign with a recommended change gets at least one subitem.
- Each subitem's Status is tracked and updated independently as it is approved/executed.
  Do not require all subitems to share the same status, and do not mark the parent
  complete based on any single subitem's status.

## 5. Update Formatting (mandatory)
Plain markdown, zero emojis, fully self-contained.

**5a. Parent Task Update (Pacing) / Standalone Task Update (Performance):**
- Section 1 — Diagnostics ("Platform Pacing Summary" for Pacing / "Performance
  Diagnostics" for Performance): state exact Analysis Date Range, list metrics in
  Performance Ranking Hierarchy order (see `ads-performance-data-standards`).
- Section 2 — Concrete Action Items:
  - **Pacing parent tasks:** do NOT restate individual budget figures here. List which
    campaigns require action and point to their subitems, e.g. "Campaign A: staged
    budget increase, see 3 subitems below. Campaign B: single-step budget decrease, see
    subitem below." This section summarizes scope only — it does not execute changes.
  - **Performance standalone tasks:** unchanged — step-by-step, exact parameters to change.
- Section 3 — Strategic Justification: 2-3 sentences referencing the triggering
  metric/rule and expected outcome for the run as a whole.

**5b. Subitem Update (Pacing only):**
Every subitem carries its own update using the SAME mandatory 3-section format, scoped
entirely to that one action, so a human can execute it using only that subitem's
update — without opening the parent task or any other subitem:
- Section 1 — Diagnostics: the specific campaign's current daily budget, target daily
  budget for this step, its Pacing Status, and the rule that triggered this action
  (Brand Protection / Performance Ranking placement / Headroom Check / ON_PACE
  reallocation — per `pacing-math`).
- Section 2 — Concrete Action Items: the single, exact platform change — e.g. "In
  Google Ads, change Campaign A's daily budget from $100.00 to $120.00." Include the
  effective/target date for this step.
- Section 3 — Strategic Justification: 2-3 sentences specific to this one action, not
  the whole run.

