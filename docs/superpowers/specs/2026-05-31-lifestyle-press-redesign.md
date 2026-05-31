# Portfolio UI Updates — 2026-05-31

## Scope

Four targeted changes to `_layouts/portfolio.html` and `_sass/4-layouts/_portfolio.scss`.

---

## 1. Email icon → emoji

- In the hero social icons, replace the `✉` text inside `.mp-social__icon--email` with `📧`.

---

## 2. Press thumbnails (manual image slots)

**Where:** Project detail modal — press items inside each project's `sections` data.

**Data change:** Add optional `image` field to press items in the `PROJECTS` JS object.
```js
{ text: '...', sub: '...', link: '...', image: 'images/press/znews-tts.jpg' }
```

**Render change:** When building the `mp-modal__list` HTML for a section, if `item.image` is set, render a thumbnail `<img>` (100×70px, `object-fit:cover`, `border-radius:6px`) to the left of the text block. Wrap both in a flex row. If `item.image` is null/undefined, render the existing layout unchanged.

**Images already downloaded to `images/press/`:**
- `znews-tts.jpg` — TTS Znews article
- `znews-aicard.jpg` — AI Card Znews article
- `linkedin-aicard.jpg` — AI Card LinkedIn post

**CSS:** Add `.mp-modal__list-item-img` (width:100px, height:70px, object-fit:cover, border-radius:6px, flex-shrink:0) and wrap the existing `li` content in a flex row.

---

## 3. Lifestyle section — card grid

**Replace** the current `.mp-photo-grid` photo grid (and `.mp-photo-hint` note) with a 3-column `.mp-proj-grid` card grid identical in structure to the Projects section.

**Cards:**

| Activity | Emoji | Badge label | Badge color | Accent gradient |
|---|---|---|---|---|
| Running | 🏃 | Sport | Orange `#E65100` | `#E65100 → #FFB347` |
| Badminton | 🏸 | Sport | Blue `#1565C0` | `#1565C0 → #42A5F5` |
| Volunteer | 🤝 | Community | Green `#2E7D32` | `#2E7D32 → #81C784` |

Each card: top color bar, badge, title, one-line description, "View journey →" CTA. `data-modal` attribute targets the lifestyle modal (`data-modal="ls-running"` etc.).

**Remove** from SCSS: `.mp-photo-grid`, `.mp-photo-card`, `.mp-photo-placeholder`, `.mp-placeholder-icon`, `.mp-placeholder-hint`, `.mp-photo-card__body`, `.mp-photo-card__activity`, `.mp-photo-card__caption`, `.mp-photo-hint`, `.mp-lifestyle__intro`. These classes are unused after the redesign.

---

## 4. Lifestyle timeline modal

**Reuses** the existing `#mp-modal-overlay` / `.mp-modal` overlay — no new DOM element.

**Data:** A separate `LIFESTYLE` JS object keyed by `ls-running`, `ls-badminton`, `ls-volunteer`. Each entry has `badge`, `title`, `desc`, `color` (accent hex), and `timeline` array. Each timeline item: `{ date, title, desc, image }` — all fields can be empty strings / null as placeholders.

**Render:** The modal body renders the timeline as Option B card rows:
```
[ 📷 image or placeholder ] | date badge · bold title · description text
```
Each row: `background:#FFF8F0`, `border-radius:12px`, `border-left:3px solid <color>`, flex row with 90×65px image/placeholder on left.

**Trigger:** Lifestyle cards use the same `.mp-proj-card` click listener. `openModal()` checks both `PROJECTS` and `LIFESTYLE` objects by key.

**Placeholder entries:** 2 entries per activity, all fields empty — `date:''`, `title:'Your milestone'`, `desc:''`, `image:null`. User replaces these with real content.

**SCSS:** Add `.mp-timeline-entry` (flex row, gap 16px, padding 14px, background `#FFF8F0`, border-radius 12px, border-left 3px), `.mp-timeline-entry__img` (90×65px, object-fit cover, border-radius 8px, flex-shrink 0), `.mp-timeline-entry__placeholder` (same size, gradient background, center emoji), `.mp-timeline-entry__date` (pill badge, small font, white text, background = activity color), `.mp-timeline-entry__title` (bold, dark), `.mp-timeline-entry__desc` (small, muted).

---

## Files changed

| File | Change |
|---|---|
| `_layouts/portfolio.html` | Email emoji, lifestyle card HTML, lifestyle JS data + openModal update |
| `_sass/4-layouts/_portfolio.scss` | Press thumbnail CSS, timeline entry CSS, remove unused photo-grid CSS |
| `images/press/*.jpg` | Already present |
