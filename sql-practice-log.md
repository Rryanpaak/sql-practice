# SQL Practice Log (DataLemur, PostgreSQL)

Purpose: track what I can solve alone, where I get stuck, and the patterns I have learned.
Rule: try alone for 15-20 minutes, then use one hint at a time. Mark "hint used". Redo hint-used problems after 2-3 days with no help.

## Session 1 - 2026-10-29

Time: about 25 minutes. Problems attempted: 5. Solved alone: 0 (hints used, so this is not a clean baseline).

| # | Problem | Status | Where I got stuck | Pattern to learn |
|---|---------|--------|-------------------|------------------|
| 1 | Swapped Food Delivery | Not solved | Edge case when the last order has no pair | CASE WHEN with an edge case |
| 2 | Bloomberg Stock Min/Max | Not solved | Date format `Mon-YYYY`; getting the month of the max/min value | TO_CHAR, top-1 per group |
| 3 | Best-Selling Product | Not solved | No ranking inside each category; wrong ORDER BY | Top-1 per group |
| 4 | Amazon Shopping Spree | Not solved | Error on `transaction_date`; counting 3 days in a row | Cast to DATE, consecutive days |
| 5 | Histogram of Users and Purchases | Not solved | Thought I needed DISTINCT; really needed the latest date per user | Latest row per user |

## Patterns

### 1. Date format
```sql
TO_CHAR(date_column, 'Mon-YYYY')   -- Jan-2026
date_column::DATE                  -- remove the time part of a timestamp
```

### 2. Top-1 per group (best / latest / highest per group)
Rule: PARTITION BY group + ORDER BY criteria + rank = 1.
```sql
WITH ranked AS (
  SELECT
    group_col,
    item_col,
    ROW_NUMBER() OVER (
      PARTITION BY group_col
      ORDER BY metric_col DESC, tie_breaker_col DESC
    ) AS rn
  FROM some_table
)
SELECT group_col, item_col
FROM ranked
WHERE rn = 1
ORDER BY group_col;
```
Use ROW_NUMBER for exactly one row per group. RANK keeps ties.

### 3. Latest row per user
Same pattern as above: `PARTITION BY user_id ORDER BY transaction_date DESC`, then keep `rn = 1`, then COUNT. DISTINCT is not the tool for this.

### 4. Consecutive days
1. Make a clean list of unique `user_id, activity_date::DATE`.
2. Check that the same user also has `date + 1` and `date + 2`, using a self-join or `LEAD(activity_date, 1)` and `LEAD(activity_date, 2)`.

### 5. CASE WHEN with an edge case
Write the normal rule first (odd +1, even -1). Then list the exception (last odd number has no pair) and give it its own WHEN branch.

## How I approach a new problem
1. Read the question.
2. Write the output columns.
3. Write the steps in plain words.
4. Write the SQL.

## Redo log

| Problem | Redo date | Solved alone? | Notes |
|---------|-----------|---------------|-------|
| Swapped Food Delivery | 2026-11-01 | | |
| Bloomberg Stock Min/Max | 2026-11-01 | | |
| Best-Selling Product | 2026-11-01 | | |
| Amazon Shopping Spree | 2026-11-01 | | |
| Histogram of Users and Purchases | 2026-11-01 | | |

Target for the 30-day mark: 7 out of 10 medium problems solved with no hints.
