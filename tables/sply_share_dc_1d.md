---
type: "BigQuery Table"
title: "sply_share_dc_1d"
description: "공급사 분담 할인 분해. dc_deal_sply_share_tot"
resource: "bigquery://bq-datafarm.data_warehouse.sply_share_dc_1d"
tags: ["tier1", "fact_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# sply_share_dc_1d

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.sply_share_dc_1d`
- **Grain**: 주문상품 × 공급사분담
- **Partition**: `ord_date` (DATE)
- **Key columns**: ord_date+center_cd+master_cd+content_no+deal_no (조인키)
- **Used for**: 공급사 분담 할인 분해. dc_deal_sply_share_tot
- **Standard filter required**: Y
- **Source confidence**: high

# Pitfalls

std_ord_prd_mthly_1d와 조인 시 5개 키 모두 필요

# Joins

- [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md)
