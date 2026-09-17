---
type: "Metric"
title: "사업라인 정의 (전체 / 1P / 3P / FBK / N마트)"
description: "컬리 매출을 1P·Pure 3P·FBK(3PL)·N마트로 분해하는 사업라인 표준 정의."
tags: ["finance", "business-line", "sot"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: sot-v1
    resource: "kurly-analytics/data/l2/sot_definitions_v1.md"
    title: "L2 SoT 정의 확정 v1"
---

# Definition

핵심 소스 테이블은 적재 단계에서 이미 사업 유형(ptype)을 세 갈래로 분류해 저장한다. 적재 로직: `fulfillment_owner_id`가 컬리이면서 판매 주체가 3자 파트너인 경우는 '3pl'(FBK)로, 그 외는 원래 ptype 값을 그대로 사용한다.

| 라인 | 정의 |
|---|---|
| 1P | ptype='1p' (마켓+뷰티+컬리나우 포함) |
| FBK(3PL) | ptype='3pl' (컬리 풀필먼트 + 외부셀러, 전통주 포함) |
| Pure 3P | ptype='3p' (셀러 직배송, 컬리물류 미경유) |
| N마트 | 위 분류에서 제외되며(2025-09-04부로 분리), 별도 N마트 소스에서 집계 |

"전체"는 관점에 따라 두 가지로 공존한다:
- **재무 거래액** = 1P + 3P (+N마트, Pure 3P 포함)
- **사업 표준(biz_std)** = 1P + FBK (+N마트) = ptype이 3P가 아닌 것 (헤드라인 지표 기준)

# Rules

- **가산성**: GMV는 그대로 더할 수 있다(가산). 주문수는 group_ord_cd distinct 기준(묶음구매 조인 시 중복 주의). 구매자수는 비가산(단순합 불가, 중복 인원 발생).
- **L2 표출 적용**: 카드 스트립은 1P·FBK·N마트를 기본으로 하고 Pure 3P는 참고용으로 노출한다. 운영(캐파) 관점 카드는 1P+FBK+N마트로 구성한다(Pure 3P는 물류를 컬리가 경유하지 않으므로 자연 제외).
- 재무 쪽 손익 시트도 동일하게 1P/Pure 3P/FBK/컬리나우로 분해하되, Pure 3P는 구매확정 기준(전일자만), 1P는 주문 기준으로 그레인이 다르므로 비교 시 주의해야 한다.

# Examples

- ptype 코드값은 소스에 따라 다르다: 한 소스 계열에서는 0=1P/1=3P/2=3PL로 정수 코드를 쓰고, 다른 소스 계열에서는 문자열 '1p'/'3p'/'3pl'을 쓴다. 두 코드 체계를 혼동하지 않아야 한다.
