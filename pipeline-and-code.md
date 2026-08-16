# 파이프라인·코드

## 전체 흐름

```text
매장 목록 수집
→ 네이버 리뷰 수집(최신순·추천순)
→ 리뷰 복구·공통 스키마·결합
→ KoELECTRA 감정 분석과 주제 지수 생성
→ 내부 이미지 수집·필터링·CLIP 특성 생성
→ 매장 단위 텍스트·이미지 피처 결합
→ EDA·통계 검정
→ LightGBM / XGBoost 학습·평가
→ 신규 식당 예측·설명·데이터 완성도 표시
```

## 단계별 계약

### 1. 매장 목록

- 블루리본 선정·비선정 매장을 분리해 수집한다.
- 매장명, 카테고리, 주소, 블루리본 여부·개수, 신규 여부를 공통 열로 맞춘다.
- 지역·업종별 표본 불균형과 수집 시점을 기록한다.

### 2. 리뷰 수집과 결합

- 최신순과 추천순을 서로 다른 원천으로 보존한다.
- 리뷰 탭 없음, 200개 제한, 수동 복구, 중단·재시작 상태를 manifest에 기록한다.
- 복구본을 사용하더라도 실제 원천과 처리 입력 경로를 행 단위로 모두 남긴다.
- 정확 중복만 합치고 본문·별점 충돌 후보는 보존한다.

재현 진입점:

```powershell
python code/scripts/normalize_tabular_sources.py
python code/scripts/patch_xlsx_formula_text_cells.py
python code/scripts/verify_normalized_data.py
python code/scripts/build_combined_reviews.py
python code/scripts/verify_combined_reviews.py
```

### 3. 텍스트 특성

프로젝트 최종 텍스트 특성은 한국어 구어체 리뷰를 위해 검토했던 KcELECTRA가 아니라, 대규모 한국어 리뷰 말뭉치 기반 KoELECTRA 계열 모델을 사용한 결과다. 가족, 기념일, 데이트, 거리, 재방문, 서비스, 분위기, 전망, 맛, 가격, 웨이팅, 고급성, 실망의 점수·빈도·비율·가중 점수를 생성한다.

현재 기준 파일은 `KoELECTRA_X_feature_v2.3_weighted.csv`다. v2.1→v2.2에서 공통 값 재계산이 있었고, v2.2→v2.3은 가중 점수 13열을 추가한 변경이다.

### 4. 이미지 특성

- 내부 이미지 필터링 후 CLIP으로 고급성·쾌적성·감성 점수를 만든다.
- 매장명은 유니코드 정규화를 적용하고 주소를 보조 키로 사용한다.
- 이미지가 없거나 내부 사진 통과 수가 0이면 0으로 가장하지 않고 결측과 완성도 필드로 구분한다.

### 5. 모델링·평가

- 데이터 버전·SHA-256과 split을 먼저 고정한다.
- LightGBM·XGBoost는 같은 피처 행렬과 같은 train/validation/test 행을 사용한다.
- 임계값은 validation에서 선택하고 test는 최종 한 번만 평가한다.
- 예측 확률, 실제값, 오류 유형, 데이터 완성도를 식당 단위로 저장한다.

재평가 진입점:

```powershell
python -X utf8 code/scripts/reevaluate_models_1428.py
python -X utf8 code/scripts/verify_model_reevaluation.py
```

## 코드 문서 상태

각 실행 파일은 다음 상태 중 하나를 명시한다.

- `canonical`: 현재 기준 실행 경로
- `superseded`: 더 최신 구현으로 대체됨
- `experimental`: 탐색용, 결과 기준선으로 사용하지 않음
- `broken`: 입력·의존성·경로 문제로 현재 재현 불가

코드 문서에는 목적, 상태, 입력·출력 버전, 핵심 파라미터, 실행 환경, GitHub permalink, 알려진 문제를 기록한다.

## 출처

- [Notion 02. 파이프라인 · 코드](https://app.notion.com/p/3a8bf34cc2ef8121970ccfa39eed04f9)
- [code 저장소](https://github.com/Tacademy-ML-pirates/code)
- [code README](https://github.com/Tacademy-ML-pirates/code/blob/main/README.md)
