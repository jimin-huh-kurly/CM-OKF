---
type: "Metric"
title: "비용부담률 제약: B1(상품할인율) / B2(마케팅비용률)"
description: "상품할인율(B1)과 마케팅비용률(B2)을 GMV 대비 부담률로 정의하고, 캐파 소진(K2)과 함께 보는 통제공백 관점."
tags: ["finance", "marketing", "sot"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: sot-v1
    resource: "kurly-analytics/data/l2/sot_definitions_v1.md"
    title: "L2 SoT 정의 확정 v1"
---

# Definition

- **B1(상품할인율)** = 컬리 분담 상품할인액 / GMV. 계획선은 월별 실행계획 시트의 비용 지수(대 GMV 비율)로, 계획 대비 비교가 가능하다.
- **B2(마케팅비용률)** = ([B2 정의](./marketing-cost-ratio-b2.md) 참조 3종 쿠폰 합) / GMV. 별도 계획선이 없어 과거 추세와 표준화 편차(z-score)로 통제 여부를 판단한다.

# Rules

캐파 소진(K2)·상품할인(B1)·마케팅비용(B2) 세 부담률은 한 화면에서 합산해 보는 것이 통제공백(계획 대비 이탈)을 판단하는 핵심 뷰다. 손익 시트의 할인 분해(상품할인 공급사/컬리 분담, 쿠폰 파트너사/컬리 분담)와 B1 정의는 정합해야 하며, 컬리 분담분만이 B1의 분자가 된다.

# Change Notes

- 상품 프로모션 쿠폰(상품 프로모션·상시가격대응 분류)의 컬리 분담률은 비효율 감시 지표로 별도 관리한다(2026-06-23).
