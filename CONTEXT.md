# web-browser-old — AI Context File
_Last synced: 2026-10-14 @ 172cc22_

## 1. What This Is (Plain English)
- **In one sentence:** a ready-to-run copy of the old Firefox 52.9 browser for Windows that lives in one folder (USB stick, any drive) and doesn't install anything on the PC.
- **Why it exists:** some old stuff only works in an old browser. Think legacy (XUL) add-ons, old Java applets on admin panels, and sites built for 2018 browsers. Firefox 52 ESR is the last version that runs them. This repo keeps that browser in one known-good, hardened copy.
- **Who uses it:** just me (the repo is **public** on GitHub, so anything committed is world-readable).
- **Vibe:** a time capsule. Freeze the old browser, patch what can be patched with settings, never "upgrade" it.

## 2. How To Run It
- **Setup once:** clone or download the repo, copy the `FirefoxPortableLegacy52/` folder wherever you want it (USB stick is fine). Windows only. The CPU needs SSE2.
- **Run:** double-click `FirefoxPortableLegacy52\FirefoxPortable.exe`.
  - First run creates `Data\` (your profile) from `App\DefaultData\`. That folder is gitignored, so it stays on your machine.
- **Build / deploy:** nothing to build. The launcher `.exe` is precompiled; its source is in `Other/Source/` (NSIS). It can't be rebuilt in this repo (no NSIS toolchain set up).
- **Required env vars:** none.

## 3. Tech Stack
- **Browser:** Mozilla Firefox **52.9.0 ESR**, BuildID `20180621064021` (`App/Firefox/application.ini`). Ships both 32-bit (`App/Firefox`) and 64-bit (`App/Firefox64`) builds.
- **Launcher:** PortableApps.com Firefox Portable launcher **2.1.2.0**, written in NSIS (`Other/Source/FirefoxPortableU.nsi:29`). Package version `52.9.0.0` (`App/AppInfo/appinfo.ini`), packaged 2018-06-28.
- **Helper:** `App/Bin/sqlite3.exe`. The launcher uses it to rewrite add-on paths when the drive letter changes (`FirefoxPortableU.nsi:512`).
- **What kind of project:** a vendored binary app (~204 MB tracked, ~200 MB of it Windows binaries). No source code of our own beyond config.
- **External services:** none required. Mozilla's 2018 services (updates, telemetry, Pocket, tiles) are switched off by `mozilla.cfg`.

## 4. Code Map (The Important Files Only)
- `FirefoxPortableLegacy52/FirefoxPortable.exe` — the entry point. Starts Firefox with `-profile Data\profile`, then fixes drive-letter paths in the profile.
- `FirefoxPortableLegacy52/FirefoxPortable.ini` — **active** launcher config, because it sits next to the `.exe`. `AlwaysUse32Bit=true` means the 64-bit folder is never used. `Other/Source/FirefoxPortable.ini` is the untouched example copy.
- `App/Firefox/mozilla.cfg` + `App/Firefox/defaults/pref/autoconfig.js` — **our hardening.** The `autoconfig.js` file makes Firefox load `mozilla.cfg` at startup. The same pair is in `App/Firefox64/`, so keep the two copies identical.
- `App/DefaultData/profile/prefs.js` — starter prefs copied into a fresh profile (disk cache off, no default-browser nag).
- `App/AppInfo/appinfo.ini` — package name and version, plus PortableApps.com platform metadata.
- `App/AppInfo/pac_installer_log.ini` — looks like junk. **It isn't** (see §6).
- `Other/Source/FirefoxPortableU.nsi` — launcher source. Read it to learn what the `.exe` does; editing it changes nothing until someone recompiles.
- `Other/Source/Readme.txt` — what every `FirefoxPortable.ini` option means.
- `.gitattributes` / `.gitignore` — binaries marked binary; Windows text checked out as CRLF; `Data/` ignored.

## 5. Rules For Editing This Code
- **Never upgrade Firefox.** The whole point is 52.9.0 ESR. Don't drop in newer `xul.dll`/`omni.ja`. Don't unlock `app.update.*` in `mozilla.cfg`.
- **Never commit `FirefoxPortableLegacy52/Data/`.** It holds cookies, history, sessions and saved logins. It's gitignored; don't `git add -f` it.
- **Hardening goes in `mozilla.cfg`, in both `App/Firefox/` and `App/Firefox64/`.** Not in `defaults/pref/*.js`: `firefox.js` inside `browser/omni.ja` loads later and overrides those.
  - `lockPref` = forced. `defaultPref` = the user can still change it in `about:config`.
  - Line 1 of `mozilla.cfg` must stay a comment (Firefox skips it).
- **Only use prefs that exist in Firefox 52.** Check against the built-in defaults first: `unzip -p App/Firefox/omni.ja greprefs.js` and `unzip -p App/Firefox/browser/omni.ja defaults/preferences/firefox.js`.
- **Don't break legacy compatibility for "best practice".** TLS 1.0, 3DES and NPAPI stay as Firefox 52 shipped them unless I decide otherwise (see TRAJECTORY questions).
- **Binary files are opaque.** Don't hand-edit `.exe/.dll/.ja/.xpi`. Add any new binary extension to `.gitattributes` as `binary`.
- Syntax-check edited `.cfg`/`.js` with `node --check` (copy `.cfg` to a `.js` name first).

## 6. Fragile Bits & Landmines
- **`App/AppInfo/pac_installer_log.ini` must stay, byte for byte.** The launcher compares its `Info2` line to a fixed string (`FirefoxPortableU.nsi:154-157`). If the file or line is missing, every launch shows "Integrity Failure Warning". The file itself says to gitignore it; ignore that advice.
- **First-run seeding depends on default paths.** The launcher copies `App/DefaultData/*` into `Data/` only if `Data\profile\prefs.js` is missing **and** the INI paths are the defaults (`FirefoxPortableU.nsi:275-279, 379-390`). Change `ProfileDirectory` and you get an empty profile with no starter prefs.
- **The launcher appends to `prefs.js` on every start** (`browser.shell.checkDefaultBrowser`, `FirefoxPortableU.nsi:480-492`). Harmless duplicates; Firefox de-dupes on exit.
- **`AlwaysUse32Bit=true`** means `App/Firefox64/` (~107 MB) is never launched by the current INI. It looks removable, but the readme default is `false`, so keep it unless I decide otherwise.
- **Pulling commit `172cc22` deletes `Data/` from an older checkout.** Git removes files that a commit untracks. If you ran the browser from a checkout of `ad5c169`, back up `Data/` before pulling. Recover with `git checkout ad5c169 -- FirefoxPortableLegacy52/Data`.
- **The old profile is still in public git history** (commit `ad5c169`). Untracking doesn't erase it (see TRAJECTORY T011).
- **Line endings:** `.ini/.nsi/.nsh/.js/.cfg/.html/.txt` are LF in the repo and CRLF on checkout (`.gitattributes`). Don't "fix" warnings about this.
- **Expect broken HTTPS on some sites.** The CA list (`nssckbi.dll`) is from 2018, so sites on newer root CAs may show `SEC_ERROR_UNKNOWN_ISSUER`. This can't be fixed by prefs alone.

## 7. Current State
- **Last shipped:** `172cc22` stopped publishing the personal profile, locked updates off, and switched off dead Mozilla services (telemetry, Pocket, tiles). It also turned on punycode and WebRTC IP-leak protection, set `.gitattributes`, and fixed the misleading INI comment.
- **Working on now:** docs (CONTEXT / CHANGELOG / TRAJECTORY / README / RELEASE) and the first release plan.
- **Next up:** see `TRAJECTORY.md`.

## 8. Update Protocol (Verbatim)
> **For the AI Assistant:** When asked to "Update CONTEXT.md":
> 1. Re-run Phase 0 — check for new or changed `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` / `.github/` files, merge what's new, and re-run Phase 3 so each of them still points to `CONTEXT.md`.
> 2. Re-scan the tree, manifests, and `.github/workflows/` for drift.
> 3. Read our recent conversation for new decisions, fragile bits discovered, or shifted goals.
> 4. Refresh the `_Last synced_` line with today's date and current commit SHA.
> 5. Rewrite — do not append. One clean source of truth. Preserve still-true content, revise the rest.
> 6. Keep §1 and §2 in plain English. Keep the file under ~200 lines.
