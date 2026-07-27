---
description: 현재 레포에 worklog·plan·AGENTS 문서 규율 구조를 심고, 자동 문서화 규칙을 CLAUDE.md에 주입한다
argument-hint: "[작업자 이름 (선택)]"
allowed-tools: Bash, Read, Write, Edit
---

# init-workstructure

현재 작업 디렉터리(레포 루트)에 **worklog-discipline** 문서 규율을 설치한다.
플러그인 자원은 `${CLAUDE_PLUGIN_ROOT}` 아래에 있다. 아래를 순서대로, 멱등하게(이미 있으면 건드리지 않게) 실행하라.

## 1. 디렉터리 생성

없을 때만 생성한다:

```
docs/worklog/
docs/plan/active/
docs/plan/completed/
docs/plan/templates/
```

## 2. 템플릿 복사

`${CLAUDE_PLUGIN_ROOT}/templates/` 의 세 파일을 `docs/plan/templates/` 로 복사한다.
**대상에 같은 이름이 이미 있으면 덮어쓰지 말고 건너뛴다** (프로젝트가 커스터마이즈했을 수 있음):

- `DEV_LOG_TEMPLATE.md`
- `HANDOVER_TEMPLATE.md`
- `AGENTS_TEMPLATE.md`

## 3. handover-index 생성

`docs/plan/handover-index.md` 가 없으면 아래 골격으로 생성한다 (있으면 건드리지 않는다):

```markdown
# Handover Index

> ⚙️ 이 파일은 자동으로 관리됩니다. 기능 인계 완료 시 갱신됩니다.
> 상세 문서는 `docs/plan/completed/` 참조.

---

## 인계 현황

| 날짜 | 기능명 | 담당자 | 문서 경로 | 상태 |
| ---- | ------ | ------ | --------- | ---- |

---

## 인계 대기 중

`docs/plan/active/` 에 있는 문서들:

| 기능명 | 문서 경로 | 작성일 |
| ------ | --------- | ------ |
```

## 4. 자동 문서화 규칙을 CLAUDE.md에 주입 (핵심)

`${CLAUDE_PLUGIN_ROOT}/rules/auto-doc-rules.md` 의 **전체 내용**을 레포 루트 `CLAUDE.md` 에 넣는다. 멱등 규칙:

- `CLAUDE.md` 가 없으면: `# CLAUDE.md` 헤더 + 규칙 블록으로 새로 만든다.
- 이미 `<!-- worklog-discipline:auto-doc-rules` 마커가 있으면: 그 시작 마커부터 `<!-- /worklog-discipline:auto-doc-rules -->` 끝 마커까지를 새 내용으로 **교체**한다. 단 교체 시 기존 블록의 `<!-- PROJECT-EXTENSIONS:` 아래 프로젝트가 손으로 추가한 확장 규칙이 있으면 **보존해서 새 블록의 같은 위치에 옮겨 붙인다**.
- 마커가 없으면: 파일 끝에 개행 두 줄 후 규칙 블록을 append 한다.

## 5. 루트 AGENTS.md (선택)

레포 루트에 `AGENTS.md` 가 없으면 `${CLAUDE_PLUGIN_ROOT}/templates/AGENTS_TEMPLATE.md` 형식으로 최소 골격만 만든다(Parent 줄은 삭제). 이미 있으면 건드리지 않는다.

## 6. 작업자 이름

`$ARGUMENTS` 로 작업자 이름이 주어졌으면 기억해 두고, 이후 worklog의 "작업자"에 쓴다. 없으면 이번엔 넘어가고 첫 worklog 작성 시 사용자에게 물어본다.

## 7. 결과 보고

무엇을 새로 만들고 / 건너뛰고 / 교체했는지 표로 요약한다. 그리고 다음 한 줄을 안내한다:
"이제 작업이 끝날 때마다 `docs/worklog/{오늘}.md` 에 자동으로 기록됩니다. 기능 개발을 시작하려면 '개발 시작해줘: {기능명}' 이라고 하세요."
