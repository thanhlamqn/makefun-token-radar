# makefun-token-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/thanhlamqn/makefun-token-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/thanhlamqn/makefun-token-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

A token launch radar: it reads a sealed snapshot of every protocol DefiLlama tracks,
works out which ones still record no token, ranks them by what their launch would be
worth, and hands in a web dashboard with the board, a detail view for each protocol and
the steps to farm it.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input <snapshot.json|directory>
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=3ky049kf&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>

## The model

Implied value follows only from the snapshot's own figures:

```
implied = round(tvl x category_multiple
  x (1 + clamp(change_7d, -60, 150) / 100 x 0.50 up, 0.75 down)
  x (0.60 + min(age_days, 1460) / 1460 x 0.40, x 0.85 when age_days < 90))
```

The base multiple is not a guess: for each category it is the median market-cap-to-TVL
ratio of the launched protocols in that category (three or more of them), falling back
to the global median where a category has too few launched names. Multiples are clamped
to 0.05x–1.50x. A decline hurts more than a rise helps, a protocol younger than 90 days
is discounted as too young to judge, and age stops mattering after four years.

The board covers the snapshot's whole 40-protocol watchlist and adds every rankable
pre-token protocol below the watchlist floor (186 more, 226 rows). Live-token protocols
are set aside with their market caps shown — they are the calibration set, not the
ranking.

## The program

`main.ts` (Node 22, standard library only) reads the snapshot, writes the ranked board
to `board.json`, writes the dashboard's data beside the page (`data.js`), copies
`dashboard/` and `screenshots/`, and writes `dossier.json` with the vendored make.fun
library. The dashboard is a static page with no network: the board, a detail view for
every protocol with its model breakdown and farm plan, live-token comparables, and a
Method view that states the model and its calibration table.

## Layout

- `main.ts` — the entrypoint
- `lib/model.ts` — the radar-2 model, the farm plans and the board checks
- `vendor/makefun-board.ts` — make.fun's dashboard-dossier library, as shipped in the kit
- `dashboard/` — the static page (index.html, app.css, app.js)
- `screenshots/` — the board, a protocol's detail view and a filtered view

## License

MIT. See LICENSE.
