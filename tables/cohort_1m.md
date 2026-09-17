---
type: "BigQuery Table"
title: "cohort_1m"
description: "월별 코호트 리텐션·BS·ARPU·freq"
resource: "bigquery://bq-datafarm.data_warehouse.cohort_1m"
tags: ["tier1", "cohort_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# cohort_1m

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.cohort_1m`
- **Grain**: 가입월(yymm) × 주문월(order_ym) × 세그
- **Partition**: `yymm` (DATE)
- **Key columns**: yymm+order_ym+grp_*
- **Used for**: 월별 코호트 리텐션·BS·ARPU·freq
- **Standard filter required**: N
- **Source confidence**: mid

# Pitfalls

p1_BS=gmv2/orders, p1_ARPU=gmv2/purchaser, p1_freq=orders/purchaser [v4 검증대기]

# Joins

- cohort_group_1m
- cohort_orders_1m
