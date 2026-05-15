# Changelog

All notable changes to Datesorted Pro will be listed here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.2.0] — 2026-05-15

The app updates itself now.

### Added
- **One-click auto-update.** When a new version is available, the update
  dialog has a **"Download & install update"** button. It downloads the new
  installer directly, verifies it against a SHA-256 hash published in the
  update manifest, then launches it and closes the app so the install can
  complete. No more digging through your Gumroad library.
- Live download progress (MB downloaded / total) inside the dialog.
- **Safety:** any failure — bad hash, network drop, launch error — aborts
  cleanly and falls back to the manual "Open Gumroad library" path. A file
  that fails hash verification is deleted and never run. The dialog can't be
  closed mid-download.
- `build.bat` now prints the installer's SHA-256 after building, so it can
  be pasted straight into `version.json` for the release.

### Notes
- The manual Gumroad-library path remains available as a permanent fallback
  (an auto-updater can't fix a broken auto-updater).
- This build is **not code-signed**, so the downloaded installer will still
  show a Windows SmartScreen warning ("More info → Run anyway"). A signing
  certificate will remove that in a future release.

---

## [1.1.3] — 2026-05-15

Installer migration fix, plus a proper in-app update section.

### Added
- **Settings → Updates section.** A "Check for updates" button that pings
  GitHub on demand (same logic as the startup check) and now also tells you
  when you're already on the latest version or can't reach the server — the
  startup check stays silent in those cases. Plus an "Open my Gumroad
  library" button.

### Changed
- **Update links now point existing customers to their Gumroad *library*,
  not the product page.** The product page is the buy screen — useless to
  someone who already owns the app. The library (logged in with the
  purchase email) is where their downloads actually live. Fixed in both the
  update dialog and the new Settings section. The License screen's "Buy a
  license" link still points to the product page, since unactivated users
  may genuinely need to purchase.

### Fixed
- **Customers who had the original 1.0.0 could end up with two copies of
  the app after updating.** Early 1.0.0 builds could install per-machine
  (Program Files); 1.1.x installs per-user. Windows treats those as
  separate installs, so the new version sat alongside the old one instead
  of replacing it — two Start Menu entries, two uninstall entries, and the
  old (buggy) 1.0.0 still launchable. The installer now detects a leftover
  per-machine install and offers to remove it automatically before
  installing, so the upgrade is clean. Your license, presets, and history
  are always preserved (they live in your user profile, not the program
  folder, and are never touched by install or uninstall).

- **Window resizing was very stuttery.** CustomTkinter regenerates every
  rounded-corner image and relays out the scrollable column on every resize
  event. The window is now fixed-size — it already auto-fits your screen at
  launch and the column scrolls, so nothing is lost and the jank is gone.

### Note for anyone who already has two copies
Uninstall **both** "Datesorted Pro" entries from Settings > Apps, then run
the 1.1.3 installer once. Nothing is lost — your license and settings come
right back.

---

## [1.1.2] — 2026-05-15

Hotfix for a display-size issue reported by a customer, plus a more
visible update notice.

### Changed
- **Update notifications are now a dialog you must dismiss**, not a toast
  that auto-disappears after a few seconds. Shows the new version, the
  release notes, a "Download from Gumroad" button, and "Remind me later".
  Stays on screen until you act on it so updates can't be missed.

### Fixed
- **App froze and became completely unclickable when switching to Light
  mode from Settings.** Root cause: running the global appearance redraw
  while the Settings panel was still alive (it held an input grab AND
  contained a scrollable frame, both of which deadlock the redraw). Fixed
  decisively — the theme picker is now a segmented button (no dropdown
  grab), and the Settings panel fully closes itself before the theme is
  applied so the redraw only touches the main window.
- **App window opened larger than the screen on smaller / lower-resolution
  displays**, pushing the Organize button off the bottom with no way to
  reach it. The window now measures the actual screen at startup and clamps
  itself to fit (max 96% width, 92% height), then centers. The preferred
  1240×1180 layout is still used on screens big enough for it. The left
  column was already scrollable, so on short windows every control —
  including Organize — remains reachable by scrolling.
- Minimum window size lowered so the app is usable on 1366×768 laptops.
- Toggle switch knobs are now theme-aware (dark in Light mode, white in
  Dark mode) so they're always visible instead of looking clipped.

---

## [1.1.1] — 2026-05-15

Quality-of-life release. Removes two friction points reported in early
testing: being forced to rename, and being forced to pick a destination.

### Added
- **"Keep original filenames" switch** on the Filename Format card — when
  on, files are sorted into date folders with their existing names
  untouched. The format builder collapses out of the way so the choice is
  unmistakable
- **"Organize inside the source folder" toggle** on the Destination card —
  sort a messy folder into `Year/Month` subfolders in place, no separate
  destination needed. Safe: the file list is snapshotted before any moves,
  so nothing gets re-scanned mid-run
- **Smart default destination** — adding your first source folder now
  pre-fills a sibling `Datesorted/` folder on the same drive (fast moves,
  not nested in the source). Override it or switch to in-place anytime

### Changed
- The "pick a destination" error now points you at the in-place toggle
  instead of just blocking you
- Both new settings persist to config like every other preference

### Fixed
- First attempt at fixing the Light-mode freeze (a deferred grab release).
  This did not fully resolve it — see 1.1.2 for the decisive fix.
- Top-bar theme icon now stays in sync when the theme is changed from
  the Settings panel

---

## [1.1.0] — 2026-05-14

The "polish + power-user" release. Big focus on visual feedback, in-app help,
and making the format builder feel direct-manipulation.

### Added
- **In-app Help panel** (`?` button in the top-right) — quick start, format guide,
  drag-and-drop tips, dedup explanation, undo guide, support info
- **Tooltips** on every icon button and stat card so it's obvious what each does
  without hunting through docs
- **Visible drag feedback in the Filename Format builder** — the dragged token
  now visually "lifts" with a gold border, and a vertical gold drop-marker
  shows exactly where it will land before you release
- **Job / Shoot name input** directly in the Filename Format card, with live
  preview that updates as you type. Used wherever the JOB token appears
- **Snapchat memories support** — point the app at your `memories_history.json`
  export and it will read every snap's original timestamp, so saved snaps end
  up in the correct chronological folder instead of all dated to "today"
- **Auto-update notifications** — app pings GitHub on launch and shows a toast
  when a new version is available
- **Crash log** written to `%USERPROFILE%\.datesorted\crash.log` for any
  unhandled exception, with a dialog telling the user where to find it

### Changed
- **Default filename format is now `Day-Month-Year-Counter`** (e.g.
  `15-09-2022-0001`). The old MM-DD-YY default felt US-centric and ambiguous;
  DD-MM-YYYY is unambiguous and works internationally
- Quick format presets in the builder now lead with the new DD-MM-YYYY option
- Default window opens at 1240×1180 — fits all options + the Organize button
  without scrolling on a typical desktop
- Sources card now collapses cleanly when no folders are added (no more dead
  space below the drop zone)
- ZIP-by-year originals now move to a `_trash/` folder after byte-verified
  zipping, instead of being deleted outright. Recoverable until you manually
  empty it
- Duplicate detection: switched MD5 → BLAKE2b for hashing (faster, no
  collision risk), and parallelized across up to 8 worker threads
- **Corruption check is now parallelized** (up to 8 worker threads). On NVMe
  disks, expect ~2-4× speedup vs the previous single-threaded version
- **EXIF resolution is now parallelized** — a new `READING EXIF` phase
  processes file metadata in parallel before the move phase. On large
  libraries (50k+ files) this saves 10-30% of total run time
- ETA during the hashing phase is now bytes-based instead of file-count-based,
  so it's accurate even when RAW files vary 10× in size
- Elapsed time now reflects the entire wall-clock run (scan + corruption +
  hashing + organize + zip), not just the move phase
- License file is now HMAC-signed and bound to your machine ID — protects
  against trivial tampering
- File operations now use Windows long-path support (`\\?\` prefix) so deeply
  nested folders no longer hit MAX_PATH (260 char) errors
- License activation switched from Gumroad's deprecated `product_permalink`
  parameter to the current `product_id` parameter

### Fixed
- License activation crashed with `urllib.request has no attribute urlencode`
  — `urlencode` was being called from the wrong submodule
- License activation showed "Could not connect to internet" for every error,
  including HTTP 500 from Gumroad. Real error messages now surface
- `safe_move` had a TOCTOU race where two processes could overwrite the same
  destination — now atomically reserves the name with O_EXCL
- Undo silently overwrote files at the original path if you'd put new files
  there since the last run — now prompts with a Yes / No / Cancel dialog
- HEIC files from iPhones lost their actual capture date because Pillow needs
  `pillow-heif` to read HEIC EXIF — now bundled by default
- Filename Format Builder's Save and Cancel buttons were below the visible
  area on shorter windows — now pinned to the bottom regardless of height

---

## [1.0.0] — 2026-05-12

Initial public release. Built and tested on Windows 10 and 11.

### Features
- EXIF-based date sorting with filename and modified-time fallbacks
- Drag-token filename builder
- Duplicate detection
- Corruption checking with quarantine
- RAW / Photo / Video category separation
- Multi-camera-body workflows (split RAW by EXIF serial)
- Full undo
- Dark / light mode

---

## Versioning

Datesorted Pro uses [Semantic Versioning](https://semver.org):
- **MAJOR** — breaking changes (e.g., config file format incompatible)
- **MINOR** — new features that don't break existing workflows
- **PATCH** — bug fixes only, no new features

`x.y.z` numbers in this changelog correspond to the version baked into the
installer at `installer_output\DatesortedPro_Setup_vX.Y.Z.exe` and to the
`version` field in [`version.json`](version.json) on this repo.
