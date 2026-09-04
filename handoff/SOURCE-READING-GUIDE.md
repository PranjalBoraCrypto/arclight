# Reading guide for `arclight-source.html`

I'm attaching the complete source of Arclight — all 7,203 lines of it. It's my
own code and I'm giving it to you deliberately, so you can see exactly what
"finished" means rather than guessing from my descriptions.

**Read this guide before you open the file.**

---

## The one rule

**This is a reference for craft depth. It is not a template.**

Read it to understand *how deep the detail goes* — how many states each element
has, how physics is actually implemented, how a loader is composed, how much CSS
one card really needs. Then build HyperFlow's own version of that depth.

If you copy the structure, the class names, the palette, the character art, the
loader, or the section rhythm, you will produce Arclight in mint and I will
throw it away. I have already asked you once not to clone it, and you got that
right — don't lose it now that you can see the source.

Concretely: **study the mechanisms, invent your own surfaces.**

---

## What the file is

- One self-contained HTML document. No build step, no dependencies.
- Lines 1–1571: `<head>` — meta, OG tags, then ~1,250 lines of CSS
- Lines 1573–1814: markup — nav rail, mobile tabs, page containers, SVG sprite
- Line 1815: `<script>` — the character art module (11 procedurally drawn traders)
- Line 2185: `<script>` — the main app, ~5,000 lines
- Line 7188: analytics snippet

## Where things live

| System | Line | Why it's worth reading |
|---|---|---|
| `api()` | 2410 | cache + TTL + in-flight dedupe + abort + a distinct `RATE` error. Small, does a lot. |
| `reveal()` / `quietly()` | 2446 | staggered scroll reveal, and the "don't replay the animation on a user-triggered repaint" fix |
| `countUp()` | 2464 | number animation with an easing curve |
| card tilt | 2490 | 3D tilt — note the listener is on the card, not the wrapper |
| `seal()` / `character()` | 2538 | deterministic canvas portraits |
| `spark()` | 2587 | tiny inline sparkline |
| `show()` | 2614 | router, and where per-page rAF loops get stopped and restarted |
| `tipShow()` | 2293 | the tooltip system, including the free-text and pointerout fixes |
| see-saw | 3385 | torsional spring physics — the thing you should study hardest |
| `archetype()` `score()` `badges()` | 3799 | how a wallet gets classified from behaviour |
| `scanning()` | 4022 | the wallet loader: converging particles, progress arc, skeleton beneath |
| `pager()` | 4423 | reusable pagination |
| `areaChart()` | 4476 | equity chart: value axis, crosshair, hover readout, range switching |
| `drawCard()` | 4696 | the share card export — measured block layout, not hand-tuned offsets |
| `buildPlays()` | 4992 | **the position reconstruction fix.** Anchors on current size, walks backwards |
| `Replay()` | 5045 | the trade replay engine — ~1,200 lines. Camera, markers, particles, verdict |
| `radarLoader()` | 6281 | the candle-building loader |
| `magnetise()` | 6609 | pointer-reactive card lift |
| `carryScan()` / `paintCarry()` | 6660 | the funding page and its arithmetic |

## Read these first, in this order

1. **`Replay()` at 5045.** It's a fifth of the app. It is what people actually
   shared. Look at how much is in there: camera easing, marker collision
   avoidance, particle bursts clipped to the chart, a hold beat before the
   verdict, a composed end card. That density is the bar.
2. **The see-saw at 3385.** ~90 lines for one interactive object. Spring
   constant, damping, pointer velocity gating, pan inertia. This is what "real
   physics" means as opposed to a CSS transition.
3. **`scanning()` at 4022.** A loading state with more code in it than most of
   your current pages.
4. **The CSS, lines 320–1570.** Count the states per component. Notice that
   almost nothing has only a default appearance.

## What to take, and what to leave

**Take the mechanisms:**
- how state is stopped and restarted across route changes
- how rAF loops are paused off-screen and cancelled on detach
- how canvas DPR is capped and how geometry is measured, not assumed
- how tooltips survive pointer movement between children
- how a repaint avoids replaying entrance animations
- how the share card composes a measured block
- how the position/PnL maths is anchored to venue truth
- the density: how many hover states, transitions and edge cases a finished
  component actually carries

**Leave every one of these — they're Arclight's identity:**
- the sage/green palette and its tokens
- Archivo / JetBrains Mono / Instrument Sans
- the class names (`.rv`, `.chero`, `.ccard`, `.cbest`, `.pod`, `.mover`…)
- the left rail + 8-tab mobile nav
- the eyebrow → headline → lede section rhythm
- the character designs and their names
- the candle-building loader and the converging-particle loader
- the share card composition

## Also read the comments

Where something was hard, I made you write down why. The comments on
`buildPlays()`, the funding walk, the see-saw and the reveal system explain bugs
that took real time to find. Those explanations are worth more than the code.

## Then

Rebuild the HyperFlow wallet page to that standard, in HyperFlow's own visual
language. Show me. Tell me which parts you know are still thin.
