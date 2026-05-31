# masterPortfolio-Style Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rewrite the Jekyll portfolio with masterPortfolio-inspired design — single-page scrolling sections, warm orange palette, large hero, experience timeline, gradient-top-bar project cards with rich modals, and lifestyle photo grid.

**Architecture:** Two files only — `_sass/4-layouts/_portfolio.scss` (full CSS rewrite, all classes prefixed `mp-`) and `_layouts/portfolio.html` (full HTML rewrite, scroll sections replacing tabs). The `portfolio-base.html` layout is unchanged. All CSS custom properties scoped to `body.mp-page`.

**Tech Stack:** Jekyll/Liquid, SCSS (LibSass), vanilla JS (IntersectionObserver, smooth scroll, modal), Roboto (already loaded via Google Fonts), Georgia serif (system font for logo).

---

## File Map

| File | Action |
|------|--------|
| `_sass/4-layouts/_portfolio.scss` | Full rewrite — MP color tokens + all component styles |
| `_layouts/portfolio.html` | Full rewrite — navbar, 5 scroll sections, modals, JS |

---

## Task 1: Rewrite `_sass/4-layouts/_portfolio.scss`

**Files:**
- Modify: `_sass/4-layouts/_portfolio.scss` (full rewrite)

- [ ] **Step 1: Replace the entire file**

```scss
// =====================================================================
// masterPortfolio-style — all classes prefixed mp-
// CSS custom properties scoped to body.mp-page
// =====================================================================

body.mp-page {
  --mp-primary:   #E65100;
  --mp-secondary: #FF8C00;
  --mp-accent:    #FFB347;
  --mp-bg:        #FFF8F0;
  --mp-surface:   #FFFFFF;
  --mp-text:      #1A1A1A;
  --mp-muted:     #777777;
  --mp-border:    #FDE8D8;

  font-family: 'Roboto', sans-serif;
  background: var(--mp-bg);
  color: var(--mp-text);
  margin: 0;
}

// ── Navbar ────────────────────────────────────────────────────────
.mp-nav {
  position: sticky;
  top: 0;
  z-index: 100;
  height: 70px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 5%;
  background: rgba(255, 248, 240, 0.95);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid var(--mp-border);
  transition: box-shadow 0.3s;

  &.scrolled {
    background: var(--mp-surface);
    box-shadow: 0 2px 16px rgba(0, 0, 0, 0.08);
  }
}

.mp-nav__logo {
  font-family: Georgia, serif;
  font-size: 1.6rem;
  font-weight: 900;
  font-style: italic;
  color: var(--mp-primary);
  cursor: pointer;
  text-decoration: none;
  transition: opacity 0.2s;

  &:hover { opacity: 0.75; }
}

.mp-nav__links {
  display: flex;
  gap: 4px;

  @media (max-width: 768px) {
    display: none;
    flex-direction: column;
    position: absolute;
    top: 70px;
    left: 0;
    right: 0;
    background: var(--mp-surface);
    padding: 12px 5%;
    border-bottom: 1px solid var(--mp-border);
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
    gap: 4px;

    &.open { display: flex; }
  }
}

.mp-nav__link {
  padding: 8px 18px;
  border-radius: 8px;
  font-size: 0.9rem;
  font-weight: 500;
  color: var(--mp-muted);
  text-decoration: none;
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
  background: none;
  border: none;
  font-family: 'Roboto', sans-serif;

  &:hover { background: var(--mp-accent); color: #fff; }
  &.active { font-weight: 700; color: var(--mp-primary); }
}

.mp-nav__hamburger {
  display: none;
  flex-direction: column;
  gap: 5px;
  cursor: pointer;
  padding: 4px;
  background: none;
  border: none;

  span {
    display: block;
    width: 22px;
    height: 2px;
    background: var(--mp-text);
    border-radius: 2px;
    transition: all 0.2s;
  }

  @media (max-width: 768px) { display: flex; }
}

// ── Hero ──────────────────────────────────────────────────────────
.mp-hero {
  min-height: calc(100vh - 70px);
  display: flex;
  align-items: center;
  padding: 60px 5%;
  background: linear-gradient(135deg, #FFF8F0 0%, #FFF0E0 60%, #FFE8CC 100%);
}

.mp-hero__inner {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 60px;
  width: 100%;
  max-width: 1100px;
  margin: 0 auto;

  @media (max-width: 768px) {
    flex-direction: column-reverse;
    text-align: center;
    gap: 32px;
  }
}

.mp-hero__text {
  flex: 1;
  animation: mpFadeUp 0.6s ease both;
}

.mp-hero__hi {
  font-size: 1rem;
  color: #999;
  letter-spacing: 2px;
  text-transform: uppercase;
  margin-bottom: 12px;
}

.mp-hero__name {
  font-size: 4rem;
  font-weight: 900;
  line-height: 1.05;
  color: var(--mp-text);
  margin-bottom: 16px;

  span { color: var(--mp-primary); }

  @media (max-width: 1100px) { font-size: 3rem; }
  @media (max-width: 768px)  { font-size: 2.2rem; }
}

.mp-hero__subtitle {
  font-size: 1.2rem;
  font-weight: 500;
  color: #555;
  margin-bottom: 16px;

  @media (max-width: 768px) { font-size: 1rem; }
}

.mp-hero__desc {
  font-size: 1rem;
  color: var(--mp-muted);
  line-height: 1.7;
  max-width: 520px;
  margin-bottom: 28px;

  @media (max-width: 768px) { max-width: 100%; }
}

.mp-social {
  display: flex;
  gap: 10px;
  margin-bottom: 28px;
  flex-wrap: wrap;

  @media (max-width: 768px) { justify-content: center; }
}

.mp-social__icon {
  width: 44px;
  height: 44px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #fff;
  text-decoration: none;
  font-size: 1rem;
  font-weight: 700;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover {
    transform: translateY(-3px);
    box-shadow: 0 6px 16px rgba(0, 0, 0, 0.2);
    color: #fff;
  }

  &--linkedin { background: #0077B5; }
  &--scholar  { background: #4285F4; font-size: 0.82rem; }
  &--github   { background: #181717; }
  &--email    { background: var(--mp-primary); }
}

.mp-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  background: var(--mp-primary);
  color: #fff;
  padding: 14px 28px;
  border-radius: 28px;
  font-size: 1rem;
  font-weight: 600;
  font-family: 'Roboto', sans-serif;
  text-decoration: none;
  border: none;
  cursor: pointer;
  box-shadow: 0 4px 16px rgba(230, 81, 0, 0.35);
  transition: background 0.2s, transform 0.2s, box-shadow 0.2s;

  &:hover {
    background: #BF360C;
    transform: translateY(-2px);
    box-shadow: 0 6px 20px rgba(230, 81, 0, 0.4);
    color: #fff;
  }
}

.mp-hero__image {
  flex-shrink: 0;
  text-align: center;
  animation: mpFadeUp 0.6s ease 0.2s both;
}

.mp-hero__photo {
  width: 240px;
  height: 240px;
  border-radius: 50%;
  object-fit: cover;
  border: 4px solid var(--mp-primary);
  box-shadow: 0 0 0 8px rgba(230, 81, 0, 0.12), 0 16px 48px rgba(230, 81, 0, 0.2);
  display: block;

  @media (max-width: 768px) { width: 160px; height: 160px; }
}

@keyframes mpFadeUp {
  from { opacity: 0; transform: translateY(20px); }
  to   { opacity: 1; transform: translateY(0); }
}

// ── Section layout ────────────────────────────────────────────────
.mp-section {
  padding: 80px 5%;

  &--white { background: var(--mp-surface); }
  &--warm  { background: var(--mp-bg); }
}

.mp-section__inner {
  max-width: 1100px;
  margin: 0 auto;
}

.mp-section__label {
  font-size: 0.75rem;
  font-weight: 700;
  letter-spacing: 2px;
  text-transform: uppercase;
  color: var(--mp-primary);
  margin-bottom: 8px;
}

.mp-section__title {
  font-size: 2.2rem;
  font-weight: 700;
  color: var(--mp-text);
  margin-bottom: 48px;

  @media (max-width: 768px) { font-size: 1.7rem; }
}

// ── Sub-section ───────────────────────────────────────────────────
.mp-subsection {
  margin-bottom: 56px;

  &:last-child { margin-bottom: 0; }
}

.mp-subsection__title {
  font-size: 1.4rem;
  font-weight: 700;
  color: var(--mp-text);
  margin-bottom: 24px;
  padding-bottom: 12px;
  border-bottom: 2px solid var(--mp-border);
}

// ── Timeline ──────────────────────────────────────────────────────
.mp-timeline {
  position: relative;
  padding-left: 28px;

  &::before {
    content: '';
    position: absolute;
    left: 6px;
    top: 8px;
    bottom: 0;
    width: 2px;
    background: linear-gradient(to bottom, var(--mp-primary), var(--mp-accent), transparent);
  }
}

.mp-timeline__item {
  position: relative;
  margin-bottom: 32px;

  &:last-child { margin-bottom: 0; }
}

.mp-timeline__dot {
  position: absolute;
  left: -28px;
  top: 6px;
  width: 14px;
  height: 14px;
  border-radius: 50%;
  background: var(--mp-primary);
  border: 2px solid var(--mp-surface);
  box-shadow: 0 0 0 3px rgba(230, 81, 0, 0.2);
}

.mp-exp-card {
  background: var(--mp-bg);
  border: 1px solid var(--mp-border);
  border-radius: 14px;
  padding: 20px 24px;
  transition: box-shadow 0.2s;

  &:hover { box-shadow: 0 4px 20px rgba(230, 81, 0, 0.12); }
}

.mp-exp-card__header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 4px;
}

.mp-exp-card__role {
  font-size: 1rem;
  font-weight: 700;
  color: var(--mp-text);
  margin: 0;
}

.mp-exp-card__date {
  font-size: 0.8rem;
  color: #aaa;
  white-space: nowrap;
}

.mp-exp-card__org {
  font-size: 0.85rem;
  color: var(--mp-primary);
  font-weight: 600;
  margin: 0 0 10px;
}

.mp-exp-card__bullets {
  margin: 0;
  padding-left: 18px;

  li {
    font-size: 0.9rem;
    color: #666;
    line-height: 1.65;
    margin-bottom: 5px;

    &:last-child { margin-bottom: 0; }
    strong { color: var(--mp-text); }
  }
}

// ── Info card (Education / Certification) ─────────────────────────
.mp-info-card {
  background: var(--mp-surface);
  border-radius: 14px;
  border-left: 4px solid var(--mp-primary);
  padding: 24px 28px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.07);
  max-width: 700px;
  margin-bottom: 24px;
  display: flex;
  gap: 20px;
  align-items: flex-start;

  &--placeholder {
    border-left-color: #ddd;
    border-style: solid;
    border-width: 1px 1px 1px 4px;
    box-shadow: none;
    opacity: 0.7;
  }
}

.mp-info-card__icon {
  width: 52px;
  height: 52px;
  border-radius: 12px;
  background: #FFF0E6;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.6rem;
  flex-shrink: 0;
}

.mp-info-card__body { flex: 1; }

.mp-info-card__title {
  font-size: 1rem;
  font-weight: 700;
  color: var(--mp-text);
  margin: 0 0 4px;
}

.mp-info-card__sub {
  font-size: 0.85rem;
  color: var(--mp-primary);
  font-weight: 600;
  margin: 0 0 6px;
}

.mp-info-card__meta {
  font-size: 0.82rem;
  color: var(--mp-muted);
  margin: 0 0 8px;
}

.mp-info-card__note {
  font-size: 0.85rem;
  color: var(--mp-text);
  margin: 0;

  strong { color: var(--mp-primary); }
}

.mp-info-card__placeholder {
  font-size: 0.85rem;
  color: #bbb;
  font-style: italic;
  margin: 0;
}

// ── Project cards ─────────────────────────────────────────────────
.mp-proj-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 24px;

  @media (max-width: 768px) { grid-template-columns: 1fr; }
}

.mp-proj-card {
  background: var(--mp-surface);
  border-radius: 14px;
  overflow: hidden;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover {
    transform: translateY(-4px);
    box-shadow: 0 8px 28px rgba(230, 81, 0, 0.14);
  }
}

.mp-proj-card__bar {
  height: 5px;
  background: linear-gradient(90deg, var(--mp-primary), var(--mp-accent));
}

.mp-proj-card__body { padding: 20px; }

.mp-proj-card__badge {
  display: inline-block;
  font-size: 0.68rem;
  font-weight: 700;
  padding: 3px 10px;
  border-radius: 10px;
  background: #FFF0E6;
  color: var(--mp-primary);
  margin-bottom: 10px;
}

.mp-proj-card__title {
  font-size: 1rem;
  font-weight: 700;
  color: var(--mp-text);
  margin: 0 0 8px;
}

.mp-proj-card__desc {
  font-size: 0.85rem;
  color: var(--mp-muted);
  line-height: 1.6;
  margin: 0 0 12px;
}

.mp-proj-card__tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  margin-bottom: 12px;
}

.mp-tag {
  font-size: 0.68rem;
  padding: 3px 10px;
  border-radius: 6px;
  background: #f5f5f5;
  color: #666;
}

.mp-proj-card__cta {
  display: block;
  font-size: 0.82rem;
  font-weight: 600;
  color: var(--mp-primary);
  margin-top: 4px;
}

// ── Detail modal ──────────────────────────────────────────────────
.mp-modal-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 500;
  align-items: center;
  justify-content: center;
  padding: 24px;

  &.open { display: flex; }
}

.mp-modal {
  background: var(--mp-surface);
  border-radius: 20px;
  padding: 36px 40px;
  max-width: 640px;
  width: 100%;
  max-height: 85vh;
  overflow-y: auto;
  box-shadow: 0 16px 64px rgba(0, 0, 0, 0.2);
  position: relative;

  @media (max-width: 600px) { padding: 28px 24px; border-radius: 14px; }
}

.mp-modal__close {
  position: absolute;
  top: 16px;
  right: 16px;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  border: none;
  background: #f5f5f5;
  color: #666;
  font-size: 14px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.2s;

  &:hover { background: #eee; }
}

.mp-modal__badge {
  display: inline-block;
  font-size: 0.7rem;
  font-weight: 700;
  padding: 3px 10px;
  border-radius: 10px;
  background: #FFF0E6;
  color: var(--mp-primary);
  margin-bottom: 10px;
}

.mp-modal__title {
  font-size: 1.4rem;
  font-weight: 700;
  color: var(--mp-text);
  margin: 0 0 12px;
  padding-right: 40px;
}

.mp-modal__desc {
  font-size: 0.95rem;
  color: var(--mp-muted);
  line-height: 1.7;
  margin: 0 0 16px;
}

.mp-modal__tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
  margin-bottom: 24px;
}

.mp-modal__section { margin-bottom: 20px; &:last-child { margin-bottom: 0; } }

.mp-modal__section-title {
  font-size: 0.8rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1px;
  color: var(--mp-primary);
  margin: 0 0 10px;
}

.mp-modal__list {
  list-style: none;
  margin: 0;
  padding: 0;

  li {
    font-size: 0.88rem;
    color: var(--mp-text);
    padding: 6px 0;
    border-bottom: 1px solid var(--mp-border);
    line-height: 1.5;

    &:last-child { border-bottom: none; }

    a { color: var(--mp-primary); text-decoration: none; font-weight: 500;
      &:hover { text-decoration: underline; } }

    em { color: var(--mp-muted); font-size: 0.82rem; }
  }
}

.mp-modal__stat-row {
  display: flex;
  gap: 16px;
  flex-wrap: wrap;
  margin-bottom: 20px;
}

.mp-modal__stat {
  background: var(--mp-bg);
  border-radius: 10px;
  padding: 10px 16px;
  text-align: center;
  flex: 1;
  min-width: 70px;
}

.mp-modal__stat-value {
  display: block;
  font-size: 1.1rem;
  font-weight: 700;
  color: var(--mp-primary);
}

.mp-modal__stat-label {
  display: block;
  font-size: 0.7rem;
  color: var(--mp-muted);
  margin-top: 2px;
}

// ── Lifestyle ─────────────────────────────────────────────────────
.mp-lifestyle__intro {
  font-size: 1rem;
  color: var(--mp-muted);
  font-style: italic;
  margin: -32px 0 36px;
}

.mp-photo-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 240px);
  gap: 16px;
  margin-bottom: 24px;

  @media (max-width: 900px) { grid-template-columns: repeat(2, 1fr); grid-template-rows: auto; }
  @media (max-width: 600px) { grid-template-columns: 1fr; }
}

.mp-photo-card {
  border-radius: 16px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  background: var(--mp-bg);
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover { transform: scale(1.02); box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15); }
  &--tall { grid-row: span 2; }
  &--wide { grid-column: span 2;
    @media (max-width: 900px) { grid-column: span 1; } }
}

.mp-photo-card__img { width: 100%; flex: 1; object-fit: cover; display: block; min-height: 0; }

.mp-photo-placeholder {
  flex: 1;
  background: linear-gradient(135deg, #FFF0E0, #FFD9B0);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  min-height: 160px;
}

.mp-placeholder-icon { font-size: 40px; }
.mp-placeholder-hint { font-size: 12px; color: #C07020; opacity: 0.8; }

.mp-photo-card__body { padding: 12px 16px; background: var(--mp-surface); flex-shrink: 0; }
.mp-photo-card__activity { font-size: 0.85rem; font-weight: 600; color: var(--mp-text); margin: 0 0 2px; }
.mp-photo-card__caption  { font-size: 0.8rem;  color: var(--mp-muted); margin: 0; }

.mp-photo-hint {
  background: var(--mp-bg);
  border: 1px dashed var(--mp-border);
  border-radius: 12px;
  padding: 12px 20px;
  font-size: 0.82rem;
  color: var(--mp-muted);
  text-align: center;

  code { background: var(--mp-surface); padding: 1px 6px; border-radius: 4px; font-size: 0.78rem; }
}
```

- [ ] **Step 2: Verify SCSS compiles with no errors**

```bash
bundle exec jekyll build 2>&1 | grep -i "error" | grep -v deprecat
echo "EXIT:$?"
```

Expected: no error lines, `EXIT:1` (grep returns 1 when no matches = no errors).

- [ ] **Step 3: Commit**

```bash
git add _sass/4-layouts/_portfolio.scss
git commit -m "feat: rewrite portfolio SCSS — masterPortfolio-style orange theme"
```

---

## Task 2: Rewrite `_layouts/portfolio.html`

**Files:**
- Modify: `_layouts/portfolio.html` (full rewrite)

- [ ] **Step 1: Replace the entire file**

```html
---
layout: portfolio-base
---

<!-- ================================================================
     NAVBAR
     ================================================================ -->
<nav class="mp-nav" id="mp-nav">
  <a class="mp-nav__logo" href="#home" id="mp-logo">Cuong.</a>

  <div class="mp-nav__links" id="mp-nav-links">
    <a class="mp-nav__link" href="#experience" data-section="experience">Experience</a>
    <a class="mp-nav__link" href="#projects"   data-section="projects">Projects</a>
    <a class="mp-nav__link" href="#lifestyle"  data-section="lifestyle">Lifestyle</a>
  </div>

  <button class="mp-nav__hamburger" id="mp-hamburger"
          aria-label="Toggle menu" aria-expanded="false">
    <span></span><span></span><span></span>
  </button>
</nav>

<!-- ================================================================
     HOME
     ================================================================ -->
<section id="home" class="mp-hero">
  <div class="mp-hero__inner">

    <div class="mp-hero__text">
      <p class="mp-hero__hi">Hi all, I am</p>
      <h1 class="mp-hero__name">Cuong Manh<br><span>Nguyen</span></h1>
      <p class="mp-hero__subtitle">AI Engineer 🚀 Speech &amp; Language · Drug Discovery</p>
      <p class="mp-hero__desc">
        Building Vietnamese LLMs, TTS and voice systems at Zalo AI (VNG) since 2020.
        Research Intern at HySonLab (University of Alabama) working on protein and
        antibody generation. Passionate about AI that improves everyday life.
      </p>

      <div class="mp-social">
        <a class="mp-social__icon mp-social__icon--linkedin"
           href="https://www.linkedin.com/in/cuongnm5"
           target="_blank" rel="noopener noreferrer" aria-label="LinkedIn">in</a>
        <a class="mp-social__icon mp-social__icon--scholar"
           href="https://scholar.google.com/citations?user=E_pcCewAAAAJ&hl=en"
           target="_blank" rel="noopener noreferrer" aria-label="Google Scholar">Gs</a>
        <a class="mp-social__icon mp-social__icon--github"
           href="https://github.com/cuongnm5"
           target="_blank" rel="noopener noreferrer" aria-label="GitHub">
          <svg width="18" height="18" viewBox="0 0 24 24" fill="white" aria-hidden="true">
            <path d="M12 0C5.37 0 0 5.37 0 12c0 5.3 3.438 9.8 8.205 11.385.6.113.82-.258.82-.577 0-.285-.01-1.04-.015-2.04-3.338.724-4.042-1.61-4.042-1.61C4.422 18.07 3.633 17.7 3.633 17.7c-1.087-.744.084-.729.084-.729 1.205.084 1.838 1.236 1.838 1.236 1.07 1.835 2.809 1.305 3.495.998.108-.776.417-1.305.76-1.605-2.665-.3-5.466-1.332-5.466-5.93 0-1.31.465-2.38 1.235-3.22-.135-.303-.54-1.523.105-3.176 0 0 1.005-.322 3.3 1.23.96-.267 1.98-.399 3-.405 1.02.006 2.04.138 3 .405 2.28-1.552 3.285-1.23 3.285-1.23.645 1.653.24 2.873.12 3.176.765.84 1.23 1.91 1.23 3.22 0 4.61-2.805 5.625-5.475 5.92.42.36.81 1.096.81 2.22 0 1.606-.015 2.896-.015 3.286 0 .315.21.69.825.57C20.565 21.795 24 17.295 24 12c0-6.63-5.37-12-12-12"/>
          </svg>
        </a>
        <a class="mp-social__icon mp-social__icon--email"
           href="mailto:s.cuongnm5@gmail.com" aria-label="Email">✉</a>
      </div>

      <a class="mp-btn" href="mailto:s.cuongnm5@gmail.com">Contact Me 👋</a>
    </div>

    <div class="mp-hero__image">
      <img class="mp-hero__photo"
           src="{{ site.baseurl }}/images/avt.jpg"
           alt="Cuong Manh Nguyen"
           width="240" height="240">
    </div>

  </div>
</section>

<!-- ================================================================
     EXPERIENCE
     ================================================================ -->
<section id="experience" class="mp-section mp-section--white">
  <div class="mp-section__inner">
    <p class="mp-section__label">What I have done so far</p>
    <h2 class="mp-section__title">Experience</h2>

    <!-- Work Experience -->
    <div class="mp-subsection">
      <h3 class="mp-subsection__title">Work Experience</h3>
      <div class="mp-timeline">

        <div class="mp-timeline__item">
          <div class="mp-timeline__dot"></div>
          <div class="mp-exp-card">
            <div class="mp-exp-card__header">
              <h4 class="mp-exp-card__role">Research Intern</h4>
              <span class="mp-exp-card__date">Apr 2025 – Present</span>
            </div>
            <p class="mp-exp-card__org">HySonLab, University of Alabama · Remote</p>
            <ul class="mp-exp-card__bullets">
              <li>Conducted research on drug discovery and repurposing with a focus on protein and antibody generation.</li>
              <li>Co-authored <strong>TeBaAb</strong>: text-based antigen-conditioned antibody redesign — submitted to NeurIPS 2025 Workshop; aiming for Oxford Journal publication.</li>
              <li>Co-authored review: <strong>Efficient Deep Learning for Medical Imaging</strong> (arXiv:2602.00910, with Prof. Truong-Son Hy).</li>
            </ul>
          </div>
        </div>

        <div class="mp-timeline__item">
          <div class="mp-timeline__dot"></div>
          <div class="mp-exp-card">
            <div class="mp-exp-card__header">
              <h4 class="mp-exp-card__role">AI Engineer</h4>
              <span class="mp-exp-card__date">Feb 2020 – Present</span>
            </div>
            <p class="mp-exp-card__org">Zalo AI, VNG Corporation · Ho Chi Minh City</p>
            <ul class="mp-exp-card__bullets">
              <li><strong>KiLM</strong> — Vietnamese-optimized LLM; KiLM-13b-v24.7.1 scored 66.07, closely trailing Llama-3-70B at 66.44.</li>
              <li>Refactored AI creative card service Python → Golang, increasing concurrent throughput by <strong>500%</strong>. Product reached 3.7M cards, 1.7M users in one campaign.</li>
              <li>Built RAG system for KiLM: Milvus (vector search) + Elasticsearch (FAQ) + MinIO (storage).</li>
              <li><strong>Kiki Auto</strong> — Vietnamese in-car voice assistant; TTS research (Tacotron2, FastSpeech, VITS); <strong>80% error rate reduction</strong> after service refactor.</li>
              <li>Research on Vietnamese TTS accepted at PRICAI 2021 (Q2) — recognized internationally by Znews.</li>
            </ul>
          </div>
        </div>

        <div class="mp-timeline__item">
          <div class="mp-timeline__dot" style="background:var(--mp-accent);"></div>
          <div class="mp-exp-card">
            <div class="mp-exp-card__header">
              <h4 class="mp-exp-card__role">President, Programming PTIT Club (ProPTIT)</h4>
              <span class="mp-exp-card__date">Dec 2019 – Dec 2020</span>
            </div>
            <p class="mp-exp-card__org">Post &amp; Telecommunication Institute of Technology · Hanoi</p>
            <ul class="mp-exp-card__bullets">
              <li>Led university programming club; organized AI and programming courses (C, C++, Java, Data Structures, AI).</li>
              <li>Managed academic competitions, events, communications, finances, and human resources.</li>
            </ul>
          </div>
        </div>

      </div>
    </div>

    <!-- Education -->
    <div class="mp-subsection">
      <h3 class="mp-subsection__title">Education</h3>
      <div class="mp-info-card">
        <div class="mp-info-card__icon">🎓</div>
        <div class="mp-info-card__body">
          <p class="mp-info-card__title">Bachelor's in Information Technology</p>
          <p class="mp-info-card__sub">Post &amp; Telecommunication Institute of Technology (PTIT) · Hanoi, Vietnam</p>
          <p class="mp-info-card__meta">Aug 2017 – Dec 2021 &nbsp;·&nbsp; GPA: 3.16 / 4.0 (7.93 / 10)</p>
          <p class="mp-info-card__note">Thesis: Research to improve recommendation system quality — Score: <strong>10/10</strong> · Top 0.01%</p>
        </div>
      </div>
    </div>

    <!-- Certifications -->
    <div class="mp-subsection">
      <h3 class="mp-subsection__title">Certifications</h3>
      <div class="mp-info-card mp-info-card--placeholder">
        <div class="mp-info-card__icon">📜</div>
        <div class="mp-info-card__body">
          <p class="mp-info-card__title">Certifications</p>
          <p class="mp-info-card__placeholder">Professional credentials and online course certifications coming soon.</p>
        </div>
      </div>
    </div>

  </div>
</section>

<!-- ================================================================
     PROJECTS
     ================================================================ -->
<section id="projects" class="mp-section mp-section--warm">
  <div class="mp-section__inner">
    <p class="mp-section__label">What I have built</p>
    <h2 class="mp-section__title">Projects</h2>

    <div class="mp-proj-grid">

      <div class="mp-proj-card" role="button" tabindex="0" data-modal="tts">
        <div class="mp-proj-card__bar"></div>
        <div class="mp-proj-card__body">
          <span class="mp-proj-card__badge">🥇 VLSP 2020 · 1st Prize</span>
          <h3 class="mp-proj-card__title">Vietnamese Text-to-Speech</h3>
          <p class="mp-proj-card__desc">End-to-end Vietnamese TTS system (Tacotron-2 + HiFiGAN + WaveGlow). MOS 3.77/4.22 — best result at VLSP 2020. Research published at PRICAI 2021 (Q2). Featured in Znews.</p>
          <div class="mp-proj-card__tags">
            <span class="mp-tag">TTS</span>
            <span class="mp-tag">Deep Learning</span>
            <span class="mp-tag">Speech Synthesis</span>
            <span class="mp-tag">Python</span>
          </div>
          <span class="mp-proj-card__cta">View details →</span>
        </div>
      </div>

      <div class="mp-proj-card" role="button" tabindex="0" data-modal="aicard">
        <div class="mp-proj-card__bar" style="background:linear-gradient(90deg,#FF8C00,#FFD700);"></div>
        <div class="mp-proj-card__body">
          <span class="mp-proj-card__badge" style="background:#FFF8E6;color:#FF8C00;">Zalo AI · 3.7M Cards</span>
          <h3 class="mp-proj-card__title">AI Greeting Card — Zalo</h3>
          <p class="mp-proj-card__desc">AI card generator on Zalo — personalized poetry, lyrics, rap via KiLM. Refactored Python → Golang for 500% throughput gain. 3.7M cards, 1.7M users in one Women's Day campaign.</p>
          <div class="mp-proj-card__tags">
            <span class="mp-tag">Golang</span>
            <span class="mp-tag">Python</span>
            <span class="mp-tag">LLM</span>
            <span class="mp-tag">Production</span>
          </div>
          <span class="mp-proj-card__cta">View details →</span>
        </div>
      </div>

      <div class="mp-proj-card" role="button" tabindex="0" data-modal="cosmic">
        <div class="mp-proj-card__bar" style="background:linear-gradient(90deg,#7B2FBE,#E65100);"></div>
        <div class="mp-proj-card__body">
          <span class="mp-proj-card__badge" style="background:#F3E8FF;color:#7B2FBE;">Side Project · Live 🌙</span>
          <h3 class="mp-proj-card__title">Cosmic Corner</h3>
          <p class="mp-proj-card__desc">Vietnamese esoteric platform — AI-powered tarot readings, tea leaf divination, horoscope &amp; natal charts, 78-card flashcard system. Freemium with e-commerce shop.</p>
          <div class="mp-proj-card__tags">
            <span class="mp-tag">AI</span>
            <span class="mp-tag">Full Stack</span>
            <span class="mp-tag">Vietnamese</span>
            <span class="mp-tag">Side Project</span>
          </div>
          <span class="mp-proj-card__cta">View details →</span>
        </div>
      </div>

      <div class="mp-proj-card" role="button" tabindex="0" data-modal="medical">
        <div class="mp-proj-card__bar"></div>
        <div class="mp-proj-card__body">
          <span class="mp-proj-card__badge">Research · HySonLab</span>
          <h3 class="mp-proj-card__title">AI for Medicine &amp; Drug Discovery</h3>
          <p class="mp-proj-card__desc">Antibody redesign (TeBaAb, NeurIPS 2025 Workshop) and review of efficient deep learning for clinical medical imaging deployment.</p>
          <div class="mp-proj-card__tags">
            <span class="mp-tag">Drug Discovery</span>
            <span class="mp-tag">Medical AI</span>
            <span class="mp-tag">Deep Learning</span>
            <span class="mp-tag">Research</span>
          </div>
          <span class="mp-proj-card__cta">View details →</span>
        </div>
      </div>

    </div>
  </div>
</section>

<!-- ================================================================
     LIFESTYLE
     ================================================================ -->
<section id="lifestyle" class="mp-section mp-section--white">
  <div class="mp-section__inner">
    <p class="mp-section__label">Life outside code</p>
    <h2 class="mp-section__title">Lifestyle</h2>
    <p class="mp-lifestyle__intro">When I'm not training models, I'm training myself.</p>

    <div class="mp-photo-grid">

      <div class="mp-photo-card mp-photo-card--tall">
        <img class="mp-photo-card__img"
             src="{{ site.baseurl }}/images/run_wp.png"
             alt="Running and marathon training">
        <div class="mp-photo-card__body">
          <p class="mp-photo-card__activity">🏃 Running</p>
          <p class="mp-photo-card__caption">Marathon training &amp; road races</p>
        </div>
      </div>

      <!-- PLACEHOLDER: swap div.mp-photo-placeholder with <img class="mp-photo-card__img" src="{{ site.baseurl }}/images/trekking.jpg" alt="Trekking"> -->
      <div class="mp-photo-card">
        <div class="mp-photo-placeholder">
          <span class="mp-placeholder-icon">🏔</span>
          <span class="mp-placeholder-hint">Add trekking photo</span>
        </div>
        <div class="mp-photo-card__body">
          <p class="mp-photo-card__activity">🏔 Trekking</p>
          <p class="mp-photo-card__caption">Into the mountains</p>
        </div>
      </div>

      <!-- PLACEHOLDER: swap with <img class="mp-photo-card__img" src="{{ site.baseurl }}/images/badminton.jpg" alt="Badminton"> -->
      <div class="mp-photo-card">
        <div class="mp-photo-placeholder">
          <span class="mp-placeholder-icon">🏸</span>
          <span class="mp-placeholder-hint">Add badminton photo</span>
        </div>
        <div class="mp-photo-card__body">
          <p class="mp-photo-card__activity">🏸 Badminton</p>
          <p class="mp-photo-card__caption">Weekly smash sessions</p>
        </div>
      </div>

      <div class="mp-photo-card mp-photo-card--wide">
        <img class="mp-photo-card__img"
             src="{{ site.baseurl }}/images/mc.jpg"
             alt="Marathon race highlight">
        <div class="mp-photo-card__body">
          <p class="mp-photo-card__activity">🏅 Marathon</p>
          <p class="mp-photo-card__caption">Race day highlights</p>
        </div>
      </div>

      <!-- PLACEHOLDER: swap with your photo -->
      <div class="mp-photo-card">
        <div class="mp-photo-placeholder">
          <span class="mp-placeholder-icon">🌿</span>
          <span class="mp-placeholder-hint">Add more photos</span>
        </div>
        <div class="mp-photo-card__body">
          <p class="mp-photo-card__activity">🌿 More Adventures</p>
          <p class="mp-photo-card__caption">Life in motion</p>
        </div>
      </div>

    </div>

    <p class="mp-photo-hint">
      📸 Drop photos into <code>/images/</code> and swap the placeholder
      <code>&lt;div&gt;</code> blocks with
      <code>&lt;img class="mp-photo-card__img"&gt;</code>
    </p>

  </div>
</section>

<!-- ================================================================
     PROJECT DETAIL MODAL
     ================================================================ -->
<div class="mp-modal-overlay" id="mp-modal-overlay" aria-hidden="true">
  <div class="mp-modal" role="dialog" aria-modal="true" aria-labelledby="mp-modal-title">
    <button class="mp-modal__close" id="mp-modal-close" aria-label="Close">✕</button>
    <span class="mp-modal__badge" id="mp-modal-badge"></span>
    <h2 class="mp-modal__title" id="mp-modal-title"></h2>
    <p class="mp-modal__desc"  id="mp-modal-desc"></p>
    <div class="mp-modal__tags" id="mp-modal-tags"></div>
    <div id="mp-modal-body"></div>
  </div>
</div>

<!-- ================================================================
     JAVASCRIPT
     ================================================================ -->
<script>
(function () {
  'use strict';

  // ── Project data ─────────────────────────────────────────────────
  var PROJECTS = {
    tts: {
      badge: '🥇 VLSP 2020 · 1st Prize',
      title: 'Vietnamese Text-to-Speech',
      desc: 'End-to-end Vietnamese TTS pipeline — Tacotron-2 + HiFiGAN vocoder + WaveGlow denoiser. Achieved MOS 3.77/4.22, the best result at VLSP 2020. Research also applied to Vietnamese-English code-switching synthesis and multi-task text normalization.',
      tags: ['TTS', 'Deep Learning', 'Speech Synthesis', 'Python', 'Signal Processing'],
      sections: [
        { title: '📄 Publications', items: [
          { text: 'Development of Smartcall Vietnamese TTS for VLSP 2020', sub: 'VLSP 2020 (ACL workshop)', link: null },
          { text: 'Vietnamese-English Code-Switching Speech Synthesis', sub: 'PRICAI 2021 · Springer LNAI · Q2', link: null },
          { text: 'Multi-Task Text Normalization for Speech Synthesis', sub: 'PRICAI 2021 · Springer LNAI · Q2', link: null }
        ]},
        { title: '📰 Press', items: [
          { text: 'Vietnamese AI Research Recognized at International Conference', sub: 'Znews — PRICAI 2021 coverage', link: 'https://znews.vn/nghien-cuu-ve-ai-cua-nhom-ky-su-viet-duoc-cong-nhan-cap-quoc-te-post1281985.html' }
        ]}
      ]
    },
    aicard: {
      badge: 'Zalo AI · 3.7M Cards Created',
      title: 'AI Greeting Card — Zalo',
      desc: 'AI-powered greeting card generator on Zalo — creates personalized messages as poetry, song lyrics, and rap using KiLM (Vietnamese LLM). Refactored backend service from Python to Golang, increasing concurrent user throughput by 500%.',
      tags: ['Golang', 'Python', 'LLM', 'Production', 'KiLM'],
      stats: [
        { value: '3.7M', label: 'Cards Created' },
        { value: '1.7M', label: 'Users' },
        { value: '4×',   label: 'YoY Growth' },
        { value: '500%', label: 'Throughput Gain' }
      ],
      sections: [
        { title: '📰 Press', items: [
          { text: 'Nearly 4 Million AI Cards Created on Zalo for Women\'s Day', sub: 'Znews', link: 'https://znews.vn/gan-4-trieu-tam-thiep-ai-duoc-tao-ra-tren-zalo-trong-dip-83-post1539259.html' },
          { text: 'LinkedIn — Campaign highlight post', sub: 'LinkedIn', link: 'https://www.linkedin.com/feed/update/urn:li:ugcPost:7258038391957528576/' }
        ]}
      ]
    },
    cosmic: {
      badge: 'Side Project · Live 🌙',
      title: 'Cosmic Corner',
      desc: 'Personal side project — a Vietnamese esoteric learning platform combining traditional spirituality with modern AI. Built end-to-end: AI interpretation engine, horoscope & natal chart generator, 78-card flashcard library, quizzes, and an e-commerce shop.',
      tags: ['AI', 'Full Stack', 'Vietnamese', 'Side Project', 'Freemium'],
      sections: [
        { title: '🔗 Live Site', items: [
          { text: 'cosmic-corner.io.vn', sub: 'Tarot · Tea Leaf · Horoscope · Shop', link: 'https://cosmic-corner.io.vn/' }
        ]},
        { title: '✨ Features', items: [
          { text: 'AI-powered tarot readings with contextual interpretation', sub: null, link: null },
          { text: 'Tea leaf divination using Eastern mystical traditions',    sub: null, link: null },
          { text: 'Horoscope & natal chart generation',                       sub: null, link: null },
          { text: '78-card flashcard system with quizzes',                   sub: null, link: null },
          { text: 'E-commerce: decks, books, crystals & accessories',        sub: null, link: null }
        ]}
      ]
    },
    medical: {
      badge: 'Research · HySonLab',
      title: 'AI for Medicine & Drug Discovery',
      desc: 'Research at HySonLab (University of Alabama at Birmingham) on protein and antibody generation for drug discovery, alongside a comprehensive review of efficient deep learning architectures for clinical AI deployment.',
      tags: ['Drug Discovery', 'Medical AI', 'Deep Learning', 'Bioinformatics', 'Research'],
      sections: [
        { title: '📄 Publications', items: [
          { text: 'TeBaAb: Text-based antigen-conditioned antibody redesign', sub: 'NeurIPS 2025 Workshop · Submitted · Oxford Journal target', link: null },
          { text: 'Efficient Deep Learning for Medical Imaging: Bridging the Gap Between High-Performance AI and Clinical Deployment', sub: 'arXiv:2602.00910 (Jan 2026)', link: 'https://arxiv.org/abs/2602.00910' }
        ]},
        { title: '🔬 Lab', items: [
          { text: 'HySonLab — University of Alabama at Birmingham', sub: 'Supervisor: Prof. Truong-Son Hy', link: null }
        ]}
      ]
    }
  };

  // ── Modal helpers ─────────────────────────────────────────────────
  var overlay  = document.getElementById('mp-modal-overlay');
  var closeBtn = document.getElementById('mp-modal-close');

  function esc(str) {
    return str.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;');
  }

  function openModal(id) {
    var p = PROJECTS[id]; if (!p) return;
    document.getElementById('mp-modal-badge').textContent = p.badge;
    document.getElementById('mp-modal-title').textContent = p.title;
    document.getElementById('mp-modal-desc').textContent  = p.desc;

    document.getElementById('mp-modal-tags').innerHTML =
      p.tags.map(function(t){ return '<span class="mp-tag">'+esc(t)+'</span>'; }).join('');

    var html = '';
    if (p.stats) {
      html += '<div class="mp-modal__stat-row">';
      p.stats.forEach(function(s){
        html += '<div class="mp-modal__stat">'
              + '<span class="mp-modal__stat-value">'+esc(s.value)+'</span>'
              + '<span class="mp-modal__stat-label">'+esc(s.label)+'</span>'
              + '</div>';
      });
      html += '</div>';
    }
    (p.sections||[]).forEach(function(sec){
      html += '<div class="mp-modal__section"><p class="mp-modal__section-title">'+esc(sec.title)+'</p><ul class="mp-modal__list">';
      sec.items.forEach(function(item){
        html += '<li>';
        html += item.link
          ? '<a href="'+esc(item.link)+'" target="_blank" rel="noopener noreferrer">'+esc(item.text)+'</a>'
          : esc(item.text);
        if (item.sub) html += ' <em>— '+esc(item.sub)+'</em>';
        html += '</li>';
      });
      html += '</ul></div>';
    });
    document.getElementById('mp-modal-body').innerHTML = html;

    overlay.classList.add('open');
    overlay.removeAttribute('aria-hidden');
    document.body.style.overflow = 'hidden';
    closeBtn.focus();
  }

  function closeModal() {
    overlay.classList.remove('open');
    overlay.setAttribute('aria-hidden','true');
    document.body.style.overflow = '';
  }

  document.querySelectorAll('.mp-proj-card').forEach(function(card){
    card.addEventListener('click', function(){ openModal(this.dataset.modal); });
    card.addEventListener('keydown', function(e){
      if (e.key==='Enter'||e.key===' '){ e.preventDefault(); openModal(this.dataset.modal); }
    });
  });
  closeBtn.addEventListener('click', closeModal);
  overlay.addEventListener('click', function(e){ if(e.target===overlay) closeModal(); });

  // ── Navbar scroll shadow ──────────────────────────────────────────
  var nav = document.getElementById('mp-nav');
  window.addEventListener('scroll', function(){
    nav.classList.toggle('scrolled', window.scrollY > 20);
  }, { passive: true });

  // ── Smooth scroll ─────────────────────────────────────────────────
  function scrollTo(id){ var el=document.getElementById(id); if(el) el.scrollIntoView({behavior:'smooth'}); }

  document.getElementById('mp-logo').addEventListener('click', function(e){
    e.preventDefault(); scrollTo('home');
  });

  var navLinks   = document.getElementById('mp-nav-links');
  var hamburger  = document.getElementById('mp-hamburger');

  document.querySelectorAll('.mp-nav__link').forEach(function(link){
    link.addEventListener('click', function(e){
      e.preventDefault();
      scrollTo(this.dataset.section);
      navLinks.classList.remove('open');
      hamburger.setAttribute('aria-expanded','false');
    });
  });

  // ── IntersectionObserver — active link ────────────────────────────
  var sectionEls = Array.from(document.querySelectorAll('#experience,#projects,#lifestyle'));
  var linkEls    = Array.from(document.querySelectorAll('.mp-nav__link'));

  var io = new IntersectionObserver(function(entries){
    entries.forEach(function(entry){
      if (entry.isIntersecting) {
        var id = entry.target.id;
        linkEls.forEach(function(l){ l.classList.toggle('active', l.dataset.section===id); });
      }
    });
  }, { rootMargin: '-40% 0px -55% 0px' });

  sectionEls.forEach(function(s){ io.observe(s); });

  // ── Mobile hamburger ──────────────────────────────────────────────
  hamburger.addEventListener('click', function(){
    var open = navLinks.classList.toggle('open');
    hamburger.setAttribute('aria-expanded', String(open));
  });

  // ── Escape key ────────────────────────────────────────────────────
  document.addEventListener('keydown', function(e){
    if (e.key==='Escape'){
      if (overlay.classList.contains('open'))    closeModal();
      if (navLinks.classList.contains('open')){ navLinks.classList.remove('open'); hamburger.setAttribute('aria-expanded','false'); }
    }
  });

}());
</script>
```

- [ ] **Step 2: Verify build**

```bash
bundle exec jekyll build 2>&1 | grep -i "error" | grep -v deprecat
echo "EXIT:$?"
```

Expected: no error lines, `EXIT:1`.

- [ ] **Step 3: Spot-check built output**

```bash
grep -c "mp-nav"      _site/index.html
grep -c "mp-hero"     _site/index.html
grep -c "mp-timeline" _site/index.html
grep -c "mp-proj-card" _site/index.html
grep -c "mp-photo-grid" _site/index.html
grep -c "cosmic-corner" _site/index.html
grep -c "2602.00910"   _site/index.html
```

Expected: all return 1 or higher.

- [ ] **Step 4: Commit**

```bash
git add _layouts/portfolio.html
git commit -m "feat: rewrite portfolio — masterPortfolio-style scroll sections, hero, experience, 4 projects, lifestyle"
```

---

## Task 3: Build verification & screenshot

**Files:** none (read-only verification)

- [ ] **Step 1: Clean build**

```bash
bundle exec jekyll clean && bundle exec jekyll build 2>&1 | tail -5
```

Expected: ends with `done in X seconds`, no errors.

- [ ] **Step 2: Serve and screenshot**

```bash
pkill -f "jekyll serve" 2>/dev/null; sleep 1
bundle exec jekyll serve --port 4000 --baseurl "" --no-watch 2>&1 &
sleep 5
curl -s -o /dev/null -w "%{http_code}" http://localhost:4000/portfolio/
```

Expected: `200`.

- [ ] **Step 3: Take screenshots**

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --screenshot=/tmp/mp-home.png \
  --window-size=1440,900 --no-sandbox --disable-gpu \
  "http://localhost:4000/portfolio/" 2>/dev/null && echo "ok"
```

- [ ] **Step 4: Final commit**

```bash
git add -A
git status
# only commit if there are unexpected uncommitted changes
```

---

## Spec Coverage

| Spec requirement | Task |
|-----------------|------|
| MP color system (8 tokens) | Task 1 — `body.mp-page` CSS vars |
| Navbar: logo → home, Experience/Projects/Lifestyle | Task 2 — `.mp-nav` |
| Navbar scroll shadow + active link via IO | Task 2 — JS |
| Mobile hamburger max-height menu | Task 1 CSS + Task 2 JS |
| Hero: warm gradient bg, large name, "Nguyen" orange | Task 1 + 2 |
| Hero: 44px social icon circles (4 platforms) | Task 1 + 2 |
| Hero: circular avt.jpg with orange ring | Task 1 + 2 |
| Hero: fade-in animation | Task 1 `@keyframes mpFadeUp` |
| Experience: timeline with 3 entries | Task 2 — `.mp-timeline` |
| Experience: Education card (PTIT, 10/10 thesis) | Task 2 |
| Experience: Certifications placeholder | Task 2 |
| Projects: 4 cards with gradient top bar | Task 2 |
| Projects: modal with stats, publications, press, links | Task 2 — JS `PROJECTS` data |
| Cosmic Corner live link | Task 2 — `cosmic` modal |
| arXiv:2602.00910 link | Task 2 — `medical` modal |
| Lifestyle photo grid (run_wp.png, mc.jpg, 3 placeholders) | Task 2 |
| Responsive: stacked hero, 1-col projects on mobile | Task 1 `@media` |
