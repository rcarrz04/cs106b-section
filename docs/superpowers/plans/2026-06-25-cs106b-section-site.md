# CS106B Section Website Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build and deploy a single-page static CS106B section website for Ruben Carrazco at `https://rcarrz04.github.io/cs106b-section/`.

**Architecture:** One `index.html` with all content, one `styles.css` with all styles, no JavaScript, no build step. CSS uses custom properties for the full color palette. All asset links are relative so GitHub Pages subdirectory path works without configuration.

**Tech Stack:** HTML5, CSS3 (custom properties, Flexbox, CSS Grid), Google Fonts (Libre Baskerville + IBM Plex Sans), GitHub Pages.

---

## File Map

| File | Responsibility |
|---|---|
| `index.html` | All page content and structure |
| `styles.css` | All styles — CSS variables, sidebar, main content, each section |
| `img/.gitkeep` | Placeholder until `ruben.jpg` is added |
| `files/.gitkeep` | Placeholder for uploaded PDFs |

---

### Task 1: Scaffold — HTML shell, CSS variables, directory structure

**Files:**
- Create: `index.html`
- Create: `styles.css`
- Create: `img/.gitkeep`
- Create: `files/.gitkeep`

- [ ] **Step 1: Create directory structure**

```bash
mkdir -p img files
touch img/.gitkeep files/.gitkeep
```

- [ ] **Step 2: Create `styles.css` with CSS custom properties and reset**

```css
*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

:root {
  --sidebar-w: 200px;
  --bg: #FAFAF8;
  --sidebar-bg: #1C1917;
  --sidebar-text: #E7E5E4;
  --sidebar-muted: #78716C;
  --accent: #C2773B;
  --body-text: #292524;
  --muted: #78716C;
  --divider: #E7E5E4;
  --card-bg: #FFFFFF;
  --tag-bg: #FEF3C7;
  --tag-text: #92400E;
}

html, body {
  height: 100%;
  background: var(--bg);
  font-family: 'IBM Plex Sans', sans-serif;
  font-weight: 300;
  color: var(--body-text);
  font-size: 15px;
  line-height: 1.6;
}

.layout {
  display: flex;
  min-height: 100vh;
}
```

- [ ] **Step 3: Create `index.html` shell**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CS106B — Ruben's Section</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link href="https://fonts.googleapis.com/css2?family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&family=IBM+Plex+Sans:wght@300;400;500&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <div class="layout">
    <!-- sidebar goes here -->
    <!-- main goes here -->
  </div>
</body>
</html>
```

- [ ] **Step 4: Open in browser and confirm blank page loads without errors**

```bash
open index.html
```

Open browser DevTools → Console. Expected: no errors, page is blank off-white (`#FAFAF8`).

- [ ] **Step 5: Commit**

```bash
git add index.html styles.css img/.gitkeep files/.gitkeep
git commit -m "feat: scaffold HTML shell and CSS variables"
```

---

### Task 2: Sidebar

**Files:**
- Modify: `index.html` — add `<aside class="sidebar">` inside `.layout`
- Modify: `styles.css` — add sidebar styles

- [ ] **Step 1: Add sidebar HTML inside `.layout` in `index.html`**

Replace `<!-- sidebar goes here -->` with:

```html
<aside class="sidebar">
  <div class="sidebar-name">Ruben Carrazco</div>
  <div class="sidebar-course">CS106B · Section [#]</div>
  <nav>
    <a href="#welcome" class="nav-link active">Welcome</a>
    <a href="#announcements" class="nav-link">Announcements</a>
    <a href="#slides" class="nav-link">Slides &amp; Handouts</a>
    <a href="#oh" class="nav-link">Office Hours</a>
    <a href="#resources" class="nav-link">Resources</a>
  </nav>
  <div class="sidebar-meta">
    Fri<br>
    10:30 AM<br>
    <span style="margin-top: 0.3rem; display: block;">CoDa B42</span>
  </div>
</aside>
```

- [ ] **Step 2: Add sidebar styles to `styles.css`**

```css
/* ── Sidebar ── */
.sidebar {
  width: var(--sidebar-w);
  background: var(--sidebar-bg);
  position: fixed;
  top: 0; left: 0; bottom: 0;
  padding: 2.5rem 1.5rem;
  display: flex;
  flex-direction: column;
}

.sidebar-name {
  font-family: 'Libre Baskerville', serif;
  font-size: 1.05rem;
  font-weight: 700;
  color: #FAFAF8;
  letter-spacing: -0.01em;
  line-height: 1.2;
  margin-bottom: 0.25rem;
}

.sidebar-course {
  font-size: 0.7rem;
  color: var(--sidebar-muted);
  text-transform: uppercase;
  letter-spacing: 0.08em;
  margin-bottom: 2.5rem;
}

.sidebar nav {
  display: flex;
  flex-direction: column;
  gap: 0.1rem;
  flex: 1;
}

.nav-link {
  font-size: 0.78rem;
  color: var(--sidebar-muted);
  text-decoration: none;
  padding: 0.35rem 0;
  transition: color 0.15s;
}
.nav-link:hover { color: var(--sidebar-text); }
.nav-link.active { color: var(--sidebar-text); font-weight: 500; }

.sidebar-meta {
  margin-top: auto;
  padding-top: 2rem;
  border-top: 1px solid #292524;
  font-size: 0.68rem;
  color: var(--sidebar-muted);
  line-height: 1.9;
}

/* ── Main content area (offset by sidebar) ── */
.main {
  margin-left: var(--sidebar-w);
  flex: 1;
  padding: 3rem 3.5rem;
  max-width: 760px;
}
```

- [ ] **Step 3: Verify in browser**

```bash
open index.html
```

Expected: dark charcoal sidebar on the left, 200px wide, with name "Ruben Carrazco", course tag, nav links, and time/room at the bottom. Off-white area to the right.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat: add fixed dark sidebar with nav"
```

---

### Task 3: Main layout wrapper + Welcome section

**Files:**
- Modify: `index.html` — add `<main class="main">` and welcome section
- Modify: `styles.css` — add section block, hero row, and divider styles

- [ ] **Step 1: Add main wrapper and welcome section in `index.html`**

Replace `<!-- main goes here -->` with:

```html
<main class="main">

  <!-- Welcome -->
  <div class="section-block" id="welcome">
    <div class="section-label">Welcome</div>
    <div class="hero-row">
      <div class="hero-photo">
        <!-- Replace img src with img/ruben.jpg when ready -->
        <div class="hero-photo-placeholder">photo</div>
      </div>
      <div class="hero-text">
        <h1>Hi, I'm Ruben.</h1>
        <p>I'm your section leader for CS106B this quarter. Here you'll find weekly slides, announcements, office hours, and useful links. Feel free to reach out anytime — I'm here to help.</p>
      </div>
    </div>
    <hr class="divider">
  </div>

  <!-- remaining sections go here -->

</main>
```

- [ ] **Step 2: Add welcome section styles to `styles.css`**

```css
/* ── Shared section structure ── */
.section-block { margin-bottom: 3.5rem; }

.section-label {
  font-size: 0.65rem;
  font-weight: 500;
  text-transform: uppercase;
  letter-spacing: 0.12em;
  color: var(--muted);
  margin-bottom: 1.25rem;
}

hr.divider {
  border: none;
  border-top: 1px solid var(--divider);
  margin: 2rem 0;
}

/* ── Welcome / Hero ── */
.hero-row {
  display: flex;
  align-items: flex-start;
  gap: 1.75rem;
}

.hero-photo {
  width: 108px;
  height: 128px;
  border-radius: 6px;
  flex-shrink: 0;
  overflow: hidden;
}

.hero-photo img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.hero-photo-placeholder {
  width: 100%;
  height: 100%;
  background: #D6D3D1;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #A8A29E;
  font-size: 0.7rem;
  letter-spacing: 0.04em;
}

.hero-text h1 {
  font-family: 'Libre Baskerville', serif;
  font-size: 1.5rem;
  font-weight: 400;
  color: var(--body-text);
  margin-bottom: 0.5rem;
  line-height: 1.2;
}

.hero-text p {
  font-size: 0.85rem;
  color: var(--muted);
  max-width: 380px;
  line-height: 1.65;
}
```

- [ ] **Step 3: Verify in browser**

```bash
open index.html
```

Expected: "Welcome" label, then a gray placeholder box (108×128px) beside "Hi, I'm Ruben." heading and paragraph text. Amber-tinted divider line below.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat: add welcome section with hero photo placeholder"
```

---

### Task 4: Announcements section

**Files:**
- Modify: `index.html` — add announcements section after welcome
- Modify: `styles.css` — add announcement card styles

- [ ] **Step 1: Add announcements HTML in `index.html`**

Replace `<!-- remaining sections go here -->` with:

```html
  <!-- Announcements -->
  <div class="section-block" id="announcements">
    <div class="section-label">Announcements</div>
    <div class="announce">
      <div class="announce-text">Welcome to CS106B! Slides from the first section are posted below.</div>
      <div class="announce-date">September 26, 2026</div>
    </div>
  </div>

  <!-- slides, oh, resources go here -->
```

- [ ] **Step 2: Add announcement styles to `styles.css`**

```css
/* ── Announcements ── */
.announce {
  background: var(--tag-bg);
  border-left: 2px solid var(--accent);
  padding: 0.7rem 1rem;
  border-radius: 0 4px 4px 0;
  margin-bottom: 0.75rem;
}

.announce-text {
  font-size: 0.82rem;
  color: var(--tag-text);
}

.announce-date {
  font-size: 0.68rem;
  color: #A16207;
  margin-top: 0.2rem;
}

.announce.stale {
  opacity: 0.5;
}
```

- [ ] **Step 3: Verify in browser**

Expected: "Announcements" label, then an amber-left-bordered card with announcement text and a date below it.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat: add announcements section"
```

---

### Task 5: Slides & Handouts section

**Files:**
- Modify: `index.html` — add slides section
- Modify: `styles.css` — add slides table styles

- [ ] **Step 1: Add slides HTML in `index.html`**

Replace `<!-- slides, oh, resources go here -->` with:

```html
  <!-- Slides & Handouts -->
  <div class="section-block" id="slides">
    <div class="section-label">Slides &amp; Handouts</div>
    <table class="slides-list">
      <tbody>
        <tr>
          <td class="week-label">Wk 1</td>
          <td>Introduction &amp; C++ Basics</td>
          <td><a href="files/week1.pdf" class="dl-link" download>PDF ↓</a></td>
        </tr>
      </tbody>
    </table>
  </div>

  <!-- oh, resources go here -->
```

- [ ] **Step 2: Add slides table styles to `styles.css`**

```css
/* ── Slides table ── */
.slides-list {
  width: 100%;
  border-collapse: collapse;
}

.slides-list tr {
  border-bottom: 1px solid var(--divider);
}

.slides-list tr:last-child {
  border-bottom: none;
}

.slides-list td {
  padding: 0.75rem 0;
  font-size: 0.82rem;
  vertical-align: middle;
}

.slides-list .week-label {
  color: var(--muted);
  font-size: 0.7rem;
  width: 56px;
}

.slides-list .dl-link {
  color: var(--accent);
  text-decoration: none;
  font-size: 0.75rem;
  font-weight: 500;
  letter-spacing: 0.03em;
  display: block;
  text-align: right;
}

.slides-list .dl-link:hover {
  text-decoration: underline;
}

.slides-list tr.past td {
  opacity: 0.4;
}
```

- [ ] **Step 3: Verify in browser**

Expected: "Slides & Handouts" label, then a clean table row: "Wk 1" in muted text, "Introduction & C++ Basics", and "PDF ↓" in amber right-aligned. Bottom border separates rows.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat: add slides and handouts table"
```

---

### Task 6: Office Hours section

**Files:**
- Modify: `index.html` — add office hours section
- Modify: `styles.css` — add OH card grid styles

- [ ] **Step 1: Add office hours HTML in `index.html`**

Replace `<!-- oh, resources go here -->` with:

```html
  <!-- Office Hours -->
  <div class="section-block" id="oh">
    <div class="section-label">Office Hours</div>
    <div class="oh-grid">
      <div class="oh-card">
        <div class="oh-day">Friday</div>
        <div class="oh-time">11:00 AM – 12:30 PM</div>
        <div class="oh-loc">CoDa B42</div>
      </div>
      <div class="oh-card">
        <div class="oh-day">Wednesday</div>
        <div class="oh-time">TBD</div>
        <div class="oh-loc">Zoom — <a href="#" style="color: var(--accent);">link</a></div>
      </div>
    </div>
  </div>

  <!-- resources go here -->
```

- [ ] **Step 2: Add OH styles to `styles.css`**

```css
/* ── Office Hours ── */
.oh-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.oh-card {
  background: var(--card-bg);
  border: 1px solid var(--divider);
  border-radius: 6px;
  padding: 1rem 1.25rem;
}

.oh-day {
  font-size: 0.7rem;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  color: var(--muted);
  margin-bottom: 0.25rem;
}

.oh-time {
  font-family: 'Libre Baskerville', serif;
  font-size: 1rem;
  color: var(--body-text);
}

.oh-loc {
  font-size: 0.75rem;
  color: var(--muted);
  margin-top: 0.2rem;
}
```

- [ ] **Step 3: Verify in browser**

Expected: "Office Hours" label, then two white cards side by side — each with a day (uppercase muted), serif time, and location below. Cards have a subtle border.

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat: add office hours section"
```

---

### Task 7: Resources section

**Files:**
- Modify: `index.html` — add resources section
- Modify: `styles.css` — add resource list styles

- [ ] **Step 1: Add resources HTML in `index.html`**

Replace `<!-- resources go here -->` with:

```html
  <!-- Resources -->
  <div class="section-block" id="resources">
    <div class="section-label">Resources</div>
    <a href="https://en.cppreference.com/" class="resource-item" target="_blank" rel="noopener">
      <div class="resource-icon">C++</div>
      <div>
        <div>cppreference.com</div>
        <div class="resource-desc">Standard library reference</div>
      </div>
    </a>
    <a href="https://web.stanford.edu/class/cs106b/style-guide" class="resource-item" target="_blank" rel="noopener">
      <div class="resource-icon">📋</div>
      <div>
        <div>Course Style Guide</div>
        <div class="resource-desc">Stanford CS106B code style expectations</div>
      </div>
    </a>
    <a href="https://cs106b.stanford.edu/past-exams" class="resource-item" target="_blank" rel="noopener">
      <div class="resource-icon">📝</div>
      <div>
        <div>Past Exams</div>
        <div class="resource-desc">Previous midterms and finals with solutions</div>
      </div>
    </a>
    <a href="https://edstem.org" class="resource-item" target="_blank" rel="noopener">
      <div class="resource-icon">🔗</div>
      <div>
        <div>Ed Discussion</div>
        <div class="resource-desc">Course Q&amp;A forum</div>
      </div>
    </a>
  </div>
```

- [ ] **Step 2: Add resource list styles to `styles.css`**

```css
/* ── Resources ── */
.resource-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.65rem 0;
  border-bottom: 1px solid var(--divider);
  text-decoration: none;
  color: var(--body-text);
  font-size: 0.82rem;
  transition: color 0.12s;
}

.resource-item:last-child { border-bottom: none; }
.resource-item:hover { color: var(--accent); }

.resource-icon {
  width: 28px;
  height: 28px;
  border-radius: 4px;
  background: var(--divider);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.7rem;
  flex-shrink: 0;
  color: var(--muted);
}

.resource-desc {
  font-size: 0.7rem;
  color: var(--muted);
  margin-top: 0.05rem;
}
```

- [ ] **Step 3: Verify in browser — full page check**

```bash
open index.html
```

Expected full-page checklist:
- Sidebar: name, course, nav links, time/room at bottom ✓
- Welcome: photo placeholder beside intro text, divider ✓
- Announcements: amber card with text and date ✓
- Slides: table row with week, topic, PDF link ✓
- Office Hours: two-column card grid ✓
- Resources: 4 rows with icons, titles, descriptions ✓
- Clicking nav links scrolls to correct sections ✓

- [ ] **Step 4: Commit**

```bash
git add index.html styles.css
git commit -m "feat: add resources section — site content complete"
```

---

### Task 8: Add profile photo

**Files:**
- Add: `img/ruben.jpg` (user-provided)
- Modify: `index.html` — replace placeholder div with `<img>` tag

- [ ] **Step 1: Copy your photo into `img/`**

Place your photo file at `img/ruben.jpg`. Any JPEG works. Recommended: at least 216×256px (2× for retina).

- [ ] **Step 2: Replace the placeholder in `index.html`**

In the welcome section, replace:
```html
<div class="hero-photo">
  <div class="hero-photo-placeholder">photo</div>
</div>
```

With:
```html
<div class="hero-photo">
  <img src="img/ruben.jpg" alt="Ruben Carrazco">
</div>
```

- [ ] **Step 3: Verify photo appears correctly in browser**

```bash
open index.html
```

Expected: your photo fills the 108×128px box, cropped from center with `object-fit: cover`. No gray placeholder visible.

- [ ] **Step 4: Commit**

```bash
git add img/ruben.jpg index.html
git commit -m "feat: add profile photo"
```

---

### Task 9: Create GitHub repo and deploy to GitHub Pages

**Files:** No code changes — repo setup only.

- [ ] **Step 1: Create public GitHub repo**

```bash
gh repo create cs106b-section --public --source=. --remote=origin --push
```

Expected output includes: `✓ Created repository rcarrz04/cs106b-section on GitHub` and confirms the push.

- [ ] **Step 2: Enable GitHub Pages via gh CLI**

```bash
echo '{"source":{"branch":"main","path":"/"}}' | \
  gh api repos/rcarrz04/cs106b-section/pages --method POST --input -
```

Expected: JSON response with `"status": "queued"`.

- [ ] **Step 3: Wait ~60 seconds, then verify the live site**

```bash
open https://rcarrz04.github.io/cs106b-section/
```

Expected: the site loads live with sidebar, all sections, and correct styling. If it shows a 404, wait another 30 seconds and reload — first deploy takes up to 2 minutes.

- [ ] **Step 4: Confirm URL and add it to the repo description**

```bash
gh repo edit rcarrz04/cs106b-section \
  --description "CS106B Section site" \
  --homepage "https://rcarrz04.github.io/cs106b-section/"
```

---

## How to Update the Site After Launch

**Add new week's slides:**
1. Upload PDF to `files/week<N>.pdf`
2. Add a new `<tr>` at the top of `.slides-list tbody` in `index.html`
3. Add `class="past"` to all previous rows
4. `git add files/ index.html && git commit -m "content: add week N slides" && git push`

**Post an announcement:**
1. Add a new `<div class="announce">` at the top of `#announcements`
2. Add `class="announce stale"` to old ones
3. `git add index.html && git commit -m "content: announcement" && git push`

**Update office hours:**
1. Edit the `.oh-card` divs in `#oh`
2. `git add index.html && git commit -m "content: update OH times" && git push`
