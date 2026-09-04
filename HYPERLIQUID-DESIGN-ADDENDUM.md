# Addendum to the build brief — design and interaction spec

Paste this as a follow-up message in the same chat.

The brief covered data, architecture and bugs. This covers how the thing should
**feel**. Every pattern below is one I asked for on Arclight and got working, and
each one carries the gotcha that cost me time getting there — so implement the
pattern *and* avoid the trap.

Read the last section first. It's the one that matters most.

---

## A. Hard rule — this must not look like Arclight

I'm not asking for a reskin. Keep the **principles**, rebuild the **execution**.

**Keep (these are what make it good):**
- Everything is real, checkable, and shows its working
- Read-only, no wallet connect, no seed phrase
- Physics-driven interaction rather than decorative transitions
- Procedural canvas graphics, no image assets or icon libraries
- Plain-English explanation of every number
- Mobile designed separately, not degraded from desktop

**Change (these are Arclight's identity, not mine to reuse twice):**

| Arclight does | Do something else |
|---|---|
| Sage/forest green on near-black | Hyperliquid's mint/teal — and build the whole ramp fresh, don't hue-shift mine |
| Archivo + JetBrains Mono + Instrument Sans | Pick a different pairing with its own character |
| Fixed left rail + 8-item bottom tab bar | Try a different navigation model entirely — a top command bar, a search-first shell, a radial or sheet-based nav on mobile |
| Loading = candles building left to right, or particles converging into a ring | Invent a different signature loader. It should still be *about trading*, but not that |
| Characters = flat vector busts in circles | Different art system entirely — generative sigils, isometric scenes, emblem/crest shapes, layered depth. Same idea (a wallet gets a face), different execution |
| Share card = portrait left, big number right, footer rule | Compose it differently. Different aspect emphasis, different hierarchy |
| Section pattern: eyebrow → headline → lede → content | Find your own rhythm |

If at any point a screen could be mistaken for Arclight with the colours
swapped, that's a fail. Show me early mockups of the shell and one data page
before building everything, so I can call it.

---

## B. Interaction patterns to carry over

### 1. `?` tooltips on everything numeric
Every metric, score component, and jargon term gets a small `?` that explains it
in plain English on hover. Non-obvious labels get one too.

**Gotchas I hit:**
- Support both a lookup key *and* a free-text string in the same attribute. My
  first version only handled keys, so every hand-written tooltip silently
  rendered nothing while still showing a help cursor.
- `pointerout` fires when the cursor moves between children of the same anchor.
  If you schedule the hide on `pointerout` and bail early on re-entry, the
  tooltip dies under the cursor. Clear the timer *before* the identity check,
  and ignore events whose `relatedTarget` is still inside the anchor.
- If the tooltip handler runs in the capture phase and calls
  `stopPropagation()`, it will silently eat clicks on anything it wraps. A
  market tile that is both a tooltip anchor and a link must still navigate.
- On touch there's no hover: tap toggles. But a tap that scrolls the element
  into view must not immediately dismiss it — reposition on scroll instead of
  closing.

### 2. 3D tilt cards
The share card tilts toward the cursor in 3D with a light sheen that tracks the
pointer. On touch, tilt responds to a drag instead — and if `DeviceOrientation`
is available and permitted, gentle device tilt is a nicer mobile equivalent.

**Gotchas:**
- Put the pointer listener on the **card itself**, not its wrapper. I had it on
  the wrapper and the control buttons underneath inherited the tilt, which I
  hated.
- Keep the **tilt transform and the flip transform on separate elements**.
  Sharing one transform made the flip laggy and unreliable — a click with a
  still cursor did nothing, because the rAF loop only ran on pointermove.
- Disable entirely under `prefers-reduced-motion`.

### 3. Cursor-reactive fields with real physics
At least one page should have a field of objects that responds to the pointer
with actual physics — elastic collisions, momentum, damping. Not a hover
transition, a simulation.

**Gotchas:**
- **Gate repulsion by cursor speed.** My first version pushed objects away from
  a stationary cursor, so you could never hover one to read it. Compute pointer
  velocity and scale the push by it: sweep fast to shove things around, rest the
  cursor to read.
- Clip particle bursts to their container or they escape across the layout.
- Cap device pixel ratio on mobile (1.5 is plenty) and pause the rAF loop when
  the canvas is off-screen via IntersectionObserver. This is the single biggest
  battery/jank win.

### 4. Kinetic energy in data objects
Anything that represents a balance of forces should behave like one. On Arclight
a funding see-saw used a torsional spring — you could shove the beam and it
oscillated and settled back to the true rate, with the pans swinging on their
own inertia.

**Gotchas:**
- Torque scales with **distance from the pivot**, not proximity to it. I had it
  backwards and the thing felt dead.
- If a child sits inside a transformed parent, it already inherits that
  transform. I applied the tilt twice and the pans tore off the ends of the beam.
- Percentage offsets: be clear whether your fraction is of the half-width or the
  full width. Mine was applied at double scale and the pans left the viewport on
  mobile.
- Cancel the rAF on page leave — **and restart it on return.** My page only
  rendered once, so coming back to it left the physics frozen.

### 5. Reveal on scroll, section by section
Content animates in as you reach it, staggered within each batch — not all at
once on load.

**Gotchas:**
- Stagger by sorting the intersecting entries by their top position and applying
  an incremental delay, capped so a long list doesn't crawl.
- If you add a safety-net timer that force-reveals anything the observer missed,
  **restrict it to elements already in the viewport.** Mine revealed the whole
  page at once and undid the effect.
- **A repaint triggered by a user control must not replay the entrance
  animation.** Every filter click made the section flash to invisible and fade
  back — it read as a full page reload. Applying the "revealed" class instantly
  is not enough, because the keyframe still starts at opacity 0. You need an
  explicit no-animation class for user-triggered repaints.

### 6. Hover response nearly everywhere
Roughly 80% of interactive surfaces should react: lift, border warm, icon nudge,
number scale, siblings dim slightly so the hovered one leads. Subtle, fast
(200–260ms), and consistent.

### 7. The shareable card
This was the most-shared thing on Arclight, so it deserves real effort. It needs
a hero object (not just text on a gradient), download, copy-image, and
share-on-X with prefilled text, plus a **blur-the-address toggle** for people who
don't want to dox their wallet. Offer 16:9 and 9:16.

**Gotchas:**
- Compose the export by measuring a declared block: every gap named up front,
  then position as one unit. Hand-tuned offsets drift and collide the moment a
  font or number length changes — my label overlapped the headline number at one
  point purely because the type was large.
- Never let stat tiles touch the footer rule. Reserve the footer band first,
  then lay out into what's left.
- Cap DPR when exporting large canvases or you'll blow memory on phones.

### 8. Gamification, lightly
Archetypes/characters, a score with a visible breakdown, badges that clearly say
they're our own classification and not official. It should feel like getting a
result, not reading a dashboard.

### 9. Trader types with a profile picture
Every wallet gets assigned **one archetype out of a set** — Arclight had eleven
(The Monk, The Leviathan, The Sniper, The Architect, The Surfer, The Ghost…) —
each with a name, a one-line tagline, a longer description, and its own
**procedurally drawn portrait**. That portrait becomes the wallet's profile
picture everywhere: the wallet page, every leaderboard row, the share card, the
replay verdict screen.

This was the most-loved part of the product. Do it properly:

- **Assign from real behaviour**, never at random or from a hash of the address.
  Median hold time, fill count, maker vs taker share, how many markets, position
  sizing, how long they've been active — the type has to be *earned* by the data
  or the whole thing is a gimmick. On Hyperliquid you also have staking tenure
  and HIP-3 participation to draw on, so there's room for types Arcus couldn't
  express.
- **Drawn on canvas, deterministic, no image files.** A shared rig — ground,
  aura, silhouette, features — plus per-type motifs and props, so they read as
  one family rather than eleven unrelated drawings.
- **Legible at both extremes.** The same art must work at ~28px in a
  leaderboard row and at 300px on a share card. My first attempt was too busy
  and at small size "looked like a clock" — visually noisy, hard on the eyes,
  and every type looked the same. Test at the small size first, not last.
- **Make the types visually distinct at a glance** — different silhouettes and
  colour identities, not the same figure with different hats.
- Show the whole cast somewhere, so people want to find out which one they are.

**But do not reuse Arclight's art style.** Mine are flat vector busts in
circles. Pick a different visual system entirely — generative sigils, isometric
scenes, crests/emblems, layered depth, whatever — and give the types different
names and personalities suited to Hyperliquid.

### 10. Loading states are design
A bare spinner looks broken next to everything else. Loaders should be on-theme,
show real progress and name the step being done, and — where the page structure
is known — put a skeleton of it underneath so nothing jumps when data lands.

### 11. Pagination, not infinite inner scroll
Long lists paginate (10 per page worked well) with a compact pager. Don't nest a
scrollbar inside a card — it fights the page scroll on mobile.

---

## C. Mobile — 60% of my traffic

Treat this as the primary target, not the fallback.

- Test every page at **1440 / 390 / 360 / 320**. Nothing may extend past the
  viewport, no text clipped inside its own box, no horizontal scroll.
- **Design the mobile layout, don't reflow the desktop one.** Where a desktop
  row of five items becomes unreadable at 360px, restructure it — different
  grid, different order, different information density. Some things should be
  hidden on mobile rather than shrunk.
- Long prose must not be squeezed into a narrow column beside other elements.
  Break it to its own full-width row.
- Touch targets 44px minimum. Segmented controls go full-width.
- Anything hover-only needs a tap equivalent.
- Style the scrollbars — the default ones look foreign against a dark UI.
- Watch for canvases painted at a hardcoded size overflowing a smaller container
  on mobile. Measure the container instead.
- Respect `prefers-reduced-motion` throughout, and `viewport-fit=cover` plus
  `env(safe-area-inset-*)` for notched phones.

---

## D. Performance — it must never tax the device

- One rAF loop per visible animation, cancelled the moment it's off-screen or
  the page is left. Never leave a loop running on a detached canvas.
- Cap DPR at 1.5–2. Full retina on a large canvas is where phones die.
- Debounce resize handlers and bail if the element is no longer connected.
- Prefer CSS transforms and opacity for motion; avoid animating layout.
- Keep the DOM small — paginate, don't render 500 rows.
- Cache API responses in memory with a per-call TTL and dedupe in-flight
  requests for the same path.

---

## E. Also carry over

- **Light and dark themes**, both properly designed. I hit an unreadable
  dark-on-dark button in light mode because a brand colour was used as a text
  colour on a brand background — define an explicit "on-brand" token.
- **Namespace every CSS class by page prefix.** Three separate collision bugs on
  Arclight, each one invisible until something disappeared.
- Empty states deserve the same care as full ones — the "nothing here yet" page
  is often someone's first impression.
- A footer credit: Built by Pranjal, X: https://x.com/Crypto_Pranjal.

---

Before you build the full UI: show me the **shell** (nav, one data page, the
loading state, and a share card) so I can check it doesn't feel like Arclight.
I'd rather redirect at the mockup stage than after seven pages exist.
