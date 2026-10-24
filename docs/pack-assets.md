# Textures and ULD layouts

These files belong in the target language-pack repository, beside `corpus/` and `pack.json`.
PackBuilder includes them in the built pack. It does not change the installed game files.

```text
ffxiv-language-pack-it/
  pack.json
  corpus/
  ui/icon/120000/en/120001.tex
  ui/icon/120000/en/120001_hr1.tex
  layouts/castbar.json
```

## Textures

Keep the game's path and filename under `ui/icon/`. Use `en` for every target language because
Gubal replaces the game's English resources.

| Requirement | Value |
|---|---|
| Folders | `120000/en`, `121000/en`, `122000/en`, `123000/en`, `124000/en`, `127000/en`, `128000/en` |
| Filename | Six-digit ID with the folder's first three digits, such as `120001.tex` |
| HD filename | The same ID with `_hr1`, such as `120001_hr1.tex` |
| Format | TEX with uncompressed BGRA pixels, format `0x1450` |
| Resolutions | Both normal and HD are required; HD width and height must each be twice the normal value |
| TEX layout | 80-byte header, one depth slice, one mip level, pixel data starting at byte 80 |

Do not put PNG previews or other files under `ui/icon/`. PackBuilder checks every file there and
stops for unsupported paths, invalid TEX data, or missing resolution pairs. The corresponding
original resources must exist in the game source used for the build.

PackBuilder copies valid TEX files into the same paths in the output folder. It does not convert
PNG files. The Tools repository's `fontgenerator/README.md` describes banner generation and TEX output.

Optional preview labels go in `dev/banner-previews.json` in the target repository:

```json
[
  { "Id": 120001, "Name": "Translated banner", "OriginalName": "Original banner" }
]
```

Use the normal texture ID. PackBuilder includes labels only for textures present in the build.

## ULD font sizes

Use one top-level JSON file per ULD in `layouts/`. Each definition selects text nodes and changes
their font size. Other ULD properties are not supported. Do not place modified ULD binaries there.

`layouts/castbar.json`:

```json
{
  "gamePath": "ui/uld/castbar.uld",
  "sourceSha256": "CA8DDAF29E2276F7476ABB0BBC7D3530D5FF075F58C2483C68B53A31F0353CAF",
  "nodes": [
    { "nodeId": 6, "textId": 1050, "expectedFontSize": 12, "fontSize": 10 }
  ]
}
```

The hash and node values must match the original ULD for your game source. Do not reuse this
example's hash for a different original file.

| Field | Meaning |
|---|---|
| `gamePath` | Lowercase game path under `ui/uld/`, ending in `.uld` |
| `sourceSha256` | SHA-256 of the complete original ULD bytes, 64 hexadecimal characters |
| `nodes` | Non-empty list of font-size changes |
| `componentId` | Optional component ID; omit it for a node in the root widget |
| `nodeId` | Nonzero text-node ID within the selected root or component |
| `textId` | Expected text ID stored in that ULD node |
| `expectedFontSize` | Original font size, from 1 to 255 |
| `fontSize` | Replacement font size, from 1 to 255, different from the original |

Each `gamePath` must be unique. Each node can appear only once within its root or component.
Unknown fields and duplicate JSON properties are rejected.

PackBuilder checks the original hash, node type, text ID, and font size. It changes only the
specified font-size bytes, parses the result again, and verifies the output. A mismatch stops the
build. After a game update, inspect changed originals before updating a definition or its hash.

To obtain a hash from an extracted original file in PowerShell:

```powershell
(Get-FileHash .\originals\ui\uld\castbar.uld -Algorithm SHA256).Hash
```

## Build and check

Set the game source, English corpus, target repository, and output folder in `tools.ini`.
With the standalone PackBuilder, run from the folder containing its executable and INI:

```powershell
.\PackBuilder.exe --language it --skip-coverage --skip-zip
```

This builds the local pack, including textures and ULD changes, without LocalizationKit or a ZIP.
The output contains translated textures under `ui/icon/` and generated layouts under `ui/uld/`.

Validate only the ULD definitions without writing a pack:

```powershell
.\PackBuilder.exe --language it --verify-layouts
```

Check ULD files in an existing built folder or ZIP against the definitions and original game source:

```powershell
.\PackBuilder.exe --language it --verify-pack-layouts C:\Gubal\Italian-test
```

For builds from exported game sheets, include the originals needed by the target's layout definitions.
With `game` pointing to the installed game's sqpack, export those ULD originals into a sheet export
of the same game version:

```powershell
.\PackBuilder.exe --language it --export-layouts C:\Gubal\game-sheets
```

Select the built folder as **Custom** in Gubal. Restart FFXIV to test changed textures and layouts.
