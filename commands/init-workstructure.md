---
description: 현재 레포에 research 또는 poc 문서 구조를 심고, 자동 문서화 규칙을 CLAUDE.md에 주입한다
argument-hint: "<research|poc> [작업자 이름 (선택)]"
allowed-tools: Bash, Read, Write, Edit
---

# init-workstructure

현재 작업 디렉터리(Claude가 실행된 위치, `pwd`)에 **worklog-discipline** 문서 규율을 설치한다. git 레포 루트로 이동하지 않는다 — 아래 모든 경로는 현재 작업 디렉터리 기준이다.
플러그인 자원은 `${CLAUDE_PLUGIN_ROOT}` 아래에 있다. 아래를 순서대로, 멱등하게(이미 있으면 건드리지 않게) 실행하라.

## 0. 모드 결정

`$ARGUMENTS` 의 첫 단어가 `research` 또는 `poc` 면 그것이 모드다. 나머지는 작업자 이름이다.

- 모드가 없으면: `CLAUDE.md` 의 `<!-- worklog-discipline:auto-doc-rules ... mode=X -->` 마커에서 기존 모드를 읽는다.
- 그것도 없으면: 사용자에게 research / poc 중 무엇인지 묻는다.
  - research: 실험·조사 중심 (report, reference)
  - poc: 개발 계획 중심 (plan/active, plan/done, reference)

## 1. 디렉터리 생성

없을 때만 생성한다.

| research | poc |
|---|---|
| `docs/worklog/` | `docs/worklog/` |
| `docs/report/` | `docs/plan/active/` |
| `docs/reference/` | `docs/plan/done/` |
| | `docs/reference/` |

빈 디렉터리에는 git 추적용 `.gitkeep` 을 둔다. `src/` 는 만들지 않는다 (코드 위치는 프로젝트가 정한다).
날짜별 로그 파일(`docs/worklog/{YYYY_MM_DD}.md`)은 미리 만들지 않는다 — 첫 작업 기록 시 규칙에 따라 생성된다.

## 2. docs/index.md

없을 때만 만든다. 1단계의 폴더마다 `## {폴더}` 섹션과 한 줄 설명을 두고, worklog 섹션에는 `[worklog](worklog/)` 링크만 둔다. 이미 있으면 건드리지 않는다.

## 3. 자동 문서화 규칙을 CLAUDE.md에 주입 (핵심)

규칙 블록을 조립한다: `${CLAUDE_PLUGIN_ROOT}/rules/core.md` 를 읽어
- 첫 줄 마커의 `{MODE}` 를 모드로 바꾸고,
- `<!-- MODE-RULES -->` 줄을 `${CLAUDE_PLUGIN_ROOT}/rules/{모드}.md` 의 전체 내용으로 바꾼다.

조립한 블록을 현재 작업 디렉터리의 `CLAUDE.md` 에 넣는다. 멱등 규칙:

- `CLAUDE.md` 가 없으면: `# CLAUDE.md` 헤더 + 규칙 블록으로 새로 만든다.
- 이미 `<!-- worklog-discipline:auto-doc-rules` 마커가 있으면: 그 시작 마커부터 `<!-- /worklog-discipline:auto-doc-rules -->` 끝 마커까지를 새 내용으로 **교체**한다. 단 교체 시 기존 블록의 `<!-- PROJECT-EXTENSIONS:` 아래 프로젝트가 손으로 추가한 확장 규칙이 있으면 **보존해서 새 블록의 같은 위치에 옮겨 붙인다**.
- 마커가 없으면: 파일 끝에 개행 두 줄 후 규칙 블록을 append 한다.

## 4. AGENTS.md (선택)

현재 작업 디렉터리에 `AGENTS.md` 가 없으면 `${CLAUDE_PLUGIN_ROOT}/templates/AGENTS_TEMPLATE.md` 형식으로 최소 골격만 만든다(Parent 줄은 삭제). 이미 있으면 건드리지 않는다.

## 5. 작업자 이름

작업자 이름이 주어졌으면 기억해 두고, 이후 worklog의 "작업자"에 쓴다. 없으면 이번엔 넘어가고 첫 worklog 작성 시 사용자에게 물어본다.

## 6. 결과 보고

모드와 함께, 무엇을 새로 만들고 / 건너뛰고 / 교체했는지 표로 요약한다. 그리고 다음 한 줄을 안내한다:
"이제 커밋할 때마다(또는 "기록해줘" 요청 시) `docs/worklog/{YYYY_MM_DD}.md` (그날 날짜) 에 자동으로 기록됩니다."

기존 v2 구조(`docs/worklog/details/`, `docs/worklog/templates/`)가 있으면 지우거나 옮기지 말고, 남아 있다는 사실만 보고한다.
