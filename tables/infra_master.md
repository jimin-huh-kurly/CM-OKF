---
type: "BigQuery Table"
title: "infra_master"
description: "사내 표준 코드·제외 마스터 보관. user_key=\"exclude_mcd\"가 표준 매출 필터에서 제외할 마스터 코드 목록"
resource: "bigquery://bq-datafarm.data_warehouse.infra_master"
tags: ["tier1", "config_table"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# infra_master

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.infra_master`
- **Grain**: user_key × user_value
- **Partition**: `-` (-)
- **Key columns**: user_key
- **Used for**: 사내 표준 코드·제외 마스터 보관. user_key="exclude_mcd"가 표준 매출 필터에서 제외할 마스터 코드 목록
- **Standard filter required**: N
- **Source confidence**: high

# Pitfalls

표준 매출 필터에 반드시 LEFT JOIN 또는 NOT IN 으로 활용

# Joins

- [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md)
