---
type: "BigQuery Table"
title: "closeout_sale_dashboard_daily"
description: "임박할인(close-out sale) 노출/유입/구매 분석"
resource: "bigquery://bq-da-common.co_data.closeout_sale_dashboard_daily"
tags: ["tier2", "agg_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# closeout_sale_dashboard_daily

- **Project.Dataset.Table**: `bq-da-common.co_data.closeout_sale_dashboard_daily`
- **Grain**: 일
- **Partition**: `dt` (DATE)
- **Key columns**: dt
- **Used for**: 임박할인(close-out sale) 노출/유입/구매 분석
- **Standard filter required**: N
- **Source confidence**: high

# Pitfalls

** closeout = 재무 마감 아님. 유통기한 임박 할인이라는 뜻 **

# Joins

- closeout_sale_dashboard_hourly
- closeout_sale_entry
