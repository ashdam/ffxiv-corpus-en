# Inventory exclusions

Each language pack stores its rules in `inventory-exclusions.json` at the repository root. All packs
use the same format. These rules define the scope of its localization inventory.

```json
{
  "excludedSheets": ["Item"],
  "exclusion_groups": [
    {
      "why": "Keep this action name in English.",
      "rows": [{"gameKey": "Action#1", "hash": "6FA9716C3A7C71FD"}]
    }
  ]
}
```

| Field | Rule |
|---|---|
| `excludedSheets` | Required array of exact source sheet names. Use `[]` for no whole-sheet exclusions. |
| `exclusion_groups` | Required array of row groups. Use `[]` for no row exclusions. |
| `why` | Required, non-empty reason for the group. |
| `rows` | Required array of source rows. |
| `gameKey` | Exact source key. A key can appear in only one group. |
| `hash` | Current 16-character hexadecimal source hash. Copy it from the English corpus. |
| `_comment` | Optional format reference or file instructions. |

A sheet exclusion removes all its rows from the localization inventory. A row exclusion removes
only the named row when its source hash matches. Sheet exclusions take precedence over row rules.
To keep selected rows in scope, list the excluded rows and leave their sheet out of `excludedSheets`.

When a row exclusion's source hash changes, that row rule no longer applies and validation fails.
Review the source before updating the hash or removing the rule. Unknown sheets, missing rows and
duplicate keys fail validation.

An absent file means there are no pack-specific inventory exclusions. These rules affect the `Localized` percentage. They do not remove existing translations from the
generated language pack.

`WIP & future localization` reports pending percentages for each sheet affected by these rules. Its
counts include all source rows with translatable English in that sheet, including rows kept in scope.
Existing translations reduce the pending percentage. Blank text, digit-only text, placeholders and
Japanese text do not count. Categories with no pending text are omitted from the Gubal column.

## Publication

Edit and commit this file through the language pack's pull request process. A change to it triggers
a release after the pull request is merged. The build calculates coverage and includes the results
in `coverage.json` and `gubal-manifest.json`. Gubal reads the published manifest.

The source corpus has separate extraction exclusions in `glossary/excluded-rows.json`. Those apply
to every language and remove rows from the source corpus. Pack inventory exclusions are specific to
one language and keep the rows in its corpus.
