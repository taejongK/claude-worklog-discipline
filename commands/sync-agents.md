---
description: 현재 레포를 순회하며 디렉터리별 AGENTS.md 문서를 코드 기반으로 생성/갱신한다 (MANUAL 블록 보존)
argument-hint: "[대상 경로 (선택, 기본: 레포 루트)]"
allowed-tools: Bash, Read, Write, Edit, Glob, Grep
---

# sync-agents

현재 레포의 주요 디렉터리마다 `AGENTS.md`를 **코드를 실제로 읽어서** 생성/갱신한다.
`$ARGUMENTS` 로 시작 경로가 주어지면 그 하위만, 없으면 레포 루트부터 처리한다.

## 1. 대상 디렉터리 선정

레포를 순회하되 아래는 **제외**한다:

```
.git  node_modules  .venv  venv  __pycache__  dist  build
.next  .cache  .ruff_cache  .omc  .idea  .vscode  coverage
```

소스가 있는 의미 있는 디렉터리만 대상으로 한다 — 빈 폴더나 자원(이미지/바이너리)만 있는 폴더는 건너뛴다.
너무 깊이 들어가지 말 것: 코드 모듈 경계(패키지/서브패키지) 수준까지만.

## 2. 각 디렉터리마다 AGENTS.md 작성

`${CLAUDE_PLUGIN_ROOT}/templates/AGENTS_TEMPLATE.md` 형식을 따른다. 다음을 **코드를 읽고** 채운다:

- **헤더 주석**: `<!-- Parent: {상위 AGENTS.md 상대경로} -->` (레포 루트면 이 줄 생략) / `<!-- Generated / Updated -->` 날짜
- **Purpose**: 이 디렉터리가 무엇을 담당하는지 2~3문장 (파일들을 훑어 추론)
- **Key Files**: 주요 파일과 한 줄 설명 (엔트리포인트·핵심 모듈 위주, 전부 나열하지 말 것)
- **Subdirectories**: 하위 디렉터리와 목적, 각 항목에 `(see {하위}/AGENTS.md)` 링크
- **For AI Agents**: 작업 규칙 / 테스트 방법 / 반복 패턴 — 코드에서 관찰되는 것만, 지어내지 말 것

## 3. MANUAL 블록 보존 (필수)

디렉터리에 **이미 `AGENTS.md`가 있으면**:

1. 먼저 Read 한다.
2. `<!-- MANUAL: -->` 마커부터 파일 끝까지의 내용을 **그대로 추출**한다.
3. 자동 섹션(Purpose~For AI Agents)만 새로 생성한다.
4. 새 문서 끝에 **보존한 MANUAL 블록을 그대로 다시 붙인다.**
5. 기존에 사람이 손으로 쓴 자동 섹션 내용이 여전히 정확하면 무리하게 갈아엎지 말고 보강만 한다.

`AGENTS.md`가 없으면 템플릿대로 새로 만든다.

## 4. 상위-하위 링크 정합성

각 디렉터리 AGENTS.md의 Subdirectories 표가 실제 하위 디렉터리와 일치하도록 맞춘다.
상위에서 없어진 하위를 가리키거나, 새 하위가 표에서 빠지지 않게 한다.

## 5. 순서

**바닥(리프 디렉터리)부터 위로** 처리하면 상위의 Subdirectories 표를 채울 때 하위 Purpose를 참고할 수 있어 좋다.

## 6. 결과 보고

생성 N개 / 갱신 M개 / MANUAL 보존 K개를 디렉터리 목록과 함께 표로 요약한다.
지어낸 내용 없이 코드 근거로만 채웠는지 스스로 점검하고, 불확실했던 디렉터리는 따로 표시한다.
