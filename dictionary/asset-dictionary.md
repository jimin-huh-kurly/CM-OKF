---
type: "Reference"
title: "손익 관련 자산 컬럼 노트 (검증 대기 초안)"
description: "mem_seg_metric_daily / non_membership_biz_plan_main_pfm / dashboard_bs_trend_catg 컬럼에 대한 검증 대기 상태의 해석 초안."
tags: ["dictionary", "draft", "profitability"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: asset-dict-draft
    resource: "kurly-analytics/data/dictionary/a_asset_dict_draft.csv"
    title: "Kurly Asset Dictionary (Draft)"
---

# 개요

이 문서는 [column_dictionary](../tables/index.md)보다 앞서 작성된 **검증 대기 상태의 컬럼 해석 초안**이다. "추정"·"검증 필요"로 표시된 항목은 아직 확정된 정의가 아니므로, 사용 전 원본 소스로 재확인이 필요하다. 대상 테이블은 [mem_seg_metric_daily](../tables/mem_seg_metric_daily.md), [non_membership_biz_plan_main_pfm](../tables/non_membership_biz_plan_main_pfm.md), [dashboard_bs_trend_catg](../tables/dashboard_bs_trend_catg.md)이다.

# mem_seg_metric_daily (세그별 손익 일별 마트)

| 컬럼 | 정의(초안) | 검증 필요 사항 |
|---|---|---|
| ord_date | 주문일(KST 23시 환산 규약 가정) | 다른 BI 마트와 동일 규약인지 확인 필요 |
| cust_crm_grp | CRM 세그 그룹 (유지/회복/장미/이탈/기존/신규/범신규/탈퇴 8분류 추정) | 검증 필요 |
| mem_flg | 멤버스 여부 플래그 | 다른 테이블의 유사 컬럼과 명칭이 달라 일관성 점검 필요 |
| gmv2 | GMV2(VAT 포함, 컬리 분담 후) — 손익 산출의 기본 매출 | 손익 대시보드의 gmv2와 매칭 검증 필요 |
| ord_n | 주문 건수(합주문 단위 distinct count 추정) | 다른 마트의 주문건수 시리즈와 일관성 점검 필요 |
| cust_n | 구매 고객 수(distinct count) | 세그 합산 시 중복 가능성 |
| bs | Basket Size = gmv2 / ord_n | 월별 코호트 마트의 동명 지표와 정의 동일성 검증 필요 |
| arpu | ARPU = gmv2 / cust_n | BS(주문당)와 ARPU(고객당) 혼동 주의 |
| freq | 구매빈도 = ord_n / cust_n | 월별 코호트 마트의 동명 지표와 정의 동일성 검증 필요 |

# non_membership_biz_plan_main_pfm (비멤버스 사업계획 대비 실적)

| 컬럼 | 정의(초안) | 검증 필요 사항 |
|---|---|---|
| membership_flg | 멤버스 여부(특정 값은 비멤포지션을 의미하는 필터로 사용됨) | 다른 테이블의 유사 컬럼과 명칭·값 일관성 검증 필요 |
| mem_new_flg | 멤버스 신규 여부 | membership_flg와의 차이 명확화 필요 |
| seg | 세그(유지/회복/신규/기존 4값) | 다른 마트의 8분류를 축약한 것으로 추정, 명칭 차이(기존 vs 기존전환) 주의 |
| seg_new | 신규 세그 분류 | 정의 미확정, 미사용 가능성 |
| cp_flg | 쿠폰 분류 플래그(주로 'crm'/'capa' 2값 사용) | 다른 마트의 18조건 체계를 축약한 것으로 추정, 일관성 검증 필요 |
| ptype | 사업유형(1p/전체/3p) | 대소문자 표기 혼재 여부 점검 |

# dashboard_bs_trend_catg (카테고리별 BS/ARPU 트렌드 대시보드)

| 컬럼 | 정의(초안) | 검증 필요 사항 |
|---|---|---|
| catg_1_nm | 카테고리 대분류 | 상품 마스터 카테고리 뷰와 동일 체계로 조인 가능 |
| week_no | 주차 번호 | 연 단위 주차인지 누적 주차인지 구분 필요 |
| cnt | 수량(판매 단위 카운트 추정) | 주문건수(ord_n)와는 다른 차원(상품 수량 vs 주문 건수) |

# 공통 소스 참고

이 초안의 각 항목은 `cd_data.daily_coupon_em_1h`(distinct 추출) 및 관련 손익 대시보드 자료와의 교차 검증을 전제로 작성되었으며, 최종 확정 전까지는 참고용으로만 사용한다.
