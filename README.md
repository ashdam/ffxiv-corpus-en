# ffxiv-corpus-en

The English text of Final Fantasy XIV, one row per line the game shows, with the game's own French,
Japanese and German beside it. Extracted from the game's Excel sheets and regenerated after every patch.

Every language pack mirrors this layout so a translation of a row lives at the same path, under the
same `gameKey`, in `ffxiv-language-pack-<language>/corpus/`.

`config/extraction.json` defines the sheets and text columns to extract.
It is shared configuration, not a translation file; do not copy it into language packs.

## Folder layout

```
corpus/
  quest/<bucket>/<questId>.json         one file per quest
  cut_scene/<bucket>/VoiceMan_<id>.json one file per voiced cutscene
  custom/<bucket>/<name>_<id>.json      the "Ask about..." menus and other scripted talk
  flat/<Sheet>.json                     one file per flat sheet: DefaultTalk, NpcYell, Addon, Item, PlaceName...
  ui/banners.json                      English banner labels and original TEX hashes, one entry per line
  warp/ transport/ raid/ content/ dungeon/ opening/ shop/ leve/ guild_order/ story/ system/
                                        small keyed families, one file per sheet
metadata/proper-names-auto.json         proper names extracted automatically from the game
metadata/proper-names-manual.json       reviewed proper names absent from the automatic catalog
metadata/peoples-manual.json           additional peoples, races, tribes and clans
config/gender-overrides.json          character, NPC-row and dialogue-row gender overrides
config/excluded-rows.json             reviewed rows omitted from every language corpus
versions/<gameVersion>.json             what each patch added, rewrote and retired
docs/                                   sheets, glossaries, and language-pack assets
```

Language packs can also contain translated textures and interface layout changes. See
[Banner translations and UI layouts](docs/banner-translations-and-ui-layouts.md) for paths, formats, and PackBuilder commands.

See [Shared configuration](config/config.md) for extraction settings, gender overrides and source exclusions.
See [Shared metadata](metadata/metadata.md) for common game data. Language-specific decisions belong in each pack.

## File structure example

`corpus/flat/DefaultTalk.json`, one entry:

```json
{
  "schemaVersion": 3,
  "sourceLanguage": "en",
  "gameVersion": "2026.10.06.0000.0000",
  "conversation": "DefaultTalk",
  "entries": [
    {
      "gameKey": "DefaultTalk#590415.20",
      "en": "I'm the hired captain of the <italic(1)>Orion<italic(0)>. She's a fine vessel, true, but a hired captain can love his ship only as much as a paid bride can love her betrothed.",
      "hash": "E38048F1B03D2DD9",
      "unresolved": false,
      "fr": "Salut moussaillon. Je suis Ahldskyf, le capitaine de l'Orion. Cela fait maintenant dix ans que je suis dans le métier, ce qui fait de moi un vrai loup de mer.",
      "ja": "俺は、外洋交易船「オライオン号」の船長アルドスキフ。<br>雇われ船長を続けて、早うんじゅうねん……<br>もう雇われ船長としては、ベテランといえるな。",
      "de": "Schiff ahoi, Landratte. Ich bin Ahldskyf, Kapitän des Handelsschiffs <italic(1)>Orion<italic(0)>. Tja, Kapitän darf ich mich nennen, aber leider gehört der Kahn nicht mir sondern der Handelsgesellschaft, für die ich arbeite.",
      "conversation": "DefaultTalk#590415",
      "seq": 20,
      "speaker": "Ahldskyf",
      "speakerKind": "character",
      "speakerGender": "male",
      "kind": "ambient",
      "sheet": "DefaultTalk"
    }
  ]
}
```

| Field | |
|---|---|
| `gameKey` | The sheet row: conversation path, `#`, row label. The key every translation is joined on. |
| `en`, `fr`, `ja`, `de` | Effective text with all SeString markup and all branches preserved. Missing references are omitted. |
| `hash` | Text rows: fingerprint of `gameKey` and English. Banner rows: fingerprint of the original normal and HD TEX files. Translations carry the source hash to detect changed originals. |
| `unresolved` | `true` when the English text uses event-local numeric parameters such as `lnum1`. Preserve all markup and branches. |
| `speaker`, `speakerKind`, `speakerGender` | Who says it. See below. |
| `gender` | Resolved gender for `ENpcResident` and `BNpcName`: `male`, `female`, `unknown` or `n/a`. |
| `kind` | What the row is. See below. |
| `questId`, `questName`, `section`, `category` | On quest rows: where the line sits in the journal. |

Use [Dalamud SeString documentation](https://dalamud.dev/plugin-development/sestring/) for `<...>` markup.
Keep player commands such as `/nombre_comando` unchanged. Consult French, Japanese and German
for gender, number and form of address. Use context and the target glossary; noun gender can differ
between languages. Never evaluate or flatten a reference to store it.

## Speakers

`speakerKind` says what the row's speaker token is. Only `character` rows carry `speaker` and
`speakerGender`.

| `speakerKind` | |
|---|---|
| `character` | A person or creature. `speaker` names it. |
| `prompt` | A dialogue choice or answer slot: the token is `Q1`, `A3`, `CHOICE1Q1`, `YES`, `NO`. A menu entry, not a voice. |
| `system` | A structural row: the token is `SCENE00001`, `POPMESSAGE`, `SYSTEM`, `SEQ`, `TODO`. |
| `unknown` | The row has no speaker token. Most flat sheets: item names, interface text, journal entries. |

`speaker` takes one of two shapes:

- **The name the player sees**, when the game text carries it: `Ahldskyf`, `Bakool Ja Ja the Mighty`,
  `Winebaud's Message`, or `???` for a speaker the game hides on purpose. Flat sheets and labelled
  quest lines.
- **The token of the text key**, upper case, when it does not: `TLAQATIA`, `RESIDENTOFFICER`,
  `KEEPEROFTHEENTWINEDSERPENTS`. Most quest lines. The same character can appear both ways.

`speakerGender` is `male`, `female` or `unknown`, from the game's NPC sheets. Grammatical agreement
in a translation depends on it; the English text alone cannot tell.

`gender` uses linked humanoid model data and the [shared gender overrides](config/config.md#gender-overridesjson).

## Row kinds

| `kind` | |
|---|---|
| `dialogue` | Spoken in the Talk window or a cutscene. Quest and scripted files. |
| `ambient` | Everything from a flat sheet: NPC chatter, balloons, interface text, item names. |
| `seq` | Journal text for a quest step. Never in the Talk window. |
| `todo` | A quest objective line. Never in the Talk window. |
| `system` | A system message inside a quest script. |

## A patch

Every file records the game version it was extracted from. After a patch the whole corpus is
regenerated; rows the game dropped disappear, rewritten rows get a new `hash`, and
`versions/<gameVersion>.json` lists all three.
