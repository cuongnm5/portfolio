# Portfolio UI Updates Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add email emoji, press thumbnails, and replace the lifestyle photo grid with activity cards that open a timeline modal.

**Architecture:** All changes are confined to two files — `_layouts/portfolio.html` (HTML structure + inline JS) and `_sass/4-layouts/_portfolio.scss` (styles). The existing modal overlay and `.mp-proj-card` click infrastructure is reused for lifestyle cards. A new `LIFESTYLE` JS object mirrors the `PROJECTS` object; `openModal()` checks both.

**Tech Stack:** Jekyll, Liquid, SCSS, vanilla JS (ES5)

---

### Task 1: Email emoji

**Files:**
- Modify: `_layouts/portfolio.html` (hero social icons)

- [ ] **Step 1: Replace the email icon text**

In `_layouts/portfolio.html`, find the email social icon anchor and change the inner text from `✉` to `📧`:

```html
<!-- Before -->
<a class="mp-social__icon mp-social__icon--email"
   href="mailto:s.cuongnm5@gmail.com" aria-label="Email">✉</a>

<!-- After -->
<a class="mp-social__icon mp-social__icon--email"
   href="mailto:s.cuongnm5@gmail.com" aria-label="Email">📧</a>
```

- [ ] **Step 2: Build and verify**

```bash
bundle exec jekyll build 2>&1 | grep -E "done|error"
```

Open `_site/index.html` in a browser (or check `http://localhost:4000/portfolio/` if server is running). Confirm the email circle shows 📧.

- [ ] **Step 3: Commit**

```bash
git add _layouts/portfolio.html
git commit -m "feat: replace email icon with 📧 emoji"
```

---

### Task 2: Press thumbnail CSS

**Files:**
- Modify: `_sass/4-layouts/_portfolio.scss` (modal list section)

- [ ] **Step 1: Update `.mp-modal__list li` to be a flex row and add helper classes**

In `_sass/4-layouts/_portfolio.scss`, find the `.mp-modal__list` block (around line 693). Update the `li` rule to add flex layout, and append two new classes after the closing `}` of `.mp-modal__list`:

```scss
// Inside .mp-modal__list, update the li rule:
li {
  background: var(--mp-bg);
  border-radius: 10px;
  padding: 14px 16px;
  border-left: 3px solid var(--mp-primary);
  font-size: 0.88rem;
  color: var(--mp-text);
  line-height: 1.55;
  display: flex;           // ADD
  gap: 14px;              // ADD
  align-items: flex-start; // ADD

  a {
    color: var(--mp-primary);
    text-decoration: none;
    font-weight: 600;
    display: block;
    margin-bottom: 3px;

    &:hover { text-decoration: underline; }
  }

  em {
    display: block;
    color: var(--mp-muted);
    font-size: 0.8rem;
    font-style: normal;
    margin-top: 2px;
  }
}
```

Then add these two classes immediately after the closing `}` of `.mp-modal__list`:

```scss
.mp-modal__list-item-img {
  width: 100px;
  height: 70px;
  object-fit: cover;
  border-radius: 6px;
  flex-shrink: 0;
}

.mp-modal__list-item-body {
  flex: 1;
  min-width: 0;
}
```

- [ ] **Step 2: Build and check for SCSS errors**

```bash
bundle exec jekyll build 2>&1 | grep -E "done|error|Error"
```

Expected: `done in X seconds` with no errors.

- [ ] **Step 3: Commit**

```bash
git add _sass/4-layouts/_portfolio.scss
git commit -m "feat: add flex layout and img classes to modal press list"
```

---

### Task 3: Press thumbnail data and render

**Files:**
- Modify: `_layouts/portfolio.html` (PROJECTS data + `openModal` render loop)

- [ ] **Step 1: Add `image` fields to PROJECTS press items**

In `_layouts/portfolio.html`, find the `PROJECTS` JS object. Add `image` fields to the press items in `tts` and `aicard`:

```js
tts: {
  // ...
  sections: [
    { title: '📄 Publications', items: [
      { text: 'Development of Smartcall Vietnamese TTS for VLSP 2020', sub: 'VLSP 2020 (ACL workshop)', link: null },
      { text: 'Vietnamese-English Code-Switching Speech Synthesis', sub: 'PRICAI 2021 · Springer LNAI · Q2', link: null },
      { text: 'Multi-Task Text Normalization for Speech Synthesis', sub: 'PRICAI 2021 · Springer LNAI · Q2', link: null }
    ]},
    { title: '📰 Press', items: [
      { text: 'Vietnamese AI Research Recognized at International Conference', sub: 'Znews — PRICAI 2021 coverage', link: 'https://znews.vn/nghien-cuu-ve-ai-cua-nhom-ky-su-viet-duoc-cong-nhan-cap-quoc-te-post1281985.html', image: '{{ site.baseurl }}/images/press/znews-tts.jpg' }
    ]}
  ]
},
```

```js
aicard: {
  // ...
  sections: [
    { title: '📰 Press', items: [
      { text: 'Nearly 4 Million AI Cards Created on Zalo for Women\'s Day', sub: 'Znews', link: 'https://znews.vn/gan-4-trieu-tam-thiep-ai-duoc-tao-ra-tren-zalo-trong-dip-83-post1539259.html', image: '{{ site.baseurl }}/images/press/znews-aicard.jpg' },
      { text: 'LinkedIn — Campaign highlight post', sub: 'LinkedIn', link: 'https://www.linkedin.com/feed/update/urn:li:ugcPost:7258038391957528576/', image: '{{ site.baseurl }}/images/press/linkedin-aicard.jpg' }
    ]}
  ]
},
```

- [ ] **Step 2: Update the modal list render loop to support images**

In the same file, find the `(p.sections||[]).forEach` block in `openModal`. Replace the inner `sec.items.forEach` render with:

```js
(p.sections||[]).forEach(function(sec){
  html += '<div class="mp-modal__section"><p class="mp-modal__section-title">'+esc(sec.title)+'</p><ul class="mp-modal__list">';
  sec.items.forEach(function(item){
    html += '<li>';
    if (item.image) {
      html += '<img class="mp-modal__list-item-img" src="'+esc(item.image)+'" alt="" loading="lazy">';
    }
    html += '<div class="mp-modal__list-item-body">';
    html += item.link
      ? '<a href="'+esc(item.link)+'" target="_blank" rel="noopener noreferrer">'+esc(item.text)+'</a>'
      : esc(item.text);
    if (item.sub) html += ' <em>— '+esc(item.sub)+'</em>';
    html += '</div></li>';
  });
  html += '</ul></div>';
});
```

- [ ] **Step 3: Build and verify**

```bash
bundle exec jekyll build 2>&1 | grep -E "done|error|Error"
```

Open the site, click the "Vietnamese Text-to-Speech" project card. In the modal, the Press section should show the Znews thumbnail (100×70px) to the left of the article title. Click "AI Greeting Card" — both Znews and LinkedIn thumbnails should appear.

- [ ] **Step 4: Commit**

```bash
git add _layouts/portfolio.html
git commit -m "feat: add press thumbnails to project modal"
```

---

### Task 4: Remove unused lifestyle CSS, add timeline CSS

**Files:**
- Modify: `_sass/4-layouts/_portfolio.scss` (lifestyle section at bottom)

- [ ] **Step 1: Remove the old photo-grid CSS block**

In `_sass/4-layouts/_portfolio.scss`, find and delete the entire `// ── Lifestyle` section (from the comment down through `.mp-photo-hint`). This covers these classes: `.mp-lifestyle__intro`, `.mp-photo-grid`, `.mp-photo-card`, `.mp-photo-placeholder`, `.mp-placeholder-icon`, `.mp-placeholder-hint`, `.mp-photo-card__body`, `.mp-photo-card__activity`, `.mp-photo-card__caption`, `.mp-photo-hint`.

Replace the deleted block with the new timeline CSS:

```scss
// ── Lifestyle timeline entries ─────────────────────────────────────
.mp-timeline-list {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.mp-timeline-entry {
  display: flex;
  gap: 16px;
  align-items: flex-start;
  padding: 14px;
  background: var(--mp-bg);
  border-radius: 12px;
  border-left: 3px solid var(--mp-primary);
}

.mp-timeline-entry__img {
  width: 90px;
  height: 65px;
  object-fit: cover;
  border-radius: 8px;
  flex-shrink: 0;
}

.mp-timeline-entry__placeholder {
  width: 90px;
  height: 65px;
  border-radius: 8px;
  flex-shrink: 0;
  background: linear-gradient(135deg, #FFF0E0, #FFD9B0);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.5rem;
}

.mp-timeline-entry__body { flex: 1; min-width: 0; }

.mp-timeline-entry__date {
  display: inline-block;
  font-size: 0.68rem;
  font-weight: 700;
  color: #fff;
  padding: 2px 8px;
  border-radius: 6px;
  margin-bottom: 6px;
}

.mp-timeline-entry__title {
  font-size: 0.95rem;
  font-weight: 700;
  color: var(--mp-text);
  margin: 0 0 4px;
}

.mp-timeline-entry__desc {
  font-size: 0.82rem;
  color: var(--mp-muted);
  margin: 0;
  line-height: 1.5;
}
```

- [ ] **Step 2: Build and check for errors**

```bash
bundle exec jekyll build 2>&1 | grep -E "done|error|Error"
```

Expected: clean build. (The lifestyle section will look broken in the browser until Task 5 replaces the HTML — that's expected.)

- [ ] **Step 3: Commit**

```bash
git add _sass/4-layouts/_portfolio.scss
git commit -m "feat: remove photo-grid CSS, add lifestyle timeline CSS"
```

---

### Task 5: Lifestyle section HTML — card grid

**Files:**
- Modify: `_layouts/portfolio.html` (lifestyle section)

- [ ] **Step 1: Replace the photo grid HTML**

In `_layouts/portfolio.html`, find the lifestyle section (starts with `<section id="lifestyle" ...>`). Keep the `<section>`, `<div class="mp-section__inner">`, label `<p>`, and `<h2>` unchanged. Replace everything from `<p class="mp-lifestyle__intro">` through the closing `</div>` of `.mp-section__inner` with:

```html
    <div class="mp-proj-grid">

      <div class="mp-proj-card" role="button" tabindex="0" data-modal="ls-running">
        <div class="mp-proj-card__bar"></div>
        <div class="mp-proj-card__body">
          <span class="mp-proj-card__badge">🏃 Sport</span>
          <h3 class="mp-proj-card__title">Running</h3>
          <p class="mp-proj-card__desc">Marathon training, road races &amp; endurance milestones.</p>
          <span class="mp-proj-card__cta">View journey →</span>
        </div>
      </div>

      <div class="mp-proj-card" role="button" tabindex="0" data-modal="ls-badminton">
        <div class="mp-proj-card__bar" style="background:linear-gradient(90deg,#1565C0,#42A5F5);"></div>
        <div class="mp-proj-card__body">
          <span class="mp-proj-card__badge" style="background:#E3F2FD;color:#1565C0;">🏸 Sport</span>
          <h3 class="mp-proj-card__title">Badminton</h3>
          <p class="mp-proj-card__desc">Weekly sessions, local tournaments &amp; continuous improvement.</p>
          <span class="mp-proj-card__cta" style="color:#1565C0;">View journey →</span>
        </div>
      </div>

      <div class="mp-proj-card" role="button" tabindex="0" data-modal="ls-volunteer">
        <div class="mp-proj-card__bar" style="background:linear-gradient(90deg,#2E7D32,#81C784);"></div>
        <div class="mp-proj-card__body">
          <span class="mp-proj-card__badge" style="background:#E8F5E9;color:#2E7D32;">🤝 Community</span>
          <h3 class="mp-proj-card__title">Volunteer</h3>
          <p class="mp-proj-card__desc">Teaching, organising &amp; giving back beyond code.</p>
          <span class="mp-proj-card__cta" style="color:#2E7D32;">View journey →</span>
        </div>
      </div>

    </div>
```

- [ ] **Step 2: Build and verify cards render**

```bash
bundle exec jekyll build 2>&1 | grep -E "done|error|Error"
```

Open the Lifestyle section. Confirm 3 cards appear in a row — Running (orange), Badminton (blue), Volunteer (green). Clicking any card should open the modal (currently empty body — fixed in Task 6).

- [ ] **Step 3: Commit**

```bash
git add _layouts/portfolio.html
git commit -m "feat: replace lifestyle photo grid with activity cards"
```

---

### Task 6: Lifestyle JS data and timeline modal render

**Files:**
- Modify: `_layouts/portfolio.html` (JS block)

- [ ] **Step 1: Add the LIFESTYLE data object**

In the JS block of `_layouts/portfolio.html`, immediately after the closing `};` of the `PROJECTS` object, add:

```js
var LIFESTYLE = {
  'ls-running': {
    badge: '🏃 Running',
    title: 'Running',
    desc: 'My running journey — from first steps to races.',
    color: '#E65100',
    timeline: [
      { date: '', title: 'Your milestone', desc: '', image: null },
      { date: '', title: 'Your milestone', desc: '', image: null }
    ]
  },
  'ls-badminton': {
    badge: '🏸 Badminton',
    title: 'Badminton',
    desc: 'Weekly sessions, tournaments, and skill progression.',
    color: '#1565C0',
    timeline: [
      { date: '', title: 'Your milestone', desc: '', image: null },
      { date: '', title: 'Your milestone', desc: '', image: null }
    ]
  },
  'ls-volunteer': {
    badge: '🤝 Volunteer',
    title: 'Volunteer',
    desc: 'Community involvement, teaching, and giving back.',
    color: '#2E7D32',
    timeline: [
      { date: '', title: 'Your milestone', desc: '', image: null },
      { date: '', title: 'Your milestone', desc: '', image: null }
    ]
  }
};
```

- [ ] **Step 2: Update `openModal` to support both PROJECTS and LIFESTYLE**

Replace the entire `openModal` function with:

```js
function openModal(id) {
  var p = PROJECTS[id] || LIFESTYLE[id];
  if (!p) return;

  var badgeEl = document.getElementById('mp-modal-badge');
  badgeEl.textContent = p.badge;
  badgeEl.style.background = p.color || '';

  document.getElementById('mp-modal-title').textContent = p.title;
  document.getElementById('mp-modal-desc').textContent  = p.desc;

  document.getElementById('mp-modal-tags').innerHTML =
    (p.tags||[]).map(function(t){ return '<span class="mp-tag">'+esc(t)+'</span>'; }).join('');

  var html = '';

  if (p.timeline) {
    html += '<div class="mp-timeline-list">';
    p.timeline.forEach(function(entry){
      html += '<div class="mp-timeline-entry" style="border-left-color:'+esc(p.color)+'">';
      if (entry.image) {
        html += '<img class="mp-timeline-entry__img" src="'+esc(entry.image)+'" alt="" loading="lazy">';
      } else {
        html += '<div class="mp-timeline-entry__placeholder">📷</div>';
      }
      html += '<div class="mp-timeline-entry__body">';
      if (entry.date) {
        html += '<span class="mp-timeline-entry__date" style="background:'+esc(p.color)+'">'+esc(entry.date)+'</span>';
      }
      html += '<p class="mp-timeline-entry__title">'+esc(entry.title)+'</p>';
      if (entry.desc) {
        html += '<p class="mp-timeline-entry__desc">'+esc(entry.desc)+'</p>';
      }
      html += '</div></div>';
    });
    html += '</div>';
  } else {
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
        if (item.image) {
          html += '<img class="mp-modal__list-item-img" src="'+esc(item.image)+'" alt="" loading="lazy">';
        }
        html += '<div class="mp-modal__list-item-body">';
        html += item.link
          ? '<a href="'+esc(item.link)+'" target="_blank" rel="noopener noreferrer">'+esc(item.text)+'</a>'
          : esc(item.text);
        if (item.sub) html += ' <em>— '+esc(item.sub)+'</em>';
        html += '</div></li>';
      });
      html += '</ul></div>';
    });
  }

  document.getElementById('mp-modal-body').innerHTML = html;

  overlay.classList.add('open');
  overlay.removeAttribute('aria-hidden');
  document.body.style.overflow = 'hidden';
  document.querySelector('main').setAttribute('aria-hidden', 'true');
  closeBtn.focus();
}
```

- [ ] **Step 3: Build and verify end-to-end**

```bash
bundle exec jekyll build 2>&1 | grep -E "done|error|Error"
```

Open the site and verify all of the following:

1. **Email icon** — hero shows 📧
2. **TTS modal** — Press section shows Znews thumbnail left of article title
3. **AI Card modal** — Press section shows both Znews + LinkedIn thumbnails
4. **Running card** — click opens modal with orange badge, 2 placeholder timeline rows with 📷 boxes
5. **Badminton card** — click opens modal with blue badge, 2 placeholder rows
6. **Volunteer card** — click opens modal with green badge, 2 placeholder rows
7. **Close / Esc** — modal closes correctly for all cards
8. **Existing project cards** — Publications sections unaffected (no images, still render correctly)

- [ ] **Step 4: Commit**

```bash
git add _layouts/portfolio.html
git commit -m "feat: lifestyle timeline modal with LIFESTYLE data and updated openModal"
```
