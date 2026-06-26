# Feedback Panel — Design Spec
**Date:** 2026-06-26  
**Status:** Approved

---

## Overview

Add a toggleable feedback panel to the right side of the CS106B section site. The panel lets students (a) pick a topic they want focused on in that week's section, and (b) leave anonymous free-form feedback. Submissions go to a private Google Sheet via Google Apps Script. The panel is hidden by default and can be toggled open/closed.

---

## Layout & Placement

The panel lives in the blank space to the right of the main content column (which is capped at `max-width: 760px`). It is a fixed-width right column (`~260px`) that slides in/out with a CSS transition. When closed, only the edge tab is visible.

**Layout change:** The `.layout` flex container gains a third child — the feedback panel — after `.main`. The panel starts with `width: 0; overflow: hidden` (closed) and transitions to `width: 260px` (open).

---

## Toggle Triggers

Two independent triggers, both toggle the same panel open/closed:

1. **Sidebar nav link** — A "Feedback" entry added below "Resources" in the left sidebar `<nav>`. Styled in accent color (`#C2773B`) with a `▶` indicator. Clicking toggles the panel and flips the indicator to `◀` when open.
2. **Vertical edge tab** — A fixed-position element anchored to the right edge of `.main`. Reads "FEEDBACK" in rotated vertical text, accent background. It tracks the panel state and stays at the right edge of `.main` (moves left when panel opens). Clicking toggles the panel.

Both triggers share a single JS toggle function and a single `data-panel-open` state attribute on `<body>` (or a boolean variable).

---

## Week Auto-Detection

Current week is determined at page load by comparing `new Date()` against hardcoded date ranges. If today falls outside all defined ranges, a fallback "General" state is shown with a generic topic list.

```
Week 1: Jun 22 – Jun 28
Week 2: Jun 29 – Jul 5
Week 3: Jul 6  – Jul 12
Week 4: Jul 13 – Jul 19
Week 5: Jul 20 – Jul 26
Week 6: Jul 27 – Aug 2
```

---

## Topic Options Per Week

Topics shown in the dropdown change based on the auto-detected week:

| Week | Topics |
|------|--------|
| 1 | C++ Fundamentals, Pass-by-Reference & Strings, Vectors & Grids, Testing Basics |
| 2 | Stacks & Queues, Sets & Maps, Big-O & Algorithm Analysis |
| 3 | Introduction to Recursion, More Recursion, Fractals & Recursive Problem Solving, Recursive Backtracking |
| 4 | Recursive Backtracking & Enumeration, Sorting Algorithms, Review & Problem Solving |
| 5 | Object-Oriented Programming, Pointers & Arrays, Dynamic Memory Management, Priority Queues & Binary Heaps |
| 6 | Introduction to Linked Lists, More Linked Lists, Trees, Binary Search Trees |
| Fallback | General Questions, Lecture Material, Assignments, Other |

---

## Panel UI Components

Matches existing site styles exactly (fonts, colors, border-radius, etc.):

- **Header row:** "FEEDBACK" section label (`.section-label` style) + `✕` close button (right-aligned)
- **Week badge:** Warm yellow pill (`#FEF3C7` bg, `#92400E` text) with accent dot, shows "Week N · Mon DD–Mon DD"
- **Topic field:** `<label>` + `<select>` dropdown; label reads "What should we focus on this week?"; sub-label: "Pick the topic you'd most like to drill in section."
- **Divider:** `<hr>` styled as `.section-divider`
- **Anonymous field:** `<label>` + `<textarea>` (3 rows); label reads "Anything else on your mind?"; sub-label: "Anonymous. About section, the material, me — anything relevant."
- **Submit button:** Full-width, `.pill-slides` style (`#FDE8C8` bg, `#92400E` text), reads "Submit feedback →"
- **Footer note:** Small muted text: 🔒 "Responses are anonymous and go directly to a private Google Sheet."

On successful submission: replace form contents with a brief confirmation ("Thanks — got it.") that fades in. No redirect.

On error: show a small inline error below the submit button.

---

## Data Flow

```
User fills form → JS fetch POST → Google Apps Script web app URL
→ GAS doPost(e) → append row to Google Sheet
Row columns: [Timestamp, Week Number, Topic Selected, Anonymous Feedback Text]
```

The GAS web app URL is stored as a JS constant near the top of `index.html` for easy updating:

```js
const FEEDBACK_ENDPOINT = 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec';
```

The form submits both fields even if one is empty (empty string for the unused field). No client-side validation beyond preventing double-submit while a request is in flight.

---

## Files Changed

| File | Change |
|------|--------|
| `index.html` | Add sidebar nav link, feedback panel HTML, edge tab element, toggle JS, GAS fetch JS |
| `styles.css` | Add panel layout styles, edge tab styles, panel component styles, open/close transition |

No new files. No build step. No dependencies.

---

## Google Apps Script Setup (one-time, manual)

This is a manual step Ruben does once before deploying:

1. Go to [script.google.com](https://script.google.com), create a new project
2. Paste the `doPost(e)` function (provided in implementation plan)
3. Deploy as Web App → Execute as: Me, Who has access: Anyone
4. Copy the deployment URL into `FEEDBACK_ENDPOINT` in `index.html`
5. Link the script to a Google Sheet (created in step 1 or separately)

---

## Out of Scope

- No authentication or identity — fully anonymous by design
- No admin UI on the website to view responses (use Google Sheets directly)
- No rate limiting (trusting section students)
- No mobile layout fixes (pre-existing issue, not in scope)
