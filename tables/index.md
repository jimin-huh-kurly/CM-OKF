# BigQuery Table Catalog

* [std_ord_prd_mthly_1d](./std_ord_prd_mthly_1d.md) - 매출 SoT 1순위. GMV2/주문수/구매자수 산출의 베이스
* [cm_prd_data_v1](./cm_prd_data_v1.md) - 쿠폰·할인 세부 분해. 16종 컬리분담 할인 + 공급사분담 분해
* [sply_share_dc_1d](./sply_share_dc_1d.md) - 공급사 분담 할인 분해. dc_deal_sply_share_tot
* [seg_mem_margin_cm](./seg_mem_margin_cm.md) - 재무 P&L SoT 후보. em=Earned Margin, cm=Contribution Margin
* [live_gmv_margin](./live_gmv_margin.md) - 라이브커머스 GMV·마진 분석
* [cust_seg_master_1d](./cust_seg_master_1d.md) - RFM 세그·연속유지·CRM그룹·멤버스여부 매스터
* [daily_member_1d](./daily_member_1d.md) - 일별 활성 회원 추적. au_yn_app(앱활성) au_yn_homerecomm(홈추천활성) lastm_crm_group(전월CRM그룹)
* [core_cust_info_1d](./core_cust_info_1d.md) - 고객 마스터. 가입경로/디바이스/추천인 등
* [rp_subscription_member_daily](./rp_subscription_member_daily.md) - 멤버스 가입자 일자별 추적
* [mkt_metric_home_1d](./mkt_metric_home_1d.md) - 홈 메인 KPI 마트. 모수 8종 + 쿠폰 5종 분해 + 할인 분담 보유
* [mkt_crm_grp_mthly](./mkt_crm_grp_mthly.md) - 월별 CRM 그룹 세그 (장미/이탈/유지 등)
* [non_membership_biz_plan_main_pfm](./non_membership_biz_plan_main_pfm.md) - 비멤버스 사업계획 vs 실적 마트
* [cm_new_cust_monthly](./cm_new_cust_monthly.md) - 신규 고객 30분 단위 시간대 분석
* [mem_seg_metric_daily](./mem_seg_metric_daily.md) - 일별 세그 매출 마트 (bs/arpu/freq 포함)
* [dashboard_bs_trend_catg](./dashboard_bs_trend_catg.md) - BS(Basket Size) 카테고리별 트렌드 대시보드 SoT
* [dashboard_arpu_trend_catg](./dashboard_arpu_trend_catg.md) - ARPU 카테고리별 트렌드 대시보드
* [pntr_dashboard_catg1](./pntr_dashboard_catg1.md) - PNTR(Penetration) 침투율 대시보드
* [pntr_dashboard_catg2](./pntr_dashboard_catg2.md) - PNTR 중카테고리 침투율
* [arpu_dashboard_catg1](./arpu_dashboard_catg1.md) - ARPU 주차별 카테고리 대시보드
* [cohort_1m](./cohort_1m.md) - 월별 코호트 리텐션·BS·ARPU·freq
* [NK 마진/할인 월별 마트 (NK_margin_discount_YYYYMM, 월별 샤딩)](./nk-margin-discount-monthly.md) - 월별 분담 마진. 12종 카테고리 한글 분류 (컬리나우/프로모션/MD요청/CO마크다운/선물세트/제휴/멤버스/컬리N마트/미확인/미분류)
* [closeout_sale_dashboard_daily](./closeout_sale_dashboard_daily.md) - 임박할인(close-out sale) 노출/유입/구매 분석
* [infra_master](./infra_master.md) - 사내 표준 코드·제외 마스터 보관. user_key="exclude_mcd"가 표준 매출 필터에서 제외할 마스터 코드 목록
* [PMS 상품 마스터 (원천)](./pms-source.md) - 상품 마스터 원천. 카테고리·브랜드·공급사·표시명 SoT
* [ORD 주문 원천](./ord-source.md) - 주문 원천 (DW 마트 std_ord_prd_mthly_1d의 베이스)
* [Appsflyer 광고 성과 원천](./appsflyer-source.md) - 앱 광고 성과 (Appsflyer 원천)
* [검색광고 리포트 원천](./search-ad-report-source.md) - 네이버·구글 검색광고 성과
* [Braze CRM 메시지 원천](./braze-source.md) - Braze 발송·오픈·클릭 등 CRM 성과
* [daily_nmart](./daily_nmart.md) - **N마트 매출 SoT**. 1P+FBK+N마트 합산 매출의 N마트 분
* [cust_target_master_1d](./cust_target_master_1d.md) - 고객단위 브리지(m_uuid↔cust_no). 세그(cust_crm_grp), 재주문(ord_seq), 멤버십(membership_yn), consecutive_retain_class 동시 보유
* [funnel_stats_event_1d_v1](./funnel_stats_event_1d_v1.md) - 앱 행동 퍼널(홈→PDP→장바구니→구매전환) 단계별 전환율 산출. 2026-07 실측: 홈→PDP 44.48%/PDP→장바구니 42.28%/장바구니→구매전환 23.65%/홈→구매전환 7.50%
