# TRAJECTORY
_Last synced: 2026-10-14 @ f2e0001 · built from: my requests (chat) + GitHub issues (none exist yet) + CHANGELOG + CONTEXT §6/§7. No TODO comments in the code._

## 🎯 Final Goal
**"A fresh version of portable Firefox, in the original Firefox version uploaded in v0, but more hardened for internet use"** (chat). That means Firefox 52.9.0 ESR, Portable Edition, for Windows, byte-identical to the official installer. It must contain none of my personal data (*"just delete personal data"*), never update itself, and carry every safety setting an unpatched 2018 engine can take.

**Done when:**
- [x] Repo content = official `FirefoxPortableLegacy52_52.9.0_English.paf.exe` install + `mozilla.cfg`/`autoconfig.js` only (`f2e0001`)
- [ ] No personal browser data anywhere in the public repo, **including git history**
- [ ] A fresh clone launches on Windows with a clean profile, no "Integrity Failure Warning", hardening visible in `about:config`
- [ ] A tagged GitHub Release (zip + SHA-256) anyone can download and roll back to

## ✅ To Do

### Now
- [ ] **T011** — Remove the old profile from git history — `ad5c169` still serves it publicly · `(chat: "just delete personal data")`
  - [ ] Log out of / revoke sessions for every site used in that profile (cookies may still be valid)
  - [ ] Rewrite history without `FirefoxPortableLegacy52/Data` and force-push `main` (AI attempt blocked; owner runs it or allows it)
  - [ ] Ask GitHub Support to purge cached commits / PR refs that still point at `ad5c169`
- [ ] **T012** — Launch-test on Windows — hardening was machine-checked only, never run · `(CHANGELOG Unreleased)`
  - [ ] Fresh clone → `FirefoxPortable.exe` → profile created from `App/DefaultData/`, no integrity warning
  - [ ] `about:config`: `app.update.enabled` locked false; `security.tls.version.min` = 3
  - [ ] Browse 5 everyday sites (search, mail, bank, YouTube, Wikipedia): note what breaks
- [ ] **T013** — First release `v2026.10.14` (zip of `FirefoxPortableLegacy52/`) after T011 · `(chat, RELEASE draft)`

### Next
- [ ] **T015** — Check that `security.enterprise_roots.enabled` really fixes `SEC_ERROR_UNKNOWN_ISSUER` on modern sites · `(CONTEXT §6)`
- [ ] **T020** — Optional "max safety" switch: Ion/Baseline JIT off (`javascript.options.ion`/`baselinejit`) if T012 shows sites still usable · `(chat: "more hardened")`

### Later
- [ ] **T018** — Rebuild launcher from `Other/Source/` with NSIS — start when: a launcher bug actually needs fixing · `(CONTEXT §2)`

## ✔️ Done (latest 10)
- [x] **T019** — Byte-exact fresh install: 6 `.chk` restored, installer line endings, no custom INI · `(f2e0001)`
- [x] **T014** — TLS/cipher floor: TLS 1.2 min, 3DES/DHE off (decided by "hardened for internet use") · `(f2e0001)`
- [x] **T021** — Internet hardening: plugins/Flash/WebGL/wasm/DRM off, tracking protection, 3rd-party cookies blocked · `(f2e0001)`
- [x] **T001** — Stop tracking the personal `Data/` profile; gitignore it · `(172cc22)`
- [x] **T002** — Lock Firefox updates off via `mozilla.cfg` (32- and 64-bit) · `(172cc22)`
- [x] **T003** — Default off telemetry, experiments, self-repair, Pocket, tiles · `(172cc22)`
- [x] **T004** — Punycode IDNs + WebRTC IP-leak protection · `(172cc22)`
- [x] **T005** — `.gitattributes` hardening (superseded by `* -text` in `f2e0001`) · `(172cc22)`
- [x] **T006** — Correct the misleading INI comment (INI later removed for fresh install) · `(172cc22)`
- [x] **T007** — `CONTEXT.md`, `CHANGELOG.md`, `TRAJECTORY.md`, `README.md` · `(64c8c1b)`

## ❌ Won't Do
- **T008** — Upgrade Firefox past 52.9.0 — why not: must stay "the original Firefox version uploaded in v0" · `(chat)`
- **T009** — Edit binaries (`xul.dll`, `omni.ja`) to patch CVEs — why not: breaks "fresh install"; no source build here · `(chat)`
- **T010** — Remove `pac_installer_log.ini` (the file says to gitignore it) — why not: launcher shows "Integrity Failure Warning" without it · `(CONTEXT §6)`
- **T016** — Separate "known limits" README item — why not: done inside the README rewrite (⚠️ section) · `(64c8c1b)`
- **T017** — Drop `App/Firefox64/` — why not: part of the fresh install, and now the default build on 64-bit Windows · `(chat: "fresh install")`

## ❓ Questions for me
- OK for history rewrite + force-push of `main` (T011)? Everyone who cloned must re-clone; PR #1 would be replaced.
- Do you need Java/Flash/WebGL on any specific site? They're off now; I can document a per-site way to re-enable.

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
