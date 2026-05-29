# Changelog

All notable changes to Datesorted Pro will be listed here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

## [1.7.2]
A folder-structure and control release. You decide how deep the date tree
goes, your existing event folders survive the sort, audio and GIFs get swept
into one tidy place, and the filename editor is back. Also ships the dry-run
safety fix.

### Added
- **Preserve event folders.** A new option keeps a named source subfolder
  (e.g. `2019/07/NH family trip/`) together as one unit under its earliest
  date, instead of scattering its files across day folders. Camera-generated
  folders (DCIM, 100CANON, plain numbers) are ignored, so only your real
  groupings are preserved. Off by default. Strongest for libraries that are
  already partly organized.
- **Folder-depth control.** Choose how deep the date tree goes: Year only
  (`2024/`), Year + Month (`2024/03 - March/`, the default), or down to the
  day (`2024/03 - March/15/`). A segmented control at the top of OPTIONS.
- **Filename editor is back.** Build the rename pattern from tokens directly
  in the FILENAME FORMAT box: add, reorder (arrows), and remove tokens, drop
  in custom text, set a Shoot name, and pick the separator. Live preview
  updates as you go.
- **See your duplicate groups.** Click the DUPES counter to open a panel that
  shows each group of byte-identical files: which one is kept (the oldest)
  and which move to `_duplicates/`. Works in dry run too, so you can verify
  before committing.

### Changed
- **Audio and GIFs are auto-collected.** They no longer need a toggle and are
  no longer date-sorted. Audio sweeps into `Audio - Sorted/` and GIFs into
  `Gifs - Sorted/`, with original names kept and never renamed. Name
  collisions get a Windows-style ` (1)`, ` (2)` suffix.
- **Job-done notifier is now a bell button** next to Organize (filled = on),
  with an instant tooltip. It replaces the OPTIONS row.
- **The type-folders label is now "Sort into Photos / Videos / RAW."** Audio
  and GIFs have their own flat folders, so the toggle no longer governs them.
- **The two rename-mode toggles are grouped** in the FILENAME FORMAT box:
  "Rename files only" above "Keep original filenames." They are now mutually
  exclusive, and "Rename files only" greys out the options it overrides
  (type folders, zip-by-year, preserve-events, folder depth).

### Fixed
- **Dry run is now truly dry.** The Dry run toggle was being ignored, so a
  "preview" run still moved files. It now previews only and never moves or
  changes a file. (Data-safety fix.)
- **Zip-by-year now works with type folders on.** Previously the zip scan
  only looked at the top level and found no year folders when "Sort into type
  folders" nested them under `Photos/`, `Videos/`, etc. It now zips
  `Photos/2024/`, `Videos/2024/`, and so on.

---

## [1.7.1]
Hardening pass over the v1.7.0 release. Fixes 15 bugs surfaced by a
max-effort code review: 3 packaging/import blockers, 6 concurrency and
state-leak issues, and 6 silent-failure paths. No new features.

### Fixed
- **Windows installer no longer bundles dead Tk dependencies.** The Bucket B
  cleanup deleted the legacy CustomTkinter UI but the PyInstaller spec was
  still pulling in `customtkinter` and `tkinterdnd2`. Installer is now
  the size it should have been in 1.7.0.
- **`tkinter` hard-import removed from `datesorted_pro.py`.** The deleted
  legacy UI left orphaned imports that would crash on any environment
  without Tk installed.
- **Drag-drop subclass crash on window re-create.** Calling `enable_drop`
  twice was clobbering the Win32 WNDPROC thunk; the freed callback caused
  a use-after-free on the next window message. Now guarded.
- **Undo no longer double-runs.** Rapid double-clicks on Undo could destroy
  the previous run's history record. Re-entry guard added.
- **Stats panel no longer hangs on source changes.** Switching sources
  mid-walk could leave the panel stuck at "computing…" forever.
- **Stats panel no longer caches stale data after re-adding a source.**
- **Resume after interruption now handles crashed runs**, not just cancelled
  ones. Crashed runs save resume metadata too.
- **Resume preserves day-counter sequence.** Renumbering after a partial
  cancel no longer produces non-sequential filenames.
- **Dialog state no longer leaks between Resume / Trial / What's-new and
  the update prompt.** Centralized reset; all dialogs restore cleanly.
- **Hash-verify is now strict in archive mode.** A pre-hash failure aborts
  the file as an error instead of silently moving it without verification.
- **Trial-mode gate counts only files that will actually move.** Dupes and
  corrupted files no longer count against the 500-file cap.
- **Moved-counter is now post-verify.** A byte-mismatched move no longer
  inflates the success count.
- **`version.json` fetch is size-capped.** Malicious or compromised CDN
  response can't blow memory before JSON parsing errors.
- **Auto-update exit Timer reference held.** Less GC fragility in the
  600 ms window between installer launch and `os._exit`.
- **JS Promise chains have `.catch()` everywhere.** Backend exceptions now
  surface as toasts instead of vanishing silently.

---

## [1.7.0]
Trial mode, real folder drag-drop, RAW+JPG pairing, what's-new dialog, and a
big internal cleanup (legacy CustomTkinter UI removed). Now $20 one-time.

### Added
- **Trial mode.** Unlicensed users can run real jobs up to 500 files per run.
  Lets people prove the app works on their library before paying $20. The
  license screen now has a "Try it first" button alongside Activate.
- **RAW + JPG (and HEIC) pairing.** When you shoot RAW + JPG, both files
  share the same renamed stem and land in the same folder. The RAW is the
  primary; siblings follow it. Same rule already used for `.xmp` sidecars.
  Conservative: lone same-name JPGs without a RAW sibling are still treated
  as independent, and video + photo with the same stem don't pair (common
  on iPhone where they're separate captures).
- **Real folder drag-and-drop.** Dragging folders from Explorer onto the
  app window now adds them as sources with real Windows paths, not the
  HTML5 `C:\fakepath\` placeholder. Implemented via a Win32 WM_DROPFILES
  subclass (`windrop.py`); the HTML5 dropzone is now a fallback.
- **"What's new" on first launch after an update.** Once you upgrade,
  the first run pops a panel listing what changed. Dismiss once and it
  stays away until the next upgrade.

### Changed
- **Legacy CustomTkinter UI removed.** The original CTk app was retired in
  v1.5.0 in favour of pywebview; this release deletes the ~2,100 dead lines
  and drops the `customtkinter` and `tkinterdnd2` dependencies. Installer
  is meaningfully smaller and the codebase is one UI to maintain instead
  of two.
- **`datesorted_pro.py` is now backend-only.** Running it directly prints a
  pointer to `datesorted_web.py`. No behaviour change for end users.

### Internal
- `collect_files` now returns `(primaries, xmps, companions)` instead of
  `(media, xmps)`. Both callers updated.
- `run_job` ends with `"trial_limit"` status when the unlicensed gate
  fires; UI shows an upsell dialog with a Buy link.

---

## [1.6.0]
New "Rename files only" mode + auto-update reliability fix.

### Added
- **"Rename files only" mode** (Options → processing flags). Renames files
  in their current folder using your filename format — no date folders, no
  destination required. Turns Datesorted Pro into a straight batch
  renamer. The live preview groups by each file's actual folder so you see
  exactly what every name becomes before you run.

### Fixed
- **Auto-update could fail with "DeleteFile failed; code 5 — Access is
  denied"** while installing. The freshly-downloaded installer tried to
  replace the app's .exe before the old instance had fully released it.
  The installer now uses the Windows Restart Manager (`CloseApplications` +
  `AppMutex`) to detect the running app, close it cleanly, replace the
  files, then relaunch — so the in-app "Download & install" completes
  without any manual step. **Every future auto-update is one click, no
  reboot.**

### If you hit code 5 on 1.5.0
One-time only: reboot, then run the installer manually (or re-download from
your Gumroad library) with the app closed. From 1.6.0 onward, auto-update
handles the locked-file case itself.

---

## [1.5.0]
Audio & GIF support.

### Added
- **Audio files are now organized** (.mp3, .wav, .flac, .m4a, .aac, .ogg,
  .opus, .wma, .aiff, …). With "Sort into type folders" on, they land in an
  **Audio/** tree by Year/Month. Dated by filename, then file time (audio has
  no EXIF).
- **GIFs get their own type.** .gif files now sort into a **GIFs/** folder
  instead of being lumped in with photos.
- **Two new processing toggles** in Options:
  - **Include audio files** — sweep audio into Audio/ (on by default)
  - **Include GIFs** — sweep .gif into GIFs/ (on by default)
  Turn either off to leave those file types untouched.
- The type-sort feature label is now "Sort into Photos / Videos / RAW /
  Audio / GIFs" and creates all five top-level folders as needed.

### Changed
- **Redesigned update dialog** (Capture One-style): "A new version is
  available!" header, a structured **What's new** list (feature headings
  with sub-bullets, driven by a new `whats_new` array in the update
  manifest), and a **Skip this version** button. The automatic startup
  check now respects a skipped version; the manual Settings → Check for
  updates always shows.

### Notes
- Existing duplicate detection, undo, dry-run and auto-update all work with
  audio and GIFs unchanged — they're just additional media types in the
  same pipeline.

---

## [1.2.0]
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

## [1.1.3]
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

## [1.1.2]
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

## [1.1.1]
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

## [1.1.0]
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

## [1.0.0]
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
