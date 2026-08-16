# XLS · 인코딩 · 수식 셀 복구 보고서

실행일: 2026-08-16  
범위: `data/MANIFEST.csv`에서 `review_required`로 분류된 원본 8개

## 결론

- 원본 8개의 경로와 SHA-256은 작업 전후 모두 동일하다.
- 정규화본 8개를 별도 `data/normalized/` 아래에 생성했다.
- 문제 원본 8개, 총 93,096,821 bytes를 `data/archive/source_problematic/`에 경로 구조 그대로 복사하고 SHA-256 일치를 확인했다.
- `.xls` 확장자지만 실제 CSV인 5개는 UTF-8 BOM CSV와 공통 리뷰 스키마로 변환했다.
- 혼합 인코딩 손상 파일 1개는 검증된 정상 대응본을 이용해 34,474행으로 복구했다.
- XLSX 2개의 리뷰 셀 6개는 수식이 아닌 표시 내용 그대로의 문자열로 복원했다.
- 정규화본 8개 모두 행 수·스키마·체크섬·수식 제거 검증을 통과했다.

## 복구 결과

| 문제 | 파일 수 | 처리 결과 |
|---|---:|---|
| `.xls` 확장자지만 실제 CSV | 5 | UTF-8 BOM CSV, 13열 공통 스키마로 변환 |
| 8열 헤더 아래 6열 행 | 1개 파일의 405행 | 누락된 `전체_방문자_리뷰_수`, `정렬기준`을 공란으로 두고 `수집_상태=hybrid_recovered` 기록 |
| 혼합 CP949·UTF-8 손상 | 1 | 정상 v2.0 34,476행에서 완전 중복 2행을 제거한 34,474행으로 복구 |
| XLSX 수식형 리뷰 텍스트 | 2개 파일의 6셀 | 계산 결과가 아니라 원래 표시 문자열로 저장 |

## 공통 리뷰 스키마

```text
매장명, 주소, 유저ID, 날짜, 별점, 리뷰_내용,
카테고리, 블루리본_여부, 정렬기준, 전체_방문자_리뷰_수,
수집_상태, 원천_파일, 복구_근거_파일
```

원본에 없던 값은 추측하지 않고 공란으로 둔다. 파생 가능한 `카테고리`, `블루리본_여부`, `정렬기준`만 파일의 수집 단위에서 채웠다.

## 혼합 인코딩 파일의 복구 근거

손상 파일:

`code/catchtable/no_blueribbon_한식_리뷰_추천순_v2.3.csv`

복구 근거:

`data/review/한식/한식_no_blueribbon_리뷰_추천순_v2.0.csv`

손상 파일은 UTF-8·CP949·EUC-KR 어느 하나로도 엄격 디코딩되지 않는다. 정상 v2.0은 34,476행이며 완전 중복 2행을 제거하면 34,474행으로, 손상 v2.3의 행 수와 정확히 일치한다. 따라서 손상 바이트를 임의 치환하지 않고 정상 대응본의 중복 제거 결과를 복구본으로 사용했다. 복구본 모든 행에는 `수집_상태=recovered_from_clean_v2.0_deduplicated`와 복구 근거 파일을 기록했다.

## 산출물

- `data/normalized/reviews/`: 정규화된 CSV 6개
- `data/normalized/xlsx/`: 수식형 리뷰 셀을 문자열로 복원한 XLSX 2개
- `data/normalized/NORMALIZATION_LOG.csv`: 원본·결과 경로, SHA-256, 행 수, 복구 수와 판정
- `data/normalized/NORMALIZED_MANIFEST.csv`: 정규화본 8개의 고유 ID와 체크섬
- `data/archive/source_problematic/ARCHIVE_MANIFEST.csv`: 원본과 archive 사본의 경로·크기·SHA-256 검증 기록
- `outputs/recovery_20260816/data_recovery_qa.xlsx`: 요약, 파일별 로그, 복구 셀 QA 워크북

## 재실행과 검증

```powershell
python code\scripts\normalize_tabular_sources.py
python code\scripts\patch_xlsx_formula_text_cells.py
python code\scripts\verify_normalized_data.py
```

일식 XLSX 복구와 QA 워크북 생성에는 스프레드시트 artifact 런타임을 사용한다. `merge_blue.xlsx`는 전체 재직렬화 시 8GB 힙을 초과하므로, 대상 셀 `G86987`만 OOXML `inlineStr`로 바꾸고 나머지 ZIP 멤버를 그대로 복사한다.

## 다음 단계

정규화본을 입력으로 최신순·추천순 리뷰를 공통 스키마에 맞춰 합치고, 중복·충돌 보고서와 `combined` 버전을 생성한다. 기존 `*_merged` 파일은 최신순과 추천순의 완전 합집합이 아니므로 기준본으로 사용하지 않는다.
