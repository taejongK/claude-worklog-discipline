<!-- worklog-discipline:auto-doc-rules v0.1.0 -->
<!-- 이 블록은 worklog-discipline 플러그인의 /init-workstructure 가 주입한다. 통째로 교체/갱신되므로 이 마커 사이를 직접 편집하지 말 것. -->

## 자동 문서화 규칙 (필수)

작업과 함께 문서를 갱신한다. 아래 상황에서는 **사용자가 요청하지 않아도 자동으로** 실행한다.

### 작업 완료 시 (Worklog) — 항상

```
1. 작업이 끝나면 docs/worklog/{YYYY_MM_DD}.md 파일에 기록한다.
   - 파일이 없으면 생성, 있으면 해당 날짜 파일에 작업별로 append.
   - 파일명 형식: YYYY_MM_DD.md (예: 2026_07_10.md)

2. 각 작업 항목에 반드시 포함:
   - 작업자 이름 (모르면 사용자에게 물어본다)
   - 대상 파일/모듈
   - 무엇을·왜 변경했는지, 검증 방법/결과

3. 헤더 최소화 — 헤더를 여러 단계로 쓰지 않는다.
   각 작업 항목 제목만 `###`로 구분하고, 항목 내부 구분(배경/방법/결과/검증 등)은
   별도 헤더 대신 볼드 라벨·불렛으로 가볍게 표현한다.
```

### 기능 개발 시작 시 (devlog)

```
docs/plan/templates/DEV_LOG_TEMPLATE.md 를 복사해
docs/plan/active/{YYYYMMDD}_{기능명}_devlog.md 생성:
- 기능명·목표·완료 기준을 채우고 초기 개발 계획을 작성한다.
```

### 개발 중 방향/결정 변경 시 (devlog 갱신)

```
docs/plan/active/{기능명}_devlog.md 갱신:
- 변경 이력 테이블에 날짜·변경 내용·이유를 추가한다.
- 현재 상태 섹션(마지막 작업/다음 할 일)을 갱신한다.
```

### 기능 구현 완료 시 (인계 문서)

```
docs/plan/templates/HANDOVER_TEMPLATE.md 를 복사해
docs/plan/active/{YYYYMMDD}_{기능명}.md 생성:
- 핵심 구현 로직, 데이터 흐름, 설계 결정, 구현 시 주의사항을 채운다.
docs/plan/handover-index.md 의 인계 대기 테이블에 추가한다.
```

### 인계 완료 시

```
docs/plan/active/{기능명}.md 와 {기능명}_devlog.md 를 docs/plan/completed/ 로 이동.
docs/plan/handover-index.md 의 인계 현황 테이블을 갱신한다.
```

### 디렉터리 구조 변경 시 (AGENTS.md 계층)

```
새 디렉터리 추가/역할 변경 시 해당 디렉터리의 AGENTS.md 를 갱신한다.
- 상위 디렉터리 AGENTS.md 의 Subdirectories 표도 함께 갱신.
- <!-- MANUAL: --> 마커 아래 손으로 쓴 내용은 절대 덮어쓰지 않는다.
- 없으면 docs/plan/templates/AGENTS_TEMPLATE.md 형식으로 새로 만든다.
```

### 프로젝트별 확장 규칙 (Extension)

> 이 프로젝트 고유의 자동 문서화 규칙(예: 모델 레지스트리, API 레퍼런스, 스키마 변경 로그)은
> 아래에 추가한다. 위 코어 규칙은 플러그인이 갱신 시 교체하지만, 이 확장 절은 건드리지 않는다.

<!-- PROJECT-EXTENSIONS: 아래에 프로젝트 고유 규칙을 적는다 -->

<!-- /worklog-discipline:auto-doc-rules -->
