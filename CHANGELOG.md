# Changelog

All notable changes to the NFS No Limits Mission Calendar.

This project follows [Semantic Versioning](https://semver.org/). Because the calendar
is a single static page with no API, "breaking" is read as a change to the mission
rotation the calendar predicts, or to previously saved local state.

## [1.2.0] — 2026-09-04

Reported by a player in Europe: *"The missions begin at 18:00 UTC the day before you
have them listed. People in Europe start the missions the evening before you have
listed in your calendar."* They were right about the day — the rollover time is 18:30
UTC, not 18:00, but everything west of UTC+5:30 was indeed getting each mission an
evening before the calendar drew it.

### Changed

- **Missions are now drawn on the day they start in your own time zone.** The rollover
  is 18:30 UTC worldwide, which clears midnight only at UTC+5:30 and east. For viewers
  in Europe, Africa and the Americas every mission bar therefore moves one day earlier
  than in v1.1.0 — onto the evening they actually get it. Asia and Oceania are
  unchanged. The rotation itself is untouched; only which cell a mission is drawn in.

### Fixed

- The whole grid was off by one day at UTC−6 and west (all of the US Pacific and
  Mountain zones, Hawaii). Mission days were derived from *local noon* minus the
  anchor, which silently assigns each mission to whichever local day it overlaps most —
  a rule that flips at UTC−6. Mission days now come from the rollover instant itself,
  so there is no hidden boundary and no timezone sees a different rotation.
- Today's date circle is once again the viewer's own date. It had been keyed to the
  mission rollover, which put the circle on the wrong cell for anyone whose local day
  and mission day disagreed.

### Added

- The mission running **right now** is filled in solid, and every other bar is washed
  out — matching how Calendar renders selected and unselected events in dark mode.
  A multi-day mission stays filled across all of its segments, including where it
  wraps to the next week row.
  - The washed-out variant is not a guess: it was measured off Calendar's own event
    bars, and comes out as hue unchanged, saturation ×0.82, brightness ×0.39.
  - Mission colors are now macOS **dark**-appearance NSColor values, read from AppKit.
    They were light-appearance values before, which is why several bars read too dark.
  - White text on a filled bar is swapped for black on yellow, green and orange, where
    white drops under 3:1. Calendar uses white on all of them; on yellow that is a
    1.4:1 contrast and genuinely unreadable, so this one detail departs from it.
- Hovering a mission bar shows its real start and end instant in your local time,
  e.g. `Sun 20:30 → Mon 20:30 (your time)`.

## [1.1.0] — 2026-08-15

### Added

- Sidebar category filters are remembered between visits. The active categories are
  stored in your browser under `localStorage` key `nfsnl-mission-calendar.filters`,
  so hiding a mission type now sticks instead of resetting on every reload.
  Nothing is sent anywhere — the value never leaves your device.
  - Defaults still come from the markup, so a first visit behaves exactly as before
    (Crew UGR / TT / SE hidden).
  - Unchecking every category is preserved as a real choice.
  - Falls back to the defaults when storage is unavailable (private browsing,
    `data:` URLs, storage disabled) or the stored value is unusable.

## [1.0.1] — 2026-08-15

### Fixed

- Phone display. The desktop layout pins the window to `100vh` and hides the overflow,
  which left the later weeks of the month unreachable on a phone. Below 768px the page
  itself now scrolls vertically, week rows keep their computed `min-height` instead of
  being squeezed to fit, and the toolbar sticks to the top so Prev / Today / Next stay
  reachable while scrolling. The sidebar moves above the grid and lays out horizontally.

> In this monorepo 1.0.0 and 1.0.1 are the same commit (`a072e01`) — the mobile fix
> landed alongside the release prep rather than as its own commit, so both tags point
> there. The two versions are only separable as GitHub releases.

## [1.0.0] — 2026-08-15

First public release, published to GitHub Pages.

### Added

- Perpetual month grid: any month, any year, with nothing hardcoded.
- Mission engine deriving daily and weekly missions from the 2-, 3-, 4- and 6-week
  rotations, anchored to the week of Monday 10 Aug 2026 (ISO week 33).
- Multi-day missions drawn as continuous bars, with slot allocation so overlapping
  missions stack instead of hiding each other, and seamless joins across week rows.
- Seven mission categories as Apple system colors, each toggleable in the sidebar.
- ISO week numbers down the left edge, matching Apple Calendar.
- Today's highlight keyed to the game's 18:30 UTC global reset rather than local
  midnight, so the current mission is correct in every timezone.
