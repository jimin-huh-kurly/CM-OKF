---
type: "BigQuery Table"
title: "daily_member_1d"
description: "일별 활성 회원 추적. au_yn_app(앱활성) au_yn_homerecomm(홈추천활성) lastm_crm_group(전월CRM그룹)"
resource: "bigquery://bq-datafarm.data_warehouse.daily_member_1d"
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

# daily_member_1d

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.daily_member_1d`
- **Grain**: 회원(m_no) × 일(pdt)
- **Partition**: `pdt` (DATE)
- **Key columns**: m_no, pdt
- **Used for**: 일별 활성 회원 추적. au_yn_app(앱활성) au_yn_homerecomm(홈추천활성) lastm_crm_group(전월CRM그룹)
- **Standard filter required**: N
- **Source confidence**: mid

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `pdt` | DATE | 일자 | YES | 활동/관측 기준일자(일별 스냅샷 키) | - | 파티션 강제는 아니나 행 수 매우 큼(31일 약 26.2M행) — 기간 필터 사실상 필수. 최근 31일 distinct m_no 약 408만 |
| `m_no` | INT64 | 키 | YES | 회원 식별자(회원번호) | - | cust_no와 동일 키 가정 금지 — 사내 매핑 확인 필요 |
| `au_yn_app` | INT64 | yn(0/1) | YES | 앱 활동 여부(DAU 플래그) | - | 0/1 또는 NULL/1 형태. 집계 시 MAX 또는 IF(>0,1,0) 정규화 권장 |
| `au_yn_mweb` | INT64 | yn(0/1) | YES | 모바일 웹 활동 여부 | - | - |
| `au_yn_dweb` | INT64 | yn(0/1) | YES | 데스크탑 웹 활동 여부 | - | - |
| `au_yn_market` | INT64 | yn(0/1) | YES | 마켓컬리(식품) 영역/도메인 활동 | - | - |
| `au_yn_beauty` | INT64 | yn(0/1) | YES | 뷰티컬리 영역/도메인 활동 | - | - |
| `au_yn_search` | INT64 | yn(0/1) | YES | 검색 이용 활동 | - | - |
| `au_yn_homerecomm` | INT64 | yn(0/1) | YES | 홈 추천 영역 관련 활동(추정) | - | ⚠️ 노출인지 클릭/이용인지 컬럼명만으로 구분 불가 — SoT 확인 필수 |
| `au_yn_paid` | INT64 | yn(0/1) | YES | 유료매체/광고 유입 여부 | - | ⚠️ paid 범위(검색광고/디스플레이/리타겟/CPA)와 어트리뷰션 룰 불명 — 정의 확인 필요 |
| `au_yn_kalog` | INT64 | yn(0/1) | YES | 내부 로그 소스 활동(추정: Kurly Activity Log?) | - | ⚠️ 정의 불명 — 데이터 오너 확인 필요 |
| `cv_yn_ord_homerecomm` | INT64 | yn(0/1) | YES | 홈추천 경로 주문 전환 여부 | - | ⚠️ 전환 윈도우(당일 vs lookback N일) 미확정 |
| `cv_yn_ord_search` | INT64 | yn(0/1) | YES | 검색 경로 주문 전환 여부 | - | ⚠️ 전환 윈도우 미확정 |
| `cv_yn_pur_onmarket` | INT64 | yn(0/1) | YES | 마켓컬리에서 구매 전환 여부 | - | - |
| `cv_yn_pur_onbeauty` | INT64 | yn(0/1) | YES | 뷰티컬리에서 구매 전환 여부 | - | - |
| `cv_yn_control` | INT64 | yn(0/1) | YES | 컨트롤 그룹 여부(추정) | - | ⚠️ 어떤 실험의 컨트롤인지 확정 불가 — 범용 라벨인지 특정 대시보드 전용인지 확인 필요 |
| `lastm_crm_group` | STRING | 코드 | YES | 직전 달(last month) 기준 CRM 세그 | 유지, 신규, 회복_기타, 회복_범신규, 기존 (사내 쿼리에서 사용된 값. 전수 확인 필요) | cust_seg_master_1d.cust_crm_grp(장미/이탈/탈퇴/프렌즈/화이트/라벤더/일반_*/퍼플/더퍼플 등)와 코드값 체계가 다름 — 매핑표 필요 |
| `lastm_yn_members` | INT64 | yn(0/1) | YES | 직전 달 기준 멤버스 구독 여부 | - | 주문 당시 멤버스 여부(std_ord_prd_mthly_1d.membership_code)와는 시점이 다름 |
| `update_dt` | DATETIME | 시각 | YES | 적재/업데이트 시각 | - | - |

# Pitfalls

m_no = cust_no 동일키 여부는 [v4 검증대기]. 매핑률 95%+ 가정

# Joins

- [cust_seg_master_1d via cust_no](./cust_seg_master_1d.md)
