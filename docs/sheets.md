# Excel sheets

## File names

    exd/logmessage_0_en.exd
        │           │  └── language: en, fr, de, ja
        │           └───── the page's first row id; a sheet is split into pages
        └───────────────── sheet name, lowercased

## Addressing

`Sheet#row` ==> a row. `Sheet#row.column` ==> a cell, where a sheet has several text columns.
`quest/<cube>/<questId>#TEXT_…` ==> quest text.

A row id is the same in every language, which is what lets a translation be served by row id rather
than by matching text.

`Description#3604482.2` ==> row 3604482, **subrow** 2. `ExdPage.Parse` refuses subrow sheets.

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

    HowTo                       261 tutorial titles
    HowToPage                   the tutorial bodies. Columns 4, 5 and 6 are the mouse, keyboard and
                                gamepad variants; each row fills exactly one
    HowToCategory               the 16 tab headings
    DescriptionString           the guide a content window opens: Occult Crescent, Frontline,
                                mahjong, housing, New Game+
    Description                 those guides' window titles
    Addon, AddonTransient       the interface itself — buttons, window titles, «Requirements»
    EventItemHelp               help for event items
    GuidePageString             the job gauge guide
    ContentsTutorialPage        content briefings: Bozja, variant dungeons, Island Sanctuary
    EventTutorialPage           event and minigame tutorials
    MultipleHelpString          the multi-page help windows

In every help family the header sheet is structure and carries no text; the text is in the
`…Page`/`…String` sheet. `Tutorial`, `Guide`, `GuidePage`, `InstanceContentGuide`, `ScenarioTreeTips`
and `LoadingTips` have zero string columns. `LoadingTipsSub` has one and 251 rows, all empty.

## Finding things

A line's `gameKey` names its sheet and row, `Addon#1572`, so a search for the English text across
`corpus/` says where the game holds it. Text the corpus does not carry lives in a sheet nobody has
extracted; ask for it rather than guess.
