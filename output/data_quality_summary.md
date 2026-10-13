# Data quality summary

- Slice: 30,000 rows × 44 columns, 32 pseudonymized clients.
- Grain holds: 30,000 unique `content_id` values (duplicates: 0).
- Visibility floor `impressions_prev_30d >= 100` keeps 18,010 pages (60.0% of the slice).
- Rows with no prior-window denominator (`impressions_prev_30d == 0`): 3,388 — excluded by the floor.
- `avg_position == 0` means "no data": 1,205 rows.
- Rate columns are ×100 percentages; `scroll_rate` / `ai_traffic_pct` can exceed 100 by design.
- Missingness in `search_volume` / `word_count` is content-type-linked, not random.
- Two stacked 30-day windows are the smallest honest decision/outcome pair this slice offers.