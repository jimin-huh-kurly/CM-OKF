# CM-OKF

컬리(Kurly) 데이터 분석 조직의 데이터 카탈로그·지표 정의·분석 방법론을 [Open Knowledge Format (OKF) v0.2](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/7a0224426fcd84418399d0d0c1ef1998a50a4053/okf/SPEC.md) 스펙에 맞춰 축적하는 지식 번들입니다.

OKF는 마크다운 파일 + YAML 프론트매터로 "메타데이터, 맥락, 큐레이션된 인사이트"를 표현하는 개방형 포맷입니다. 중앙 등록소나 전용 도구 없이, 이 저장소를 그대로 clone하거나 열람하는 것만으로 지식을 소비할 수 있습니다.

## 구조

```
CM-OKF/
├── index.md          # 번들 루트 인덱스 (okf_version: 0.2)
├── log.md            # 번들 업데이트 이력
├── tables/            # BigQuery 핵심 테이블 카탈로그 (type: BigQuery Table)
├── metrics/           # 사업라인·수익성·비용률·캐파 등 지표(SoT) 정의 (type: Metric)
├── methodology/       # 인과관계 발견 방법론·게이트·변수 편입 절차 (type: Playbook)
└── dictionary/        # 쿠폰 분류, 자산 사전, 재사용 쿼리 템플릿 (type: Reference/Dictionary)
```

각 폴더의 `index.md`에서 하위 개념 목록을 확인할 수 있습니다. 전체 목록은 [index.md](./index.md)부터 시작하세요.

## 원본과 범위

- 원본 데이터: 사내 `kurly-analytics` 저장소의 `data/catalog`, `data/dictionary`, `data/l2`, `data/l3` 일부.
- 포함 범위: 현재 유효한(최신) 테이블 카탈로그, 컬럼 사전, SoT/지표 정의, 분석 방법론, 참고 사전만 포함했습니다.
- 의도적으로 제외한 것: 자격증명, 백업/버전 파일(.bak/.backup), 구체적인 비즈니스 인사이트·가설·검증 로그(`l3/insights`, `l3/diag` 등), 운영 백로그, 원시 메타데이터 전체 덤프. 이 저장소는 "확정된 정의·카탈로그"를 다루며, 진행 중인 분석 발견물은 다루지 않습니다.

## 상태

모든 개념 문서는 `status: draft`이며 `generated: { by: "claude/sonnet-5" }` 로 최초 자동 변환되었습니다. 아직 사람 검증(`verified`)이 붙지 않았으므로, 실제 업무에 사용하기 전 담당자 확인을 권장합니다. 일부 재무 수치는 원본의 특정 시점 정확한 금액 대신 정의 설명으로 일반화했습니다(민감도 고려).

## 비공개 저장소

이 저장소는 내부 데이터 카탈로그와 지표 정의를 포함하므로 private으로 유지합니다.
