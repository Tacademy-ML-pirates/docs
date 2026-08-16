# 블루리본 매장 예측 ML 프로젝트 문서

소비자 리뷰와 식당 내부 이미지를 결합해 블루리본 선정 가능성을 예측하고, 선정·비선정 매장의 특성을 분석한 T아카데미 11기 팀 프로젝트 문서 저장소입니다.

## 문서 읽는 순서

1. [프로젝트 개요](project-overview.md) — 목표, 연구 질문, 팀, 저장소 운영 원칙
2. [데이터·품질·버전](data-and-versioning.md) — 기준 파일, 계보, 정규화, 리뷰 결합 규칙
3. [파이프라인·코드](pipeline-and-code.md) — 수집부터 신규 식당 예측까지의 실행 흐름
4. [분석·모델링](analysis-and-modeling.md) — 피처, 모델 후보, 실험과 재현 기준
5. [평가·결과](evaluation-and-results.md) — 1,428건 재평가 지표, 임계값, 오류와 한계
6. [결정·회의 기록](decisions-and-meetings.md) — 확정 결정과 회의록 저장 규칙

## 데이터 품질 문서

- [로컬·Drive 데이터 파일 감사](data-file-audit.md)
- [원본 manifest와 Drive 매핑](data-manifest.md)
- [확장자·인코딩·수식 셀 복구](data-normalization.md)
- [최신순·추천순 리뷰 결합본 재생성](review-combination.md)
- [1,428건 기준 모델 재평가](model-reevaluation.md)
- [Notion 원문 감사와 문서 구조](notion-content-audit.md)

## 현재 기준

| 영역 | 기준 |
|---|---|
| 전체 특성 모델링 데이터 | `블루리본_최최종_마참내_v1.1.1.csv` — 1,428행, 67열 |
| 축소 특성 모델링 데이터 | `블루리본_최최종_마참내_v1.2.1.csv` — 1,428행, 22열 |
| 텍스트 특성 | `KoELECTRA_X_feature_v2.3_weighted.csv` |
| 리뷰 결합본 | `reviews_*_combined_v1.0.0.csv` 8개, 총 281,763행 |
| 모델 재평가 | LightGBM·XGBoost, validation 임계값, test 286건 |

발표 자료의 결과는 1,440행 `v1.2` 기준이며, 이후 재학습 기준인 1,428행 `v1.2.1` 결과와 구분합니다.

## 저장소 역할

- [`code`](https://github.com/Tacademy-ML-pirates/code): 실행 가능한 수집·전처리·특성 생성·모델링 코드
- [`docs`](https://github.com/Tacademy-ML-pirates/docs): 버전 관리되는 프로젝트 문서와 회의 기록
- [Google Drive](https://drive.google.com/drive/folders/12DjeIDFp103VEpUEExMaS7mESkHR-A4b?usp=sharing): 대용량 원천 데이터와 중간 산출물
- [Notion 프로젝트 홈](https://app.notion.com/p/aa2bf34cc2ef833c8c75815c5ade4762): 탐색용 요약, 작업 관리, 원문 보관

## 발표 자료

- [ML 팀프로젝트 1조.pdf](ML%20팀프로젝트%201조.pdf)
- [아이데이션 보드](아이데이션.png)
- [프로젝트 워크플로우](workflow.png)

문서 기준일: 2026-08-16
