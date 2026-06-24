# CLAUDE.md — FIFA26

Persistent context for Claude Code. Read this before editing.

## What this is

A single-file, phone-first "what-if" analyser for the 2026 FIFA World Cup group stage. The user picks outcomes for the remaining group matches; a sticky board fills in live — 12 colour-coded group cards (each showing the group's winner + runner-up) plus a row of the 8 best third-placed teams.

Audience: football fans on phones. The single job of the page: let someone play out the rest of the group stage and instantly see who qualifies.

**Current data state:** both group rounds complete (through June 23, 2026). Only the 24 Matchday-3 games remain in `FIXTURES`. Seven teams are already mathematically qualified (Mexico, USA, Germany, Argentina, France, Norway, Colombia) and render with a 🔒.

## Repo files

- `index.html` — the entire app (HTML + CSS + JS in one file).
- `r32-bracket.js` — phase-2 data: the official Round-of-32 mapping (16 fixed matches, third-place pools, all 495 Annex C combinations, `assignThirds()`). Not yet imported by `index.html`.
- `README.md` — human-facing overview + deploy steps.
- `CLAUDE.md` — this file.
- `LICENSE` — MIT.

## Hard constraints (do not break without asking)

- **One file, zero build, zero dependencies.** Everything (HTML + CSS + JS) lives in `index.html`. No frameworks, no bundler, no npm. It must run by opening the file directly.
- **No browser storage yet.** State is in-memory only. (localStorage is a planned feature — see roadmap — but keep it optional and guarded so the file still runs from `file://`.)
- **Phone-first.** Layout targets ~380px width. The board stays sticky at the top and must not push the fixtures off-screen.
- Flags are **images** from `https://flagcdn.com/<iso>.svg`, NOT emoji. (Flag emoji don't render on Windows — they degrade to two-letter codes. That's the bug we already fixed; don't reintroduce emoji flags.)

## Data model (top of the `<script>` block)

- `TEAMS` — `{ CODE: { n: name, iso: flagcdn-code, g: groupLetter } }`. 48 teams. ISO is lowercase alpha-2, or `gb-eng` / `gb-sct` for England / Scotland.
- `PLAYED` — finished matches: `["GROUP","HOME",homeGoals,awayGoals,"AWAY"]`. Source of all points + goal difference.
- `FIXTURES` — remaining pickable matches, grouped by date: `{ date, note, ms:[ [id,group,homeCode,awayCode], ... ] }`.

To finalize a match: move it from `FIXTURES` into `PLAYED` with the real score. Everything downstream recomputes.

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

## Roadmap (planned, not yet built)

1. **Optional score steppers** per match so goal-difference tiebreakers are real (removes the current simplification).
2. **Round-of-32 knockout pairings** rendered below the board once 32 are decided. The official mapping is already captured in `r32-bracket.js` (exported `R32_MATCHES`, `THIRD_COLS`, `THIRD_MAP` = all 495 Annex C combinations, and `assignThirds()`). Wire the board's group winners/runners-up into the 16 fixed match slots, then call `assignThirds(qualifyingThirdGroups)` to place the 8 third-placed teams.
3. **Persist picks** to `localStorage`, guarded with try/catch so `file://` still works.
4. A small "results last updated" note + easy data-refresh path.

## Deploy (GitHub Pages, branch root)

```bash
git add -A && git commit -m "update" && git push
```
Pages is served from `main` / root: repo Settings → Pages → Source: "Deploy from a branch" → `main` → `/ (root)` → Save. Live at `https://iprash.github.io/FIFA26/` within a minute or two of each push.
