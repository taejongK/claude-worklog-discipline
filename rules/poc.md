### 문서 구조 (poc)

```
docs/
  index.md               문서 목차·요약·링크
  worklog/{YYYY_MM_DD}.md  날짜별 작업 로그
  plan/active/           진행 중인 개발 계획
  plan/done/             완료된 개발 계획
  reference/             참고 문서·이미지
```

### 개발을 시작할 때 (docs/plan)

```
여러 커밋에 걸치는 개발은 시작 전에 docs/plan/active/{주제}.md 로 계획을 쓴다.
- 구성: 목표 / 범위(안 하는 것 포함) / 단계별 할 일(체크박스) / 완료 기준
- 진행하면서 체크박스와 변경된 결정을 갱신한다. 긴 디버깅·설계 비교도 이 문서에 적는다.
- 그날 worklog 작업 항목에 링크를 남긴다:
  - 계획: [{주제}](../plan/active/{주제}.md)
- 완료 기준을 충족하면 docs/plan/done/ 으로 옮기고 결과 한 줄을 문서 끝에 덧붙인다.
  옮긴 뒤 index.md 링크를 고친다 (worklog 의 과거 링크는 고치지 않는다).
```

### 참고 자료를 받았을 때 (docs/reference)

```
작업에 참고하는 문서·이미지(스펙, 화면 시안, 스크린샷 등)를 docs/reference/ 에 저장하고,
쓰는 plan 문서에서 링크한다.
```
