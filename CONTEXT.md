# web-browser-old — AI Context File
_Last synced: 2026-10-14 @ f2e0001_

## 1. What This Is (Plain English)
- **In one sentence:** a fresh, never-used copy of portable Firefox 52.9 for Windows that lives in one folder (USB stick, any drive), with extra safety settings for browsing the internet.
- **Why it exists:** I want exactly the Firefox version from the v0 upload (52.9.0 ESR), installed clean with none of my personal data, and as safe as an old browser can be made for internet use.
- **Who uses it:** just me (the repo is **public** on GitHub, so anything committed is world-readable).
- **Vibe:** a time capsule with a lock on it. Freeze the old browser, tighten every setting we can, never "upgrade" it.

## 2. How To Run It
- **Setup once:** clone or download the repo, copy the `FirefoxPortableLegacy52/` folder wherever you want it (USB stick is fine). Windows only. The CPU needs SSE2.
- **Run:** double-click `FirefoxPortableLegacy52\FirefoxPortable.exe`.
  - First run creates `Data\` (your profile) from `App\DefaultData\`. That folder is gitignored, so it stays on your machine.
  - On 64-bit Windows 7+ it runs the 64-bit build; otherwise the 32-bit one (launcher default).
- **Build / deploy:** nothing to build. The launcher `.exe` is precompiled; its source is in `Other/Source/` (NSIS). It can't be rebuilt in this repo (no NSIS toolchain set up).
- **Required env vars:** none.

## 3. Tech Stack
- **Browser:** Mozilla Firefox **52.9.0 ESR**, BuildID `20180621064021` (`App/Firefox/application.ini`). Ships both 32-bit (`App/Firefox`) and 64-bit (`App/Firefox64`) builds.
- **Package:** PortableApps.com `FirefoxPortableLegacy52_52.9.0_English.paf.exe`. Package version `52.9.0.0` (`App/AppInfo/appinfo.ini`), packaged 2018-06-28. Launcher **2.1.2.0**, NSIS (`Other/Source/FirefoxPortableU.nsi:29`).
- **Helper:** `App/Bin/sqlite3.exe`. The launcher uses it to rewrite add-on paths when the drive letter changes (`FirefoxPortableU.nsi:512`).
- **What kind of project:** a vendored binary app (~204 MB tracked, almost all Windows binaries). Our own code is two config files.
- **External services:** none required. Mozilla's 2018 services (updates, telemetry, Pocket, tiles) are switched off by `mozilla.cfg`.

## 4. Code Map (The Important Files Only)
- `FirefoxPortableLegacy52/FirefoxPortable.exe` — the entry point. Starts Firefox with `-profile Data\profile`, then fixes drive-letter paths in the profile.
- `App/Firefox/mozilla.cfg` + `App/Firefox/defaults/pref/autoconfig.js` — **our hardening** (36 prefs). The `autoconfig.js` file makes Firefox load `mozilla.cfg` at startup. The same pair is in `App/Firefox64/`; keep both copies identical.
- `App/DefaultData/profile/prefs.js` — starter prefs copied into a fresh profile (disk cache off, no default-browser nag).
- `App/AppInfo/appinfo.ini` + `pac_installer_log.ini` — package metadata in its *installed* state. **Not junk** (see §6).
- `Other/Source/FirefoxPortable.ini` — stock example of launcher options. It only takes effect if copied next to `FirefoxPortable.exe` (none is there now = fresh install defaults).
- `Other/Source/FirefoxPortableU.nsi` — launcher source. Read it to learn what the `.exe` does; editing it changes nothing until someone recompiles.
- `.gitattributes` (`* -text`) / `.gitignore` (`Data/`).

## 5. Rules For Editing This Code
- **Never upgrade Firefox.** The whole point is 52.9.0 ESR as uploaded in v0. Don't drop in newer `xul.dll`/`omni.ja`. Don't unlock `app.update.*` in `mozilla.cfg`.
- **Keep it a fresh install.** Everything except `mozilla.cfg`/`autoconfig.js` must stay byte-identical to the official installer. The two `App/AppInfo` files are the installer-written exception. To check, extract the `.paf.exe` with `7z x` and `cmp` every file.
- **Never commit `FirefoxPortableLegacy52/Data/`.** It holds cookies, history, sessions and saved logins. It's gitignored; don't `git add -f` it.
- **Hardening goes in `mozilla.cfg`, in both `App/Firefox/` and `App/Firefox64/`.** Not in `defaults/pref/*.js`: `firefox.js` inside `browser/omni.ja` loads later and overrides those.
  - `lockPref` = forced (only updates). `defaultPref` = the user can still change it in `about:config`.
  - Line 1 of `mozilla.cfg` must stay a comment (Firefox skips it).
- **Only use prefs that exist in Firefox 52.** Check first: `unzip -p App/Firefox/omni.ja greprefs.js` and `unzip -p App/Firefox/browser/omni.ja defaults/preferences/firefox.js`. (`toolkit.telemetry.enabled` is read by code, not listed there.)
- **Internet safety beats legacy compatibility.** TLS 1.0/1.1, 3DES, DHE, plugins, Flash, WebGL, wasm and DRM are off on purpose. Re-enable one only in `about:config` for one site, not in the repo.
- **Binary files are opaque.** Don't hand-edit `.exe/.dll/.ja/.xpi/.chk`.
- Syntax-check edited `.cfg`/`.js` with `node --check` (copy `.cfg` to a `.js` name first).

## 6. Fragile Bits & Landmines
- **`App/AppInfo/pac_installer_log.ini` must stay, byte for byte.** The launcher compares its `Info2` line to a fixed string (`FirefoxPortableU.nsi:154-157`). If the file or line is missing, every launch shows "Integrity Failure Warning". The file itself says to gitignore it; ignore that advice. Same family: `appinfo.ini`'s `[PortableApps.comInstaller]` block.
- **First-run seeding depends on default paths.** The launcher copies `App/DefaultData/*` into `Data/` only if `Data\profile\prefs.js` is missing **and** paths are the defaults (`FirefoxPortableU.nsi:275-279, 379-390`). A custom INI with another `ProfileDirectory` gives an empty profile with no starter prefs.
- **The launcher appends to `prefs.js` on every start** (`browser.shell.checkDefaultBrowser`, `FirefoxPortableU.nsi:480-492`). Harmless duplicates; Firefox de-dupes on exit.
- **`.chk` files look like CHKDSK junk, but they are not.** They are NSS crypto signatures and were lost once in the v0 import; restored in `f2e0001`.
- **`.gitattributes` is `* -text` on purpose.** Files are stored with the installer's own mix of CRLF and LF. Don't add `text=auto`/`eol=` rules; they'd make checkouts differ from a fresh install.
- **Pulling `172cc22` or later deletes `Data/` from an older checkout.** Git removes files that a commit untracks. Back up `Data/` first if you ran the browser from a git checkout of `ad5c169`.
- **The old profile is still in public git history** (commit `ad5c169`). A history rewrite was attempted but blocked by the AI session's safety rules; it needs the owner (see TRAJECTORY T011).
- **Some HTTPS sites may still fail with `SEC_ERROR_UNKNOWN_ISSUER`.** The built-in CA list (`nssckbi.dll`) is from 2018. `security.enterprise_roots.enabled` may help via the Windows store; not tested.
- **Many modern sites will look or work broken.** It's a 2018 engine, and WebGL/wasm are off. That's the price of the hardening, not a bug.

## 7. Current State
- **Last shipped:** `f2e0001` made the package a byte-exact fresh install of the official installer (restored 6 `.chk` files, installer line endings, no custom INI). It also hardened `mozilla.cfg` for internet use. Earlier, `172cc22` untracked the personal profile and locked updates off.
- **Working on now:** removing the personal profile from git history (needs owner action), then the first release.
- **Next up:** see `TRAJECTORY.md`.

## 8. Update Protocol (Verbatim)
> **For the AI Assistant:** When asked to "Update CONTEXT.md":
> 1. Re-run Phase 0 — check for new or changed `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` / `.github/` files, merge what's new, and re-run Phase 3 so each of them still points to `CONTEXT.md`.
> 2. Re-scan the tree, manifests, and `.github/workflows/` for drift.
> 3. Read our recent conversation for new decisions, fragile bits discovered, or shifted goals.
> 4. Refresh the `_Last synced_` line with today's date and current commit SHA.
> 5. Rewrite — do not append. One clean source of truth. Preserve still-true content, revise the rest.
> 6. Keep §1 and §2 in plain English. Keep the file under ~200 lines.
