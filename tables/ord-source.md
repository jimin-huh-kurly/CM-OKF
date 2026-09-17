---
type: "BigQuery Table"
title: "ORD 주문 원천"
description: "주문 원천 (DW 마트 std_ord_prd_mthly_1d의 베이스)"
tags: ["tier3", "raw_source"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# ORD 주문 원천

- **Project.Dataset.Table**: `bq-marketkurly.ord.(ORD 주문 원천)`
- **Grain**: 주문
- **Partition**: `-` (-)
- **Key columns**: group_ord_cd, ord_cd
- **Used for**: 주문 원천 (DW 마트 std_ord_prd_mthly_1d의 베이스)
- **Standard filter required**: Y
- **Source confidence**: low
- **Note**: [자동크롤링 단계에서 정확한 테이블명 매핑]

# Pitfalls

거의 사용 안 함. DW 마트로 충분

# Joins

- [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md)
