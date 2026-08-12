# herdr-yazi

![herdr 0.7+](https://img.shields.io/badge/herdr-0.7%2B-8a2be2)
![platform: macOS](https://img.shields.io/badge/platform-macOS-informational)
![zero JS dependencies](https://img.shields.io/badge/deps-zero-brightgreen)

![herdr-yazi open in a split pane beside Claude Code, showing the Yazi file explorer on the right](assets/hero.jpeg)

[🇰🇷 한국어](README.ko.md) | 🇺🇸 English

**A thin wrapper that opens [Yazi](https://yazi-rs.github.io/) inside a [herdr](https://herdr.dev) pane.** All file browsing, preview, and file operations are Yazi's own — this plugin's only job is finding the directory you're actually working in and opening Yazi there.

## Why you'd want it

- **Never leave your herdr session.** Browse and preview files without switching to VSCode.
- **No reimplementation.** Tree navigation, previews, file operations — all real Yazi. If a feature is missing, update Yazi itself.
- **Install and go.** The plugin's `[[build]]` step installs Yazi via Homebrew if it's missing.
- **Opens where you actually are.** The directory of the pane/workspace that triggered the key is read from herdr's context and passed via `--cwd`, so Yazi always opens in the folder you were just working in.

## What it does

- `[[build]]`: checks `command -v yazi`, runs `brew install yazi` if missing.
- `[[actions]] open`: reads the triggering pane/workspace's directory from `$HERDR_PLUGIN_CONTEXT_JSON` and opens a split pane via `herdr plugin pane open --placement split --cwd`.
- `[[actions]] open-tab`: same, but with `--placement tab` to open in a new tab instead.
- `[[panes]] explorer`: `exec yazi` inside that pane. That's the whole plugin.

Directory resolution order: `$HERDR_EXPLORER_DIR` → `HERDR_PLUGIN_CONTEXT_JSON`'s `focused_pane_cwd`/`workspace_cwd` → current directory.

## Quick start

```bash
# 1. Install (auto-installs Yazi via Homebrew if missing)
herdr plugin install speardragon/herdr-yazi
```

Add a keybinding in your herdr config (`~/.config/herdr/config.toml`):

```toml
[[keys.command]]              # open in a split pane
key = "prefix+y"
type = "plugin_action"
command = "ray.file-explorer.open"
description = "open file explorer"

[[keys.command]]              # open in a new tab
key = "prefix+Y"
type = "plugin_action"
command = "ray.file-explorer.open-tab"
description = "open file explorer in a new tab"
```

Run `herdr server reload-config`, then press the key. `prefix+y` opens a split, `prefix+Y` (Shift+y) opens a new tab.

### Alternative: register it in Command Center

Already using [Command Center](https://github.com/speardragon/herdr-command-center) instead of loose `prefix+<key>` bindings? You can register herdr-yazi's actions there too, so they show up in the same popup as everything else.

Add to your `commands.toml` (open it with `herdr plugin action invoke edit-config --plugin cdragon.command-center`):

```toml
[[commands]]
slot = "f"
label = "File explorer"
type = "plugin_action"
command = "ray.file-explorer.open"

[[commands]]
slot = "t"
label = "File explorer (new tab)"
type = "plugin_action"
command = "ray.file-explorer.open-tab"
```

The `[[keys.command]]` setup above still works fine on its own — this is just an option if you'd rather not add another dedicated key.

## Keys

This plugin defines no keys of its own — the pane runs plain Yazi, so every keybinding is [Yazi's own](https://yazi-rs.github.io/docs/keymap).

## Development

```bash
herdr plugin link /path/to/herdr-yazi   # local dev link (skips the [[build]] step)
herdr plugin action invoke ray.file-explorer.open
```

`bin/resolve-dir.sh` is a standalone shell script, testable without herdr:

```bash
HERDR_PLUGIN_CONTEXT_JSON='{"focused_pane_cwd":"/some/dir"}' bin/resolve-dir.sh
```

## Requirements

macOS + [Homebrew](https://brew.sh/).

---

## More herdr plugins

**[Command Center](https://github.com/speardragon/herdr-command-center)** — tired of trying to remember which `prefix+<key>` does what as you install more plugins? One keybinding opens a popup listing everything you've registered; press the slot key next to it and it runs immediately. No more digging through `config.toml` to recall a binding you set up three plugins ago.

![The Command Center popup listing commands in a grid, each with its own slot key](https://raw.githubusercontent.com/speardragon/herdr-command-center/main/docs/popup-list.png)

```bash
herdr plugin install speardragon/herdr-command-center --yes
```

**[Plugin Manager](https://github.com/speardragon/herdr-plugin-manager)** — installing, updating, and removing herdr plugins by hand means memorizing `herdr plugin ...` incantations in a spare pane every time. Want to see what has an update waiting, toggle plugins on and off, and browse what the community has built — all from one popup? This does exactly that.

| ![main view — installed plugin list](https://raw.githubusercontent.com/speardragon/herdr-plugin-manager/main/assets/main.png) | ![marketplace view — community plugins sorted by stars](https://raw.githubusercontent.com/speardragon/herdr-plugin-manager/main/assets/market.png) |
| :---: | :---: |
| Main view | Marketplace view |

```bash
herdr plugin install speardragon/herdr-plugin-manager --yes
```
