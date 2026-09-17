---
type: "BigQuery Table"
title: "dashboard_arpu_trend_catg"
description: "ARPU 카테고리별 트렌드 대시보드"
resource: "bigquery://bq-da-common.mkt_data.dashboard_arpu_trend_catg"
tags: ["tier1", "agg_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
  - id: column_dictionary
    resource: "kurly-analytics/data/dictionary/column_dictionary.csv"
    title: "Kurly Column Dictionary"
---

# dashboard_arpu_trend_catg

- **Project.Dataset.Table**: `bq-da-common.mkt_data.dashboard_arpu_trend_catg`
- **Grain**: 일 × catg_1 × ptype × week_no
- **Partition**: `ord_date` (DATE)
- **Key columns**: ord_date+catg_1_nm+ptype
- **Used for**: ARPU 카테고리별 트렌드 대시보드
- **Standard filter required**: N
- **Source confidence**: mid
- **Note**: [v4 검증대기]

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `flg` | STRING | - | YES | - | - | [v4 보강 — 환경회복 후 검증 필요] |
| `stnd_ymd` | DATE | - | YES | - | - | [v4 보강 — 환경회복 후 검증 필요] |
| `week_no` | INT64 | - | YES | 주차 번호 | - | [v4 보강 — 환경회복 후 검증 필요] |
| `catg_1_nm` | STRING | - | YES | 대카테고리 | - | [v4 보강 — 환경회복 후 검증 필요] |
| `ptype` | STRING | - | YES | 1p/3p 구분 | - | [v4 보강 — 환경회복 후 검증 필요] |
| `gmv2` | INT64 | - | YES | GMV2 | - | [v4 보강 — 환경회복 후 검증 필요] |
| `cnt` | INT64 | - | YES | 판매 수량 | - | [v4 보강 — 환경회복 후 검증 필요] |
| `ord_n` | INT64 | - | YES | 주문건수 | - | [v4 보강 — 환경회복 후 검증 필요] |
| `cust_n` | INT64 | - | YES | 구매자수 | - | [v4 보강 — 환경회복 후 검증 필요] |
| `arpu` | FLOAT64 | - | YES | ARPU (=gmv2/cust_n) | - | [v4 보강 — 환경회복 후 검증 필요] |
| `update_date` | DATE | - | YES | - | - | [v4 보강 — 환경회복 후 검증 필요] |

# Pitfalls

ARPU = gmv2 / cust_n

# Joins

- [dashboard_bs_trend_catg](./dashboard_bs_trend_catg.md)
