# Portfolio Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a professional `/portfolio/` page for the Jekyll blog with a dark centered-card hero, sticky orange tab bar (Experience · Projects · Certifications · Publications · Volunteering · Awards), and an orange color scheme.

**Architecture:** A new `_layouts/portfolio.html` extends `default.html` to inherit the site header/nav/footer while rendering a full-width tabbed UI. All portfolio content is hardcoded in the layout HTML — `⚠️ UPDATE` comments mark each placeholder to replace with real CV data. A scoped `_sass/4-layouts/_portfolio.scss` handles all styles under `.portfolio-page`. Tabs are driven by inline vanilla JS (no new dependencies).

**Tech Stack:** Jekyll, Liquid, SCSS (uses existing `$brand-color: #f6b141`, `$dark: #393e46`, `$global-transition` from `_sass/0-settings/_variables.scss`), vanilla JS

**Local dev URL:** `http://localhost:4000/blog/portfolio/` (because `baseurl: "/blog"` in `_config.yml`)

---

## File Map

| Action | Path | Responsibility |
|--------|------|---------------|
| Create | `_sass/4-layouts/_portfolio.scss` | All portfolio styles scoped under `.portfolio-page` |
| Modify | `_includes/main.scss` | Add `@import "4-layouts/portfolio"` after the Tag Page import |
| Create | `_layouts/portfolio.html` | Full-width layout: hero + tab bar + 6 section panels + JS |
| Create | `_pages/portfolio.md` | Front matter only: layout, title, permalink |

---

### Task 1: Create SCSS file and register import

**Files:**
- Create: `_sass/4-layouts/_portfolio.scss`
- Modify: `_includes/main.scss`

- [ ] **Step 1: Create `_sass/4-layouts/_portfolio.scss`**

Write this complete file:

```scss
// Hero
.portfolio-hero {
  background: linear-gradient(160deg, #1a1a2e 60%, #2d1a0e 100%);
  padding: 48px 20px;
  text-align: center;
  position: relative;
  overflow: hidden;

  &::before,
  &::after {
    content: '';
    position: absolute;
    border-radius: 50%;
    pointer-events: none;
  }

  &::before {
    width: 200px;
    height: 200px;
    background: rgba(246, 177, 65, 0.06);
    top: -60px;
    left: -60px;
  }

  &::after {
    width: 150px;
    height: 150px;
    background: rgba(255, 107, 53, 0.06);
    bottom: -40px;
    right: -40px;
  }
}

.portfolio-hero__photo {
  width: 96px;
  height: 96px;
  border-radius: 50%;
  border: 3px solid rgba(246, 177, 65, 0.4);
  object-fit: cover;
  margin: 0 auto;
  display: block;
}

.portfolio-hero__name {
  color: #fff;
  font-size: 26px;
  font-weight: 800;
  margin: 16px 0 6px;
  letter-spacing: 0.3px;
}

.portfolio-hero__title {
  color: $brand-color;
  font-size: 13px;
  letter-spacing: 0.5px;
  margin: 0 0 10px;
}

.portfolio-hero__bio {
  color: #9a9ab0;
  font-size: 13px;
  line-height: 1.7;
  max-width: 480px;
  margin: 0 auto 18px;
}

.portfolio-hero__links {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 8px;
}

.portfolio-hero__link {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  padding: 7px 16px;
  border-radius: 20px;
  font-size: 11px;
  font-weight: 600;
  text-decoration: none;
  transition: $global-transition;

  &--primary {
    background: #ff6b35;
    color: #fff;

    &:hover {
      background: darken(#ff6b35, 10%);
      color: #fff;
    }
  }

  &--secondary {
    background: rgba(255, 255, 255, 0.08);
    color: #ccc;
    border: 1px solid rgba(255, 255, 255, 0.15);

    &:hover {
      background: rgba(255, 255, 255, 0.16);
      color: #fff;
    }
  }
}

// Tab bar
.portfolio-tabs {
  position: sticky;
  top: 0;
  z-index: 100;
  background: #fff;
  border-bottom: 2px solid #f0f0f0;
  display: flex;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
  padding: 0 16px;

  &::-webkit-scrollbar {
    display: none;
  }
}

.portfolio-tab {
  background: none;
  border: none;
  border-bottom: 2px solid transparent;
  padding: 14px 16px;
  font-size: 11px;
  font-weight: 700;
  color: #aaa;
  cursor: pointer;
  white-space: nowrap;
  margin-bottom: -2px;
  letter-spacing: 0.5px;
  transition: color 0.2s;
  font-family: $base-font-family;

  &:hover {
    color: #ff6b35;
  }

  &.active {
    color: #ff6b35;
    border-bottom-color: #ff6b35;
  }
}

// Content area
.portfolio-content {
  background: #f9f9fb;
  min-height: 400px;
  padding: 28px 20px;
  max-width: 900px;
  margin: 0 auto;

  @media (max-width: 640px) {
    padding: 20px 16px;
  }
}

.tab-panel {
  display: none;

  &.active {
    display: block;
  }
}

.section-title {
  font-size: 11px;
  font-weight: 800;
  color: #393e46;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  margin: 0 0 20px;
  padding-bottom: 10px;
  border-bottom: 2px solid #f0e8d8;
}

// Timeline (shared by Experience + Volunteering)
.timeline {
  list-style: none;
  margin: 0;
  padding: 0;
}

.timeline-item {
  display: flex;
  gap: 16px;
  margin-bottom: 22px;

  &:last-child .timeline-track::after {
    display: none;
  }
}

.timeline-track {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex-shrink: 0;
  width: 12px;
  padding-top: 4px;

  &::before {
    content: '';
    width: 12px;
    height: 12px;
    border-radius: 50%;
    background: #ff6b35;
    flex-shrink: 0;
  }

  &::after {
    content: '';
    width: 2px;
    flex: 1;
    background: linear-gradient(to bottom, rgba(255, 107, 53, 0.3), transparent);
    margin-top: 4px;
    min-height: 24px;
  }

  &--secondary::before {
    background: $brand-color;
  }
}

.timeline-body {
  flex: 1;
  padding-bottom: 4px;
}

.timeline-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  gap: 8px;
  flex-wrap: wrap;
}

.timeline-role {
  font-size: 14px;
  font-weight: 700;
  color: #393e46;
  margin: 0;
}

.timeline-date {
  font-size: 11px;
  color: #aaa;
  white-space: nowrap;
}

.timeline-org {
  font-size: 12px;
  color: #ff6b35;
  font-weight: 600;
  margin: 4px 0 6px;
}

.timeline-desc {
  font-size: 13px;
  color: #666;
  line-height: 1.65;
  margin: 0;
}

// Card grid (shared by Projects + Certifications)
.card-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 14px;

  @media (max-width: 640px) {
    grid-template-columns: 1fr;
  }
}

.portfolio-card {
  background: #fff;
  border-radius: 10px;
  padding: 16px;
  box-shadow: 0 1px 6px rgba(0, 0, 0, 0.06);
  border-left: 3px solid #ff6b35;
  transition: box-shadow 0.2s, transform 0.2s;

  &:hover {
    box-shadow: 0 4px 16px rgba(255, 107, 53, 0.12);
    transform: translateY(-2px);
  }

  &--amber {
    border-left-color: $brand-color;
  }
}

.card-badge {
  width: 44px;
  height: 44px;
  border-radius: 8px;
  background: linear-gradient(135deg, #ff6b35, $brand-color);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  margin-bottom: 10px;
}

.card-title {
  font-size: 13px;
  font-weight: 700;
  color: #393e46;
  margin: 0 0 4px;
}

.card-sub {
  font-size: 11px;
  color: #ff6b35;
  font-weight: 600;
  margin: 0 0 2px;
}

.card-meta {
  font-size: 11px;
  color: #aaa;
  margin: 0 0 8px;
}

.card-desc {
  font-size: 12px;
  color: #666;
  line-height: 1.55;
  margin: 0 0 10px;
}

.card-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 4px;
  margin-bottom: 10px;
}

.tag {
  font-size: 10px;
  padding: 2px 8px;
  border-radius: 10px;

  &--primary {
    background: #fff3e8;
    color: #ff6b35;
  }

  &--secondary {
    background: #f0f0f0;
    color: #666;
  }
}

.card-links {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
}

.card-link {
  font-size: 11px;
  padding: 4px 10px;
  border-radius: 10px;
  text-decoration: none;
  background: #f9f9fb;
  color: #555;
  border: 1px solid #e8e8e8;
  transition: $global-transition;

  &:hover {
    background: #fff3e8;
    color: #ff6b35;
    border-color: #f0c8a8;
  }
}

// Publications
.pub-list {
  list-style: none;
  margin: 0 0 24px;
  padding: 0;
}

.pub-item {
  background: #fff;
  border-radius: 10px;
  padding: 14px 16px;
  margin-bottom: 12px;
  border-left: 3px solid #ff6b35;
  box-shadow: 0 1px 6px rgba(0, 0, 0, 0.06);
}

.pub-title {
  font-size: 13px;
  font-weight: 700;
  color: #393e46;
  margin: 0 0 5px;

  a {
    color: inherit;
    text-decoration: none;

    &:hover {
      color: #ff6b35;
    }
  }
}

.pub-meta {
  font-size: 11px;
  color: #888;
  margin: 0 0 8px;
}

.pub-venue {
  color: #ff6b35;
  font-weight: 600;
}

.pub-links {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
}

.pub-link {
  font-size: 10px;
  padding: 3px 9px;
  border-radius: 10px;
  text-decoration: none;
  background: #fff3e8;
  color: #ff6b35;
  border: 1px solid #f0c8a8;
  transition: $global-transition;

  &:hover {
    background: #ff6b35;
    color: #fff;
  }

  &--secondary {
    background: #f0f0f0;
    color: #666;
    border-color: #e0e0e0;

    &:hover {
      background: #393e46;
      color: #fff;
      border-color: #393e46;
    }
  }
}

.reviewer-section {
  margin-top: 24px;

  h4 {
    font-size: 11px;
    font-weight: 800;
    color: #393e46;
    text-transform: uppercase;
    letter-spacing: 1px;
    margin: 0 0 10px;
  }
}

.reviewer-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 6px;
}

.reviewer-tag {
  background: #fff;
  border: 1px solid #e8e8e8;
  border-radius: 6px;
  padding: 5px 12px;
  font-size: 11px;
  color: #555;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
}

// Awards
.award-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.award-item {
  background: #fff;
  border-radius: 10px;
  padding: 14px 16px;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  gap: 14px;
  box-shadow: 0 1px 6px rgba(0, 0, 0, 0.06);
  transition: box-shadow 0.2s;

  &:hover {
    box-shadow: 0 4px 12px rgba(255, 107, 53, 0.1);
  }
}

.award-icon {
  font-size: 26px;
  flex-shrink: 0;
}

.award-body {
  flex: 1;
}

.award-name {
  font-size: 13px;
  font-weight: 700;
  color: #393e46;
  margin: 0 0 3px;
}

.award-issuer {
  font-size: 11px;
  color: #ff6b35;
  margin: 0;
}

.award-year {
  font-size: 12px;
  color: #aaa;
  white-space: nowrap;
}
```

- [ ] **Step 2: Register import in `_includes/main.scss`**

Open `_includes/main.scss`. After the last import line (`@import '4-layouts/tag-page';`), append:

```scss
// 4.6 Portfolio
@import '4-layouts/portfolio';
```

- [ ] **Step 3: Verify SCSS compiles**

```bash
bundle exec jekyll build
```

Expected: build completes with no SCSS errors. Look for `done in X seconds` — any `Error: Invalid CSS` line means a typo in the SCSS.

- [ ] **Step 4: Commit**

```bash
git add _sass/4-layouts/_portfolio.scss _includes/main.scss
git commit -m "feat: add portfolio SCSS styles"
```

---

### Task 2: Create portfolio layout and page

**Files:**
- Create: `_layouts/portfolio.html`
- Create: `_pages/portfolio.md`

- [ ] **Step 1: Create `_layouts/portfolio.html`**

Write this complete file:

```html
---
layout: default
---
<div class="portfolio-page">

  <!-- HERO -->
  <div class="portfolio-hero">
    <img
      class="portfolio-hero__photo"
      src="{{ site.baseurl_root }}/images/avt.jpg"
      alt="Cuong Manh Nguyen"
      width="96"
      height="96"
    >
    <h1 class="portfolio-hero__name">Cuong Manh Nguyen</h1>
    <!-- ⚠️ UPDATE: your tagline -->
    <p class="portfolio-hero__title">AI Researcher · Software Engineer · Educator</p>
    <!-- ⚠️ UPDATE: your bio (1-2 sentences) -->
    <p class="portfolio-hero__bio">
      Passionate about natural language processing, AI for education, and building intelligent systems that make a difference. MSc in Computer Science.
    </p>
    <div class="portfolio-hero__links">
      <!-- ⚠️ UPDATE: replace href values with your real profile URLs -->
      <a href="https://linkedin.com/in/YOUR-PROFILE" class="portfolio-hero__link portfolio-hero__link--primary" target="_blank" rel="noopener noreferrer">🔗 LinkedIn</a>
      <a href="https://github.com/cuongnm5" class="portfolio-hero__link portfolio-hero__link--secondary" target="_blank" rel="noopener noreferrer">GitHub</a>
      <a href="https://scholar.google.com/citations?user=YOUR-ID" class="portfolio-hero__link portfolio-hero__link--secondary" target="_blank" rel="noopener noreferrer">Google Scholar</a>
      <a href="mailto:s.cuongnm5@gmail.com" class="portfolio-hero__link portfolio-hero__link--secondary">✉️ Email</a>
    </div>
  </div>

  <!-- TAB BAR -->
  <div class="portfolio-tabs" role="tablist" aria-label="Portfolio sections">
    <button class="portfolio-tab active" data-tab="experience" role="tab" aria-selected="true" aria-controls="panel-experience">💼 EXPERIENCE</button>
    <button class="portfolio-tab" data-tab="projects" role="tab" aria-selected="false" aria-controls="panel-projects">🚀 PROJECTS</button>
    <button class="portfolio-tab" data-tab="certifications" role="tab" aria-selected="false" aria-controls="panel-certifications">📜 CERTIFICATIONS</button>
    <button class="portfolio-tab" data-tab="publications" role="tab" aria-selected="false" aria-controls="panel-publications">📚 PUBLICATIONS</button>
    <button class="portfolio-tab" data-tab="volunteering" role="tab" aria-selected="false" aria-controls="panel-volunteering">🤝 VOLUNTEERING</button>
    <button class="portfolio-tab" data-tab="awards" role="tab" aria-selected="false" aria-controls="panel-awards">🏆 AWARDS</button>
  </div>

  <!-- CONTENT PANELS -->
  <div class="portfolio-content">

    <!-- EXPERIENCE -->
    <div id="panel-experience" class="tab-panel active" role="tabpanel">
      <p class="section-title">Career Experience</p>
      <ul class="timeline">
        <!-- ⚠️ UPDATE: replace with real job entries, newest first.
             Use timeline-track--secondary for older roles (renders amber dot).
             Copy the <li> block to add more entries. -->
        <li class="timeline-item">
          <div class="timeline-track"></div>
          <div class="timeline-body">
            <div class="timeline-header">
              <h3 class="timeline-role">Senior AI Engineer</h3>
              <span class="timeline-date">2022 — Present</span>
            </div>
            <p class="timeline-org">Company Name · Full-time</p>
            <p class="timeline-desc">Led development of NLP pipelines and deployed machine learning models to production. Mentored junior engineers and collaborated with cross-functional teams on AI product strategy.</p>
          </div>
        </li>
        <li class="timeline-item">
          <div class="timeline-track timeline-track--secondary"></div>
          <div class="timeline-body">
            <div class="timeline-header">
              <h3 class="timeline-role">ML Research Intern</h3>
              <span class="timeline-date">2021 — 2022</span>
            </div>
            <p class="timeline-org">Research Lab · Internship</p>
            <p class="timeline-desc">Researched transformer-based models for low-resource NLP tasks in Vietnamese. Contributed to publications at international conferences.</p>
          </div>
        </li>
      </ul>
    </div>

    <!-- PROJECTS -->
    <div id="panel-projects" class="tab-panel" role="tabpanel">
      <p class="section-title">Projects</p>
      <div class="card-grid">
        <!-- ⚠️ UPDATE: replace with real projects.
             Add portfolio-card--amber for alternating border color.
             Copy the <div class="portfolio-card"> block to add more. -->
        <div class="portfolio-card">
          <h3 class="card-title">ViNLP Toolkit</h3>
          <p class="card-desc">Vietnamese NLP preprocessing library with tokenizer, POS tagger, and named entity recognition.</p>
          <div class="card-tags">
            <span class="tag tag--primary">Python</span>
            <span class="tag tag--secondary">NLP</span>
            <span class="tag tag--secondary">Open Source</span>
          </div>
          <div class="card-links">
            <a href="#" class="card-link">GitHub</a>
          </div>
        </div>
        <div class="portfolio-card portfolio-card--amber">
          <h3 class="card-title">EduBot</h3>
          <p class="card-desc">AI-powered chatbot for K-12 students answering curriculum questions using large language models.</p>
          <div class="card-tags">
            <span class="tag tag--primary">LLM</span>
            <span class="tag tag--secondary">React</span>
            <span class="tag tag--secondary">FastAPI</span>
          </div>
          <div class="card-links">
            <a href="#" class="card-link">Demo</a>
            <a href="#" class="card-link">GitHub</a>
          </div>
        </div>
      </div>
    </div>

    <!-- CERTIFICATIONS -->
    <div id="panel-certifications" class="tab-panel" role="tabpanel">
      <p class="section-title">Licenses &amp; Certifications</p>
      <div class="card-grid">
        <!-- ⚠️ UPDATE: replace with real certifications.
             Pick badge emoji: 🎓 📜 🏅 🔐 ☁️
             Add portfolio-card--amber for alternating border color.
             Copy <div class="portfolio-card"> to add more. -->
        <div class="portfolio-card">
          <div class="card-badge">🎓</div>
          <h3 class="card-title">AWS Solutions Architect</h3>
          <p class="card-sub">Amazon Web Services</p>
          <p class="card-meta">Issued 2023</p>
          <div class="card-links">
            <a href="#" class="card-link">Verify</a>
          </div>
        </div>
        <div class="portfolio-card portfolio-card--amber">
          <div class="card-badge">📜</div>
          <h3 class="card-title">Deep Learning Specialization</h3>
          <p class="card-sub">Coursera / DeepLearning.AI</p>
          <p class="card-meta">Issued 2022</p>
          <div class="card-links">
            <a href="#" class="card-link">Verify</a>
          </div>
        </div>
      </div>
    </div>

    <!-- PUBLICATIONS -->
    <div id="panel-publications" class="tab-panel" role="tabpanel">
      <p class="section-title">Publications</p>
      <ul class="pub-list">
        <!-- ⚠️ UPDATE: replace with real publications.
             Copy <li class="pub-item"> to add more entries. -->
        <li class="pub-item">
          <p class="pub-title"><a href="#">Vietnamese Named Entity Recognition using PhoBERT</a></p>
          <p class="pub-meta">Cuong M. Nguyen et al. · <span class="pub-venue">EMNLP 2023</span> Workshop</p>
          <div class="pub-links">
            <a href="#" class="pub-link">📄 Paper</a>
            <a href="#" class="pub-link pub-link--secondary">🔗 Scholar</a>
            <a href="#" class="pub-link pub-link--secondary">DOI</a>
          </div>
        </li>
      </ul>
      <div class="reviewer-section">
        <h4>Workshop Reviewer</h4>
        <div class="reviewer-tags">
          <!-- ⚠️ UPDATE: add <span class="reviewer-tag"> for each venue you reviewed for -->
          <span class="reviewer-tag">ACL 2023</span>
          <span class="reviewer-tag">EMNLP 2022</span>
        </div>
      </div>
    </div>

    <!-- VOLUNTEERING -->
    <div id="panel-volunteering" class="tab-panel" role="tabpanel">
      <p class="section-title">Volunteering</p>
      <ul class="timeline">
        <!-- ⚠️ UPDATE: replace with real volunteering entries.
             Same template as Experience. Copy <li class="timeline-item"> to add more. -->
        <li class="timeline-item">
          <div class="timeline-track"></div>
          <div class="timeline-body">
            <div class="timeline-header">
              <h3 class="timeline-role">AI Education Volunteer</h3>
              <span class="timeline-date">2021 — Present</span>
            </div>
            <p class="timeline-org">Vietnam AI Community</p>
            <p class="timeline-desc">Ran free workshops on machine learning fundamentals for university students across Vietnam. Contributed to open educational resources.</p>
          </div>
        </li>
      </ul>
    </div>

    <!-- AWARDS -->
    <div id="panel-awards" class="tab-panel" role="tabpanel">
      <p class="section-title">Honors &amp; Awards</p>
      <ul class="award-list">
        <!-- ⚠️ UPDATE: replace with real awards, newest first.
             Pick icon: 🥇 🥈 🎖️ 🏅 🏆 🌟
             Copy <li class="award-item"> to add more. -->
        <li class="award-item">
          <span class="award-icon">🥇</span>
          <div class="award-body">
            <p class="award-name">Best Paper Award</p>
            <p class="award-issuer">EMNLP 2023 Workshop</p>
          </div>
          <span class="award-year">2023</span>
        </li>
        <li class="award-item">
          <span class="award-icon">🎖️</span>
          <div class="award-body">
            <p class="award-name">National AI Hackathon — 1st Place</p>
            <p class="award-issuer">Ministry of Science &amp; Technology</p>
          </div>
          <span class="award-year">2022</span>
        </li>
      </ul>
    </div>

  </div><!-- .portfolio-content -->

  <!-- TAB SWITCHING JS -->
  <script>
    (function () {
      var tabs = document.querySelectorAll('.portfolio-tab');
      tabs.forEach(function (tab) {
        tab.addEventListener('click', function () {
          var target = this.getAttribute('data-tab');
          tabs.forEach(function (t) {
            t.classList.remove('active');
            t.setAttribute('aria-selected', 'false');
          });
          document.querySelectorAll('.tab-panel').forEach(function (p) {
            p.classList.remove('active');
          });
          this.classList.add('active');
          this.setAttribute('aria-selected', 'true');
          document.getElementById('panel-' + target).classList.add('active');
        });
      });
    }());
  </script>

</div><!-- .portfolio-page -->
```

- [ ] **Step 2: Create `_pages/portfolio.md`**

Write this complete file:

```markdown
---
layout: portfolio
title: Portfolio
permalink: /portfolio/
---
```

- [ ] **Step 3: Build and verify page is generated**

```bash
bundle exec jekyll build
```

Expected: no errors and `_site/blog/portfolio/index.html` exists:

```bash
ls _site/blog/portfolio/
```

Expected output: `index.html`

- [ ] **Step 4: Visual check**

```bash
bundle exec jekyll serve
```

Visit `http://localhost:4000/blog/portfolio/`. Verify:
- Dark gradient hero with photo (`avt.jpg`), name, tagline, bio, and 4 social link pills
- White sticky tab bar with 6 orange-accented tabs below the hero
- Experience tab is active by default showing a 2-item timeline
- Clicking each tab switches the visible panel correctly
- No broken or unstyled sections

- [ ] **Step 5: Commit**

```bash
git add _layouts/portfolio.html _pages/portfolio.md
git commit -m "feat: add portfolio page layout with all six sections"
```

---

### Task 3: Verify nav link (no code change expected)

The existing nav loop in `_includes/header.html` auto-generates links for all pages with a `title`:

```liquid
{% for page in site.pages %}
  {% if page.title %}
  <li class="nav__item">
    <a href="{{ page.url | prepend: site.baseurl }}" class="nav__link">{{ page.title }}</a>
  </li>
  {% endif %}
{% endfor %}
```

Because `_pages/portfolio.md` has `title: Portfolio`, the nav link appears automatically.

- [ ] **Step 1: Start the dev server if not already running**

```bash
bundle exec jekyll serve
```

- [ ] **Step 2: Verify nav link**

Visit `http://localhost:4000/blog/`. Confirm the site header nav includes a **Portfolio** link. Click it — must route to `http://localhost:4000/blog/portfolio/` and display the portfolio page correctly.

- [ ] **Step 3: Only if nav link is missing — add manually**

If the auto-link did not appear (e.g. Jekyll version quirk), open `_includes/header.html` and add this line inside the `<ul class="nav__list list-reset">` block, after the Home `<li>`:

```html
<li class="nav__item">
  <a href="{{ site.baseurl }}/portfolio/" class="nav__link">Portfolio</a>
</li>
```

Then commit:

```bash
git add _includes/header.html
git commit -m "feat: add Portfolio nav link"
```

---

### Task 4: Replace placeholder content with real CV data

All `⚠️ UPDATE` comments in `_layouts/portfolio.html` mark placeholder data. This task replaces every placeholder with real content from your CV (the `CuongManhNguyen_Portfolio.pdf`).

**Files:**
- Modify: `_layouts/portfolio.html`

**Template reference — use these for each section:**

**Experience / Volunteering entry:**
```html
<li class="timeline-item">
  <div class="timeline-track"></div><!-- use timeline-track--secondary for non-current roles -->
  <div class="timeline-body">
    <div class="timeline-header">
      <h3 class="timeline-role">ROLE TITLE</h3>
      <span class="timeline-date">START_YEAR — END_YEAR (or Present)</span>
    </div>
    <p class="timeline-org">ORGANIZATION · TYPE (Full-time / Part-time / Contract / Internship)</p>
    <p class="timeline-desc">DESCRIPTION OF RESPONSIBILITIES AND ACHIEVEMENTS.</p>
  </div>
</li>
```

**Project card:**
```html
<div class="portfolio-card"><!-- add portfolio-card--amber for alternating accent color -->
  <h3 class="card-title">PROJECT NAME</h3>
  <p class="card-desc">ONE OR TWO SENTENCE DESCRIPTION.</p>
  <div class="card-tags">
    <span class="tag tag--primary">PRIMARY TECH</span>
    <span class="tag tag--secondary">OTHER TECH</span>
  </div>
  <div class="card-links">
    <a href="GITHUB_URL" class="card-link">GitHub</a>
    <a href="DEMO_URL" class="card-link">Demo</a><!-- remove if no demo -->
  </div>
</div>
```

**Certification card:**
```html
<div class="portfolio-card"><!-- add portfolio-card--amber for alternating accent color -->
  <div class="card-badge">🎓</div><!-- pick: 🎓 📜 🏅 🔐 ☁️ -->
  <h3 class="card-title">CERTIFICATION NAME</h3>
  <p class="card-sub">ISSUING ORGANIZATION</p>
  <p class="card-meta">Issued YEAR · Credential ID: XXXXXXXX</p>
  <div class="card-links">
    <a href="VERIFY_URL" class="card-link">Verify</a><!-- remove if no verify link -->
  </div>
</div>
```

**Publication entry:**
```html
<li class="pub-item">
  <p class="pub-title"><a href="PAPER_URL">FULL PAPER TITLE</a></p>
  <p class="pub-meta">AUTHOR1, AUTHOR2, ... · <span class="pub-venue">VENUE YEAR</span></p>
  <div class="pub-links">
    <a href="PAPER_URL" class="pub-link">📄 Paper</a>
    <a href="SCHOLAR_URL" class="pub-link pub-link--secondary">🔗 Scholar</a>
    <a href="DOI_URL" class="pub-link pub-link--secondary">DOI</a><!-- remove if no DOI -->
  </div>
</li>
```

**Reviewer tag:**
```html
<span class="reviewer-tag">VENUE YEAR</span>
```

**Award entry:**
```html
<li class="award-item">
  <span class="award-icon">🥇</span><!-- pick: 🥇 🥈 🎖️ 🏅 🏆 🌟 -->
  <div class="award-body">
    <p class="award-name">AWARD NAME</p>
    <p class="award-issuer">ISSUING ORGANIZATION</p>
  </div>
  <span class="award-year">YEAR</span>
</li>
```

- [ ] **Step 1: Update hero section**

In `_layouts/portfolio.html`, update these three fields:

```html
<p class="portfolio-hero__title">YOUR REAL TAGLINE HERE</p>
<p class="portfolio-hero__bio">
  YOUR REAL BIO (1-2 sentences).
</p>
```

And update the `href` values on the four social link `<a>` tags.

- [ ] **Step 2: Replace Experience entries**

Delete the two placeholder `<li class="timeline-item">` blocks inside `#panel-experience`. Add real entries using the template above.

- [ ] **Step 3: Replace Projects entries**

Delete the two placeholder `<div class="portfolio-card">` blocks inside `#panel-projects`. Add real entries using the template above.

- [ ] **Step 4: Replace Certifications entries**

Delete the two placeholder `<div class="portfolio-card">` blocks inside `#panel-certifications`. Add real entries using the template above.

- [ ] **Step 5: Replace Publications entries**

Delete the placeholder `<li class="pub-item">` inside `#panel-publications`. Add real publications using the template above. Replace the two `<span class="reviewer-tag">` entries with real reviewer experience.

- [ ] **Step 6: Replace Volunteering entries**

Delete the placeholder `<li class="timeline-item">` inside `#panel-volunteering`. Add real entries using the same template as Experience.

- [ ] **Step 7: Replace Awards entries**

Delete the two placeholder `<li class="award-item">` blocks inside `#panel-awards`. Add real awards using the template above.

- [ ] **Step 8: Build and final visual verification**

```bash
bundle exec jekyll build && bundle exec jekyll serve
```

Visit `http://localhost:4000/blog/portfolio/`. Check each tab:
- All 6 tabs switch content correctly
- No `#` placeholder `href` values remain in social links
- Page looks correct on a narrow viewport (shrink browser to ~375px width): tab bar scrolls horizontally, card grids stack to 1 column

- [ ] **Step 9: Commit**

```bash
git add _layouts/portfolio.html
git commit -m "feat: populate portfolio with real CV content"
```
