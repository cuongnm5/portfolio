# masterPortfolio-Style Redesign

**Date:** 2026-05-31
**Status:** Approved
**Reference:** https://github.com/ashutosh1919/masterPortfolio

---

## Overview

Full visual redesign of the Jekyll portfolio to match the masterPortfolio aesthetic:
- Single-page scrolling (sections replace tabs)
- Warm off-white background with orange accent system
- Large bold hero with social icon circles
- Orange timeline for Experience
- Gradient-top-bar project cards with click-to-detail modal
- Lifestyle photo grid section

**Files changed (portfolio-scoped only — no global files except head.html font):**

| File | Action |
|------|--------|
| `_layouts/portfolio.html` | Full rewrite — scroll sections, new hero, navbar |
| `_sass/4-layouts/_portfolio.scss` | Full rewrite — masterPortfolio-inspired component styles |

---

## Color System

```scss
--mp-primary:   #E65100;   // deep orange — buttons, active nav, accents
--mp-secondary: #FF8C00;   // amber — gradient right side, timeline dots
--mp-accent:    #FFB347;   // light amber — hover highlights
--mp-bg:        #FFF8F0;   // warm off-white — page background
--mp-surface:   #FFFFFF;   // white — cards, experience section bg
--mp-text:      #1A1A1A;   // near-black — headings
--mp-muted:     #777777;   // grey — body text, dates
--mp-border:    #FDE8D8;   // pale orange — dividers, card borders
```

Typography:
- Logo "Cuong." → Georgia italic bold, no extra font load
- Hero name → 64px desktop / 36px mobile, font-weight 900, Roboto
- Section labels → 0.75rem uppercase, letter-spacing 2px, `--mp-primary`
- Section titles → 2.2rem, font-weight 700
- Body → Roboto 15–16px (already loaded)

---

## Section 1 — Navbar

**Height:** 70px. **Sticky**, transparent → white + shadow on scroll (JS scroll listener).

```
[ Cuong. ]                    [ Experience   Projects   Lifestyle ]
  Georgia italic, #E65100        hover: #FFB347 bg, border-radius 8px
  cursor: pointer                active: font-weight 700, color #E65100
  → smooth-scrolls to #home      
  mobile: ☰ hamburger → max-height slide-down
```

- `Cuong.` logo clicks → smooth-scroll to `#home`
- Nav links scroll to `#experience`, `#projects`, `#lifestyle`
- Active link updated by IntersectionObserver as user scrolls
- No "Home" nav link — logo serves that purpose

**Mobile nav** (≤768px):
- Hamburger button (right side)
- Dropdown reveals links stacked vertically
- max-height 0 → 200px transition (0.2s ease-out, masterPortfolio pattern)

---

## Section 2 — Home (Hero)

**Background:** `linear-gradient(135deg, #FFF8F0 0%, #FFF0E0 60%, #FFE8CC 100%)`
**Min-height:** `calc(100vh - 70px)`
**Layout:** two-column flex, text left / photo right, centered vertically.

### Left column
```
Hi all, I am           ← 1rem, #999, uppercase, letter-spacing 2px

Cuong Manh             ← 64px, font-weight 900, #1A1A1A
Nguyen                 ← "Nguyen" in #E65100

AI Engineer 🚀 Speech & Language · Drug Discovery
                       ← 1.2rem, font-weight 500, #555

[bio paragraph]        ← 1rem, #777, line-height 1.7, max-width 520px

[in] [Gs] [GH] [✉]    ← 44×44px circles, per-platform color, hover float-up

[ Contact Me 👋 ]      ← filled orange pill button, box-shadow
```

### Right column
- `avt.jpg` in 240×240px circle
- Border: 4px solid `#E65100`
- Box-shadow: `0 0 0 8px rgba(230,81,0,0.12), 0 16px 48px rgba(230,81,0,0.2)`

### Social icon circles (masterPortfolio style)
| Icon | Color |
|------|-------|
| LinkedIn | `#0077B5` |
| Google Scholar | `#4285F4` |
| GitHub | `#181717` |
| Email | `#E65100` |

Size: 44×44px, border-radius 50%, color white. Hover: `translateY(-3px)` + deeper shadow.

### Entrance animation
All hero content: `opacity: 0 → 1`, `translateY(20px → 0)`, `0.6s ease`, on page load.

---

## Section 3 — Experience

**Background:** `#FFFFFF` (white, contrast with hero and projects)
**Padding:** 80px 5%

The section has three sub-sections stacked vertically, each with its own label + title + content:

### 3a — Work Experience

```
What I have done so far     ← section label
Work Experience             ← section title

│ ● Research Intern                          Apr 2025 – Present
│   HySonLab, University of Alabama · Remote
│   • Drug discovery & repurposing…
│   • TeBaAb: NeurIPS 2025 Workshop…
│
│ ● AI Engineer                              Feb 2020 – Present
│   Zalo AI, VNG Corporation · Ho Chi Minh City
│   • KiLM — Vietnamese LLM; 66.07 benchmark…
│   • Python → Golang refactor: 500% throughput…
│   • RAG system: Milvus + Elasticsearch + MinIO…
│   • Kiki Auto — 80% TTS error reduction…
│
│ ● President, Programming PTIT Club         Dec 2019 – Dec 2020
    PTIT · Hanoi
    • Led 100+ member club; AI/CS courses and competitions…
```

### 3b — Education

```
Where I studied             ← section label
Education                   ← section title

┌──────────────────────────────────────────────────────┐
│  🎓  Bachelor's in Information Technology            │
│      Post & Telecommunication Institute of           │
│      Technology (PTIT) · Hanoi, Vietnam              │
│      Aug 2017 – Dec 2021  |  GPA: 3.16/4.0          │
│                                                      │
│      Thesis: Research to improve the quality of      │
│      recommendation systems — Score: 10/10 (Top 0.01%)│
└──────────────────────────────────────────────────────┘
```

Education uses a single wide card (not timeline) with a 🎓 icon, border-left orange accent.

### 3c — Certifications

```
What I have achieved        ← section label
Certifications              ← section title

┌──────────────────────────────────────────────────────┐
│  📜  Certifications coming soon                      │
│      Check back for professional credentials and     │
│      online course completions.                      │
└──────────────────────────────────────────────────────┘
```

Placeholder card with dashed border and muted text — same structure as Education card, easy to populate later.

**Timeline style (Work Experience):**
- Vertical line: 2px, `linear-gradient(#E65100, #FFB347, transparent)`
- Dot: 14px circle, `#E65100`, ring shadow `rgba(230,81,0,0.2)`
- Card: background `#FFF8F0`, border `1px solid #FDE8D8`, border-radius 14px
- Card hover: `box-shadow: 0 4px 20px rgba(230,81,0,0.12)`
- Org text: `#E65100`, font-weight 600

**Education / Certification card style:**
- White background, border-radius 14px, border-left 4px solid `#E65100`
- Box-shadow: `0 2px 12px rgba(0,0,0,0.07)`
- Icon: 48px emoji in orange-tinted circle (`#FFF0E6` bg)
- Max-width 700px, centered

---

## Section 4 — Projects

**Background:** `#FFF8F0` (warm off-white)
**Padding:** 80px 5%

**Grid:** 2-column desktop, 1-column mobile.
**Card:** white, border-radius 14px, box-shadow `0 2px 12px rgba(0,0,0,0.08)`
**Gradient bar:** `linear-gradient(90deg, #E65100, #FFB347)` — 5px height
**Hover:** `translateY(-4px)` + deeper orange shadow
**Click:** opens detail modal with full content (see modal spec per project below)

### 4 projects displayed:

---

#### Project 1 — Text-to-Speech (Vietnamese TTS)

**Card:**
- Badge: `🥇 VLSP 2020 · 1st Prize`
- Title: `Vietnamese Text-to-Speech`
- Desc: End-to-end Vietnamese TTS system (Tacotron-2 + HiFiGAN + WaveGlow). Achieved MOS 3.77/4.22 — best result at VLSP 2020. Research papers accepted at PRICAI 2021 (Q2).
- Tags: `TTS` · `Deep Learning` · `Speech Synthesis` · `Python`
- Gradient bar: `#E65100 → #FF8C00`

**Detail modal content:**
- Description: Full project write-up on Vietnamese TTS pipeline
- Publications:
  - "Development of Smartcall Vietnamese TTS for VLSP 2020" — VLSP 2020 (ACL)
  - "Vietnamese-English Code-Switching Speech Synthesis" — PRICAI 2021 (Q2)
  - "Multi-task Text Normalization for Speech Synthesis" — PRICAI 2021 (Q2)
- 📰 Press: [Znews — Vietnamese AI Research Recognized Internationally](https://znews.vn/nghien-cuu-ve-ai-cua-nhom-ky-su-viet-duoc-cong-nhan-cap-quoc-te-post1281985.html)
- Key quote from article: *"The gap between Vietnam and the world in AI is narrowing."*

---

#### Project 2 — AI Greeting Card (Zalo AI)

**Card:**
- Badge: `Zalo AI · 3.7M Cards Created`
- Title: `AI Greeting Card — Zalo`
- Desc: AI-powered greeting card product on Zalo generating personalized poetry, lyrics and rap. Refactored service Python→Golang (+500% throughput). 3.7M cards, 1.7M users in Women's Day 2025 campaign alone.
- Tags: `Golang` · `Python` · `LLM` · `Production`
- Gradient bar: `#FF8C00 → #FFD700`

**Detail modal content:**
- Description: AI card generation service at Zalo AI. Integrated with KiLM (Vietnamese LLM). Refactored backend Python→Golang yielding 500% concurrent user throughput gain.
- 📰 Press:
  - [Znews — 3.7 Million AI Cards on Zalo for March 8](https://znews.vn/gan-4-trieu-tam-thiep-ai-duoc-tao-ra-tren-zalo-trong-dip-83-post1539259.html)
  - [LinkedIn post](https://www.linkedin.com/feed/update/urn:li:ugcPost:7258038391957528576/)
- Stats: 3.7M cards · 1.7M users · 4× YoY growth · 3× engagement vs prior year

---

#### Project 3 — Cosmic Corner (Side Project)

**Card:**
- Badge: `Side Project · Live`
- Title: `Cosmic Corner 🌙`
- Desc: Vietnamese esoteric learning platform — AI-powered tarot readings, tea leaf divination, horoscope & natal charts, 78-card flashcard system. Freemium with community features.
- Tags: `AI` · `Full Stack` · `Vietnamese` · `Side Project`
- Gradient bar: `#7B2FBE → #E65100`

**Detail modal content:**
- Description: Personal side project combining traditional Vietnamese spirituality with modern AI. Built end-to-end: AI interpretation engine, card library, e-commerce shop.
- 🔗 Live site: [cosmic-corner.io.vn](https://cosmic-corner.io.vn/)
- Features: AI tarot · tea leaf divination · horoscope · 78-card flashcards · quizzes · shop

---

#### Project 4 — AI in Medical Domain (Side Project / Research)

**Card:**
- Badge: `Research · HySonLab`
- Title: `AI for Medicine & Drug Discovery`
- Desc: Research on antibody redesign (TeBaAb, NeurIPS 2025 Workshop) and a comprehensive review of efficient deep learning for medical imaging — bridging high-performance AI and clinical deployment.
- Tags: `Drug Discovery` · `Medical AI` · `Deep Learning` · `Research`
- Gradient bar: `#E65100 → #FFB347`

**Detail modal content:**
- Publications:
  - **TeBaAb**: "Text-based antigen-conditioned antibody redesign" — submitted to NeurIPS 2025 Workshop; aiming to publish in Oxford Journal. (Cuong Manh Nguyen, Huy-Hoang Do-Huu, Viet Thanh Duy Nguyen, Truong-Son Hy)
  - **Review**: "Efficient Deep Learning for Medical Imaging: Bridging the Gap Between High-Performance AI and Clinical Deployment" — arXiv:2602.00910 (Cuong Manh Nguyen, Truong-Son Hy)
- 🔗 Links: [arXiv:2602.00910](https://arxiv.org/abs/2602.00910)
- Lab: HySonLab, University of Alabama at Birmingham (Prof. Truong-Son Hy)

---

### Detail Modal — updated structure

Each project's modal now shows:
```
[badge chip]
[project title]                          [✕ close]
[description paragraph]
[tags row]

📄 Publications (if any)
   • Paper title — Venue (Year)  [link]

📰 Press / References (if any)
   • Article title [link]

🔗 Links (if any)
   • Live site / arXiv / LinkedIn

[ Draft note if content incomplete ]
```

No longer shows generic "draft coming soon" for projects with real content.

---

## Section 5 — Lifestyle

**Background:** `#FFFFFF`
**Padding:** 80px 5%

```
Life outside code           ← section label
Lifestyle                   ← section title
"When I'm not training models, I'm training myself."

┌──────────────┐ ┌──────┐ ┌──────┐
│              │ │ 🏔   │ │ 🏸   │
│  🏃 Running  │ │Trekk.│ │Badm. │
│  run_wp.png  │ │ phdr │ │ phdr │
│  (tall card) │ │      │ │      │
│              │ ├──────┴─┴──────┤
│              │ │ 🏅 Marathon   │
└──────────────┘ │   mc.jpg      │
┌──────────────┐ └──────────────┘
│ 🌿 More…    │
│  placeholder │
└──────────────┘
```

Same photo grid as current (5 cards, run_wp.png + mc.jpg real images, 3 placeholders).
Placeholder gradient: `#FFF0E0 → #FFD9B0` (warm orange tones to match new palette).

---

## Responsive Breakpoints

| Breakpoint | Change |
|-----------|--------|
| ≥ 1100px | Full 2-col hero; 2-col project grid; full timeline |
| 768–1099px | Narrower hero; projects still 2-col |
| < 768px | Stacked hero (photo above text); 1-col projects; hamburger nav |

---

## JS Behaviours

1. **Navbar scroll shadow** — `window.scroll` → add `.scrolled` class (white bg + shadow)
2. **Active nav link** — IntersectionObserver on each section → update active link
3. **Smooth scroll** — all nav links and logo use `scrollIntoView({behavior:'smooth'})`
4. **Mobile hamburger** — toggle `.open` on nav list (max-height animation)
5. **Hero fade-in** — CSS animation on `.hero-content` (opacity + translateY)
6. **Project detail modal** — unchanged from current implementation
7. **Escape key** — closes modal (unchanged)

---

## What is NOT in scope

- Lottie animation (user chose circle photo)
- Skills section

- Blogs section
- Contact form / footer contact
- i18n / multi-language
- Changing any file outside `_layouts/` and `_sass/4-layouts/`
