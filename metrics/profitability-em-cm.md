---
type: "Metric"
title: "수익성 지표: EM / Adj.EM / CM"
description: "손익 계단(GMV에서 EBITDA까지)에서 Effective Margin, Adjusted EM, Contribution Margin을 구분하는 정의."
tags: ["finance", "profitability", "sot"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: sot-v1
    resource: "kurly-analytics/data/l2/sot_definitions_v1.md"
    title: "L2 SoT 정의 확정 v1"
---

# Definition

손익은 다음 계단을 거쳐 산출된다(재무 손익 시트의 표준 라벨을 그대로 따름):

**GMV(VAT 제외)** → 상품할인(공급사/컬리 분담) 차감 → 쿠폰할인(파트너사/컬리 분담) 차감 → 적립금 사용 차감 → **EM(Effective Margin)** → 공급사/타사 분담분 add-back 조정 → **Adj.EM(Adjusted Effective Margin)** → 직접운영비 차감 → **CM(Contribution Margin)** → EBITDA

세 지표는 서로 다른 단계이며 혼용하지 않는다:
- **EM** = 분담 조정 전 마진.
- **Adj.EM** = EM에 공급사·타사 분담분을 다시 더한(add-back) 컬리 실질 마진. Adj.EM률 = Adj.EM 금액 / GMV(VAT 제외).
- **CM(공헌이익)** = Adj.EM에서 직접운영비(물류 등 CLOF 추정 자료 기준)를 차감한 값.

# Rules

- 운영(물류) 간접비는 "직접운영비(CLOF 추정 자료)" 라인으로, EBITDA 앞 단계에 위치한다. 추정치이므로 데이터웨어하우스의 단일 테이블로 정확히 재현되지 않을 수 있다.
- **적재 지연 주의**: 손익 시트(CM 원천)는 실시간 집계가 아니라 수 주 지연되어 마감되므로, CM은 일별 실시간 지표로 쓰기에 부적합하다. 월/확정일 단위 추세로 보아야 한다.
- 일별·사업라인별·카테고리별 세부 EM/Adj.EM은 상대적으로 빠르게 산출 가능하나, CM/직접운영비는 손익 시트의 확정일 그레인에 의존한다.
- 전략적으로, CM은 제약조건(캐파·물류 등 운영 효율)이 손익으로 귀결되는 지점이다. 캐파 가동률이 떨어지면 단위 직접운영비가 상승해, Adj.EM이 동일해도 CM은 잠식될 수 있다.

# Examples

검증된 계산 예시(참고용, 특정 시점 값):
- GMV(VAT제외) → EM → Adj.EM → 직접운영비 → CM → EBITDA 순으로 각 단계 금액이 감소하며, Adj.EM에서 직접운영비를 뺀 값과 시트에 기재된 CM 사이에는 미세한 조정 차이가 존재할 수 있다(시트 수식 미확인 항목으로 별도 확인 필요).
