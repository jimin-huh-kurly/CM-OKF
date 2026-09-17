---
type: "BigQuery Table"
title: "funnel_stats_event_1d_v1"
description: "앱 행동 퍼널(홈→PDP→장바구니→구매전환) 단계별 전환율 산출. 2026-07 실측: 홈→PDP 44.48%/PDP→장바구니 42.28%/장바구니→구매전환 23.65%/홈→구매전환 7.50%"
resource: "bigquery://bq-marketkurly.kalog.funnel_stats_event_1d_v1"
tags: ["tier1", "agg_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# funnel_stats_event_1d_v1

- **Project.Dataset.Table**: `bq-marketkurly.kalog.funnel_stats_event_1d_v1`
- **Grain**: 일×플랫폼×스텝쌍(prev_step→next_step)×step_gap
- **Partition**: `pdt` (DATE)
- **Key columns**: pdt, platform, prev_step, next_step, step_gap
- **Used for**: 앱 행동 퍼널(홈→PDP→장바구니→구매전환) 단계별 전환율 산출. 2026-07 실측: 홈→PDP 44.48%/PDP→장바구니 42.28%/장바구니→구매전환 23.65%/홈→구매전환 7.50%
- **Standard filter required**: N
- **Source confidence**: 실데이터확인
- **Note**: 2026-08-19 발견(MKT효율표 세션, §3 갭G 해소 중). CLAUDE.md §2.3에 bq-marketkurly는 11개 데이터셋만 문서화돼 있으나 실제 140개 존재 확인(문서 최신화 필요 항목). 재현 근거: output/20260811_101054_MKT효율표_설계_진행노트.md §8

# Pitfalls

★함정: prev_session_cnt는 같은(prev_step,pdt,platform)이면 next_step·step_gap이 달라도 항상 동일값이 반복 기재됨 — SELECT DISTINCT로 중복제거 후 SUM해야 함(안 하면 분모가 30배 이상 과대, 실제로 "월 세션 11.5억"이라는 비현실적 수치로 발각됨). next_session_cnt는 step_gap별 진짜 다른 값이라 그대로 SUM 가능. bq-marketkurly는 billing bq-da-common 경유 조회(§3.8 F1395 범위 내).

# Joins

- funnel_stats_screen_1d_v1(스크린 버전
- 동일 구조로 추정·미검증)
