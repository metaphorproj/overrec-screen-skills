# overrec-screen

A Claude Code skill that lets AI agents control screen overlays, take screenshots, and snap application windows to exact positions using [OverRec](https://apps.microsoft.com/detail/9pdn41kpj3hg).

## Requirements

- Windows 10/11 (or WSL with access to Windows executables)
- [OverRec](https://apps.microsoft.com/detail/9pdn41kpj3hg) installed and on PATH

## What it does

Trigger this skill when the user asks to:

- Take a screenshot of a screen region (saved to file and/or clipboard)
- Draw a highlight/overlay rectangle on screen (named color or `#RRGGBB` hex)
- List connected monitors and their resolutions, positions, and scale factors
- Find a running window by title keyword (with optional location/size details)
- Snap (move + resize) a window to an exact location and size
- Tile or arrange multiple windows side by side
- Watch/monitor a screen region by capturing frames at an interval

## Example prompts

```
Take a screenshot of the top-left 800x600 area
Draw a red overlay at position 100,100 size 640x480 for 3 seconds
Draw a highlight using color #FF4400 around the toolbar
Find the Chrome window and snap it to the left half of my screen
Tile Notepad and Explorer side by side on monitor 1
List all visible windows
Show me Chrome's current position and size
Watch the top-right corner of my screen every 5 seconds
```

## CLI reference summary

| Command | Purpose |
|---|---|
| `cli monitors [--all]` | List monitors (compact or full details) |
| `cli window [--all] [keyword...]` | Find windows by title; `--all` adds location/size |
| `cli snap --windowid ID --location X,Y --size WxH` | Move and resize a window |
| `cli draw --location X,Y --size WxH [--color] [--timeout]` | Draw overlay rectangle |
| `cli screenshot --location X,Y --size WxH [--output] [--no-clipboard]` | Capture screen region |

### Command details

#### `monitors` — List displays
```
overrec cli monitors [--all]
```
Without `--all`: compact ID/resolution list. With `--all`: full details — resolution, absolute position, scale factor, refresh rate, rotation, primary flag.

#### `window` — Find a window
```
overrec cli window [--all] [<keyword...>]
```
Lists visible windows whose title contains all given keywords (case-insensitive). Omit keywords to list every visible window.

- Default: compact ID/title list
- `--all`: also shows monitor number, location, and size (`max`/`min` for maximised/minimised)

#### `snap` — Move and resize a window
```
overrec cli snap --windowid ID --location X,Y --size WxH [--monitor ID]
```
Moves and resizes the window to the exact position and size. DWM shadow margins are corrected automatically so the visible frame lands exactly at the requested coordinates.

#### `draw` — Draw an overlay rectangle
```
overrec cli draw --location X,Y --size WxH [--color COLOR] [--timeout SECS] [--monitor ID]
```
Colors: `red`, `green`, `blue`, `yellow`, `white`, `black`, or `#RRGGBB` hex. Default is blue.

#### `screenshot` — Capture a screen region
```
overrec cli screenshot --location X,Y --size WxH [--output FILE.png] [--no-clipboard] [--monitor ID]
```
By default the image is copied to the clipboard. Use `--output` to save to file. Use `--no-clipboard` to skip clipboard copy.

## Typical workflow

```bash
# 1. Check monitor layout
overrec cli monitors --all

# 2. Find the target window
overrec cli window chrome

# 3. Snap it to the left half of monitor 0
overrec cli snap --windowid 657846 --location 0,0 --size 960x1080

# 4. Draw a reference rectangle to verify placement (auto-closes after 2s)
overrec cli draw --location 0,0 --size 960x1080 --timeout 2

# 5. Take a screenshot of that area
overrec cli screenshot --location 0,0 --size 960x1080 --output left-half.png
```

The skill definition lives in [`skills/overrec-screen/SKILL.md`](skills/overrec-screen/SKILL.md).
