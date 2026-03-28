---
name: md-to-slides
description: Markdown 문서를 테마 선택 가능한 HTML 슬라이드로 변환 (슬라이드별 레이아웃 자동 감지)
triggers:
  - "md to slides"
  - "markdown to slides"
  - "슬라이드 생성"
  - "슬라이드로 변환"
argument-hint: "<markdown-file> [--theme default|dark|minimal|corporate]"
level: 2
---

# md-to-slides 스킬 실행 지침

이 스킬은 Markdown 파일을 읽어 self-contained HTML 슬라이드로 변환한다.
`templates.md`의 CSS/JS 명세를 그대로 임베드하여 외부 의존성 없는 단일 HTML 파일을 생성한다.
(단, Pretendard 폰트 CDN은 예외적으로 허용한다.)

각 슬라이드는 콘텐츠를 분석하여 최적의 레이아웃(`data-layout`)을 자동 결정한다.

---

## 1. 인자 파싱

인자 문자열에서 다음을 추출한다:

| 인자 | 기본값 | 설명 |
|------|--------|------|
| `<markdown-file>` | (필수) | 변환할 .md 파일 경로 |
| `--theme` | `default` | `default` \| `dark` \| `minimal` \| `corporate` |

**파싱 규칙:**
- 첫 번째 비-플래그 인자가 파일 경로
- `--theme <값>` 또는 `--theme=<값>` 형식 모두 처리
- 유효하지 않은 테마 값이면 기본값 사용

**출력 경로:** 입력 파일과 동일한 디렉토리, 동일한 파일명에 확장자만 `.html`로 변경
- 예: `docs/presentation.md` → `docs/presentation.html`

---

## 2. Markdown 파싱 규칙

파일을 읽고 다음 규칙으로 슬라이드 데이터 구조를 만든다.

### 헤딩 매핑

| Markdown | 슬라이드 타입 | 설명 |
|----------|---------------|------|
| `# 제목` | `cover` | 표지 슬라이드 제목 |
| H1 바로 아래 첫 문단 | cover subtitle | 표지 부제목 |
| `## 섹션명` | `divider` | 섹션 간지 슬라이드 |
| `### 슬라이드명` | `content` | 본문 슬라이드 |

### 파싱 순서

1. 전체 MD를 줄 단위로 읽는다
2. H1을 만나면 → cover 제목 저장, 그 다음 첫 비어있지 않은 문단을 subtitle로 저장
3. H2를 만나면 → 새 섹션 시작, divider 슬라이드 생성
4. H3를 만나면 → 현재 섹션 안에 content 슬라이드 생성, H3 이후 다음 H2/H3가 나오기 전까지의 모든 내용이 해당 슬라이드의 본문
5. **H3 없는 H2 섹션**: H2 다음에 바로 H2/문서 끝이 오더라도 빈 content 슬라이드 1개 생성 (H2 아래의 문단이 있으면 그 내용을 본문으로 사용)
6. H1/H2/H3 이외의 최상위 레벨 콘텐츠(파일 앞부분 도입부 등)는 무시하거나 표지 subtitle에 포함

### 목차(TOC) 자동 생성

- TOC는 H2 섹션 제목 목록으로 구성
- H3 제목도 있으면 H2 아래 들여쓰기로 포함 가능하나, 기본은 H2만
- 각 TOC 항목에 `data-goto="slide-N"` 속성 추가 (해당 divider 슬라이드 id)

---

## 3. 슬라이드 구성 순서

다음 순서로 슬라이드 배열을 구성한다:

```
슬라이드 1:  cover      (H1 제목 + subtitle)
슬라이드 2:  toc        (H2 목록 자동 생성)
슬라이드 3:  divider    (첫 번째 H2)
슬라이드 4:  content    (첫 번째 H3 또는 빈 content)
슬라이드 5:  content    (두 번째 H3 ...)
...
슬라이드N-1: divider    (마지막 H2)
슬라이드N:   content    (마지막 H3)
슬라이드N+1: closing    ("감사합니다" + 파일명 + 날짜)
```

**슬라이드 id:** `slide-1`, `slide-2`, ... (1부터 순차)

**divider 번호:** 섹션 순서에 따라 `01`, `02`, `03` ... (2자리 zero-padding)

---

## 3.5 핵심 메시지 추출 알고리즘 (핵심)

각 content 슬라이드 생성 시 아래 알고리즘을 순서대로 실행한다.

### Step 1 — Title 유형 판별 (TOPIC vs ACTION)

H3 제목이 **TOPIC**(명사구)인지 **ACTION**(메시지 문장)인지 판별한다.

| 유형 | 특징 | 예시 |
|------|------|------|
| **ACTION** | 동사 포함, 결론/주장 표현 | "키워드 입력 시 스킬이 자동 실행된다" |
| **TOPIC** | 명사구만, 동사 없음 | "매직 키워드", "에이전트 카탈로그" |

**ACTION이면** → 제목 그대로 사용
**TOPIC이면** → Step 2로 진행하여 Action Title 생성

### Step 2 — Action Title 생성 (TOPIC일 때만)

아래 우선순위로 핵심 메시지를 추출하여 슬라이드 제목을 재작성한다:

1. 본문 첫 번째 **굵은(bold) 문장** → "주어 + 동사 + 결론" 형태로 압축
2. 첫 번째 문단의 핵심 술어 추출
3. 리스트 항목들의 공통 패턴에서 결론 도출
4. 마지막 수단: "섹션이 독자에게 말하는 단 한 가지"를 직접 작성

**재작성 예시:**
- "매직 키워드" → "특정 단어 입력만으로 해당 스킬이 즉시 자동 실행된다"
- "에이전트 카탈로그" → "29+ 에이전트가 5개 전문 레인으로 역할 분리된다"
- "상태 특성" → "원자적 쓰기·TTL·자동 마이그레이션으로 상태 일관성을 보장한다"

### Step 3 — 콘텐츠 분량 기준 (인지과학 기반)

| 요소 | 기준 | 근거 |
|------|------|------|
| 슬라이드당 총 단어 | **40단어 이하** (제목 제외) | Miller's Law, Mayer Coherence Principle |
| 불릿 수 | **3-5개** (최대 6개) | 작업기억 용량 7±2 |
| 불릿당 단어 | **7단어 이하** | 완전한 문장 지양 |
| 코드블록 | **12줄 이하** | 슬라이드 가독성 |
| 표 | **5행 × 4열 이하** | 슬라이드 공간 제약 |

초과 시: 중요도 낮은 항목 제거 → 슬라이드 분할 순으로 처리

### Step 4 — "So What" 어노테이션

표·다이어그램·코드블록이 포함된 슬라이드에는 **반드시** h3 바로 아래 한 줄 메시지 추가:

```html
<p class="slide-message"><strong>So What:</strong> [시각 요소가 말하는 핵심 결론]</p>
```

예시:
- 표 → "3개 환경변수로 OMC 전체 동작을 제어할 수 있다"
- 다이어그램 → "5단계 이벤트 파이프라인을 모든 사용자 입력이 통과한다"
- 코드블록 → "두 줄의 설정으로 팀 에이전트 모드가 활성화된다"

### Step 5 — 다이어그램+표 병존 시 분리 판단

| 상황 | 처리 |
|------|------|
| 다이어그램=구조 이해 + 표=수치 요약 | **같은 슬라이드** |
| 다이어그램=개념 소개 + 표=상세 목록 | **2장으로 분리** |
| 표 2개 이상이고 각각 독립 메시지 | **각각 별도 슬라이드** |
| 표 행 수 > 5 | **핵심 5행만 선택**, 나머지 생략 |

---

## 4. 슬라이드별 레이아웃 자동 감지

각 슬라이드의 콘텐츠를 분석하여 `data-layout` 값을 자동 결정한다.

### 4.1 구조형 (슬라이드 타입으로 고정)

| 슬라이드 타입 | data-layout | 설명 |
|---------------|-------------|------|
| `cover` | `title-cover` | 고정 |
| `divider` | `section-divider` | 고정 |
| `toc` | `agenda` | 고정 |
| `closing` | `end` | 고정 |

### 4.2 content 슬라이드 자동 감지 규칙

`data-type="content"` 슬라이드는 본문 콘텐츠를 분석하여 아래 규칙을 **우선순위 순서대로** 검사한다. 첫 번째로 매칭되는 레이아웃을 적용한다.

| 우선순위 | data-layout | 감지 조건 |
|----------|-------------|-----------|
| 1 | `diagram-fullbleed` | mermaid 코드블록만 있고 텍스트 거의 없음 (리스트/문단 0개) |
| 2 | `diagram-split` | mermaid 코드블록 + 리스트 또는 문단 혼재 |
| 3 | `diagram` | mermaid 코드블록 + h3 제목만 (설명 최소) |
| 4 | `iframe-embed` | `<iframe>` 태그 또는 단독 URL 한 줄 |
| 5 | `fullbleed-image` | `![bg]()` 패턴 또는 이미지만 있고 텍스트 없음 |
| 6 | `image-left` | 이미지가 첫 번째 요소 |
| 7 | `image-right` | 텍스트 먼저, 이미지가 마지막 요소 |
| 8 | `code-focus` | 코드블록이 콘텐츠의 50% 이상 차지 (mermaid 제외) |
| 9 | `data-chart` | mermaid 코드블록 중 `pie`, `xychart`, `gantt` 키워드 |
| 10 | `dashboard` | "KPI", "달성률", "지표" 키워드 + 복수 테이블 |
| 11 | `table-data` | 마크다운 테이블 (4열 이상 또는 5행 이상) |
| 12 | `comparison` | "vs", "versus", "비교" 키워드 또는 ✅/❌ 패턴 또는 2~3열 테이블 |
| 13 | `timeline` | 날짜/분기 패턴(`2024`, `Q1`, `1월`) + 순서 있는 리스트 |
| 14 | `steps-process` | `→`, `Step`, `Phase`, `단계` 키워드 + 순서 리스트 |
| 15 | `icon-grid` | 이모지로 시작하는 항목이 3개 이상 반복 |
| 16 | `three-cols` | H3가 정확히 3개 (각 H3 아래 동등한 분량) |
| 17 | `two-cols-header` | H3 + `::left::` / `::right::` 구분자 |
| 18 | `two-cols` | `::left::` / `::right::` 구분자 (H3 없음) |
| 19 | `asymmetric-split` | `ratio:` 키워드 또는 `60:40`, `70:30` 패턴 |
| 20 | `fact` | H3 텍스트가 숫자/퍼센트로 시작 또는 단독 큰 숫자 |
| 21 | `quote` | blockquote(`>`) 가 주 콘텐츠 |
| 22 | `statement` | 굵은(**) 단일 문장, 리스트 없음, 50자 이내 |
| 23 | `intro-profile` | 이미지 + 이름 형태 텍스트 + 직함/역할 텍스트 |
| 24 | `center` | 텍스트 전체 5단어 이내 (h3 포함), 리스트/코드 없음 |
| 25 | `list` | 리스트 항목 3개 이상, 문단 없음 |
| 26 | `default` | 위 어느 것도 해당 안 됨 (fallback) |

**참고:** `title-cover`, `section-divider`, `agenda`, `end`는 슬라이드 타입으로 이미 결정되므로 content 슬라이드 감지 목록에 없다.

---

## 5. HTML 생성 지침

### 5.1 기본 구조

`templates.md` Section 5의 완전한 단일 파일 HTML 템플릿을 기반으로 생성한다.

```html
<!DOCTYPE html>
<html lang="ko" data-theme="{{THEME}}">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>{{TITLE}}</title>
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/variable/pretendardvariable.min.css">
  <style>
    /* templates.md Section 5의 전체 CSS를 그대로 임베드 */
  </style>
</head>
<body>
  <div class="slideshow" id="slideshow">
    {{SLIDES}}
  </div>

  <!-- 좌/우 네비게이션 버튼 -->
  <button class="nav-btn nav-prev" id="navPrev" aria-label="이전 슬라이드">&#8249;</button>
  <button class="nav-btn nav-next" id="navNext" aria-label="다음 슬라이드">&#8250;</button>

  <!-- 하단 진행 바 + 슬라이드 번호 -->
  <div class="nav-bar" id="navBar">
    <div class="nav-progress">
      <div class="nav-progress-fill" id="navProgressFill"></div>
    </div>
    <div class="nav-info">
      <span id="currentSlide">1</span>
      <span class="nav-sep">/</span>
      <span id="totalSlides">1</span>
    </div>
  </div>

  <script>
    /* templates.md Section 4의 전체 JS를 그대로 임베드 */
  </script>
</body>
</html>
```

**중요:** Pretendard 폰트 CDN 링크를 제외하고, 외부 CDN, 외부 스크립트 참조 금지. 모든 CSS/JS는 `<style>`/`<script>` 태그 안에 직접 삽입.

### 5.2 치환 변수

| 변수 | 치환 내용 |
|------|-----------|
| `{{THEME}}` | 인자로 받은 테마 값 (`default`/`dark`/`minimal`/`corporate`) |
| `{{TITLE}}` | MD 파일의 H1 텍스트 |
| `{{SLIDES}}` | 생성된 슬라이드 HTML 전체 |

### 5.3 슬라이드 타입별 HTML 생성

#### cover 슬라이드

```html
<div class="slide active" id="slide-1" data-type="cover" data-layout="title-cover">
  <div class="cover-inner">
    <span class="cover-eyebrow">PRESENTATION</span>
    <h1>{{H1_TITLE}}</h1>
    <p class="subtitle">{{SUBTITLE}}</p>
    <div class="meta"><span>{{FILENAME}}</span><span>{{DATE}}</span></div>
  </div>
</div>
```

- `cover-eyebrow`: "PRESENTATION" 고정 텍스트
- subtitle: H1 바로 아래 첫 문단. 없으면 생략 (빈 `<p>` 태그 출력 금지)
- meta: 파일명(확장자 없이)과 오늘 날짜 (YYYY-MM-DD 형식)

#### toc 슬라이드

```html
<div class="slide" id="slide-2" data-type="toc" data-layout="agenda">
  <h2>목차</h2>
  <ol>
    <li data-goto="slide-3">첫 번째 섹션 제목</li>
    <li data-goto="slide-7">두 번째 섹션 제목</li>
    <li data-goto="slide-12">세 번째 섹션 제목</li>
  </ol>
</div>
```

- `data-goto` 값은 해당 H2의 divider 슬라이드 실제 id

#### divider 슬라이드

```html
<div class="slide" id="slide-N" data-type="divider" data-layout="section-divider">
  <span class="divider-number">01</span>
  <h2>섹션 제목</h2>
  <p class="divider-sub">H2 아래 첫 문단 (있을 경우만)</p>
</div>
```

- `divider-sub`: H2 바로 아래 문단 텍스트. 없으면 생략
- `divider-number`: 01, 02, 03 ... 순서

#### content 슬라이드

```html
<div class="slide" id="slide-N" data-type="content" data-layout="{{AUTO_DETECTED_LAYOUT}}">
  <h3>슬라이드 제목</h3>
  <!-- 마크다운 본문을 HTML로 변환한 내용 -->
</div>
```

- `{{AUTO_DETECTED_LAYOUT}}`: Section 4의 자동 감지 규칙에 따라 결정된 레이아웃 값

#### closing 슬라이드

```html
<div class="slide" id="slide-N" data-type="closing" data-layout="end">
  <div class="closing-card">
    <div class="closing-icon">🎉</div>
    <h2>감사합니다</h2>
    <p class="closing-message">질문이 있으시면 언제든지 연락주세요.</p>
    <div class="closing-contact"><span>{{FILENAME}}</span><span>{{DATE}}</span></div>
  </div>
</div>
```

---

## 6. 마크다운 → HTML 변환 규칙

슬라이드 본문의 마크다운 문법을 다음 규칙으로 HTML로 변환한다.

### 인라인 요소

| 마크다운 | HTML |
|----------|------|
| `**텍스트**` 또는 `__텍스트__` | `<strong>텍스트</strong>` |
| `*텍스트*` 또는 `_텍스트_` | `<em>텍스트</em>` |
| `` `코드` `` | `<code>코드</code>` |
| `[텍스트](URL)` | `<a href="URL">텍스트</a>` |

### 블록 요소

| 마크다운 | HTML |
|----------|------|
| 빈 줄로 구분된 문단 | `<p>...</p>` |
| `- 항목` 또는 `* 항목` | `<ul><li>...</li></ul>` |
| `1. 항목` | `<ol><li>...</li></ol>` |
| ` ```코드블록``` ` | `<pre><code>...</code></pre>` |
| `> 인용` | `<blockquote><p>...</p></blockquote>` |

### 테이블

```markdown
| 헤더1 | 헤더2 |
|-------|-------|
| 셀1   | 셀2   |
```

→

```html
<table>
  <thead><tr><th>헤더1</th><th>헤더2</th></tr></thead>
  <tbody><tr><td>셀1</td><td>셀2</td></tr></tbody>
</table>
```

### Mermaid 다이어그램 → 인라인 HTML 변환

mermaid 코드블록(```` ```mermaid ````)을 `<pre><code>` 대신 아래 규칙으로 인라인 HTML 다이어그램으로 변환한다.

**flowchart LR (좌→우):**
```html
<div class="diagram-container">
  <div class="diagram-flow-lr">
    <div class="diagram-node node-gold">노드1</div>
    <span class="diagram-arrow">→</span>
    <div class="diagram-node node-green">노드2</div>
    <span class="diagram-arrow">→</span>
    <div class="diagram-node node-blue">노드3</div>
  </div>
</div>
```

**flowchart TB (위→아래):**
```html
<div class="diagram-container">
  <div class="diagram-flow-tb">
    <div class="diagram-node node-gold">노드1</div>
    <span class="diagram-arrow">↓</span>
    <div class="diagram-node node-green">노드2</div>
  </div>
</div>
```

**분기(branch)가 있는 경우:**
```html
<div class="diagram-container">
  <div class="diagram-flow-tb">
    <div class="diagram-node">부모</div>
    <span class="diagram-arrow">↓</span>
    <div class="diagram-branch">
      <div class="diagram-flow-tb">
        <div class="diagram-node">자식A</div>
      </div>
      <div class="diagram-flow-tb">
        <div class="diagram-node">자식B</div>
      </div>
    </div>
  </div>
</div>
```

**subgraph가 있는 경우:**
```html
<div class="diagram-subgraph">
  <div class="diagram-subgraph-label">서브그래프 이름</div>
  <div class="diagram-flow-lr">
    <!-- 내부 노드들 -->
  </div>
</div>
```

**classDef 색상 매핑:**
- `fill:#FFD700` (금색) → `node-gold`
- `fill:#90EE90` (초록) → `node-green`
- `fill:#87CEEB` (파랑) → `node-blue`
- `fill:#E6E6FA` (연보라) → `node-purple`
- accent 색상 → `node-highlight`
- 기본 → (클래스 없음)

### 이스케이프

HTML 특수문자 이스케이프:
- `&` → `&amp;`
- `<` → `&lt;`
- `>` → `&gt;` (단, 마크다운 블록 요소 파싱 후 적용)

---

## 7. 출력

1. 생성된 HTML을 입력 파일과 같은 디렉토리에 `.html` 확장자로 저장
2. 저장 완료 후 다음 정보를 사용자에게 출력:
   - 출력 파일 경로
   - 생성된 슬라이드 수
   - 적용된 테마
   - 감지된 레이아웃 목록 (각 슬라이드별 data-layout 값)
   - 브라우저에서 열기 안내 (`open <파일경로>` 또는 브라우저로 직접 열기)

---

## 8. 실행 예시

**입력:**
```
/docs/intro.md --theme dark
```

**처리 흐름:**
1. `/docs/intro.md` 읽기
2. 테마=`dark` 확인
3. MD 파싱 → 슬라이드 구조 생성
4. 각 content 슬라이드 본문 분석 → `data-layout` 자동 결정
5. templates.md의 CSS/JS 임베드
6. `/docs/intro.html` 저장

**출력 메시지 예시:**
```
슬라이드 생성 완료
출력 파일: /docs/intro.html
슬라이드 수: 12장
테마: dark
레이아웃 감지 결과:
  slide-1:  title-cover
  slide-2:  agenda
  slide-3:  section-divider
  slide-4:  list
  slide-5:  two-cols
  slide-6:  code-focus
  slide-7:  section-divider
  slide-8:  diagram
  slide-9:  table-data
  slide-10: quote
  slide-11: fact
  slide-12: end
브라우저에서 열기: open /docs/intro.html
```

---

## 9. 주의사항

- **외부 의존성**: Pretendard 폰트 CDN만 예외적으로 허용. 그 외 CDN 링크, 외부 스크립트 참조 금지
- **CSS/JS 완전 임베드**: templates.md의 CSS와 JS를 `<style>`/`<script>` 태그 안에 전부 포함
- **H1 없는 파일**: 파일명을 표지 제목으로 사용
- **섹션 없는 파일**: H2/H3 없이 본문만 있을 경우, 전체 내용을 단일 content 슬라이드로 생성 (cover + content + closing 3장)
- **HTML 인젝션 방지**: 마크다운 내 HTML 태그는 이스케이프 처리
- **긴 콘텐츠**: content 슬라이드에 `overflow-y: auto`가 적용되어 있으므로 내용이 많아도 스크롤 가능
- **레이아웃 자동 감지 실패 시**: `default` 레이아웃을 적용 (안전한 fallback)
- **슬라이드 전환**: 트랜지션 없이 display 토글만으로 즉각 전환
