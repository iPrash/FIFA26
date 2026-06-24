# Road to 32 — World Cup 2026 What-If

A phone-first, single-file web app for playing out the final World Cup 2026 group-stage results and watching the Round-of-32 field fill in live. Pick a winner (or draw) for each remaining match and the qualification board at the top recomputes instantly: 12 group winners, 12 runners-up, and the 8 best third-placed teams.

**Live:** https://iprash.github.io/road-to-32/

## What it does

- Sticky **qualification board** of 32 slots that updates as you pick.
- Six teams show a 🔒 because they're already mathematically through, no matter the scoreline (Mexico, USA, Germany, France, Norway, Argentina as of MD2).
- All remaining matches grouped by date with a tap-to-pick `HOME / DRAW / AWAY` control.
- Win = 3, draw = 1, loss = 0. Total points shown next to each team, live.

## Run it

It's one static file. Just open `index.html` in a browser — no build, no server, no dependencies. Flag images load from [flagcdn.com](https://flagcdn.com).

## Updating results after each matchday

All data lives in three constants near the top of the `<script>` block in `index.html`:

- `TEAMS` — name, ISO flag code, and group for all 48 teams.
- `PLAYED` — finished matches as `["GROUP","HOME",homeScore,awayScore,"AWAY"]`. Points and goal difference are derived from these.
- `FIXTURES` — the remaining (pickable) matches, grouped by date.

To bake in a finished match: remove it from `FIXTURES`, add it to `PLAYED` with the real score. The board, standings, and 🔒 locks all recompute automatically.

## Known simplification

Picks are win/draw/loss only — they don't set a scoreline. So when two teams finish level on points, they're separated by the goal difference already banked from played games. Adding optional score inputs for true tiebreakers is on the roadmap.

## Deploy

GitHub Pages, served from the `main` branch root. See `CLAUDE.md` for the deploy steps and project conventions.

## License

MIT
