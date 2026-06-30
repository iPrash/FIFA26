# CLAUDE.md — FIFA26

Persistent context for Claude Code. Read this before editing.

## What this is

A single-file, phone-first World Cup 2026 predictor — group stage results, knockout bracket picks, and live score tracking. 12 colour-coded group cards show qualification standings; a full bracket from R32 to the Final lets users pick winners and see cascading results.

Audience: football fans on phones. Pick match outcomes, track live scores, and predict the tournament winner.

**Current data state:** All group stage matches (MD1–MD3) are complete. Knockout stage is in progress — results come from `scores.json` at runtime. ESPN API provides live scores, goal scorers, and penalty shootout data.

## Repo files

- `index.html` — the entire app (HTML + CSS + JS in one file).
- `scores.json` — match results: group + knockout scores, goal scorers, ET/penalty data.
- `CHANGELOG.md` — reverse-chronological feature log with date+time headers.
- `README.md` — human-facing overview + deploy steps.
- `CLAUDE.md` — this file.
- `LICENSE` — MIT.

## Hard constraints (do not break without asking)

- **One file, zero build, zero dependencies.** Everything (HTML + CSS + JS) lives in `index.html`. No frameworks, no bundler, no npm. It must run by opening the file directly.
- **localStorage for picks.** Group picks, bracket picks, My Picks, ESPN score cache, and What's New version all use localStorage (guarded with try/catch so `file://` still works).
- **Phone-first.** Layout targets ~380px width. The board stays sticky at the top and must not push the fixtures off-screen.
- Flags are **images** from `https://flagcdn.com/<iso>.svg`, NOT emoji. (Flag emoji don't render on Windows — they degrade to two-letter codes. That's the bug we already fixed; don't reintroduce emoji flags.)

## Data model (top of the `<script>` block)

- `TEAMS` — `{ CODE: { n: name, iso: flagcdn-code, g: groupLetter } }`. 48 teams. ISO is lowercase alpha-2, or `gb-eng` / `gb-sct` for England / Scotland.
- `PLAYED` — finished matches: `["GROUP","HOME",homeGoals,awayGoals,"AWAY"]`. Hardcoded with MD1 + MD2 results. MD3 results are added dynamically at runtime by `fetchScores()`.
- `FIXTURES` — all Matchday 3 matches, grouped by date: `{ date, note, ms:[ [id,group,homeCode,awayCode,venue], ... ] }`. These stay permanently — never delete entries. `fetchScores()` uses `fixByTeams` to map team codes to fixture IDs.
- `scores.json` — the single source of truth for match results. Contains all MD3 group matches and knockout matches. Each entry can include: scores, goal scorers (`goals`), extra time flag (`et`), penalty shootout scores (`pen`).

**To finalize a match: only update `scores.json` with the real score.** Do NOT modify `PLAYED` or `FIXTURES` in `index.html`. At runtime, `fetchScores()` reads `scores.json`, sets `locked[id]`, pushes into `PLAYED`, and recomputes standings.

## Engine

- `baseStats()` → points/gf/ga/gd per team from `PLAYED` only. Cached as `BASE`.
- `statsWith(picks)` → clones `BASE`, adds **points only** for picks (W=3 / D=1 / L=0). Picks never change goal difference (no scoreline) — this is intentional and documented as the current simplification.
- `standings(picks)` → per-group tables + ranked thirds. Tiebreak order: points → goal difference → goals scored → seed index (current standing, frozen at load). Note: FIFA's official 4th/5th/6th criteria (fair-play score, then FIFA ranking) are **not** tracked here — fine for a toy, worth a footnote if thirds finish level.
- `clinchedTop2()` → static set of teams guaranteed top-2 regardless of scoreline. Rule: brute-force every W/D/L combo of a group's remaining games; a team is locked only if in **every** combo at most one other team can finish on equal-or-higher points. This is scoreline-proof and returns exactly the teams FIFA lists as qualified. Don't replace it with a goal-difference-dependent check — that over-locks.

## Design system

- Light "matchday programme" body (`--paper #f6f5ef`, `--ink #0d1b14`) with a dark LED-style **sticky board** (green pitch stripes) as the signature element. Keep the contrast: dark board, light fixtures.
- **Group identity by colour.** Each group A–L has a distinct hue in the `GC` map (a 12-step spectrum red→rose). The board's top 24 are 12 `.gcard` group cards (winner = rank 1, runner-up = rank 2). A third-placed team carries its **group colour** into the `.thirds` row so you can see which group it came from. Don't collapse these back to a single green.
- `--pitch #1f9d57` is reserved for qualified/positive cues; 🔒 marks mathematically-through teams. (gold/silver/bronze vars still exist but the board now uses rank numbers + group colour, not medal colours.)
- Type: system sans for UI, `ui-monospace` for codes/points/labels (scoreboard feel).
- Spend boldness on the board; keep fixtures quiet and dense.

## What's built (features already implemented)

- Score steppers with +/− buttons for exact scorelines and live GD tiebreakers.
- Full knockout bracket: R32 → R16 → QF → SF → Third Place → Final, with cascading picks.
- Bracket Tree overlay with match numbers, dates, venues, and connector lines.
- localStorage persistence for all picks (group, bracket, My Picks).
- ESPN live score integration with 60-second polling, goal scorers, and penalty support.
- Likely Picks auto-fill from `LIKELY_STRENGTH` rankings.
- Pick mode system with gold border indicators and confirmation dialogs.
- Collapsible board drawer, scroll-to-live-match, refresh cooldown with cache busting.
- PWA with service worker, offline support, and What's New overlay.

## Roadmap (planned, not yet built)

1. Fox Sports match highlights integration (CORS-blocked from browser — needs proxy or server).
2. "Results last updated" timestamp note.

## Deploy (GitHub Pages, branch root)

```bash
git add -A && git commit -m "update" && git push
```
Pages is served from `main` / root: repo Settings → Pages → Source: "Deploy from a branch" → `main` → `/ (root)` → Save. Live at `https://iprash.github.io/FIFA26/` within a minute or two of each push.
