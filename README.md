# Arclight

A read-only lens on [Arcus](https://app.arcus.xyz) perpetuals. Paste any EVM
wallet address and get its whole trading story explained in plain English —
positions, profit, funding, archetype, score and badges.

**Live:** _(add your Vercel URL here)_

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
- **Compare** — two wallets head to head across 14 measures
- **Similar wallets** — sampled from the same market's trade tape
- **Watchlist** — starred wallets, stored in the visitor's own browser
- **Markets** — all 51 perps, filterable and sortable, with per-market
  candles and a live tape
- **Leaderboard** — top 100 by volume, profit or fees
- **Learn** — an eight-question primer on perps for newcomers
- Shareable 3D cards rendered to PNG on canvas, dark and light themes,
  separate desktop and mobile layouts

## Configuration

The first lines of the `<script>` block in `index.html`:

```js
const SITE_NAME = "Arclight";
const REF_LINK  = "https://app.arcus.xyz/ref/PRANJAL";
const SITE_URL  = "";   // set to your domain once deployed
```

## Deploying

Any static host works. On Vercel: import this repo, framework preset
"Other", no build command, output directory `./`.

---

Built by Pranjal · [@Crypto_Pranjal](https://x.com/Crypto_Pranjal)
Not affiliated with Arcus or dYdX Labs. Not financial advice.
