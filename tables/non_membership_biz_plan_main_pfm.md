---
type: "BigQuery Table"
title: "non_membership_biz_plan_main_pfm"
description: "비멤버스 사업계획 vs 실적 마트"
resource: "bigquery://bq-da-common.cman_data.non_membership_biz_plan_main_pfm"
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

# non_membership_biz_plan_main_pfm

- **Project.Dataset.Table**: `bq-da-common.cman_data.non_membership_biz_plan_main_pfm`
- **Grain**: 일 × ptype × seg × cp_flg
- **Partition**: `ord_date` (DATE)
- **Key columns**: ord_date+ptype+seg+cp_flg
- **Used for**: 비멤버스 사업계획 vs 실적 마트
- **Standard filter required**: N (집계됨)
- **Source confidence**: high
- **Note**: 사용자 사업계획 시트의 베이스

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `ord_date` | DATE | 일 | YES | 주문일 | - | - (SoT_ref: Q5) |
| `membership_flg` | STRING | - | YES | 멤버스 여부 (Q5에서 = "-" 필터: 비멤포지션 제외 의미) | - | mem_seg_metric_daily.mem_flg와 명칭/값 일관성 검증 필요 (OI119 후속) |
| `mem_new_flg` | STRING | - | YES | 멤버스 신규 여부 (Q5에서 = "-" 필터) | - | membership_flg와 차이 명확히 정의 필요 |
| `seg` | STRING | - | YES | 고객 행동 단계 세그 (F912 5분류). Q5는 4값(유지/회복/신규/기존) 사용 — 탈퇴/범신규/장미/이탈 미세분. mkt_metric_home_1d 8분류의 축약 — 인계 v3 § 8.1 | - | "기존" vs "기존전환" 명칭 차이 (mkt_metric_home_1d는 "기존전환") |
| `seg_new` | STRING | - | YES | seg 5분류 (신규/유지/회복/기존/탈퇴)를 2분류로 축약한 칼럼. CASE WHEN seg IN ('기존','유지','회복') THEN '기유회' WHEN seg='신규' THEN '신규' END. 비멤버스 사업라인 진척 추적용. (F942 근거) | - | OI: 사업부 컨택 필요 |
| `cp_flg` | STRING | - | YES | 쿠폰 분류 플래그 (Q5에서 "crm" / "capa" 2값 명시 사용. mkt_metric_home_1d 18조건의 축약) | - | mkt_metric_home_1d Q2의 18조건 cp_flg와 다른 체계 — 일관성 검증 필요 \| SoT_ref: F922 cp_flg 18조건과 비교 |
| `cust_n` | INT64 | 명 | YES | 고객 수 | - | - |
| `blank` | STRING | - | YES | Placeholder 리터럴 칼럼 — 항상 '-' 값. SELECT 결과의 컬럼명은 'blank'이지만 실제 집계 로직 없음 (', '-' as blank'). 주석에는 '월 uniq 고객수'라 기재되어 있으나 실제 구현은 placeholder. (F943 근거) | - | OI: 컬럼 정의 컨택 필요 |
| `ord_n` | INT64 | 건 | YES | 주문 건수 | - | - (SoT_ref: Q5) |
| `gmv2` | INT64 | 원 | YES | GMV2 | - | EBIT Dashboard gmv2와 매칭 검증 (V5) \| SoT: Summary_Daily_EBIT |
| `ptype` | STRING | - | YES | 사업라인 구분 키 (F909 std_ord 1p/3p와 동일 체계). Q5는 p1/p3/tot 3값 (tot=1p+3p). ptype 단독으로 FBK 식별 불가 — business_type='PARTNER_KURLY_CONSIGNMENT' 결합 필요. 인계 v3 § 6 B24, § 8.2 F909 | - | F913 ptype 패턴 (대문자 P1 vs 소문자 p1 혼재 점검) \| SoT_ref: F913 |

# Pitfalls

membership_flg='-' = 전체합산. seg=[유지/회복/신규/기존]. cp_flg=[crm/capa/전체]

# Joins

- [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md)
