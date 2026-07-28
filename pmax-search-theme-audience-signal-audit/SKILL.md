# Skill: pmax-search-theme-audience-signal-audit

## Purpose
Recommends adding high-converting search themes and refining audience signal
definitions to improve PMax's machine-learning targeting inputs. Used
exclusively by PMax Optimizer Agent.

## 1. Data Retrieval
- Pull currently configured search themes per asset group.
- Pull PMax insights/search-term-category data over the run's
  `analysis_window` (the categories of actual converting traffic PMax exposes
  via its insights reporting).
- Pull currently configured audience signals per asset group (customer lists,
  custom segments, interest categories) and, where available, their matched
  audience size / engagement stats.

## 2. Decision Criteria (explicit — replaces the undefined "high-converting")
- **Search theme candidate:** a theme/category appearing in PMax insights data
  with ≥ 5 attributed conversions (or ranking in the top quartile of
  categories by conversion volume) over the analysis window, AND not already
  configured as a search theme for that asset group.
- **Audience signal refinement candidate:** EITHER
  - (a) a configured signal shows near-zero matched audience size or
    engagement per PMax's audience insights (contributing negligible ML
    signal), OR
  - (b) a client-provided converter list (e.g. existing customer/purchaser
    list) exists in the account's audience manager but is NOT currently linked
    to the asset group's audience signal.

## 3. Recommendation Format
- `asset_group_id`
- `recommendation_type`: "Add Search Theme" / "Refine Audience Signal"
- `specific_value`: the theme text to add, or the specific audience list to
  link or remove
- `supporting_data`: conversion count, matched audience size, or engagement
  metric backing the recommendation
- `reason`

## 4. Output Aggregation
One task per asset group. Apply the standard batching rule if an unusually
high number of asset groups are flagged in a single run.
