# Excel sheets

## File names

    exd/logmessage_0_en.exd
        │           │  └── language: en, fr, de, ja
        │           └───── the first row id of the page; a sheet is split into pages
        └───────────────── sheet name, in lower case

## Addressing

`Sheet#row` is a row. `Sheet#row.column` is a cell, for a sheet with several text columns.
`quest/<bucket>/<questId>#TEXT_…` is quest text.

A row id is the same in every language. This is what lets a translation be served by row id and
not by matching text.

`Description#3604482.2` is row 3604482, subrow 2. Subrow sheets are not supported.

## Dialogue and ambient

    DefaultTalk                 NPC chatter outside a quest
    NpcYell                     shouts and speech balloons over an NPC
    Balloon                     balloon text
    InstanceContentTextData     dungeon, trial and raid dialogue
    PublicContentTextData       open-world content
    ContentTalk, GimmickTalk    duty NPC talk; object and gimmick text
    LogMessage                  the combat log, duty seals, system notices, party and market messages
    CustomTalk                  the verbs in an NPC's first menu, not the dialogue behind them
    Quest                       quest titles. Column 0; the other 51 string columns are script ids

## Help and interface

    HowTo                       tutorial titles
    HowToPage                   the tutorial bodies. Columns 4, 5 and 6 are the mouse, keyboard and
                                gamepad variants; each row fills one of them
    HowToCategory               the tab headings
    DescriptionString           the guide a content window opens: Occult Crescent, Frontline,
                                mahjong, housing, New Game+
    Description                 the window titles of those guides
    Addon, AddonTransient       the interface: buttons, window titles, «Requirements»
    EventItemHelp               help for event items
    GuidePageString             the job gauge guide
    ContentsTutorialPage        content briefings: Bozja, variant dungeons, Island Sanctuary
    EventTutorialPage           event and minigame tutorials
    MultipleHelpString          the multi-page help windows

In every help family, the header sheet is structure and has no text. The text is in the
`…Page` or `…String` sheet.

## Finding things

A line's `gameKey` names its sheet and row, `Addon#1572`. A search for the English text across
`corpus/` says where the game holds it. Text the corpus does not carry lives in a sheet nobody has
extracted; ask for it rather than guess.
