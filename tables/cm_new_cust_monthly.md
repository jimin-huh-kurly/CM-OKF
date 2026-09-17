---
type: "BigQuery Table"
title: "cm_new_cust_monthly"
description: "신규 고객 30분 단위 시간대 분석"
resource: "bigquery://bq-da-common.cman_data.cm_new_cust_monthly"
tags: ["tier2", "agg_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# cm_new_cust_monthly

- **Project.Dataset.Table**: `bq-da-common.cman_data.cm_new_cust_monthly`
- **Grain**: 일 × 시간대(hm) × part
- **Partition**: `ord_date` (DATE)
- **Key columns**: ord_date+hm+part
- **Used for**: 신규 고객 30분 단위 시간대 분석
- **Standard filter required**: N
- **Source confidence**: mid
- **Note**: part 컬럼 의미 [v4 검증대기]

# Pitfalls

hm = '23:00' 또는 '23:30' 형식 (30분 단위)
