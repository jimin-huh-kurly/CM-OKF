---
type: "BigQuery Table"
title: "mkt_crm_grp_mthly"
description: "월별 CRM 그룹 세그 (장미/이탈/유지 등)"
resource: "bigquery://bq-da-common.cman_data.mkt_crm_grp_mthly"
tags: ["tier2", "dim_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# mkt_crm_grp_mthly

- **Project.Dataset.Table**: `bq-da-common.cman_data.mkt_crm_grp_mthly`
- **Grain**: 고객 × 월
- **Partition**: `dt` (DATE)
- **Key columns**: cust_no, dt
- **Used for**: 월별 CRM 그룹 세그 (장미/이탈/유지 등)
- **Standard filter required**: N
- **Source confidence**: mid

# Pitfalls

cust_seg_master_1d와 중복 가능. 어떤게 SoT인지 [v4 검증대기]

# Joins

- [cust_seg_master_1d](./cust_seg_master_1d.md)
