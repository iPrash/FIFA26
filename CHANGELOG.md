# Changelog — FIFA26

All notable changes to this project, in reverse chronological order.

---

## 2026-06-30 · Morning — Code Review Cleanup

- **Qualified counter fixed**: now shows 32/32 once all groups are complete (counts top-2 locked teams + 8 best thirds). Future-proof — only adds thirds when all groups are done.
- **ESPN XSS protection**: added `esc()` sanitizer for player names, minutes, and clock text from ESPN API.
- **CSS consolidation**: extracted `.overlay` base class (4 overlays shared identical positioning), `.badge` base class (locked/live badges shared 95% CSS).
- **Removed unused CSS**: `--pick` variable (duplicate of `--pitch`), `.mono` class (never applied).
- **Moved inline styles to CSS**: `.dot` background, `#hint` margin, bracket button emoji font-size.
- **Memoized `resolveKnockout()`**: caches expensive third-place brute-force computation, returns deep copies since callers mutate results.
- **Extracted `getTodayStr()`**: shared helper for scroll functions, removing duplicate date-string construction.

---

## 2026-06-30 · Morning — Documentation Audit

- **README overhauled**: added penalty support, goal scorers, pick mode indicators, collapsible board, scroll-to-live, refresh cooldown, updated `scores.json` format examples.
- **CLAUDE.md updated**: reflects current project state (all groups complete, KO in progress), lists built features, updated repo files and data model.
- **Probable → Likely**: all remaining doc references updated.

---

## 2026-06-30 · Early — Penalties, Pick Mode Highlights & Rename

- **Penalty support from ESPN**: live and completed KO matches now extract shootout scores and determine winner correctly.
- **Pick mode indicator**: Likely Picks and My Picks buttons highlight gold when active. Confirmation dialog protects against accidental overwrites.
- **Rename Probable → Likely**: all code, IDs, comments, and user-facing text unified.
- **Uniform button styling**: pick buttons share one consistent style on both main page and overlay.
- **Live match picking**: can now pick winners from live KO matches in brackets tab with visual feedback.
- Scores updated for GER-PAR (pens 3-4) and NED-MAR (pens 2-3).

---

## 2026-06-29 · Night — Goal Scorers, Scroll & Live Match Fixes

- **Goal scorers for every match**: all 74 completed matches (MD1–MD3 + KO) now have scorer names and minutes in `scores.json`.
- **Fix goal display for late-locked matches**: matches locked via ESPN cache before `fetchScores` ran would miss goals — now backfills goals from `scores.json`.
- **Live KO matches now look identical to completed ones**: score alignment, win/draw highlights, separator styling, thicker red border all apply to both.
- **Fix ESPN goal scorer extraction**: use team ID mapping and `scoringPlay` flag instead of broken `displayName` lookup.
- **Scroll-to-live-match**: refresh now scrolls directly to the live match card (not just today's date header), awaiting ESPN data before scrolling.
- **Scroll functions preserved**: renamed `scrollToToday`/`scrollToTodayBracket` → `scrollUpGroup`/`scrollUpBracket`, scoped group search to groups panel only.

---

## 2026-06-29 · Evening — Collapsible Board & Bracket Layout Polish

- **Collapsible board drawer**: board starts collapsed showing one row of group cards. Green chevron tab below the board expands/collapses the full view.
- **Bracket match layout**: match number box, team name stacking, and grid structure now match group stage cards exactly.
- Fix iOS Safari text inflation on Jul 1 bracket matches (`-webkit-text-size-adjust`).

---

## 2026-06-28 · Late — Default to Bracket Tab & Scroll to Today

- App now starts on the Knockout Bracket tab on mobile (group stage is complete).
- Bracket panel scrolls to today's date header on load (live matches take priority).
- Switching tabs scrolls to today's matches on both group and bracket sides.
- Removed bracket peek animation (no longer needed).
- README rewritten with comprehensive feature documentation.

---

## 2026-06-28 · Evening — Bracket Scores, KO Match Display & Group Colours

### Bracket Scores
- Knockout match scores now display in both list view and bracket tree overlay.
- Completed matches render identically to group stage locked matches: split team cards, big scores with separator, winner highlighted in gold, "Final" badge on venue line.
- Live KO matches show red border + "🔴 LIVE" badge with live score, neither side dimmed during play.
- Bracket tree overlay shows scores inline next to team codes; completed matches get green background.

### Score Updates
- R32: RSA 0-1 CAN (Match 73).

### KO Match Integrity
- Picks locked on completed matches — click handlers blocked, `bkt-team--pickable` suppressed.
- Probable Picks uses actual winner for completed matches, only predicts remaining.
- Clear Picks and My Picks restore both preserve completed match winners.
- ESPN live score handler optimised: cached bracket resolution per batch.

### Visual Improvements
- 12 group colours redesigned for maximum distinctness (red, orange, yellow, green, cyan, blue, purple, magenta, lime, pink, brown, teal).
- Group letter tags styled as small white cards with black border for readability.
- Group letters added to completed KO match team cards.
- Bracket tree font sizes increased: teams 10→12px, match info 7→8px, venue 6→7px.
- Incomplete KO match cards enlarged to match group stage card sizing.
- Footer text updated for knockout stage.

---

## 2026-06-27 · Evening — Live Card Fix, Scores & Bracket Alignment

### Live Match Card
- Simplified live match rendering: same layout as normal matches with red border + LIVE badge.
- Live scores shown inline inside team buttons (home right-aligned, away left-aligned).
- Reducing winner's score now pulls loser's score down correspondingly (respects live floor).
- "Live score floor" flash message when stepper is blocked by actual score.

### Score Updates
- Groups K and L complete: PAN 0-2 ENG, CRO 2-1 GHA, COL 0-0 POR, COD 3-1 UZB.
- Fixed day-of-week on all R32/QF/SF/Final bracket dates (were all one day behind).

### Bracket Overlay
- R16 and QF columns shifted up for better vertical centering with R32.
- SF and Final column alignment improved.
- Connector lines between rounds properly span from center of top match to center of bottom match.

### Other
- Shortened "Qualified" to "Qual" in board counter to prevent line wrapping with 🟢.

---

## 2026-06-27 · Morning — Scenario Locks, Live Picks & Bracket Fix

### Scenario Locks (🟢)
- New 🟢 icon on the board for teams mathematically locked **in your pick scenario** (not just confirmed results).
- Same brute-force as 🔒 but treats your picks as settled, only enumerates unpicked matches.
- Board counter shows "🔒 Qualified: N/32 + M🟢" when scenario locks exist.
- Group card headers show 🟢🟢 when both top-2 teams are scenario-locked.

### Live Match Picks
- Users can now pick winners during live matches — no longer read-only.
- Live score displayed as centered score card (like Final) with pickable buttons below.
- Score steppers enforce live scores as a floor — can only add goals, never reduce below actual score.
- Picking the losing side auto-sets their score above the leader. Picking draw auto-equalizes.
- ESPN poll bumps your pick scores up if live scores increase during the match.

### Third-Place Bracket Fix
- Fixed premature locking of third-place R32 slots caused by proxy GD tiebreaking.
- When third-place teams are tied on points at the 8th/9th qualifying cut, all possible subsets are now enumerated.
- Prevents incorrect team assignments (e.g., ECU locked into match 79 when SCO could still shift the Annex C mapping).
- Slots only lock when invariant across every possible combination.

### ESPN API Fixes
- Fixed live match detection: uses `status.type.state` ("in"/"post") instead of status name (handles STATUS_SECOND_HALF, STATUS_FIRST_HALF, etc.).
- Fetches both default and today's dated scoreboard to catch cross-day matches.
- Auto-scroll prioritizes live matches over today's date header.

---

## 2026-06-27 · Early AM — ESPN Live Scores

### Auto-Refresh from ESPN API
- App now fetches live scores from ESPN's public API on page load.
- Completed matches auto-lock without needing a scores.json update.
- In-progress matches show a pulsing 🔴 LIVE badge and poll every 60 seconds.
- ESPN scores cached in localStorage for instant display on reload.
- `scores.json` remains as fallback (file:// and offline).
- 48-team `ESPN_TEAMS` mapping handles all name conversions.

---

## 2026-06-26 · Late Night — Probable KO Picks & Golden Winners

### Probable Picks → Full Bracket
- Probable Picks now fills the entire knockout bracket (R32 through Final), not just group matches.
- Uses Polymarket strength ranking (France → Argentina → Spain → ...) to pick the favored team in each matchup.
- First-visit auto-load also fills bracket picks.

### Golden Winner Borders
- Picked winners across all views (Groups tab, Bracket tab, Overlay) now highlighted with a golden border.
- Completed (locked) match winners also use golden border styling.
- Removed redundant ✓ tick marks from group match picks.

### Overlay Enhancements
- Date and time shown next to match number on all bracket overlay matches.
- Final and 3rd Place show date/time on a separate line below the match number.
- Brighter match number and date text for readability.

---

## 2026-06-26 · Night — Bracket Picks, My Picks & UI Polish

### Bracket Picks
- Tap any team in a knockout match (bracket tab or overlay) to pick a winner.
- Picks cascade: winning team auto-fills the next round's slot through to the Final.
- Upstream group-pick changes automatically invalidate affected bracket picks (multi-pass).
- Bracket picks persist to localStorage and survive page reloads.

### Share Includes Brackets
- Share URL now encodes both group picks and bracket picks (`&ko=` param).
- Share toast shows combined count (e.g. "18/24 group + 5 bracket picks").
- Native Web Share API used on phones; clipboard fallback on desktop.

### My Picks Snapshot
- New "My Picks" button saves and restores your manual picks independently.
- My Picks are never wiped by Clear or Probable — always restorable.
- Combined My Picks + Share into a unified split button (green, with universal share SVG icon).

### Bracket Overlay Buttons
- Probable Picks, My Picks + Share, and Clear Picks buttons added to the bracket overlay header.
- Buttons sit on their own row below the heading, always visible while scrolling the bracket.
- Styled for the dark overlay theme (blue/green/red outlines).

### UI Polish
- Bracket button centered in the bar panel (between hint text and action buttons).
- Hint text simplified: "Tap a Team to Choose. / Board Updates Live."
- Probable Picks button styled blue; My Picks styled green.
- Tooltips on all buttons.
- Clear Picks no longer requires confirmation (easy to restore via My Picks or Probable).

---

## 2026-06-26 · Evening — Bracket Tree Overlay

- New "🏆 Bracket" button in the board header opens a full tournament bracket popup.
- CSS Grid–based bracket tree with symmetric left/right halves: R32 → R16 → QF → SF → Final.
- Connector lines link each pair of matches to their next-round target.
- Shows confirmed teams with flags and group colours; unresolved slots display labels (W73, 3rd, etc.).
- 3rd-place match displayed below the Final.
- Horizontally scrollable on mobile for full bracket visibility.
- Click outside or × to dismiss.

---

## 2026-06-26 · Afternoon — Auto-Scroll & Footer Update

- App smoothly scrolls to today's first fixture on load, accounting for the sticky board height so the date header is visible.
- Updated footer text to reflect Matchday 3 progress: "Matchday 3 underway: 6 groups complete."

---

## 2026-06-26 · Morning — Partial Third-Place Resolution

### Bracket Logic
- R32 third-place slots now resolve as soon as they're mathematically locked, without waiting for all 12 groups to finish.
- Full brute-force: enumerates every W/D/L outcome for open groups, cross-products all combinations, ranks 12 thirds, runs Annex C assignment, and locks any slot that gets the same team in every scenario.
- Result: USA vs BIH (Match 81) now shows immediately with 6 groups complete — matches all major sports outlets.

### Bug Fix
- Fixed double-counting of user picks in the brute-force: open-group stats now start from confirmed results only (BASE), not from picks-inflated stats.

### Performance
- Added safeguard: brute-force skips if cross-product exceeds 100k combinations (prevents browser freeze with many open groups).

---

## 2026-06-25 · 9:00 PM — PWA Stability & Board Cleanup

- Fixed blank page caused by stale localStorage picks after fixture ID migration.
- Service worker now auto-reloads the page when a new version activates — no more stuck blank pages.
- Board counter simplified to "🔒 Qualified: 13/32" (removed confusing picks counter).
- Scores updated: Group F MD3 complete (JPN 1-1 SWE, TUN 1-3 NED).

---

## 2026-06-25 · 7:00 PM — Match Numbers, Partial Slots, Refresh & Branding

### Match Numbers
- Group stage fixtures now use official FIFA match numbers (49–72) instead of internal IDs.
- Match number displayed below group letter badge on each match card.
- scores.json updated with `m` field for each match.

### Bracket Logic
- Single locked team with undecided position (e.g. COL) now shows as "COL / TBD" in both possible bracket slots instead of disappearing.
- Fixed bug where the `couldDrop` check was too broad, incorrectly hiding USA and ARG from the bracket.

### UI
- App renamed to MyFIFA26 (header + page title).
- Added refresh button (↻) next to help button for PWA users.
- Help tooltip added for consistency with refresh button.

---

## 2026-06-25 · 5:00 PM — Smarter Bracket Slots

### Bracket Logic
- When two locked teams still play each other (e.g. FRA vs NOR), bracket shows both as "FRA/NOR" with dual flags instead of guessing who's 1st vs 2nd.
- When a single locked team has remaining matches that could change their position (e.g. COL vs POR), bracket no longer prematurely assigns them to a slot — waits for the result.
- Dual slots render in mirror-image style consistent with other bracket cards: home = `flag CODE / flag CODE`, away = `CODE flag / CODE flag`.

---

## 2026-06-25 · 3:00 PM — Progressive Web App

- MyFIFA26 is now installable as an app on iOS, Android, and desktop via "Add to Home Screen".
- Added `manifest.json`, service worker (`sw.js`), and app icons (192px, 512px, SVG).
- Service worker caches flag images (cache-first) and app files (network-first) for offline support.
- "Install App" link added to Help overlay — opens a super-overlay with platform-specific install instructions (iOS Safari, Android Chrome, Desktop).

---

## 2026-06-25 · 1:00 PM — Bug Fix & Mirror Layout

### Bug Fix
- Fixed R32 third-place team mapping (`THIRD_HOST_TO_MATCH` was scrambled), which caused illegal same-group pairings (e.g. GER vs ECU rematch).

### UI
- Team cards now mirror-image: home = flag left / left-aligned text, away = right-aligned text / flag right. Applied to both group stage and bracket.
- Tightened date line spacing (smaller gap, letter-spacing) and added `white-space:nowrap` to prevent wrapping.
- Bracket team flags (22×16px) and fonts (13px code, 10px name) bumped up to match group stage.

### Data
- Scores updated through Jun 25 (Group A MD3, Group B MD3, Group C MD3, Group E MD3).

---

## 2026-06-25 · 11:00 AM — Venues & Polish

### Stadium Venues
- Every group stage match now shows its stadium and city centered above the team cards (e.g. "BC Place, Vancouver").
- All knockout bracket matches (R32 through Final) show venue with kick-off time on the same line (e.g. "3:00 PM ET · MetLife Stadium, New Jersey").
- Bracket date headers now include day-of-week to match group stage format.

### UI Tweaks
- Date line reordered: time → group → matchday (was matchday → group).
- Group letter badges softened from black to muted grey.
- All times standardized to `:00` format for whole hours.
- "Final" badge for completed matches repositioned into venue row.

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
