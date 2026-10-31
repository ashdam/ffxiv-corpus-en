# Shared configuration

## extraction.json

Defines the sheets and text columns that CorpusExtractor reads from the game.
PackBuilder and CorpusValidator use the same definition. Do not copy it into language packs.

```json
{
  "Addon": [0],
  "ClassJob": [0, 1, 30]
}
```

Each key is a sheet name. Its value lists the approved text column indexes, starting at zero.
Keep identifiers, lookup fields and file paths outside this definition.

## gender-overrides.json

Manual gender corrections shared by all language packs. CorpusExtractor applies them to source
rows. CorpusValidator checks their format and the resulting source values.

```json
{
  "characterOverride": {
    "MOGZIN": {"gender":"male","why":"French dialogue uses seul for Mogzin."}
  },
  "textOverride": {
    "BNpcName#10029": {"gender":"female","why":"French uses soldate."},
    "ENpcResident#1020816": {"gender":"female","why":"French uses artisane vira."},
    "quest/002/SubWil032_00224#TEXT_SUBWIL032_00224_KIKIPU_003": {"gender":"unknown","why":"The letter addresses Kikipu. It does not identify the writer."}
  }
}
```

| Field | Meaning |
|---|---|
| `characterOverride` | Speaker gender corrections keyed by normalized character name, uppercase letters and digits only. |
| `textOverride` | NPC gender corrections keyed by sheet row, or speaker gender corrections keyed by exact dialogue `gameKey`. |
| `gender` | `male`, `female`, `unknown` or `n/a`. |
| `why` | Required text or game-data evidence for the correction. |

`BNpcName#<row>` and `ENpcResident#<row>` correct `gender` on every extracted column of that row.
Do not include a column suffix. Other keys correct `speakerGender` only when `speakerKind` is `character`.
A dialogue correction takes precedence over the character correction and automatic speaker resolution.
These values identify the character's gender, not the grammatical gender of a translated noun.

`unknown` removes an unsupported inference. `n/a` means gender does not apply.
Removing an override permits automatic resolution to assign a gender again.
Keep one correction per line. Use the source text and context as evidence; do not infer the speaker from an addressee.

## excluded-rows.json

Source exclusions shared by all language packs.

### Structure

```json
{
  "exclusion_groups": [
    {
      "why": "Development placeholder.",
      "rows": [
        {"gameKey":"NotebookDivision#40","hash":"CDBD155512EF77BD"}
      ]
    }
  ]
}
```

Each group requires `why` and `rows`. Each row requires its exact source `gameKey` and `hash`.
Copy the hash from the corpus. An exclusion applies only while its hash matches the current source row.
`_comment` can state file-level restrictions.

### How to add an exclusion

1. Confirm that the row must be excluded for every language pack. Use pack exclusions for a pack-specific decision.
2. Copy the exact `gameKey` and current `hash` from the source corpus into a group's `rows`.
3. State the reason in the group's `why`. Group rows only when the same reason applies.
4. Check JSON syntax and duplicate rows. Do not invent a hash or retain an exclusion after its source changes without review.
