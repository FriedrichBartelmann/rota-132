
# Route 132 Rota

A set of lightweight, installable web pages for viewing the Bexleyheath **route 132** duty rotas on a phone. Pick your line number and see your duty for every day, plan holidays and keep diary notes. No accounts, no server, no build step.

> **Unofficial.** This project is based on the rota sheets only. It is not linked to the company website or TfL, so always check official duty information for changes.

## Pages

| Page | File | What it is |
|---|---|---|
| Landing page | `index.html` | Links to the three rotas, plus all duties, the comparison tables and route 132 facts |
| 132 Early | `early.html` | Lines 201–214 (14-week rotation) |
| 132 Late | `late.html` | Lines 251–262 (12-week rotation) |
| 132 Main | `main.html` | Lines 221–241 (21-week rotation) |
| Duties (optional) | `duties.html` | Standalone copy of the duties and comparison content that is also on the landing page |

Each rota page works independently. The rota pages do not link to each other.

## Features

**Rota pages**
- Calendar for the selected line, Saturday-first, with rest days, weekends, holidays and today highlighted
- Month flip animation, swipe and keyboard navigation (← / →, **T** for today)
- Holiday planner: type a date range, or tap **Select days** on the year chart and drag across days
- Year chart that follows your selected line
- "Year at a glance" mini calendar with an expanded view showing duty numbers
- 7-day diary book with a note for every day, saved as you type
- Export and import backup of notes and holidays (JSON)
- Light and dark theme, reduced-motion support and keyboard-friendly controls
- Works offline once installed

**Landing page**
- Animated route banner and rota cards
- Table of all duties (filter, search, sortable columns)
- Comparison of Early, Late and Main, with hours differences
- Route 132 facts: buses, length, stops, drivers per day and average time between stops

## Install on your phone (PWA)

1. Host the files over HTTPS (see below).
2. Open the site on your phone.
3. **Android Chrome:** menu → **Install app**. **iPhone Safari:** Share → **Add to Home Screen**.

A service worker will not run from a local file, so use the hosted address.

## Deploy

The site is plain static files, so any static host works.

**GitHub Pages:** Settings → Pages → deploy from the `main` branch, root folder.

**Vercel / Netlify / Cloudflare Pages:** import the repository and deploy with no build command. Make sure deployment protection is off for the production domain, and that there is no catch-all rewrite to `index.html`, or the manifest and service worker will not load.

All files must sit in the same folder:

```
index.html  early.html  late.html  main.html  duties.html
manifest.webmanifest  sw.js
icon-192.png  icon-512.png  icon-maskable-512.png  apple-touch-icon.png
```

## Updating the rota

Each rota page holds its data near the top of its `<script>`:

| What | Variable | Example |
|---|---|---|
| Duties (times and hours) | `DUTY` | `A:"223,0452,1251,7.06"` is duty 223, 04:52 to 12:51, 7 h 06 min |
| Weekly pattern | `ROTA` | `"--AAABB"` is Sat to Fri, `-` is a rest day and letters point to `DUTY` |
| Start date | `START` | `Date.UTC(2026,9,3)` (month counts from 0, so 9 is October) |

1. Edit `DUTY`, `ROTA` and `START` in the page you want to change.
2. Update the text that mentions the period (`Period commencing …`, the help dialog and the "before the rota starts" message).
3. If the line numbers change, edit the line loop (`for(var l=…;l<=…;l++)`), the base number in `shift()` (`ln-201`, `ln-251` or `ln-221`) and the default `var line=`.
4. If you change the duties on the landing page content, update `duties.html` and `index.html` in the same way.
5. **Bump the cache name in `sw.js`** (for example `rota-v11` to `rota-v12`). Without this, phones may keep showing the old version.
6. Commit. On the phone, close the app fully and open it twice to pick up the new version.

The rotation assumes each line moves to the next line's pattern the following week (for example 201 to 202, with the last line returning to the first).

## Your data and privacy

Notes, holidays and settings are stored only in your browser's `localStorage`. Nothing is sent anywhere.

| Page | Notes | Holidays |
|---|---|---|
| Early | `rotaNotes` | `rotaHols` |
| Late | `rotaNotesLate` | `rotaHolsLate` |
| Main | `rotaNotesMain` | `rotaHolsMain` |

Theme (`rotaTheme`) and the first-visit help flag (`rotaHelpSeen`) are shared. Clearing browser data or changing phone deletes saved notes, and an installed iPhone app keeps its own storage separate from Safari. Use **Export backup** now and then.

## Data notes

- Duties were transcribed from the rota sheets (period commencing 03/10/2026). Weekly totals were cross-checked against the sheets, apart from a few lines that were partly hidden in the source images.
- Times after midnight (for example 00:52) finish the next morning.
- **Gap** on the landing page is spell (start to finish) minus TOD. It is calculated, not printed on the rota sheets.
- Route facts (buses, length, stops and journey times) come from public sources such as Wikipedia, the Bus Routes in London wiki, a 2022 ride report and Moovit. They may be out of date, so check TfL for current figures.

## Tech

Plain HTML, CSS and JavaScript in single files. No frameworks, no dependencies, no build. Offline support comes from `sw.js`, which caches the files and refreshes them in the background.
