# NFS No Limits — Mission Calendar

A single-file, zero-dependency perpetual calendar that predicts the daily and weekly
missions of **Need for Speed: No Limits**, laid out like the macOS Calendar app.

NFSNL's missions are not random: they rotate on fixed 2-, 3-, 4- and 6-week cycles that
line up with ISO week numbers. Give the engine an anchor week and it can render any
month — past or future — including multi-day missions that span across weeks.

> Unofficial fan project. Not affiliated with, endorsed by, or supported by EA or
> Firemonkeys. Mission data was reverse-engineered from community observation and may
> drift if the game changes its rotation.

## Features

- **Perpetual** — navigate to any month in any year; missions are computed, not stored.
- **Apple Calendar look and feel** — dark theme, macOS window chrome, ISO week numbers
  in the left gutter, Apple system colors for event categories.
- **True multi-day events** — 1/2/3/5/7-day missions render as continuous bars that
  span days and weeks, with seamless corners at the week boundary and the title
  repeated on Monday when a bar wraps.
- **Category filters** — toggle mission types from the sidebar. Crew missions locked to
  a specific mode (UGR / Tuner Trials / Special Event) are hidden by default.
- **Correct global reset** — the day boundary is anchored to the game's UTC reset
  moment, not to your local midnight, so the "active" mission is right regardless of
  the timezone your machine is in.
- **Automatic row height** — a week with many overlapping missions grows taller instead
  of clipping events.

## Quick start

No build step, no package manager, no server required:

```bash
open index.html
```

Or serve the directory if you prefer a real origin:

```bash
python3 -m http.server 8000
```

Then visit <http://localhost:8000/>.

Everything — markup, styles and the rotation engine — lives in that one HTML file, so
it also works fine from a USB stick, a phone browser, or GitHub Pages (enable Pages on
the repository and the calendar is served straight from `index.html`).

## How the engine works

Every mission is derived from a single **mission index**: the number of whole game-days
elapsed since the anchor moment.

```
anchor        = 2026-08-09 18:30 UTC   // Mon 2026-08-10 02:30 in UTC+8
missionIndex  = floor((now - anchor) / 86_400_000)
W             = floor(missionIndex / 7)   // week number; anchor week is W = 0
dayOfWeek     = mod(missionIndex + 1, 7)  // 1 = Mon … 0 = Sun
```

The anchor is the exact instant the game flips to the "Monday" mission set worldwide,
so a day in the calendar is a *game day*, not a local calendar day. Dates before the
anchor produce a negative `W`, which is why the code uses a sign-correct `mod()` helper
rather than JavaScript's `%`.

Each weekday then picks its missions from a rotation table indexed by `mod(W, n)`:

| Day | Missions |
| --- | --- |
| Mon | 1-day cash/fuel (`W % 3`) · UGR Driver 4 tiers (2d) · UGR 6 tiers (3d) |
| Tue | Car **brand**, 2-day (`W % 6`) · generic 3-day task (`W % 4`) |
| Wed | Crew UGR event, 5-day (`W % 2`) |
| Thu | Crew Win 600 SE races (5d, fixed) · SE sub-mission, 5-day (`W % 3`) |
| Fri | 1-day cash/fuel (`W % 3`) · generic 3-day task (`W % 4`, offset from Tue) |
| Sat | Car **type**, 2-day (`W % 6`) · Crew TT Consume 240 fuel (7d, fixed) |
| Sun | UGR division Win 12 races (`W % 3`) · Crew event, 5-day (`W % 2`) |

### Rotation tables

**`W % 3` — 1-day missions (Mon & Fri) and the Sunday UGR division**

| Remainder | Mon / Fri | Sunday UGR |
| --- | --- | --- |
| 0 | Earn 80K cash | Driver |
| 1 | Spend 100K cash | Speedster |
| 2 | Consume 25 fuel | Breakneck |

**`W % 4` — 3-day tasks** (Tuesday and Friday are offset by two steps)

| Remainder | Tue | Fri |
| --- | --- | --- |
| 0 | Nitro 360s | Drift 24K m |
| 1 | Near miss 30 | Airtime 30s |
| 2 | Drift 24K m | Nitro 360s |
| 3 | Airtime 30s | Near miss 30 |

**`W % 6` — 2-day car missions**

| Remainder | Tue (brand) | Sat (type) |
| --- | --- | --- |
| 0 | Tuner Trials — airtime 60s | Classic Sports — airtime 60s |
| 1 | Bugatti — near miss 40 | Hyper — near miss 40 |
| 2 | Porsche — airtime 60s | Sports — airtime 60s |
| 3 | Ferrari — near miss 40 | Street — near miss 40 |
| 4 | Lamborghini — airtime 60s | Super — airtime 60s |
| 5 | Koenigsegg — near miss 40 | Muscle — near miss 40 |

Note the easy-to-miss difference: the 2-day car missions ask for **near miss 40 /
airtime 60s**, while the 3-day generic tasks ask for **near miss 30 / airtime 30s**.

**`W % 2` — 5-day crew events**

| Remainder | Wed | Sun |
| --- | --- | --- |
| 0 | Crew UGR Nitro 60K s | Crew Win 750 races |
| 1 | Crew UGR Drift 600K m | Crew Open 300 crates |

**Fixed every week:** Thursday's Crew Win 600 SE races, Saturday's 7-day Crew TT
Consume 240 fuel, and Monday's two UGR tier missions.

The `[n SC]` suffix on each event is the mission's in-game reward.

## Categories and colors

| Category | Color | Contents |
| --- | --- | --- |
| `type-crew-modes` | Brown | Crew missions restricted to a mode (UGR / TT / SE) — **hidden by default** |
| `type-crew` | Yellow | General crew missions (win races, open crates) |
| `type-ugr` | Blue | Underground Racing missions |
| `type-se` | Green | Special Event missions |
| `type-1day` | Red | 1-day cash / fuel missions |
| `type-2day` | Purple | 2-day car brand / car type missions |
| `type-3day` | Orange | 3-day nitro / drift / airtime / near miss missions |

## Re-anchoring

If the game ever shifts its rotation, you don't need to recompute history — just
re-anchor:

1. Open the game on a Monday and note that day's 1-day mission, car brand, and crew
   event.
2. Find the remainder each of those corresponds to in the tables above.
3. Adjust `anchorTimestamp` in `index.html` so that the Monday you observed lands on
   the matching `W` (e.g. a week that should read remainder 0 for all cycles is any `W`
   divisible by 12).

Because the cycle lengths are 2, 3, 4 and 6, the whole system repeats every **12
weeks** — an anchor shift of 12 weeks changes nothing.

## Repository layout

```
index.html   The whole app — HTML + CSS + vanilla JS, no dependencies
README.md    This file, which doubles as the mission specification
LICENSE      MIT
```

That's the entire project. The rotation tables above were derived from months of
day-by-day mission logs collected by the community; if you change the logic in
`index.html`, verify it against a real in-game week before trusting it, and keep the
tables in this README in sync with the code.

## Browser support

Any modern browser. The layout targets desktop widths; on narrow screens the grid
scrolls horizontally.

## License

[MIT](LICENSE). Mission names and other game content referenced here are the property
of Electronic Arts.
