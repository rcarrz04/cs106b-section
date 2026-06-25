# CS106B Section Website — Design Spec

**Date:** 2026-06-25  
**Status:** Approved

---

## Purpose

A personal section website for Ruben Carrazco's CS106B section, hosted on GitHub Pages. Serves two goals: resource hub (slides, handouts, links) and section identity (about Ruben, announcements, office hours). Students can find everything they need in one place.

---

## Architecture

**Single-page static HTML/CSS site — no build step, no framework, no dependencies.**

- One `index.html` file with all sections on a single scrollable page
- One `styles.css` file for all styles
- A `files/` directory for uploaded PDFs (slides, handouts)
- An optional `img/` directory for the profile photo
- Deployed via GitHub Pages from the `main` branch root

Updates are made by editing `index.html` directly and pushing to GitHub. New slides are added by uploading a PDF to `files/` and adding one `<tr>` row to the slides table.

---

## Layout

Fixed dark sidebar + scrollable main content area.

**Sidebar (200px, fixed):**
- Name: Ruben Carrazco (Libre Baskerville serif, bold)
- Course tag: CS106B · Section 14 (uppercase, muted)
- Navigation links: Welcome · Announcements · Slides & Handouts · Office Hours · Resources
- Bottom meta: section time and room

**Main content (fluid, left-offset by sidebar width):**
- Max-width ~760px for comfortable reading
- Sections stack vertically with generous spacing

---

## Sections (in order)

1. **Welcome** — Profile photo (rectangular, ~108×128px) beside a short intro paragraph ("Hi, I'm Ruben…"). Photo source: `img/ruben.jpg`.

2. **Announcements** — Amber-accented card items, newest first. Each has body text and a date. Older items fade to 50% opacity.

3. **Slides & Handouts** — Minimal table: week number, topic title, PDF download link. Current week items are full opacity; past weeks are 40% opacity.

4. **Office Hours** — Two-column card grid. Each card has: day, time (Libre Baskerville), and location. Zoom link as a plain `<a>` under the Zoom card if applicable.

5. **Resources** — Icon + title + one-line description list. Links open in a new tab. Initial links: cppreference.com, Course Style Guide, Past Exams, Ed Discussion.

---

## Visual Design

| Token | Value |
|---|---|
| Background | `#FAFAF8` (warm off-white) |
| Sidebar | `#1C1917` (warm charcoal) |
| Sidebar muted text | `#78716C` |
| Accent | `#C2773B` (warm amber) |
| Body text | `#292524` |
| Dividers | `#E7E5E4` |
| Announcement bg | `#FEF3C7` |
| Announcement text | `#92400E` |

**Fonts (Google Fonts):**
- Display/headings: Libre Baskerville (400, 700, italic)
- Body: IBM Plex Sans (300, 400, 500)

---

## Hosting & Deployment

- Public GitHub repo (name TBD, e.g. `cs106b-section`)
- GitHub Pages deployed from `main` branch, root directory
- URL: `https://<github-username>.github.io/cs106b-section/`
- No CI, no build pipeline — push to `main` = live

---

## Update Workflow

**New week's slides:** Upload PDF to `files/`, add one `<tr>` row at the top of the slides table in `index.html`, push.

**New announcement:** Add a new `.announce` div at the top of the announcements section, push. Add `style="opacity:0.5"` to old ones when they're stale.

**Office hours change:** Edit the relevant `.oh-card` in `index.html`, push.

---

## Out of Scope

- JavaScript interactivity (no sorting, filtering, search)
- Dark mode toggle
- Contact form
- Comments or discussion
- Mobile-responsive sidebar (nice-to-have, not required for v1)
