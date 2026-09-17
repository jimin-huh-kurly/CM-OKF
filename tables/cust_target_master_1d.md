---
type: "BigQuery Table"
title: "cust_target_master_1d"
description: "고객단위 브리지(m_uuid↔cust_no). 세그(cust_crm_grp), 재주문(ord_seq), 멤버십(membership_yn), consecutive_retain_class 동시 보유"
resource: "bigquery://bq-datafarm.data_warehouse.cust_target_master_1d"
tags: ["tier1", "dim_mart"]
status: "draft"
generated: { by: "claude/sonnet-5", at: "2026-09-17T00:00:00Z" }
sources:
  - id: table_catalog
    resource: "kurly-analytics/data/catalog/table_catalog.csv"
    title: "Kurly BigQuery Table Catalog"
---

# cust_target_master_1d

- **Project.Dataset.Table**: `bq-datafarm.data_warehouse.cust_target_master_1d`
- **Grain**: 고객(cust_no) 1행
- **Partition**: `-` (없음(비파티션))
- **Key columns**: cust_no, m_uuid
- **Used for**: 고객단위 브리지(m_uuid↔cust_no). 세그(cust_crm_grp), 재주문(ord_seq), 멤버십(membership_yn), consecutive_retain_class 동시 보유
- **Standard filter required**: N
- **Source confidence**: 실데이터확인
- **Note**: 2026-08-13 발견(MKT효율표 세션, §4.5 모집단 불일치 재조사 중). 재현 근거: output/20260811_101054_MKT효율표_설계_진행노트.md §6.3

# Pitfalls

파티션 없어 SELECT COUNT(*) 0바이트, 필요컬럼 8개 선택해도 1.16GB(PRECISE). core_cust_info_1d(같은 목적, dry-run 144GB↔974GB로 요동쳐 신뢰불가)의 대체 브리지로 사용할 것. ord_seq는 "현재 시점 누적 주문횟수" 스냅샷 — 특정 과거월 주문의 회고적 시퀀스 번호 아님(lookahead bias 주의)

# Joins

- [core_cust_info_1d](./core_cust_info_1d.md)
- braze_mart.braze_campaign_targets(external_id=m_uuid로 조인)
- [cust_seg_master_1d](./cust_seg_master_1d.md)
