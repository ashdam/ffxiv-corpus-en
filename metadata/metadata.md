# Shared metadata

This directory contains game facts used by all language packs. Translations and language-specific term decisions belong in each pack.
All files use UTF-8 JSON. Keep each manually maintained entry on one line.

| File | Content | Maintenance | Consumer |
|---|---|---|---|
| `proper-names-auto.json` | Proper names extracted automatically from the game. | Generated | CorpusValidator, RegisterMap. |
| `proper-names-manual.json` | Manually reviewed proper names not included in `proper-names-auto.json`. | Manual | CorpusValidator, RegisterMap. |
| `peoples-manual.json` | Additional names of peoples, races, tribes and clans. | Manual | CorpusValidator. |

## proper-names-auto.json

Proper names extracted automatically from the game. Includes people and other named beings,
such as the primal Shiva. CorpusValidator accepts complete names as spelling exceptions.
RegisterMap uses names to identify addressees. These names do not set pack translations.

### Structure

```json
{
  "names": [
    "2B",
    "Shiva",
    "Y'shtola"
  ]
}
```

`names` contains strings. Names must be unique, ignoring case, and must not occur in
`proper-names-manual.json`. Separate words and parts are not spelling exceptions.

### How to add a name

Do not edit this generated file. Review missing proper names and add them to
`proper-names-manual.json`.

## proper-names-manual.json

Manually reviewed names of individual people and other named beings that are missing from
`proper-names-auto.json`. CorpusValidator accepts complete English names as spelling exceptions
unless the pack glossary defines the term. RegisterMap uses names to identify addressees.

### Structure

```json
{
  "names": [
    {"en":"Athena","fr":"Athéna","de":"Athena","ja":"アテナ"}
  ]
}
```

`en` is required. Optional `fr`, `de` and `ja` fields contain official forms for review.
Names must be unique across both proper-name catalogs, ignoring case.
Separate words and parts are not spelling exceptions.

### How to add a name

1. Confirm that the source names an individual person or being. Read the full row and its context.
   Do not add places, peoples, standalone titles or common words.
2. Search `metadata/proper-names-auto.json` (`names[]`, generated from `ENpcResident` and `BNpcName`)
   and `metadata/proper-names-manual.json` (`names[].en`), ignoring case.
   Add a manual entry only if the complete English name is absent from both files.
3. Add an object under `names`. Copy the complete English name into `en`. Add official `fr`, `de`
   and `ja` forms when available. Omit forms you cannot verify. Do not split the name.
4. Keep one entry per line, sorted by `en`. Put pack translations and language-specific forms
   in the pack's glossary. Do not add notes or review history here.
5. Check JSON syntax and duplicates. Check the affected sentence again with the grammar validator.
   If a valid name still receives a spelling warning, check dictionary loading and the exact
   spelling before adding another exception. Use validator reviews for sentence-specific false positives.

## peoples-manual.json

Names of peoples, races, tribes and clans not present in the `BeastTribe`, `Race` or `Tribe`
name columns. CorpusValidator reads those sheets directly from the source corpus and the pack.
It accepts complete English names from this manual catalog as spelling exceptions unless
the pack glossary defines the term.

### Structure

```json
{
  "terms": [
    {"en":"Gundu","ja":"グンド"}
  ]
}
```

`en` is required. Optional `fr`, `de` and `ja` fields contain official forms for review.
Names must be unique, ignoring case, and must not repeat names from those sheets.
Separate words in a name are not spelling exceptions.

### How to add a name

1. Confirm that the source names a people, race, tribe or clan. Read the full row and its context.
   Do not add individuals, places, standalone titles or common words.
2. Search this file and the name columns in `BeastTribe`, `Race` and `Tribe`, ignoring case.
   Do not add a name already present in any of them.
3. Add an object under `terms`. Copy the complete English name into `en`. Add official `fr`, `de`
   and `ja` forms when available. Omit forms you cannot verify. Do not split the name.
4. Keep one entry per line, sorted by `en`. Put pack translations and language-specific forms
   in the pack's glossary. Do not add notes or review history here.
5. Check JSON syntax and duplicates. Check the affected sentence again with the grammar validator.
   If a valid name still receives a spelling warning, check dictionary loading and the exact
   spelling before adding another exception. Use validator reviews for sentence-specific false positives.
