# Datesorted Pro

The photo & video organizer for photographers and people who love organized folders.

**$20, one-time. No subscription. [→ Buy on Gumroad](https://nonflavored.gumroad.com/l/exufpl)**

Free trial: organize up to 500 files in any folder before you pay.

![Version](https://img.shields.io/github/v/release/Nonflavored/datesorted-pro?label=latest&color=f0b429)

---

## About this repo

This repo hosts the **update manifest** ([`version.json`](version.json)) that the installed app pings on launch to check for new releases, plus the public changelog. The application source is not open source — the app is sold on Gumroad.

If you're looking for one of these, here's where to go:

- **Use the app:** [buy it on Gumroad](https://nonflavored.gumroad.com/l/exufpl)
- **Check the latest version:** see [`version.json`](version.json) or the [Releases page](../../releases)
- **See what's new:** [CHANGELOG.md](CHANGELOG.md)
- **Report a bug / request a feature:** [open an issue](../../issues), or email **Support@datesorted.net**

---

## What Datesorted Pro does

A Windows desktop tool that reads EXIF dates from your photos and videos and sorts them into clean `Year / Month` folders with renamed files. Built for photographers who want to consolidate years of mixed-up backups into a single organized library.

### Features

- EXIF-based date sorting (with filename and modified-time fallbacks)
- Drag-token filename builder (Capture One style)
- Duplicate detection (BLAKE2b hashing, multi-threaded)
- Corruption checking with quarantine
- RAW / Photo / Video separation
- Multi-camera-body workflows (split RAW by EXIF serial number)
- HEIC / iPhone support
- Snapchat memories integration
- Full undo for every run
- Drag-and-drop, dark / light theme, modern UI

Runs entirely on your machine. No cloud, no telemetry, no account.

### Requirements

Windows 10 or 11. ~50 MB installed. No admin rights required.

---

## Support

I'm a photographer in New England building this in my spare time. I'm at my computer all day for work, so support response is usually fast.

**Email:** Support@datesorted.net
**Issues:** [GitHub Issues](../../issues)

If the app crashed, attach `%USERPROFILE%\.datesorted\crash.log` — it makes diagnosis much faster.

---

— Nonflavored
