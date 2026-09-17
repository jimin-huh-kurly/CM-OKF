---
type: "BigQuery Table"
title: "mem_seg_metric_daily"
description: "일별 세그 매출 마트 (bs/arpu/freq 포함)"
resource: "bigquery://bq-da-common.cman_data.mem_seg_metric_daily"
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

# mem_seg_metric_daily

- **Project.Dataset.Table**: `bq-da-common.cman_data.mem_seg_metric_daily`
- **Grain**: 일 × CRM세그 × 멤버스플래그
- **Partition**: `ord_date` (DATE)
- **Key columns**: ord_date+cust_crm_grp+mem_flg
- **Used for**: 일별 세그 매출 마트 (bs/arpu/freq 포함)
- **Standard filter required**: N (집계됨)
- **Source confidence**: mid

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `ord_date` | DATE | 일 | YES | 주문일 (KST 23시 환산 추정, 다른 BI 마트와 동일 규약 가정 필요) | - | cust_seg_master_1d / mkt_metric_home_1d의 reg_ym과 다른 단위 (일 vs 월) \| SoT_ref: Q8 (시스템 프롬프트 노출, 쿼리 본문 미명시) |
| `cust_crm_grp` | STRING | - | YES | 고객 CRM 세그 (F912 5분류 표준: 유지/회복(=이탈+장미+범신규)/기존전환/신규전환/탈퇴). 회복=장미+이탈 통합은 F917 2025.08.20 합의 — 인계 v3 § 8.1 | - | 비멤버스 세그용 (mkt_metric_home_1d의 cust_crm_grp_new와 같은 변형일 가능성) \| SoT_ref: F926(daily_nmart) 세그 8분류 패턴 참고 |
| `mem_flg` | STRING | - | YES | 멤버스 여부 플래그 (members / NULL 또는 - 추정) | - | non_membership_biz_plan_main_pfm의 membership_flg와 같은 의미일 가능성. 명칭 다름 — 일관성 점검 필요 \| SoT_ref: mkt_metric_home_1d의 members_flg와 비교 |
| `gmv2` | INT64 | 원 | YES | GMV2 (VAT included, kurly 분담 후) — 손익 산출의 기본 매출 | - | EBIT Dashboard의 gmv2와 매칭 검증 필요 (V5) \| SoT: Summary_Daily_EBIT (★★) |
| `ord_n` | INT64 | 건 | YES | 주문 건수 (group_ord_cd 단위 distinct count 추정) | - | mkt_metric_home_1d의 주문건수_* 시리즈와 일관성 점검 필요 \| SoT_ref: Q2/Q5 주문건수 패턴 |
| `cust_n` | INT64 | 명 | YES | 구매 고객 수 (cust_no distinct count) | - | 비멤포지션(전체) vs 세그 합산 시 중복 가능성 |
| `bs` | FLOAT64 | 원/주문 | YES | Basket Size (장바구니) — 1주문당 매출 = gmv2/ord_n. GMV = BS × 주문수 = ARPU × 고객수. ARPU(1인당)와 다른 차원 — 인계 v3 § 8.5 F910 BS vs ARPU 정의 | - | cohort_1m의 p1_BS와 정의 동일성 검증 필요 \| SoT_ref: Q3 p1_BS 패턴 |
| `arpu` | FLOAT64 | 원/명 | YES | ARPU = 1인당 누적매출 = gmv2/cust_n = 객단가. ARPU = BS × freq 관계. BS(1주문당)와 다른 차원 — 인계 v3 § 8.5 F910 | - | BS(주문당) vs ARPU(고객당) 혼동 위험. 사용자 정의 명확 필요 \| SoT_ref: Q3 p1_ARPU, F910 BS vs ARPU 구분 |
| `freq` | FLOAT64 | 건/명 | YES | 구매 빈도 — ord_n / cust_n (고객 1명당 평균 주문 수) | - | cohort_1m의 p1_freq와 정의 동일성 검증 필요 \| SoT_ref: Q3 p1_freq 패턴 |

# Pitfalls

bs=gmv2/ord_n, arpu=gmv2/cust_n, freq=ord_n/cust_n 추론. [v4 검증대기]

# Joins

- [seg_mem_margin_cm (월버전)](./seg_mem_margin_cm.md)
