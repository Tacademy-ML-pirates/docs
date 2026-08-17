# 데이터 MANIFEST 및 원본 체크섬

생성일: 2026-08-16  
대상: 프로젝트 루트와 `data/`, `code/` 아래의 CSV/XLS/XLSX 파일

## 결과

- 표 형식 파일 69개, 총 615,802,173 bytes를 SHA-256으로 고정했다.
- 실제 저장 형식은 구분자 텍스트 62개와 XLSX 7개다.
- `.xls` 확장자 5개는 모두 실제 Excel 파일이 아니라 구분자 텍스트다.
- 동일한 바이트 내용을 가진 파일 그룹은 3개다.
- 8개 파일은 확장자 불일치, 인코딩 손상 후보, 비정상 행 또는 수식으로 저장된 리뷰 셀 때문에 후속 검토가 필요하다.
- Google Drive의 표 형식 파일 53개에 파일 ID와 URL을 기록했다.
- Drive 53개 중 45개는 파일명(별칭 포함)과 바이트 크기로 로컬 사본과 대응했고, 8개는 Drive에만 있다.
- 로컬 파일 69개 전부에 기존 경로와 제안 정규 경로의 매핑을 만들었다.
- 이번 단계에서는 원본 데이터의 이름·내용·위치를 변경하지 않았다.

## 산출물

- `data/MANIFEST.csv`: 파일별 경로, 분류, 확장자, 실제 형식, 인코딩, 크기, 수정 시각, SHA-256, 중복 그룹, 행·열 수와 품질 경고
- `data/MANIFEST.sha256`: 일반 체크섬 검증 도구에서 사용할 수 있는 `SHA-256 *상대경로` 목록
- `data/DRIVE_MANIFEST.csv`: Drive 파일 53개의 ID, 경로, MIME 형식, 크기, 수정 시각과 URL
- `data/LOCAL_DRIVE_MAPPING.csv`: Drive 파일과 로컬 파일의 대응 관계 및 Drive 전용 파일 표시
- `data/PATH_MAPPING.csv`: 로컬 파일 69개의 `old_path → canonical_path` 제안과 후속 조치
- `code/scripts/build_data_manifest.py`: 같은 규칙으로 MANIFEST를 다시 생성하는 스크립트
- `code/scripts/build_data_mappings.py`: 한글 NFC 정규화 후 Drive·로컬 및 경로 매핑을 재생성하는 스크립트

`MANIFEST.csv`는 Excel에서 한글이 깨지지 않도록 UTF-8 BOM으로 저장한다. `MANIFEST.sha256`과 체크섬 비교 시 기준 경로는 프로젝트 루트다.

## 품질 경고 8개

| 구분 | 파일 수 | 조치 |
|---|---:|---|
| `.xls` 확장자지만 실제 CSV 텍스트 | 5 | 내용을 바꾸지 않고 `.csv` 정규화본 생성 |
| UTF-8 치환 문자로 인코딩이 손상된 후보 | 1 | 원본·Drive 사본과 대조 후 복구 또는 격리 |
| XLSX 리뷰 셀이 수식으로 저장됨 | 2 | 지정 셀을 문자열로 복원한 정규화본 생성 |
| 비정상 열 수가 포함된 파일 | 1 | 누락 열을 명시적 NULL과 복구 상태 열로 보존 |

한 파일에 두 경고가 동시에 있을 수 있으므로 표의 합계는 고유 파일 수와 다를 수 있다.

## 현재 기준 버전

| 역할 | 현재 파일 |
|---|---|
| 재학습 기준 데이터 | `data/blueribbon_final_reduced_v1.2.1.csv` |
| 전체 특성 보존 기준 | `data/blueribbon_final_full_v1.1.1.csv` |
| KoELECTRA 최신 특성 후보 | `data/KoELECTRA_X_feature_v2.3_weighted.csv` |
| 발표 수치 재현용 | `data/blueribbon_final_reduced_presentation_v1.2.0.csv` |

재학습 기준 1,428개와 발표 재현 기준 1,440개는 섞지 않는다.

## 재생성

프로젝트 루트에서 다음을 실행한다.

```powershell
python code\scripts\build_data_manifest.py
python code\scripts\build_data_mappings.py
```

재생성 전후 `MANIFEST.csv`의 SHA-256과 파일 크기를 비교하면 원본 변경 여부를 확인할 수 있다. 다음 단계에서는 `review_required` 8개를 원본 보존 방식으로 정규화한다.
