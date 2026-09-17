---
type: "Reference"
title: "재사용 쿼리 템플릿 모음"
description: "표준 매출 필터, 안전 집계 패턴, 검증용 쿼리 등 재사용 가능한 BigQuery SQL 템플릿 모음."
tags: ["dictionary", "sql", "reference"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: query-templates
    resource: "kurly-analytics/data/dictionary/query_templates.csv"
    title: "Kurly Query Templates"
---

# QT500 — 표준 매출 인정 필터 (완전판, 표준)

`mkt_metric_home_1d` 적재 쿼리에서 그대로 추출한, 사내에서 가장 권위 있는 표준 매출 필터 정의.

```sql
WHERE ord_dt >= S_DATE - 1 AND ord_dt < DATE_ADD(S_DATE, INTERVAL 1 MONTH)
  AND (deal_status < 40 AND ord_status < 40 AND group_ord_status < 40)
  AND master_nm NOT LIKE '%대량주문%'
  AND master_nm NOT LIKE '%임직원%판매%'
  AND gmv_kurly > 0
  AND ord_type != 'REORDER'
  AND ord_type != 'EXCHANGE'
  AND master_cd NOT IN (SELECT DISTINCT user_value FROM `bq-datafarm.data_warehouse.infra_master`
                          WHERE user_key = 'exclude_mcd')
```

# QT501 — 23시 이후 주문 익일 환산 (표준)

```sql
CASE WHEN EXTRACT(HOUR FROM ord_dt) >= 23
     THEN DATE(ord_dt) + 1
     ELSE DATE(ord_dt)
END AS ord_date
```
23시(포함)부터 익일 0시(미포함) 사이의 주문을 익일자로 환산하는 표준 로직.

# QT001 — 일별 주문/구매자/GMV2 안전 집계 (주문단위 dedup, 표준)

딜라인 단위 테이블에서 주문단위 컬럼(결제액·배송매출·적립금 등)을 중복 합산하지 않도록 주문단위로 먼저 dedup한 뒤 집계하는 패턴.

```sql
WITH line AS (
  SELECT DATE(ord_dt) AS ord_date, group_ord_cd, cust_no,
    SUM(gmv_kurly) AS gmv2_line,
    SUM(dc_deal_tot) AS dc_deal_tot_line,
    SUM(dc_deal_coupon) AS coupon_line,
    SUM(dc_deal_coupon_product) AS coupon_product_line
  FROM `bq-datafarm.data_warehouse.std_ord_prd_mthly_1d`
  WHERE DATE(ord_dt) BETWEEN '2026-05-01' AND '2026-05-31'
  GROUP BY 1,2,3
),
ord AS (
  SELECT DATE(ord_dt) AS ord_date, group_ord_cd,
    MAX(group_ord_pay) AS pay_1x,
    MAX(dlvy_sales) AS dlvy_sales_1x,
    MAX(coupon_free_shipping) AS free_ship_coupon_1x,
    MAX(dc_ord_point) AS ord_point_1x,
    MAX(dc_ord_paid_point) AS ord_paid_point_1x
  FROM `bq-datafarm.data_warehouse.std_ord_prd_mthly_1d`
  WHERE DATE(ord_dt) BETWEEN '2026-05-01' AND '2026-05-31'
  GROUP BY 1,2
)
SELECT l.ord_date,
  COUNT(DISTINCT l.group_ord_cd) AS orders,
  COUNT(DISTINCT l.cust_no) AS buyers,
  SUM(l.gmv2_line) AS gmv2,
  SUM(o.pay_1x) AS paid_amount,
  SUM(o.dlvy_sales_1x) AS delivery_sales,
  SUM(l.dc_deal_tot_line) AS deal_discount,
  SUM(o.free_ship_coupon_1x) AS free_shipping_coupon,
  SUM(o.ord_point_1x + o.ord_paid_point_1x) AS points_used_order_level
FROM line l JOIN ord o USING(ord_date, group_ord_cd)
GROUP BY 1 ORDER BY 1
```
필수 필터: `WHERE DATE(ord_dt) BETWEEN ...` (파티션 필터). 추가로 상태컷(`deal_status`/`ord_status`/`group_ord_status` < 40), `gmv_kurly > 0`, `partial_cancel = 'normal'`, 대량주문/임직원판매 제외가 적용되어야 한다.

# QT002 — 표준 매출 필터 템플릿 (초안)

```sql
SELECT DATE(ord_dt) AS ord_date,
  COUNT(DISTINCT group_ord_cd) AS orders,
  COUNT(DISTINCT cust_no) AS buyers,
  SUM(gmv_kurly) AS gmv2
FROM `bq-datafarm.data_warehouse.std_ord_prd_mthly_1d`
WHERE DATE(ord_dt) BETWEEN '2026-05-01' AND '2026-05-10'
  AND group_ord_status < 40 AND ord_status < 40 AND deal_status < 40
  AND gmv_kurly > 0
  AND partial_cancel = 'normal'
  AND NOT (master_nm LIKE '%대량주문%' OR master_nm LIKE '%임직원%판매%')
GROUP BY 1 ORDER BY 1
```
상태코드의 정확한 의미는 사내 코드북으로 별도 확정이 필요하다.

# QT003 — 쿠폰 reason 조합 매핑표 추출용 (진단)

[쿠폰 분류 체계](./coupon-classification.md)의 CASE WHEN 로직을 역추적/검증하기 위한 사실 베이스 추출 쿼리.

```sql
SELECT coupon_reason1, coupon_reason2, coupon_benefit_type,
  COUNT(*) AS line_cnt, SUM(dc_deal_coupon) AS coupon_amt
FROM `bq-datafarm.data_warehouse.std_ord_prd_mthly_1d`
WHERE DATE(ord_dt) BETWEEN '2026-05-01' AND '2026-05-31'
  AND dc_deal_coupon > 0
GROUP BY 1,2,3 ORDER BY coupon_amt DESC LIMIT 200
```

# QT004 — infra_master exclude_mcd 확인 (진단)

```sql
SELECT user_key, desc1, desc2, user_value, user_arg1, user_arg2, user_arg3
FROM `bq-datafarm.data_warehouse.infra_master`
WHERE user_key='exclude_mcd' LIMIT 200
```
`infra_master`에는 `master_cd` 컬럼이 별도로 없고 `user_value`/`user_arg1~3`에 담겨 있을 가능성이 있다.

# QT005 — sourcing_type NULL × ptype 교차 검증 (진단)

```sql
SELECT ptype, sourcing_type, COUNT(*) AS line_cnt
FROM `bq-datafarm.data_warehouse.std_ord_prd_mthly_1d`
WHERE DATE(ord_dt) BETWEEN DATE_SUB(CURRENT_DATE(),INTERVAL 90 DAY)
                      AND DATE_SUB(CURRENT_DATE(),INTERVAL 1 DAY)
GROUP BY 1,2 ORDER BY line_cnt DESC
```

# QT100 — 최신 카테고리로 재분류 (표준)

주문 시점 카테고리가 아니라 최신 상품 마스터 카테고리 매핑으로 GMV를 재집계.

```sql
WITH o AS (
  SELECT master_cd, SUM(gmv_kurly) AS gmv2
  FROM `bq-datafarm.data_warehouse.std_ord_prd_mthly_1d`
  WHERE DATE(ord_dt) BETWEEN DATE_SUB(CURRENT_DATE(),INTERVAL 7 DAY)
                       AND DATE_SUB(CURRENT_DATE(),INTERVAL 1 DAY)
    AND deal_status<40 AND ord_status<40 AND group_ord_status<40
  GROUP BY 1
)
SELECT mc.catg_1_nm, mc.catg_2_nm, mc.catg_3_nm, SUM(o.gmv2) AS gmv2
FROM o LEFT JOIN `bq-marketkurly.pms.master_category_view` mc USING(master_cd)
GROUP BY 1,2,3 ORDER BY gmv2 DESC
```
"주문 당시 카테고리"가 필요하면 `std_ord_prd_mthly_1d.catg_*_nm`을 대신 사용한다.

# QT101 — 카테고리 팀 정보 결측 보정 (표준)

```sql
SELECT IF(team='-' OR team IS NULL,'기타',team) AS team_nm, *
FROM `bq-da-common.cman_data.cm_catg_team_info`
```

# QT102 — 브랜드명 정규식 UDF 정의 조회 (진단)

```sql
SELECT routine_name, routine_definition
FROM `bq-datafarm.data_warehouse.INFORMATION_SCHEMA.ROUTINES`
WHERE routine_name='f_regex_brand_nm'
```

# QT200 — cm_prd_data_v1 팀명 복수값 검증 (진단)

```sql
SELECT master_cd, COUNT(DISTINCT team_nm) AS team_versions
FROM `bq-da-common.cman_data.cm_prd_data_v1`
WHERE ord_date BETWEEN DATE_SUB(CURRENT_DATE(),INTERVAL 90 DAY)
                  AND DATE_SUB(CURRENT_DATE(),INTERVAL 1 DAY)
GROUP BY 1 HAVING team_versions>1
ORDER BY team_versions DESC LIMIT 50
```

# QT300 — daily_member_1d 키 유니크성 검증 (진단)

```sql
SELECT pdt, COUNT(*) AS row_cnt, COUNT(DISTINCT m_no) AS member_cnt,
  COUNT(*)-COUNT(DISTINCT m_no) AS dup_cnt
FROM `bq-datafarm.data_warehouse.daily_member_1d`
WHERE pdt BETWEEN DATE_SUB(CURRENT_DATE(),INTERVAL 7 DAY) AND CURRENT_DATE()
GROUP BY 1 ORDER BY 1
```

# QT301 — True MAU 표준 정의 (표준)

월간 True MAU = 앱/모바일웹/데스크톱웹 중 하나라도 활성인 회원의 distinct count.

```sql
SELECT COUNT(DISTINCT m_no) AS true_mau_30d
FROM `bq-datafarm.data_warehouse.daily_member_1d`
WHERE pdt BETWEEN DATE_SUB(CURRENT_DATE(),INTERVAL 29 DAY) AND CURRENT_DATE()
  AND (IFNULL(au_yn_app,0)=1 OR IFNULL(au_yn_mweb,0)=1 OR IFNULL(au_yn_dweb,0)=1)
```
마켓/뷰티는 도메인별 활동이라 MAU 정의와는 분리해서 본다. 회원 단위 DISTINCT가 필수(중복 제거).

# QT302 — 활동 플래그 값 분포 검증 (진단)

```sql
SELECT 'au_yn_app' AS col, au_yn_app AS val, COUNT(*) AS cnt
FROM `bq-datafarm.data_warehouse.daily_member_1d`
WHERE pdt >= DATE_SUB(CURRENT_DATE(),INTERVAL 31 DAY)
GROUP BY 1,2
UNION ALL
SELECT 'cv_yn_ord_search', cv_yn_ord_search, COUNT(*)
FROM `bq-datafarm.data_warehouse.daily_member_1d`
WHERE pdt >= DATE_SUB(CURRENT_DATE(),INTERVAL 31 DAY)
GROUP BY 1,2
```

# QT400 — 테이블별 적재 SLA 점검 템플릿 (범용, 표준)

날짜 컬럼을 테이블에 맞게 치환해서 사용한다 (예: `std_ord_prd_mthly_1d`→`ord_dt`, `daily_member_1d`→`pdt`, `cm_prd_data_v1`→`ord_date`, `core_cust_info_1d`→`reg_date`, `mkt_metric_home_1d`→`stnd_date`).

```sql
SELECT <DATE_COL> AS dt, MAX(update_dt) AS last_update_dt,
  EXTRACT(HOUR FROM MAX(update_dt)) AS last_update_hour
FROM `<PROJECT>.<DATASET>.<TABLE>`
WHERE <DATE_COL> BETWEEN DATE_SUB(CURRENT_DATE(),INTERVAL 14 DAY)
                   AND DATE_SUB(CURRENT_DATE(),INTERVAL 1 DAY)
GROUP BY 1 ORDER BY 1
```
