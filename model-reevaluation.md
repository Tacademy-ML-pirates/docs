# 1,428건 기준 모델 재평가

실행일: 2026-08-16  
기준 데이터: `data/blueribbon_final_reduced_v1.2.1.csv`

## 결론

발표 당시 사용한 1,440행 `v1.2`가 아니라 중복 매장 12행을 제거한 1,428행 `v1.2.1`을 재학습 기준으로 고정했다. LightGBM과 XGBoost에 같은 피처 행렬과 같은 층화 분할을 적용하고, 검증셋에서 선택한 임계값을 고정한 뒤 테스트셋을 한 번 평가했다.

테스트 기준 XGBoost가 ROC-AUC 0.6939, PR-AUC 0.7334, F1 0.6919로 LightGBM보다 조금 높았다. 다만 두 모델 모두 검증 대비 테스트 성능이 낮아졌고, 학습 ROC-AUC가 0.98 이상이므로 최종 운영 모델로 확정하기보다 **재현 가능한 새 기준선**으로 보는 것이 안전하다.

## 실행 기준

| 항목 | 값 |
|---|---|
| 데이터 버전 | `v1.2.1`, 1,428행 × 22열 |
| 데이터 SHA-256 | `21c385895d87e3b4a25c47ba18e5175a11675d5ff70eb1b5f3f411fdfdfc093c` |
| code 저장소 기준 commit | `63111030e950bb71e90afd87e562aa777374ad3f` |
| 작업 트리 | 실행 시 미커밋 파일 있음 |
| 분할 | train 856 / validation 286 / test 286 |
| 분할 규칙 | 층화 60/20/20, `random_state=42` |
| 피처 | 가중 리뷰·이미지 수치 19개 + 음식 카테고리 원-핫 4개 = 23개 |
| 타깃 분포 | 비선정 689 / 선정 739 |
| 임계값 선택 | validation F1 최대, 동률이면 0.5에 가까운 값 |
| 패키지 | LightGBM 4.6.0, XGBoost 3.0.2, scikit-learn 1.5.1 |

commit은 기존 `code` 저장소의 실행 기준점을 나타낸다. 이번 재평가 스크립트와 산출물은 당시 작업 트리에 새로 추가되어 있었으므로 `code_working_tree_dirty=true`도 함께 기록했다.

## 모델 비교

| 모델 | split | 임계값 | ROC-AUC | PR-AUC | F1 | Precision | Recall | Accuracy |
|---|---|---:|---:|---:|---:|---:|---:|---:|
| LightGBM | validation | 0.2350 | 0.7252 | 0.7340 | 0.7102 | 0.5787 | 0.9189 | 0.6119 |
| LightGBM | test | 0.2350 | 0.6820 | 0.7304 | 0.6823 | 0.5551 | 0.8851 | 0.5734 |
| XGBoost | validation | 0.2809 | 0.7374 | 0.7498 | 0.7170 | 0.5964 | 0.8986 | 0.6329 |
| XGBoost | test | 0.2809 | 0.6939 | 0.7334 | 0.6919 | 0.5766 | 0.8649 | 0.6014 |

낮은 임계값은 검증 F1을 최대화하면서 선정 식당 재현율을 높인 결과다. 그 대신 비선정 식당을 선정으로 예측하는 false positive가 많으므로, 실제 서비스 목적이 정밀도 중심이면 별도의 비용 함수나 최소 precision 제약으로 임계값을 다시 정해야 한다.

## 테스트 confusion matrix

### LightGBM

| 실제 \ 예측 | 비선정 0 | 선정 1 |
|---|---:|---:|
| 비선정 0 | 33 | 105 |
| 선정 1 | 17 | 131 |

### XGBoost

| 실제 \ 예측 | 비선정 0 | 선정 1 |
|---|---:|---:|
| 비선정 0 | 44 | 94 |
| 선정 1 | 20 | 128 |

## 오류 사례와 데이터 완성도

`test_predictions.csv`는 식당별 예측과 함께 다음 두 값을 기록한다.

- `data_completeness`: 모델 입력 23개 중 결측이 아닌 비율
- `image_data_completeness`: 고급성·쾌적성·감성 이미지 피처 6개의 완성 비율

테스트 286개 중 79개는 이미지 피처가 전부 비어 있었다. 두 모델 모두 오분류 행의 평균 전체 완성도는 약 0.904, 정분류 행은 약 0.944였다. 이미지 완성도도 오분류 약 0.632, 정분류 약 0.79로 차이가 있었다. 이는 인과관계 증명이 아니라 **누락 데이터가 예측 신뢰도를 낮출 수 있다는 운영 경고**다.

신규 식당 예측 결과를 제공할 때는 확률만 노출하지 말고 두 완성도와 이미지 데이터 유무를 함께 표시해야 한다. 이미지 완성도가 0인 경우에는 `image_missing` 경고를 붙이고, 운영 임계값 결정 전 별도 검증 집합으로 재확인한다.

## 주요 피처

LightGBM의 분할 횟수 상위에는 가격, 재방문, 실망, 서비스, 고급성, 분위기, 웨이팅, 전망 가중 점수가 있었다. XGBoost 상위에는 고급성, 맛, 중식 카테고리, 재방문, 기념일, 서비스 점수가 포함됐다. 모델별 importance 정의가 다르므로 절댓값을 서로 비교하지 않고 모델 안에서의 순위만 해석한다.

## 산출물과 재현

- `outputs/model_reevaluation_20260816/metrics.csv`
- `outputs/model_reevaluation_20260816/confusion_matrices.csv`
- `outputs/model_reevaluation_20260816/test_predictions.csv`
- `outputs/model_reevaluation_20260816/error_cases.csv`
- `outputs/model_reevaluation_20260816/feature_importance.csv`
- `outputs/model_reevaluation_20260816/run_metadata.json`
- `outputs/model_reevaluation_20260816/models/`

```powershell
python -X utf8 code/scripts/reevaluate_models_1428.py
python -X utf8 code/scripts/verify_model_reevaluation.py
```

검증 스크립트는 저장된 테스트 예측에서 모든 지표와 confusion matrix를 다시 계산하고, 데이터 완성도가 0~1 범위인지 확인한다.

## 발표 결과와의 관계

발표 자료와 기존 코드의 결과는 1,440행 `v1.2`에서 생성되었다. 이번 결과는 중복을 제거한 1,428행 `v1.2.1`, 별도 validation split과 validation 기반 임계값을 사용하므로 발표 지표의 단순 재현이 아니다. 앞으로의 비교는 이 문서의 분할·버전·해시를 기준으로 해야 한다.
