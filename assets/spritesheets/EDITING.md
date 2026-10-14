# Karakter spritesheetek – kézi szerkesztés (Aseprite / bármilyen pixel editor)

**A PNG csíkok a mérvadó forrás.** Ezek a repóban: `public/assets/spritesheets/<id>/`. (A kiinduló változatot az art repó `art/style/characters/src/anim_chars.py` generálta; azt `--force`-szal ne futtasd kézi szerkesztés után.)

## Karakterek és mappák
| id | Karakter | Hitbox (32×40 cellában) | Repó helye |
|---|---|---|---|
| harcos | Harcos | 16×34, offset (8,6) | `public/assets/spritesheets/harcos/` |
| erdojaro | Erdőjáró | 16×34, offset (8,6) | `public/assets/spritesheets/erdojaro/` |
| vervadasz | Vérvadász | 16×32, offset (9,8) | `public/assets/spritesheets/vervadasz/` |
| kotoro | Kőtörő | 20×34, offset (6,6) | `public/assets/spritesheets/kotoro/` |

A `SHEET_SPEC.json` (fps, loop, hitbox, origin, footAnchored, hitFrames) ugyanebben a mappában van; a kód (`src/game/core/constants.ts` → `PLAYER_ANIMS`) ebből olvassa az fps/loop/hitbox/hitFrames értékeket.

## Fájlok minden mappában
`<id>-<anim>.png`, egy animáció = egy vízszintes csík, képkockák **balról jobbra**, rés nélkül:

| anim | képkocka @ fps | cella | loop |
|---|---|---|---|
| idle | 4 @ 6 | 32×40 | igen |
| run | 8 @ 12 | 32×40 | igen |
| jump | 2 @ 10 | 32×40 | nem (utolsó tartva) |
| fall | 2 @ 8 | 32×40 | igen |
| land | 2 @ 15 | 32×40 | nem |
| attack-ground | 5 @ 15 | 32×40 | nem |
| attack-air | 4 @ 15 | 32×40 | nem |
| hurt | 2 @ 10 | 32×40 | nem |
| death | 8 @ 8 | **64×40** | nem (utolsó tartva) |

- A karakter jobbra néz (balra a játék tükrözi). Origin: alul középen (0.5, 1). A 64 széles death cellában a test közepe x=32, a hitbox offsetX +16.
- `preview-*.gif` / `preview_all.png` csak az art repóban vannak (előnézet), a játék nem használja.

## Szabályok
- Alpha csak 0 vagy 255 (nincs félátlátszóság, nincs antialias).
- Csak a `src/game/data/palette.json` színei (Harcosnál + a build13 felülírások, ezek is palettaszínek). **#ff2a8a magenta tilos** (veszélyjelzés).
- Földi animációk (idle, run, land, attack-ground, hurt, death): a legalsó pixel a **39. sorban** (utolsó sor). A jump, fall, attack-air levegőben van: ott a törzs áll, a láb felhúzódik, nem kell a 39. sorig érnie.
- Méret: szélesség = cella × képkockaszám, magasság 40. Ha képkockát adsz hozzá, írd át a `frames`-t a `SHEET_SPEC.json`-ban (a kód onnan veszi).
- Fegyverek most a sprite részei. Harcos: a shuriken NINCS rajzolva (kódban jön létre a hitFrame-en). Erdőjáró: a toboz az 1–3. támadó képkockán eltűnik a kézből (dobás/bumeráng a kódban), a 4.-en visszakapja.

## Ellenőrzés szerkesztés után
```
# a repó gyökeréből
python3 scripts/check_sheets.py                                              # mind a négy mappa
python3 scripts/check_sheets.py public/assets/spritesheets/kotoro            # egy karakter
python3 scripts/check_sheets.py public/assets/spritesheets/kotoro/kotoro-run.png   # egy fájl
npm run palette:check
```
`RESULT: 0 hard error(s)` = rendben. A cellaszélt érintő képkockák csak figyelmeztetések.

## Betöltés
A játék sima PNG-ket tölt be (Phaser `load.spritesheet`), nincs build lépés: szerkeszt → ment (`public/assets/spritesheets/<id>/`) → böngésző frissítés.
