---
type: "BigQuery Table"
title: "rp_subscription_member_daily"
description: "멤버스 가입자 일자별 추적"
resource: "bigquery://bq-da-common.mkt_data.rp_subscription_member_daily"
tags: ["tier1", "dim_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# rp_subscription_member_daily

- **Project.Dataset.Table**: `bq-da-common.mkt_data.rp_subscription_member_daily`
- **Grain**: 회원 × 일
- **Partition**: `std_dt` (DATE)
- **Key columns**: member_no, std_dt
- **Used for**: 멤버스 가입자 일자별 추적
- **Standard filter required**: N
- **Source confidence**: mid

# Pitfalls

cust_grp 코드값 확인 필요

# Joins

- [std_ord_prd_mthly_1d via member_no=cust_no](./std_ord_prd_mthly_1d.md)
