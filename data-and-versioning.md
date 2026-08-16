# 데이터·품질·버전

## 기준선

| 영역 | 기준 파일 | 의미 |
|---|---|---|
| 전체 특성 모델링 | `블루리본_최최종_마참내_v1.1.1.csv` | 1,428행 × 67열, 중복 매장 12행 제거 |
| 축소 특성 모델링 | `블루리본_최최종_마참내_v1.2.1.csv` | 1,428행 × 22열, 재평가 기준 |
| 텍스트 특성 | `KoELECTRA_X_feature_v2.3_weighted.csv` | v2.2의 43열에 가중 점수 13열 추가 |
| 리뷰 결합본 | `reviews_*_combined_v1.0.0.csv` | 8개 그룹, 281,763행 |

`v1.1.1`과 `v1.2.1`은 같은 1,428개 매장 키를 사용하며 공통 22열 값이 같다. 발표 결과를 만든 1,440행 `v1.1`·`v1.2`와 중복 제거 후 재학습 기준을 섞지 않는다.

## 확인한 데이터 범위

- 로컬 표 형식 파일: 69개, 615,802,173 bytes
- Google Drive 표 형식 파일: 53개
- Drive와 로컬 대응: 45개
- Drive에만 있는 과거·테스트 산출물: 8개
- 내용 해시가 같은 로컬 중복 그룹: 3개

자세한 파일 단위 결과와 Drive 매핑은 [data-file-audit.md](data-file-audit.md), [data-manifest.md](data-manifest.md)를 기준으로 한다.

## 복구·정규화 결과

- 실제 CSV 텍스트인데 `.xls`로 저장된 5개를 공통 스키마 CSV로 변환
- 가변 6열 행 405개의 누락 메타데이터를 공란과 `hybrid_recovered` 상태로 보존
- 혼합 인코딩 손상 한식 리뷰 34,474행을 검증된 v2.0 원천의 중복 제거 결과로 복구
- XLSX 리뷰 수식 오인 셀 6개를 문자열로 복원
- 문제 원본 8개의 해시 동일 사본을 archive에 보관

복구본은 원본을 덮어쓰지 않는다. 원천 경로, 복구 근거, 행 수, 바이트, SHA-256을 `NORMALIZATION_LOG.csv`와 `NORMALIZED_MANIFEST.csv`에 기록한다. 세부 내용은 [data-normalization.md](data-normalization.md)에 있다.

## 리뷰 결합 규칙

1. 음식 종류 4개 × 선정 여부 2개, 총 8개 그룹의 최신순·추천순을 공통 15열 스키마로 맞춘다.
2. 비교 키는 NFKC 정규화, 공백 축약, 대소문자 무시를 적용하되 출력 원문은 보존한다.
3. `(매장명, 주소, 유저ID, 날짜, 별점, 리뷰_내용)`이 같은 경우만 정확 중복으로 합친다.
4. 같은 `(매장명, 주소, 유저ID, 날짜)`에 별점 또는 본문이 다르면 모든 변형을 유지하고 충돌 보고서에 기록한다.
5. 행마다 원천 파일, 실제 처리 입력, 정렬 기준, 수집·복구 상태를 남긴다.

입력 476,977행에서 정확 중복 195,214행을 결합해 281,763행을 생성했다. 충돌 후보 3,662그룹, 7,332행은 삭제하지 않았다. 기존 `*_merged.csv` 8개는 해시와 함께 archive에 보존했다. 자세한 검증표는 [review-combination.md](review-combination.md)에 있다.

## 공통 리뷰 스키마

```text
매장명, 주소, 유저ID, 날짜, 별점, 리뷰_내용,
카테고리, 블루리본_여부, 정렬기준, 전체_방문자_리뷰_수,
수집_상태, 원천_파일, 처리_입력_파일, 복구_근거_파일, 원천_중복_수
```

## 버전 변경 규칙

- 원천 내용이나 계산식 변경: minor 또는 major 버전 증가
- 정확 중복 제거·오탈자 정정처럼 의미를 보존하는 수정: patch 증가
- 파일명만으로 의미를 추측해야 하는 `_최최종`, `_마참내`, `_커트` 사용 중단
- 모든 기준본은 행·열 수, 키, 해시, 생성 코드 commit을 manifest에 기록
- Drive revision 기록만 믿지 않고 파일 manifest를 버전 계보의 기준으로 사용

## 출처

- [Notion 01. 데이터 · 품질 · 버전](https://app.notion.com/p/3a8bf34cc2ef81dfb9d6c2ed87449e5d)
- [Google Drive 데이터 폴더](https://drive.google.com/drive/folders/12DjeIDFp103VEpUEExMaS7mESkHR-A4b?usp=sharing)
- [데이터 파일 감사](data-file-audit.md)
- [원본 manifest](data-manifest.md)
- [복구·정규화](data-normalization.md)
- [리뷰 결합](review-combination.md)
