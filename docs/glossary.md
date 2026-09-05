# Glossary

Every language pack has a `glossary/` folder with two files. `overrides.json` holds the terms the
language has decided. `register.json` holds how each character speaks. This page describes both.

In the examples, `<code>` is the language code from the pack's `pack.json`: `es-ES`, `it`, `pt-BR`,
`pl`. It is the name of the column that holds the translation of a term.

The glossary wins over the corpus. A term is written as the glossary says, even where most of the
translated text says otherwise. A count over the corpus decides only the terms the glossary does
not have.

## overrides.json

Terms decided by hand. They come on top of `glossary/characters.json`, `places.json` and
`tribes.json` of this repository, which hold the names that exist as sheet rows. The file has two
arrays: `terms` and `emotes`. A term:

```json
{
  "en": "Temple Knights",
  "fr": "éclaireur des templiers",
  "ja": "捜索中の神殿騎士",
  "es-ES": "los Caballeros del Templo",
  "protect": false,
  "note": "The order, not any knight of it; the singular is a description and stays lower case."
}
```

| Field | |
|---|---|
| `en` | The English as the corpus writes it. Every check matches on this key. |
| `fr`, `ja` | The official renderings, copied from corpus lines. Empty when that language uses no fixed term. |
| `<code>` | The agreed rendering in this language. Used everywhere. |
| `protect` | `true`: keep the English as it is. `false`: translate it, and `<code>` is the result. |
| `forbidden` | Renderings the validator rejects. The plural is allowed: `["mercader"]`. |
| `note` | The decision and its traps. Not the history. |

An entry whose `en` starts with `_` is not a term: `_comment` holds the conventions of the file, and
`_divider` is a heading with no rule.

An emote has `en` and `<code>` only. Emotes are the stage directions the game shows as text,
`\<sigh>`.

## register.json

How each speaker addresses the player and, where it matters, other characters. The file is JSONC:
the rules of the language are `//` comments at the top. A key is the speaker token with only letters
and digits, in upper case: `Kan-E-Senna` is `KANESENNA`.

```jsonc
"ALFONSE": {
  "player": "tú",
  "others": { "GAIUS": "usted" },
  "confidence": "high",
  "why": "Werlyt: elder brother of the orphans. Formal, principled voice.",
  "voice": "Never contracts; addresses officers by rank.",
  "examples": ["quest/041/AktKba101_04102#TEXT_AKTKBA101_04102_ALFONSE_000_002"]
}
```

| Field | |
|---|---|
| `player` | How the speaker addresses the player. The values are the language's own forms. |
| `others` | The same for named characters, when it differs. |
| `confidence` | `high` and `medium` are measured against the corpus: the reason is in `why` and the rows in `examples`. `low` cites no rows and is followed until the scenes contradict it. |
| `why` | The reason for the entry. |
| `voice` | The speaker's tic: an accent, a catchphrase, a way to name themselves. |
| `examples` | Rows that show the register. |

## What is not here

A speaker's gender is a fact of the game and the same for every language. It lives in
`glossary/gender-overrides.json` of this repository, with the same speaker token as `register.json`.

## A new language

| File | Start |
|---|---|
| `overrides.json` | The `protect: true` terms of an existing pack, with `en`, `fr`, `ja` and `protect` only. They say what stays English. |
| `register.json` | The `//` rules written for the language, and an empty `register`. |
