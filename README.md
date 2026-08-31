# Arclight

A read-only lens on [Arcus](https://app.arcus.xyz) perpetuals. Paste any EVM
wallet address and get its whole trading story explained in plain English —
positions, profit, funding, archetype, score and badges.

**Live:** https://arclight-five.vercel.app

## How it works

One static file. `index.html` is the entire application: no framework, no
build step, no backend, no database. It calls the public Arcus API
(`api.arcus.xyz`) directly from each visitor's browser, so rate limits are
spread across visitors rather than pooled on a server, and hosting costs
nothing at any traffic level.

## Features

- **Wallet dashboard** — plain-English narrative, trader archetype, 0–100
  score with a five-part breakdown, 12 badges, positions, equity curve,
  market mix, win rate and hold times
- **Characters** — eleven trading personalities, drawn procedurally on
  canvas so they stay crisp from a 28px avatar to a 1080px share card
- **History Run** — pick any trade a wallet made (or still holds), watch it
  replay on real candles, export it as video in 16:9 or 9:16
- **Similar wallets** — sampled from the same market's trade tape
- **Watchlist** — starred wallets, stored in the visitor's own browser
- **Markets** — a mouse-reactive physics field of every perp, plus the
  sortable table with per-market candles and a live tape
- **Leaderboard** — an arena that re-racks when you change the measure,
  plus the plain table
- **Learn** — an eight-question primer on perps for newcomers
- Shareable 3D cards rendered to PNG on canvas, dark and light themes,
  separate desktop and mobile layouts

## Configuration

The first lines of the `<script>` block in `index.html`:

```js
const SITE_NAME = "Arclight";
const REF_LINK  = "https://app.arcus.xyz/ref/PRANJAL";
const SITE_URL  = "https://arclight-five.vercel.app";
```

## Deploying

Any static host works. On Vercel: import this repo, framework preset
"Other", no build command, output directory `./`.

---

Built by Pranjal · [@Crypto_Pranjal](https://x.com/Crypto_Pranjal)
Not affiliated with Arcus or dYdX Labs. Not financial advice.
