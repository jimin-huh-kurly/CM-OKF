---
type: "BigQuery Table"
title: "검색광고 리포트 원천"
description: "네이버·구글 검색광고 성과"
tags: ["tier2", "raw_source"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# 검색광고 리포트 원천

- **Project.Dataset.Table**: `bq-marketkurly.search_ad_report.(검색광고 리포트)`
- **Grain**: 광고×일
- **Partition**: `-` (-)
- **Key columns**: -
- **Used for**: 네이버·구글 검색광고 성과
- **Standard filter required**: N
- **Source confidence**: low
- **Note**: [자동크롤링 단계에서 정리]

# Pitfalls

194개 테이블. 자동크롤링 단계에서 핵심만 선별
