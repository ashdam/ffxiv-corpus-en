# ffxiv-corpus-en

The English text of Final Fantasy XIV, one row per line the game shows, with the game's own French
and Japanese beside it. Extracted from the game's Excel sheets and regenerated after every patch.

Every language pack mirrors this layout so a translation of a row lives at the same path, under the
same `gameKey`, in `ffxiv-language-pack-<language>/corpus/`.

## Folder layout

```
corpus/
  quest/<bucket>/<questId>.json         one file per quest
  cut_scene/<bucket>/VoiceMan_<id>.json one file per voiced cutscene
  custom/<bucket>/<name>_<id>.json      the "Ask about..." menus and other scripted talk
  flat/<Sheet>.json                     one file per flat sheet: DefaultTalk, NpcYell, Addon, Item...
  warp/ transport/ raid/ content/ dungeon/ opening/ shop/ leve/ guild_order/ story/ system/
                                        small keyed families, one file per sheet
glossary/characters.json               every named NPC, en/fr/ja, from the game's own sheets
glossary/places.json                   every place name, en/fr/ja
glossary/tribes.json                   the beast tribes, en/fr/ja
glossary/gender-overrides.json         speaker genders the NPC sheets get wrong; an input to the extraction
versions/<gameVersion>.json             what each patch added, rewrote and retired
docs/                                   macros, sheets and the glossary files of a language pack
```
## File structure example

`corpus/flat/DefaultTalk.json`, one entry:

```json
{
  "schemaVersion": 2,
  "sourceLanguage": "en",
  "gameVersion": "2026.08.11.0000.0000",
  "conversation": "DefaultTalk",
  "entries": [
    {
      "gameKey": "DefaultTalk#590415.20",
      "en": "I'm the hired captain of the Orion. She's a fine vessel, true, but a hired captain can love his ship only as much as a paid bride can love her betrothed.",
      "hash": "E38048F1B03D2DD9",
      "macro": "I'm the hired captain of the <italic(1)>Orion<italic(0)>. She's a fine vessel, true, but a hired captain can love his ship only as much as a paid bride can love her betrothed.",
      "unresolved": false,
      "fr": "Salut moussaillon. Je suis Ahldskyf, le capitaine de l'Orion. Cela fait maintenant dix ans que je suis dans le métier, ce qui fait de moi un vrai loup de mer.",
      "ja": "俺は、外洋交易船「オライオン号」の船長アルドスキフ。\r\n雇われ船長を続けて、早うんじゅうねん……\r\nもう雇われ船長としては、ベテランといえるな。",
      "speaker": "Ahldskyf",
      "speakerKind": "character",
      "speakerGender": "male",
      "kind": "ambient"
    }
  ]
}
```

| Field | |
|---|---|
| `gameKey` | The sheet row: conversation path, `#`, row label. The key every translation is joined on. |
| `en`, `fr`, `ja` | The line as the game ships it in each language, macros stripped. |
| `macro`, `frMacro`, `jaMacro` | The same line with its game macros: `<italic(1)>`, `<if(...)>`, `<br>`. Present only when the line has any. The text to translate is `macro` when it exists, with every macro copied verbatim. |
| `hash` | Fingerprint of `gameKey` and the English. A translation carries it, so a later patch can tell whether the line it was written against is still the line the game ships. |
| `unresolved` | `true` when a macro needs a value only the game calculates, such as the player's name. |
| `speaker`, `speakerKind`, `speakerGender` | Who says it. See below. |
| `kind` | What the row is. See below. |
| `questId`, `questName`, `section`, `category` | On quest rows: where the line sits in the journal. |

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

`glossary/gender-overrides.json` corrects the sheet where the game's own dialogue contradicts it:

```json
{ "overrides": { "STARKWOAD": { "gender": "female", "why": "the lines, in two languages, that show it" } } }
```

The key is the speaker token with only letters and digits, upper case. The value is `male`, `female`
or `unknown`, which forces neutral phrasing. An entry needs the game text quoted in at least two
languages in `why`.

## Row kinds

| `kind` | |
|---|---|
| `dialogue` | Spoken in the Talk window or a cutscene. Quest and scripted files. |
| `ambient` | Everything from a flat sheet: NPC chatter, balloons, interface text, item names. |
| `seq` | Journal text for a quest step. Never in the Talk window. |
| `todo` | A quest objective line. Never in the Talk window. |
| `system` | A system message inside a quest script. |

## Proper nouns

`glossary/characters.json`, `glossary/places.json` and `glossary/tribes.json` have the same shape.
Generated from the game's own sheets, so they are regenerated with the corpus and never edited.

```json
{
  "sourceLanguage": "en",
  "gameVersion": "2026.08.11.0000.0000",
  "terms": [
    { "en": "A'aba Tia", "fr": "A'aba Tia", "ja": "ア・アバ・ティア", "protect": true, "note": "Flame Private" },
    { "en": "A Faraway Place", "fr": "Ville lointaine", "ja": "とある街", "protect": false }
  ]
}
```

| Field | |
|---|---|
| `en`, `fr`, `ja` | The name as the game ships it in each language. `fr` or `ja` is absent when that language has no row. |
| `protect` | Whether the official French translation kept the name as it is in English. `true`: French left it unchanged, so it is a real name. `false`: French translated it, so it is a description that reads as a name, and a translation probably should translate it too. Only French is compared; Japanese rewrites every name. |
| `note` | Characters only, when the sheet has it: the title shown under the name, `Flame Private`, `Merchant & Mender`. |

One entry per English name across the three files. `characters.json` comes from the NPC sheet,
`places.json` from the place-name sheet, `tribes.json` from the beast-tribe sheet.

## A patch

Every file records the game version it was extracted from. After a patch the whole corpus is
regenerated; rows the game dropped disappear, rewritten rows get a new `hash`, and
`versions/<gameVersion>.json` lists all three.
