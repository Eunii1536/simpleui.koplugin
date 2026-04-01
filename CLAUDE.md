# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Workflow

This is a KOReader plugin written in Lua. There is no build or test command; development typically involves running the code in a KOReader emulator or deploying directly to an e-ink device.

### Extracting Translations
If you add or change strings shown to the user (wrapped in `_()` or `_lc()`), update `locale/simpleui.pot` using this Python 3 script:

```bash
python3 -c "
import re, datetime, pathlib

strings = set()
pattern = re.compile(r'(?:_|_lc)\(\"([^\"]+)\"\)')
for f in pathlib.Path('.').rglob('*.lua'):
    strings.update(pattern.findall(f.read_text(errors='ignore')))

lines = []
for s in sorted(strings):
    lines.append(f'msgid \"{s}\"\nmsgstr \"\"\n')

pathlib.Path('locale/simpleui.pot').write_text('\n'.join(lines))
print(f'{len(strings)} strings written to locale/simpleui.pot')
"
```

## Architecture and Structure

The plugin provides a custom Home Screen, bottom navigation bar, top status bar, and reworked library title bar for KOReader.

### Core Components
- `main.lua` — Plugin entry point and lifecycle management.
- `sui_config.lua` — Constants, action catalogue, and settings helpers.
- `sui_core.lua` — Shared layout infrastructure.
- `sui_patches.lua` — Monkey-patches for core KOReader functionality.
- `sui_menu.lua` — Settings menu (lazy-loaded).
- `sui_foldercovers.lua` — Folder covers and book cover overlays for the CoverBrowser mosaic view.
- `sui_i18n.lua` — Translation loader.

### UI Sections
- `sui_homescreen.lua` — The Home Screen widget.
- `sui_bottombar.lua` — Bottom navigation bar.
- `sui_topbar.lua` — Top status bar.
- `sui_titlebar.lua` — Reworked library title bar.
- `sui_quickactions.lua` — Quick Actions.

### Home Screen Modules (`desktop_modules/`)
The Home Screen is modular, allowing users to arrange and scale sections independently.
- `moduleregistry.lua` — Module registry and ordering.
- `module_books_shared.lua` — Shared helpers for book modules (e.g. caching covers, getting metadata).
- `module_clock.lua` — Clock and date.
- `module_currently.lua` — Currently Reading.
- `module_recent.lua` — Recent Books.
- `module_collections.lua` — Collections.
- `module_reading_goals.lua` — Reading Goals tracker.
- `module_reading_stats.lua` — Reading Stats.
- `module_quick_actions.lua` — Shortcut buttons.
- `module_new_books.lua` — Recently added books.
- `module_quote.lua` / `quotes.lua` — Quote of the Day.
