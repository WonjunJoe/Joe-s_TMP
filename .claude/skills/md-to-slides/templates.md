# md-to-slides: HTML 슬라이드 템플릿 명세

이 파일은 Markdown → HTML 슬라이드 변환 시 참조하는 완전한 CSS/JS 레퍼런스다.

---

## 1. CSS 변수 명세 (테마별)

### 공통 CSS 변수 구조

```css
:root {
  /* === 색상 === */
  --bg:           #ffffff;   /* 슬라이드 배경 */
  --bg-alt:       #f5f5f5;   /* 보조 배경 (divider, 패널 등) */
  --fg:           #1a1a1a;   /* 기본 텍스트 */
  --fg-muted:     #6b7280;   /* 보조 텍스트 */
  --accent:       #2563eb;   /* 포인트 색상 (링크, 강조, 헤더 등) */
  --accent-light: #dbeafe;   /* 포인트 연하게 */
  --border:       #e5e7eb;   /* 구분선 */
  --code-bg:      #f3f4f6;   /* 코드블록 배경 */

  /* === 타이포그래피 === */
  --font-sans:    'Inter', 'Pretendard', system-ui, sans-serif;
  --font-mono:    'JetBrains Mono', 'Fira Code', monospace;
  --size-title:   clamp(2rem, 5vw, 3.5rem);
  --size-h2:      clamp(1.5rem, 3vw, 2.25rem);
  --size-h3:      clamp(1.2rem, 2.5vw, 1.75rem);
  --size-body:    clamp(0.9rem, 1.8vw, 1.1rem);
  --size-small:   0.85rem;

  /* === 간격 === */
  --pad:          clamp(2rem, 5vw, 4rem);
  --pad-sm:       clamp(1rem, 2.5vw, 2rem);
  --radius:       0.5rem;

  /* === 전환 === */
  --transition:   0.35s ease;
}
```

### 테마: default (흰 배경, 파란 포인트)

```css
[data-theme="default"] {
  --bg:           #ffffff;
  --bg-alt:       #f8fafc;
  --fg:           #0f172a;
  --fg-muted:     #64748b;
  --accent:       #2563eb;
  --accent-light: #dbeafe;
  --border:       #e2e8f0;
  --code-bg:      #f1f5f9;
}
```

### 테마: dark (어두운 배경, 보라 포인트)

```css
[data-theme="dark"] {
  --bg:           #1e1e2e;
  --bg-alt:       #181825;
  --fg:           #cdd6f4;
  --fg-muted:     #a6adc8;
  --accent:       #a78bfa;
  --accent-light: #3b2f6e;
  --border:       #313244;
  --code-bg:      #181825;
}
```

### 테마: minimal (완전 흰 배경, 극도로 간결)

```css
[data-theme="minimal"] {
  --bg:           #ffffff;
  --bg-alt:       #ffffff;
  --fg:           #000000;
  --fg-muted:     #555555;
  --accent:       #000000;
  --accent-light: #f0f0f0;
  --border:       #dddddd;
  --code-bg:      #f8f8f8;
  --font-sans:    'Helvetica Neue', 'Arial', sans-serif;
  --radius:       0;
}
```

### 테마: corporate (남색 헤더, 비즈니스 스타일)

```css
[data-theme="corporate"] {
  --bg:           #f4f6f9;
  --bg-alt:       #e8ecf2;
  --fg:           #1a2332;
  --fg-muted:     #5a6a7e;
  --accent:       #1e3a5f;
  --accent-light: #d0dce8;
  --border:       #c8d3df;
  --code-bg:      #edf0f4;
  --header-bg:    #1e3a5f;        /* corporate 전용: 헤더 배경 */
  --header-fg:    #ffffff;        /* corporate 전용: 헤더 텍스트 */
}
```

---

## 2. 전환효과(Transition)별 CSS

### 공통 HTML 래퍼

```html
<!DOCTYPE html>
<html lang="ko" data-theme="default" data-transition="horizontal">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>{{TITLE}}</title>
  <style>
    /* === BASE RESET === */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    /* === 테마 CSS 변수 (위 명세 삽입) === */
    /* ... */

    /* === 전환효과별 CSS (아래 명세 삽입) === */
    /* ... */

    /* === 슬라이드 타입별 CSS (아래 명세 삽입) === */
    /* ... */

    /* === 슬라이드 레이아웃별 CSS (아래 명세 삽입) === */
    /* ... */
  </style>
</head>
<body>
  <div class="slideshow" id="slideshow">
    <!-- 슬라이드들 -->
    {{SLIDES}}
  </div>

  <!-- 네비게이션 UI -->
  <div class="slide-counter" id="slideCounter">
    <span id="currentSlide">1</span> / <span id="totalSlides">1</span>
  </div>

  <script>
    /* === 네비게이션 JS (아래 명세 삽입) === */
  </script>
</body>
</html>
```

---

### 전환효과: horizontal (좌우 전환)

```css
/* data-transition="horizontal" */
[data-transition="horizontal"] .slideshow {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: var(--bg);
}

[data-transition="horizontal"] .slide {
  position: absolute;
  top: 0; left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
  padding: var(--pad);
  background: var(--bg);
  color: var(--fg);
  font-family: var(--font-sans);
  transform: translateX(100%);
  transition: transform var(--transition);
  will-change: transform;
}

[data-transition="horizontal"] .slide.active {
  transform: translateX(0);
}

[data-transition="horizontal"] .slide.prev {
  transform: translateX(-100%);
}

/* 슬라이드 카운터 위치 */
[data-transition="horizontal"] .slide-counter {
  position: fixed;
  bottom: 1.5rem;
  right: 2rem;
  font-size: var(--size-small);
  color: var(--fg-muted);
  font-family: var(--font-sans);
  z-index: 100;
}
```

---

### 전환효과: vertical (상하 전환)

```css
/* data-transition="vertical" */
[data-transition="vertical"] .slideshow {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: var(--bg);
}

[data-transition="vertical"] .slide {
  position: absolute;
  top: 0; left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
  padding: var(--pad);
  background: var(--bg);
  color: var(--fg);
  font-family: var(--font-sans);
  transform: translateY(100%);
  transition: transform var(--transition);
  will-change: transform;
}

[data-transition="vertical"] .slide.active {
  transform: translateY(0);
}

[data-transition="vertical"] .slide.prev {
  transform: translateY(-100%);
}

[data-transition="vertical"] .slide-counter {
  position: fixed;
  bottom: 1.5rem;
  right: 2rem;
  font-size: var(--size-small);
  color: var(--fg-muted);
  font-family: var(--font-sans);
  z-index: 100;
}
```

---

### 전환효과: fade (페이드 전환)

```css
/* data-transition="fade" */
[data-transition="fade"] .slideshow {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: var(--bg);
}

[data-transition="fade"] .slide {
  position: absolute;
  top: 0; left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
  padding: var(--pad);
  background: var(--bg);
  color: var(--fg);
  font-family: var(--font-sans);
  opacity: 0;
  pointer-events: none;
  transition: opacity var(--transition);
}

[data-transition="fade"] .slide.active {
  opacity: 1;
  pointer-events: auto;
}

[data-transition="fade"] .slide-counter {
  position: fixed;
  bottom: 1.5rem;
  right: 2rem;
  font-size: var(--size-small);
  color: var(--fg-muted);
  font-family: var(--font-sans);
  z-index: 100;
}
```

---

## 3. 슬라이드 타입별 CSS 클래스 명세

### data-type="cover" — 표지 슬라이드

```css
.slide[data-type="cover"] {
  justify-content: center;
  align-items: center;
  text-align: center;
  background: var(--bg);
}

.slide[data-type="cover"] .cover-eyebrow {
  font-size: var(--size-small);
  font-weight: 600;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--accent);
  margin-bottom: 1rem;
}

.slide[data-type="cover"] h1 {
  font-size: var(--size-title);
  font-weight: 800;
  line-height: 1.1;
  color: var(--fg);
  margin-bottom: 1rem;
  max-width: 16ch;
}

.slide[data-type="cover"] .subtitle {
  font-size: var(--size-h3);
  font-weight: 400;
  color: var(--fg-muted);
  margin-bottom: 2rem;
}

.slide[data-type="cover"] .meta {
  font-size: var(--size-small);
  color: var(--fg-muted);
  display: flex;
  gap: 1.5rem;
  justify-content: center;
}

/* corporate 테마 cover 오버라이드 */
[data-theme="corporate"] .slide[data-type="cover"] {
  background: var(--header-bg);
  color: var(--header-fg);
}

[data-theme="corporate"] .slide[data-type="cover"] h1,
[data-theme="corporate"] .slide[data-type="cover"] .subtitle,
[data-theme="corporate"] .slide[data-type="cover"] .meta {
  color: rgba(255,255,255,0.9);
}

[data-theme="corporate"] .slide[data-type="cover"] .cover-eyebrow {
  color: #7eb8e8;
}
```

---

### data-type="toc" — 목차 슬라이드

```css
.slide[data-type="toc"] {
  align-items: flex-start;
  padding: var(--pad);
}

.slide[data-type="toc"] h2 {
  font-size: var(--size-h2);
  font-weight: 700;
  color: var(--fg);
  margin-bottom: 2rem;
  padding-bottom: 0.75rem;
  border-bottom: 2px solid var(--accent);
}

.slide[data-type="toc"] ol {
  list-style: none;
  counter-reset: toc-counter;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  width: 100%;
  max-width: 700px;
}

.slide[data-type="toc"] ol li {
  counter-increment: toc-counter;
  display: flex;
  align-items: center;
  gap: 1rem;
  font-size: var(--size-body);
  color: var(--fg);
  padding: 0.5rem 0;
  border-bottom: 1px solid var(--border);
  cursor: pointer;
  transition: color 0.2s;
}

.slide[data-type="toc"] ol li::before {
  content: counter(toc-counter, decimal-leading-zero);
  font-size: var(--size-small);
  font-weight: 700;
  color: var(--accent);
  min-width: 2em;
}

.slide[data-type="toc"] ol li:hover {
  color: var(--accent);
}
```

---

### data-type="divider" — 섹션 구분 슬라이드 (간지)

```css
.slide[data-type="divider"] {
  justify-content: center;
  align-items: center;
  text-align: center;
  background: var(--accent);
}

.slide[data-type="divider"] .divider-number {
  font-size: var(--size-small);
  font-weight: 700;
  letter-spacing: 0.2em;
  text-transform: uppercase;
  color: rgba(255,255,255,0.6);
  margin-bottom: 1rem;
}

.slide[data-type="divider"] h2 {
  font-size: clamp(2.5rem, 6vw, 5rem);
  font-weight: 800;
  color: #ffffff;
  line-height: 1.1;
  max-width: 14ch;
}

.slide[data-type="divider"] .divider-sub {
  font-size: var(--size-body);
  color: rgba(255,255,255,0.75);
  margin-top: 1.5rem;
  max-width: 40ch;
}

/* dark 테마 divider */
[data-theme="dark"] .slide[data-type="divider"] {
  background: var(--accent);
}

/* minimal 테마 divider — 반전 배경 */
[data-theme="minimal"] .slide[data-type="divider"] {
  background: #000000;
}

[data-theme="minimal"] .slide[data-type="divider"] h2 {
  color: #ffffff;
}

/* corporate 테마 divider */
[data-theme="corporate"] .slide[data-type="divider"] {
  background: var(--accent);
}
```

---

### data-type="content" — 일반 콘텐츠 슬라이드

```css
.slide[data-type="content"] {
  justify-content: flex-start;
  align-items: flex-start;
  padding: var(--pad);
  overflow-y: auto;
}

/* corporate 테마: 상단 헤더 바 */
[data-theme="corporate"] .slide[data-type="content"]::before {
  content: '';
  display: block;
  height: 4px;
  background: var(--accent);
  position: absolute;
  top: 0; left: 0; right: 0;
}

.slide[data-type="content"] h3 {
  font-size: var(--size-h3);
  font-weight: 700;
  color: var(--fg);
  margin-bottom: 1.5rem;
  padding-bottom: 0.75rem;
  border-bottom: 2px solid var(--accent);
  width: 100%;
}

/* corporate: h3 스타일 오버라이드 */
[data-theme="corporate"] .slide[data-type="content"] h3 {
  background: var(--header-bg);
  color: var(--header-fg);
  padding: 0.75rem var(--pad-sm);
  border-bottom: none;
  margin: calc(-1 * var(--pad)) calc(-1 * var(--pad)) 1.5rem;
  width: calc(100% + 2 * var(--pad));
  font-size: var(--size-body);
  font-weight: 600;
  letter-spacing: 0.02em;
}

.slide[data-type="content"] p {
  font-size: var(--size-body);
  line-height: 1.7;
  color: var(--fg);
  margin-bottom: 1rem;
  max-width: 70ch;
}

.slide[data-type="content"] ul,
.slide[data-type="content"] ol {
  padding-left: 1.5rem;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  max-width: 70ch;
}

.slide[data-type="content"] li {
  font-size: var(--size-body);
  line-height: 1.6;
  color: var(--fg);
}

.slide[data-type="content"] li::marker {
  color: var(--accent);
}

.slide[data-type="content"] code {
  font-family: var(--font-mono);
  font-size: 0.9em;
  background: var(--code-bg);
  color: var(--accent);
  padding: 0.15em 0.4em;
  border-radius: calc(var(--radius) * 0.5);
  border: 1px solid var(--border);
}

.slide[data-type="content"] pre {
  background: var(--code-bg);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  padding: 1.25rem;
  overflow-x: auto;
  font-family: var(--font-mono);
  font-size: 0.85em;
  line-height: 1.6;
  width: 100%;
  margin: 1rem 0;
}

.slide[data-type="content"] pre code {
  background: none;
  border: none;
  padding: 0;
  color: var(--fg);
}

.slide[data-type="content"] blockquote {
  border-left: 3px solid var(--accent);
  padding: 0.75rem 1.25rem;
  background: var(--accent-light);
  border-radius: 0 var(--radius) var(--radius) 0;
  font-style: italic;
  color: var(--fg-muted);
  margin: 1rem 0;
  max-width: 65ch;
}

/* 이미지 */
.slide[data-type="content"] img {
  max-width: 100%;
  height: auto;
  border-radius: var(--radius);
  border: 1px solid var(--border);
}

/* 표 */
.slide[data-type="content"] table {
  width: 100%;
  border-collapse: collapse;
  font-size: var(--size-small);
}

.slide[data-type="content"] th {
  background: var(--accent);
  color: #fff;
  padding: 0.6rem 0.9rem;
  text-align: left;
  font-weight: 600;
}

.slide[data-type="content"] td {
  padding: 0.5rem 0.9rem;
  border-bottom: 1px solid var(--border);
  color: var(--fg);
}

.slide[data-type="content"] tr:nth-child(even) td {
  background: var(--bg-alt);
}
```

---

### data-type="closing" — 마무리 슬라이드

```css
.slide[data-type="closing"] {
  justify-content: center;
  align-items: center;
  text-align: center;
  background: var(--bg);
}

.slide[data-type="closing"] .closing-icon {
  font-size: 3rem;
  margin-bottom: 1.5rem;
  opacity: 0.6;
}

.slide[data-type="closing"] h2 {
  font-size: var(--size-h2);
  font-weight: 800;
  color: var(--fg);
  margin-bottom: 1rem;
}

.slide[data-type="closing"] .closing-message {
  font-size: var(--size-body);
  color: var(--fg-muted);
  max-width: 40ch;
  line-height: 1.7;
  margin-bottom: 2rem;
}

.slide[data-type="closing"] .closing-contact {
  display: flex;
  gap: 1.5rem;
  justify-content: center;
  flex-wrap: wrap;
  font-size: var(--size-small);
  color: var(--accent);
}

/* corporate 테마 closing */
[data-theme="corporate"] .slide[data-type="closing"] {
  background: var(--header-bg);
}

[data-theme="corporate"] .slide[data-type="closing"] h2,
[data-theme="corporate"] .slide[data-type="closing"] .closing-message {
  color: rgba(255,255,255,0.9);
}

[data-theme="corporate"] .slide[data-type="closing"] .closing-contact {
  color: #7eb8e8;
}
```

---

## 3.5. 슬라이드 per-slide 레이아웃 CSS

각 슬라이드의 `data-layout` 속성에 따라 적용되는 레이아웃 CSS다.
콘텐츠 자동 감지 결과가 이 속성에 반영된다.

### 구조형 (4개)

```css
/* ── title-cover ── */
.slide[data-layout="title-cover"] {
  justify-content: center;
  align-items: center;
  text-align: center;
}

/* ── section-divider ── */
.slide[data-layout="section-divider"] {
  justify-content: center;
  align-items: center;
  text-align: center;
}

/* ── agenda ── */
.slide[data-layout="agenda"] {
  justify-content: center;
  align-items: flex-start;
}

/* ── end ── */
.slide[data-layout="end"] {
  justify-content: center;
  align-items: center;
  text-align: center;
}
```

### 콘텐츠형 (9개)

```css
/* ── default ── */
.slide[data-layout="default"] {
  justify-content: flex-start;
  align-items: flex-start;
}

.slide[data-layout="default"] h3 {
  width: 100%;
  margin-bottom: 1.5rem;
}

/* ── statement ── */
.slide[data-layout="statement"] {
  justify-content: center;
  align-items: center;
  text-align: center;
}

.slide[data-layout="statement"] p {
  font-size: clamp(1.5rem, 3.5vw, 2.5rem);
  font-weight: 700;
  line-height: 1.3;
  max-width: 20ch;
  color: var(--fg);
}

/* ── quote ── */
.slide[data-layout="quote"] {
  justify-content: center;
  align-items: flex-start;
}

.slide[data-layout="quote"] blockquote {
  font-size: clamp(1.2rem, 2.5vw, 1.8rem);
  font-style: italic;
  line-height: 1.6;
  border-left: 4px solid var(--accent);
  padding: 1.5rem 2rem;
  background: var(--accent-light);
  border-radius: 0 var(--radius) var(--radius) 0;
  max-width: 50ch;
  color: var(--fg);
}

.slide[data-layout="quote"] blockquote p {
  font-size: inherit;
  margin-bottom: 0;
}

.slide[data-layout="quote"] .quote-author {
  font-size: var(--size-body);
  color: var(--fg-muted);
  margin-top: 1rem;
  font-style: normal;
}

/* ── fact ── */
.slide[data-layout="fact"] {
  justify-content: center;
  align-items: center;
  text-align: center;
}

.slide[data-layout="fact"] .fact-number {
  font-size: clamp(3rem, 10vw, 8rem);
  font-weight: 900;
  line-height: 1;
  color: var(--accent);
  margin-bottom: 1rem;
}

.slide[data-layout="fact"] .fact-label {
  font-size: var(--size-h3);
  color: var(--fg-muted);
  max-width: 30ch;
}

/* ── intro-profile ── */
.slide[data-layout="intro-profile"] {
  justify-content: center;
  align-items: center;
  flex-direction: row;
  gap: 3rem;
}

.slide[data-layout="intro-profile"] img {
  width: 180px;
  height: 180px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid var(--accent);
  flex-shrink: 0;
}

.slide[data-layout="intro-profile"] .profile-info {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.slide[data-layout="intro-profile"] .profile-name {
  font-size: var(--size-h2);
  font-weight: 800;
  color: var(--fg);
}

.slide[data-layout="intro-profile"] .profile-title {
  font-size: var(--size-body);
  color: var(--fg-muted);
}

/* ── two-cols ── */
.slide[data-layout="two-cols"] .cols-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  width: 100%;
  height: 100%;
  align-items: start;
}

.slide[data-layout="two-cols"] .cols-container .col {
  padding: 0 1rem;
}

.slide[data-layout="two-cols"] .cols-container .col:first-child {
  border-right: 1px solid var(--border);
}

/* ── two-cols-header ── */
.slide[data-layout="two-cols-header"] h3 {
  width: 100%;
  margin-bottom: 1.5rem;
}

.slide[data-layout="two-cols-header"] .cols-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  width: 100%;
  flex: 1;
  align-items: start;
}

.slide[data-layout="two-cols-header"] .cols-container .col {
  padding: 0 1rem;
}

.slide[data-layout="two-cols-header"] .cols-container .col:first-child {
  border-right: 1px solid var(--border);
}

/* ── three-cols ── */
.slide[data-layout="three-cols"] .cols-container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  gap: 1.5rem;
  width: 100%;
  height: 100%;
  align-items: start;
}

.slide[data-layout="three-cols"] .cols-container .col {
  padding: 0 0.75rem;
}

.slide[data-layout="three-cols"] .cols-container .col:not(:last-child) {
  border-right: 1px solid var(--border);
}

.slide[data-layout="three-cols"] .cols-container .col h3 {
  font-size: var(--size-body);
  font-weight: 700;
  margin-bottom: 1rem;
  padding-bottom: 0.5rem;
  border-bottom: 2px solid var(--accent);
}

/* ── icon-grid ── */
.slide[data-layout="icon-grid"] .grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1.5rem;
  width: 100%;
  margin-top: 1rem;
}

.slide[data-layout="icon-grid"] .grid-item {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  padding: 1.5rem 1rem;
  background: var(--bg-alt);
  border-radius: var(--radius);
  border: 1px solid var(--border);
}

.slide[data-layout="icon-grid"] .grid-item .icon {
  font-size: 2rem;
  margin-bottom: 0.75rem;
}

.slide[data-layout="icon-grid"] .grid-item .grid-label {
  font-size: var(--size-body);
  font-weight: 600;
  color: var(--fg);
}

.slide[data-layout="icon-grid"] .grid-item .grid-desc {
  font-size: var(--size-small);
  color: var(--fg-muted);
  margin-top: 0.25rem;
}

/* ── list ── */
.slide[data-layout="list"] {
  justify-content: flex-start;
  align-items: flex-start;
}

.slide[data-layout="list"] ul,
.slide[data-layout="list"] ol {
  padding-left: 1.5rem;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  max-width: 70ch;
}

.slide[data-layout="list"] li {
  font-size: var(--size-body);
  line-height: 1.6;
}

/* ── center ── */
.slide[data-layout="center"] {
  justify-content: center;
  align-items: center;
  text-align: center;
}

.slide[data-layout="center"] h3 {
  font-size: var(--size-h2);
  border-bottom: none;
  margin-bottom: 0.5rem;
}
```

### 데이터형 (4개)

```css
/* ── data-chart ── */
.slide[data-layout="data-chart"] {
  justify-content: flex-start;
  align-items: center;
  padding: var(--pad-sm) var(--pad);
}

.slide[data-layout="data-chart"] h3 {
  font-size: var(--size-body);
  margin-bottom: 0.75rem;
  width: 100%;
}

.slide[data-layout="data-chart"] .chart-area {
  flex: 1;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 0;
}

.slide[data-layout="data-chart"] .chart-area svg {
  max-width: 100%;
  max-height: 100%;
}

/* ── comparison ── */
.slide[data-layout="comparison"] .compare-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0;
  width: 100%;
  flex: 1;
}

.slide[data-layout="comparison"] .compare-side {
  padding: 2rem;
}

.slide[data-layout="comparison"] .compare-side:first-child {
  background: var(--accent-light);
  border-radius: var(--radius) 0 0 var(--radius);
}

.slide[data-layout="comparison"] .compare-side:last-child {
  background: var(--bg-alt);
  border-radius: 0 var(--radius) var(--radius) 0;
}

.slide[data-layout="comparison"] .compare-side h4 {
  font-size: var(--size-h3);
  font-weight: 700;
  margin-bottom: 1rem;
  color: var(--fg);
}

/* ── table-data ── */
.slide[data-layout="table-data"] {
  justify-content: flex-start;
  padding: var(--pad-sm) var(--pad);
}

.slide[data-layout="table-data"] h3 {
  font-size: var(--size-body);
  margin-bottom: 0.75rem;
}

.slide[data-layout="table-data"] table {
  width: 100%;
  border-collapse: collapse;
  font-size: calc(var(--size-small) * 0.95);
}

.slide[data-layout="table-data"] th {
  background: var(--accent);
  color: #fff;
  padding: 0.4rem 0.6rem;
  text-align: left;
  font-weight: 600;
  font-size: calc(var(--size-small) * 0.9);
}

.slide[data-layout="table-data"] td {
  padding: 0.35rem 0.6rem;
  border-bottom: 1px solid var(--border);
}

.slide[data-layout="table-data"] tr:nth-child(even) td {
  background: var(--bg-alt);
}

/* ── dashboard ── */
.slide[data-layout="dashboard"] {
  justify-content: flex-start;
  padding: var(--pad-sm);
}

.slide[data-layout="dashboard"] h3 {
  font-size: var(--size-body);
  margin-bottom: 0.75rem;
  width: 100%;
}

.slide[data-layout="dashboard"] .kpi-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 1rem;
  width: 100%;
  margin-bottom: 1rem;
}

.slide[data-layout="dashboard"] .kpi-card {
  background: var(--bg-alt);
  border: 1px solid var(--border);
  border-radius: var(--radius);
  padding: 1rem;
  text-align: center;
}

.slide[data-layout="dashboard"] .kpi-card .kpi-value {
  font-size: var(--size-h2);
  font-weight: 800;
  color: var(--accent);
}

.slide[data-layout="dashboard"] .kpi-card .kpi-label {
  font-size: var(--size-small);
  color: var(--fg-muted);
  margin-top: 0.25rem;
}

.slide[data-layout="dashboard"] table {
  width: 100%;
  font-size: calc(var(--size-small) * 0.9);
}
```

### 시각형 (5개)

```css
/* ── fullbleed-image ── */
.slide[data-layout="fullbleed-image"] {
  padding: 0;
  position: relative;
}

.slide[data-layout="fullbleed-image"] img {
  position: absolute;
  top: 0; left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 0;
  border: none;
}

.slide[data-layout="fullbleed-image"] .overlay-text {
  position: relative;
  z-index: 1;
  background: rgba(0,0,0,0.5);
  color: #fff;
  padding: 1.5rem 2rem;
  border-radius: var(--radius);
  margin: auto;
  text-align: center;
}

/* ── image-left ── */
.slide[data-layout="image-left"] {
  flex-direction: row;
  padding: 0;
  gap: 0;
}

.slide[data-layout="image-left"] .image-panel {
  width: 40%;
  height: 100%;
  flex-shrink: 0;
  overflow: hidden;
}

.slide[data-layout="image-left"] .image-panel img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 0;
  border: none;
}

.slide[data-layout="image-left"] .text-panel {
  flex: 1;
  padding: var(--pad);
  display: flex;
  flex-direction: column;
  justify-content: center;
  overflow-y: auto;
}

/* ── image-right ── */
.slide[data-layout="image-right"] {
  flex-direction: row;
  padding: 0;
  gap: 0;
}

.slide[data-layout="image-right"] .text-panel {
  flex: 1;
  padding: var(--pad);
  display: flex;
  flex-direction: column;
  justify-content: center;
  overflow-y: auto;
}

.slide[data-layout="image-right"] .image-panel {
  width: 40%;
  height: 100%;
  flex-shrink: 0;
  overflow: hidden;
}

.slide[data-layout="image-right"] .image-panel img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 0;
  border: none;
}

/* ── asymmetric-split ── */
.slide[data-layout="asymmetric-split"] .cols-container {
  display: grid;
  gap: 2rem;
  width: 100%;
  height: 100%;
  align-items: start;
}

.slide[data-layout="asymmetric-split"] .cols-container.ratio-60-40 {
  grid-template-columns: 3fr 2fr;
}

.slide[data-layout="asymmetric-split"] .cols-container.ratio-70-30 {
  grid-template-columns: 7fr 3fr;
}

.slide[data-layout="asymmetric-split"] .cols-container.ratio-30-70 {
  grid-template-columns: 3fr 7fr;
}

.slide[data-layout="asymmetric-split"] .cols-container.ratio-40-60 {
  grid-template-columns: 2fr 3fr;
}

.slide[data-layout="asymmetric-split"] .cols-container .col {
  padding: 0 1rem;
}

/* ── iframe-embed ── */
.slide[data-layout="iframe-embed"] {
  padding: var(--pad-sm);
}

.slide[data-layout="iframe-embed"] h3 {
  font-size: var(--size-body);
  margin-bottom: 0.5rem;
}

.slide[data-layout="iframe-embed"] iframe {
  flex: 1;
  width: 100%;
  border: 1px solid var(--border);
  border-radius: var(--radius);
}
```

### 특수형 (3개)

```css
/* ── code-focus ── */
.slide[data-layout="code-focus"] {
  justify-content: flex-start;
  padding: var(--pad-sm) var(--pad);
}

.slide[data-layout="code-focus"] h3 {
  font-size: var(--size-body);
  margin-bottom: 0.5rem;
}

.slide[data-layout="code-focus"] pre {
  flex: 1;
  width: 100%;
  overflow: auto;
  font-size: clamp(0.7rem, 1.5vw, 0.95rem);
  line-height: 1.5;
  margin: 0;
}

/* ── timeline ── */
.slide[data-layout="timeline"] {
  justify-content: flex-start;
  align-items: flex-start;
}

.slide[data-layout="timeline"] .timeline-container {
  position: relative;
  padding-left: 2.5rem;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
  width: 100%;
}

.slide[data-layout="timeline"] .timeline-container::before {
  content: '';
  position: absolute;
  left: 0.75rem;
  top: 0;
  bottom: 0;
  width: 2px;
  background: var(--accent);
}

.slide[data-layout="timeline"] .timeline-item {
  position: relative;
}

.slide[data-layout="timeline"] .timeline-item::before {
  content: '';
  position: absolute;
  left: -1.95rem;
  top: 0.4rem;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background: var(--accent);
  border: 2px solid var(--bg);
}

.slide[data-layout="timeline"] .timeline-date {
  font-size: var(--size-small);
  font-weight: 700;
  color: var(--accent);
  margin-bottom: 0.25rem;
}

.slide[data-layout="timeline"] .timeline-content {
  font-size: var(--size-body);
  color: var(--fg);
}

/* ── steps-process ── */
.slide[data-layout="steps-process"] .steps-container {
  display: flex;
  align-items: flex-start;
  gap: 0;
  width: 100%;
  margin-top: 1rem;
}

.slide[data-layout="steps-process"] .step {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  padding: 0 0.75rem;
  position: relative;
}

.slide[data-layout="steps-process"] .step:not(:last-child)::after {
  content: '';
  position: absolute;
  right: -0.5rem;
  top: 1.25rem;
  width: 1rem;
  height: 2px;
  background: var(--accent);
}

.slide[data-layout="steps-process"] .step-number {
  width: 2.5rem;
  height: 2.5rem;
  border-radius: 50%;
  background: var(--accent);
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: var(--size-body);
  margin-bottom: 0.75rem;
  flex-shrink: 0;
}

.slide[data-layout="steps-process"] .step-label {
  font-size: var(--size-body);
  font-weight: 600;
  color: var(--fg);
  margin-bottom: 0.25rem;
}

.slide[data-layout="steps-process"] .step-desc {
  font-size: var(--size-small);
  color: var(--fg-muted);
}
```

### 다이어그램형 (3개)

```css
/* ── diagram ── */
.slide[data-layout="diagram"] {
  justify-content: flex-start;
  align-items: center;
  padding: var(--pad-sm) var(--pad);
}

.slide[data-layout="diagram"] h3 {
  font-size: var(--size-body);
  margin-bottom: 0.5rem;
  width: 100%;
}

.slide[data-layout="diagram"] .diagram-area {
  flex: 1;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 0;
  overflow: auto;
}

.slide[data-layout="diagram"] .diagram-area svg {
  max-width: 100%;
  max-height: 100%;
}

/* ── diagram-fullbleed ── */
.slide[data-layout="diagram-fullbleed"] {
  padding: 0;
  position: relative;
  justify-content: center;
  align-items: center;
}

.slide[data-layout="diagram-fullbleed"] h3 {
  position: absolute;
  top: 1rem;
  left: 1.5rem;
  font-size: var(--size-small);
  color: var(--fg-muted);
  border-bottom: none;
  margin: 0;
  z-index: 1;
  background: rgba(var(--bg), 0.8);
  padding: 0.25rem 0.75rem;
  border-radius: var(--radius);
}

.slide[data-layout="diagram-fullbleed"] .diagram-area {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem;
}

.slide[data-layout="diagram-fullbleed"] .diagram-area svg {
  max-width: 100%;
  max-height: 100%;
}

/* ── diagram-split ── */
.slide[data-layout="diagram-split"] {
  flex-direction: row;
  padding: 0;
  gap: 0;
}

.slide[data-layout="diagram-split"] .diagram-panel {
  width: 60%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem;
  overflow: auto;
}

.slide[data-layout="diagram-split"] .diagram-panel svg {
  max-width: 100%;
  max-height: 100%;
}

.slide[data-layout="diagram-split"] .text-panel {
  width: 40%;
  height: 100%;
  padding: var(--pad);
  display: flex;
  flex-direction: column;
  justify-content: center;
  overflow-y: auto;
  border-left: 1px solid var(--border);
  background: var(--bg-alt);
}
```

---

## 4. 네비게이션 JavaScript (완전한 코드)

```js
(function () {
  'use strict';

  const slideshow  = document.getElementById('slideshow');
  const counterEl  = document.getElementById('slideCounter');
  const currentEl  = document.getElementById('currentSlide');
  const totalEl    = document.getElementById('totalSlides');
  const transition = document.documentElement.dataset.transition || 'horizontal';

  // 모든 슬라이드 수집
  const slides = Array.from(slideshow.querySelectorAll('.slide'));
  let current  = 0;

  // URL hash에서 초기 슬라이드 결정
  function hashToIndex(hash) {
    const m = (hash || '').match(/slide-(\d+)/);
    if (m) {
      const idx = parseInt(m[1], 10) - 1;
      if (idx >= 0 && idx < slides.length) return idx;
    }
    return 0;
  }

  function updateHash(idx) {
    const id = slides[idx].id || ('slide-' + (idx + 1));
    history.replaceState(null, '', '#' + id);
  }

  function updateCounter(idx) {
    if (currentEl) currentEl.textContent = idx + 1;
    if (totalEl)   totalEl.textContent   = slides.length;
  }

  function goTo(idx, skipHash) {
    if (idx < 0 || idx >= slides.length) return;

    const prev = current;
    slides[prev].classList.remove('active');

    // fade 전환은 prev 클래스 불필요 (opacity 기반)
    if (transition !== 'fade') {
      slides[prev].classList.add('prev');
    }

    current = idx;
    slides[current].classList.remove('prev');
    slides[current].classList.add('active');

    // prev 클래스 정리 (전환 완료 후)
    if (transition !== 'fade') {
      setTimeout(() => {
        slides.forEach((s, i) => {
          if (i !== current) s.classList.remove('prev');
        });
      }, 400);
    }

    updateCounter(current);
    if (!skipHash) updateHash(current);
  }

  function next() { goTo(current + 1); }
  function prev() { goTo(current - 1); }

  // ── 키보드 네비게이션 ──────────────────────────────
  document.addEventListener('keydown', function (e) {
    switch (e.key) {
      case 'ArrowRight':
      case 'ArrowDown':
      case ' ':
        e.preventDefault();
        next();
        break;
      case 'ArrowLeft':
      case 'ArrowUp':
        e.preventDefault();
        prev();
        break;
      case 'Home':
        e.preventDefault();
        goTo(0);
        break;
      case 'End':
        e.preventDefault();
        goTo(slides.length - 1);
        break;
    }
  });

  // ── 클릭 네비게이션 ──────────────────────────────
  document.addEventListener('click', function (e) {
    // 링크 클릭 무시
    if (e.target.tagName === 'A') return;
    // TOC 항목 클릭 처리
    const gotoTarget = e.target.closest('[data-goto]');
    if (gotoTarget) {
      const targetId = gotoTarget.dataset.goto;
      const targetIdx = slides.findIndex(s => s.id === targetId);
      if (targetIdx !== -1) { goTo(targetIdx); return; }
    }
    // 일반 클릭 → 다음 슬라이드
    next();
  });

  // ── URL hash 변경 감지 ────────────────────────────
  window.addEventListener('hashchange', function () {
    const idx = hashToIndex(location.hash);
    if (idx !== current) goTo(idx, true);
  });

  // ── 터치/스와이프 지원 ────────────────────────────
  let touchStartX = 0;
  let touchStartY = 0;

  document.addEventListener('touchstart', function (e) {
    touchStartX = e.changedTouches[0].screenX;
    touchStartY = e.changedTouches[0].screenY;
  }, { passive: true });

  document.addEventListener('touchend', function (e) {
    const dx = e.changedTouches[0].screenX - touchStartX;
    const dy = e.changedTouches[0].screenY - touchStartY;
    const threshold = 50;
    if (transition === 'vertical') {
      if (dy < -threshold) next();
      else if (dy > threshold) prev();
    } else {
      if (dx < -threshold) next();
      else if (dx > threshold) prev();
    }
  }, { passive: true });

  // ── 초기화 ───────────────────────────────────────
  function init() {
    if (totalEl) totalEl.textContent = slides.length;

    // 모든 슬라이드 초기화
    slides.forEach((s, i) => {
      s.classList.remove('active', 'prev');
      if (!s.id) s.id = 'slide-' + (i + 1);
    });

    // 초기 슬라이드 결정
    const startIdx = hashToIndex(location.hash);
    // 첫 슬라이드 이전 슬라이드들을 prev 상태로 초기화 (transform 전환용)
    if (transition !== 'fade') {
      for (let i = 0; i < startIdx; i++) {
        slides[i].classList.add('prev');
      }
    }
    slides[startIdx].classList.add('active');
    current = startIdx;
    updateCounter(current);
  }

  init();
})();
```

---

## 5. 완전한 단일 파일 HTML 템플릿 (레퍼런스 구현)

아래는 `horizontal` 전환 + `default` 테마를 사용하는 최소 완전 구현이다.
실제 생성 시 `data-theme`과 `data-transition`을 치환하고, 각 슬라이드에 `data-layout`을 부여한다.

```html
<!DOCTYPE html>
<html lang="ko" data-theme="{{THEME}}" data-transition="{{TRANSITION}}">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>{{TITLE}}</title>
  <style>
    /* ── Reset ── */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    html, body { width: 100%; height: 100%; overflow: hidden; }

    /* ── CSS 변수 기본(default) ── */
    :root {
      --bg: #ffffff; --bg-alt: #f8fafc; --fg: #0f172a; --fg-muted: #64748b;
      --accent: #2563eb; --accent-light: #dbeafe; --border: #e2e8f0;
      --code-bg: #f1f5f9;
      --font-sans: 'Inter', system-ui, sans-serif;
      --font-mono: 'JetBrains Mono', monospace;
      --size-title: clamp(2rem,5vw,3.5rem); --size-h2: clamp(1.5rem,3vw,2.25rem);
      --size-h3: clamp(1.2rem,2.5vw,1.75rem); --size-body: clamp(0.9rem,1.8vw,1.1rem);
      --size-small: 0.85rem; --pad: clamp(2rem,5vw,4rem); --pad-sm: clamp(1rem,2.5vw,2rem);
      --radius: 0.5rem; --transition: 0.35s ease;
    }
    [data-theme="dark"] {
      --bg:#1e1e2e; --bg-alt:#181825; --fg:#cdd6f4; --fg-muted:#a6adc8;
      --accent:#a78bfa; --accent-light:#3b2f6e; --border:#313244;
      --code-bg:#181825;
    }
    [data-theme="minimal"] {
      --bg:#fff; --bg-alt:#fff; --fg:#000; --fg-muted:#555;
      --accent:#000; --accent-light:#f0f0f0; --border:#ddd;
      --code-bg:#f8f8f8; --font-sans:'Helvetica Neue',Arial,sans-serif; --radius:0;
    }
    [data-theme="corporate"] {
      --bg:#f4f6f9; --bg-alt:#e8ecf2; --fg:#1a2332; --fg-muted:#5a6a7e;
      --accent:#1e3a5f; --accent-light:#d0dce8; --border:#c8d3df;
      --code-bg:#edf0f4; --header-bg:#1e3a5f; --header-fg:#ffffff;
    }

    /* ── Slideshow 기본 ── */
    .slideshow { position:relative; width:100vw; height:100vh; overflow:hidden; background:var(--bg); }

    /* ── 공통 슬라이드 ── */
    .slide {
      position:absolute; top:0; left:0; width:100%; height:100%;
      display:flex; flex-direction:column; justify-content:center; align-items:flex-start;
      padding:var(--pad); background:var(--bg); color:var(--fg); font-family:var(--font-sans);
      will-change:transform,opacity;
    }

    /* ── 전환효과: horizontal ── */
    [data-transition="horizontal"] .slide { transform:translateX(100%); transition:transform var(--transition); }
    [data-transition="horizontal"] .slide.active { transform:translateX(0); }
    [data-transition="horizontal"] .slide.prev { transform:translateX(-100%); }

    /* ── 전환효과: vertical ── */
    [data-transition="vertical"] .slide { transform:translateY(100%); transition:transform var(--transition); }
    [data-transition="vertical"] .slide.active { transform:translateY(0); }
    [data-transition="vertical"] .slide.prev { transform:translateY(-100%); }

    /* ── 전환효과: fade ── */
    [data-transition="fade"] .slide { opacity:0; pointer-events:none; transition:opacity var(--transition); }
    [data-transition="fade"] .slide.active { opacity:1; pointer-events:auto; }

    /* ── 슬라이드 카운터 ── */
    .slide-counter {
      position:fixed; bottom:1.5rem; right:2rem; font-size:var(--size-small);
      color:var(--fg-muted); font-family:var(--font-sans); z-index:100; user-select:none;
    }

    /* ══════════════════════════════════════════════════
       슬라이드 타입별 CSS
       ══════════════════════════════════════════════════ */

    /* ── cover ── */
    .slide[data-type="cover"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-type="cover"] .cover-eyebrow { font-size:var(--size-small); font-weight:600; letter-spacing:.15em; text-transform:uppercase; color:var(--accent); margin-bottom:1rem; }
    .slide[data-type="cover"] h1 { font-size:var(--size-title); font-weight:800; line-height:1.1; color:var(--fg); margin-bottom:1rem; }
    .slide[data-type="cover"] .subtitle { font-size:var(--size-h3); font-weight:400; color:var(--fg-muted); margin-bottom:2rem; }
    .slide[data-type="cover"] .meta { font-size:var(--size-small); color:var(--fg-muted); display:flex; gap:1.5rem; }
    [data-theme="corporate"] .slide[data-type="cover"] { background:var(--accent); }
    [data-theme="corporate"] .slide[data-type="cover"] h1,
    [data-theme="corporate"] .slide[data-type="cover"] .subtitle,
    [data-theme="corporate"] .slide[data-type="cover"] .meta,
    [data-theme="corporate"] .slide[data-type="cover"] .cover-eyebrow { color:rgba(255,255,255,.9); }

    /* ── toc ── */
    .slide[data-type="toc"] h2 { font-size:var(--size-h2); font-weight:700; color:var(--fg); margin-bottom:2rem; padding-bottom:.75rem; border-bottom:2px solid var(--accent); width:100%; }
    .slide[data-type="toc"] ol { list-style:none; counter-reset:toc; display:flex; flex-direction:column; gap:.75rem; max-width:700px; }
    .slide[data-type="toc"] ol li { counter-increment:toc; display:flex; align-items:center; gap:1rem; font-size:var(--size-body); color:var(--fg); padding:.5rem 0; border-bottom:1px solid var(--border); cursor:pointer; }
    .slide[data-type="toc"] ol li::before { content:counter(toc,decimal-leading-zero); font-size:var(--size-small); font-weight:700; color:var(--accent); min-width:2em; }
    .slide[data-type="toc"] ol li:hover { color:var(--accent); }

    /* ── divider ── */
    .slide[data-type="divider"] { justify-content:center; align-items:center; text-align:center; background:var(--accent); }
    .slide[data-type="divider"] .divider-number { font-size:var(--size-small); font-weight:700; letter-spacing:.2em; text-transform:uppercase; color:rgba(255,255,255,.6); margin-bottom:1rem; }
    .slide[data-type="divider"] h2 { font-size:clamp(2.5rem,6vw,5rem); font-weight:800; color:#fff; line-height:1.1; }
    .slide[data-type="divider"] .divider-sub { font-size:var(--size-body); color:rgba(255,255,255,.75); margin-top:1.5rem; }
    [data-theme="minimal"] .slide[data-type="divider"] { background:#000; }

    /* ── content ── */
    .slide[data-type="content"] { justify-content:flex-start; overflow-y:auto; }
    .slide[data-type="content"] h3 { font-size:var(--size-h3); font-weight:700; color:var(--fg); margin-bottom:1.5rem; padding-bottom:.75rem; border-bottom:2px solid var(--accent); width:100%; }
    [data-theme="corporate"] .slide[data-type="content"] h3 { background:var(--accent); color:#fff; padding:.75rem var(--pad-sm); border-bottom:none; margin:calc(-1*var(--pad)) calc(-1*var(--pad)) 1.5rem; width:calc(100% + 2*var(--pad)); font-size:var(--size-body); font-weight:600; }
    [data-theme="corporate"] .slide[data-type="content"]::before { content:''; display:block; height:4px; background:var(--accent); position:absolute; top:0; left:0; right:0; }
    .slide[data-type="content"] p { font-size:var(--size-body); line-height:1.7; color:var(--fg); margin-bottom:1rem; max-width:70ch; }
    .slide[data-type="content"] ul, .slide[data-type="content"] ol { padding-left:1.5rem; display:flex; flex-direction:column; gap:.5rem; max-width:70ch; }
    .slide[data-type="content"] li { font-size:var(--size-body); line-height:1.6; color:var(--fg); }
    .slide[data-type="content"] li::marker { color:var(--accent); }
    .slide[data-type="content"] code { font-family:var(--font-mono); font-size:.9em; background:var(--code-bg); color:var(--accent); padding:.15em .4em; border-radius:calc(var(--radius)*.5); border:1px solid var(--border); }
    .slide[data-type="content"] pre { background:var(--code-bg); border:1px solid var(--border); border-radius:var(--radius); padding:1.25rem; overflow-x:auto; font-family:var(--font-mono); font-size:.85em; line-height:1.6; width:100%; margin:1rem 0; }
    .slide[data-type="content"] pre code { background:none; border:none; padding:0; color:var(--fg); }
    .slide[data-type="content"] blockquote { border-left:3px solid var(--accent); padding:.75rem 1.25rem; background:var(--accent-light); border-radius:0 var(--radius) var(--radius) 0; font-style:italic; color:var(--fg-muted); margin:1rem 0; }
    .slide[data-type="content"] img { max-width:100%; height:auto; border-radius:var(--radius); border:1px solid var(--border); }
    .slide[data-type="content"] table { width:100%; border-collapse:collapse; font-size:var(--size-small); margin:1rem 0; }
    .slide[data-type="content"] th { background:var(--accent); color:#fff; padding:.6rem .9rem; text-align:left; font-weight:600; }
    .slide[data-type="content"] td { padding:.5rem .9rem; border-bottom:1px solid var(--border); color:var(--fg); }
    .slide[data-type="content"] tr:nth-child(even) td { background:var(--bg-alt); }

    /* ── closing ── */
    .slide[data-type="closing"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-type="closing"] .closing-icon { font-size:3rem; margin-bottom:1.5rem; opacity:.6; }
    .slide[data-type="closing"] h2 { font-size:var(--size-h2); font-weight:800; color:var(--fg); margin-bottom:1rem; }
    .slide[data-type="closing"] .closing-message { font-size:var(--size-body); color:var(--fg-muted); max-width:40ch; line-height:1.7; margin-bottom:2rem; }
    .slide[data-type="closing"] .closing-contact { display:flex; gap:1.5rem; justify-content:center; flex-wrap:wrap; font-size:var(--size-small); color:var(--accent); }
    [data-theme="corporate"] .slide[data-type="closing"] { background:var(--accent); }
    [data-theme="corporate"] .slide[data-type="closing"] h2,
    [data-theme="corporate"] .slide[data-type="closing"] .closing-message { color:rgba(255,255,255,.9); }
    [data-theme="corporate"] .slide[data-type="closing"] .closing-contact { color:#7eb8e8; }

    /* ══════════════════════════════════════════════════
       슬라이드 per-slide 레이아웃 CSS (data-layout)
       ══════════════════════════════════════════════════ */

    /* ── 구조형 ── */
    .slide[data-layout="title-cover"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-layout="section-divider"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-layout="agenda"] { justify-content:center; align-items:flex-start; }
    .slide[data-layout="end"] { justify-content:center; align-items:center; text-align:center; }

    /* ── 콘텐츠형 ── */
    .slide[data-layout="default"] { justify-content:flex-start; align-items:flex-start; }
    .slide[data-layout="default"] h3 { width:100%; margin-bottom:1.5rem; }

    .slide[data-layout="statement"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-layout="statement"] p { font-size:clamp(1.5rem,3.5vw,2.5rem); font-weight:700; line-height:1.3; max-width:20ch; color:var(--fg); }

    .slide[data-layout="quote"] { justify-content:center; align-items:flex-start; }
    .slide[data-layout="quote"] blockquote { font-size:clamp(1.2rem,2.5vw,1.8rem); font-style:italic; line-height:1.6; border-left:4px solid var(--accent); padding:1.5rem 2rem; background:var(--accent-light); border-radius:0 var(--radius) var(--radius) 0; max-width:50ch; color:var(--fg); }
    .slide[data-layout="quote"] blockquote p { font-size:inherit; margin-bottom:0; }
    .slide[data-layout="quote"] .quote-author { font-size:var(--size-body); color:var(--fg-muted); margin-top:1rem; font-style:normal; }

    .slide[data-layout="fact"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-layout="fact"] .fact-number { font-size:clamp(3rem,10vw,8rem); font-weight:900; line-height:1; color:var(--accent); margin-bottom:1rem; }
    .slide[data-layout="fact"] .fact-label { font-size:var(--size-h3); color:var(--fg-muted); max-width:30ch; }

    .slide[data-layout="intro-profile"] { justify-content:center; align-items:center; flex-direction:row; gap:3rem; }
    .slide[data-layout="intro-profile"] img { width:180px; height:180px; border-radius:50%; object-fit:cover; border:3px solid var(--accent); flex-shrink:0; }
    .slide[data-layout="intro-profile"] .profile-info { display:flex; flex-direction:column; gap:.5rem; }
    .slide[data-layout="intro-profile"] .profile-name { font-size:var(--size-h2); font-weight:800; color:var(--fg); }
    .slide[data-layout="intro-profile"] .profile-title { font-size:var(--size-body); color:var(--fg-muted); }

    .slide[data-layout="two-cols"] .cols-container { display:grid; grid-template-columns:1fr 1fr; gap:2rem; width:100%; height:100%; align-items:start; }
    .slide[data-layout="two-cols"] .cols-container .col { padding:0 1rem; }
    .slide[data-layout="two-cols"] .cols-container .col:first-child { border-right:1px solid var(--border); }

    .slide[data-layout="two-cols-header"] h3 { width:100%; margin-bottom:1.5rem; }
    .slide[data-layout="two-cols-header"] .cols-container { display:grid; grid-template-columns:1fr 1fr; gap:2rem; width:100%; flex:1; align-items:start; }
    .slide[data-layout="two-cols-header"] .cols-container .col { padding:0 1rem; }
    .slide[data-layout="two-cols-header"] .cols-container .col:first-child { border-right:1px solid var(--border); }

    .slide[data-layout="three-cols"] .cols-container { display:grid; grid-template-columns:1fr 1fr 1fr; gap:1.5rem; width:100%; height:100%; align-items:start; }
    .slide[data-layout="three-cols"] .cols-container .col { padding:0 .75rem; }
    .slide[data-layout="three-cols"] .cols-container .col:not(:last-child) { border-right:1px solid var(--border); }
    .slide[data-layout="three-cols"] .cols-container .col h3 { font-size:var(--size-body); font-weight:700; margin-bottom:1rem; padding-bottom:.5rem; border-bottom:2px solid var(--accent); }

    .slide[data-layout="icon-grid"] .grid-container { display:grid; grid-template-columns:repeat(auto-fit,minmax(200px,1fr)); gap:1.5rem; width:100%; margin-top:1rem; }
    .slide[data-layout="icon-grid"] .grid-item { display:flex; flex-direction:column; align-items:center; text-align:center; padding:1.5rem 1rem; background:var(--bg-alt); border-radius:var(--radius); border:1px solid var(--border); }
    .slide[data-layout="icon-grid"] .grid-item .icon { font-size:2rem; margin-bottom:.75rem; }
    .slide[data-layout="icon-grid"] .grid-item .grid-label { font-size:var(--size-body); font-weight:600; color:var(--fg); }
    .slide[data-layout="icon-grid"] .grid-item .grid-desc { font-size:var(--size-small); color:var(--fg-muted); margin-top:.25rem; }

    .slide[data-layout="list"] { justify-content:flex-start; align-items:flex-start; }
    .slide[data-layout="list"] ul, .slide[data-layout="list"] ol { padding-left:1.5rem; display:flex; flex-direction:column; gap:.75rem; max-width:70ch; }
    .slide[data-layout="list"] li { font-size:var(--size-body); line-height:1.6; }

    .slide[data-layout="center"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-layout="center"] h3 { font-size:var(--size-h2); border-bottom:none; margin-bottom:.5rem; }

    /* ── 데이터형 ── */
    .slide[data-layout="data-chart"] { justify-content:flex-start; align-items:center; padding:var(--pad-sm) var(--pad); }
    .slide[data-layout="data-chart"] h3 { font-size:var(--size-body); margin-bottom:.75rem; width:100%; }
    .slide[data-layout="data-chart"] .chart-area { flex:1; width:100%; display:flex; justify-content:center; align-items:center; min-height:0; }
    .slide[data-layout="data-chart"] .chart-area svg { max-width:100%; max-height:100%; }

    .slide[data-layout="comparison"] .compare-container { display:grid; grid-template-columns:1fr 1fr; gap:0; width:100%; flex:1; }
    .slide[data-layout="comparison"] .compare-side { padding:2rem; }
    .slide[data-layout="comparison"] .compare-side:first-child { background:var(--accent-light); border-radius:var(--radius) 0 0 var(--radius); }
    .slide[data-layout="comparison"] .compare-side:last-child { background:var(--bg-alt); border-radius:0 var(--radius) var(--radius) 0; }
    .slide[data-layout="comparison"] .compare-side h4 { font-size:var(--size-h3); font-weight:700; margin-bottom:1rem; color:var(--fg); }

    .slide[data-layout="table-data"] { justify-content:flex-start; padding:var(--pad-sm) var(--pad); }
    .slide[data-layout="table-data"] h3 { font-size:var(--size-body); margin-bottom:.75rem; }
    .slide[data-layout="table-data"] table { width:100%; border-collapse:collapse; font-size:calc(var(--size-small)*.95); }
    .slide[data-layout="table-data"] th { background:var(--accent); color:#fff; padding:.4rem .6rem; text-align:left; font-weight:600; font-size:calc(var(--size-small)*.9); }
    .slide[data-layout="table-data"] td { padding:.35rem .6rem; border-bottom:1px solid var(--border); }
    .slide[data-layout="table-data"] tr:nth-child(even) td { background:var(--bg-alt); }

    .slide[data-layout="dashboard"] { justify-content:flex-start; padding:var(--pad-sm); }
    .slide[data-layout="dashboard"] h3 { font-size:var(--size-body); margin-bottom:.75rem; width:100%; }
    .slide[data-layout="dashboard"] .kpi-row { display:grid; grid-template-columns:repeat(auto-fit,minmax(150px,1fr)); gap:1rem; width:100%; margin-bottom:1rem; }
    .slide[data-layout="dashboard"] .kpi-card { background:var(--bg-alt); border:1px solid var(--border); border-radius:var(--radius); padding:1rem; text-align:center; }
    .slide[data-layout="dashboard"] .kpi-card .kpi-value { font-size:var(--size-h2); font-weight:800; color:var(--accent); }
    .slide[data-layout="dashboard"] .kpi-card .kpi-label { font-size:var(--size-small); color:var(--fg-muted); margin-top:.25rem; }
    .slide[data-layout="dashboard"] table { width:100%; font-size:calc(var(--size-small)*.9); }

    /* ── 시각형 ── */
    .slide[data-layout="fullbleed-image"] { padding:0; position:relative; }
    .slide[data-layout="fullbleed-image"] img { position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover; border-radius:0; border:none; }
    .slide[data-layout="fullbleed-image"] .overlay-text { position:relative; z-index:1; background:rgba(0,0,0,.5); color:#fff; padding:1.5rem 2rem; border-radius:var(--radius); margin:auto; text-align:center; }

    .slide[data-layout="image-left"] { flex-direction:row; padding:0; gap:0; }
    .slide[data-layout="image-left"] .image-panel { width:40%; height:100%; flex-shrink:0; overflow:hidden; }
    .slide[data-layout="image-left"] .image-panel img { width:100%; height:100%; object-fit:cover; border-radius:0; border:none; }
    .slide[data-layout="image-left"] .text-panel { flex:1; padding:var(--pad); display:flex; flex-direction:column; justify-content:center; overflow-y:auto; }

    .slide[data-layout="image-right"] { flex-direction:row; padding:0; gap:0; }
    .slide[data-layout="image-right"] .text-panel { flex:1; padding:var(--pad); display:flex; flex-direction:column; justify-content:center; overflow-y:auto; }
    .slide[data-layout="image-right"] .image-panel { width:40%; height:100%; flex-shrink:0; overflow:hidden; }
    .slide[data-layout="image-right"] .image-panel img { width:100%; height:100%; object-fit:cover; border-radius:0; border:none; }

    .slide[data-layout="asymmetric-split"] .cols-container { display:grid; gap:2rem; width:100%; height:100%; align-items:start; }
    .slide[data-layout="asymmetric-split"] .cols-container.ratio-60-40 { grid-template-columns:3fr 2fr; }
    .slide[data-layout="asymmetric-split"] .cols-container.ratio-70-30 { grid-template-columns:7fr 3fr; }
    .slide[data-layout="asymmetric-split"] .cols-container.ratio-30-70 { grid-template-columns:3fr 7fr; }
    .slide[data-layout="asymmetric-split"] .cols-container.ratio-40-60 { grid-template-columns:2fr 3fr; }
    .slide[data-layout="asymmetric-split"] .cols-container .col { padding:0 1rem; }

    .slide[data-layout="iframe-embed"] { padding:var(--pad-sm); }
    .slide[data-layout="iframe-embed"] h3 { font-size:var(--size-body); margin-bottom:.5rem; }
    .slide[data-layout="iframe-embed"] iframe { flex:1; width:100%; border:1px solid var(--border); border-radius:var(--radius); }

    /* ── 특수형 ── */
    .slide[data-layout="code-focus"] { justify-content:flex-start; padding:var(--pad-sm) var(--pad); }
    .slide[data-layout="code-focus"] h3 { font-size:var(--size-body); margin-bottom:.5rem; }
    .slide[data-layout="code-focus"] pre { flex:1; width:100%; overflow:auto; font-size:clamp(.7rem,1.5vw,.95rem); line-height:1.5; margin:0; }

    .slide[data-layout="timeline"] { justify-content:flex-start; align-items:flex-start; }
    .slide[data-layout="timeline"] .timeline-container { position:relative; padding-left:2.5rem; display:flex; flex-direction:column; gap:1.5rem; width:100%; }
    .slide[data-layout="timeline"] .timeline-container::before { content:''; position:absolute; left:.75rem; top:0; bottom:0; width:2px; background:var(--accent); }
    .slide[data-layout="timeline"] .timeline-item { position:relative; }
    .slide[data-layout="timeline"] .timeline-item::before { content:''; position:absolute; left:-1.95rem; top:.4rem; width:12px; height:12px; border-radius:50%; background:var(--accent); border:2px solid var(--bg); }
    .slide[data-layout="timeline"] .timeline-date { font-size:var(--size-small); font-weight:700; color:var(--accent); margin-bottom:.25rem; }
    .slide[data-layout="timeline"] .timeline-content { font-size:var(--size-body); color:var(--fg); }

    .slide[data-layout="steps-process"] .steps-container { display:flex; align-items:flex-start; gap:0; width:100%; margin-top:1rem; }
    .slide[data-layout="steps-process"] .step { flex:1; display:flex; flex-direction:column; align-items:center; text-align:center; padding:0 .75rem; position:relative; }
    .slide[data-layout="steps-process"] .step:not(:last-child)::after { content:''; position:absolute; right:-.5rem; top:1.25rem; width:1rem; height:2px; background:var(--accent); }
    .slide[data-layout="steps-process"] .step-number { width:2.5rem; height:2.5rem; border-radius:50%; background:var(--accent); color:#fff; display:flex; align-items:center; justify-content:center; font-weight:700; font-size:var(--size-body); margin-bottom:.75rem; flex-shrink:0; }
    .slide[data-layout="steps-process"] .step-label { font-size:var(--size-body); font-weight:600; color:var(--fg); margin-bottom:.25rem; }
    .slide[data-layout="steps-process"] .step-desc { font-size:var(--size-small); color:var(--fg-muted); }

    /* ── 다이어그램형 ── */
    .slide[data-layout="diagram"] { justify-content:flex-start; align-items:center; padding:var(--pad-sm) var(--pad); }
    .slide[data-layout="diagram"] h3 { font-size:var(--size-body); margin-bottom:.5rem; width:100%; }
    .slide[data-layout="diagram"] .diagram-area { flex:1; width:100%; display:flex; justify-content:center; align-items:center; min-height:0; overflow:auto; }
    .slide[data-layout="diagram"] .diagram-area svg { max-width:100%; max-height:100%; }

    .slide[data-layout="diagram-fullbleed"] { padding:0; position:relative; justify-content:center; align-items:center; }
    .slide[data-layout="diagram-fullbleed"] h3 { position:absolute; top:1rem; left:1.5rem; font-size:var(--size-small); color:var(--fg-muted); border-bottom:none; margin:0; z-index:1; background:var(--bg); padding:.25rem .75rem; border-radius:var(--radius); opacity:.85; }
    .slide[data-layout="diagram-fullbleed"] .diagram-area { width:100%; height:100%; display:flex; justify-content:center; align-items:center; padding:2rem; }
    .slide[data-layout="diagram-fullbleed"] .diagram-area svg { max-width:100%; max-height:100%; }

    .slide[data-layout="diagram-split"] { flex-direction:row; padding:0; gap:0; }
    .slide[data-layout="diagram-split"] .diagram-panel { width:60%; height:100%; display:flex; justify-content:center; align-items:center; padding:2rem; overflow:auto; }
    .slide[data-layout="diagram-split"] .diagram-panel svg { max-width:100%; max-height:100%; }
    .slide[data-layout="diagram-split"] .text-panel { width:40%; height:100%; padding:var(--pad); display:flex; flex-direction:column; justify-content:center; overflow-y:auto; border-left:1px solid var(--border); background:var(--bg-alt); }
  </style>
</head>
<body>
  <div class="slideshow" id="slideshow">
    {{SLIDES}}
  </div>

  <div class="slide-counter" id="slideCounter">
    <span id="currentSlide">1</span> / <span id="totalSlides">1</span>
  </div>

  <script>
    /* 네비게이션 JS — Section 4 참조 */
    (function(){
      'use strict';
      const slideshow=document.getElementById('slideshow');
      const currentEl=document.getElementById('currentSlide');
      const totalEl=document.getElementById('totalSlides');
      const transition=document.documentElement.dataset.transition||'horizontal';
      const slides=Array.from(slideshow.querySelectorAll('.slide'));
      let current=0;

      function hashToIndex(h){const m=(h||'').match(/slide-(\d+)/);if(m){const i=parseInt(m[1],10)-1;if(i>=0&&i<slides.length)return i;}return 0;}
      function updateHash(i){const id=slides[i].id||('slide-'+(i+1));history.replaceState(null,'','#'+id);}
      function updateCounter(i){if(currentEl)currentEl.textContent=i+1;if(totalEl)totalEl.textContent=slides.length;}

      function goTo(idx,skipHash){
        if(idx<0||idx>=slides.length)return;
        const prev=current;
        slides[prev].classList.remove('active');
        if(transition!=='fade')slides[prev].classList.add('prev');
        current=idx;
        slides[current].classList.remove('prev');
        slides[current].classList.add('active');
        if(transition!=='fade'){
          setTimeout(()=>{slides.forEach((s,i)=>{if(i!==current)s.classList.remove('prev');});},400);
        }
        updateCounter(current);
        if(!skipHash)updateHash(current);
      }

      function next(){goTo(current+1);}
      function prev(){goTo(current-1);}

      document.addEventListener('keydown',function(e){
        if(e.key==='ArrowRight'||e.key==='ArrowDown'||e.key===' '){e.preventDefault();next();}
        else if(e.key==='ArrowLeft'||e.key==='ArrowUp'){e.preventDefault();prev();}
        else if(e.key==='Home'){e.preventDefault();goTo(0);}
        else if(e.key==='End'){e.preventDefault();goTo(slides.length-1);}
      });

      document.addEventListener('click',function(e){
        if(e.target.tagName==='A')return;
        const gt=e.target.closest('[data-goto]');
        if(gt){const i=slides.findIndex(s=>s.id===gt.dataset.goto);if(i!==-1){goTo(i);return;}}
        next();
      });

      window.addEventListener('hashchange',function(){const i=hashToIndex(location.hash);if(i!==current)goTo(i,true);});

      let tsx=0,tsy=0;
      document.addEventListener('touchstart',function(e){tsx=e.changedTouches[0].screenX;tsy=e.changedTouches[0].screenY;},{passive:true});
      document.addEventListener('touchend',function(e){
        const dx=e.changedTouches[0].screenX-tsx,dy=e.changedTouches[0].screenY-tsy,th=50;
        if(transition==='vertical'){if(dy<-th)next();else if(dy>th)prev();}
        else{if(dx<-th)next();else if(dx>th)prev();}
      },{passive:true});

      function init(){
        if(totalEl)totalEl.textContent=slides.length;
        slides.forEach((s,i)=>{s.classList.remove('active','prev');if(!s.id)s.id='slide-'+(i+1);});
        const start=hashToIndex(location.hash);
        if(transition!=='fade'){for(let i=0;i<start;i++)slides[i].classList.add('prev');}
        slides[start].classList.add('active');
        current=start;updateCounter(current);
      }
      init();
    })();
  </script>
</body>
</html>
```

---

## 6. 생성 가이드 요약

| 항목 | 치환값 |
|------|--------|
| `{{THEME}}` | `default` / `dark` / `minimal` / `corporate` |
| `{{TRANSITION}}` | `horizontal` / `vertical` / `fade` |
| `{{TITLE}}` | 문서 제목 |
| `{{SLIDES}}` | 각 슬라이드 HTML (`.slide` div들) |
| `data-type` | `cover` / `toc` / `divider` / `content` / `closing` |
| `data-layout` | 슬라이드별 자동 감지 결과 (30개 중 1개) |

### 슬라이드 타입 결정 규칙
- **H1** (문서 최상단) → `cover` (data-layout=`title-cover`)
- **TOC** (자동 생성) → `toc` (data-layout=`agenda`)
- **H2** (단독 섹션 제목) → `divider` (data-layout=`section-divider`)
- **H3 + 본문** → `content` (data-layout=자동 감지)
- **마지막** → `closing` (data-layout=`end`)
