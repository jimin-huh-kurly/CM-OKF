---
type: "BigQuery Table"
title: "mkt_metric_home_1d"
description: "홈 메인 KPI 마트. 모수 8종 + 쿠폰 5종 분해 + 할인 분담 보유"
resource: "bigquery://bq-da-common.cman_data.mkt_metric_home_1d"
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

# mkt_metric_home_1d

- **Project.Dataset.Table**: `bq-da-common.cman_data.mkt_metric_home_1d`
- **Grain**: 일
- **Partition**: `stnd_date` (DATE)
- **Key columns**: stnd_date
- **Used for**: 홈 메인 KPI 마트. 모수 8종 + 쿠폰 5종 분해 + 할인 분담 보유
- **Standard filter required**: N (이미 집계됨)
- **Source confidence**: high
- **Note**: 검증식: 총_모수 = 멤버스+비멤유지+장미+이탈+기존+신규+범신규+탈퇴

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `ord_mm` | DATE | 월 | YES | 기준월 (월초 날짜, DATE_TRUNC(ord_date, MONTH)) | - | 23시 환산 후 월 산정 (HOUR>=23 → DATE+1 후 월 절단) |
| `stnd_date` | DATE | 일자 | YES | 기준일 (1P/3P 중 가장 먼저 주문한 일자, LEAST(first_ord_date_1p, first_ord_date_3p)) | - | 구매 안한 고객은 LAST_DAY(ord_mm)로 채워짐 / 23시 환산 반영됨 |
| `모수_비멤유지` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `모수_비멤회복` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `모수_비멤장미` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=장미 (장미) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='장미' ← 장미 | - |
| `모수_비멤이탈` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=이탈 (이탈) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `모수_비멤기존` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `모수_비멤신규` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=신규 (신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='신규' ← 신규 | - |
| `모수_비멤범신규` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=범신규 (범신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `모수_비멤탈퇴` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `모수_유지멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `모수_회복멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `모수_장미멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=장미 (장미) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='장미' ← 장미 | - |
| `모수_이탈멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=이탈 (이탈) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `모수_기존멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `모수_신규멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=신규 (신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='신규' ← 신규 | - |
| `모수_범신규멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=범신규 (범신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `모수_탈퇴멤포` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `모수_멤버스_유지` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `모수_멤버스_회복` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `모수_멤버스_장미` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=장미 (장미) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='장미' ← 장미 | - |
| `모수_멤버스_이탈` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=이탈 (이탈) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `모수_멤버스_기존` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `모수_멤버스_신규` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=신규 (신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='신규' ← 신규 | - |
| `모수_멤버스_범신규` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=범신규 (범신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `모수_멤버스_탈퇴` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `모수_연속유지` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) — 연속유지 한정 (cc_flg='연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `모수_비연속유지` | INT64 | 명 | YES | 월 기준 회원 모수 (cust_seg_master_1d.reg_ym=S_DATE) — 비연속유지 한정 (cc_flg='비연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `구매자수_비멤유지` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `구매자수_비멤회복` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `구매자수_비멤장미` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=장미 (장미) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='장미' ← 장미 | - |
| `구매자수_비멤이탈` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=이탈 (이탈) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `구매자수_비멤기존` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `구매자수_비멤신규` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=신규 (신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='신규' ← 신규 | - |
| `구매자수_비멤범신규` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=범신규 (범신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `구매자수_비멤탈퇴` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `구매자수_유지멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `구매자수_회복멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `구매자수_장미멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=장미 (장미) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='장미' ← 장미 | - |
| `구매자수_이탈멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=이탈 (이탈) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `구매자수_기존멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `구매자수_신규멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=신규 (신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='신규' ← 신규 | - |
| `구매자수_범신규멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=범신규 (범신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `구매자수_탈퇴멤포` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `구매자수_멤버스_유지` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `구매자수_멤버스_회복` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `구매자수_멤버스_장미` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=장미 (장미) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='장미' ← 장미 | - |
| `구매자수_멤버스_이탈` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=이탈 (이탈) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `구매자수_멤버스_기존` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `구매자수_멤버스_신규` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=신규 (신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='신규' ← 신규 | - |
| `구매자수_멤버스_범신규` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=범신규 (범신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `구매자수_멤버스_탈퇴` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `구매자수_연속유지` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) — 연속유지 한정 (cc_flg='연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `구매자수_비연속유지` | INT64 | 명 | YES | 월 기준 구매자수 (중복 제거, 1P+3P 통합) — 비연속유지 한정 (cc_flg='비연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `CRM_구매자수_비멤유지` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `CRM_구매자수_비멤회복` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `CRM_구매자수_비멤장미` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=장미 (장미) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='장미' ← 장미 | - |
| `CRM_구매자수_비멤이탈` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=이탈 (이탈) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `CRM_구매자수_비멤기존` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `CRM_구매자수_비멤신규` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=신규 (신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='신규' ← 신규 | - |
| `CRM_구매자수_비멤범신규` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=범신규 (범신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `CRM_구매자수_비멤탈퇴` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `CRM_구매자수_유지멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `CRM_구매자수_회복멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `CRM_구매자수_장미멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=장미 (장미) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='장미' ← 장미 | - |
| `CRM_구매자수_이탈멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=이탈 (이탈) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `CRM_구매자수_기존멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `CRM_구매자수_신규멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=신규 (신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='신규' ← 신규 | - |
| `CRM_구매자수_범신규멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=범신규 (범신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `CRM_구매자수_탈퇴멤포` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `CRM_구매자수_멤버스_유지` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | - |
| `CRM_구매자수_멤버스_회복` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) |
| `CRM_구매자수_멤버스_장미` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=장미 (장미) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='장미' ← 장미 | - |
| `CRM_구매자수_멤버스_이탈` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=이탈 (이탈) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='이탈' ← 이탈 | - |
| `CRM_구매자수_멤버스_기존` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | - |
| `CRM_구매자수_멤버스_신규` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=신규 (신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='신규' ← 신규 | - |
| `CRM_구매자수_멤버스_범신규` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=범신규 (범신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='범신규' ← 범신규 | - |
| `CRM_구매자수_멤버스_탈퇴` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 |
| `CRM_구매자수_연속유지` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) — 연속유지 한정 (cc_flg='연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `CRM_구매자수_비연속유지` | INT64 | 명 | YES | 월 기준 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 CRM으로 한번이라도 구매) — 비연속유지 한정 (cc_flg='비연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `CRM_쿠폰비용_비멤유지` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_비멤회복` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_비멤장미` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=장미 (장미) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='장미' ← 장미 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_비멤이탈` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=이탈 (이탈) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_비멤기존` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_비멤신규` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=신규 (신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='신규' ← 신규 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_비멤범신규` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=범신규 (범신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_비멤탈퇴` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_유지멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_회복멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_장미멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=장미 (장미) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='장미' ← 장미 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_이탈멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=이탈 (이탈) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='이탈' ← 이탈 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_기존멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_신규멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=신규 (신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='신규' ← 신규 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_범신규멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=범신규 (범신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='범신규' ← 범신규 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_탈퇴멤포` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_유지` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_회복` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_장미` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=장미 (장미) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='장미' ← 장미 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_이탈` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=이탈 (이탈) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_기존` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_신규` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=신규 (신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='신규' ← 신규 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_범신규` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=범신규 (범신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_멤버스_탈퇴` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `CRM_쿠폰비용_연속유지` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) — 연속유지 한정 (cc_flg='연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `CRM_쿠폰비용_비연속유지` | INT64 | 원 | YES | 월 기준 CRM 쿠폰 비용 합계 (재무기획 기준, 2025.07.01 수정) — 비연속유지 한정 (cc_flg='비연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `CAPA_쿠폰비용_비멤유지` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_비멤회복` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_비멤장미` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=장미 (장미) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_비멤이탈` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=이탈 (이탈) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_비멤기존` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_비멤신규` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=신규 (신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_비멤범신규` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=범신규 (범신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_비멤탈퇴` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_유지멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_회복멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_장미멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=장미 (장미) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_이탈멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=이탈 (이탈) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_기존멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_신규멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=신규 (신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_범신규멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=범신규 (범신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_탈퇴멤포` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_유지` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_회복` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_장미` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=장미 (장미) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_이탈` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=이탈 (이탈) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_기존` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_신규` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=신규 (신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_범신규` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=범신규 (범신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_멤버스_탈퇴` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 / '케파달성용_무배'(coupon_benefit_type=FREE_SHIPPING+reason2=매출제고)는 별도 cp_flg이며 본 컬럼 미포함 |
| `CAPA_쿠폰비용_연속유지` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 — 연속유지 한정 (cc_flg='연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `CAPA_쿠폰비용_비연속유지` | INT64 | 원 | YES | 월 기준 CAPA(케파달성용) 쿠폰 비용 합계 — 비연속유지 한정 (cc_flg='비연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `GMV2_비멤유지` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_비멤회복` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_비멤장미` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=장미 (장미) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_비멤이탈` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=이탈 (이탈) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_비멤기존` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_비멤신규` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=신규 (신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_비멤범신규` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=범신규 (범신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_비멤탈퇴` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_유지멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_회복멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_장미멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=장미 (장미) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_이탈멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=이탈 (이탈) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_기존멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_신규멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=신규 (신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_범신규멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=범신규 (범신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_탈퇴멤포` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_유지` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_회복` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_장미` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=장미 (장미) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_이탈` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=이탈 (이탈) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_기존` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_신규` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=신규 (신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_범신규` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=범신규 (범신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_멤버스_탈퇴` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `GMV2_연속유지` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) — 연속유지 한정 (cc_flg='연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `GMV2_비연속유지` | INT64 | 원 | YES | 월 기준 GMV2 (컬리판매가기준, gmv_kurly 합계) — 비연속유지 한정 (cc_flg='비연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `주문건수_비멤유지` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_비멤회복` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_비멤장미` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=장미 (장미) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_비멤이탈` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=이탈 (이탈) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_비멤기존` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_비멤신규` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=신규 (신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_비멤범신규` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=범신규 (범신규) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_비멤탈퇴` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=비멤 (members_flg IS NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_유지멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_회복멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_장미멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=장미 (장미) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_이탈멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=이탈 (이탈) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_기존멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_신규멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=신규 (신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_범신규멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=범신규 (범신규) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_탈퇴멤포` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤포 ((멤버스 무관)) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_유지` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=유지 (프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='유지' ← 프렌즈/화이트/라벤더/일반_7_15/일반_0_7/퍼플/더퍼플 (7개 하위 등급) | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_회복` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=회복 (장미 + 이탈 (통합 컬럼군)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='회복' ← 장미 + 이탈 (통합 컬럼군) | 회복 = 장미 + 이탈 통합 (2025.08.20 추가, 분리 컬럼군도 동시 적재) / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_장미` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=장미 (장미) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='장미' ← 장미 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_이탈` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=이탈 (이탈) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='이탈' ← 이탈 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_기존` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=기존 (기존 → 기존전환) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='기존' ← 기존 → 기존전환 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_신규` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=신규 (신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='신규' ← 신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_범신규` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=범신규 (범신규) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='범신규' ← 범신규 | 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_멤버스_탈퇴` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) \| 세그=탈퇴 (그 외 (탈퇴고객)) \| 멤버스 차원=멤버스 (members_flg IS NOT NULL) | cust_crm_grp_new='탈퇴' ← 그 외 (탈퇴고객) | 탈퇴 retroactive (2025.10.01): 전월 seg로 매핑, 직전월 없으면 신규로 처리 → 본 컬럼은 실제 '탈퇴 상태로 잡힌 사람' 아님 / 2026.02.02 추가 컬럼 — 이전 기간 데이터 NULL/0 가능 |
| `주문건수_연속유지` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) — 연속유지 한정 (cc_flg='연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `주문건수_비연속유지` | INT64 | 건 | YES | 월 기준 주문건수 (COUNT DISTINCT group_ord_cd) — 비연속유지 한정 (cc_flg='비연속유지', 멤포 차원만 존재) | cc_flg: 연속유지(C2,C3,...) / 비연속유지(NULL or C1) | 멤포 차원만 존재 (멤버스/비멤 분리 컬럼 없음) / '유지' 세그에만 적용 (cust_crm_grp_new='유지' AND cc_flg=...) |
| `총_모수` | INT64 | 명 | YES | 월 전체 회원 모수 (멤버스+비멤버스, COUNT DISTINCT cust_no) | - | 탈퇴고객은 retroactive로 전월 seg에 포함됨 (2025.10.01) |
| `모수_멤버스` | INT64 | 명 | YES | 월 전체 멤버스 회원 모수 (members_flg IS NOT NULL) | - | 전월 마지막날 멤버스 여부를 당월 키로 변환(LAST_DAY+1) |
| `총_구매자수` | INT64 | 명 | YES | 월 전체 구매자수 (1P+3P 통합, COUNT DISTINCT cust_no) | - | REORDER/EXCHANGE 제외 / gmv_kurly>0 조건 |
| `구매자수_멤버스` | INT64 | 명 | YES | 월 멤버스 구매자수 (members_flg IS NOT NULL) | - | 멤버스 식별은 전월 마지막날+1 기준 — 당월 신규 가입자는 다음 달부터 멤버스로 카운트됨 |
| `구매자수_3P` | INT64 | 명 | YES | 3P만 구매한 고객수 (first_ord_date_1p IS NULL) | - | 1P 구매가 있으면 본 컬럼에 미산입 (2025.09.03 추가) |
| `CRM_총 구매자수` | INT64 | 명 | YES | 월 CRM 쿠폰 사용 구매자수 (1P 또는 3P 중 한 번이라도 CRM 사용) | - | CRM 정의 = cp_flg='crm' (coupon_reason1 IN ('CRM','유저마케팅') 또는 reason2='CRM 외부광고') |
| `CRM_구매자수_멤버스` | INT64 | 명 | YES | 월 CRM 사용 멤버스 구매자수 | - | 멤버스 식별 = 전월 마지막날+1 기준 / CRM 정의 = cp_flg='crm' (1P 또는 3P 한 번이라도) |
| `CRM_구매자수_3P` | INT64 | 명 | YES | CRM으로 3P만 구매한 고객수 (first_ord_date_1p_crm IS NULL AND first_ord_date_3p_crm IS NOT NULL) | - | 1P를 CRM으로 구매한 고객은 본 컬럼에 미산입 |
| `회원모수` | INT64 | 명 | YES | 당일 기준 누적 회원 모수 (core_cust_info_1d.reg_date) | - | core_cust_info_1d 기준, mosu와 다른 소스 |
| `문자수신동의자` | INT64 | 명 | YES | 당일 기준 SMS 수신동의 회원 수 (sms_yn='y') | sms_yn IN ('y','n') | - |
| `가입자` | INT64 | 명 | YES | 당일 신규 가입자 수 (reg_date = DATE(join_dt)) | - | - |
| `쿠폰_전체` | INT64 | 원 | YES | 전체 쿠폰 사용액 합계 (SUM dc_deal_coupon, 모든 cp_flg) | - | cp_flg='구분자없음' 포함 |
| `쿠폰_CRM` | INT64 | 원 | YES | CRM 쿠폰 사용액 (cp_flg='crm') | - | 2025.07.01 재무기획 기준 변경 — 이전 데이터와 비교 시 주의 |
| `쿠폰_멤버스` | INT64 | 원 | YES | 멤버스 쿠폰 사용액 (cp_flg='멤버스') | - | FREE_SHIPPING+멤버십 / coupon_nm LIKE %뷰티%+멤버십 / reason2='멤버십' 3종 통합 |
| `쿠폰_ARPU` | INT64 | 원 | YES | ARPU 향상 목적 쿠폰 사용액 (coupon_reason2='ARPU') | - | coupon_reason2='ARPU' 단일 조건 매핑 — '쿠폰_CRM'과 달리 분류 경계 모호함 적음 |
| `쿠폰_제휴` | INT64 | 원 | YES | 제휴 쿠폰 사용액 (coupon_reason2='제휴') | - | coupon_reason1='제휴' AND reason2='지원'은 별도(커머스_파트너사쿠폰) |
| `쿠폰_뷰티` | INT64 | 원 | YES | 뷰티 쿠폰 사용액 (coupon_reason2='뷰티', 멤버십 제외) | - | coupon_nm LIKE %뷰티% AND reason2='멤버십'은 멤버스로 분류됨 |
| `할인_전체` | INT64 | 원 | YES | 상품 전체 할인액 (SUM dc_deal_tot, std_ord_prd_mthly_1d) | - | 선물하기 주문완료~배송전(deal_status=99) 포함 / b2b·임직원·gmv_kurly=0 제외 |
| `할인_컬리분담` | INT64 | 원 | YES | 컬리 분담 할인액 (dc_deal_tot - dc_sply_share, sply_share_dc_1d 기준) | - | 공급사 분담분 차감 후 잔여 |
| `할인_공급사분담` | INT64 | 원 | YES | 공급사 분담 할인액 (SUM dc_deal_sply_share_tot, sply_share_dc_1d) | - | 별도 테이블 LEFT JOIN — 데이터 없으면 0 처리 |

# Pitfalls

한글 컬럼명 (총_모수, 쿠폰_전체, 할인_컬리분담 등)

# Joins

- 적재 쿼리에 표준필터 정의 보유
