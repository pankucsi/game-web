# Tiled map — tower / Tiled pálya — torony

Specs: `docs/specs/DESIGN_LOCK.md` (layer grammar, authoritative), `docs/specs/ART_BIBLE.md` (art rules).

## Files
- `tower.json` — the map Phaser loads (45×144 tiles @16px = 720×2304 px). The tileset **`tower_tileset` is embedded** (firstgid 1, image `tower_tileset.png`), because Phaser cannot load external tilesets.
- `tower_tileset.png` — 16×16 tiles, 16 columns, 0 margin/spacing, transparent background.
- `tower_tileset.tsj` — the same tileset as an external Tiled file (source of truth for tile properties `collides`, `oneWay`, `deco`, `name`).

## Editing in Tiled (stays Tiled-editable)
1. Open `tower.json` in [Tiled](https://www.mapeditor.org/). The embedded tileset works as-is.
2. If you change tile properties, edit `tower_tileset.tsj`, then in the map use *Map → Replace Tileset* / or re-import, and **before saving the map make sure the tileset is embedded** (Tilesets panel → *Embed Tileset*). Never ship a `tower.json` whose `tilesets[]` entry has a `source` field — Phaser will fail to load it.
3. Save as JSON with the same name. Reload the game.

## Layers (bottom → top)
`background` → `bg-objects` → `ground` → `tileLayer` → `collidePlatforms` → `platforms` (object) → `deco_front`.
- Gameplay collision comes **only** from the `platforms` object layer (rectangles; props `tier`, `kind` main|float, `noDrop`, `isBoss`). Tile properties are informational / future tile collision.
- `deco_front` draws in front of the player (depth `DEPTH.DECO_FRONT`), enemy bullets/telegraphs above it (`DEPTH.ENEMY_DANGER`). Toggle via `SHOW_DECO_FRONT` in `src/game/core/constants.ts`; in `?lockscene` key **D** toggles it.
- Phaser uses object centers: `center = (x + width/2, y + height/2)`.

## Palette
All map PNGs must pass `npm run palette:check` without exception (`src/game/data/palette.json`).
