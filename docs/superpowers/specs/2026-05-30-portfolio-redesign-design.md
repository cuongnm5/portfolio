# Portfolio Redesign — Material Design 3

**Date:** 2026-05-30
**Status:** Approved
**Scope:** `_layouts/portfolio.html` + `_sass/4-layouts/_portfolio.scss` only

---

## Overview

Redesign the Jekyll portfolio page (`/portfolio/`) with Material Design 3 (MD3 / Material You).
No changes to the blog theme, `header.html`, `default.html`, or other pages.

The portfolio becomes a self-contained page with its own MD3 Top App Bar, a primary tab bar,
and six content panels: Home, Experience, Projects, Publications, Awards, Lifestyle.

---

## Architecture

### File changes

| File | Action |
|------|--------|
| `_layouts/portfolio-base.html` | New — minimal HTML shell (no blog header) for the portfolio page |
| `_layouts/portfolio.html` | Full rewrite — extends `portfolio-base`, contains MD3 Top App Bar + Tab Bar + 6 panels |
| `_sass/4-layouts/_portfolio.scss` | Full rewrite — MD3 CSS custom properties + component styles |
| `_pages/portfolio.md` | No change (still `layout: portfolio`) |
| `images/` | No new images required at implementation time |

### Overriding the blog header

A new `_layouts/portfolio-base.html` provides a minimal HTML shell (doctype, head, body, footer JS)
with no blog header included. `_layouts/portfolio.html` sets `layout: portfolio-base` in its front
matter, so the MD3 Top App Bar is the only navigation on the portfolio page. No global files touched.

---

## MD3 Color System

CSS custom properties defined on `:root` within `_portfolio.scss` scope:

```scss
// Primary — teal
--md-primary: #006874;
--md-on-primary: #ffffff;
--md-primary-container: #97f0ff;
--md-on-primary-container: #001f24;

// Secondary
--md-secondary: #4a6267;
--md-on-secondary: #ffffff;
--md-secondary-container: #cde7ec;
--md-on-secondary-container: #051f23;

// Surface tones
--md-surface: #fafdfd;
--md-surface-container-lowest: #ffffff;
--md-surface-container-low: #f0fafb;
--md-surface-container: #e4f3f5;
--md-surface-container-high: #deedf0;
--md-on-surface: #161d1e;
--md-on-surface-variant: #3f484a;
--md-outline: #6f797a;
--md-outline-variant: #bec8ca;
```

Typography: **Roboto** loaded via Google Fonts (Display · Headline · Body · Label scales).
Elevation via box-shadow layers (MD3 tonal, not drop shadows).

---

## Section 1 — Top App Bar

**Sticky, 64px desktop / 56px mobile.**

```
[ C ]  Cuong Manh Nguyen          Home   Portfolio   About   ✉
```

- Left: circular monogram chip `[C]` + wordmark
- Right: flat text links — Home (`/`), Portfolio (`/portfolio/`), About (`/about/`), email icon button
- Background: `--md-surface-container-low` with 1px bottom border (`--md-outline-variant`) on scroll
- Mobile: name truncates; links collapse to `☰` icon-button opening a bottom sheet / side drawer

---

## Section 2 — Tab Bar

**Sticky below Top App Bar (top: 64px desktop / 56px mobile). MD3 primary tabs.**

Tabs: `Home` · `Experience` · `Projects` · `Publications` · `Awards` · `Lifestyle`

- Active: `--md-primary` text + 3px teal underline indicator
- Inactive: `--md-on-surface-variant` muted text
- Scrollable horizontally on mobile, no wrap
- Height: 48px, 16px horizontal padding per tab

---

## Section 3 — Home Panel

**Two-column desktop (photo left, text right), stacked mobile.**

Background: full-width `--md-surface-container` teal wash.

### Hero block
- Photo: `avt.jpg`, 128px circle, `--md-primary` ring border
- Name: MD3 Display Small — "Cuong Manh Nguyen"
- Subtitle: "AI Engineer · Speech & Language Technology · Drug Discovery"
- Bio: current dual roles at Zalo AI + HySonLab, passion for AI in daily life
- CTA buttons: LinkedIn, GitHub, Email, Google Scholar, Download CV

### Skills chips row
MD3 input chip style, scrollable, from CV tech stack:
`Python` · `Golang` · `PyTorch` · `TensorFlow` · `FastAPI` · `Kubernetes` ·
`Elasticsearch` · `Milvus` · `Redis` · `MongoDB` · `CUDA` · `Git`

### Quick Stats row (4 MD3 filled-tonal cards)
| Stat | Value |
|------|-------|
| Publications | 5+ Papers |
| Industry | 5 yrs @ Zalo AI |
| Thesis | 10/10 Score |
| Top Award | 🥇 VLSP 2020 |

---

## Section 4 — Experience Panel

Vertical timeline. Each entry: MD3 outlined card with left teal border strip.

### Entries (reverse chronological)

**HySonLab, University of Alabama** — Research Intern · Remote · Apr 2025–Present
- Drug discovery & repurposing: protein and antibody generation research

**Zalo AI, VNG Corporation** — AI Engineer · Ho Chi Minh City · Feb 2020–Present
- KiLM — Vietnamese-optimized LLM; KiLM-13b scored 66.07 vs Llama-3-70B 66.44
- Kiki Auto — Vietnamese in-car voice assistant; TTS research (Tacotron2, FastSpeech, VITS); 80% error reduction
- RAG system: Milvus + Elasticsearch + MinIO for KiLM
- Refactored AI card service Python→Golang; 500% concurrent throughput gain

**Programming PTIT Club** — President · Hanoi · Dec 2019–Dec 2020
- Led AI/programming courses, academic competitions, club operations, finance

---

## Section 5 — Projects Panel

2-column card grid desktop, 1-column mobile. MD3 elevated card.

| Project | Key detail |
|---------|-----------|
| TeBaAb — Antibody Redesign | Text-based antigen-conditioned; submitted NeurIPS 2025 Workshop |
| Vietnamese TTS for VLSP 2020 | Tacotron-2 + HiFiGAN + WaveGlow; MOS 3.77/4.22; 1st prize |
| Vietnamese-English CS TTS | Tacotron-2 + speaker/language embeddings; code-switching |
| Neural News Recommendation | Attentive user+global news model; ACIIDS 2022 |
| Recommendation System | Bachelor's thesis; 10/10; top 0.01% in class |

Each card: title (Headline Small), description (Body Medium), tech tag chips, optional link buttons.

---

## Section 6 — Publications Panel

Numbered list, reverse-chronological. MD3 surface-variant rows.

1. **TeBaAb** — NeurIPS 2025 Workshop (submitted) `[Submitted]`
2. **Neural News Recommendation** — ACIIDS 2022 (Springer LNCS, Q2)
3. **Code-Switching TTS** — PRICAI 2021 (Springer LNAI, Q2)
4. **Multi-Task Text Normalization for TTS** — PRICAI 2021 (Springer LNAI, Q2)
5. **Vietnamese TTS for VLSP 2020** — VLSP 2020 (ACL workshop)

Each row: title (link), authors, venue badge chip, Scholar link button.

---

## Section 7 — Awards Panel

MD3 filled-tonal list rows with medal icons.

| Icon | Award | Year |
|------|-------|------|
| 🥇 | 1st Prize TTS — VLSP 2020 | 2020 |
| 🏅 | Top 10 Finalists — National Digital Race 2020 | 2020 |
| 🥈 | 3rd Prize — ACM/ICPC PTIT 2019 | 2019 |
| 🏅 | Consolation Prize — ACM/ICPC PTIT 2018 | 2018 |
| 📚 | Academic Scholarship PTIT | 2018, 2019, 2020 |

---

## Section 8 — Lifestyle Panel

**Heading:** "My Life Outside Code"
**Subhead:** "When I'm not training models, I'm training myself."

3-column photo grid desktop, 1-column mobile.

| Card | Image | Caption |
|------|-------|---------|
| 🏃 Running | `run_wp.png` (existing) | Marathon training & road races |
| 🏔 Trekking | `placeholder` | Into the mountains |
| 🏸 Badminton | `placeholder` | Weekly smash sessions |
| 🏅 Marathon | `mc.jpg` (existing) | Highlight race photo (spans 2 cols) |
| 🌿 More | `placeholder` | More adventures |

Placeholders: `<img src="">` with CSS fallback — teal gradient + centered emoji icon.
HTML comments label each placeholder for easy swap.
Info chip at bottom: "📸 Drop photos in /images/ and update src attributes."

Each card: image fills top ~60%, activity name + caption on `--md-surface-container` background.
Hover: MD3 elevation lift + `scale(1.02)`.

---

## Responsive Breakpoints

| Breakpoint | Behavior |
|-----------|---------|
| ≥ 1200px | Full two-column hero; 2-col card grids; 3-col lifestyle grid |
| 768–1199px | Single-column hero; 2-col cards; 2-col lifestyle |
| < 768px | Stacked hero; 1-col cards; 1-col lifestyle; collapsible top nav |

---

## What is NOT in scope

- Changing `header.html`, `default.html`, blog post styles, or any other page
- Adding a CMS or data layer — all content is hardcoded HTML
- Internationalisation (i18n) updates
- Deploying or pushing to GitHub Pages
