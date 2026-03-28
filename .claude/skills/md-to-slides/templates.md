# md-to-slides: HTML 슬라이드 템플릿 명세

이 파일은 Markdown → HTML 슬라이드 변환 시 참조하는 완전한 CSS/JS 레퍼런스다.

---

## 1. CSS 변수 명세 (테마별)

### 공통 CSS 변수 구조

```css
:root {
  /* === 색상 === */
  --bg:           #ffffff;   /* 슬라이드 배경 */
  --bg-alt:       #f5f5f5;   /* 보조 배경 (divider, split 패널 등) */
  --fg:           #1a1a1a;   /* 기본 텍스트 */
  --fg-muted:     #6b7280;   /* 보조 텍스트 */
  --accent:       #2563eb;   /* 포인트 색상 (링크, 강조, 헤더 등) */
  --accent-light: #dbeafe;   /* 포인트 연하게 */
  --border:       #e5e7eb;   /* 구분선 */
  --code-bg:      #f3f4f6;   /* 코드블록 배경 */
  --nav-bg:       #f9fafb;   /* split 레이아웃 네비 패널 배경 */

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
  --nav-bg:       #f8fafc;
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
  --nav-bg:       #181825;
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
  --nav-bg:       #fafafa;
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
  --nav-bg:       #1e3a5f;
  --nav-fg:       #ffffff;        /* corporate 전용: 네비 패널 텍스트 */
  --header-bg:    #1e3a5f;        /* corporate 전용: 헤더 배경 */
  --header-fg:    #ffffff;        /* corporate 전용: 헤더 텍스트 */
}
```

---

## 2. 레이아웃별 HTML 구조 스켈레톤

### 공통 HTML 래퍼

```html
<!DOCTYPE html>
<html lang="ko" data-theme="default" data-layout="horizontal">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>{{TITLE}}</title>
  <style>
    /* === BASE RESET === */
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

    /* === 테마 CSS 변수 (위 명세 삽입) === */
    /* ... */

    /* === 레이아웃별 CSS (아래 명세 삽입) === */
    /* ... */

    /* === 슬라이드 타입별 CSS (아래 명세 삽입) === */
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

### 레이아웃: horizontal (좌우 전환)

```css
/* data-layout="horizontal" */
[data-layout="horizontal"] .slideshow {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: var(--bg);
}

[data-layout="horizontal"] .slide {
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

[data-layout="horizontal"] .slide.active {
  transform: translateX(0);
}

[data-layout="horizontal"] .slide.prev {
  transform: translateX(-100%);
}

/* 슬라이드 카운터 위치 */
[data-layout="horizontal"] .slide-counter {
  position: fixed;
  bottom: 1.5rem;
  right: 2rem;
  font-size: var(--size-small);
  color: var(--fg-muted);
  font-family: var(--font-sans);
  z-index: 100;
}
```

```html
<!-- horizontal 레이아웃 슬라이드 예시 -->
<div class="slideshow" id="slideshow">
  <div class="slide active" id="slide-1" data-type="cover">
    <!-- 슬라이드 내용 -->
  </div>
  <div class="slide" id="slide-2" data-type="content">
    <!-- 슬라이드 내용 -->
  </div>
</div>
```

---

### 레이아웃: vertical (상하 전환)

```css
/* data-layout="vertical" */
[data-layout="vertical"] .slideshow {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: var(--bg);
}

[data-layout="vertical"] .slide {
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

[data-layout="vertical"] .slide.active {
  transform: translateY(0);
}

[data-layout="vertical"] .slide.prev {
  transform: translateY(-100%);
}

[data-layout="vertical"] .slide-counter {
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

### 레이아웃: split (왼쪽 네비 패널 + 오른쪽 콘텐츠)

```css
/* data-layout="split" */
[data-layout="split"] body {
  display: flex;
  height: 100vh;
  overflow: hidden;
}

/* 왼쪽 네비게이션 패널 */
[data-layout="split"] .split-nav {
  width: 260px;
  min-width: 200px;
  flex-shrink: 0;
  background: var(--nav-bg);
  border-right: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  overflow-y: auto;
  padding: 1.5rem 1rem;
  gap: 0.25rem;
  z-index: 10;
}

/* corporate 테마 전용 nav 색상 */
[data-theme="corporate"][data-layout="split"] .split-nav {
  background: var(--nav-bg);
  color: var(--nav-fg, #fff);
}

[data-layout="split"] .split-nav-title {
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  color: var(--fg-muted);
  padding: 0.5rem 0.75rem 1rem;
  border-bottom: 1px solid var(--border);
  margin-bottom: 0.5rem;
}

[data-layout="split"] .split-nav-item {
  padding: 0.5rem 0.75rem;
  border-radius: var(--radius);
  font-size: var(--size-small);
  color: var(--fg-muted);
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

[data-layout="split"] .split-nav-item:hover {
  background: var(--accent-light);
  color: var(--accent);
}

[data-layout="split"] .split-nav-item.active {
  background: var(--accent-light);
  color: var(--accent);
  font-weight: 600;
}

/* 오른쪽 콘텐츠 영역 */
[data-layout="split"] .slideshow {
  flex: 1;
  position: relative;
  overflow: hidden;
  background: var(--bg);
}

[data-layout="split"] .slide {
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

[data-layout="split"] .slide.active {
  opacity: 1;
  pointer-events: auto;
}

[data-layout="split"] .slide-counter {
  position: fixed;
  bottom: 1.5rem;
  right: 2rem;
  font-size: var(--size-small);
  color: var(--fg-muted);
  font-family: var(--font-sans);
  z-index: 100;
}
```

```html
<!-- split 레이아웃 HTML 구조 -->
<!DOCTYPE html>
<html lang="ko" data-theme="default" data-layout="split">
<head><!-- ... --></head>
<body>
  <!-- 왼쪽 패널 (JS가 자동 생성) -->
  <nav class="split-nav" id="splitNav">
    <div class="split-nav-title">목차</div>
    <!-- JS가 슬라이드 제목 기반으로 채움 -->
  </nav>

  <!-- 오른쪽 콘텐츠 -->
  <div class="slideshow" id="slideshow">
    <div class="slide active" id="slide-1" data-type="cover"><!-- ... --></div>
    <div class="slide" id="slide-2" data-type="content"><!-- ... --></div>
  </div>

  <div class="slide-counter" id="slideCounter">
    <span id="currentSlide">1</span> / <span id="totalSlides">1</span>
  </div>
</body>
</html>
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

```html
<!-- cover 슬라이드 HTML -->
<div class="slide active" id="slide-1" data-type="cover">
  <span class="cover-eyebrow">{{EYEBROW}}</span>
  <h1>{{TITLE}}</h1>
  <p class="subtitle">{{SUBTITLE}}</p>
  <div class="meta">
    <span>{{AUTHOR}}</span>
    <span>{{DATE}}</span>
  </div>
</div>
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

```html
<!-- toc 슬라이드 HTML -->
<div class="slide" id="slide-2" data-type="toc">
  <h2>목차</h2>
  <ol>
    <li data-goto="slide-3">섹션 1 제목</li>
    <li data-goto="slide-5">섹션 2 제목</li>
    <li data-goto="slide-8">섹션 3 제목</li>
  </ol>
</div>
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

```html
<!-- divider 슬라이드 HTML -->
<div class="slide" id="slide-3" data-type="divider">
  <span class="divider-number">01</span>
  <h2>섹션 제목</h2>
  <p class="divider-sub">간략한 섹션 설명 (선택)</p>
</div>
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

/* 2컬럼 레이아웃 유틸리티 */
.slide[data-type="content"] .cols-2 {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 2rem;
  width: 100%;
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

```html
<!-- content 슬라이드 HTML -->
<div class="slide" id="slide-4" data-type="content">
  <h3>슬라이드 제목</h3>
  <p>본문 내용...</p>
  <ul>
    <li>항목 1</li>
    <li>항목 2</li>
  </ul>
</div>
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

```html
<!-- closing 슬라이드 HTML -->
<div class="slide" id="slide-10" data-type="closing">
  <div class="closing-icon">🎉</div>
  <h2>감사합니다</h2>
  <p class="closing-message">질문이 있으시면 언제든지 연락주세요.</p>
  <div class="closing-contact">
    <span>{{EMAIL}}</span>
    <span>{{GITHUB}}</span>
  </div>
</div>
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
  const layout     = document.documentElement.dataset.layout || 'horizontal';

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

  // split 레이아웃: 네비 패널 아이템 활성화
  function updateSplitNav(idx) {
    if (layout !== 'split') return;
    const items = document.querySelectorAll('.split-nav-item');
    items.forEach((item, i) => {
      item.classList.toggle('active', i === idx);
    });
  }

  function goTo(idx, skipHash) {
    if (idx < 0 || idx >= slides.length) return;

    const prev = current;
    slides[prev].classList.remove('active');
    if (layout !== 'split') {
      slides[prev].classList.add('prev');
    }

    current = idx;
    slides[current].classList.remove('prev');
    slides[current].classList.add('active');

    // prev 클래스 정리 (전환 완료 후)
    setTimeout(() => {
      slides.forEach((s, i) => {
        if (i !== current) s.classList.remove('prev');
      });
    }, 400);

    updateCounter(current);
    updateSplitNav(current);
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
  // split 레이아웃은 slideshow 영역 클릭만 처리
  const clickTarget = layout === 'split' ? slideshow : document;
  clickTarget.addEventListener('click', function (e) {
    // split-nav 클릭 무시 (별도 처리)
    if (e.target.closest('.split-nav')) return;
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

  // ── split 네비 패널 자동 생성 ──────────────────────
  function buildSplitNav() {
    if (layout !== 'split') return;
    const nav = document.getElementById('splitNav');
    if (!nav) return;
    slides.forEach((slide, i) => {
      const heading = slide.querySelector('h1, h2, h3');
      const label   = heading ? heading.textContent.trim() : ('슬라이드 ' + (i + 1));
      const item    = document.createElement('div');
      item.className = 'split-nav-item';
      item.textContent = label;
      item.dataset.index = i;
      item.addEventListener('click', function () { goTo(i); });
      nav.appendChild(item);
    });
  }

  // ── TOC 항목 클릭 핸들러 (data-goto) ─────────────
  // (위 clickTarget listener에서 통합 처리됨)

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
    if (layout === 'vertical') {
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

    buildSplitNav();

    // 초기 슬라이드 결정
    const startIdx = hashToIndex(location.hash);
    // 첫 슬라이드 이전 슬라이드들을 prev 상태로 초기화
    for (let i = 0; i < startIdx; i++) {
      slides[i].classList.add('prev');
    }
    slides[startIdx].classList.add('active');
    current = startIdx;
    updateCounter(current);
    updateSplitNav(current);
  }

  init();
})();
```

---

## 5. 완전한 단일 파일 HTML 템플릿 (레퍼런스 구현)

아래는 `horizontal` + `default` 테마를 사용하는 최소 완전 구현이다.
실제 생성 시 `data-theme`과 `data-layout`을 치환하면 된다.

```html
<!DOCTYPE html>
<html lang="ko" data-theme="{{THEME}}" data-layout="{{LAYOUT}}">
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
      --code-bg: #f1f5f9; --nav-bg: #f8fafc;
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
      --code-bg:#181825; --nav-bg:#181825;
    }
    [data-theme="minimal"] {
      --bg:#fff; --bg-alt:#fff; --fg:#000; --fg-muted:#555;
      --accent:#000; --accent-light:#f0f0f0; --border:#ddd;
      --code-bg:#f8f8f8; --font-sans:'Helvetica Neue',Arial,sans-serif; --radius:0;
    }
    [data-theme="corporate"] {
      --bg:#f4f6f9; --bg-alt:#e8ecf2; --fg:#1a2332; --fg-muted:#5a6a7e;
      --accent:#1e3a5f; --accent-light:#d0dce8; --border:#c8d3df;
      --code-bg:#edf0f4; --nav-bg:#1e3a5f;
    }

    /* ── Slideshow 기본 ── */
    .slideshow { position:relative; width:100vw; height:100vh; overflow:hidden; background:var(--bg); }

    /* ── horizontal / vertical 공통 슬라이드 ── */
    .slide {
      position:absolute; top:0; left:0; width:100%; height:100%;
      display:flex; flex-direction:column; justify-content:center; align-items:flex-start;
      padding:var(--pad); background:var(--bg); color:var(--fg); font-family:var(--font-sans);
      will-change:transform; transition:transform var(--transition);
    }

    /* horizontal */
    [data-layout="horizontal"] .slide             { transform:translateX(100%); }
    [data-layout="horizontal"] .slide.active      { transform:translateX(0); }
    [data-layout="horizontal"] .slide.prev        { transform:translateX(-100%); }

    /* vertical */
    [data-layout="vertical"] .slide               { transform:translateY(100%); }
    [data-layout="vertical"] .slide.active        { transform:translateY(0); }
    [data-layout="vertical"] .slide.prev          { transform:translateY(-100%); }

    /* split */
    [data-layout="split"] body { display:flex; height:100vh; overflow:hidden; }
    [data-layout="split"] .split-nav {
      width:260px; flex-shrink:0; background:var(--nav-bg); border-right:1px solid var(--border);
      display:flex; flex-direction:column; overflow-y:auto; padding:1.5rem 1rem; gap:.25rem;
    }
    [data-theme="corporate"][data-layout="split"] .split-nav { color:#fff; }
    [data-layout="split"] .split-nav-title {
      font-size:.75rem; font-weight:700; letter-spacing:.1em; text-transform:uppercase;
      color:var(--fg-muted); padding:.5rem .75rem 1rem; border-bottom:1px solid var(--border); margin-bottom:.5rem;
    }
    [data-theme="corporate"][data-layout="split"] .split-nav-title { color:rgba(255,255,255,.5); border-color:rgba(255,255,255,.2); }
    [data-layout="split"] .split-nav-item {
      padding:.5rem .75rem; border-radius:var(--radius); font-size:var(--size-small);
      color:var(--fg-muted); cursor:pointer; transition:background .2s,color .2s;
    }
    [data-theme="corporate"][data-layout="split"] .split-nav-item { color:rgba(255,255,255,.7); }
    [data-layout="split"] .split-nav-item:hover,
    [data-layout="split"] .split-nav-item.active { background:var(--accent-light); color:var(--accent); }
    [data-theme="corporate"][data-layout="split"] .split-nav-item:hover,
    [data-theme="corporate"][data-layout="split"] .split-nav-item.active { background:rgba(255,255,255,.15); color:#fff; }
    [data-layout="split"] .slideshow { flex:1; position:relative; overflow:hidden; }
    [data-layout="split"] .slide { transition:opacity var(--transition); transform:none !important; opacity:0; pointer-events:none; }
    [data-layout="split"] .slide.active { opacity:1; pointer-events:auto; }

    /* ── 슬라이드 카운터 ── */
    .slide-counter {
      position:fixed; bottom:1.5rem; right:2rem; font-size:var(--size-small);
      color:var(--fg-muted); font-family:var(--font-sans); z-index:100; user-select:none;
    }

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
    .slide[data-type="content"] p { font-size:var(--size-body); line-height:1.7; color:var(--fg); margin-bottom:1rem; max-width:70ch; }
    .slide[data-type="content"] ul, .slide[data-type="content"] ol { padding-left:1.5rem; display:flex; flex-direction:column; gap:.5rem; max-width:70ch; }
    .slide[data-type="content"] li { font-size:var(--size-body); line-height:1.6; color:var(--fg); }
    .slide[data-type="content"] li::marker { color:var(--accent); }
    .slide[data-type="content"] code { font-family:var(--font-mono); font-size:.9em; background:var(--code-bg); color:var(--accent); padding:.15em .4em; border-radius:calc(var(--radius)*.5); border:1px solid var(--border); }
    .slide[data-type="content"] pre { background:var(--code-bg); border:1px solid var(--border); border-radius:var(--radius); padding:1.25rem; overflow-x:auto; font-family:var(--font-mono); font-size:.85em; line-height:1.6; width:100%; margin:1rem 0; }
    .slide[data-type="content"] pre code { background:none; border:none; padding:0; color:var(--fg); }
    .slide[data-type="content"] blockquote { border-left:3px solid var(--accent); padding:.75rem 1.25rem; background:var(--accent-light); border-radius:0 var(--radius) var(--radius) 0; font-style:italic; color:var(--fg-muted); margin:1rem 0; }
    .slide[data-type="content"] .cols-2 { display:grid; grid-template-columns:1fr 1fr; gap:2rem; width:100%; }
    .slide[data-type="content"] table { width:100%; border-collapse:collapse; font-size:var(--size-small); margin:1rem 0; }
    .slide[data-type="content"] th { background:var(--accent); color:#fff; padding:.6rem .9rem; text-align:left; font-weight:600; }
    .slide[data-type="content"] td { padding:.5rem .9rem; border-bottom:1px solid var(--border); color:var(--fg); }
    .slide[data-type="content"] tr:nth-child(even) td { background:var(--bg-alt); }

    /* ── closing ── */
    .slide[data-type="closing"] { justify-content:center; align-items:center; text-align:center; }
    .slide[data-type="closing"] h2 { font-size:var(--size-h2); font-weight:800; color:var(--fg); margin-bottom:1rem; }
    .slide[data-type="closing"] .closing-message { font-size:var(--size-body); color:var(--fg-muted); max-width:40ch; line-height:1.7; margin-bottom:2rem; }
    .slide[data-type="closing"] .closing-contact { display:flex; gap:1.5rem; justify-content:center; flex-wrap:wrap; font-size:var(--size-small); color:var(--accent); }
    [data-theme="corporate"] .slide[data-type="closing"] { background:var(--accent); }
    [data-theme="corporate"] .slide[data-type="closing"] h2,
    [data-theme="corporate"] .slide[data-type="closing"] .closing-message { color:rgba(255,255,255,.9); }
    [data-theme="corporate"] .slide[data-type="closing"] .closing-contact { color:#7eb8e8; }
  </style>
</head>
<body>
  <!-- split 레이아웃 전용 nav (JS가 채움) -->
  <nav class="split-nav" id="splitNav" style="display:none">
    <div class="split-nav-title">목차</div>
  </nav>

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
      const layout=document.documentElement.dataset.layout||'horizontal';
      const slides=Array.from(slideshow.querySelectorAll('.slide'));
      let current=0;

      // split nav 표시
      const splitNav=document.getElementById('splitNav');
      if(layout==='split'&&splitNav) splitNav.style.display='flex';

      function hashToIndex(h){const m=(h||'').match(/slide-(\d+)/);if(m){const i=parseInt(m[1],10)-1;if(i>=0&&i<slides.length)return i;}return 0;}
      function updateHash(i){const id=slides[i].id||('slide-'+(i+1));history.replaceState(null,'','#'+id);}
      function updateCounter(i){if(currentEl)currentEl.textContent=i+1;if(totalEl)totalEl.textContent=slides.length;}
      function updateSplitNav(i){if(layout!=='split')return;document.querySelectorAll('.split-nav-item').forEach((el,j)=>el.classList.toggle('active',j===i));}

      function goTo(idx,skipHash){
        if(idx<0||idx>=slides.length)return;
        const prev=current;
        slides[prev].classList.remove('active');
        if(layout!=='split')slides[prev].classList.add('prev');
        current=idx;
        slides[current].classList.remove('prev');
        slides[current].classList.add('active');
        setTimeout(()=>{slides.forEach((s,i)=>{if(i!==current)s.classList.remove('prev');});},400);
        updateCounter(current);updateSplitNav(current);
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

      const clickTarget=layout==='split'?slideshow:document;
      clickTarget.addEventListener('click',function(e){
        if(e.target.closest('.split-nav'))return;
        if(e.target.tagName==='A')return;
        const gt=e.target.closest('[data-goto]');
        if(gt){const i=slides.findIndex(s=>s.id===gt.dataset.goto);if(i!==-1){goTo(i);return;}}
        next();
      });

      function buildSplitNav(){
        if(layout!=='split')return;
        const nav=document.getElementById('splitNav');
        if(!nav)return;
        slides.forEach((slide,i)=>{
          const h=slide.querySelector('h1,h2,h3');
          const label=h?h.textContent.trim():('슬라이드'+(i+1));
          const item=document.createElement('div');
          item.className='split-nav-item';item.textContent=label;item.dataset.index=i;
          item.addEventListener('click',function(){goTo(i);});
          nav.appendChild(item);
        });
      }

      window.addEventListener('hashchange',function(){const i=hashToIndex(location.hash);if(i!==current)goTo(i,true);});

      let tsx=0,tsy=0;
      document.addEventListener('touchstart',function(e){tsx=e.changedTouches[0].screenX;tsy=e.changedTouches[0].screenY;},{passive:true});
      document.addEventListener('touchend',function(e){
        const dx=e.changedTouches[0].screenX-tsx,dy=e.changedTouches[0].screenY-tsy,th=50;
        if(layout==='vertical'){if(dy<-th)next();else if(dy>th)prev();}
        else{if(dx<-th)next();else if(dx>th)prev();}
      },{passive:true});

      function init(){
        if(totalEl)totalEl.textContent=slides.length;
        slides.forEach((s,i)=>{s.classList.remove('active','prev');if(!s.id)s.id='slide-'+(i+1);});
        buildSplitNav();
        const start=hashToIndex(location.hash);
        for(let i=0;i<start;i++)slides[i].classList.add('prev');
        slides[start].classList.add('active');
        current=start;updateCounter(current);updateSplitNav(current);
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
| `{{LAYOUT}}` | `horizontal` / `vertical` / `split` |
| `{{TITLE}}` | 문서 제목 |
| `{{SLIDES}}` | 각 슬라이드 HTML (`.slide` div들) |
| `data-type` | `cover` / `toc` / `divider` / `content` / `closing` |

### 슬라이드 타입 결정 규칙
- **H1** (문서 최상단) → `cover`
- **`## 목차`** 섹션 → `toc`
- **H2** (단독 섹션 제목) → `divider`
- **H3 + 본문** → `content`
- **`## 마무리` / `## 감사합니다`** → `closing`
