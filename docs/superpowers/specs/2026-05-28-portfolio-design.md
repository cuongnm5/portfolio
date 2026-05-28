# Portfolio Page Design Spec

**Date:** 2026-05-28
**Route:** `/portfolio/`
**Color tone:** Orange (`#ff6b35` accent, `#f6b141` brand — already in `_sass/0-settings/_variables.scss`)

---

## Overview

A professional personal portfolio page at `/portfolio/` built as a Jekyll page using a dedicated `portfolio` layout. The page inherits the existing site header/nav/footer from `default.html` but renders its own full-width tabbed UI in the content area.

---

## Architecture

| File | Purpose |
|------|---------|
| `_layouts/portfolio.html` | New layout extending `default.html`; renders full-width (no column constraint) |
| `_pages/portfolio.md` | Jekyll page with `layout: portfolio`, `permalink: /portfolio/`, `title: Portfolio` |
| `_sass/4-layouts/_portfolio.scss` | All portfolio-specific styles, scoped under `.portfolio-page` |
| `_includes/main.scss` | Add `@import "4-layouts/portfolio"` |

No new JS dependencies. Tabs use vanilla JS.

---

## Hero Section

**Style:** Centered dark card (Option B)

- Background: dark gradient (`#1a1a2e` → `#2d1a0e`) with subtle orange circular blobs for depth
- Circular profile photo (`images/avt.jpg`), orange gradient border ring
- Name: `Cuong Manh Nguyen` — large, bold, white
- Title/tagline: `AI Researcher · Software Engineer · Educator` — orange (`#f6b141`)
- Short bio: 1–2 sentences, muted gray
- Social link pill buttons: LinkedIn (orange filled), GitHub, Google Scholar, Email (dark filled)

---

## Tab Navigation

Six tabs rendered as a sticky tab bar beneath the hero:

1. 💼 Experience
2. 🚀 Projects
3. 📜 Certifications
4. 📚 Publications
5. 🤝 Volunteering
6. 🏆 Awards

Active tab: orange text + 2px orange bottom border. Inactive: gray. Tab bar is horizontally scrollable on mobile. Vanilla JS swaps content visibility on click.

---

## Section Designs

### Experience
- Vertical timeline: orange dot + connector line on the left
- Each entry: Role (bold), Company · Type (orange), Date range (right-aligned, gray), description paragraph
- Entries ordered newest-first

### Projects
- 2-column responsive card grid (1-column on mobile)
- Each card: left orange border, project name (bold), 1-line description, tech tag chips (orange-tinted for primary, gray for secondary)
- Links: GitHub icon, live demo icon (if available)

### Certifications (Licenses & Certifications)
- 2-column responsive card grid
- Each card: colored icon/badge square (orange gradient), cert name (bold), Issuer (orange), Year (gray)
- Credential ID or verify link where available

### Publications
- Flat list, each entry: paper title (bold, linked), authors · venue · year (orange accent on venue), action pills: Paper, Scholar, DOI
- Below the list: a separate "Reviewer" subsection — workshop/conference reviewer experience as a simple tag list

### Volunteering
- Same vertical timeline style as Experience
- Each entry: Role (bold), Organization (orange), Date range, description

### Awards & Honors
- Icon + text list
- Each entry: emoji/icon (🥇🎖️🏅), Award name (bold), Issuer (orange or amber), Year (right-aligned gray)
- Ordered by most recent first

---

## Responsive Behavior

- Hero: full-width on all sizes
- Tab bar: `overflow-x: auto` with `-webkit-overflow-scrolling: touch` on mobile
- Grids (Projects, Certifications): 2-col ≥ 640px, 1-col below
- Timeline: full-width on all sizes

---

## Content Data

All content is hardcoded in `_pages/portfolio.md` (or in the layout HTML directly) using real data from the user's CV. No separate `_data` file needed for this scope.

Profile photo: `images/avt.jpg` (already exists). Real CV content (job titles, dates, cert names, paper titles, award names) will be filled in during implementation based on the user's actual data — placeholder examples in this spec are illustrative only.

---

## What is NOT in scope

- Dark/light mode toggle
- Animated section transitions
- Multi-language (vi/en) for the portfolio page
- Filtering/search within sections
- Contact form
