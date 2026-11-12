# Time Tracker — Development Log

> A summarized record of the conversations in which Time Tracker was built,
> version by version. User requests are quoted where they were given verbatim;
> earlier sessions are summarized. The app itself is the native Android
> project (`time-tracker-android`, local git repo); this repository holds the
> download page, release notes and APKs.

---

## The project

**Time Tracker** is a native Android multi-timer task app (Kotlin, no web
wrapper): create tasks, start/stop timers, running totals survive app kills
and device reboots. One APK supports all screen sizes (Android 8.0+, minSdk 26).
Distribution is this GitHub Pages site + versioned GitHub Releases.

## How it's released

- Tag `vX.Y.Z` → GitHub Actions creates the Release (notes from
  `releases/X.Y.Z.md`) and attaches `apk/time-tracker-X.Y.Z.apk`.
- Pushing APKs to `main` → workflow attaches them to the matching release.
- `main` also deploys the GitHub Pages download site.

## Feature timeline

### v1.0.0 — first public release
- Task list with per-task timers (Start/Stop), task cards, add/delete.
- Persistence as JSON in app-internal storage — totals survive app kill and
  device restart.

### v1.1.0
- App header (⏱️ title left, ⋮ overflow menu right).
- Settings screen (✕ closes it): sort order, debug toggle, swipe actions.
- Filter chips (Open / Done / Archived / All).
- Red Stop button while a timer runs.

### v1.2.0
- Export: Google Calendar / Outlook (.ics) / CSV.

### v1.3 (originally 1.2.3, renamed by request)
- Request: fix task cards overflowing past the screen edges.
- Attempt #1: edit actions (Delete / Export / Done) moved to their own row,
  safe side margins, responsive dimens for small phones and tablets.

### v1.4 (originally 1.2.4, renamed by request)
- Request: *"on editing mode make it so instead of blocking the whole screen
  the module box expands and shows details under it."*
- Edit mode no longer swaps the row — the card expands with a details panel
  underneath: rename field, totals / last-used / status, actions.
- Inline two-tap delete (auto-cancels after 3s) instead of a blocking dialog.
- Renaming no longer interrupted by the 1-second ticker refresh.
- Request: *"change 1.2.3 to 1.3 and 1.2.4 to 1.4"* +
  *"make sure u change the filename and the title in github"* — both APKs were
  rebuilt with the new versionName/versionCode and renamed everywhere
  (filenames, release titles, page).

### v1.4.1
- Request: *"fix the side border issue which actually didn't fix with 1.4."*
- Attempt #2: details-row buttons share the row as equal thirds, smaller
  top-row buttons, two-line task names, wider card margins. Did **not** fix
  the real issue (see root cause below).

### v1.4.2
- Request: *"in edit mode make task time editable"* (and the overflow still
  wasn't fixed; *"maybe add a border"*).
- Editable task time: −15m / −1m / +1m / +15m steppers plus a custom
  hours:minutes setter. A running timer is banked then resumed; the total is
  never below zero.

### v1.4.3 — the overflow bug, root-caused at last
- Real cause found: both screens' root layouts used
  `android:fitsSystemWindows="true"`, which **discards the layout's own
  padding** and replaces it with system-bar insets — and with gesture
  navigation those insets are **0 on the left/right sides**. Content had been
  flush against the screen edges since v1.0.0; that's why card-level fixes
  changed nothing. On Android 15/16, targetSdk 35+ also enforces
  edge-to-edge, so bottom rows could slide under the navigation bar.
- Fix: proper inset handling in code (status bar, navigation bar, keyboard,
  camera cutout padded away on every Android version), `adjustResize` for the
  keyboard, and — per the user's suggestion — a **visible rounded border
  framing the whole screen** on main and Settings.

## Notable decisions along the way

- Native Kotlin app, not a web wrapper; an early web prototype was kept but
  diverged.
- Release automation with three GitHub Actions workflows (release, APK
  upload, Pages deploy) instead of manual uploads.
- Inline interactions everywhere: expanding edit panel, two-tap delete — no
  modal dialogs.
- Editable time keeps running timers running (bank + resume around edits).

## Open items

- The old `v1.2.3` / `v1.2.4` releases still exist on GitHub (a collaborator
  re-ran the workflows and recreated them after the cleanup). Awaiting a
  quiet window to delete them and the corresponding tags.
- `v1.3` / `v1.4` tags were never pushed, so those two GitHub Releases don't
  exist yet — the APKs are on the download page.
- The Android source repo is still local-only (not pushed to GitHub).

---
*Log maintained as part of the development conversation, November 2026.*
