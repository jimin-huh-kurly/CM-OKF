---
type: "BigQuery Table"
title: "live_gmv_margin"
description: "라이브커머스 GMV·마진 분석"
resource: "bigquery://bq-da-common.mkt_data.live_gmv_margin"
tags: ["tier2", "margin_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# live_gmv_margin

- **Project.Dataset.Table**: `bq-da-common.mkt_data.live_gmv_margin`
- **Grain**: 라이브×카테고리×일
- **Partition**: `live_date` (STRING)
- **Key columns**: live_date+brand+cat_1+cat_2+cat_3
- **Used for**: 라이브커머스 GMV·마진 분석
- **Standard filter required**: N
- **Source confidence**: mid
- **Note**: achievement_rate=달성률, target=목표 보유

# Pitfalls

live_date가 STRING. 비교시 PARSE_DATE 필요
