# Superpowers Plugin 가이드

> **버전:** 5.0.6
> **제작자:** Jesse Vincent (Prime Radiant)
> **라이선스:** MIT
> **저장소:** https://github.com/obra/superpowers

---

## 1. 개요

Superpowers는 Claude Code용 소프트웨어 개발 워크플로우 플러그인이다.
14개의 **스킬(Skill)** 과 1개의 **커스텀 에이전트(code-reviewer)** 로 구성되어 있으며, 대화 시작 시 자동으로 활성화된다.

핵심 철학:
- **TDD(Test-Driven Development)** — 테스트를 먼저 작성한다
- **체계적 접근** — 추측이 아닌 프로세스를 따른다
- **복잡성 축소** — 단순함이 최우선 목표다
- **증거 기반** — 성공을 주장하기 전에 검증한다

---

## 2. 설치 및 관리

```bash
# 설치
/plugin install superpowers@claude-plugins-official

# 업데이트
/plugin update superpowers

# 설치 확인
~/.claude/plugins/installed_plugins.json
```

**설치 경로:**
`~/.claude/plugins/cache/claude-plugins-official/superpowers/5.0.6/`

---

## 3. 파일 구조

```
superpowers/5.0.6/
├── README.md                    # 플러그인 소개 및 설치 가이드
├── package.json                 # 메타데이터 (name, version)
├── LICENSE
├── CHANGELOG.md
├── RELEASE-NOTES.md
├── CODE_OF_CONDUCT.md
│
├── skills/                      # ★ 14개 스킬 정의
│   ├── brainstorming/
│   │   ├── SKILL.md             # 스킬 본체
│   │   ├── visual-companion.md  # 비주얼 브레인스토밍 가이드
│   │   ├── spec-document-reviewer-prompt.md
│   │   └── scripts/             # 브라우저 기반 시각화 서버
│   │       ├── server.cjs
│   │       ├── helper.js
│   │       ├── frame-template.html
│   │       ├── start-server.sh
│   │       └── stop-server.sh
│   │
│   ├── writing-plans/
│   │   ├── SKILL.md
│   │   └── plan-document-reviewer-prompt.md
│   │
│   ├── executing-plans/
│   │   └── SKILL.md
│   │
│   ├── subagent-driven-development/
│   │   ├── SKILL.md
│   │   ├── implementer-prompt.md
│   │   ├── spec-reviewer-prompt.md
│   │   └── code-quality-reviewer-prompt.md
│   │
│   ├── test-driven-development/
│   │   ├── SKILL.md
│   │   └── testing-anti-patterns.md
│   │
│   ├── systematic-debugging/
│   │   ├── SKILL.md
│   │   ├── root-cause-tracing.md
│   │   ├── defense-in-depth.md
│   │   ├── condition-based-waiting.md
│   │   ├── condition-based-waiting-example.ts
│   │   ├── find-polluter.sh
│   │   ├── test-pressure-1.md
│   │   ├── test-pressure-2.md
│   │   ├── test-pressure-3.md
│   │   ├── test-academic.md
│   │   └── CREATION-LOG.md
│   │
│   ├── requesting-code-review/
│   │   ├── SKILL.md
│   │   └── code-reviewer.md
│   │
│   ├── receiving-code-review/
│   │   └── SKILL.md
│   │
│   ├── verification-before-completion/
│   │   └── SKILL.md
│   │
│   ├── using-git-worktrees/
│   │   └── SKILL.md
│   │
│   ├── finishing-a-development-branch/
│   │   └── SKILL.md
│   │
│   ├── dispatching-parallel-agents/
│   │   └── SKILL.md
│   │
│   ├── using-superpowers/
│   │   ├── SKILL.md
│   │   └── references/
│   │       ├── codex-tools.md
│   │       └── gemini-tools.md
│   │
│   └── writing-skills/
│       ├── SKILL.md
│       ├── anthropic-best-practices.md
│       ├── persuasion-principles.md
│       ├── graphviz-conventions.dot
│       ├── render-graphs.js
│       ├── testing-skills-with-subagents.md
│       └── examples/
│           └── CLAUDE_MD_TESTING.md
│
├── agents/                      # ★ 커스텀 에이전트 정의
│   └── code-reviewer.md         # 코드 리뷰어 에이전트
│
├── hooks/                       # ★ 세션 훅
│   ├── hooks.json               # 훅 설정 (세션 시작 시 실행)
│   ├── session-start            # 세션 시작 스크립트
│   └── run-hook.cmd             # 훅 실행기
│
├── docs/                        # 문서
│   ├── testing.md
│   ├── README.codex.md
│   ├── README.opencode.md
│   ├── windows/
│   │   └── polyglot-hooks.md
│   ├── plans/                   # 개발 계획서들
│   └── superpowers/
│       ├── plans/
│       └── specs/
│
├── tests/                       # 테스트
│   ├── claude-code/
│   ├── explicit-skill-requests/
│   ├── skill-triggering/
│   ├── subagent-driven-dev/
│   ├── brainstorm-server/
│   └── opencode/
│
├── .cursor-plugin/              # Cursor 플러그인 설정
│   └── plugin.json
├── .opencode/                   # OpenCode 지원
│   ├── INSTALL.md
│   └── plugins/superpowers.js
├── .codex/                      # Codex 지원
│   └── INSTALL.md
└── gemini-extension.json        # Gemini CLI 지원
```

---

## 4. 기본 워크플로우

Superpowers는 다음 순서로 스킬이 자동 트리거된다:

```
사용자 요청
    │
    ▼
[1] brainstorming         ← 아이디어 탐색, 설계 문서 작성
    │
    ▼
[2] using-git-worktrees   ← 격리된 작업 환경 생성
    │
    ▼
[3] writing-plans         ← 구현 계획서 작성 (2~5분 단위 태스크)
    │
    ▼
[4] subagent-driven-development  ← 서브에이전트가 태스크별 구현
    또는 executing-plans          ← 배치 실행 + 체크포인트
    │
    ├─ test-driven-development    ← RED → GREEN → REFACTOR
    ├─ requesting-code-review     ← 태스크 완료 시 코드 리뷰
    └─ systematic-debugging       ← 버그 발생 시 체계적 디버깅
    │
    ▼
[5] verification-before-completion  ← 완료 전 검증
    │
    ▼
[6] finishing-a-development-branch  ← 브랜치 통합 (merge/PR/유지/폐기)
```

---

## 5. 스킬 분류

### 5.1 프로세스 스킬 (HOW — 접근 방법 결정)

| 스킬 | 역할 |
|------|------|
| using-superpowers | 대화 시작 시 스킬 시스템 초기화 |
| brainstorming | 구현 전 설계 탐색 |
| systematic-debugging | 버그 발생 시 체계적 디버깅 |

### 5.2 계획 스킬 (WHAT — 무엇을 할지 정의)

| 스킬 | 역할 |
|------|------|
| writing-plans | 구현 계획서 작성 |
| executing-plans | 계획서 기반 배치 실행 |
| subagent-driven-development | 서브에이전트 기반 태스크 실행 |

### 5.3 구현 스킬 (DO — 실제 수행)

| 스킬 | 역할 |
|------|------|
| test-driven-development | RED-GREEN-REFACTOR 사이클 |
| using-git-worktrees | 격리된 작업 환경 생성 |
| dispatching-parallel-agents | 독립 태스크 병렬 처리 |

### 5.4 검증 스킬 (CHECK — 품질 보증)

| 스킬 | 역할 |
|------|------|
| requesting-code-review | 코드 리뷰 요청 |
| receiving-code-review | 코드 리뷰 피드백 처리 |
| verification-before-completion | 완료 전 증거 기반 검증 |
| finishing-a-development-branch | 브랜치 통합 의사결정 |

### 5.5 메타 스킬

| 스킬 | 역할 |
|------|------|
| writing-skills | 새로운 스킬 생성/편집 |

---

## 6. 14개 스킬 상세

### 6.1 brainstorming (브레인스토밍)

- **트리거:** 기능 생성, 컴포넌트 구축, 동작 변경 등 모든 창의적 작업 전
- **동작:**
  1. 프로젝트 컨텍스트 탐색
  2. 한 번에 하나씩 명확화 질문
  3. 2~3개 접근법 제안 (트레이드오프 포함)
  4. 설계를 섹션별로 제시, 사용자 승인 획득
  5. 설계 문서를 `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md`에 저장
  6. 자체 리뷰 (placeholder, 일관성, 범위, 모호성 체크)
  7. writing-plans 스킬로 이행
- **핵심 규칙:** 설계 승인 없이 구현 절대 불가 (Hard Gate)

### 6.2 writing-plans (계획 작성)

- **트리거:** 스펙/요구사항이 있고 다단계 작업이 필요할 때
- **동작:**
  1. 파일 구조 매핑 (생성/수정 대상)
  2. 2~5분 단위 태스크로 분할
  3. 계획서 작성: 목표, 아키텍처, 기술 스택, 태스크별 파일/코드/명령/예상 결과
  4. 자체 리뷰: 스펙 커버리지, placeholder 스캔, 타입 일관성
  5. `docs/superpowers/plans/YYYY-MM-DD-<feature>.md`에 저장
  6. 실행 방식 선택: Subagent-Driven(권장) 또는 Inline(executing-plans)
- **핵심 규칙:** placeholder 금지 ("TBD", "TODO", "Task N과 유사" 등 불허)

### 6.3 executing-plans (계획 실행)

- **트리거:** 작성된 구현 계획이 있고 별도 세션에서 실행할 때
- **동작:**
  1. 계획서 로드 → 비판적 리뷰 → 의문점 제기
  2. TodoWrite로 태스크 목록 생성
  3. 태스크별: in_progress → 단계 수행 → 검증 → completed
  4. 완료 후 finishing-a-development-branch 스킬 호출
- **핵심 규칙:** main/master 브랜치에서 직접 구현 금지 (명시적 동의 없이)

### 6.4 subagent-driven-development (서브에이전트 기반 개발)

- **트리거:** 구현 계획이 있고, 태스크가 독립적이며, 같은 세션에서 실행할 때
- **동작:**
  1. 계획서에서 전체 태스크 추출 → TodoWrite 생성
  2. 태스크별:
     - 구현자(implementer) 서브에이전트 디스패치
     - **1차 리뷰:** 스펙 준수 검토
     - **2차 리뷰:** 코드 품질 검토
     - 이슈 발견 시 구현자가 수정 → 재리뷰
  3. 전체 구현 완료 후 최종 코드 리뷰
  4. finishing-a-development-branch 스킬 호출
- **핵심 규칙:** 태스크당 2단계 리뷰 필수 (스펙 → 품질), 병렬 구현자 디스패치 금지

### 6.5 test-driven-development (테스트 주도 개발)

- **트리거:** 기능 구현, 버그 수정, 리팩토링, 동작 변경 시
- **동작 (RED-GREEN-REFACTOR):**
  1. **RED:** 실패하는 테스트 작성 → 실행하여 실패 확인
  2. **GREEN:** 테스트를 통과하는 최소한의 코드 작성
  3. **REFACTOR:** 테스트가 통과하는 상태에서 정리
- **핵심 규칙:** 실패하는 테스트 없이 프로덕션 코드 작성 금지. 테스트 전에 코드를 작성했다면 삭제하고 처음부터 시작

### 6.6 systematic-debugging (체계적 디버깅)

- **트리거:** 버그, 테스트 실패, 예상치 못한 동작, 성능 문제, 빌드 실패
- **동작 (4단계 필수):**
  1. **근본 원인 조사:** 에러 메시지 읽기, 재현, 최근 변경 확인, 데이터 흐름 추적
  2. **패턴 분석:** 동일 코드베이스에서 동작하는 예시 찾기, 차이점 식별
  3. **가설 및 테스트:** 단일 가설 수립 → 최소 변경으로 검증
  4. **구현:** 실패 테스트 작성 → 단일 수정 → 검증
- **핵심 규칙:** 근본 원인 조사 없이 수정 시도 금지. 3회 이상 수정 실패 시 아키텍처 문제로 전환

### 6.7 requesting-code-review (코드 리뷰 요청)

- **트리거:** 태스크 완료, 주요 기능 구현, merge 전, 복잡한 버그 수정 후
- **동작:**
  1. git SHA 확인 (BASE_SHA, HEAD_SHA)
  2. code-reviewer 서브에이전트 디스패치
  3. 피드백 처리: Critical(즉시 수정) → Important(진행 전 수정) → Minor(나중에)
- **핵심 규칙:** "단순하니까 리뷰 생략"은 금지. Critical 이슈는 반드시 즉시 수정

### 6.8 receiving-code-review (코드 리뷰 수신)

- **트리거:** 코드 리뷰 피드백을 받았을 때
- **동작:** READ → UNDERSTAND(자기 말로 재진술) → VERIFY(코드베이스 대조) → EVALUATE(기술적 타당성) → RESPOND(수용 또는 반박) → IMPLEMENT(하나씩, 각각 테스트)
- **핵심 규칙:** 동조적 반응 금지 ("Great point!", "You're absolutely right!" 등). 기술적으로 틀리면 근거와 함께 반박

### 6.9 verification-before-completion (완료 전 검증)

- **트리거:** 작업 완료/수정/통과를 주장하기 직전
- **동작:**
  1. IDENTIFY: 무엇으로 증명할 수 있는가?
  2. RUN: 전체 명령 실행 (새로, 완전하게)
  3. READ: 전체 출력, exit code, 실패 수 확인
  4. VERIFY: 출력이 주장을 뒷받침하는가?
  5. ONLY THEN: 주장
- **핵심 규칙:** 증거 없이 완료 주장 금지. "should", "probably", "seems to" 사용 금지

### 6.10 using-git-worktrees (Git Worktree 사용)

- **트리거:** 격리가 필요한 기능 작업 시작 시, 구현 계획 실행 전
- **동작:**
  1. 디렉토리 선택: 기존 `.worktrees/` → CLAUDE.md 설정 → 사용자에게 질문
  2. 안전성 검증: `.gitignore`에 포함되었는지 확인
  3. Worktree 생성: `git worktree add <path> -b <branch>`
  4. 프로젝트 셋업 자동 실행 (npm install, cargo build 등)
  5. 테스트 기준선 검증
- **핵심 규칙:** `.gitignore` 검증 없이 worktree 생성 금지

### 6.11 finishing-a-development-branch (개발 브랜치 마무리)

- **트리거:** 구현 완료, 테스트 통과, 통합 준비 완료 시
- **동작:**
  1. 전체 테스트 스위트 실행
  2. 기본 브랜치 확인
  3. 4가지 옵션 제시:
     - 로컬 merge
     - Push + Pull Request 생성
     - 브랜치 유지 (나중에 처리)
     - 작업 폐기
  4. 선택한 옵션 실행 + worktree 정리
- **핵심 규칙:** 테스트 실패 시 옵션 제시 불가. 폐기 시 "discard" 타이핑 확인 필요

### 6.12 dispatching-parallel-agents (병렬 에이전트 디스패치)

- **트리거:** 2개 이상의 독립적 태스크, 공유 상태 없음, 순차 의존성 없음
- **동작:**
  1. 독립적 문제 도메인 식별
  2. 에이전트별 집중된 태스크 생성
  3. 병렬 디스패치
  4. 결과 리뷰 → 충돌 확인 → 전체 테스트
- **핵심 규칙:** 에이전트 간 변경 사항이 간섭할 수 있으면 사용 금지

### 6.13 using-superpowers (스킬 시스템 사용)

- **트리거:** 모든 대화 시작 시 (자동)
- **동작:**
  - 관련 스킬이 있을 확률이 1%라도 있으면 반드시 Skill tool 호출
  - 스킬 우선순위: 프로세스 스킬 → 구현 스킬
  - 사용자 지시(CLAUDE.md) > 스킬 > 기본 시스템 프롬프트
- **핵심 규칙:** 명확화 질문 전에도 스킬 체크 필수

### 6.14 writing-skills (스킬 작성)

- **트리거:** 새 스킬 생성, 기존 스킬 편집, 배포 전 검증
- **동작 (TDD 방식):**
  1. **RED:** 스킬 없이 시나리오 테스트 → 실패 패턴 기록
  2. **GREEN:** 최소 스킬 작성 → 시나리오 재테스트 → 통과 확인
  3. **REFACTOR:** 새로운 우회 패턴 식별 → 명시적 대응 추가 → 재테스트
- **핵심 규칙:** 실패 테스트 없이 스킬 작성 금지 (TDD와 동일 원칙)

---

## 7. 커스텀 에이전트: code-reviewer

`agents/code-reviewer.md`에 정의된 서브에이전트로, requesting-code-review 스킬이 디스패치한다.

**역할:** 시니어 코드 리뷰어

**검토 항목:**
1. 계획 정합성 — 구현이 원래 계획과 일치하는지
2. 코드 품질 — 패턴, 에러 처리, 타입 안전성, 네이밍
3. 아키텍처 — SOLID 원칙, 관심사 분리, 결합도
4. 문서화 — 적절한 주석, 프로젝트 표준 준수
5. 보안/성능 — 취약점, 성능 이슈

**이슈 분류:**
- **Critical:** 반드시 수정 (진행 차단)
- **Important:** 진행 전 수정 권장
- **Suggestion:** 개선 제안 (선택)

---

## 8. 훅 (Hooks)

`hooks/hooks.json`에 정의된 세션 시작 훅이 대화 시작/초기화/컴팩트 시 자동 실행된다.
`using-superpowers` 스킬을 시스템에 주입하여 모든 대화에서 스킬 시스템이 활성화되도록 보장한다.
