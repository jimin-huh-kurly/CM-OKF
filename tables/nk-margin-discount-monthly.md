---
type: "BigQuery Table"
title: "NK 마진/할인 월별 마트 (NK_margin_discount_YYYYMM, 월별 샤딩)"
description: "월별 분담 마진. 12종 카테고리 한글 분류 (컬리나우/프로모션/MD요청/CO마크다운/선물세트/제휴/멤버스/컬리N마트/미확인/미분류)"
tags: ["tier2", "margin_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# NK 마진/할인 월별 마트 (NK_margin_discount_YYYYMM, 월별 샤딩)

- **Project.Dataset.Table**: `bq-da-common.dp_temp.NK_margin_discount_YYYYMM (월별)`
- **Grain**: 월 × 채널 × 센터 × 마스터 × ptype
- **Partition**: `ord_ym` (STRING)
- **Key columns**: ord_ym+center_cd+master_cd+ptype_new
- **Used for**: 월별 분담 마진. 12종 카테고리 한글 분류 (컬리나우/프로모션/MD요청/CO마크다운/선물세트/제휴/멤버스/컬리N마트/미확인/미분류)
- **Standard filter required**: N
- **Source confidence**: high
- **Note**: 한글 컬럼명. 분담 카테고리 가장 명확

# Pitfalls

검증식: 총할인액=컬리부담+타사부담. dp_temp 임시 데이터셋이라 정책상 휘발 가능성

# Joins

- [cm_prd_data_v1](./cm_prd_data_v1.md)
