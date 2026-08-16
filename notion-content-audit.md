# Notion 문서 구조 감사 및 정리안

작성일: 2026-07-23  
기준 페이지: [T아카데미 11기 ML Team Project](https://app.notion.com/p/d7ebf34cc2ef820eae6281169db9a070)

## 현재 구조 요약

메인 페이지에는 프로젝트 소개, 팀원, GitHub/Drive 링크, 회의록, 문서, 레퍼런스, Code, 데이터 목록, To Do 데이터베이스, 예측용 신규 식당 목록이 함께 들어 있다.

직접 연결된 주요 영역:

- [회의록](https://app.notion.com/p/22fbf34cc2ef83d0892d81bd273acc91): 별도 회의록 데이터베이스를 포함
- [문서](https://app.notion.com/p/dc3bf34cc2ef823facda816f9802af23): 과거 계획서·보고서와 Kaggle 노트북 첨부
- [레퍼런스](https://app.notion.com/p/affbf34cc2ef83069c48816629d0e75f): 공모전 수상작과 이전 ML 프로젝트 자료
- [Code](https://app.notion.com/p/365bf34cc2ef80d1b86fc51991ebe71c): 크롤링, 전처리, EDA, 모델링 코드를 담은 데이터베이스
- [데이터 목록](https://app.notion.com/p/367bf34cc2ef80ad9e09f6fcc488c928): 외부 데이터 후보와 일부 첨부 파일
- [ML Kaggle 과제](https://app.notion.com/p/367bf34cc2ef8061b927c24fee451f6e): 팀 프로젝트와 직접 관련이 적은 교육 과제
- To Do List: 상태, 담당자, 마감일, 우선순위, 첨부 파일 속성을 가진 작업 데이터베이스

## 확인된 문제

### 프로젝트 문서와 작업 기록이 섞여 있음

메인 페이지에 영구 문서, 임시 메모, 신규 식당 후보, 작업 목록이 함께 있어 프로젝트 결과를 처음 보는 사람이 흐름을 따라가기 어렵다.

### Code 데이터베이스가 사실상 코드 저장소 역할을 함

Code 영역에는 다음 종류가 한 데이터베이스에 섞여 있다.

- 블루리본/비블루리본 매장명 수집
- 네이버 리뷰 크롤러 v1.0, v2.0, v2.1과 복구/200개 제한 변형
- Catchtable 수집
- KoELECTRA 감정 분석과 가중치/T-test
- EDA
- LightGBM, XGBoost, 하이퍼파라미터 튜닝
- 평가와 최종 통합 코드

코드 본문에는 `블루리본_최최종_마참내_v1.1.csv`, `v1.2.csv` 같은 구버전 파일명과 개인 PC 절대 경로가 남아 있다. GitHub를 기준 코드 저장소로 삼고, Notion에는 목적·입력·출력·최종 구현 링크만 남기는 편이 좋다.

### 버전 의미가 문서화되지 않음

`v1.0`, `v2.0`, `v2.1`, `복구코드`, `200개 짜르는 버전`, `최종 전체 코드 통합` 등이 제목에만 표현돼 있다. 어떤 코드가 기준본인지, 입력과 출력이 무엇인지, 이전 버전과 무엇이 달라졌는지 한눈에 알기 어렵다.

### 데이터 목록이 현재 프로젝트 데이터 카탈로그가 아님

데이터 목록에는 AI-Hub, KT 빅데이터 플랫폼, 토스증권 데이터 등 주제 선정 단계의 후보와 실제 식당 데이터가 함께 있다. 현재 Drive/로컬 파일의 데이터 계보, 스키마, 기준 버전은 기록되어 있지 않다.

### 복제된 메인 페이지와 하위 트리

워크스페이스 검색에서 같은 제목의 메인 페이지가 두 개 확인된다.

- 사용자가 전달한 기준 페이지: `d7ebf34c...`
- 2026-07-23에 다시 만들어진 별도 페이지: `aa2bf34c...`

두 페이지의 본문은 사실상 같지만 회의록, 문서, 레퍼런스, Code, 데이터 목록, To Do의 하위 페이지 ID가 모두 다르다. 즉 메인 페이지만 중복된 것이 아니라 하위 트리 전체가 복제된 상태다. 사용자가 전달한 `d7ebf34c...`를 임시 기준으로 조사했으며, 어느 트리를 기준본으로 삼을지 확정하기 전에는 삭제하거나 교차 병합하면 안 된다.

## 권장 Notion 구조

```text
T아카데미 11기 ML Team Project
├─ 00. 프로젝트 개요
│  ├─ 문제 정의와 목표
│  ├─ 팀과 역할
│  └─ 핵심 링크
├─ 01. 데이터
│  ├─ 데이터 수집
│  ├─ 데이터 사전
│  ├─ 파일·버전 계보
│  └─ 데이터 품질 이슈
├─ 02. 파이프라인
│  ├─ 매장 수집
│  ├─ 리뷰 수집
│  ├─ 이미지 분석
│  └─ 감정 분석·특성 생성
├─ 03. 분석과 모델링
│  ├─ EDA
│  ├─ LightGBM
│  ├─ XGBoost
│  └─ 모델 비교와 선택
├─ 04. 평가와 결과
│  ├─ 평가 지표
│  ├─ 신규 식당 예측
│  ├─ T-test·시각화
│  └─ 발표 결과·피드백
├─ 05. 회의와 의사결정
│  ├─ 회의록 데이터베이스
│  └─ 결정 로그
├─ 06. 작업 관리
│  └─ To Do List
├─ 07. 레퍼런스
└─ 99. Archive
   ├─ Kaggle 교육 과제
   ├─ 주제 선정 후보 데이터
   └─ 구버전 코드 페이지
```

## Code 페이지 정리 원칙

각 코드 페이지는 다음 템플릿으로 축약한다.

1. 목적
2. 기준 상태: `canonical`, `superseded`, `experimental`, `broken`
3. 입력 데이터와 정확한 버전
4. 출력 데이터와 정확한 버전
5. 핵심 처리 규칙
6. 실행 환경과 의존성
7. GitHub의 최종 코드 링크
8. 변경 이력
9. 알려진 이슈

Notion에 긴 코드 전문을 중복 보관하지 않고 GitHub permalink를 사용한다. 재현에 필요한 설명과 결정 근거만 Notion에 남긴다.

## docs 저장소로 옮길 권장 문서

```text
docs/
  README.md
  project-overview.md
  architecture/
    workflow.md
  data/
    data-sources.md
    data-dictionary.md
    data-lineage.md
    data-quality.md
  pipelines/
    store-collection.md
    review-collection.md
    image-analysis.md
    sentiment-feature-engineering.md
  modeling/
    experiments.md
    model-comparison.md
    evaluation.md
  decisions/
    README.md
  meetings/
  references.md
```

Notion은 협업과 탐색을 위한 허브로, GitHub docs는 버전 관리되는 기준 문서로 역할을 나누는 것이 좋다.

## 실행 순서

1. 두 복제 트리 중 기준 페이지를 하나로 확정한다. 현재 조사 기준은 사용자가 전달한 `d7ebf34c...`다.
2. 모든 하위 페이지에 `canonical/superseded/experimental/archive` 상태를 부여한다.
3. Code 데이터베이스의 페이지를 수집·전처리·분석·모델링·평가로 분류한다.
4. 로컬 데이터 감사 결과를 데이터 사전과 계보 페이지로 옮긴다.
5. 최신 코드 페이지의 개인 절대 경로와 구버전 파일명을 GitHub 기준 경로로 바꾼다.
6. 확정된 문서를 Markdown으로 만들어 docs 저장소에 커밋한다.
7. Notion 페이지에는 해당 GitHub 문서의 permalink를 연결한다.

현재 조사에서는 Notion 페이지를 생성·수정·이동·삭제하지 않았다.
