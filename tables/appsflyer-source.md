---
type: "BigQuery Table"
title: "Appsflyer 광고 성과 원천"
description: "앱 광고 성과 (Appsflyer 원천)"
tags: ["tier2", "raw_source"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# Appsflyer 광고 성과 원천

- **Project.Dataset.Table**: `bq-marketkurly.appsflyer.(Appsflyer 광고 성과)`
- **Grain**: 광고 설치/이벤트
- **Partition**: `-` (-)
- **Key columns**: -
- **Used for**: 앱 광고 성과 (Appsflyer 원천)
- **Standard filter required**: N
- **Source confidence**: low
- **Note**: [자동크롤링 단계에서 정리]

# Pitfalls

광고 채널별 성과 측정에만 사용
