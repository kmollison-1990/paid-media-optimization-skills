# Skill: shopping-product-group-subdivision-audit

## Purpose
Identifies overly broad product groups (e.g. a single "All Products" or
catch-all partition) mixing high- and low-performing products, and recommends
subdividing into dedicated segments. Used exclusively by Shopping Ads
Optimizer Agent.

## 1. Data Retrieval
- Pull the current product group / partition structure for Shopping ad groups
  in scope (how many distinct product groups exist, and whether a catch-all
  "everything else" node is present).
- Pull product-level performance within each group over the run's
  `analysis_window` (same fields as `shopping-product-id-pruning-audit`
  Section 1).

## 2. Decision Criteria (explicit — replaces the vague "high/low performers
lumped together")
Flag an ad group for subdivision if BOTH:
- It has a single product group (or catch-all partition) covering ≥ 50
  distinct product IDs, AND
- The top-quartile products' average ROAS within that group is ≥ 2x the
  bottom-quartile products' average ROAS (a measurable performance spread).

## 3. Segmentation Axis Determination
- Check whether high-ROAS products in the flagged group share a common
  attribute (product_type, brand, or custom_label) distinct from low-ROAS
  products. If a clean correlation exists, recommend subdividing along that
  attribute.
- If no clean attribute correlation exists, recommend a manual split using the
  specific item_id lists already classified as high/low performers by
  `shopping-product-id-pruning-audit`.

## 4. Recommendation Format
- `ad_group_name`, `current_structure` (e.g. "single group, 84 products")
- `recommended_subdivision`: proposed sub-groups with defining attribute or
  item_id list, plus bid-adjustment intent (e.g. "+20% bid ceiling for
  high-performer group, standard bid for remainder")
- `reason`: the ROAS spread data supporting the split

## 5. Output Aggregation
One task per flagged ad group. Apply the standard >25-recommendation batching
rule for consistency if many ad groups are flagged in a single run.
