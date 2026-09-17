---
type: "BigQuery Table"
title: "cm_prd_data_v1"
description: "쿠폰·할인 세부 분해. 16종 컬리분담 할인 + 공급사분담 분해"
resource: "bigquery://bq-da-common.cman_data.cm_prd_data_v1"
tags: ["tier1", "fact_mart"]
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

# cm_prd_data_v1

- **Project.Dataset.Table**: `bq-da-common.cman_data.cm_prd_data_v1`
- **Grain**: 주문상품
- **Partition**: `ord_date` (DATE)
- **Key columns**: group_ord_cd, content_no, deal_no
- **Used for**: 쿠폰·할인 세부 분해. 16종 컬리분담 할인 + 공급사분담 분해
- **Standard filter required**: Y
- **Source confidence**: high
- **Note**: 컬리분담/공급사분담 세부 카테고리 분해의 정수

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `ord_date` | DATE | 일자 | YES | 주문일자(파티션 키 후보) | - | - |
| `seg` | STRING | 코드 | YES | 고객 세그 | - | - |
| `master_cd` | STRING | 키 | YES | 마스터 상품 코드 | - | - |
| `master_nm` | STRING | 라벨 | YES | 마스터 상품명 | - | - |
| `ptype` | STRING | 코드 | YES | 사업 유형. std_ord_prd_mthly_1d와 코드값 다름. 여기서는 3pl이 FBK 별도 분리됨 | '1p' (1P 직매입) / '3p' (Pure 3P, 파트너 풀필) / '3pl' (FBK = 컬리 위탁) | [B24 검증완료] **std_ord_prd_mthly_1d와 코드값 다름**. std에서는 3p에 FBK+Pure3P 섞이지만, cm은 3pl=FBK 분리. 같은 컬럼명이라 주의 |
| `catg_1_nm` | STRING | 라벨 | YES | 대카테고리 | - | 마트 시점 기준 |
| `catg_2_nm` | STRING | 라벨 | YES | 중카테고리 | - | - |
| `catg_3_nm` | STRING | 라벨 | YES | 소카테고리 | - | - |
| `team_nm` | STRING | 라벨 | YES | 담당 팀 | - | master_cd별로 team_nm 복수값 존재 가능 — 검증 권장. cm_catg_team_info와 IFNULL 조합으로 최신값 보강 관행 |
| `md_nm` | STRING | 라벨 | YES | 담당 MD | - | - |
| `tax_type` | STRING | 코드 | YES | 부가세 타입 | - | - |
| `sply_cd` | STRING | 키 | YES | 공급사 코드 | - | - |
| `sply_nm` | STRING | 라벨 | YES | 공급사명 | - | - |
| `sourcing_type` | STRING | 코드 | YES | 소싱 유형 (1P 내부 분류). 사업 구분(1P/3P/FBK)에는 부적합 | 'NB' (National Brand) / 'ONLY' (컬리 ONLY) / 'PB' (Private Brand) / NULL | [B24 검증완료] 5/11 cm_prd_data_v1에서 모두 NULL. 1P 내부 분류용으로만 활용. 3P/FBK 구분에 사용 금지 |
| `gmv1` | INT64 | 원 | YES | GMV1(정가/리테일가 기준) | - | 주문원장 gmv_retail과 기준일/필터 다르면 불일치 |
| `gmv2` | INT64 | 원 | YES | GMV2(컬리 판매가 기준) | - | 주문원장 gmv_kurly와 정합성 차이 가능: 기준일/취소반영시점/3p 정산/0원 처리 등 |
| `gmv2_ex` | FLOAT64 | 원(VAT제외) | YES | VAT 제외 GMV2 | - | "ex"=excluding VAT 관례상 유력하나 공식 정의는 ETL/문서 확인 필요 |
| `cogs_ex` | FLOAT64 | 원(VAT제외) | YES | VAT 제외 매출원가(COGS) | - | 원가 인식 시점/정산 기준 차이로 주문원장과 직접 reconcile 어려움 |
| `sm` | FLOAT64 | 원 | YES | Sales Margin/Standard Margin 계열(추정) | - | 산식(=gmv2_ex - cogs_ex - …) 확정은 문서 필요 |
| `gmv_promo` | FLOAT64 | 원 | YES | 프로모션 적용 후/관련 GMV 파생 | - | 어떤 프로모션을 포함하는지 정의 필요 |
| `dc_tot_ex` | FLOAT64 | 원(VAT제외) | YES | VAT 제외 총 할인 | - | 주문원장 할인 합과 1:1 매핑되지 않을 수 있음(분담/정산 반영) |
| `dc_sply_ex` | FLOAT64 | 원(VAT제외) | YES | 공급사 부담 할인(VAT 제외) | - | 분담률/정산 기준 변경 시 시계열 주의 |
| `dc_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 컬리 부담 할인(VAT 제외) | - | 검증식: dc_tot_ex = dc_sply_ex + dc_kurly_ex |
| `dc_cmmrc` | FLOAT64 | 원 | YES | 커머스 관련 할인/비용(명칭상) | - | 정의 불명확 — 사내 문서/ETL 필요 |
| `dc_daily_special_deal_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 일일 특가 딜 할인(컬리분담, VAT제외) | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_special_price_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 특가 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_plan_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 기획전 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_catg_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 카테고리 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_prd_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 상품 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_retire_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 퇴점/단종 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_livecommerce_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 라이브커머스 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_all_price_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 전체 가격 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_md_recommend_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | MD 추천 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_all_day_dsct_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 상시할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_all_day_dsct_beauty_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 뷰티 상시할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_compete_price_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 경쟁가격 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_holiday_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 공휴일 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_d1_d5_daily_ex` | FLOAT64 | 원(VAT제외) | YES | D1~D5 일별 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_marketing_ex` | FLOAT64 | 원(VAT제외) | YES | 마케팅 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `dc_kurlynow_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 컬리나우 할인 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `new_dc_tot_ex` | FLOAT64 | 원(VAT제외) | YES | VAT 제외 신규할인 총액 | - | 검증식: 세부 항목 합 = dc_kurly_ex (확인 필요). new_dc_tot_ex는 주문원장 new_dc_deal_tot과 직접 비교 주의 |
| `coupon_ex` | FLOAT64 | 원(VAT제외) | YES | VAT 제외 쿠폰 비용 총액 | - | 주문원장 dc_deal_coupon과 불일치 가능(분담/정산, 멤버십/무배 처리) |
| `cp_sply_ex` | FLOAT64 | 원(VAT제외) | YES | 공급사 부담 쿠폰비(VAT 제외) | - | - |
| `cp_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 컬리 부담 쿠폰비(VAT 제외) | - | 검증식: coupon_ex = cp_sply_ex + cp_kurly_ex |
| `cmmrc_brand_coupon_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 커머스 브랜드 쿠폰(컬리분담) | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `cmmrc_card_coupon_kurly_ex` | FLOAT64 | 원(VAT제외) | YES | 커머스 카드 쿠폰(컬리분담) | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `plcc_card_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | PLCC 카드 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `capa_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | CAPA(케파달성용) 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `crm_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | CRM 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `lovers_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 러버스 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `np_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 제휴(NP) 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `beauty_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 뷰티 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `pfm_newdeal_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 퍼포먼스 신딜 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `membership_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 멤버십 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `arpu_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | ARPU 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `kurlynow_first_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 컬리나우 첫구매 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `promotion_product_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 프로모션 상품 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `all_day_product_coupon_ex` | FLOAT64 | 원(VAT제외) | YES | 상시 상품 쿠폰 | - | 검증식: 세부 항목 합 = cp_kurly_ex (확인 필요) |
| `ad` | FLOAT64 | 원 | YES | 광고/협찬/애드 관련 수익 또는 비용 | - | 수익/비용 sign 정책 확인 필요 |
| `point_ex` | FLOAT64 | 원(VAT제외) | YES | VAT 제외 포인트 비용/사용 | - | "사용액"인지 "비용 인식액"인지 혼동 위험 |
| `dsps_amt_ex` | FLOAT64 | 원(VAT제외) | YES | 폐기액(VAT제외) | - | [정정 F1430] dsps=disposal(폐기), cd_data.disposal_gss_error 동형 \| 정의 불명확 |
| `adj_em` | FLOAT64 | 원 | YES | EM 조정(Adjustment) | - | 산식/대상기간 확인 필요 |
| `update_dt` | DATETIME | 시각 | YES | 적재/수정 시각 | - | - |

# Pitfalls

dc_kurly 16종 분해 검증식 보유. cp_sply/cp_kurly 분해 검증식 보유. **[B24] ptype 코드값이 std_ord_prd_mthly_1d와 다름** (cm은 1p/3p/3pl, 3pl=FBK 별도 분리). sourcing_type은 1P 내부 분류용 (NB/ONLY/PB), 사업 구분(1P/FBK/3P)에 부적합. gmv2(VAT포함) vs gmv2_ex(VAT제외)

# Joins

- [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md)
