# worklog-discipline

AI가 일한 결과를 스스로 기록하게 만드는 **문서 규율**을 어떤 프로젝트에나 심는 Claude Code 플러그인.

챗봇 실험 하네스에서 검증된 3종 문서 체계를 도메인 제거 후 일반화했다:

- **worklog** — `docs/worklog/YYYY_MM_DD.md`, 작업 단위 append (작업자/대상/무엇·왜/검증)
- **plan** — devlog(진행 중) → 인계 문서(완료) → `handover-index.md` → `completed/` 이동
- **AGENTS.md 계층** — 디렉터리별 문서, `<!-- MANUAL: -->` 아래 수기 내용 보존

이 규율을 **자동 발동**시키는 엔진은 `rules/auto-doc-rules.md` 이며, 설치 시 대상 레포의 `CLAUDE.md` 에 주입된다.

## 구성

```
.claude-plugin/plugin.json     매니페스트
commands/
  init-workstructure.md        /init-workstructure — 대상 레포에 구조 설치 + CLAUDE.md 규칙 주입
  sync-agents.md               /sync-agents — 레포 스캔 후 디렉터리별 AGENTS.md 생성 (MANUAL 보존)
templates/                     DEV_LOG · HANDOVER · AGENTS 템플릿 (제네릭)
rules/auto-doc-rules.md        CLAUDE.md에 주입되는 구동 규칙 (교체 가능한 마커 블록)
```

## 설치

이 저장소는 **플러그인이자 마켓플레이스**다. Claude Code에서 저장소를 마켓플레이스로 등록한 뒤 플러그인을 설치한다:

```
/plugin marketplace add taejongK/claude-worklog-discipline
/plugin install worklog-discipline@worklog-discipline
```

> 설치에 쓰는 이름은 저장소명(`claude-worklog-discipline`)이 아니라 **플러그인명 `worklog-discipline`** 이다.
> 같은 PC에서 먼저 테스트하려면 원격 대신 로컬 경로로 등록할 수도 있다:
> `/plugin marketplace add /path/to/worklog-discipline`

## 사용

설치 후, **하네스를 세팅하려는 대상 프로젝트**에서 두 명령을 실행한다:

```
/init-workstructure [작업자이름]     # 1. 구조 + 규칙 설치
/sync-agents                          # 2. 현재 코드에 맞는 AGENTS.md 생성 (선택)
```

**1. `/init-workstructure`** — `docs/worklog/`·`docs/plan/{active,completed,templates}/` 트리와 템플릿을 깔고, 자동 문서화 규칙을 대상 레포의 `CLAUDE.md` 에 멱등하게 주입한다. 이 순간부터 작업이 끝날 때마다 worklog가, 기능 개발 시 devlog가 자동으로 작성된다.
재실행해도 안전하다 — 규칙 블록만 교체되고 프로젝트 확장 규칙(`PROJECT-EXTENSIONS`)은 보존된다.

**2. `/sync-agents`** — 레포를 순회하며 디렉터리별 `AGENTS.md` 를 코드 기반으로 생성/갱신한다. 기존 `<!-- MANUAL: -->` 아래 수기 내용은 보존한다. (worklog 자동작성과 무관한 별도 문서화 단계라 선택이다.)

이후에는 그냥 작업만 하면 된다 — 규칙이 `CLAUDE.md` 에 심겼으므로 Claude가 알아서 문서를 남긴다.

## 로드맵 (다음 단계)

- `commands/worklog.md`, `devlog.md`, `handover.md` — 각 문서를 손수 부를 수 있는 명령
- `skills/` — 작성 방법 지식 (헤더 최소화 규칙 등)
- `hooks/hooks.json` — Stop 훅으로 worklog 리마인더 백스톱
