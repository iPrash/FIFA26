# Changelog — FIFA26

All notable changes to this project, in reverse chronological order.

---

## 2026-06-25 · 10:00 AM — Help Button

- Added `?` help button in the board header — opens a brief overlay explaining all major features (Probable Picks, picking, score steppers, completed matches, knockout bracket, group standings, sharing).

---

## 2026-06-24 · 4:30 PM — Live Scores & Bracket

### Live Score Integration
- Scores now loaded from `scores.json` on page load — update match results by editing the JSON file on GitHub, no code changes needed.
- Confirmed matches render as compact locked cards: scores inline on team buttons, winner highlighted in green, "Final" badge.
- Locked matches are read-only — no accidental overwriting of real results.
- Clinched teams (🔒) always appear in the knockout bracket, even with no picks set.

### Knockout Bracket
- Full R32 → R16 → QF → SF → Final bracket, swipeable on mobile (tap "Bracket" tab).
- Bracket updates live as you change picks. All 16 R32 pairings follow official FIFA Annex C mapping.
- Third-place allocation resolves automatically when all 12 groups are complete.
- Matches grouped by date with kick-off times.

### Mobile UX
- Bracket discovery: subtle peek animation on load and on first pick nudges you to swipe right.
- Tab switching (Group / Bracket) no longer causes vertical scroll.

### Fixes
- Clear Picks now preserves confirmed match results.
- Fixed double-counting bug where locked scores were applied twice to standings.
- Fixed clinched detection for completed groups with tied teams (e.g. SUI and CAN in Group B now correctly show 🔒).

---

## 2026-06-24 · 1:30 PM — What's New Popup

- On first visit (per version), a small overlay highlights recent features: group standings, score steppers, kick-off times.
- Dismisses on "Got it" tap or clicking outside. Uses `localStorage` to show only once per version bump.

---

## 2026-06-24 · 12:45 PM — Group Popups & Kick-off Times

### Group Standings Popup
- Tap any group card on the board or "Group X" link in the fixture headers to see a full standings table.
- Popup shows all 4 teams with MP, W, D, L, GD, Pts — styled in the dark board theme with the group's colour accent.
- Includes both played results and current picks in the W/D/L tallies.
- Tap outside or ✕ to dismiss.

### Kick-off Times
- Fixtures now split by kick-off time slot (e.g. "Wed · Jun 24 · 3 PM ET") instead of just by day.
- Each slot labelled with its group (e.g. "Matchday 3 · Group B").
- Ordered chronologically within each day.

---

## 2026-06-24 · 11:55 AM — Score Steppers

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

## 2026-06-24 · 10:33 AM — Winners / Full Group Toggle

- Added an inline toggle switch in the board header row (between FIFA26 title and pick count).
- **Winners Only** (default, left): shows top 2 teams per group card — compact view.
- **Full Group** (right): shows all 4 teams per group with rank, flag, code, GD, and points.
- Full Group mode switches the grid to 3 columns so cards have room for 4 rows.
- 3rd/4th-place teams are faded in Full Group mode when not mathematically locked.
- No extra vertical space used — toggle sits on the existing header line.

---

## 2026-06-24 · 10:17 AM — localStorage Persistence & Shareable URLs

- Picks now auto-save to `localStorage` on every change and survive page reloads.
- Wrapped in `try/catch` so `file://` usage still works.
- **Share button** ("Share My Picks"): encodes all picks into a compact URL query param (`?p=...`), copies to clipboard with a toast notification.
- Opening a shared link loads the sender's picks, then cleans the URL.
- URL params take priority over localStorage so shared links always work.
- First visit auto-loads probable picks so the board is fully populated immediately.
- Button bar moved inside the sticky board header — scrolls with the board, never overlaps group cards.
- Buttons compacted to two-line labels: "Probable Picks", "Share My Picks", "Clear Picks".
- Added toast notification system for user feedback (link copied, shared picks loaded, etc.).

---

## 2026-06-24 · 9:42 AM — Probable Picks & Clear Picks

- **"Probable Picks" button**: pre-fills all 24 Matchday 3 fixtures with the statistically most likely outcome (source: The Athletic, Jun 2026 model). Users get an instant projected Round of 32 and can tweak individual matches.
- **"Clear Picks" button**: now styled red (danger action) with a `confirm()` dialog before clearing — prevents accidental wipe of carefully-made picks. Does nothing if no picks exist.
- Lightened board background from near-black to mid-dark green for readability.
- Brightened board text (chalk) for better contrast.
- Moved 🔒 icon to the left of points so point values stay right-aligned across all cards.

---

## 2026-06-24 · 9:25 AM — Baseline

- Updated `CLAUDE.md` with current data state, repo file listing, and design system notes.
- Updated `README.md` with current feature description.
- Minor `index.html` fixes.
- Added MIT `LICENSE` file.

---

## 2026-06-24 · 12:19 AM — Initial Release

- Single-file (`index.html`), zero-dependency, phone-first World Cup 2026 group-stage predictor.
- **48 teams** across 12 groups (A–L), seeded with real data from both completed rounds (Matchdays 1 & 2, through Jun 23, 2026).
- **24 Matchday 3 fixtures** as pickable matches — tap a winner or draw for each.
- **Sticky qualification board**: 12 colour-coded group cards (winner + runner-up) plus a row of the 8 best third-placed teams.
- **12-colour group palette**: each group A–L has a distinct hue; colour follows teams into the third-place row.
- **Live board updates**: every pick recalculates standings instantly (win=3, draw=1, loss=0).
- **Mathematically-qualified teams** (🔒): brute-force `clinchedTop2()` checks every W/D/L combination — scoreline-proof, returns exactly the teams FIFA lists as qualified. 7 teams locked: Mexico, USA, Germany, Argentina, France, Norway, Colombia.
- **Real flag images** via `flagcdn.com` SVGs (not emoji — emoji break on Windows).
- **Data model**: `TEAMS`, `PLAYED`, `FIXTURES` constants at top of script. To finalize a result: move from `FIXTURES` → `PLAYED` with real score; everything recomputes.
- **Engine**: `baseStats()` → cached `BASE`; `statsWith(picks)` clones and adds points; `standings(picks)` sorts groups + ranks thirds; tiebreak: points → GD → GF → seed index.
- **Design**: light "matchday programme" body with dark LED-style sticky board (green pitch stripes). System sans for UI, monospace for codes/points.
- **`r32-bracket.js`** prepared (not yet imported): 16 fixed Round-of-32 matches, third-place pools, all 495 official Annex C combinations (`THIRD_MAP`), and `assignThirds()` function.
