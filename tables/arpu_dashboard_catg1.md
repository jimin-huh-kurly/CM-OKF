---
type: "BigQuery Table"
title: "arpu_dashboard_catg1"
description: "ARPU 주차별 카테고리 대시보드"
resource: "bigquery://bq-da-common.cman_data.arpu_dashboard_catg1"
tags: ["tier2", "agg_mart"]
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

# arpu_dashboard_catg1

- **Project.Dataset.Table**: `bq-da-common.cman_data.arpu_dashboard_catg1`
- **Grain**: 주차 × 대카테고리
- **Partition**: `-` (-)
- **Key columns**: year+weeknum+catg_1_nm
- **Used for**: ARPU 주차별 카테고리 대시보드
- **Standard filter required**: N
- **Source confidence**: high

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `year` | FLOAT64 | - | YES | 연도 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `weeknum` | FLOAT64 | - | YES | 주차 번호 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `catg_1_nm` | STRING | - | YES | 대카테고리 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `gmv2` | FLOAT64 | - | YES | GMV2 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `n_cust` | FLOAT64 | - | YES | 구매자수 | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `arpu` | FLOAT64 | - | YES | ARPU (=gmv2/n_cust) | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `arpu_1week_before` | FLOAT64 | - | YES | 1주 전 ARPU | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `arpu_2week_before` | FLOAT64 | - | YES | 2주 전 ARPU | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `arpu_3week_before` | FLOAT64 | - | YES | 3주 전 ARPU | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `arpu_4week_before` | FLOAT64 | - | YES | 4주 전 ARPU | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `weeek1_perc` | FLOAT64 | - | YES | 1주 전 대비 변화율(%) | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `weeek2_perc` | FLOAT64 | - | YES | 2주 전 대비 변화율(%) | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `weeek3_perc` | FLOAT64 | - | YES | 3주 전 대비 변화율(%) | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |
| `weeek4_perc` | FLOAT64 | - | YES | 4주 전 대비 변화율(%) | - | [v4 보강 — 환경회복 후 검증 필요] week_before/perc 컬럼명 오타(weeek1_perc 등)는 사내 명명 그대로 유지 |

# Pitfalls

arpu = gmv2/n_cust. weeek_perc 컬럼명 오타

# Joins

- arpu_dashboard_catg2
- [dashboard_arpu_trend_catg](./dashboard_arpu_trend_catg.md)
