# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/). This project does **not** follow Semantic Versioning yet: the only version number in the repo is the upstream package version `52.9.0.0` (`App/AppInfo/appinfo.ini`), which is frozen on purpose. Repo-level changes are logged in dated sections until the first release tag exists (see `RELEASE.md`).

**How to read this:**
- Newest first. Dates are commit dates. The whole history is here; search it (`Ctrl+F`) for an error message, a file name or a feature.
- `(a3f291)` is a short SHA — `git show a3f291` to see the diff.
- Entries explain symptom + cause, not just "fixed."
- Last known-good commit: search for `✅`. The first hit from the top is the newest version or change that was checked and worked.

---

## [Unreleased]
_Changes since the package import (`ad5c169`). Not launch-tested on Windows yet: no Windows machine in the AI session._

### Added
- Hardening config `mozilla.cfg` + `defaults/pref/autoconfig.js` in `App/Firefox` and `App/Firefox64`: Firefox now reads one settings file at startup. `(172cc22)` ✅ verified (172cc22): `node --check` passes; every pref name checked against FF52's `greprefs.js`/`firefox.js`.
- `.gitignore` that keeps `FirefoxPortableLegacy52/Data/` (the live profile) out of git. `(172cc22)`
- Docs for humans and AI: `CONTEXT.md`, `CHANGELOG.md`, `TRAJECTORY.md`, rewritten `README.md`. `(64c8c1b)`

### Changed
- **Breaking:** `FirefoxPortableLegacy52/Data/` is no longer tracked. Pulling `172cc22` over an older checkout deletes it from disk; back it up first, or restore with `git checkout ad5c169 -- FirefoxPortableLegacy52/Data`. `(172cc22)`
- **Breaking:** app updates are locked off (`lockPref("app.update.enabled", false)` and 3 siblings). "Check for updates" no longer works by design; the browser stays on 52.9.0 ESR. `(172cc22)`
- Telemetry, health report, experiments, self-repair, Pocket and new-tab tiles default to off. Their 2018-era Mozilla endpoints are dead or unsupported; still changeable in `about:config`. `(172cc22)`
- Architectural: `.gitattributes` now marks Windows binaries `binary` and checks out `.ini/.nsi/.nsh/.js/.cfg/.html/.txt` with CRLF. The old `* text=auto` alone relied on guessing. `(172cc22)`

### Fixed
- `FirefoxPortable.ini` said *"This INI file is an example only and is not used"* while sitting next to `FirefoxPortable.exe`. In that spot it is the active config; the comment now says so. `(172cc22)`

### Security
- A used Firefox profile (`cookies.sqlite`, `places.sqlite`, `sessionstore-backups/`, `key3.db`, `webappsstore.sqlite`) was published in a **public** repo. It's now untracked; still in history at `ad5c169`. `(172cc22)`
- IDNs now display as punycode (`network.IDN_show_punycode`): closes look-alike-domain phishing that FF52 never got later fixes for. `(172cc22)`
- WebRTC exposes only the default route address (`media.peerconnection.ice.default_address_only`), so local/VPN IPs no longer leak to pages. `(172cc22)`

### Broken
- The personal profile is still downloadable from git history (`git show ad5c169:FirefoxPortableLegacy52/Data/...`). The fix needs a history rewrite plus logging out the affected sessions; waiting on owner OK. `(ad5c169)`

---

## 2026-10-14 — v0: Firefox Portable Legacy 52.9.0 imported
- Imported PortableApps.com **Firefox Portable Legacy 52** (Firefox 52.9.0 ESR, launcher 2.1.2.0, package `52.9.0.0` dated 2018-06-28), 32- and 64-bit builds. `(ad5c169)`
- Active `FirefoxPortable.ini` placed next to the launcher with `AlwaysUse32Bit=true`, so the 32-bit build always runs. `(ad5c169)`

### Security
- The import also committed the live `Data/` profile (cookies, history, session) to a public repo. Untracked later; see [Unreleased] → Security. `(ad5c169)`

### Broken
- HTTPS sites chained to root CAs newer than mid-2018 fail with `SEC_ERROR_UNKNOWN_ISSUER`: the bundled `nssckbi.dll` CA list is frozen at 52.9.0. No fix yet. `(ad5c169)`

---

## 2026-10-14 — the first commit
- Project started: empty repo with a one-line `README.md` and `.gitattributes` (`* text=auto`). `(9e850b1)`

---

_No tagged versions yet, so no link references._
<!-- When the first tag exists, add e.g.:
[Unreleased]: https://github.com/achma-learning/web-browser-old/compare/v52.9.0-h1...HEAD
[v52.9.0-h1]: https://github.com/achma-learning/web-browser-old/releases/tag/v52.9.0-h1
-->

---

## Update Protocol (Verbatim)
> **For the AI Assistant:** When asked to "Update CHANGELOG.md":
> 1. Quick mode (default): find the most recent SHA cited in the existing file. Full rebuild (only if I ask, or there's no file): start from the first commit.
> 2. Run `git log` and `git diff --stat` for everything since that SHA.
> 3. Apply the WIP Decoder — derive intent from diffs when commit messages are vague.
> 4. Group consecutive same-intent commits into single entries; split when intent changes.
> 5. Skip noise (lockfiles, formatter passes, typos, no-op commits).
> 6. Detect version bumps in manifests — if found, move the `[Unreleased]` entries into a `## [vX.Y.Z] — YYYY-MM-DD` section and add its link reference at the bottom.
> 7. Append new entries above existing ones. **Never rewrite past entries** except to fix factual errors or repair the Keep a Changelog shape (and note the correction inline).
> 8. Keep every past section, however old. Never trim, archive or delete history.
> 9. Keep every entry under 2 lines. The file itself has no length limit.
> 10. Add ✅ markers only for tagged versions and for changes I confirmed or a check passed. For "rescue", follow Rescue Mode.
> 11. Follow [Keep a Changelog](https://keepachangelog.com/en/): bucket order, ISO dates, link references, `[YANKED]`, deprecations and breaking changes always listed.
