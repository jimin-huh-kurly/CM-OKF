---
type: "BigQuery Table"
title: "seg_mem_margin_cm"
description: "재무 P&L SoT 후보. em=Earned Margin, cm=Contribution Margin"
resource: "bigquery://bq-da-common.cman_data.seg_mem_margin_cm"
tags: ["tier1", "margin_mart"]
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

# seg_mem_margin_cm

- **Project.Dataset.Table**: `bq-da-common.cman_data.seg_mem_margin_cm`
- **Grain**: 월(stnd_mm) × CRM세그 × 멤버스플래그
- **Partition**: `stnd_mm` (DATE)
- **Key columns**: stnd_mm + cust_crm_grp + mem_flg
- **Used for**: 재무 P&L SoT 후보. em=Earned Margin, cm=Contribution Margin
- **Standard filter required**: N
- **Source confidence**: mid
- **Note**: 사용자가 "고객세그별 손익 시트의 베이스" 로 명시

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `stnd_mm` | DATE | 월 | YES | 기준월(파티션 후보) | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `cust_crm_grp` | STRING | 코드 | YES | CRM 세그 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `mem_flg` | STRING | 코드 | YES | 멤버스 여부 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `gmv2` | INT64 | 원 | YES | GMV2 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `arpu` | FLOAT64 | 원 | YES | ARPU = gmv2/cust_n | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `bs` | FLOAT64 | 원 | YES | Basket Size = gmv2/ord_n | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `freq` | FLOAT64 | 회 | YES | 구매빈도 = ord_n/cust_n | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_tot` | INT64 | 원 | YES | 딜 단위 총 할인 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_master_sply_share_tot` | FLOAT64 | 원 | YES | 공급사 분담 마스터 할인 | - | [v4 보강 — 환경회복 후 검증 필요] 공급사 부담. em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_master_kurly_share_tot` | FLOAT64 | 원 | YES | 컬리 분담 마스터 할인 | - | [v4 보강 — 환경회복 후 검증 필요] 컬리 부담. em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_tot` | INT64 | 원 | YES | 딜 쿠폰 총 할인 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dis_coupon_sply` | INT64 | 원 | YES | 공급사 분담 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dis_coupon_kurly` | INT64 | 원 | YES | 컬리 분담 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_mem` | INT64 | 원 | YES | 멤버스 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_crm` | INT64 | 원 | YES | CRM 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_promo` | INT64 | 원 | YES | 프로모션 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_capa` | INT64 | 원 | YES | CAPA(케파달성용) 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_cmmrc` | INT64 | 원 | YES | 커머스 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_pfm` | INT64 | 원 | YES | 퍼포먼스 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_coupon_np` | INT64 | 원 | YES | 제휴(NP) 쿠폰 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `dc_deal_point` | INT64 | 원 | YES | 딜 적립금 사용 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `em` | FLOAT64 | 원 | YES | Earned Margin (영업이익 추정) — 사내 P&L 핵심 | - | [v4 보강 — 환경회복 후 검증 필요] ** 재무 SoT 후보 **. em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `cm` | FLOAT64 | 원 | YES | Contribution Margin (공헌이익 추정) | - | [v4 보강 — 환경회복 후 검증 필요] ** 재무 SoT 후보 **. em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `cust_n` | INT64 | 명 | YES | 구매자수 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |
| `ord_n` | INT64 | 건 | YES | 주문건수 | - | [v4 보강 — 환경회복 후 검증 필요] . em/cm 정확한 산식은 적재쿼리 확인 필요 |

# Pitfalls

[v4 검증대기] em/cm 정확한 산식은 적재쿼리 확인 필요

# Joins

- [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md)
- [mem_seg_metric_daily](./mem_seg_metric_daily.md)
