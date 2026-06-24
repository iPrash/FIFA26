# Changelog — FIFA26

All notable changes to this project, in reverse chronological order.

---

## [Unreleased] — 2026-06-24

### Score Steppers
- Added optional per-match score steppers that appear when a pick is made.
- Win picks show `[−] 1 [+] – [−] 0 [+]` (independent control of each team's goals).
- Draw picks show `[−] 0 – 0 [+]` (single control, both scores move together).
- Default scorelines: 1-0 for a win, 0-0 for a draw — always contributes to GD.
- Winner's score is enforced > loser's; draw scores are enforced equal.
- Engine (`statsWith`) now adds GF/GA/GD from pick scorelines, not just points.
- Goal-difference tiebreakers are now fully real across both played and picked matches.
- Persistence updated: localStorage migrates old string picks; share URLs encode scores (backward-compatible with old 24-char links).

### UI Polish
- Shortened hint text to two compact lines: "Probable winners shown. / Tap to change. Board updates live."
- Moved 🔒 lock icon to left of points for better alignment.
- Lightened board background (`#0a1f15` → `#163d2a`) and brightened chalk text (`#e9f3ec` → `#f5faf7`) for readability.

---

## [b17a353] — 2026-06-24

### Winners / Full Group Toggle
- Added an inline toggle switch in the board header row (between FIFA26 title and pick count).
- **Winners Only** (default, left): shows top 2 teams per group card — compact view.
- **Full Group** (right): shows all 4 teams per group with rank, flag, code, GD, and points.
- Full Group mode switches the grid to 3 columns so cards have room for 4 rows.
- 3rd/4th-place teams are faded in Full Group mode when not mathematically locked.
- No extra vertical space used — toggle sits on the existing header line.

---

## [c77ff4a] — 2026-06-24

### localStorage Persistence & Shareable URLs
- Picks now auto-save to `localStorage` on every change and survive page reloads.
- Wrapped in `try/catch` so `file://` usage still works.
- **Share button** ("Share My Picks"): encodes all picks into a compact URL query param (`?p=...`), copies to clipboard with a toast notification.
- Opening a shared link loads the sender's picks, then cleans the URL.
- URL params take priority over localStorage so shared links always work.
- First visit auto-loads probable picks so the board is fully populated immediately.

### Sticky Button Bar
- Button bar moved inside the sticky board header — scrolls with the board, never overlaps group cards.
- Buttons compacted to two-line labels: "Probable Picks", "Share My Picks", "Clear Picks".
- Added toast notification system for user feedback (link copied, shared picks loaded, etc.).

---

## [02ccdf9] — 2026-06-24

### Probable Picks & Clear Picks Improvements
- **"Probable Picks" button**: pre-fills all 24 Matchday 3 fixtures with the statistically most likely outcome (source: The Athletic, Jun 2026 model). Users get an instant projected Round of 32 and can tweak individual matches.
- **"Clear Picks" button**: now styled red (danger action) with a `confirm()` dialog before clearing — prevents accidental wipe of carefully-made picks. Does nothing if no picks exist.
- Probable outcomes stored in a `PROBABLE` map (24 fixture IDs → H/A/D).

### Board Appearance
- Lightened board background from near-black (`#0a1f15`) to mid-dark green (`#163d2a`).
- Brightened board text (chalk) from `#e9f3ec` to `#f5faf7` for better contrast.
- Moved 🔒 icon to the left of points so point values stay right-aligned across all cards.

---

## [562358b] — 2026-06-24

### Baseline
- Updated `CLAUDE.md` with current data state, repo file listing, and design system notes.
- Updated `README.md` with current feature description.
- Minor `index.html` fixes.
- Added MIT `LICENSE` file.

---

## [8ea491a] — 2026-06-24

### Initial Release — FIFA26 What-If Analyser
- Single-file (`index.html`), zero-dependency, phone-first World Cup 2026 group-stage predictor.
- **48 teams** across 12 groups (A–L), seeded with real data from both completed rounds (Matchdays 1 & 2, through Jun 23, 2026).
- **24 Matchday 3 fixtures** as pickable matches — tap a winner or draw for each.
- **Sticky qualification board**: 12 colour-coded group cards (winner + runner-up) plus a row of the 8 best third-placed teams.
- **12-colour group palette** (`GC` map): each group A–L has a distinct hue; colour follows teams into the third-place row.
- **Live board updates**: every pick recalculates standings instantly (win=3, draw=1, loss=0).
- **Mathematically-qualified teams** (🔒): brute-force `clinchedTop2()` checks every W/D/L combination — scoreline-proof, returns exactly the teams FIFA lists as qualified. 7 teams locked: Mexico, USA, Germany, Argentina, France, Norway, Colombia.
- **Real flag images** via `flagcdn.com` SVGs (not emoji — emoji break on Windows).
- **Data model**: `TEAMS`, `PLAYED`, `FIXTURES` constants at top of script. To finalize a result: move from `FIXTURES` → `PLAYED` with real score; everything recomputes.
- **Engine**: `baseStats()` → cached `BASE`; `statsWith(picks)` clones and adds points; `standings(picks)` sorts groups + ranks thirds; tiebreak: points → GD → GF → seed index.
- **Design**: light "matchday programme" body with dark LED-style sticky board (green pitch stripes). System sans for UI, monospace for codes/points.
- **`r32-bracket.js`** prepared (not yet imported): 16 fixed Round-of-32 matches, third-place pools, all 495 official Annex C combinations (`THIRD_MAP`), and `assignThirds()` function.
