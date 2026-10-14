# TRAJECTORY
_Last synced: 2026-10-14 @ 172cc22 · built from: my requests (chat) + GitHub issues (none exist yet) + CHANGELOG + CONTEXT §6/§7. No TODO comments in the code._

## 🎯 Final Goal
Keep this repo **"a old version, but hardened and stable"** (chat): Firefox 52.9.0 ESR, Portable Edition, for Windows. It should run from any folder or USB stick for the legacy sites, add-ons and plugins newer browsers dropped. It must never update itself, never leak my personal data through the repo, and switch off every dead 2018 Mozilla service that could hang or phone home.

**Done when:**
- [ ] A fresh clone launches on Windows with a clean profile and no "Integrity Failure Warning".
- [ ] No personal browser data anywhere in the public repo, including git history.
- [ ] Firefox can't update itself past 52.9.0 (`about:config` shows `app.update.enabled` locked `false`).
- [ ] A tagged GitHub Release (zip + SHA-256) anyone can download and roll back to.
- [ ] Known limits (old CA list, TLS 1.0, no security patches) written down so nobody mistakes this for a daily browser.

## ✅ To Do

### Now
- [ ] **T011** — Remove the old profile from git history — `ad5c169` still serves cookies/history publicly · `(CHANGELOG Broken, chat)`
  - [ ] Log out of / revoke sessions for every site used in that profile (cookies may still be valid)
  - [ ] Owner OK, then rewrite history (`git filter-repo --path FirefoxPortableLegacy52/Data --invert-paths`) + force-push `main` — or recreate the repo
  - [ ] Ask GitHub Support to purge cached views if the repo stays public
- [ ] **T012** — Launch-test on Windows — the hardening was syntax-checked only, never run · `(CHANGELOG Unreleased)`
  - [ ] Fresh clone → `FirefoxPortable.exe` → profile created from `App/DefaultData/`, no integrity warning
  - [ ] `about:config`: `app.update.enabled` locked false; `network.IDN_show_punycode` true
  - [ ] Help → About shows no update button / no update check
- [ ] **T013** — First release `v52.9.0-h1` (zip of `FirefoxPortableLegacy52/`) — gives Rescue mode a ✅ anchor · `(chat, RELEASE.md)`

### Next
- [ ] **T014** — Decide TLS/cipher floor: keep TLS 1.0/1.1 + 3DES (legacy devices) or raise `security.tls.version.min` to 3 · `(chat: "hardened")`
- [ ] **T015** — Old CA list: test whether `security.enterprise_roots.enabled` (exists in FF52) fixes `SEC_ERROR_UNKNOWN_ISSUER` on modern sites · `(CONTEXT §6)`
- [ ] **T016** — Write the "known limits" note (no patches since 2018; use only for legacy sites) into README · `(chat: "stable")`

### Later
- [ ] **T017** — Drop `App/Firefox64/` (~107 MB) — start when: I confirm 64-bit is never wanted (INI forces 32-bit) · `(CONTEXT §6)`
- [ ] **T018** — Rebuild launcher from `Other/Source/` with NSIS — start when: a launcher bug actually needs fixing · `(CONTEXT §2)`

## ✔️ Done (latest 10)
- [x] **T001** — Stop tracking the personal `Data/` profile; gitignore it · `(172cc22)`
- [x] **T002** — Lock Firefox updates off via `mozilla.cfg` (32- and 64-bit) · `(172cc22)`
- [x] **T003** — Default off telemetry, experiments, self-repair, Pocket, tiles · `(172cc22)`
- [x] **T004** — Punycode IDNs + WebRTC IP-leak protection · `(172cc22)`
- [x] **T005** — `.gitattributes`: explicit binaries, CRLF for Windows text · `(172cc22)`
- [x] **T006** — Correct the "example only, not used" comment in the active `FirefoxPortable.ini` · `(172cc22)`
- [x] **T007** — `CONTEXT.md`, `CHANGELOG.md`, `TRAJECTORY.md`, `README.md` · `(DOCS_SHA)`

## ❌ Won't Do
- **T008** — Upgrade Firefox past 52.9.0 — why not: the request is to keep it an old version · `(chat)`
- **T009** — Edit binaries (`xul.dll`, `omni.ja`) to patch CVEs — why not: no source build here; risk of a browser that won't start · `(chat: "stable")`
- **T010** — Remove `pac_installer_log.ini` (the file says to gitignore it) — why not: the launcher needs it or shows "Integrity Failure Warning" · `(CONTEXT §6)`

## ❓ Questions for me
- Was committing `Data/` (your live profile) intentional, e.g. to carry bookmarks? If yes, export just `bookmarks.html` into `App/DefaultData/profile/` instead of the whole profile.
- OK to rewrite `main`'s history (T011)? It's the only way to really remove the profile; anyone who cloned must re-clone.
- What do you use this old browser for: legacy add-ons, Java applets, old router/KVM pages? That decides T014 and T017.

---

## Update Protocol (Verbatim)
> **For the AI Assistant:** when asked to "Update TRAJECTORY.md":
> 1. Re-run Phase 0: the existing file, this conversation, all GitHub issues, CHANGELOG, git log since the last sync, TODO comments.
> 2. New requests or issues → new items with the next free ID (`T` + number). IDs never change and are never reused.
> 3. Finished items → tick them and move them to **Done** with the commit or PR. Keep only the latest 10 there; older ones are already in CHANGELOG.
> 4. An issue closed as not planned, or an item I rejected → move it to **Won't Do** with the reason.
> 5. Re-order **Next** by value to me ÷ effort, and say in one line what moved and why.
> 6. Rewrite the **Final Goal** only if my requests changed the direction. Then say what changed.
> 7. Refresh the `_Last synced_` line.
