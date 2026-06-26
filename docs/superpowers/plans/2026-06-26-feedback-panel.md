# Feedback Panel Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a toggleable right-side feedback panel to the CS106B section site that collects week-aware topic preferences and anonymous free-text feedback, submitting to Google Sheets via Google Apps Script.

**Architecture:** A `position: fixed` panel slides in from the right edge using a CSS transform transition; toggling is driven by a `body.feedback-open` class so CSS handles all visual state; two triggers (sidebar nav link + vertical edge tab) plus a close button all call one JS function. Form data is POSTed as URL-encoded form fields to a GAS web app endpoint, which appends a row to a Google Sheet.

**Tech Stack:** Vanilla HTML/CSS/JS (no dependencies), Google Apps Script (server-side, one-time manual setup).

---

## Files

| File | Change |
|------|--------|
| `styles.css` | Add panel, edge tab, open-state, success/error styles |
| `index.html` | Add sidebar nav link, edge tab div, panel div, JS constants, detection, toggle, submit |

---

### Task 1: CSS — Feedback panel base styles

**Files:**
- Modify: `styles.css` (append after `.retro-goal-border` block at end of file)

- [ ] **Step 1: Add panel and edge tab base CSS**

Append to the bottom of `styles.css`:

```css
/* ── Feedback Panel ── */
.feedback-panel {
  position: fixed;
  right: 0;
  top: 0;
  bottom: 0;
  width: 260px;
  background: var(--card-bg);
  border-left: 1px solid var(--divider);
  padding: 1.5rem 1rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  overflow-y: auto;
  transform: translateX(100%);
  transition: transform 0.25s ease;
  z-index: 100;
}

body.feedback-open .feedback-panel {
  transform: translateX(0);
}

/* ── Feedback Edge Tab ── */
.feedback-tab {
  position: fixed;
  right: 0;
  top: 50%;
  transform: translateY(-50%) rotate(180deg);
  background: var(--accent);
  color: #fff;
  font-family: 'IBM Plex Sans', sans-serif;
  font-size: 0.58rem;
  font-weight: 500;
  letter-spacing: 0.1em;
  text-transform: uppercase;
  padding: 0.75rem 0.35rem;
  border-radius: 4px 0 0 4px;
  writing-mode: vertical-rl;
  cursor: pointer;
  user-select: none;
  z-index: 101;
  transition: right 0.25s ease;
}

body.feedback-open .feedback-tab {
  right: 260px;
}

/* ── Sidebar Feedback nav link indicator ── */
.feedback-nav-link { color: var(--accent) !important; }
.feedback-nav-link::after { content: ' ▶'; font-size: 0.55rem; }
body.feedback-open .feedback-nav-link::after { content: ' ◀'; }

/* ── Panel internal components ── */
.panel-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.panel-close {
  background: none;
  border: none;
  color: var(--muted);
  font-size: 0.85rem;
  cursor: pointer;
  line-height: 1;
  padding: 0;
}

.panel-close:hover { color: var(--body-text); }

.panel-week-badge {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  background: var(--tag-bg);
  border: 1px solid #FDE68A;
  border-radius: 4px;
  padding: 4px 9px;
  font-size: 0.7rem;
  color: var(--tag-text);
}

.panel-week-dot {
  width: 6px;
  height: 6px;
  background: var(--accent);
  border-radius: 50%;
  flex-shrink: 0;
}

.panel-field-label {
  display: block;
  font-size: 0.68rem;
  font-weight: 500;
  color: #57534E;
  margin-bottom: 4px;
}

.panel-field-sub {
  display: block;
  font-size: 0.62rem;
  color: var(--muted);
  margin-bottom: 7px;
  line-height: 1.4;
}

.panel-select {
  width: 100%;
  padding: 6px 24px 6px 8px;
  border: 1px solid var(--divider);
  border-radius: 4px;
  font-family: 'IBM Plex Sans', sans-serif;
  font-size: 0.72rem;
  color: var(--body-text);
  background: var(--bg);
  appearance: none;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='10' height='6' viewBox='0 0 10 6'%3E%3Cpath d='M0 0l5 6 5-6z' fill='%2378716C'/%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-position: right 8px center;
  cursor: pointer;
}

.panel-select:focus { outline: none; border-color: var(--accent); }

.panel-textarea {
  width: 100%;
  padding: 7px 8px;
  border: 1px solid var(--divider);
  border-radius: 4px;
  font-family: 'IBM Plex Sans', sans-serif;
  font-size: 0.72rem;
  color: var(--body-text);
  background: var(--bg);
  resize: none;
  height: 80px;
  line-height: 1.5;
}

.panel-textarea::placeholder { color: var(--muted); }
.panel-textarea:focus { outline: none; border-color: var(--accent); }

.panel-submit {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  padding: 0.45rem 0.75rem;
  background: #FDE8C8;
  color: #92400E;
  border: none;
  border-radius: 8px;
  font-family: 'IBM Plex Sans', sans-serif;
  font-size: 0.75rem;
  cursor: pointer;
  transition: filter 0.12s;
}

.panel-submit:hover { filter: brightness(0.93); }
.panel-submit:disabled { opacity: 0.6; cursor: not-allowed; filter: none; }

.panel-anon-note {
  font-size: 0.6rem;
  color: #C4C0BB;
  text-align: center;
  line-height: 1.5;
  margin-top: auto;
}

.panel-success {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  flex: 1;
  gap: 0.75rem;
  text-align: center;
}

.panel-success-icon {
  width: 36px;
  height: 36px;
  background: #D1FAE5;
  color: #065F46;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1rem;
}

.panel-success p {
  font-size: 0.82rem;
  color: var(--muted);
}

.panel-error {
  font-size: 0.68rem;
  color: #DC2626;
  text-align: center;
}
```

- [ ] **Step 2: Open `index.html` in browser and confirm no visual change** (panel not added yet — CSS should be inert).

- [ ] **Step 3: Commit**

```bash
git add styles.css
git commit -m "style: add feedback panel, edge tab, and form component styles"
```

---

### Task 2: HTML — Sidebar nav link and edge tab

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add "Feedback" nav link to sidebar**

In `index.html`, find the sidebar `<nav>` block:
```html
        <a href="#resources" class="nav-link">Resources</a>
      </nav>
```

Replace it with:
```html
        <a href="#resources" class="nav-link">Resources</a>
        <a href="#" class="nav-link feedback-nav-link" id="feedback-nav-link">Feedback</a>
      </nav>
```

- [ ] **Step 2: Add edge tab element**

Find the closing `</aside>` tag:
```html
    </aside>
    <main class="main">
```

Add the edge tab between `</aside>` and `<main>`:
```html
    </aside>
    <div class="feedback-tab" id="feedback-tab" role="button" aria-label="Open feedback panel">FEEDBACK</div>
    <main class="main">
```

- [ ] **Step 3: Verify in browser**

Open `index.html` in Chrome. You should see:
- "Feedback" link in the left sidebar nav, in accent orange color with a ▶ indicator
- An orange vertical "FEEDBACK" tab on the right edge of the screen
Neither does anything yet (no JS wired up).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add feedback sidebar nav link and edge tab"
```

---

### Task 3: HTML — Feedback panel markup

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Add the panel div**

Find the closing `</main>` tag:
```html
    </main>
  </div>
  <script>
```

Insert the feedback panel between `</main>` and `</div>`:
```html
    </main>

    <!-- Feedback Panel -->
    <div class="feedback-panel" id="feedback-panel" aria-label="Feedback panel">
      <div class="panel-header">
        <span class="section-label">Feedback</span>
        <button class="panel-close" id="panel-close-btn" aria-label="Close feedback panel">✕</button>
      </div>

      <div class="panel-week-badge">
        <span class="panel-week-dot"></span>
        <span id="panel-week-label">Loading…</span>
      </div>

      <div>
        <label class="panel-field-label" for="topic-select">What should we focus on this week?</label>
        <span class="panel-field-sub">Pick the topic you'd most like to drill in section.</span>
        <select class="panel-select" id="topic-select"></select>
      </div>

      <hr class="divider" style="margin: 0;">

      <div>
        <label class="panel-field-label" for="anon-feedback">Anything else on your mind?</label>
        <span class="panel-field-sub">Anonymous. About section, the material, me — anything relevant.</span>
        <textarea class="panel-textarea" id="anon-feedback" placeholder="e.g. I'm confused about pointers, or section pace feels too fast…"></textarea>
      </div>

      <button class="panel-submit" id="panel-submit-btn">Submit feedback →</button>

      <p class="panel-anon-note">🔒 Responses are anonymous and go directly to a private Google Sheet.</p>
    </div>

  </div>
  <script>
```

- [ ] **Step 2: Verify in browser**

Open `index.html`. The panel should not be visible (it's off-screen to the right, waiting for JS). No visual change expected yet.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add feedback panel HTML structure"
```

---

### Task 4: JS — Week data and detection

**Files:**
- Modify: `index.html` (inside the existing `<script>` block, add before the `(function() {` IIFE)

- [ ] **Step 1: Add FEEDBACK_ENDPOINT constant and WEEK_DATA**

Find the opening of the `<script>` block:
```html
  <script>
  (function () {
    var FLAGS = ['🇧🇷',
```

Insert the following **before** the `(function () {` line:

```html
  <script>
  var FEEDBACK_ENDPOINT = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';

  var WEEK_DATA = [
    {
      week: 1, label: 'Week 1 · Jun 22–26',
      start: new Date('2026-06-22'), end: new Date('2026-06-28'),
      topics: ['C++ Fundamentals', 'Pass-by-Reference & Strings', 'Vectors & Grids', 'Testing Basics']
    },
    {
      week: 2, label: 'Week 2 · Jun 29–Jul 3',
      start: new Date('2026-06-29'), end: new Date('2026-07-05'),
      topics: ['Stacks & Queues', 'Sets & Maps', 'Big-O & Algorithm Analysis']
    },
    {
      week: 3, label: 'Week 3 · Jul 6–10',
      start: new Date('2026-07-06'), end: new Date('2026-07-12'),
      topics: ['Introduction to Recursion', 'More Recursion', 'Fractals & Recursive Problem Solving', 'Recursive Backtracking']
    },
    {
      week: 4, label: 'Week 4 · Jul 13–17',
      start: new Date('2026-07-13'), end: new Date('2026-07-19'),
      topics: ['Recursive Backtracking & Enumeration', 'Sorting Algorithms', 'Review & Problem Solving']
    },
    {
      week: 5, label: 'Week 5 · Jul 20–24',
      start: new Date('2026-07-20'), end: new Date('2026-07-26'),
      topics: ['Object-Oriented Programming', 'Pointers & Arrays', 'Dynamic Memory Management', 'Priority Queues & Binary Heaps']
    },
    {
      week: 6, label: 'Week 6 · Jul 27–31',
      start: new Date('2026-07-27'), end: new Date('2026-08-02'),
      topics: ['Introduction to Linked Lists', 'More Linked Lists', 'Trees', 'Binary Search Trees']
    }
  ];

  var FALLBACK_WEEK = {
    week: 0, label: 'General',
    topics: ['General Questions', 'Lecture Material', 'Assignments', 'Other']
  };

  function getCurrentWeekData() {
    var now = new Date();
    now.setHours(0, 0, 0, 0);
    for (var i = 0; i < WEEK_DATA.length; i++) {
      if (now >= WEEK_DATA[i].start && now <= WEEK_DATA[i].end) return WEEK_DATA[i];
    }
    return FALLBACK_WEEK;
  }

  (function () {
    var FLAGS = ['🇧🇷',
```

- [ ] **Step 2: Verify in browser console**

Open Chrome DevTools → Console. Type:
```javascript
getCurrentWeekData()
```
Expected: `{week: 1, label: "Week 1 · Jun 22–26", ...}` (since today is June 26, 2026).

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add week data and auto-detection logic"
```

---

### Task 5: JS — Panel toggle and initialization

**Files:**
- Modify: `index.html` (inside `<script>`, after the closing `})();` of the existing IIFE)

- [ ] **Step 1: Add toggle and init functions**

Find the closing of the existing IIFE:
```javascript
  })();
  </script>
```

Insert the following **between** `})();` and `</script>`:

```javascript
  // ── Feedback Panel ─────────────────────────────────────────────────
  (function () {
    function togglePanel() {
      document.body.classList.toggle('feedback-open');
    }

    function initPanel() {
      var weekData = getCurrentWeekData();
      document.getElementById('panel-week-label').textContent = weekData.label;
      var select = document.getElementById('topic-select');
      select.innerHTML = weekData.topics.map(function (t) {
        return '<option value="' + t + '">' + t + '</option>';
      }).join('');
    }

    document.getElementById('feedback-tab').addEventListener('click', togglePanel);
    document.getElementById('feedback-nav-link').addEventListener('click', function (e) {
      e.preventDefault();
      togglePanel();
    });
    document.getElementById('panel-close-btn').addEventListener('click', togglePanel);

    initPanel();
  })();
```

- [ ] **Step 2: Verify toggle in browser**

Reload `index.html`. Check all three toggle triggers:
1. Click the orange **FEEDBACK** edge tab → panel slides in from right, tab moves 260px left, sidebar shows ◀
2. Click **Feedback** in the sidebar nav → panel closes
3. Click the **✕** button inside the panel → panel closes

Confirm the topic dropdown shows 4 Week 1 topics and the week badge reads "Week 1 · Jun 22–26".

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: wire feedback panel toggle and populate week topics"
```

---

### Task 6: JS — Form submission

**Files:**
- Modify: `index.html` (inside the feedback panel IIFE from Task 5, before the closing `})();`)

- [ ] **Step 1: Add submit handler**

Inside the feedback panel IIFE (before its closing `})();`), add:

```javascript
    document.getElementById('panel-submit-btn').addEventListener('click', function () {
      var btn = document.getElementById('panel-submit-btn');
      var topic = document.getElementById('topic-select').value;
      var feedback = document.getElementById('anon-feedback').value.trim();
      var weekData = getCurrentWeekData();

      btn.disabled = true;
      btn.textContent = 'Sending…';

      var body = new URLSearchParams();
      body.append('week', weekData.week);
      body.append('weekLabel', weekData.label);
      body.append('topic', topic);
      body.append('feedback', feedback);

      fetch(FEEDBACK_ENDPOINT, { method: 'POST', body: body })
        .then(function () {
          document.getElementById('feedback-panel').innerHTML =
            '<div class="panel-success">' +
              '<div class="panel-success-icon">✓</div>' +
              '<p>Thanks — got it.</p>' +
            '</div>';
        })
        .catch(function () {
          btn.disabled = false;
          btn.textContent = 'Submit feedback →';
          var existing = document.getElementById('panel-error');
          if (!existing) {
            var err = document.createElement('p');
            err.id = 'panel-error';
            err.className = 'panel-error';
            err.textContent = 'Something went wrong — please try again.';
            btn.insertAdjacentElement('afterend', err);
          }
        });
    });
```

- [ ] **Step 2: Verify submit flow in browser (before GAS is set up)**

With `FEEDBACK_ENDPOINT` still set to the placeholder URL, open the panel, fill in both fields, and click Submit. Expected:
- Button shows "Sending…" and becomes disabled
- After ~5 seconds the network request fails → error message "Something went wrong — please try again." appears below the button
- Button re-enables

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add feedback form submission with error handling"
```

---

### Task 7: Google Apps Script setup (manual)

This task is done in Google's browser UI, not in code. No files change.

- [ ] **Step 1: Create the Google Sheet**

Go to [sheets.google.com](https://sheets.google.com) and create a new sheet called **CS106B Section Feedback**. Add headers in row 1:

| A | B | C | D | E |
|---|---|---|---|---|
| Timestamp | Week # | Week Label | Topic | Feedback |

- [ ] **Step 2: Create the Apps Script**

Go to [script.google.com](https://script.google.com) → **New project** → rename it "CS106B Feedback Handler".

Replace the default `myFunction` with:

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.openById('YOUR_SHEET_ID').getActiveSheet();
  sheet.appendRow([
    new Date(),
    e.parameter.week,
    e.parameter.weekLabel,
    e.parameter.topic,
    e.parameter.feedback
  ]);
  return ContentService.createTextOutput('ok');
}
```

Replace `YOUR_SHEET_ID` with the ID from your Sheet's URL (the long string between `/d/` and `/edit`).

- [ ] **Step 3: Deploy as Web App**

Click **Deploy → New deployment** → Type: **Web app**
- Description: `CS106B Feedback v1`
- Execute as: **Me**
- Who has access: **Anyone**

Click **Deploy** → copy the **Web app URL** (starts with `https://script.google.com/macros/s/...`).

- [ ] **Step 4: Paste URL into index.html**

In `index.html`, replace:
```javascript
  var FEEDBACK_ENDPOINT = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```
with the actual URL you just copied.

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "config: wire live Google Apps Script feedback endpoint"
```

---

### Task 8: End-to-end verification and deploy

- [ ] **Step 1: Test full submission**

Open `index.html` locally (or push to GitHub Pages and test live). Open the feedback panel. Select a topic, type something in the text area, click Submit. Expected:
- Button shows "Sending…"
- Panel content replaces with "✓ Thanks — got it."

- [ ] **Step 2: Verify in Google Sheet**

Open **CS106B Section Feedback** in Google Sheets. Confirm a new row appeared with today's timestamp, week number, week label, selected topic, and your feedback text.

- [ ] **Step 3: Test with empty feedback field**

Reopen the panel (toggle), leave the text area blank, select a topic, submit. Confirm a row appears in the Sheet with an empty feedback cell — both fields are optional.

- [ ] **Step 4: Push to GitHub Pages**

```bash
git push origin main
```

Wait ~60 seconds, then open your live site at `https://rcarrz04.github.io/cs106b-section/` and confirm the panel works end-to-end on the live site.

- [ ] **Step 5: Add .superpowers to .gitignore**

```bash
echo ".superpowers/" >> .gitignore
git add .gitignore
git commit -m "chore: ignore .superpowers brainstorm artifacts"
```
