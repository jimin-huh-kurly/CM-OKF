---
type: "BigQuery Table"
title: "cust_seg_master_1d"
description: "RFM 세그·연속유지·CRM그룹·멤버스여부 매스터"
resource: "bigquery://bq-datafarm.data_warehouse.cust_seg_master_1d"
tags: ["tier1", "dim_mart"]
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

# cust_seg_master_1d

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.cust_seg_master_1d`
- **Grain**: 고객(cust_no) × 월(reg_ym)
- **Partition**: `reg_ym` (DATE)
- **Key columns**: cust_no, reg_ym
- **Used for**: RFM 세그·연속유지·CRM그룹·멤버스여부 매스터
- **Standard filter required**: N
- **Source confidence**: high
- **Note**: cust_crm_grp → 매출쿼리에서 신규/기존/유지/회복 4분류로 추가매핑

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `reg_ym` | DATE | 월 | YES | 세그 기준 월 | - | 월 스냅샷 성격. 일별 분석 시 reg_ym 기준 조인 |
| `cust_no` | INT64 | 키 | YES | 고객번호 | - | - |
| `cust_crm_grp` | STRING | 코드 | YES | CRM 세그먼트 그룹 | 실데이터 관측(2026-04-01 기준): 범신규, 기존, 장미, 이탈, 일반_0_7, 일반_7_15, 프렌즈, 화이트, 신규, 라벤더, 탈퇴, 퍼플, 더퍼플 — "회복" 코드는 2025-06~2025-12에 0건(분리되어 저장됨) | 적재쿼리에서 mapping: 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플→유지, 장미→장미, 이탈→이탈, 범신규→범신규, 신규→신규, 기존→기존전환, else→탈퇴. 2025.08.20 "회복→장미/이탈 분리" 이력은 별도 가공 단계에서 발생(이 테이블은 이미 분리저장) |
| `consecutive_retain_class` | STRING | 코드 | YES | 연속유지 분류 | C1=비연속유지, 그 외=연속유지 (적재쿼리 cc_flg 분기 기준) | 전체 코드값 목록은 사내 문서 필요 |
| `month_ord_pay_cum_sum` | INT64 | 원 | YES | 월 누적 결제액 | - | - |
| `join_dt` | DATETIME | 시각 | YES | 가입일시 | - | - |
| `first_ord_dt` | DATETIME | 시각 | YES | 최초 주문일시 | - | - |
| `last_ord_dt` | DATETIME | 시각 | YES | 최종 주문일시 | - | - |
| `members_yn` | STRING | 코드/라벨 | YES | 멤버스 여부(자체 컬럼) | - | members_yn vs 외부 조인 기반 멤버스 식별의 정합성 확인 필요 |
| `CORE_PLUS` | STRING | 코드/라벨 | YES | CORE/PLUS 구분 (멤버스 플랜) | - | - |
| `update_dt` | DATETIME | 시각 | YES | 적재/수정 시각 | - | - |
| `frequency_grp_nm` | STRING | 코드/라벨 | YES | RFM-F 그룹 | - | RFM 구간 정의는 사내 문서 필요 |
| `monetary_grp_nm` | STRING | 코드/라벨 | YES | RFM-M 그룹 | - | RFM 구간 정의는 사내 문서 필요 |
| `fm_segment_nm` | STRING | 코드/라벨 | YES | FM 세그명 | - | RFM 구간 정의는 사내 문서 필요 |

# Pitfalls

reg_ym 파티션 필수. cust_crm_grp 13개 코드값 (프렌즈/화이트/라벤더/일반/퍼플/더퍼플/장미/이탈/범신규/신규/기존/그외)

# Joins

- [std_ord_prd_mthly_1d via cust_no](./std_ord_prd_mthly_1d.md)
- [daily_member_1d via cust_no=m_no](./daily_member_1d.md)
