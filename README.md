# web-browser-old

> A fresh, never-used copy of portable Firefox 52 (the 2018 one) for Windows, locked so it never updates and tightened as far as settings allow for browsing the internet.

**Quick start:** 1. Download or clone this repo · 2. Double-click `FirefoxPortableLegacy52\FirefoxPortable.exe` · 3. Firefox 52.9.0 ESR opens with a fresh, empty profile.

_TODO: add a screenshot of Help → About showing 52.9.0 ESR with no update button_

## What it is

This is the PortableApps.com **Firefox Portable Legacy 52** package (Firefox **52.9.0 ESR**, Windows, 32- and 64-bit). Every file matches a fresh install from the official `FirefoxPortableLegacy52_52.9.0_English.paf.exe`, checked byte by byte. The only addition is one settings file, `mozilla.cfg`, that hardens it:

- **Never updates.** It stays exactly 52.9.0; "update" is locked off.
- **Modern HTTPS only.** TLS 1.2 or newer; old weak ciphers (3DES, DHE) are off. It also trusts certificates from the Windows store, because its own list is from 2018.
- **Less to attack.** Plugins (Java, Silverlight), Flash, WebGL, WebAssembly and DRM are off. These are the usual ways into an old browser.
- **Less tracking.** Tracking protection on, third-party cookies blocked, no prefetching.
- **No phishing look-alikes or IP leaks.** Odd domains show as `xn--…`, and WebRTC hides your local IP.
- **No phoning home.** Telemetry, experiments, Pocket and new-tab tiles are off.
- **No personal data.** The profile folder `Data/` is created on first run and is gitignored.

## ⚠️ Read this first

Firefox 52 has had **no security patches since June 2018**. The settings above shrink the risk; they can't remove it. Don't use it for banking or anything you can't afford to lose. Many modern sites will look broken: it's a 2018 engine, and WebGL/WebAssembly are off.

## Install & run

Windows only (any version Firefox 52 supports, XP SP2 and up). The CPU must support SSE2.

```bash
git clone https://github.com/achma-learning/web-browser-old.git
# then, in Explorer:
#   web-browser-old\FirefoxPortableLegacy52\FirefoxPortable.exe
```

No git? Use **Code → Download ZIP** on GitHub and unzip. Copy the `FirefoxPortableLegacy52` folder anywhere: a USB stick, `D:\Apps\`, wherever. Works with the PortableApps.com Platform too.

On 64-bit Windows 7 or newer it runs the 64-bit build; otherwise the 32-bit one.

> **Upgrading an older checkout (from before Oct 2026)?** Back up `FirefoxPortableLegacy52\Data\` before `git pull`. That folder is no longer tracked, so git deletes it on pull.

## Usage

1. Run `FirefoxPortable.exe`. The first run creates `Data\profile\` from `App\DefaultData\`.
2. Browse. To check the hardening, open `about:config` and search `app.update.enabled` (**locked**, `false`) or `security.tls.version.min` (`3`).
3. A site really needs something that's off (e.g. WebGL)? Flip that one pref in `about:config` (`webgl.disabled` → `false`). Only `app.update.*` is locked.

Want the 32-bit build always? Copy `Other\Source\FirefoxPortable.ini` next to `FirefoxPortable.exe` and set `AlwaysUse32Bit=true`. Every launcher option is explained in `Other\Source\Readme.txt`.

## What's new

**2026-10-14** — now a byte-exact fresh install of the original v0 Firefox, hardened for internet use; personal data removed. [Full changelog →](./CHANGELOG.md)

## Why I built this

I wanted the exact Firefox from my v0 upload, clean, with none of my data in it, and as safe as an old browser can be made for going online.

## License

No license file for this repo's own changes yet. Bundled parts keep their upstream licenses: Firefox is MPL 2.0 (`about:license` inside the browser). The Firefox Portable launcher is GPL v2 (see [`Other/Source/License.txt`](./FirefoxPortableLegacy52/Other/Source/License.txt)).

## See also

- [`CONTEXT.md`](./CONTEXT.md) — for AI assistants joining the project
- [`CHANGELOG.md`](./CHANGELOG.md) — what shipped when
- [`TRAJECTORY.md`](./TRAJECTORY.md) — the final goal and the to-do list
