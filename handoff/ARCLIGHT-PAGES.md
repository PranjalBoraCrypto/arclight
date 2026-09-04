# Arclight — what's on each page, in plain terms

One website, eight pages, one file. Nothing is stored, nothing is logged in.
You paste a wallet address, it reads Arcus's public data in your own browser and
explains what it finds.

---

## The shell (every page shares this)

- A **left rail** on desktop with the eight pages. It collapses to icons only.
- A **bottom tab bar** on phones instead — the rail would eat half the screen.
- Pages don't reload. Clicking a tab swaps the content and the URL hash, and the
  page you left has its animations stopped so nothing keeps running in the
  background draining battery.
- Every number that isn't obvious has a small **`?`** next to it. Hover it (or tap
  on a phone) and you get one sentence of plain English explaining what the
  number is and why it matters. There are 26 of these.
- Things **fade and slide in as you scroll to them**, a few at a time rather than
  all at once. But when *you* click something — a filter, a sort button — it
  repaints instantly with no fade, because a full re-animation on every click
  reads like the page crashed and reloaded.

---

## 1. Home

**What's on it:** a search box for a wallet address, three shortcut buttons
("top by volume", "biggest winner", "trading right now") so you can look at a
real wallet without owning one, a live price ticker, your saved watchlist, four
tiles showing what Arcus did in the last 24 hours, the busiest markets, and a
closing panel with one of the eleven characters drawn on it.

**What it does:** it's the front door. The safety line under the search box is
deliberate — read-only, no seed phrase, no wallet connection, ever.

**How it loads:** the hero is there instantly. The ticker and the pulse tiles
fill in when the exchange answers; the numbers **count up** to their value rather
than snapping, so you notice them land.

---

## 2. Wallet — the main page

**What's on it,** top to bottom:

- **The trading personality.** Every wallet gets one of eleven characters — The
  Monk, The Leviathan, The Sniper and so on — with a portrait drawn on the fly
  in code, plus a name, a tagline and a description. It's earned from real
  behaviour: how long they hold, how often they trade, how many markets, how big
  they size. Not random, not from the address.
- **A shareable card** you can download or post. It tilts in 3D toward your
  cursor with a light sheen that follows the pointer, flips to a back side, and
  has a button to blur the address if you don't want to dox yourself. Exports in
  16:9 and 9:16.
- **Account value over time** — a chart with a hover crosshair, a value axis, and
  a readout that follows your cursor. Switchable time ranges.
- **The wallet, in plain English** — a written summary of what this trader has
  actually been doing.
- **A score out of 100** with the breakdown shown, so it's never a mystery number.
- **Open positions**, ten at a time with a pager.
- **Where the money goes** — which markets they actually trade, as bars.
- **Badges**, clearly marked as our own classification and not anything official.
- **Wallets like this one.**
- **Recent fills**, ten at a time with a pager. No scrollbar inside the box —
  that fights the page scroll on a phone.

**How it loads:** the page is about *who this trader is*, so the wait builds a
portrait. Particles stream in from the edges of the screen, converge into a
ring, and the wallet's own seal resolves in the middle of it. A percentage
counts up and it names the step it's on — "pulling open positions", "walking the
trade history", "working out what it all means". Underneath, a grey skeleton of
the real layout is already sitting there, so when the data lands nothing jumps.
The particle pattern is seeded from the address, so every wallet loads slightly
differently.

---

## 3. History run — the replay

**What's on it:** pick one of the wallet's trades and watch it happen again. The
price chart draws itself forward in time, entry and exit markers drop in as they
happened, the camera follows the price, particles burst on each fill, and at the
end it holds for a beat and shows a verdict card — what they made or lost and
what it says about them.

**What it does:** this is the bit people screenshot. It turns a row in a table
into something you can watch.

**How it loads:** a candle chart builds itself left to right — a tape drawing
itself — with a progress bar and a label saying what's being fetched. Then the
list of plays appears, biggest moves first.

---

## 4. Radar — the whole market, scanned

**What's on it:** movers over 24 hours with small sparklines, a heat map of where
the activity is, and a written read of what the numbers are saying.

**How it loads:** the same candle-building loader, because it's genuinely
scanning every market one at a time and the progress is real, not fake.

---

## 5. Funding — get paid to wait

**What's on it:** every market's hourly funding payment, read from the actual
payment history rather than the current snapshot, so a rate that spiked once
doesn't look like a permanent income stream.

- A hero showing the best-paying market right now.
- **A hedged / unhedged toggle.** This is the honest bit: every Arcus market is a
  perpetual, there's no spot to buy against it, so the other leg of the trade has
  to sit on another venue. Counting only the Arcus side made the yield look like
  96%. Counting the capital on both sides makes it 26%. We show both and say why.
- **The board** — every market ranked, with fees already subtracted.
- **The pattern** — the fattest funding sits in the markets that are hardest to
  hedge, and we compute that from the live scan rather than asserting it.
- **What makes this real, and what breaks it** — the risks, written out.
- A **see-saw** you can shove with your cursor. It's a real torsional spring: it
  oscillates, the pans swing on their own inertia, and it settles back to the
  true funding rate. Shove it harder and it swings further.
- A **refresh button**, because funding changes hourly and the page says how long
  ago it was read.

---

## 6. Markets

**What's on it:** two views.

- **Field** — every market is a floating body. Bigger body means more money
  moved that day, green means up, red means down. They collide with each other
  elastically. Sweep your cursor fast and you shove them around; rest your cursor
  and they settle so you can actually read one. (First version pushed things away
  from a stationary cursor and you could never hover anything — that's why the
  push is scaled by how fast you're moving.)
- **Table** — the same data as a sortable list, filtered by crypto / stocks /
  commodities / indices.

Clicking any market opens it on Arcus.

---

## 7. Leaderboard

**What's on it:** the top 100 traders, in two views — an **arena** with a podium
for the top three, or a plain table. Switchable between 24h / 30d / all time, and
sortable by volume, profit, losses or fees paid.

Changing the sort **re-racks the existing rows in place** rather than refetching,
so you actually see people move up and down.

Under it, one honest line: profit counts closed trades only, so someone sitting
on a big open winner looks flat here.

---

## 8. Learn

**What's on it:** the concepts, explained with things you can touch rather than
paragraphs.

- **What leverage actually does to you** — interactive.
- **Why holding costs money** — the funding see-saw again.
- **The stock market that never shuts** — why 34 of the 51 markets are equities.
- An **FAQ** that opens and closes.
- **Where every number comes from** — the exact API endpoints, so anyone
  suspicious can check the working themselves.

---

## The three loading states

Nobody gets a spinner. A spinner next to everything else would look broken.

1. **Converging particles → a portrait** — the wallet page.
2. **A candle chart building itself left to right** — radar, funding, replay.
3. **Grey skeletons of the real layout** — sitting under both of the above, so
   content never jumps when it lands.

All three show a real percentage and name the step being worked on.

---

## When things go wrong

- **Empty state** is designed — "no trades to replay" gets a proper panel, not a
  blank space.
- **Error state** is designed, and it distinguishes two different problems:
  Arcus being unreachable, versus Arcus rate-limiting your device specifically.
  The second one is your fault for refreshing too fast and it says so.
- Rate limits back off and retry (1.5s → 3.5s → 7s → 12s) with the skeleton held
  up meanwhile, rather than giving up and showing a hole.

---

## On phones

60% of visitors are on a phone, so the mobile layout is designed separately, not
shrunk down from the desktop one. Tested at 1440, 390, 360 and 320 pixels wide —
nothing runs off the edge, no text clipped, no sideways scroll. Touch targets are
at least 44px. Anything that works on hover has a tap equivalent. Canvas
resolution is capped so phones don't cook, and every animation stops the moment
it scrolls off screen.

If someone's phone is set to "reduce motion", the physics and the animations turn
themselves off.
