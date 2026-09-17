---
type: "BigQuery Table"
title: "std_ord_prd_mthly_1d"
description: "매출 SoT 1순위. GMV2/주문수/구매자수 산출의 베이스"
resource: "bigquery://bq-datafarm.data_warehouse.std_ord_prd_mthly_1d"
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

# std_ord_prd_mthly_1d

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.std_ord_prd_mthly_1d`
- **Grain**: 주문상품(group_ord_cd × content_no × deal_no)
- **Partition**: `ord_dt` (DATETIME)
- **Key columns**: group_ord_cd, cust_no, content_no, deal_no, master_cd, center_cd
- **Used for**: 매출 SoT 1순위. GMV2/주문수/구매자수 산출의 베이스
- **Standard filter required**: Y
- **Source confidence**: high
- **Note**: 23시 이후 주문은 익일로 환산하는 적재 표준 존재

# Schema

| Column | Type | Unit | Nullable | Business Definition | Code Values | Pitfall |
|---|---|---|---|---|---|---|
| `group_ord_cd` | INT64 | 건(키) | YES | 합주문번호. 주문 건수 산정의 기본 키(1회 결제/주문 단위) | - | 딜라인 단위 테이블이라 동일 group_ord_cd가 여러 행으로 중복됨 → group_ord_pay 등 주문단위 금액 SUM 시 중복합산 위험 |
| `ord_cd` | INT64 | 건(키) | YES | 개별주문번호(합주문 내 분리된 주문) | - | ord_cd로 주문수 세면 합주문 기준 KPI와 불일치 가능 |
| `ord_dt` | DATETIME | 시각 | YES | 주문일시. 파티션 컬럼. KPI 기간 기준 | - | 파티션 필터 없으면 쿼리 실패(full scan 방지). 23시 이후 주문을 익일로 환산하는 관행 있음(최근 30일 HOUR>=23 라인 약 105만, GMV 113억). shift 적용/미적용 리포트 혼재 시 일별 GMV 2.7~4.8억대 흔들림 |
| `content_no` | STRING | 키 | YES | 콘텐츠 번호(상품 노출 단위로 쓰이는 상위 식별자) | - | 콘텐츠/마스터/딜은 계층이 달라 조인 시 키 혼동 주의 |
| `master_cd` | STRING | 키 | YES | 마스터 상품 코드(상품 마스터 식별) | - | 딜/콘텐츠와 혼용 집계 시 중복/누락. 최근 7일 기준 1개 master_cd에 deal_no/content_no가 최대 23개까지 붙는 케이스 존재(예: M00000130400) |
| `deal_no` | INT64 | 키 | YES | 딜 번호(판매 단위). 이 테이블 행 단위(딜상품별 주문행)의 핵심 키 | - | 상품수를 deal_no로 셀지 master_cd로 셀지 KPI 정의 필요 |
| `content_nm` | STRING | 라벨 | YES | 콘텐츠명 | - | 동일 content_no에 이름 변경 이력 있을 수 있음(ANY_VALUE 주의) |
| `master_nm` | STRING | 라벨 | YES | 마스터 상품명 | - | 이름 변경 시 기간/스냅샷 일관성 이슈. 대량주문/임직원판매 패턴 제외 필터에 사용됨 |
| `deal_nm` | STRING | 라벨 | YES | 딜명 | - | 딜명 변경 가능 — 기간 내 최신명 vs 주문 당시 명 정의 필요 |
| `center_cd` | STRING | 코드 | YES | 센터/클러스터 코드(물류 거점) | - | 센터 변경/통합 시 시계열 비교 주의 |
| `group_ord_status` | INT64 | 코드 | YES | 합주문 상태 코드 | 최근 90일 관측: 1, 4, 44, 54 | <40 vs >=40: 라인 25.3M vs 1.04M, GMV2 264.4B vs 12.9B 차이. 코드별 상태명 매핑은 사내 코드북 필요(추측 금지) |
| `ord_status` | INT64 | 코드 | YES | 개별주문 상태 코드 | 최근 90일 관측: 1, 2, 3, 4, 9, 44 | <40 vs >=40: 라인 25.3M vs 1.06M, GMV2 264.2B vs 13.2B |
| `deal_status` | INT64 | 코드 | YES | 딜상품별 주문 상태 코드 | 최근 90일 관측: 1, 2, 3, 4, 44 (코드값 99는 관측 안됨) | <40 vs >=40: 라인 25.2M vs 1.13M, GMV2 263.4B vs 13.9B. "GIFT AND deal_status=99=선물하기 미배송"은 이 테이블에서는 확인 불가(99 없음) — 다른 원천 가능성 |
| `ord_type` | STRING | 코드 | YES | 주문 유형. 표준 매출 필터에서 REORDER/EXCHANGE 제외 | 'NORMAL' (일반) / 'KURLY_NOW' (컬리나우 퀵커머스) / 'BULK' (대량) / 'PICKUP' (픽업) / 'REORDER' (재주문) / 'EXCHANGE' (교환) / 'GIFT' (선물) | [B24 검증완료] 컬리나우는 ord_type=KURLY_NOW (별도 사업 라인 아니라 1P 안에 포함됨, 5/11 73M, 1554주문) |
| `ptype` | STRING | 코드 | YES | 사업 유형 1차 분류. 'p1'=1P 직매입, 'p3'=3PL (FBK+Pure3P 섞임) | '1p' / '3p' | [B24 검증완료] ptype만으로 FBK/Pure3P 분리 불가. business_type 필수. cm_prd_data_v1과 코드값 다름 (cm은 3pl 별도) |
| `business_type` | STRING | 코드 | YES | 사업 유형 2차 분류. FBK 식별의 결정적 컬럼 | 'KURLY_FULFILLMENT' (1P, 컬리 풀필) / 'PARTNER_KURLY_CONSIGNMENT' (FBK, 컬리위탁) / 'PARTNER_FULFILLMENT' (Pure 3P, 파트너 풀필) / 'PARTNER_MANUAL_PROCESSING' (Pure 3P 수동) / 'PARTNER_NON_DELIVERY' (Pure 3P 비배송/픽업) | [B24 검증완료] FBK = ptype=3p AND business_type=PARTNER_KURLY_CONSIGNMENT. 단독 사용 금지 |
| `tax_type` | STRING | 코드 | YES | 부가세 타입(마스터 상품 기준) | 관측: TAX, FREE, ZERO, NULL (NULL 약 2만 라인) | NULL을 임의로 면세/증정품 해석 금지 |
| `sourcing_type` | STRING | 코드 | YES | 소싱 유형(1p에서만 의미. 3p는 NULL 가능) | 최근 90일 관측: NB(51.9M), ONLY(21.5M), PB(7.4M), NULL(1.73M) | ONLY가 PL 포함인지 정의 확인 필요. NULL이 3p에 집중되는지 ptype 교차 검증 권장. 회사 공식 정의는 문서 필요 |
| `storage_type` | STRING | 코드 | YES | 보관 유형(상온/냉장/냉동 등) | 관측: COLD, AMBIENT_TEMPERATURE, FROZEN, ETC, NULL | kurly_keep_type과 불일치 케이스 존재(예: storage=AMBIENT인데 keep=COLD). 물류 온도 분석 시 SoT 고정 필요 |
| `partner_nm` | STRING | 코드/라벨 | YES | 판매 파트너(공급사)명 | - | 파트너명 변경/정규화 이슈 |
| `fulfillment_owner_id` | STRING | 코드/라벨 | YES | 물류/배송 주체 ID | - | ID 의미 매핑 필요(사내 문서) |
| `seller_info_sales_owner` | STRING | 코드/라벨 | YES | 판매 주체 | - | 값 정의 미확인 |
| `delivery_policy` | STRING | 코드/라벨 | YES | 배송정책(주문별) | - | 무료배송/배송비 로직이 배송정책에 의해 달라짐 |
| `courier` | STRING | 코드/라벨 | YES | 배송사 | - | NULL/기타 처리 필요 |
| `packing_type` | STRING | 코드/라벨 | YES | 포장 타입(1p에 주로 존재, 3p/택배 NULL) | - | NULL 의미를 "미포장"으로 임의 해석 금지 |
| `catg_1_nm` | STRING | 라벨 | YES | 대카테고리명(주문 시점 스냅샷) | - | 카테고리 체계 변경 시 시계열 비교 주의. "최신 카테고리"로 재분류하려면 pms.master_category_view로 master_cd 기준 LEFT JOIN |
| `catg_2_nm` | STRING | 라벨 | YES | 중카테고리명(주문 시점 스냅샷) | - | 카테고리 체계 변경 시 시계열 비교 주의. "최신 카테고리"로 재분류하려면 pms.master_category_view로 master_cd 기준 LEFT JOIN |
| `catg_3_nm` | STRING | 라벨 | YES | 소카테고리명(주문 시점 스냅샷) | - | 카테고리 체계 변경 시 시계열 비교 주의. "최신 카테고리"로 재분류하려면 pms.master_category_view로 master_cd 기준 LEFT JOIN |
| `cust_no` | INT64 | 키 | YES | 주문 고객 번호(회원키) | - | PII. 개인 단위 출력 최소화. 타 테이블과 타입 불일치(INT64 vs STRING) 가능 → 조인 전 캐스팅 확인 |
| `cust_type` | STRING | 코드 | YES | 주문 당시 고객 유형 | - | 값 정의 미확인 |
| `member_group` | INT64 | 코드 | YES | 주문 당시 회원 등급(정수 코드) | - | 코드→등급명 매핑 없으면 해석 제한 |
| `emd_cd` | STRING | 코드/라벨 | YES | 법정동 코드(8자리) | - | 문자열 표기 통일성 / 코드-명 매핑 불일치 가능 |
| `sd_nm` | STRING | 코드/라벨 | YES | 시도명 | - | 문자열 표기 통일성 / 코드-명 매핑 불일치 가능 |
| `sgg_nm` | STRING | 코드/라벨 | YES | 시군구명 | - | 문자열 표기 통일성 / 코드-명 매핑 불일치 가능 |
| `emd_nm` | STRING | 코드/라벨 | YES | 동명 | - | 문자열 표기 통일성 / 코드-명 매핑 불일치 가능 |
| `region_group_cd` | STRING | 코드/라벨 | YES | 권역 그룹 코드(주문 시점) | - | 문자열 표기 통일성 / 코드-명 매핑 불일치 가능 |
| `cnt` | INT64 | 개 | YES | 딜상품 판매수량(해당 행의 수량) | - | 취소/부분취소 반영 방식은 상태/partial_cancel과 함께 정의 필요 |
| `gmv_retail` | INT64 | 원 | YES | 권장소비가 기준 GMV (=GMV1로 통용) | - | 내부 KPI 대부분은 GMV2 기준인데 GMV1을 쓰는 실수 잦음 |
| `gmv_kurly` | INT64 | 원 | YES | 컬리판매가 기준 GMV (=GMV2로 통용) | - | 주문단위/딜단위 합산 기준 명확히(딜행 단위 금액으로 합산 OK). 0원/증정품 포함 여부는 gmv_kurly>0 필터로. 최근 90일 0원 라인 약 100만(1.2%), master_nm에 [증정품]/쇼핑백/샘플 다수 |
| `dc_deal_tot` | INT64 | 원 | YES | 상품할인액(딜 단위 총 할인) | - | 쿠폰/포인트와 합산해 "총할인" 만들면 중복/정의불일치 위험 |
| `new_dc_deal_tot` | INT64 | 원 | YES | 신규회원 상품할인액 | - | dc_deal_tot에 포함인지 별도인지 테이블만으로 확정 불가 → 합산/차감식 임의 작성 금지 |
| `dc_deal_coupon` | INT64 | 원 | YES | 쿠폰할인액(딜 단위, 일반 쿠폰) | - | dc_deal_coupon_product와 동시 SUM 시 쿠폰비 과대 가능(대체/별도 관계 확인 필요) |
| `dc_deal_point` | INT64 | 원 | YES | 적립금 사용액(딜 단위) | - | 주문단위 dc_ord_point와 동시 합산 시 중복 |
| `dc_deal_point_incl_dlvy` | INT64 | 원 | YES | 배송비 차감 전 적립금 사용액(딜 단위) | - | 기준선 점검용. KPI로 직접 쓰지 말 것 |
| `dc_ord_point` | INT64 | 원 | YES | 주문단위 적립금 사용액(배송비 사용분 포함) | - | 딜행에 반복 저장될 수 있어 SUM 시 중복. 주문단위 dedup(MAX/ANY_VALUE) 후 합산 권장 |
| `dc_deal_paid_point` | INT64 | 원 | YES | 컬리캐시 사용액(딜 단위) | - | 주문단위 dc_ord_paid_point와 중복 합산 주의 |
| `dc_deal_paid_point_incl_dlvy` | INT64 | 원 | YES | 배송비 차감 전 컬리캐시 사용액(딜 단위) | - | 기준선 점검용 |
| `dc_ord_paid_point` | INT64 | 원 | YES | 주문단위 컬리캐시 사용액 | - | 딜행 반복 저장 시 중복 합산 위험. 주문단위 dedup 필요 |
| `dlvy_sales` | INT64 | 원 | YES | 배송매출(배송비할인 제외 실결제 대상 배송비) | - | 주문단위 성격 → 딜행 중복 합산 위험. dedup 필요 |
| `coupon_free_shipping` | INT64 | 원 | YES | 무료배송 쿠폰 금액(통상 3,000) | - | 주문단위 반복 저장 시 중복 합산 위험 |
| `group_ord_pay` | INT64 | 원 | YES | 합주문 단위 실결제금액(최종 결제) | - | 가장 흔한 실수: 딜라인 테이블에서 SUM(group_ord_pay) 하면 주문당 라인수만큼 곱해짐. 반드시 group_ord_cd 단위 MAX 후 SUM |
| `coupon_meta_id` | INT64 | 키 | YES | 쿠폰 메타 ID | - | meta vs publish 혼동 주의 |
| `coupon_publish_id` | INT64 | 키 | YES | 쿠폰 발행 ID | - | 발행(캠페인/배포) 단위 vs 메타 단위 다름 |
| `coupon_nm` | STRING | 라벨 | YES | 쿠폰 메타 ID 기준 이름 | - | 이름 기준 분류는 변경/오타에 취약 |
| `coupon_publish_nm` | STRING | 라벨 | YES | 쿠폰 발행 ID 기준 이름 | - | - |
| `coupon_benefit_type` | STRING | 코드 | YES | 쿠폰 혜택 유형 | FREE_SHIPPING(무료배송), PERCENT/AMOUNT 등 추정 — 정확한 코드값 전수는 사내 문서 필요 | mkt_metric_home_1d 적재 쿼리에서 FREE_SHIPPING+coupon_reason2 조합으로 멤버스/케파달성용/유저마케팅 분류 분기 |
| `coupon_reason1` | STRING | 코드 | YES | 쿠폰 발행사유1 | 적재쿼리에서 사용: 마케팅본부, 프로모션, 기타, 제휴, MD요청, 재사용포장, 컬리나우, CRM, 유저마케팅 | 텍스트 기반 분류 — 표준화 이슈 |
| `coupon_reason2` | STRING | 코드 | YES | 쿠폰 발행사유2 | 적재쿼리에서 사용: 매출 제고/매출제고, 멤버십, CRM 외부광고, 뷰티, 러버스, 제휴, ARPU, 지원 | reason1과의 조합으로 캐달푸/CRM/멤버스/ARPU/제휴/뷰티 분기 — 매핑 CASE는 적재 쿼리 참조 |
| `coupon_sharing_ratio` | FLOAT64 | 비율 | YES | 쿠폰할인액 중 컬리 분담 비율 | - | NULL을 0으로 간주하면 왜곡 가능(정책 확인 필요) |
| `dc_deal_coupon_product` | INT64 | 원 | YES | 상품 쿠폰 할인액(딜 단위) | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `coupon_publish_id_product` | INT64 | 키 | YES | 상품 쿠폰 발행 ID | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `coupon_nm_product` | STRING | 라벨 | YES | 상품 쿠폰 메타 이름 | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `coupon_publish_nm_product` | STRING | 라벨 | YES | 상품 쿠폰 발행 이름 | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `coupon_benefit_type_product` | STRING | 라벨 | YES | 상품 쿠폰 혜택 유형 | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `coupon_reason1_product` | STRING | 라벨 | YES | 상품 쿠폰 발행사유1 | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `coupon_reason2_product` | STRING | 라벨 | YES | 상품 쿠폰 발행사유2 | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `coupon_sharing_ratio_product` | FLOAT64 | 비율 | YES | 상품 쿠폰의 컬리 분담 비율 | - | 일반 쿠폰 컬럼군(dc_deal_coupon 등)과 동시 합산 시 중복/이중계상 위험 — 정책 확인 필요 |
| `kurly_keep_type` | STRING | 코드 | YES | 상품 출고시 온도/보관 정보(운영 속성) | - | storage_type과 불일치 케이스 존재 — SoT 고정 필요 |
| `partial_cancel` | STRING | 코드 | YES | 상품 부분취소 여부 플래그 | 관측: normal(82.5M라인), partial_cancel(13K라인). 최근 30일 partial_cancel GMV2 약 45M | 부분취소 주문 전체인지 해당 딜라인만인지 범위 정의 필요. 매출 인정 정책 고정 필요 |
| `update_dt` | DATETIME | 시각 | YES | 데이터 적재/수정 시간 | - | 이벤트 시간(ord_dt)과 혼동 금지 |
| `membership_code` | STRING | 코드 | YES | 멤버십 분류 코드 | 최근 30일 관측: CORE, PLUS, NULL | NULL이 비멤버스인지 미관리인지 정책 확인 필요 |
| `purchase_confirm_date` | DATETIME | 시각 | YES | 3p 상품 구매확정 일자 | - | 최근 210일만 업데이트된다는 원본 주석 — 과거기간 분석 시 결측이 미확정 아닐 수 있음 |
| `delivery_complation_date` | DATETIME | 시각 | YES | 3p 상품 배송완료 일자 | - | 컬럼명 오탈자(complation) — 쿼리 작성 실수 잦음. 최근 210일 업데이트 윈도우 |
| `accrued_point` | INT64 | 원/포인트 | YES | 발생 적립금 | - | 발생 기준(구매확정/결제/정산) 테이블만으로 확정 불가. 딜/주문 어느 단위 저장인지 확인 후 집계 |

# Pitfalls

파티션 필터 필수. deal_status/ord_status/group_ord_status < 40 + ord_type NOT IN REORDER/EXCHANGE + master_nm NOT LIKE 대량주문/임직원. **[B24] ptype만으론 FBK 분리 불가, business_type=PARTNER_KURLY_CONSIGNMENT로 FBK 식별. **N마트 미포함** (cd_data.daily_nmart 별도). 컬리나우는 ord_type=KURLY_NOW로 1P 안에 포함

# Joins

- [sply_share_dc_1d(공급사분담)](./sply_share_dc_1d.md)
- [cm_prd_data_v1(쿠폰분해)](./cm_prd_data_v1.md)
- [cd_data.daily_nmart(N마트)](./daily_nmart.md)
