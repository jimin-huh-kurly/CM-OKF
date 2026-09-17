---
type: "BigQuery Table"
title: "PMS 상품 마스터 (원천)"
description: "상품 마스터 원천. 카테고리·브랜드·공급사·표시명 SoT"
tags: ["tier2", "dim_master"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# PMS 상품 마스터 (원천)

- **Project.Dataset.Table**: `bq-marketkurly.pms.(PMS 상품 마스터)`
- **Grain**: 상품
- **Partition**: `-` (-)
- **Key columns**: master_cd, content_no
- **Used for**: 상품 마스터 원천. 카테고리·브랜드·공급사·표시명 SoT
- **Standard filter required**: N
- **Source confidence**: low
- **Note**: [자동크롤링 단계에서 정확한 테이블명 매핑 예정]

# Pitfalls

원천계라 일자 컷팅·삭제 케이스 주의

# Joins

- [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md)
