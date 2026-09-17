---
type: "BigQuery Table"
title: "pntr_dashboard_catg1"
description: "PNTR(Penetration) 침투율 대시보드"
resource: "bigquery://bq-da-common.cman_data.pntr_dashboard_catg1"
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

# pntr_dashboard_catg1

- **Project.Dataset.Table**: `bq-da-common.cman_data.pntr_dashboard_catg1`
- **Grain**: 주차 × 대카테고리
- **Partition**: `-` (-)
- **Key columns**: year+weeknum+catg_1_nm
- **Used for**: PNTR(Penetration) 침투율 대시보드
- **Standard filter required**: N
- **Source confidence**: high
- **Note**: 컬럼명으로 산출식 명확

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `year` | FLOAT64 | - | YES | 연도 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `weeknum` | FLOAT64 | - | YES | 주차 번호 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `catg_1_nm` | STRING | - | YES | 대카테고리 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `catg_n_cust` | FLOAT64 | - | YES | 해당 카테고리 구매자수 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `tot_n_cust` | FLOAT64 | - | YES | 전체 구매자수 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `pntr` | FLOAT64 | - | YES | Penetration (=catg_n_cust / tot_n_cust) — 카테고리 침투율 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `pntr_1week_before` | FLOAT64 | - | YES | 1주 전 PNTR | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `pntr_2week_before` | FLOAT64 | - | YES | 2주 전 PNTR | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `pntr_3week_before` | FLOAT64 | - | YES | 3주 전 PNTR | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `pntr_4week_before` | FLOAT64 | - | YES | 4주 전 PNTR | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |

# Pitfalls

PNTR = catg_n_cust / tot_n_cust. weeek1~4_perc 컬럼명 오타(weeek) 사내 그대로

# Joins

- [pntr_dashboard_catg2](./pntr_dashboard_catg2.md)
- pntr_dashboard_v2
