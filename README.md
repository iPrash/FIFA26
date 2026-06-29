# FIFA26 — World Cup 2026 Bracket Predictor

A phone-first, zero-dependency web app for tracking and predicting the 2026 FIFA World Cup — from group stage results through the knockout bracket to the Final.

**Live:** https://iprash.github.io/FIFA26/

---

## Features

### Qualification Board

- **Sticky dark board** at the top of the page, always visible as you scroll through fixtures.
- **12 colour-coded group cards** (A–L), each showing the group's winner and runner-up.
- **Best 8 third-placed teams** row below the group cards — each team carries its group colour so you can see which group it came from.
- **Winners / Full Group toggle** — switch between showing just the top 2 or the full 4-team table per group.
- **Tap any group card** to open a detailed standings popup (Pts, GD, GF for every team).
- **Qualified counter** tracks how many of the 32 knockout spots are filled.

### Group Stage Matches

- All matches organised by date with venue information.
- **Tap to pick**: choose Home win, Draw, or Away win for each match.
- **Score steppers**: +/− buttons to set exact scorelines — goal difference updates live for tiebreaker accuracy.
- **Confirmed results** from `scores.json` are locked with a green border, "Final" badge, and large score display. Winner highlighted in gold, loser dimmed.
- **Live matches** show a red border, pulsing "LIVE" badge, and real-time scores from ESPN. Users can still pick outcomes, with live scores as the floor (can't go below actual score).
- **Standings engine**: Points → GD → Goals Scored → Seed Index. The 8 best third-placed teams are computed via full brute-force across all group combinations.

### Knockout Bracket

- **Full bracket from R32 to Final** — 16 Round-of-32 matches, 8 Round-of-16, 4 Quarter-Finals, 2 Semi-Finals, Third Place Match, and the Final.
- **Tap a team to pick the winner** — picks cascade automatically through R16, QF, SF, and Final.
- **Bracket Tree overlay** — tap the trophy button to open a full-screen bracket visualisation with match numbers, dates, times, venues, and connector lines.
- **Completed KO matches** display identically to group locked matches: split team cards, large scores with separator, winner in gold, "Final" badge. Picks are locked — users cannot override real results.
- **Live KO matches** show red border, live scores, and "LIVE" badge. Neither side is dimmed during play.
- **Group colour tags** appear next to each team in the bracket (white card with black border) so you can trace which group every team came from.
- **Third-place resolution** — the 8 qualifying third-placed groups are determined, and Annex C (all 495 valid combinations) maps them to the correct R32 slots.

### Live Scores

- **ESPN API integration** — automatically fetches live scores for both group and knockout matches.
- **60-second polling** while any match is in progress; stops when no matches are live.
- **Scores from `scores.json`** — confirmed final results loaded on page init. To update a result, only edit `scores.json` — never touch `index.html` data.
- **Score caching** — ESPN results cached in localStorage for instant display on reload.

### Picks & Persistence

- **Auto-save** — all group picks and bracket picks saved to localStorage.
- **My Picks** — permanently save your current picks. Restore them anytime, even after experimenting with other scenarios. Completed match results are always preserved.
- **Probable Picks** — auto-fill the bracket with most likely winners based on pre-tournament strength rankings (Polymarket odds). Completed matches always use the real result.
- **Clear Picks** — wipe all picks back to blank. Completed match results are preserved. My Picks remain saved separately.
- **Share** — generates a URL encoding both group and bracket picks. Uses native share on mobile, falls back to copy-to-clipboard. Shared links load picks automatically on open.

### Visual Design

- **Dark LED-style board** with green pitch stripes as the signature element. Light "matchday programme" body for fixtures.
- **12 maximally distinct group colours** — red, orange, yellow, green, cyan, blue, purple, magenta, lime, pink, brown, teal — consistent across board, fixtures, and bracket.
- **Gold highlights** for winners and selected picks. Green for qualified/confirmed. Red for live matches.
- **Monospace scoreboard typography** for codes, points, and scores. System sans-serif for UI text.
- **Responsive layout** — optimised for ~380px phone screens. 4-column group card grid (3-column in full mode). Swipe navigation between Group Matches and Knockout Bracket tabs on mobile.

### Installable App

- **Progressive Web App** — installable on iOS (Add to Home Screen) and Android (Install App prompt).
- **Service Worker** for offline support and update detection.
- **Refresh button** checks for app updates.
- **What's New overlay** shows feature updates once per version.

---

## Run It

It's one static file. Just open `index.html` in a browser — no build, no server, no dependencies.

Flag images load from [flagcdn.com](https://flagcdn.com). Live scores require an internet connection (ESPN API).

---

## Updating Scores

All match results are managed through `scores.json`. To record a finished match:

```json
{"m": 73, "t1": "RSA", "s1": 0, "s2": 1, "t2": "CAN"}
```

- Group matches: use `"g"` for the group letter, `"t1"`/`"t2"` for team codes, `"s1"`/`"s2"` for scores.
- Knockout matches: add `"m"` for the match number (73–104).
- Set scores to `null` for matches not yet played.

**Do not** modify `PLAYED` or `FIXTURES` in `index.html`. The app reads `scores.json` at runtime and updates everything automatically.

---

## Data Model

All data lives in `index.html`:

- **`TEAMS`** — 48 teams: `{ CODE: { n: name, iso: flagcdn-code, g: groupLetter } }`.
- **`PLAYED`** — hardcoded MD1 + MD2 results. MD3 and knockout results added dynamically from `scores.json`.
- **`FIXTURES`** — all Matchday 3 matches (permanent, never deleted).
- **`R32_MATCHES` / `R16_MATCHES` / `QF_MATCHES` / `SF_MATCHES` / `FINAL_MATCH` / `THIRD_PLACE`** — knockout bracket structure.
- **`THIRD_MAP`** — all 495 Annex C combinations mapping 8 qualifying third-placed groups to R32 slots.
- **`PROBABLE_STRENGTH`** — team ranking for auto-fill bracket predictions.

---

## Deploy

GitHub Pages, served from `main` branch root:

```bash
git add -A && git commit -m "update" && git push
```

Repo Settings → Pages → Source: "Deploy from a branch" → `main` → `/ (root)` → Save.

---

## License

MIT
