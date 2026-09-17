---
type: "BigQuery Table"
title: "dashboard_bs_trend_catg"
description: "BS(Basket Size) 카테고리별 트렌드 대시보드 SoT"
resource: "bigquery://bq-da-common.mkt_data.dashboard_bs_trend_catg"
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

# dashboard_bs_trend_catg

- **Project.Dataset.Table**: `bq-da-common.mkt_data.dashboard_bs_trend_catg`
- **Grain**: 일 × catg_1 × ptype × week_no
- **Partition**: `ord_date` (DATE)
- **Key columns**: ord_date+catg_1_nm+ptype
- **Used for**: BS(Basket Size) 카테고리별 트렌드 대시보드 SoT
- **Standard filter required**: N (집계됨)
- **Source confidence**: mid
- **Note**: [v4 검증대기]

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `ord_date` | DATE | - | YES | 주문일자(파티션 키) | - | 파티션 키. 일별 그레인. 카테고리×ptype 결합 시 grain 단위 확인 필요 (V5 EBIT 매칭 시 주차/월차 환산 주의) |
| `catg_1_nm` | STRING | 코드 | YES | 대카테고리명 | - | cm_catg_team_info의 catg_1_nm과 동일 체계 (Q7 JOIN 키) |
| `ptype` | STRING | 코드 | YES | 1p/3p 구분 | - | F913 ptype 적재 패턴 — 1p/3p/tot 3값 추정. tot은 1p+3p 합산. F926 daily_nmart 동일적재 함정 패턴과 비교 필요 |
| `week_no` | INT64 | 코드 | YES | 주차 번호(연중) | - | 연 단위 주차 vs 누적 주차 구분 필요 |
| `gmv2` | INT64 | 원 | YES | 컬리판매가 기준 GMV (=GMV2) | - | EBIT Dashboard와 매칭 (V5) — Summary_Daily_EBIT 자료와 일자×카테고리 합산 일치 검증 |
| `cnt` | INT64 | 건 | YES | 판매 수량 | - | ord_n과 다른 차원 (ord_n=주문건수, cnt=상품수량) |
| `ord_n` | INT64 | 건 | YES | 주문건수 (BS=gmv2/ord_n 의 분모) | - | BS=gmv2/ord_n의 분모. ptype별/카테고리별 분할 적재 가능성 — F913·F926 패턴 확인 필요 |

# Pitfalls

BS = gmv2 / ord_n

# Joins

- dashboard_bs_trend
- [dashboard_arpu_trend_catg](./dashboard_arpu_trend_catg.md)
