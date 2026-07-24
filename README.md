# herdr-yazi

![herdr 0.7+](https://img.shields.io/badge/herdr-0.7%2B-8a2be2)
![platform: macOS | Linux](https://img.shields.io/badge/platform-macOS%20%7C%20Linux-informational)
![zero JS dependencies](https://img.shields.io/badge/deps-zero-brightgreen)

![herdr-yazi open in a split pane beside Claude Code, showing the Yazi file explorer on the right](assets/hero.png)

🇰🇷 한국어 | [🇺🇸 English](#english)

**[Yazi](https://yazi-rs.github.io/)를 [herdr](https://herdr.dev) pane 안에서 바로 띄워주는 얇은 래퍼 플러그인.** 파일 탐색·미리보기·파일 조작 전부 Yazi 본체가 그대로 처리한다 — 이 플러그인은 "지금 작업 중인 디렉토리를 정확히 찾아서 Yazi를 그 자리에 여는 것"만 책임진다.

## 왜 필요한가

- **VSCode로 안 넘어가도 됨.** 터미널(herdr) 세션을 벗어나지 않고 파일을 훑어보고 내용을 미리 볼 수 있다.
- **Yazi를 재구현하지 않는다.** 트리 탐색, 이미지/텍스트 프리뷰, 파일 조작 — 전부 실제 Yazi 바이너리가 하는 일이고, 이 플러그인은 그걸 herdr pane 안에 배치만 한다. 기능이 부족하면 Yazi 자체를 업데이트하면 된다.
- **설치가 곧 준비 끝.** macOS에서는 플러그인의 `[[build]]` 단계가 Homebrew로 Yazi를 자동 설치한다. Linux에서는 Yazi가 이미 설치되어 있어야 한다.
- **어디서 열든 그 자리에서 뜬다.** 키를 누른 pane의 워크스페이스 디렉토리를 herdr 컨텍스트에서 읽어 `--cwd`로 넘기기 때문에, 항상 "지금 작업 중이던 그 폴더"에서 Yazi가 열린다.

## 무엇을 하는가

- `[[build]]`: `command -v yazi`로 확인하고, macOS에서만 Yazi가 없으면 `brew install yazi`를 실행한다. Linux에서는 설치 안내와 함께 종료한다.
- `[[actions]] open`: 트리거된 pane/워크스페이스의 디렉토리를 `$HERDR_PLUGIN_CONTEXT_JSON`에서 읽어 `herdr plugin pane open --placement split --cwd`로 split pane을 연다.
- `[[actions]] open-tab`: 위와 동일하지만 `--placement tab`으로 새 탭에 연다.
- `[[panes]] explorer`: 그 pane 안에서 `exec yazi` — 그게 전부다.

디렉토리 해석 우선순위: `$HERDR_EXPLORER_DIR` → `HERDR_PLUGIN_CONTEXT_JSON`의 `focused_pane_cwd`/`workspace_cwd` → 현재 디렉토리.

## 빠른 시작

```bash
# 1. 설치 (Linux에서는 Yazi를 먼저 설치해야 함)
herdr plugin install speardragon/herdr-yazi
```

herdr 설정(`~/.config/herdr/config.toml`)에 키바인딩 추가:

```toml
[[keys.command]]              # split pane으로 열기
key = "prefix+y"
type = "plugin_action"
command = "ray.file-explorer.open"
description = "open file explorer"

[[keys.command]]              # 새 탭으로 열기
key = "prefix+Y"
type = "plugin_action"
command = "ray.file-explorer.open-tab"
description = "open file explorer in a new tab"
```

`herdr server reload-config` 실행 후 키를 누르면 끝. `prefix+y`는 split, `prefix+Y`(Shift+y)는 새 탭.

## 키

이 플러그인은 자체 키를 정의하지 않는다 — pane 안에서 실행되는 건 순수 Yazi이므로, 모든 키는 [Yazi 공식 키바인딩 문서](https://yazi-rs.github.io/docs/keymap)를 따른다.

## 개발

```bash
herdr plugin link /path/to/herdr-yazi   # 로컬 개발용 링크 (build 단계는 실행 안 됨)
herdr plugin action invoke ray.file-explorer.open
```

`bin/resolve-dir.sh`는 독립 실행 가능한 셸 스크립트라 herdr 없이도 직접 테스트할 수 있다:

```bash
HERDR_PLUGIN_CONTEXT_JSON='{"focused_pane_cwd":"/some/dir"}' bin/resolve-dir.sh
```

## 요구 사항

macOS + [Homebrew](https://brew.sh/), 또는 Yazi가 설치된 Linux.

---

## English

![herdr 0.7+](https://img.shields.io/badge/herdr-0.7%2B-8a2be2)
![platform: macOS | Linux](https://img.shields.io/badge/platform-macOS%20%7C%20Linux-informational)
![zero JS dependencies](https://img.shields.io/badge/deps-zero-brightgreen)

**A thin wrapper that opens [Yazi](https://yazi-rs.github.io/) inside a [herdr](https://herdr.dev) pane.** All file browsing, preview, and file operations are Yazi's own — this plugin's only job is finding the directory you're actually working in and opening Yazi there.

### Why you'd want it

- **Never leave your herdr session.** Browse and preview files without switching to VSCode.
- **No reimplementation.** Tree navigation, previews, file operations — all real Yazi. If a feature is missing, update Yazi itself.
- **Install and go.** On macOS, the plugin's `[[build]]` step installs Yazi via Homebrew if it's missing. On Linux, Yazi must already be installed.
- **Opens where you actually are.** The directory of the pane/workspace that triggered the key is read from herdr's context and passed via `--cwd`, so Yazi always opens in the folder you were just working in.

### What it does

- `[[build]]`: checks `command -v yazi`; on macOS, it runs `brew install yazi` if missing. On Linux, it exits with installation guidance.
- `[[actions]] open`: reads the triggering pane/workspace's directory from `$HERDR_PLUGIN_CONTEXT_JSON` and opens a split pane via `herdr plugin pane open --placement split --cwd`.
- `[[actions]] open-tab`: same, but with `--placement tab` to open in a new tab instead.
- `[[panes]] explorer`: `exec yazi` inside that pane. That's the whole plugin.

Directory resolution order: `$HERDR_EXPLORER_DIR` → `HERDR_PLUGIN_CONTEXT_JSON`'s `focused_pane_cwd`/`workspace_cwd` → current directory.

### Quick start

```bash
# 1. Install (install Yazi first on Linux)
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

### Keys

This plugin defines no keys of its own — the pane runs plain Yazi, so every keybinding is [Yazi's own](https://yazi-rs.github.io/docs/keymap).

### Development

```bash
herdr plugin link /path/to/herdr-yazi   # local dev link (skips the [[build]] step)
herdr plugin action invoke ray.file-explorer.open
```

`bin/resolve-dir.sh` is a standalone shell script, testable without herdr:

```bash
HERDR_PLUGIN_CONTEXT_JSON='{"focused_pane_cwd":"/some/dir"}' bin/resolve-dir.sh
```

### Requirements

macOS + [Homebrew](https://brew.sh/), or Linux with Yazi installed.
