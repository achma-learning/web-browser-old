# web-browser-old

> Firefox 52 (the 2018 one) in a folder you can carry on a USB stick, set up so it never updates itself and doesn't phone home to services that died years ago.

**Quick start:** 1. Download or clone this repo · 2. Double-click `FirefoxPortableLegacy52\FirefoxPortable.exe` · 3. Firefox 52.9.0 ESR opens with a fresh, empty profile.

_TODO: add a screenshot of Help → About showing 52.9.0 ESR with no update button_

## What it is

This is the PortableApps.com **Firefox Portable Legacy 52** package (Firefox **52.9.0 ESR**, Windows, 32- and 64-bit). It's frozen on purpose and hardened with one settings file. Firefox 52 is the last version that still runs old XUL add-ons and NPAPI plugins (e.g. Java applets). Some ancient admin pages and intranet sites still need those.

What makes it different from just downloading the old package:
- **Updates are locked off.** One click on "update" used to replace it with a newer Firefox and kill every legacy add-on. Now it can't.
- **Dead 2018 services are off**: telemetry, experiments, Pocket, new-tab tiles.
- **A bit safer to browse with.** Look-alike domains show as `xn--…` punycode, and WebRTC doesn't leak your local IP.
- **Your profile stays yours.** `Data/` (cookies, history, logins) is gitignored.

## ⚠️ Read this first

Firefox 52 has had **no security patches since June 2018**. Use it for the one legacy site or add-on that needs it, not as your everyday browser. Some modern HTTPS sites will fail with `SEC_ERROR_UNKNOWN_ISSUER` because its list of trusted certificates is from 2018.

## Install & run

Windows only (any version Firefox 52 supports, XP SP2 and up). The CPU must support SSE2.

```bash
git clone https://github.com/achma-learning/web-browser-old.git
# then, in Explorer:
#   web-browser-old\FirefoxPortableLegacy52\FirefoxPortable.exe
```

No git? Use **Code → Download ZIP** on GitHub and unzip. Copy the `FirefoxPortableLegacy52` folder anywhere you like: a USB stick, `D:\Apps\`, wherever. Works with the PortableApps.com Platform too.

> **Upgrading an older checkout (from before Oct 2026)?** Back up `FirefoxPortableLegacy52\Data\` before `git pull`. That folder is no longer tracked, so git deletes it on pull.

## Usage

1. Run `FirefoxPortable.exe`. The first run creates `Data\profile\` from `App\DefaultData\`.
2. Install the legacy add-on you need (`.xpi` → drag onto the window), or open the old site.
3. Want to check the hardening? Open `about:config` and search `app.update.enabled`. It shows **locked** and `false`.

Launcher options (multiple instances, run-from-CD mode, 32/64-bit) live in `FirefoxPortableLegacy52\FirefoxPortable.ini`. Every option is explained in `Other\Source\Readme.txt`. Browser settings forced by this repo live in `App\Firefox\mozilla.cfg`.

## What's new

**2026-10-14** — stopped publishing the browser profile, locked updates off, switched off dead Mozilla services. [Full changelog →](./CHANGELOG.md)

## Why I built this

Some things only work in an old browser. I wanted one copy of Firefox 52 that stays exactly that version, runs from anywhere, and is as safe as an unpatched 2018 browser can reasonably be.

## License

No license file for this repo's own changes yet. Bundled parts keep their upstream licenses: Firefox is MPL 2.0 (`about:license` inside the browser). The Firefox Portable launcher is GPL v2 (see [`Other/Source/License.txt`](./FirefoxPortableLegacy52/Other/Source/License.txt)).

## See also

- [`CONTEXT.md`](./CONTEXT.md) — for AI assistants joining the project
- [`CHANGELOG.md`](./CHANGELOG.md) — what shipped when
- [`TRAJECTORY.md`](./TRAJECTORY.md) — the final goal and the to-do list
