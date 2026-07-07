# herdr-yazi

Opens [Yazi](https://yazi-rs.github.io/) (a terminal file manager) in a [herdr](https://herdr.dev) pane, starting in the current workspace's directory.

This is a thin wrapper, not a reimplementation — all file browsing, preview, and file operations are Yazi's own. Installing this plugin installs Yazi via Homebrew if it isn't already present.

## Install

```bash
herdr plugin install speardragon/herdr-yazi
```

## Open

```bash
herdr plugin action invoke ray.file-explorer.open
```

Key binding (herdr config):

```toml
[[keys.command]]
key = "prefix+o"
type = "plugin_action"
command = "ray.file-explorer.open"
description = "open file explorer"
```

## Start directory

The `open` action resolves a start directory (`$HERDR_EXPLORER_DIR` → `$HERDR_PLUGIN_CONTEXT_JSON`'s `focused_pane_cwd`/`workspace_cwd` → current directory) and passes it to the pane via `--cwd`, so Yazi opens wherever the action was triggered from — not wherever herdr happens to place the new pane.

## Requirements

macOS with [Homebrew](https://brew.sh/). Installs [Yazi](https://github.com/sxyazi/yazi) automatically if missing.
