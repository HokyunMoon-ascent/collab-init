# collab-init

Claude Code 스킬 — 프로젝트에 **human-AI 협업 표준 구조**를 만든다.

```
<프로젝트 루트>/
├── CLAUDE.md                              # 협업 규칙 섹션 자동 주입
└── human 지시 사항 및 결과 파일 모음/
    ├── README.md                          # 운영 규칙
    ├── input_by_human/                    # 사람이 남기는 작업 요청 (NNN_제목.md)
    │   └── 000_요청_템플릿.md
    └── output_from_AI/                    # 요청과 1:1 매칭되는 산출물 (NNN_제목/)
```

## 왜
Claude Code로 작업하다 보면 파일이 프로젝트 곳곳에 흩어져 사람이 이해하기 어려운 구조가 된다. 이 스킬은 인간이 보는 인터페이스를 폴더 하나로 고정한다:

- 요청은 `input_by_human/NNN_제목.md`, 산출물은 `output_from_AI/NNN_제목/` — 엄격한 1:1 매칭
- 모든 산출물 폴더의 진입점은 `00_요약.md`
- 사람이 읽는 문서는 `/humanize` 스킬(한글 AI 티 제거 윤문)을 거친 최종본만 저장
- AI는 협업 폴더 밖을 자유롭게 쓰되, 구조·설계 문서는 output_from_AI에 축적

CLAUDE.md에 마커(`<!-- collab-init:start/end -->`) 섹션을 주입해 이후 모든 세션이 규칙을 자동으로 따른다. 멱등적이라 재실행해도 기존 파일을 건드리지 않는다.

## 설치

```bash
git clone https://github.com/HokyunMoon-ascent/collab-init.git ~/.claude/skills/collab-init
```

추가로 `~/.claude/CLAUDE.md`(전역 메모리)에 아래 섹션을 넣으면 새 프로젝트에서 구조가 없을 때 Claude가 먼저 제안한다:

```markdown
## human-AI 협업 구조 자동 제안
프로젝트성 폴더(작업 파일이 있는 디렉토리)에서 작업을 시작할 때 `human 지시 사항 및 결과 파일 모음/` 폴더가 없으면, 첫 응답에서 collab-init 스킬로 협업 표준 구조를 만들지 한 번 제안한다. 동의하면 즉시 실행하고, 거절하면 그 세션에서는 다시 묻지 않는다. 홈 디렉토리 등 비프로젝트 경로에서는 제안하지 않는다. 구조가 이미 있으면 프로젝트 CLAUDE.md의 collab-init 규칙을 따른다.
```

## 사용

```
/collab-init                # 현재 프로젝트에 구조 생성/보수
/collab-init <경로>          # 지정 경로에 생성
```

## 파일

- `SKILL.md` — 스킬 본체 (루트 탐지 → 멱등 생성 → 시퀀스 스캔 → CLAUDE.md 주입 → 보고)
- `templates/collab-README.md` — 협업 폴더에 심는 운영 규칙
- `templates/request-template.md` — 요청 양식 (`000_요청_템플릿.md`)
- `templates/claude-md-section.md` — CLAUDE.md 주입 블록 (마커 포함)
