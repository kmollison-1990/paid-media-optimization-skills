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
- **Pacing → Consolidated mode:** exactly ONE parent task per client run, titled `Pacing - New Campaign Daily Budgets`, aggregating all recommendations. Campaign Column = "Multiple Campaigns."
- **Performance → Individual mode:** ONE standalone task PER recommendation, titled `Performance - [Specific Recommendation Title]`. Never group.

## 3. Required Task Columns
- Task Title (per mode above)
- Client
- Campaign
- Person = Kyle Mollison (kmollison@vividfront.com)
- Status = "Awaiting Approval"
- Logged Date = exact ISO run-date (YYYY-MM-DD)

## 4. Update Formatting (mandatory)
Plain markdown, zero emojis, fully self-contained — a human must be able to execute using only this update.

- **Section 1 — Diagnostics** ("Platform Pacing Summary" for Pacing / "Performance Diagnostics" for Performance): state exact Analysis Date Range, list metrics in Performance Ranking Hierarchy order (see `ads-performance-data-standards`).
- **Section 2 — Concrete Action Items:** step-by-step, exact parameters to change.
- **Section 3 — Strategic Justification:** 2-3 sentences referencing the triggering metric/rule and expected outcome.
