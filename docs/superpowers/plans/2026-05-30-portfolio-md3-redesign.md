# Portfolio MD3 Redesign Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign the Jekyll portfolio page (`/portfolio/`) with Material Design 3 — MD3 Top App Bar, primary tab bar, and six panels: Home, Experience, Projects, Publications, Awards, Lifestyle.

**Architecture:** A new `_layouts/portfolio-base.html` provides a minimal HTML shell (no blog header). `_layouts/portfolio.html` extends it and contains the complete MD3 UI. All CSS is rewritten in `_sass/4-layouts/_portfolio.scss` using MD3 CSS custom properties scoped to `.pf-page`. No global files (`header.html`, `default.html`, blog SCSS) are touched.

**Tech Stack:** Jekyll (Liquid), SCSS, vanilla JS, Google Fonts (Roboto via `<link>`)

---

## File Map

| File | Action | Responsibility |
|------|--------|----------------|
| `_layouts/portfolio-base.html` | Create | Minimal HTML shell — head, Roboto font, body.pf-page, JS includes. No blog header. |
| `_sass/4-layouts/_portfolio.scss` | Full rewrite | All MD3 CSS: color tokens, App Bar, Tab Bar, panel layout, all component styles, responsive breakpoints, mobile nav. |
| `_layouts/portfolio.html` | Full rewrite | Complete MD3 UI: App Bar, mobile nav drawer, Tab Bar, and all 6 panel contents + tab-switching JS. |

---

## Task 1: Create `_layouts/portfolio-base.html`

**Files:**
- Create: `_layouts/portfolio-base.html`

- [ ] **Step 1: Create the file**

```html
---
---
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{% if page.title %}{{ page.title }} | {% endif %}{{ site.data.settings.title }}</title>
  <meta name="description" content="{{ site.data.settings.description }}">
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap" rel="stylesheet">
  {% include head.html %}
</head>

<body class="pf-page">
  <main class="pf-main" aria-label="Portfolio">
    {{ content }}
  </main>
  {% include javascripts.html %}
</body>

</html>
```

- [ ] **Step 2: Verify Jekyll can build with the new layout**

Run: `bundle exec jekyll build 2>&1 | tail -20`

Expected: output ends with `done in X seconds` and no `Error` lines.

- [ ] **Step 3: Commit**

```bash
git add _layouts/portfolio-base.html
git commit -m "feat: add portfolio-base layout (MD3 shell without blog header)"
```

---

## Task 2: Rewrite `_sass/4-layouts/_portfolio.scss`

**Files:**
- Modify: `_sass/4-layouts/_portfolio.scss` (full rewrite)

- [ ] **Step 1: Replace the entire file with the MD3 stylesheet**

```scss
// =====================================================================
// Portfolio MD3 — all classes prefixed pf- to avoid global leakage
// CSS custom properties scoped to body.pf-page
// =====================================================================

.pf-page {
  // ── MD3 Color Tokens ──────────────────────────────────────────────
  --md-primary: #006874;
  --md-on-primary: #ffffff;
  --md-primary-container: #97f0ff;
  --md-on-primary-container: #001f24;
  --md-secondary: #4a6267;
  --md-on-secondary: #ffffff;
  --md-secondary-container: #cde7ec;
  --md-on-secondary-container: #051f23;
  --md-surface: #fafdfd;
  --md-surface-container-lowest: #ffffff;
  --md-surface-container-low: #f0fafb;
  --md-surface-container: #e4f3f5;
  --md-surface-container-high: #deedf0;
  --md-on-surface: #161d1e;
  --md-on-surface-variant: #3f484a;
  --md-outline: #6f797a;
  --md-outline-variant: #bec8ca;

  font-family: 'Roboto', sans-serif;
  background: var(--md-surface);
  color: var(--md-on-surface);
  margin: 0;
  min-height: 100vh;
}

.pf-main {
  min-height: 100vh;
}

// ── Top App Bar ────────────────────────────────────────────────────
.pf-app-bar {
  position: sticky;
  top: 0;
  z-index: 100;
  height: 64px;
  background: var(--md-surface-container-low);
  border-bottom: 1px solid var(--md-outline-variant);
  display: flex;
  align-items: center;
  padding: 0 24px;
  gap: 8px;
  transition: box-shadow 0.2s;

  &.scrolled {
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.12);
  }

  @media (max-width: 768px) {
    height: 56px;
    padding: 0 16px;
  }
}

.pf-app-bar__brand {
  display: flex;
  align-items: center;
  gap: 12px;
  text-decoration: none;
  flex: 1;
  min-width: 0;
}

.pf-app-bar__monogram {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: var(--md-primary);
  color: var(--md-on-primary);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 16px;
  flex-shrink: 0;
}

.pf-app-bar__name {
  font-size: 18px;
  font-weight: 500;
  color: var(--md-on-surface);
  letter-spacing: 0.15px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;

  @media (max-width: 480px) {
    display: none;
  }
}

.pf-app-bar__nav {
  display: flex;
  align-items: center;
  gap: 4px;
  flex-shrink: 0;

  @media (max-width: 768px) {
    display: none;
  }
}

.pf-app-bar__nav-link {
  padding: 8px 16px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 500;
  color: var(--md-on-surface-variant);
  text-decoration: none;
  transition: background 0.2s, color 0.2s;
  white-space: nowrap;

  &:hover {
    background: var(--md-surface-container);
    color: var(--md-on-surface);
  }

  &.active {
    background: var(--md-secondary-container);
    color: var(--md-on-secondary-container);
  }
}

.pf-icon-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: none;
  background: transparent;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--md-on-surface-variant);
  font-size: 18px;
  text-decoration: none;
  flex-shrink: 0;
  transition: background 0.2s;

  &:hover {
    background: var(--md-surface-container);
    color: var(--md-on-surface);
  }
}

.pf-menu-btn {
  display: none;

  @media (max-width: 768px) {
    display: flex;
  }
}

// ── Mobile Nav Drawer ─────────────────────────────────────────────
.pf-nav-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.4);
  z-index: 200;

  &.open {
    display: block;
  }
}

.pf-nav-drawer {
  position: fixed;
  top: 0;
  left: -280px;
  width: 280px;
  height: 100%;
  background: var(--md-surface-container-low);
  z-index: 201;
  transition: left 0.3s ease;
  padding: 24px 0;
  box-shadow: 4px 0 16px rgba(0, 0, 0, 0.12);
  overflow-y: auto;

  &.open {
    left: 0;
  }
}

.pf-nav-drawer__header {
  padding: 0 24px 20px;
  border-bottom: 1px solid var(--md-outline-variant);
  margin-bottom: 12px;
}

.pf-nav-drawer__name {
  font-size: 18px;
  font-weight: 500;
  color: var(--md-on-surface);
  margin: 0 0 4px;
}

.pf-nav-drawer__sub {
  font-size: 13px;
  color: var(--md-on-surface-variant);
  margin: 0;
}

.pf-nav-drawer__link {
  display: flex;
  align-items: center;
  padding: 12px 24px;
  font-size: 15px;
  font-weight: 500;
  color: var(--md-on-surface);
  text-decoration: none;
  border-radius: 0 28px 28px 0;
  margin-right: 12px;
  transition: background 0.2s;

  &:hover {
    background: var(--md-surface-container);
  }
}

// ── Tab Bar ────────────────────────────────────────────────────────
.pf-tab-bar {
  position: sticky;
  top: 64px;
  z-index: 99;
  background: var(--md-surface);
  border-bottom: 2px solid var(--md-outline-variant);
  display: flex;
  overflow-x: auto;
  scrollbar-width: none;

  &::-webkit-scrollbar {
    display: none;
  }

  @media (max-width: 768px) {
    top: 56px;
  }
}

.pf-tab {
  display: inline-flex;
  align-items: center;
  padding: 0 24px;
  height: 48px;
  border: none;
  border-bottom: 3px solid transparent;
  background: transparent;
  cursor: pointer;
  font-family: 'Roboto', sans-serif;
  font-size: 14px;
  font-weight: 500;
  color: var(--md-on-surface-variant);
  white-space: nowrap;
  margin-bottom: -2px;
  transition: color 0.2s, border-color 0.2s, background 0.2s;

  &:hover {
    color: var(--md-primary);
    background: var(--md-surface-container-low);
  }

  &.active {
    color: var(--md-primary);
    border-bottom-color: var(--md-primary);
  }
}

// ── Panels Container ───────────────────────────────────────────────
.pf-panels {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 32px;

  @media (max-width: 900px) {
    padding: 0 24px;
  }

  @media (max-width: 600px) {
    padding: 0 16px;
  }
}

.pf-panel {
  display: none;
  padding: 40px 0;

  &.active {
    display: block;
  }
}

// ── Section Typography ─────────────────────────────────────────────
.pf-section-header {
  margin-bottom: 32px;

  h2 {
    font-size: 28px;
    font-weight: 400;
    color: var(--md-on-surface);
    margin: 0 0 8px;
    letter-spacing: -0.25px;
  }
}

.pf-section-label {
  font-size: 12px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  color: var(--md-on-surface-variant);
  margin: 0 0 16px;
}

// ── Home: Hero ─────────────────────────────────────────────────────
.pf-home-hero {
  background: var(--md-surface-container);
  border-radius: 28px;
  padding: 48px;
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 40px;
  align-items: center;
  margin-bottom: 40px;

  @media (max-width: 768px) {
    grid-template-columns: 1fr;
    text-align: center;
    padding: 32px 24px;
    gap: 24px;
  }
}

.pf-avatar {
  width: 128px;
  height: 128px;
  border-radius: 50%;
  border: 3px solid var(--md-primary);
  object-fit: cover;
  display: block;

  @media (max-width: 768px) {
    margin: 0 auto;
  }
}

.pf-hero-name {
  font-size: 36px;
  font-weight: 400;
  color: var(--md-on-surface);
  margin: 0 0 8px;
  letter-spacing: -0.25px;
  line-height: 1.2;

  @media (max-width: 768px) {
    font-size: 26px;
  }
}

.pf-hero-title {
  font-size: 16px;
  color: var(--md-primary);
  font-weight: 500;
  margin: 0 0 16px;

  @media (max-width: 768px) {
    font-size: 14px;
  }
}

.pf-hero-bio {
  font-size: 15px;
  color: var(--md-on-surface-variant);
  line-height: 1.7;
  margin: 0 0 24px;
  max-width: 580px;
}

.pf-hero-links {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;

  @media (max-width: 768px) {
    justify-content: center;
  }
}

// ── Buttons ────────────────────────────────────────────────────────
.pf-btn {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  padding: 10px 20px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 500;
  font-family: 'Roboto', sans-serif;
  text-decoration: none;
  border: none;
  cursor: pointer;
  transition: box-shadow 0.2s, background 0.2s;
  white-space: nowrap;

  &--filled {
    background: var(--md-primary);
    color: var(--md-on-primary);

    &:hover {
      box-shadow: 0 2px 8px rgba(0, 104, 116, 0.4);
      color: var(--md-on-primary);
    }
  }

  &--tonal {
    background: var(--md-secondary-container);
    color: var(--md-on-secondary-container);

    &:hover {
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.12);
      color: var(--md-on-secondary-container);
    }
  }

  &--outlined {
    background: transparent;
    color: var(--md-primary);
    border: 1px solid var(--md-outline);

    &:hover {
      background: var(--md-surface-container);
      color: var(--md-primary);
    }
  }
}

// ── Skills Chips ───────────────────────────────────────────────────
.pf-chips {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 40px;
}

.pf-chip {
  display: inline-flex;
  align-items: center;
  padding: 6px 16px;
  border-radius: 8px;
  font-size: 13px;
  font-weight: 500;
  background: var(--md-surface-container);
  color: var(--md-on-surface-variant);
  border: 1px solid var(--md-outline-variant);
}

// ── Stats Cards ────────────────────────────────────────────────────
.pf-stats {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 16px;
  margin-bottom: 40px;

  @media (max-width: 900px) {
    grid-template-columns: repeat(2, 1fr);
  }
}

.pf-stat-card {
  background: var(--md-surface-container);
  border-radius: 16px;
  padding: 20px;
  text-align: center;
}

.pf-stat-icon {
  font-size: 28px;
  margin-bottom: 8px;
  display: block;
}

.pf-stat-value {
  font-size: 22px;
  font-weight: 700;
  color: var(--md-primary);
  display: block;
  margin-bottom: 4px;
}

.pf-stat-label {
  font-size: 12px;
  color: var(--md-on-surface-variant);
  margin: 0;
}

// ── Timeline (Experience) ──────────────────────────────────────────
.pf-timeline {
  list-style: none;
  margin: 0;
  padding: 0;
}

.pf-timeline-item {
  display: flex;
  gap: 20px;
  margin-bottom: 24px;

  &:last-child .pf-timeline-track::after {
    display: none;
  }
}

.pf-timeline-track {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex-shrink: 0;
  padding-top: 6px;

  &::before {
    content: '';
    width: 14px;
    height: 14px;
    border-radius: 50%;
    background: var(--md-primary);
    flex-shrink: 0;
  }

  &::after {
    content: '';
    width: 2px;
    flex: 1;
    background: linear-gradient(to bottom, var(--md-primary-container), transparent);
    margin-top: 4px;
    min-height: 32px;
  }
}

.pf-exp-card {
  background: var(--md-surface-container-lowest);
  border: 1px solid var(--md-outline-variant);
  border-left: 4px solid var(--md-primary);
  border-radius: 0 16px 16px 0;
  padding: 20px 24px;
  flex: 1;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.06);
  transition: box-shadow 0.2s;

  &:hover {
    box-shadow: 0 3px 12px rgba(0, 104, 116, 0.12);
  }
}

.pf-exp-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 4px;
}

.pf-exp-role {
  font-size: 16px;
  font-weight: 600;
  color: var(--md-on-surface);
  margin: 0;
}

.pf-exp-date {
  font-size: 12px;
  color: var(--md-on-surface-variant);
  white-space: nowrap;
}

.pf-exp-org {
  font-size: 13px;
  color: var(--md-primary);
  font-weight: 500;
  margin: 0 0 12px;
}

.pf-exp-bullets {
  margin: 0;
  padding-left: 20px;

  li {
    font-size: 14px;
    color: var(--md-on-surface-variant);
    line-height: 1.65;
    margin-bottom: 6px;

    &:last-child {
      margin-bottom: 0;
    }
  }
}

// ── Project Cards ──────────────────────────────────────────────────
.pf-card-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;

  @media (max-width: 768px) {
    grid-template-columns: 1fr;
  }
}

.pf-proj-card {
  background: var(--md-surface-container-lowest);
  border: 1px solid var(--md-outline-variant);
  border-radius: 16px;
  padding: 24px;
  transition: box-shadow 0.2s, transform 0.2s;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.06);

  &:hover {
    box-shadow: 0 4px 16px rgba(0, 104, 116, 0.14);
    transform: translateY(-2px);
  }
}

.pf-proj-badge {
  display: inline-block;
  padding: 3px 10px;
  border-radius: 6px;
  font-size: 11px;
  font-weight: 600;
  background: var(--md-primary-container);
  color: var(--md-on-primary-container);
  margin-bottom: 10px;
}

.pf-proj-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--md-on-surface);
  margin: 0 0 10px;
}

.pf-proj-desc {
  font-size: 14px;
  color: var(--md-on-surface-variant);
  line-height: 1.65;
  margin: 0 0 14px;
}

.pf-proj-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

.pf-tag {
  padding: 4px 12px;
  border-radius: 6px;
  font-size: 12px;
  background: var(--md-surface-container);
  color: var(--md-on-surface-variant);
  border: 1px solid var(--md-outline-variant);
}

// ── Publications ───────────────────────────────────────────────────
.pf-pub-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.pf-pub-item {
  background: var(--md-surface-container-lowest);
  border: 1px solid var(--md-outline-variant);
  border-left: 4px solid var(--md-primary);
  border-radius: 0 12px 12px 0;
  padding: 20px 24px;
  margin-bottom: 14px;
  transition: border-left-color 0.2s;

  &:hover {
    border-left-color: var(--md-secondary);
  }
}

.pf-pub-number {
  font-size: 11px;
  font-weight: 700;
  color: var(--md-primary);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin: 0 0 5px;
}

.pf-pub-title {
  font-size: 15px;
  font-weight: 500;
  color: var(--md-on-surface);
  margin: 0 0 6px;

  a {
    color: inherit;
    text-decoration: none;

    &:hover {
      color: var(--md-primary);
    }
  }
}

.pf-pub-authors {
  font-size: 13px;
  color: var(--md-on-surface-variant);
  margin: 0 0 10px;
}

.pf-pub-venue-row {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 8px;
}

.pf-venue-chip {
  display: inline-block;
  padding: 3px 10px;
  border-radius: 6px;
  font-size: 11px;
  font-weight: 600;
  background: var(--md-secondary-container);
  color: var(--md-on-secondary-container);
}

.pf-submitted-chip {
  display: inline-block;
  padding: 3px 10px;
  border-radius: 6px;
  font-size: 11px;
  font-weight: 600;
  background: #fff8e1;
  color: #e65100;
  border: 1px solid #ffcc02;
}

.pf-pub-detail {
  font-size: 12px;
  color: var(--md-on-surface-variant);
}

// ── Awards ─────────────────────────────────────────────────────────
.pf-award-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.pf-award-item {
  background: var(--md-surface-container-lowest);
  border: 1px solid var(--md-outline-variant);
  border-radius: 16px;
  padding: 16px 20px;
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  gap: 16px;
  transition: box-shadow 0.2s;

  &:hover {
    box-shadow: 0 3px 10px rgba(0, 104, 116, 0.1);
  }
}

.pf-award-icon {
  font-size: 28px;
  flex-shrink: 0;
}

.pf-award-body {
  flex: 1;
  min-width: 0;
}

.pf-award-name {
  font-size: 15px;
  font-weight: 600;
  color: var(--md-on-surface);
  margin: 0 0 3px;
}

.pf-award-org {
  font-size: 13px;
  color: var(--md-on-surface-variant);
  margin: 0;
}

.pf-award-year {
  font-size: 13px;
  color: var(--md-on-surface-variant);
  white-space: nowrap;
  flex-shrink: 0;
}

// ── Lifestyle Grid ─────────────────────────────────────────────────
.pf-lifestyle-intro {
  font-size: 15px;
  color: var(--md-on-surface-variant);
  font-style: italic;
  margin: -16px 0 32px;
}

.pf-photo-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(2, 240px);
  gap: 16px;
  margin-bottom: 24px;

  @media (max-width: 900px) {
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: auto;
  }

  @media (max-width: 600px) {
    grid-template-columns: 1fr;
  }
}

.pf-photo-card {
  border-radius: 16px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  background: var(--md-surface-container);
  transition: transform 0.2s, box-shadow 0.2s;

  &:hover {
    transform: scale(1.02);
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
  }

  &--tall {
    grid-row: span 2;
  }

  &--wide {
    grid-column: span 2;

    @media (max-width: 900px) {
      grid-column: span 1;
    }
  }
}

.pf-photo-card__img {
  width: 100%;
  flex: 1;
  object-fit: cover;
  display: block;
  min-height: 0;
}

.pf-photo-placeholder {
  flex: 1;
  background: linear-gradient(135deg, var(--md-primary-container), var(--md-secondary-container));
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  min-height: 160px;
}

.pf-placeholder-icon {
  font-size: 40px;
}

.pf-placeholder-hint {
  font-size: 12px;
  color: var(--md-on-primary-container);
  opacity: 0.8;
}

.pf-photo-card__body {
  padding: 14px 16px;
  background: var(--md-surface-container-lowest);
  flex-shrink: 0;
}

.pf-photo-card__activity {
  font-size: 14px;
  font-weight: 600;
  color: var(--md-on-surface);
  margin: 0 0 2px;
}

.pf-photo-card__caption {
  font-size: 13px;
  color: var(--md-on-surface-variant);
  margin: 0;
}

.pf-photo-hint {
  background: var(--md-surface-container);
  border: 1px dashed var(--md-outline-variant);
  border-radius: 12px;
  padding: 12px 20px;
  font-size: 13px;
  color: var(--md-on-surface-variant);
  text-align: center;

  code {
    background: var(--md-surface-container-high);
    padding: 1px 6px;
    border-radius: 4px;
    font-size: 12px;
  }
}
```

- [ ] **Step 2: Verify build still succeeds**

Run: `bundle exec jekyll build 2>&1 | tail -20`

Expected: no SCSS compilation errors; ends with `done in X seconds`.

- [ ] **Step 3: Commit**

```bash
git add _sass/4-layouts/_portfolio.scss
git commit -m "feat: rewrite portfolio SCSS with MD3 color tokens and component styles"
```

---

## Task 3: Rewrite `_layouts/portfolio.html`

**Files:**
- Modify: `_layouts/portfolio.html` (full rewrite)

- [ ] **Step 1: Replace the entire file with the MD3 portfolio layout**

```html
---
layout: portfolio-base
---

<!-- ================================================================
     TOP APP BAR
     ================================================================ -->
<header class="pf-app-bar" id="pf-app-bar">
  <a href="{{ site.baseurl }}/" class="pf-app-bar__brand" aria-label="Cuong Manh Nguyen — Home">
    <div class="pf-app-bar__monogram" aria-hidden="true">C</div>
    <span class="pf-app-bar__name">Cuong Manh Nguyen</span>
  </a>

  <nav class="pf-app-bar__nav" aria-label="Site navigation">
    <a href="{{ site.baseurl }}/" class="pf-app-bar__nav-link">Home</a>
    <a href="{{ site.baseurl }}/portfolio/" class="pf-app-bar__nav-link active">Portfolio</a>
    <a href="{{ site.baseurl }}/about/" class="pf-app-bar__nav-link">About</a>
  </nav>

  <a href="mailto:s.cuongnm5@gmail.com" class="pf-icon-btn" aria-label="Email Cuong">✉</a>

  <button class="pf-icon-btn pf-menu-btn" id="pf-menu-btn" aria-label="Open navigation menu" aria-expanded="false">
    ☰
  </button>
</header>

<!-- ================================================================
     MOBILE NAV DRAWER
     ================================================================ -->
<div class="pf-nav-overlay" id="pf-nav-overlay" aria-hidden="true"></div>
<nav class="pf-nav-drawer" id="pf-nav-drawer" aria-label="Mobile navigation" hidden>
  <div class="pf-nav-drawer__header">
    <p class="pf-nav-drawer__name">Cuong Manh Nguyen</p>
    <p class="pf-nav-drawer__sub">AI Engineer · Researcher</p>
  </div>
  <a href="{{ site.baseurl }}/" class="pf-nav-drawer__link">Home</a>
  <a href="{{ site.baseurl }}/portfolio/" class="pf-nav-drawer__link">Portfolio</a>
  <a href="{{ site.baseurl }}/about/" class="pf-nav-drawer__link">About</a>
  <a href="mailto:s.cuongnm5@gmail.com" class="pf-nav-drawer__link">✉ Contact</a>
</nav>

<!-- ================================================================
     TAB BAR
     ================================================================ -->
<div class="pf-tab-bar" role="tablist" aria-label="Portfolio sections">
  <button class="pf-tab active" data-tab="home"         role="tab" aria-selected="true"  aria-controls="panel-home"         tabindex="0">Home</button>
  <button class="pf-tab"        data-tab="experience"   role="tab" aria-selected="false" aria-controls="panel-experience"   tabindex="-1">Experience</button>
  <button class="pf-tab"        data-tab="projects"     role="tab" aria-selected="false" aria-controls="panel-projects"     tabindex="-1">Projects</button>
  <button class="pf-tab"        data-tab="publications" role="tab" aria-selected="false" aria-controls="panel-publications" tabindex="-1">Publications</button>
  <button class="pf-tab"        data-tab="awards"       role="tab" aria-selected="false" aria-controls="panel-awards"       tabindex="-1">Awards</button>
  <button class="pf-tab"        data-tab="lifestyle"    role="tab" aria-selected="false" aria-controls="panel-lifestyle"    tabindex="-1">Lifestyle</button>
</div>

<!-- ================================================================
     PANELS
     ================================================================ -->
<div class="pf-panels">

  <!-- ── HOME ────────────────────────────────────────────────────── -->
  <section id="panel-home" class="pf-panel active" role="tabpanel" tabindex="0">

    <div class="pf-home-hero">
      <img
        class="pf-avatar"
        src="{{ site.baseurl_root }}/images/avt.jpg"
        alt="Cuong Manh Nguyen"
        width="128"
        height="128"
      >
      <div>
        <h1 class="pf-hero-name">Cuong Manh Nguyen</h1>
        <p class="pf-hero-title">AI Engineer · Speech &amp; Language Technology · Drug Discovery</p>
        <p class="pf-hero-bio">
          Computer Science graduate from PTIT, Hanoi. AI Engineer at Zalo AI (VNG) building
          Vietnamese LLMs, TTS, and voice systems since 2020. Research Intern at HySonLab
          (University of Alabama) working on protein and antibody generation. Passionate about
          AI that improves everyday life.
        </p>
        <div class="pf-hero-links">
          <a href="https://www.linkedin.com/in/cuongnm5" class="pf-btn pf-btn--filled" target="_blank" rel="noopener noreferrer">LinkedIn</a>
          <a href="https://github.com/cuongnm5"          class="pf-btn pf-btn--tonal"  target="_blank" rel="noopener noreferrer">GitHub</a>
          <a href="mailto:s.cuongnm5@gmail.com"          class="pf-btn pf-btn--tonal">✉ Email</a>
          <a href="{{ site.baseurl_root }}/CuongManhNguyen_CV.pdf" class="pf-btn pf-btn--outlined" target="_blank" rel="noopener noreferrer">Download CV</a>
        </div>
      </div>
    </div>

    <h2 class="pf-section-label">Technical Skills</h2>
    <div class="pf-chips">
      <span class="pf-chip">Python</span>
      <span class="pf-chip">Golang</span>
      <span class="pf-chip">PyTorch</span>
      <span class="pf-chip">TensorFlow</span>
      <span class="pf-chip">FastAPI</span>
      <span class="pf-chip">Kubernetes</span>
      <span class="pf-chip">Elasticsearch</span>
      <span class="pf-chip">Milvus</span>
      <span class="pf-chip">Redis</span>
      <span class="pf-chip">MongoDB</span>
      <span class="pf-chip">MinIO</span>
      <span class="pf-chip">CUDA</span>
      <span class="pf-chip">Linux</span>
      <span class="pf-chip">Git</span>
    </div>

    <div class="pf-stats">
      <div class="pf-stat-card">
        <span class="pf-stat-icon">📄</span>
        <span class="pf-stat-value">5+</span>
        <p class="pf-stat-label">Publications</p>
      </div>
      <div class="pf-stat-card">
        <span class="pf-stat-icon">🏢</span>
        <span class="pf-stat-value">5 yrs</span>
        <p class="pf-stat-label">@ Zalo AI</p>
      </div>
      <div class="pf-stat-card">
        <span class="pf-stat-icon">🎓</span>
        <span class="pf-stat-value">10/10</span>
        <p class="pf-stat-label">Thesis Score</p>
      </div>
      <div class="pf-stat-card">
        <span class="pf-stat-icon">🥇</span>
        <span class="pf-stat-value">VLSP 2020</span>
        <p class="pf-stat-label">1st Prize TTS</p>
      </div>
    </div>

  </section>

  <!-- ── EXPERIENCE ───────────────────────────────────────────────── -->
  <section id="panel-experience" class="pf-panel" role="tabpanel" tabindex="0" hidden>

    <div class="pf-section-header"><h2>Experience</h2></div>

    <ul class="pf-timeline">

      <li class="pf-timeline-item">
        <div class="pf-timeline-track" aria-hidden="true"></div>
        <div class="pf-exp-card">
          <div class="pf-exp-header">
            <h3 class="pf-exp-role">Research Intern</h3>
            <span class="pf-exp-date">Apr 2025 – Present</span>
          </div>
          <p class="pf-exp-org">HySonLab, University of Alabama · Remote</p>
          <ul class="pf-exp-bullets">
            <li>Conducted research on drug discovery and repurposing with a focus on protein and antibody generation.</li>
            <li>Co-authored TeBaAb: text-based antigen-conditioned antibody redesign — submitted to NeurIPS 2025 Workshop with aim to publish in Oxford Journal.</li>
          </ul>
        </div>
      </li>

      <li class="pf-timeline-item">
        <div class="pf-timeline-track" aria-hidden="true"></div>
        <div class="pf-exp-card">
          <div class="pf-exp-header">
            <h3 class="pf-exp-role">AI Engineer</h3>
            <span class="pf-exp-date">Feb 2020 – Present</span>
          </div>
          <p class="pf-exp-org">Zalo AI, VNG Corporation · Ho Chi Minh City</p>
          <ul class="pf-exp-bullets">
            <li><strong>KiLM</strong> — Vietnamese-optimized LLM; KiLM-13b-v24.7.1 scored 66.07, closely trailing Llama-3-70B at 66.44.</li>
            <li>Refactored AI creative card service from Python to Golang, increasing concurrent user throughput by 500%.</li>
            <li>Developed a RAG system for KiLM integrating Milvus (vector search), Elasticsearch (FAQ retrieval), and MinIO (scalable storage).</li>
            <li><strong>Kiki Auto</strong> — Vietnamese in-car voice assistant; conducted in-depth TTS research (Tacotron2, FastSpeech, VITS); refactored TTS service achieving 80% error rate reduction.</li>
            <li>Expanded and hardened ASR services with advanced fault tolerance and scalability measures.</li>
          </ul>
        </div>
      </li>

      <li class="pf-timeline-item">
        <div class="pf-timeline-track" aria-hidden="true"></div>
        <div class="pf-exp-card">
          <div class="pf-exp-header">
            <h3 class="pf-exp-role">President, Programming PTIT Club (ProPTIT)</h3>
            <span class="pf-exp-date">Dec 2019 – Dec 2020</span>
          </div>
          <p class="pf-exp-org">Post &amp; Telecommunication Institute of Technology · Hanoi</p>
          <ul class="pf-exp-bullets">
            <li>Led a university programming club; organized AI and programming courses (C, C++, Java, Data Structures, AI) for students.</li>
            <li>Managed academic competitions, events, communications, finances, and human resources.</li>
          </ul>
        </div>
      </li>

    </ul>
  </section>

  <!-- ── PROJECTS ─────────────────────────────────────────────────── -->
  <section id="panel-projects" class="pf-panel" role="tabpanel" tabindex="0" hidden>

    <div class="pf-section-header"><h2>Projects</h2></div>

    <div class="pf-card-grid">

      <div class="pf-proj-card">
        <span class="pf-proj-badge">NeurIPS 2025 Workshop · Submitted</span>
        <h3 class="pf-proj-title">TeBaAb — Antibody Redesign</h3>
        <p class="pf-proj-desc">Text-based antigen-conditioned antibody redesign. Research at HySonLab, University of Alabama. Submitted to NeurIPS 2025 Workshop; aiming to publish in Oxford Journal.</p>
        <div class="pf-proj-tags">
          <span class="pf-tag">Drug Discovery</span>
          <span class="pf-tag">Bioinformatics</span>
          <span class="pf-tag">Python</span>
        </div>
      </div>

      <div class="pf-proj-card">
        <span class="pf-proj-badge">🥇 VLSP 2020 · 1st Prize</span>
        <h3 class="pf-proj-title">Vietnamese TTS for VLSP 2020</h3>
        <p class="pf-proj-desc">End-to-end Vietnamese TTS combining Tacotron-2, HiFiGAN vocoder, and WaveGlow denoiser. Achieved MOS 3.77/4.22 — best result at VLSP 2020 evaluation.</p>
        <div class="pf-proj-tags">
          <span class="pf-tag">Deep Learning</span>
          <span class="pf-tag">TTS</span>
          <span class="pf-tag">Signal Processing</span>
        </div>
      </div>

      <div class="pf-proj-card">
        <span class="pf-proj-badge">PRICAI 2021 · Q2</span>
        <h3 class="pf-proj-title">Vietnamese-English Code-Switching TTS</h3>
        <p class="pf-proj-desc">TTS system for Vietnamese-English mixed speech using Tacotron-2 with speaker and language embeddings. Handles code-switching input via a grapheme-to-syllable model.</p>
        <div class="pf-proj-tags">
          <span class="pf-tag">Speech Synthesis</span>
          <span class="pf-tag">NLP</span>
          <span class="pf-tag">Tacotron-2</span>
        </div>
      </div>

      <div class="pf-proj-card">
        <span class="pf-proj-badge">ACIIDS 2022 · Q2</span>
        <h3 class="pf-proj-title">Neural News Recommendation</h3>
        <p class="pf-proj-desc">Attentive neural news recommendation combining user-specific and global news models to recommend both personalized and broadly popular articles.</p>
        <div class="pf-proj-tags">
          <span class="pf-tag">Deep Learning</span>
          <span class="pf-tag">NLP</span>
          <span class="pf-tag">Recommendation</span>
        </div>
      </div>

      <div class="pf-proj-card">
        <span class="pf-proj-badge">Bachelor's Thesis · 10/10</span>
        <h3 class="pf-proj-title">Recommendation System Research</h3>
        <p class="pf-proj-desc">Research to improve the quality of recommendation systems. Scored 10/10 — Top 0.01% in graduating class at PTIT.</p>
        <div class="pf-proj-tags">
          <span class="pf-tag">Machine Learning</span>
          <span class="pf-tag">Research</span>
        </div>
      </div>

    </div>
  </section>

  <!-- ── PUBLICATIONS ─────────────────────────────────────────────── -->
  <section id="panel-publications" class="pf-panel" role="tabpanel" tabindex="0" hidden>

    <div class="pf-section-header"><h2>Publications</h2></div>

    <ul class="pf-pub-list">

      <li class="pf-pub-item">
        <p class="pf-pub-number">5 · 2025</p>
        <p class="pf-pub-title"><a href="#">TeBaAb: Text-based antigen-conditioned antibody redesign</a></p>
        <p class="pf-pub-authors"><strong>Cuong Manh Nguyen</strong>, Huy-Hoang Do-Huu, Viet Thanh Duy Nguyen, Truong-Son Hy</p>
        <div class="pf-pub-venue-row">
          <span class="pf-venue-chip">NeurIPS 2025 Workshop</span>
          <span class="pf-submitted-chip">Submitted</span>
        </div>
      </li>

      <li class="pf-pub-item">
        <p class="pf-pub-number">4 · 2022</p>
        <p class="pf-pub-title"><a href="#">Combining User Specific and Global News Features for Neural News Recommendation</a></p>
        <p class="pf-pub-authors"><strong>Cuong Manh Nguyen</strong>, Xuan Bach Nguyen, Minh Phuong Tu</p>
        <div class="pf-pub-venue-row">
          <span class="pf-venue-chip">ACIIDS 2022</span>
          <span class="pf-pub-detail">Springer LNCS · CORE B/Q2</span>
        </div>
      </li>

      <li class="pf-pub-item">
        <p class="pf-pub-number">3 · 2021</p>
        <p class="pf-pub-title"><a href="#">Learning Vietnamese-English Code-Switching Speech Synthesis Model Under Limited Code-Switched Data Scenario</a></p>
        <p class="pf-pub-authors"><strong>Cuong Manh Nguyen</strong> et al.</p>
        <div class="pf-pub-venue-row">
          <span class="pf-venue-chip">PRICAI 2021</span>
          <span class="pf-pub-detail">Springer LNAI · CORE B/Q2</span>
        </div>
      </li>

      <li class="pf-pub-item">
        <p class="pf-pub-number">2 · 2021</p>
        <p class="pf-pub-title"><a href="#">Multi-Task Text Normalization Approach for Speech Synthesis</a></p>
        <p class="pf-pub-authors">Thi Cuc Bui, <strong>Cuong Manh Nguyen</strong> et al.</p>
        <div class="pf-pub-venue-row">
          <span class="pf-venue-chip">PRICAI 2021</span>
          <span class="pf-pub-detail">Springer LNAI · CORE B/Q2</span>
        </div>
      </li>

      <li class="pf-pub-item">
        <p class="pf-pub-number">1 · 2020</p>
        <p class="pf-pub-title"><a href="#">Development of Smartcall Vietnamese Text-to-Speech for VLSP 2020</a></p>
        <p class="pf-pub-authors"><strong>Cuong Manh Nguyen</strong> et al.</p>
        <div class="pf-pub-venue-row">
          <span class="pf-venue-chip">VLSP 2020</span>
          <span class="pf-pub-detail">ACL · Leading regional Vietnamese NLP workshop</span>
        </div>
      </li>

    </ul>
  </section>

  <!-- ── AWARDS ───────────────────────────────────────────────────── -->
  <section id="panel-awards" class="pf-panel" role="tabpanel" tabindex="0" hidden>

    <div class="pf-section-header"><h2>Honors &amp; Awards</h2></div>

    <ul class="pf-award-list">

      <li class="pf-award-item">
        <span class="pf-award-icon" aria-hidden="true">🥇</span>
        <div class="pf-award-body">
          <p class="pf-award-name">1st Prize — Text-to-Speech Category</p>
          <p class="pf-award-org">Vietnamese Language &amp; Speech Processing (VLSP) 2020</p>
        </div>
        <span class="pf-award-year">2020</span>
      </li>

      <li class="pf-award-item">
        <span class="pf-award-icon" aria-hidden="true">🏅</span>
        <div class="pf-award-body">
          <p class="pf-award-name">Top 10 Finalists — National Digital Race</p>
          <p class="pf-award-org">FPT Corporation</p>
        </div>
        <span class="pf-award-year">2020</span>
      </li>

      <li class="pf-award-item">
        <span class="pf-award-icon" aria-hidden="true">🥈</span>
        <div class="pf-award-body">
          <p class="pf-award-name">3rd Prize — ACM/ICPC PTIT</p>
          <p class="pf-award-org">Top 6 of 174 competing teams</p>
        </div>
        <span class="pf-award-year">2019</span>
      </li>

      <li class="pf-award-item">
        <span class="pf-award-icon" aria-hidden="true">🏅</span>
        <div class="pf-award-body">
          <p class="pf-award-name">Consolation Prize — ACM/ICPC PTIT</p>
          <p class="pf-award-org">Post &amp; Telecommunication Institute of Technology</p>
        </div>
        <span class="pf-award-year">2018</span>
      </li>

      <li class="pf-award-item">
        <span class="pf-award-icon" aria-hidden="true">📚</span>
        <div class="pf-award-body">
          <p class="pf-award-name">Academic Scholarship</p>
          <p class="pf-award-org">Post &amp; Telecommunication Institute of Technology</p>
        </div>
        <span class="pf-award-year">2018, 2019, 2020</span>
      </li>

    </ul>
  </section>

  <!-- ── LIFESTYLE ─────────────────────────────────────────────────── -->
  <section id="panel-lifestyle" class="pf-panel" role="tabpanel" tabindex="0" hidden>

    <div class="pf-section-header">
      <h2>My Life Outside Code</h2>
    </div>
    <p class="pf-lifestyle-intro">When I'm not training models, I'm training myself.</p>

    <div class="pf-photo-grid">

      <!-- Running — existing image -->
      <div class="pf-photo-card pf-photo-card--tall">
        <img class="pf-photo-card__img" src="{{ site.baseurl_root }}/images/run_wp.png" alt="Running and marathon training">
        <div class="pf-photo-card__body">
          <p class="pf-photo-card__activity">🏃 Running</p>
          <p class="pf-photo-card__caption">Marathon training &amp; road races</p>
        </div>
      </div>

      <!-- Trekking — PLACEHOLDER: replace src with your trekking photo, e.g. /images/trekking.jpg -->
      <div class="pf-photo-card">
        <div class="pf-photo-placeholder">
          <span class="pf-placeholder-icon">🏔</span>
          <span class="pf-placeholder-hint">Add trekking photo</span>
        </div>
        <div class="pf-photo-card__body">
          <p class="pf-photo-card__activity">🏔 Trekking</p>
          <p class="pf-photo-card__caption">Into the mountains</p>
        </div>
      </div>

      <!-- Badminton — PLACEHOLDER: replace with your badminton photo, e.g. /images/badminton.jpg -->
      <div class="pf-photo-card">
        <div class="pf-photo-placeholder">
          <span class="pf-placeholder-icon">🏸</span>
          <span class="pf-placeholder-hint">Add badminton photo</span>
        </div>
        <div class="pf-photo-card__body">
          <p class="pf-photo-card__activity">🏸 Badminton</p>
          <p class="pf-photo-card__caption">Weekly smash sessions</p>
        </div>
      </div>

      <!-- Marathon highlight — existing image, spans 2 columns -->
      <div class="pf-photo-card pf-photo-card--wide">
        <img class="pf-photo-card__img" src="{{ site.baseurl_root }}/images/mc.jpg" alt="Marathon race highlight">
        <div class="pf-photo-card__body">
          <p class="pf-photo-card__activity">🏅 Marathon</p>
          <p class="pf-photo-card__caption">Race day highlights</p>
        </div>
      </div>

      <!-- More adventures — PLACEHOLDER: add more lifestyle photos as needed -->
      <div class="pf-photo-card">
        <div class="pf-photo-placeholder">
          <span class="pf-placeholder-icon">🌿</span>
          <span class="pf-placeholder-hint">Add more photos</span>
        </div>
        <div class="pf-photo-card__body">
          <p class="pf-photo-card__activity">🌿 More Adventures</p>
          <p class="pf-photo-card__caption">Life in motion</p>
        </div>
      </div>

    </div>

    <p class="pf-photo-hint">
      📸 Drop photos into <code>/images/</code> and replace the placeholder <code>&lt;div&gt;</code>
      blocks with <code>&lt;img class="pf-photo-card__img" src="/images/your-photo.jpg" alt="..."&gt;</code>
    </p>

  </section>

</div><!-- .pf-panels -->

<!-- ================================================================
     JAVASCRIPT
     ================================================================ -->
<script>
(function () {
  'use strict';

  // ── Tab switching ──────────────────────────────────────────────
  var tabs   = Array.from(document.querySelectorAll('.pf-tab'));
  var panels = Array.from(document.querySelectorAll('.pf-panel'));

  function activateTab(tab) {
    tabs.forEach(function (t) {
      t.classList.remove('active');
      t.setAttribute('aria-selected', 'false');
      t.setAttribute('tabindex', '-1');
    });
    panels.forEach(function (p) {
      p.classList.remove('active');
      p.hidden = true;
    });
    tab.classList.add('active');
    tab.setAttribute('aria-selected', 'true');
    tab.setAttribute('tabindex', '0');
    var panel = document.getElementById('panel-' + tab.getAttribute('data-tab'));
    panel.classList.add('active');
    panel.hidden = false;
  }

  tabs.forEach(function (tab, idx) {
    tab.addEventListener('click', function () { activateTab(this); });
    tab.addEventListener('keydown', function (e) {
      var i = tabs.indexOf(this);
      if (e.key === 'ArrowRight') { e.preventDefault(); activateTab(tabs[(i + 1) % tabs.length]); }
      if (e.key === 'ArrowLeft')  { e.preventDefault(); activateTab(tabs[(i - 1 + tabs.length) % tabs.length]); }
      if (e.key === 'Home')       { e.preventDefault(); activateTab(tabs[0]); }
      if (e.key === 'End')        { e.preventDefault(); activateTab(tabs[tabs.length - 1]); }
    });
  });

  // ── App Bar scroll shadow ──────────────────────────────────────
  var appBar = document.getElementById('pf-app-bar');
  window.addEventListener('scroll', function () {
    appBar.classList.toggle('scrolled', window.scrollY > 10);
  }, { passive: true });

  // ── Mobile nav drawer ─────────────────────────────────────────
  var menuBtn = document.getElementById('pf-menu-btn');
  var overlay = document.getElementById('pf-nav-overlay');
  var drawer  = document.getElementById('pf-nav-drawer');

  function openNav() {
    overlay.classList.add('open');
    overlay.removeAttribute('aria-hidden');
    drawer.classList.add('open');
    drawer.removeAttribute('hidden');
    menuBtn.setAttribute('aria-expanded', 'true');
    document.body.style.overflow = 'hidden';
  }

  function closeNav() {
    overlay.classList.remove('open');
    overlay.setAttribute('aria-hidden', 'true');
    drawer.classList.remove('open');
    drawer.hidden = true;
    menuBtn.setAttribute('aria-expanded', 'false');
    document.body.style.overflow = '';
  }

  menuBtn.addEventListener('click', openNav);
  overlay.addEventListener('click', closeNav);

  document.addEventListener('keydown', function (e) {
    if (e.key === 'Escape' && drawer.classList.contains('open')) closeNav();
  });
}());
</script>
```

- [ ] **Step 2: Verify the build produces the expected output**

Run: `bundle exec jekyll build 2>&1 | tail -20`

Expected: no errors; ends with `done in X seconds`.

- [ ] **Step 3: Spot-check the built HTML for key elements**

Run:
```bash
grep -c "pf-app-bar"    _site/portfolio/index.html
grep -c "pf-tab-bar"    _site/portfolio/index.html
grep -c "panel-home"    _site/portfolio/index.html
grep -c "panel-lifestyle" _site/portfolio/index.html
grep -c "TeBaAb"        _site/portfolio/index.html
grep -c "HySonLab"      _site/portfolio/index.html
grep -c "pf-photo-grid" _site/portfolio/index.html
```

Expected: each command prints `1` or higher (not `0`).

- [ ] **Step 4: Serve locally and visually verify all 6 tabs**

Run: `bundle exec jekyll serve --livereload`

Open: `http://localhost:4000/blog/portfolio/`

Check:
- Top App Bar is visible with monogram `[C]`, name, nav links, email icon
- Tab bar shows: Home · Experience · Projects · Publications · Awards · Lifestyle
- Home tab: hero with photo, name, title, bio, 4 CTA buttons, skill chips, 4 stat cards
- Experience tab: 3 timeline entries (HySonLab, Zalo AI with bullet points, PTIT Club)
- Projects tab: 5 cards in 2-col grid; TeBaAb shows "NeurIPS 2025 Workshop · Submitted" badge
- Publications tab: 5 items numbered 5→1; item 5 shows yellow "Submitted" chip
- Awards tab: 5 rows; includes "Academic Scholarship 2018, 2019, 2020"
- Lifestyle tab: running image visible, 3 teal gradient placeholders, marathon image visible
- Mobile (resize to <768px): hamburger appears, nav links hide, drawer opens/closes on tap

- [ ] **Step 5: Commit**

```bash
git add _layouts/portfolio.html
git commit -m "feat: rewrite portfolio with MD3 Top App Bar, 6-panel tab layout, full CV data"
```

---

## Task 4: Final integration commit

**Files:** none (verification only)

- [ ] **Step 1: Clean build**

Run:
```bash
bundle exec jekyll clean && bundle exec jekyll build 2>&1 | tail -5
```

Expected: ends with `done in X seconds`, no warnings about missing files or SCSS errors.

- [ ] **Step 2: Verify portfolio page is not blank**

Run: `wc -l _site/portfolio/index.html`

Expected: > 200 lines.

- [ ] **Step 3: Confirm blog posts are unaffected**

Run: `bundle exec jekyll build 2>&1 | grep -i error`

Expected: no output (zero errors).

- [ ] **Step 4: Final commit**

```bash
git add -A
git status
git commit -m "feat: MD3 portfolio redesign — App Bar, tabs, Home/Experience/Projects/Publications/Awards/Lifestyle panels"
```

---

## Spec Coverage Check

| Spec requirement | Covered by |
|-----------------|-----------|
| MD3 color system (teal/indigo) | Task 2 — CSS custom properties |
| Top App Bar with site nav links | Task 3 — `.pf-app-bar` |
| Primary Tab Bar (sticky below App Bar) | Task 3 — `.pf-tab-bar` |
| Homepage panel (hero, chips, stats) | Task 3 — `#panel-home` |
| Experience panel (HySonLab, Zalo AI, PTIT) | Task 3 — `#panel-experience` |
| Projects panel (5 cards incl. TeBaAb) | Task 3 — `#panel-projects` |
| Publications panel (5 papers, submitted chip) | Task 3 — `#panel-publications` |
| Awards panel (incl. scholarships) | Task 3 — `#panel-awards` |
| Lifestyle panel (run_wp.png, mc.jpg, 3 placeholders) | Task 3 — `#panel-lifestyle` |
| Mobile nav drawer | Task 2 (CSS) + Task 3 (HTML + JS) |
| No global files touched | All tasks — only `portfolio-base.html`, `portfolio.html`, `_portfolio.scss` |
| Responsive breakpoints (1200 / 768 / 600px) | Task 2 — `@media` blocks throughout SCSS |
