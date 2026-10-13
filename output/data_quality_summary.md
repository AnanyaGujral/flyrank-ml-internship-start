# Data-quality summary

- Bundled slice: 30,000 raw rows -> 30,000 analysis rows.
- Pseudo-clients: 32. Duplicate content ids after dedup: 0.
- Proxy decline base rate (bundled): 0.542.
- Full-release pool: 100,893 pages scored; held-out test 20,002 pages; base rate 0.515 (whole pool) / 0.429 (held-out clients).
- Exclusions: impressions < 100; pages without Search Console reporting; GA4 as a hard filter;
  content-release dates as features; label-derived columns as features.
- Privacy: hashed ids only; no client names, domains, URLs, or queries anywhere.