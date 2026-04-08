# overrec-screen-skills

A Claude Code skill that lets AI agents control screen overlays, take screenshots, and snap application windows to exact positions using [OverRec](https://apps.microsoft.com/detail/9pdn41kpj3hg).

## Requirements

- Windows 10/11 (or WSL with access to Windows executables)
- [OverRec](https://apps.microsoft.com/detail/9pdn41kpj3hg) installed and on PATH

## What it does

Trigger this skill when the user asks to:

- Take a screenshot of a screen region
- Draw a highlight/overlay rectangle on screen
- List connected monitors and their resolutions
- Find a running window by title keyword
- Snap (move + resize) a window to an exact location and size

## Example prompts

```
Take a screenshot of the top-left 800x600 area
Draw a red overlay at position 100,100 size 640x480 for 3 seconds
Find the Chrome window and snap it to the left half of my screen
Tile Notepad and Explorer side by side on monitor 1
```
