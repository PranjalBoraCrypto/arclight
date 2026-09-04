# Build brief — a Hyperliquid version of Arclight

Paste this whole file as the first message of a new chat.

---

## Who I am and what I want

I'm Pranjal (X: @Crypto_Pranjal). I already built and shipped **Arclight**
(arclight-five.vercel.app) — a read-only analytics site for **Arcus** perpetuals.
It works, it's live, and it's good. I now want the same thing for
**Hyperliquid** (@HyperliquidX), built better, using everything the first one
taught me.

Everything below is hard-won from actually shipping the first one. Please read
all of it before writing any code.

---

## 1. Non-negotiables

**Read-only, forever.** The site never asks to connect a wallet, never asks for
a signature, and never, ever asks for a seed phrase. You paste an address, you
get a read. That's both the safety promise and the marketing.

**Every number must be real and checkable.** Serious traders will look at this.
If one figure is invented or subtly wrong, the whole thing is worthless. So:
- Never write a number you haven't pulled from the live API and verified.
- When you compute something derived, show the working in the UI.
- If the data can't support a claim, say so on the page instead of guessing.
- Before shipping any page with numbers on it, recompute them independently
  from the raw API and diff against what the page renders. I want to see that
  comparison.

**Readings, not recommendations.** Nothing on the site tells anyone what to
trade. Frame everything as observations with the method shown. Say plainly that
it has no idea what price does next.

**Never mention airdrops.** Not the word, not "points", not "eligibility", not
"farming", not "season 2", not a hint. This matters for two reasons: it would
cheapen the product, and implying that a score predicts a future reward would
be misleading to users. The rating card describes **what a wallet has actually
done on Hyperliquid** — past tense, descriptive, never predictive. If you find
yourself writing anything that implies a future benefit, stop and rewrite it.

**Mobile is 60% of traffic.** Design mobile and desktop as separate layouts,
don't degrade desktop to fit mobile. Test at 1440 / 390 / 360 / 320.

---

## 2. Architecture that worked — keep it

- **One self-contained `index.html`.** No framework, no build step, no
  dependencies. Vanilla JS, hand-rolled canvas graphics. It's ~7000 lines and
  that's fine. Deploys to Vercel from GitHub on push.
- **100% client-side.** Every visitor's browser talks to the exchange API
  directly. This is the key scaling property: rate limits are per-IP, so traffic
  spreads across visitors and there's no shared bottleneck and no server cost.
- **Hash routing** (`#/wallet/0x…`) so wallet links are shareable.
- **A small `api()` helper** with an in-memory cache keyed by path, a TTL per
  call site, in-flight deduplication, a 15s abort, and a distinct `RATE` error
  on 429. Everything goes through it.

---

## 3. Hyperliquid data — what I verified myself

The API is `POST https://api.hyperliquid.xyz/info`, JSON body `{"type": "..."}`.
**It is CORS-enabled** — I called it from a third-party browser origin and it
worked, so the client-side architecture carries over. An unknown `type` returns
422 with a clear message.

Confirmed working (I tested each one):

| type | gives you |
|---|---|
| `meta` | perp universe — names, szDecimals, maxLeverage |
| `metaAndAssetCtxs` | universe + live ctx: mark, oracle, funding, OI, day volume |
| `spotMeta` | spot universe (~135KB) |
| `perpDexs` | **HIP-3 builder-deployed perp DEXs** — name, deployer, oracleUpdater, feeRecipient, per-asset streaming OI caps |
| `clearinghouseState` `{user}` | perp account: accountValue, margin summaries, **assetPositions** → per-user OI |
| `spotClearinghouseState` `{user}` | spot balances incl. HYPE |
| `userFills` `{user}` | fill history |
| `portfolio` `{user}` | accountValueHistory / pnlHistory across day/week/month/allTime |
| `userFees` `{user}` | dailyUserVlm — daily volume, user vs exchange |
| `userRole` `{user}` | user / agent / vault — **check this first**, a vault reads differently |
| `delegations` `{user}` | current stake per validator |
| `delegatorSummary` `{user}` | delegated, undelegated, pending withdrawals |
| `delegatorHistory` `{user}` | **timestamped delegate/undelegate events → "staking since"** |
| `delegatorRewards` `{user}` | staking reward history |
| `validatorSummaries` | validator names, so you can show *who* they stake with |
| `tokenDetails` `{tokenId}` | token info — **HYPE returned 5.2MB**, it includes holders |

### Things to verify before you build on them — do not trust my recall

- **HIP-4.** I could not confirm this exists. `perpDexs` proves HIP-3 is real
  and queryable. Check the current Hyperliquid docs for what HIP-4 actually is
  (if anything) before designing around it. Don't invent a feature.
- **Hypurr NFT holders.** Not in the `info` API as far as I could tell. Likely
  a HyperEVM contract, which means an NFT indexer or a direct RPC call. Decide
  the source, verify it returns what you think, and tell me the tradeoff before
  building it in.
- **Genesis distribution recipients.** Not an API endpoint — it's a historical
  snapshot. Work out where that list comes from, how big it is, and whether it
  can live client-side at all. It may need to be a static bundled file. Flag the
  size cost honestly.
- **`tokenDetails` is 5.2MB.** Do not put that on a page load. If you need
  holder data, find a narrower path or precompute.

### Per-user open interest

`clearinghouseState` → `assetPositions[]` gives live positions per coin, and
`marginSummary.totalNtlPos` gives total notional. That's real per-user OI and
it's a genuinely interesting thing to show — how much a wallet has at risk right
now, across which markets, at what leverage. Present it as exactly that.

---

## 4. The rating card

Combine the signals into a single card describing how established a wallet is on
Hyperliquid. Suggested inputs, all verifiable:

- trading: lifetime volume, fills, markets touched, win rate, typical hold
- position: current OI, leverage in use, margin usage
- staking: amount delegated, how long staked (from `delegatorHistory`), which
  validators, rewards accrued
- holdings: HYPE balance (spot state)
- spread: HIP-3 dex participation, spot + perp, how many distinct venues
- tenure: first fill date, first delegation date

**Rules for the card:**
- Show the breakdown, not just the total. Every component visible with its own
  bar, so someone can see exactly why they scored what they scored.
- Label it as descriptive. Something like "how deep this wallet is in
  Hyperliquid" — never a rank, never a grade against others, never a
  qualification.
- Any badge must say it's Arclight's own classification, not an official one.
- Missing data shows as missing, not as zero. A wallet with no staking history
  isn't a 0 on staking, it's "hasn't staked".

Same as Arcus: give it a **character/personality system** (I had 11 procedurally
drawn canvas characters — The Monk, The Leviathan, The Sniper etc.) and a
shareable card with download / copy-image / share-on-X. That was the most
shared part of the whole product.

---

## 5. Bugs that cost me real time — do not repeat these

These are all real, all from the Arcus build.

**1. Never rebuild a position by counting fills forward.**
The fills endpoint is capped. For an active wallet the window opens
mid-position, so you see closes with no matching opens and the running size
drifts permanently wrong. One wallet computed to "+7.6 BTC held" when it held a
fraction of that. **Fix: anchor on the size the exchange reports right now and
walk backwards through the fills.** That gives the true size after every fill
and the real flat points where one trade ends and the next begins.

**2. PnL: use weighted-average cost, not cash flow.**
Tracking cash in/out double-counts the moment a position is partly closed — the
proceeds sit in your cash total *and* in the venue's realised PnL. A trade
displaying $45 live closed on "+$2,319". Proof: short 10 @ 100, buy back 5 @ 90,
price 95. Truth is $75. Cash-flow gives $125.

**3. One number, one series.** Whatever counts up during an animation and
whatever the final card shows must come from the same computation. If they can
disagree, they will, and in public.

**4. Class collisions in a single-file stylesheet.** Three separate incidents:
`.rv` (reveal) collided with a leaderboard value class and made every value
invisible; `.field` collided between a physics container and a search box,
making the search box 400px tall on mobile; `.lab` collided and drew a stray
border round a card label. **Namespace every new class by page prefix.**

**5. Duplicate function names.** I added a `countUp()` for a new page; the file
already had one. The later declaration silently won and every animated number on
another page started rendering as currency — the score read "$81". **Grep for
the name before you declare it.**

**6. Measure geometry, not computed style.** A progress bar had `width: 79%` in
computed styles and rendered nothing, because the element was an `<i>` and so
`display: inline`. Later, the collapsed sidebar looked correct by width but its
icons were sitting at x = −37, clipped away by overflow. **Assert on
`getBoundingClientRect()`.**

**7. Test the real document, not a wrapper.** My whole test harness wrapped the
HTML in `<!doctype html>` with a viewport meta. The deployed file had neither —
so it ran in quirks mode and would have rendered at ~980px on phones, and every
"mobile is clean" pass I'd run was testing a document that never shipped. I
caught it hours before launch. **Build the test page by injecting into the real
file, never by wrapping it.**

**8. Reveal-on-scroll animations replaying on every repaint.** Any control that
rebuilt a section replayed the entrance animation, so the page flashed to
opacity 0 and back on every click. Applying the "revealed" class instantly isn't
enough — the keyframe still starts at 0. **Need an explicit no-animation class
for user-triggered repaints.**

**9. Tooltips.** Two bugs: a `data-tip` holding a sentence rather than a lookup
key rendered nothing at all, and `pointerout` fires when moving between children
of the same element, so tooltips died under the cursor. Also: a tooltip
handler that `stopPropagation`s in the capture phase will silently eat clicks on
anything it wraps.

**10. Rate limits bite one visitor, not the fleet.** Per-IP limits mean traffic
scales fine, but a single visitor loading two heavy pages back to back
rate-limits *themselves*. Retries need real backoff (I ended at
1.5s/3.5s/7s/12s) and the UI should keep a skeleton up rather than flash a
failure at data that's seconds away.

**11. Analytics leaks the URL.** Vercel Web Analytics sends `location.href` with
every event — and my router keeps the wallet address in the hash, so every
address anyone searched was going to a third party. **Use the `beforeSend` hook
to reduce the URL to origin + pathname.** Also, hash routing means analytics
sees a single page forever, so send a named event per route with the page name
only.

**12. Ship a real `<head>`.** doctype, charset, viewport, description, Open
Graph and Twitter card with a 1200×630 image. Without OG tags a shared link is a
bare URL with no preview.

---

## 6. Testing discipline I want you to keep

- Playwright, headless, with the API stubbed by fixtures — plus live checks
  against the real API for anything numeric.
- A standing audit that walks **every page at 1440 / 390 / 360 / 320**, scrolls
  each one end to end so lazy sections lay out, and asserts: no element extends
  past the viewport, no text clipped inside its own box, no missing content, no
  JS or console errors.
- Screenshot and actually look at it. Several bugs were only visible in a
  render, not in assertions.
- When I report a bug, reproduce it with a measurement first. Twice I said
  something was broken, you checked geometry, and the numbers showed exactly
  why — that's much faster than guessing.
- Don't tell me something is fixed until a test proves it.

---

## 7. Pages that worked on Arcus

Carry these over, adapted:

1. **Search / home** — paste an address, plus live "try one" chips.
2. **Wallet read** — the archetype strip, headline tiles, an equity curve with
   a value axis and switchable time ranges, positions and fills (paginated 10 a
   page), score breakdown, a plain-English narrative revealed in batches.
3. **History run** — pick any trade, watch it replay candle by candle with
   entry/add/exit/liquidation markers, a running PnL, and a closing verdict card
   showing peak vs captured. Downloadable as video, 16:9 and 9:16. This was the
   single most compelling feature.
4. **Funding** — which markets pay to hold, what that's worth on a given size
   and leverage, break-even on fees, and an honest panel on what breaks it.
   Hyperliquid funding is hourly too, so this transfers directly.
5. **Radar** — movers, a heat map of every scanned market, and an indicator
   screener that shows its working (RSI, volume vs average, price vs moving
   averages).
6. **Leaderboard**, **Markets**, and a **"What is Hyperliquid?"** explainer page
   with interactive toys rather than walls of text.

New for Hyperliquid: **staking**, **HIP-3 builder markets**, and the
**rating card** as its own destination.

---

## 8. Design language

Dark-first, deliberately not a generic crypto dashboard. Arcus used sage/green;
Hyperliquid's brand is mint/teal on near-black — build a fresh palette in that
direction rather than recolouring the old one.

- Fonts: Archivo (display, 700/800), JetBrains Mono (data/labels), Instrument
  Sans (body).
- Procedural canvas graphics throughout — characters, share cards, the replay
  engine, loaders. No image assets, no icon libraries.
- Real physics for interactive bits: elastic collisions, pointer repulsion gated
  by cursor speed, torsional springs, particle systems with gravity.
- Staggered reveal on scroll via IntersectionObserver, and respect
  `prefers-reduced-motion` everywhere.
- Loading states are part of the design, not an afterthought — a bare spinner
  looks broken next to the rest.

---

## 9. How I want you to work

- Ask me before adding a whole new page. Tell me if something fits an existing
  one first.
- Verify against the live API before you build on an assumption. If you're not
  sure something exists, say so rather than filling the gap.
- Tell me when I'm wrong. If I ask for something that would mislead users or
  break a promise the site makes, push back.
- Keep the running commentary short. Show me the result.
- When you fix something I reported, tell me what the actual cause was — I want
  to understand my own product.

---

## 10. First session

Don't start writing the site. Start by:

1. Probing the Hyperliquid API properly and reporting back what's actually
   available, especially: HIP-3 via `perpDexs`, whether HIP-4 exists at all,
   and realistic sources for Hypurr holders and genesis distribution data.
2. Telling me which parts of the rating card are genuinely supportable from
   real data and which aren't.
3. Proposing the page list, and asking me about anything ambiguous.

Then we build.
