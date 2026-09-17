---
type: "BigQuery Table"
title: "core_cust_info_1d"
description: "고객 마스터. 가입경로/디바이스/추천인 등"
resource: "bigquery://bq-datafarm.data_warehouse.core_cust_info_1d"
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

# core_cust_info_1d

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.core_cust_info_1d`
- **Grain**: 고객(cust_no) × 가입일(reg_date)
- **Partition**: `reg_date` (DATE)
- **Key columns**: cust_no, reg_date
- **Used for**: 고객 마스터. 가입경로/디바이스/추천인 등
- **Standard filter required**: N
- **Source confidence**: high

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `reg_date` | DATE | 일자 | YES | 기준일자(일별 스냅샷) | - | - |
| `cust_no` | INT64 | yn/코드 | YES | 고객번호 | - | - |
| `m_uuid` | STRING | yn/코드 | YES | 회원 UUID | - | - |
| `join_dt` | DATETIME | 시각 | YES | 가입일시 | - | - |
| `cust_crm_grp` | STRING | yn/코드 | YES | CRM 세그(일별 시점) | - | cust_seg_master_1d와 동명 컬럼 정합성 확인 필요(cust_crm_grp/monetary_grp_nm/frequency_grp_nm/fm_segment_nm/consecutive_retain_class) |
| `cust_lovers_grp` | STRING | yn/코드 | YES | 러버스 등급 그룹 | - | - |
| `is_kp_active` | STRING | yn/코드 | YES | 컬리패스(KP) 활성 여부 추정 | - | - |
| `ord_seq` | INT64 | 건/회 | YES | 주문 시퀀스(누적 주문 횟수) | - | - |
| `ord_pay_cum_sum` | INT64 | 원 | YES | 누적 결제액 | - | - |
| `ord_pay_avg` | INT64 | 원 | YES | 평균 주문결제액 | - | - |
| `voc_cum_cnt` | INT64 | 건/회 | YES | VOC 누적 건수 | - | - |
| `review_cum_cnt` | INT64 | 건/회 | YES | 리뷰 누적 건수 | - | - |
| `goods_review_cum_cnt` | INT64 | 건/회 | YES | 상품 리뷰 누적 건수 | - | - |
| `ord_cnt` | INT64 | 건/회 | YES | 주문건수 | - | - |
| `ord_pay` | INT64 | 원 | YES | 결제액 | - | - |
| `dc_coupon` | INT64 | yn/코드 | YES | 쿠폰 사용액 | - | - |
| `dc_point` | INT64 | yn/코드 | YES | 적립금 사용액 | - | - |
| `last_login_dt` | DATETIME | 시각 | YES | 최근 로그인 시각 | - | - |
| `last_ord_dt` | DATETIME | 시각 | YES | 최근 주문 시각 | - | - |
| `last_ord_pay` | INT64 | 원 | YES | 최근 주문 결제액 | - | - |
| `last_ord_term` | INT64 | 건/회 | YES | 최근 주문 텀(일) | - | - |
| `last_sku_cnt` | INT64 | 건/회 | YES | 최근 주문 SKU 수 | - | - |
| `last_unit_cnt` | INT64 | 건/회 | YES | 최근 주문 수량 | - | - |
| `update_dt` | DATETIME | 시각 | YES | 적재/수정 시각 | - | - |
| `sms_yn` | STRING | yn/코드 | YES | SMS 수신동의 여부 (y/n) | - | - |
| `now_first_ord_dt` | DATETIME | 시각 | YES | 컬리나우 첫 주문일시 | - | - |
| `bty_first_ord_dt` | DATETIME | 시각 | YES | 뷰티컬리 첫 주문일시 | - | - |
| `bty_last_ord_dt` | DATETIME | 시각 | YES | 뷰티컬리 최근 주문일시 | - | - |
| `bty_ord_seq` | INT64 | 건/회 | YES | 뷰티 주문 시퀀스 | - | - |
| `bty_crm_grp` | STRING | yn/코드 | YES | 뷰티 CRM 세그 | - | - |
| `normal_user_yn` | STRING | yn/코드 | YES | 정상 사용자 여부 | - | - |
| `last_ord_dt_num` | INT64 | yn/코드 | YES | 최근 주문 경과일 | - | - |
| `last_ord_90_gmv2` | INT64 | 원 | YES | 최근 90일 GMV2 | - | - |
| `last_ord_90_freq` | INT64 | 건/회 | YES | 최근 90일 주문빈도 | - | - |
| `last_ord_90_cycle` | FLOAT64 | 건/회 | YES | 최근 90일 주문 주기 | - | - |
| `monetary_grp_nm` | STRING | yn/코드 | YES | RFM-M 그룹 | - | cust_seg_master_1d와 동명 컬럼 정합성 확인 필요(cust_crm_grp/monetary_grp_nm/frequency_grp_nm/fm_segment_nm/consecutive_retain_class) |
| `frequency_grp_nm` | STRING | 건/회 | YES | RFM-F 그룹 | - | cust_seg_master_1d와 동명 컬럼 정합성 확인 필요(cust_crm_grp/monetary_grp_nm/frequency_grp_nm/fm_segment_nm/consecutive_retain_class) |
| `fm_segment_nm` | STRING | yn/코드 | YES | FM 세그명 | - | cust_seg_master_1d와 동명 컬럼 정합성 확인 필요(cust_crm_grp/monetary_grp_nm/frequency_grp_nm/fm_segment_nm/consecutive_retain_class) |
| `membership_yn` | STRING | yn/코드 | YES | 멤버스 여부 | - | - |
| `consecutive_retain_class` | STRING | yn/코드 | YES | 연속유지 분류 | - | cust_seg_master_1d와 동명 컬럼 정합성 확인 필요(cust_crm_grp/monetary_grp_nm/frequency_grp_nm/fm_segment_nm/consecutive_retain_class) |

# Pitfalls

reg_date 파티션 필수
