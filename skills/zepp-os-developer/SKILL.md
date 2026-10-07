---
name: zepp-os-developer
description: "Use when writing, debugging, building, or previewing Zepp OS (Zeus CLI) smartwatch apps in the high-roller project: pages, widgets, layouts, app.json, permissions, i18n, simulator, or zeus dev/build/preview errors. Triggers: 'zepp os', 'zeus', 'zeus dev', 'zeus build', 'watch app', 'amazfit', 'hmUI', '@zos', 'app.json', 'simulator', 'GTS 3', 'GTR 4'."
---

# Zepp OS Development (high-roller)

The app lives in `high-roller/` (run all `zeus` commands from there). Zeus CLI is installed in the workspace root `node_modules`, so use `npx zeus ...`.

## Project layout

| Path | Purpose |
|------|---------|
| `app.js` | `App({ globalData, onCreate, onDestroy })` entry point |
| `app.json` | Manifest: appId, permissions, API version, targets, i18n |
| `page/<target>/<name>/index.page.js` | Page logic: `Page({ onInit, build, onDestroy })` |
| `page/<target>/<name>/index.page.[r\|s].layout.js` | Layout per screen shape: `r` = round, `s` = square |
| `page/i18n/<lang>.po` | Translations, read with `getText(msgid)` |
| `assets/<target>.<shape>/` | Per-shape images and icons (frame sequences go in `anim/<name>/`) |
| `scripts/` | Python generators for animation frames (not bundled into the app) |
| `utils/` | Shared helpers (`assets(type)(path)`) |

Manifest is `configVersion: v3`. A target (here `gt`) lists its `pages` and `platforms` (`st: "r"` / `"s"`). Every page must be registered under `targets.<target>.module.page.pages` without the `.js` extension.

## Conventions

- Import APIs from `@zos/*` modules (`@zos/ui`, `@zos/device`, `@zos/utils`, `@zos/i18n`, `@zos/router`, `@zos/storage`, `@zos/sensor`). Never use the legacy global `hmUI`, `hmFS`, or `hmSetting`.
- Create widgets in `build()`, not `onInit()`. Use `onDestroy()` to stop timers and listeners.
- Select the shape-specific layout with `import { X } from "zosLoader:./index.page.[pf].layout.js"`. Keep every layout file exporting the same names.
- Size and position everything with `px()` from `@zos/utils`. `designWidth` in `app.json` (480) is the baseline.
- Read screen size from `getDeviceInfo()` in `@zos/device` instead of hard-coding it.
- Colors are numeric hex (`0xffffff`). Logging goes through `log.getLogger("name")` from `@zos/utils`.
- Each permission a feature needs (storage, sensors, device info) must be listed in `app.json` `permissions`, or the API call fails at runtime.
- User-facing text goes in `page/i18n/*.po` and `app.json` `i18n`, not inline strings.

## API level and devices

`runtime.apiVersion` in `app.json` (`compatible`, `target`, `minVersion`) decides which devices can install the app. Check the device list before raising it: https://docs.zepp.com/docs/reference/related-resources/device-list/

| Device | API level |
|--------|-----------|
| Amazfit GTS 3 | 1.0 |
| Amazfit GTR 4 | 3.5 |

An app targeting API 3.5 or lower runs on the GTR 4. API 4.0+ does not.

## Commands

| Command | Use |
|---------|-----|
| `npx zeus dev` | Compile and hot-reload on the simulator (requires a running simulator with a device opened) |
| `npx zeus build` | Compile locally to a `.zab` package. No phone or device needed |
| `npx zeus preview` | Generates a QR code for the Zepp phone app. Needs a phone |
| `npx zeus create <name>` | Scaffold a project (already done) |

## Simulator

Zepp OS Simulator v2 is installed at `/Applications/simulator.app`. Start it, download a device model in its download manager, open the device, then run `npx zeus dev`. Keyboard: Home = fn + Left, Select = Enter, Back = Delete, Up/Down = arrows, mouse wheel = digital crown.

## Known Zeus CLI issues in this workspace

- **`Cannot find module 'zeppos-app-utils'`**: `@zeppos/zeus-cli@1.9.3` bundles this module but resolves it through `_moduleAliases` in the nearest `package.json`. The workspace-root `package.json` carries that alias. Do not `npm install zeppos-app-utils`; the public package has no `fetchDevices` and fails with `devicesData is not a function`.
- **Paths with spaces**: the workspace folder is `High Roller`, and Zeus builds shell commands without quoting paths (for example the post-create `npm install` failed). If a Zeus step fails with `No such file or directory` at `.../High`, run that step manually inside quotes, or work from a symlink without a space.
- **`zeus dev` exits immediately**: confirm the simulator is open with a device window before retrying.

## Animation, gestures, and dynamic widgets

- **Frame sequences** use `widget.IMG_ANIM` (`x`, `y`, `anim_path`, `anim_prefix`, `anim_ext`, `anim_fps`, `anim_size`, `repeat_count`). Frames are named `<prefix>_<n>.<ext>` starting at 0, under `assets/<target>.<shape>/<anim_path>/`. Start with `setProperty(prop.ANIM_STATUS, anim_status.START)`. `repeat_count: 0` loops forever and never fires `anim_complete_call`.
- The `widgetAnimations` doc page covers property animations (`prop.X/Y/W/H/ALPHA` tweens), not image sequences. Use `IMG_ANIM` for PNG frames.
- Images are not scaled by `px()`. Render frames at a fixed pixel size and center them from `getDeviceInfo()`.
- **Swipes**: `onGesture({ callback })` from `@zos/interaction` with `GESTURE_LEFT/RIGHT/UP/DOWN`. Only one handler can be registered. Return `true` to suppress the default (swipe right otherwise exits the app).
- **Taps**: `widget.addEventListener(event.CLICK_UP, fn)`.
- **Dynamic UI**: `hmUI.deleteWidget(w)` then recreate, which is simpler than mutating props. Page-level `let` state must be reset in `onInit()`.
- **Radial placement**: bearing `b` in degrees clockwise from 12 o'clock gives `x = cx + r*sin(b)`, `y = cy - r*cos(b)`.
- Generated assets: this project renders its dice frames with `scripts/render_d6.py` and `scripts/render_dice.py` (Pillow + numpy). See `AGENTS.md`.

## Workflow when changing the app

1. Edit the page and both layout files (`r` and `s`) together so the shapes stay in sync.
2. Register new pages in `app.json`; add needed permissions.
3. Run `npx zeus build` to catch manifest and bundling errors without a device.
4. Verify visually with `npx zeus dev` on the simulator.
5. When unsure about an API, check the docs at https://docs.zepp.com/docs/ rather than guessing signatures.
