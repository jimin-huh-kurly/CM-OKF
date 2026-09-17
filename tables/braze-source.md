---
type: "BigQuery Table"
title: "Braze CRM 메시지 원천"
description: "Braze 발송·오픈·클릭 등 CRM 성과"
tags: ["tier2", "raw_source"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# Braze CRM 메시지 원천

- **Project.Dataset.Table**: `bq-marketkurly.braze.(Braze CRM 메시지)`
- **Grain**: CRM 메시지×고객
- **Partition**: `-` (-)
- **Key columns**: -
- **Used for**: Braze 발송·오픈·클릭 등 CRM 성과
- **Standard filter required**: N
- **Source confidence**: low
- **Note**: [자동크롤링 단계에서 정리]

# Pitfalls

83개 테이블. 자동크롤링 단계에서 핵심만 선별
