# Changelog

All notable changes to the NFS No Limits Mission Calendar.

This project follows [Semantic Versioning](https://semver.org/). Because the calendar
is a single static page with no API, "breaking" is read as a change to the mission
rotation the calendar predicts, or to previously saved local state.

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
- `spec.md` documenting the rotation and the rendering rules; `history/` holding the
  community mission logs the rotation was derived from.
