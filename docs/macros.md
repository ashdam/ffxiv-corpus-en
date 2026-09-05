# Macros

What the game inserts into a line, and what a translation must keep. The examples are Spanish; the
rules are the same for every language.

## Names the game inserts

`<ennoun(Sheet,article,id,count,1)>` inserts a name from `Sheet`, with an article in the client's
language, which is English. The second parameter selects the article. Always write type 3.

    <ennoun(Item,1,lnum1,1,1)>   ==>  a cobalt ingot        never
    <ennoun(Item,2,lnum1,1,1)>   ==>  the cobalt ingot      never
    <ennoun(Item,3,lnum1,1,1)>   ==>  cobalt ingot          this one

The inserted name is always English. Whether the translation adds an article depends on the sheet:

    Item, EventItem     common nouns  ==>  write the masculine article where the sentence needs one
    all other sheets    proper names  ==>  no article

    «Vendes un <ennoun(Item,3,…)> por 7 gil.»       ==>  Vendes un aurum regis ingot por 7 gil.
    «Das a <ennoun(BNpcName,3,…)> la orden “X”.»    ==>  Das a Carbuncle la orden “Aullido”.
    «<head(<ennoun(BNpcName,3,…)>)> se retira.»     ==>  Carbuncle se retira.

`<frnoun>`, `<denoun>` and `<janoun>` are the same macro in the other languages. They cannot be used:
an English client evaluates `ennoun`, and the gender data lives in each language's own sheets. Use
the French only as a model for the phrasing.

`<sheet(Sheet,id,col)>` inserts the text at that row and column. The ids are data: a different
number points at a different thing.

    <sheet(PetAction,lnum2,0)>   ==>  Aullido

`<string(gstr1)>` is the player's full name. `<split(<string(gstr1)>, ,1)>` is the first name and
`,2)` the surname. `<pcname(lnum3)>` is a named player.

`<num(lnum1)>` is a number. `<kilo(lnum1,\,)>` is a number with thousands separators.

## Capitalisation

`<head(...)>` capitalises the first letter of what it wraps. `<headall(...)>` capitalises every word.
On a possessive, `<head>` can land mid-sentence and capitalise an article, «de La Zona». Keep it: a
dropped macro is a violation.

## Branches

`<if([cond],A,B)>` selects A or B. `<switch(n,a,b,c)>` selects the nth branch by position. Both hold
prose and both are translated. So does `<ifself>`, below. No other macro holds prose.

    <if([lnum2==1],punto,puntos)>          number agreement
    <if(gnum4,Bienvenida,Bienvenido)>      the player's gender
    <if([gnum11<12],mañana,tarde)>         the in-game hour

In a `<switch>`, the count and the order of the branches carry the meaning. A dropped branch moves
every later one to the wrong value. Translate the words, keep the commas.

The comma is the separator, so a comma inside a branch must be escaped as `\,`. A translation that
adds a comma changes the branch count, and the game then serves the wrong branch for every value
after it, with no error.

    <switch(gnum70,Es usted quien preparó este festín\, ¿no es así?,…)>

Escape the comma rather than rephrase around it. The validator's `switchAltered` check confirms the
count.

Do not assume the player's gender outside a conditional. Where English is neutral and the language
needs agreement, add `<if(gnum4,…)>`. The French shows how, and puts the article inside the branch.

    fr  Encore <if(gnum4,une aventurière,un aventurier)>?
    es  ¿Otra <if(gnum4,aventurera,aventurero)>?

Time-of-day branches do not map one to one. English splits morning, day and evening; a language
may need one word for two of them. Keep the structure, change the words.

A branch may be empty: `<if([lnum3==0],,\(<num(lnum3)>%\))>` exists in the English. This is also how
a translation drops a word in one branch.

A condition repeated inside its own else branch is dead: `<if([A],x,<if([A],y,z)>)>` never reaches
`y`. Translate it anyway, it is a macro, but nothing that matters may live there.

`<if>` renders to nothing in the `en` column. A row whose whole sentence sits inside one shows as
`"  ."`. Read `macro`.

Only two `<if>` may be added: the player's gender, and a number agreement that mirrors one the
English already has. An `<if>` invented to inflect a verb is rejected. Fold the person into the
branch the line already has:

    <head(<if([gstr1==gstr2],No puedes llevar,<X> no puede llevar)>)>

## Who is who in a log line

`gstr1` is the player reading the log. `gstr2` is whoever acts. `gstr3` is whoever it is done to.
So `[gstr1==gstr2]` means «you do it» and `[gstr1==gstr3]` means «it is done to you».

    <if([gstr1==gstr2],te ríes,se ríe)>       second person when you are the one acting
    <if([gstr1==gstr3],ti,<…gstr3…>)>         «se ríe de ti» when you are the one it lands on

The English possessive is an `'s` outside the inner branch. Split the phrase across both branches,
or drop the possessive in the player's branch: «de tus HP» already says whose they are.

    en  <if([gstr1==gstr2],Your,<if(…)>'s)> attack
    es  <if([gstr1==gstr2],tu ataque,el ataque de <if(…)>)>

    en  <head(<if([gstr1==gstr2],Your,<if(…)>'s)>)> <string(lstr1)> restores … of your HP.
    es  <head(<string(lstr1)>)><if([gstr1==gstr2],, de <if(…)>)> restaura … de tus HP.

## Formatting

| Macro | |
|---|---|
| `<br>` | Line break. |
| `<nbsp>` | Non-breaking space. Present in some English rows; keep it. |
| `<italic(1)>…<italic(0)>` | Italics. |
| `<icon(n)>`, `<icon2(n)>` | A button glyph. |
| `<switchplatform(n)>` | Branches on the input device. |
| `<key>` | Waits for a key press. It separates the pages of one duty line. |
| `<colortype(n)><edgecolortype(n)>…<edgecolortype(0)><colortype(0)>` | Highlight. A wrapped name is an interface label and stays English. Some rows ship a malformed wrapper: reproduce it as it is. |
| `<color(n)><edgecolor(n)>…<edgecolor(stackcolor)><color(stackcolor)>` | The same highlight with a literal colour. |
| `<edge(n)>`, `<shadow(n)>`, `<bold(1)>` | The rest of the family. |

## Macros that hold no prose

None of these is translated. Copy them as they are.

    <lower(x)> <caps(x)>         case, the mirror of <head>
    <ordinal(n)>                 «1st», «2nd»
    <digit(n,2)> <float(n,10,.)> zero-padded and decimal numbers
    <settime(n)> <sec(x)>        a timestamp and its fields: t_hour, t_min, t_year
    <setresettime(h,d)>          the weekly reset, read by the <switch(t_wday,…)> after it
    <sound(1,96)> <levelpos(n)>  a sound cue and a map flag
    <sheetsub(A,r,c,n,B,m)>      <sheet> for a sheet whose row points into another one
    <fixed(6,618)> <lowerhead(x)>  rare; nothing to write in either

`<ifself(n,A,B)>` is «you» against a named player, in the combat log:
`<ifself(lnum1,You are,<head(<pcname(lnum1)>)> is)> picked up by the kraken`. It branches like
`<if>` and its branches hold prose, so it is translated like `<if>`.

## Event-local parameters

`lnum1`, `lnum2`… have a value only inside the event that sets them. Rows that use them are marked
`unresolved`, and their `en` column has holes: « will be sealed off in  !». Translate those rows
from `macro`, never from `en`. `fr` and `ja` are whole and say what the sentence means.

`unresolved` does not mean "do not translate". The pack serves rows by id, so these rows ship.

## Not macros

`\<sigh>` is a stage direction. The backslash makes the game print the angle brackets, so it is text
in the line, not a macro.

    en  \<sigh> Hopefully things will be better this time...
    es  \<suspiro> Espero que esta vez las cosas vayan mejor...

Written without the backslash, the validator reads an unclosed macro, reports `macroInvented`, and
the line is rejected. Do not replace one with prose («Ay...»): the player knows the convention.

`<-->` and `<->` are a non-breaking and a soft hyphen. The validator does not see them, because its
macro test needs a letter after `<`. Keep them where the English has them, `Radz<-->at<-->Han`, and
drop them when the compound they join disappears in translation.

## Traps

- U+3000 inside a macro is invisible and fatal: `<ennoun(Item,3,　28,2,1)>`.
- Conditions use `==`. One source row has a stray space; reproduce it.
- Do not copy the French `<nbsp>` before `?` and `!`. That is French typography. An `<nbsp>` inside
  an English macro is a different thing and stays.
- A macro in the Japanese or French but not in the English is not yours to import. If a row has no
  `macro` field, translate the plain `en` and do not adopt the `frMacro`.
