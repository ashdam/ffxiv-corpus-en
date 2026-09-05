# Macros

Reference: what the game inserts into a line, and what a translation has to keep.

## Names the game inserts

**`<ennoun(Sheet,article,id,count,1)>`** ==> a name from `Sheet`, **plus an article in the client's
language, which is English**. The second parameter chooses the article. **Always write type 3.**

    <ennoun(Item,1,lnum1,1,1)>   ==>  a cobalt ingot        never
    <ennoun(Item,2,lnum1,1,1)>   ==>  the cobalt ingot      never
    <ennoun(Item,3,lnum1,1,1)>   ==>  cobalt ingot          this one

The inserted name is always English. **Whether you then write an article depends on the sheet:**

    Item, EventItem     common nouns  ==>  write the masculine article where the sentence wants one
    everything else     proper names  ==>  no article, ever

    «Vendes un <ennoun(Item,3,…)> por 7 gil.»       ==>  Vendes un aurum regis ingot por 7 gil.
    «Das a <ennoun(BNpcName,3,…)> la orden “X”.»    ==>  Das a Carbuncle la orden “Aullido”.
    «<head(<ennoun(BNpcName,3,…)>)> se retira.»     ==>  Carbuncle se retira.

«al Carbuncle» and «del Carbuncle» are wrong for the same reason «al Juan» is. And the masculine is
safe on `Item` only because the noun that lands there is English: `un aurum regis ingot`.

French has ten article types (`<frnoun>`, including «au»/«du» contractions) against English's three.
**Unusable**: an English client evaluates `ennoun`, and the gender data lives in each language's own
sheets. Use the French only as a model for phrasing.

**`<sheet(Sheet,id,col)>`** ==> the text at that row and column, verbatim. Ids are data: changing a
number points at a different thing.

    <sheet(PetAction,lnum2,0)>   ==>  Aullido

**`<string(gstr1)>`** ==> the player's full name. **`<split(<string(gstr1)>, ,1)>`** ==> first name,
`,2)` ==> surname. **`<pcname(lnum3)>`** ==> a named player.

**`<num(lnum1)>`** ==> a number. **`<kilo(lnum1,\,)>`** ==> a number with thousands separators.

## Capitalisation

**`<head(...)>`** ==> capitalises the first letter of what it wraps. **`<headall(...)>`** ==> every
word. On a possessive `<head>` lands mid-sentence and capitalises an article — «de La Zona». Leave
it; dropping a macro is a violation.

## Branches

**`<if([cond],A,B)>`** ==> picks A or B. **`<switch(n,a,b,c)>`** ==> picks the nth branch **by
position**. **Both hold prose and both are translated.** So does `<ifself>`, below; those three are
the only macros in the game that do.

    <if([lnum2==1],punto,puntos)>          number agreement
    <if(gnum4,Bienvenida,Bienvenido)>      the player's gender
    <if([gnum11<12],mañana,tarde)>         the in-game hour

**In a `<switch>` the count and the order are load-bearing.** Drop a branch and every later one lands
on the wrong thing — the wrong Grand Company, the wrong reward. Translate the words, keep the commas.

**THE COMMA IS THE SEPARATOR, SO A COMMA INSIDE A BRANCH MUST BE ESCAPED `\,`.** This is the trap,
because Spanish reaches for a comma where English does not, so the branch count only breaks on
translation. On 18 August 2026 a chamberlain's eight-branch line was delivered as ten — the Spanish
was correct prose and two branches simply held an internal comma — and a switch whose count has
shifted serves the wrong branch for every value after it, silently.

    <switch(gnum70,Es usted quien preparó este festín\, ¿no es así?,…)>

565 rows already do this. Escaping is better than rephrasing around the comma: it keeps the Spanish
you wanted, and the validator's `switchAltered` confirms the count is intact.

**Never assume the player's gender outside a conditional.** Where English is neutral and Spanish
forces agreement, add `<if(gnum4,…)>`; French shows how, and pulls the article inside the branch.

    en  Encore <if(gnum4,une aventurière,un aventurier)>?     (fr)
    es  ¿Otra <if(gnum4,aventurera,aventurero)>?

**Time-of-day branches do not map one to one.** English splits morning/day/evening; Spanish needs
«noches» for both the small hours and the late evening. Keep the structure, change the words.

**A branch may be empty.** `<if([lnum3==0],,\(<num(lnum3)>%\))>` is in the English. It is also how
Spanish drops a word in one branch without inventing a macro to hold the other.

**A condition repeated inside its own else branch is dead.** `<if([A],x,<if([A],y,z)>)>` can never
reach `y`, and the log is full of it. Translate it anyway — it is a macro — but nothing that matters
may live there.

**`<if>` renders to nothing in the `en` column.** A row whose whole sentence sits inside one comes
back as `"  ."`: `LogMessage#4321`, the message after every desynthesis. Read `macro`.

**Two `<if>` you may add, and only these two:** the player's gender, and a number agreement that
mirrors one the English already has. **A second `<if>` invented to inflect a verb is fatal** — the
merge dropped `LogMessage#4327` for it. Fold the person into the branch the line already has:

    <head(<if([gstr1==gstr2],No puedes llevar,<X> no puede llevar)>)>

## Who is who in a log line

**`gstr1`** ==> the player reading the log. **`gstr2`** ==> whoever acts. **`gstr3`** ==> whoever it
is done to. So `[gstr1==gstr2]` means «lo haces tú» and `[gstr1==gstr3]` means «te lo hacen a ti».

    <if([gstr1==gstr2],te ríes,se ríe)>       second person when you are the one acting
    <if([gstr1==gstr3],ti,<…gstr3…>)>         «se ríe de ti» when you are the one it lands on

**The English possessive is an `'s` hanging outside the inner branch, and «de ti» is never its
translation.** Split the phrase across both branches, or drop the possessive in the player's branch —
«de tus HP» already says whose they are.

    en  <if([gstr1==gstr2],Your,<if(…)>'s)> attack
    es  <if([gstr1==gstr2],tu ataque,el ataque de <if(…)>)>

    en  <head(<if([gstr1==gstr2],Your,<if(…)>'s)>)> <string(lstr1)> restores … of your HP.
    es  <head(<string(lstr1)>)><if([gstr1==gstr2],, de <if(…)>)> restaura … de tus HP.

## Formatting

**`<br>`** ==> line break. **`<nbsp>`** ==> non-breaking space; present in some English rows, keep it.
**`<italic(1)>…<italic(0)>`** ==> italics. **`<icon(n)>`, `<icon2(n)>`** ==> a button glyph.
**`<switchplatform(n)>`** ==> branches on the input device. **`<key>`** ==> wait for a keypress; it
separates the pages of one duty line, so it is a break, not a character.

**`<colortype(n)><edgecolortype(n)>…<edgecolortype(0)><colortype(0)>`** ==> highlight. A wrapped name
is an interface label and stays English. Some rows ship a malformed wrapper — reproduce it verbatim.
**`<color(n)><edgecolor(n)>…<edgecolor(stackcolor)><color(stackcolor)>`** ==> the same thing with a
literal colour instead of a palette entry. **`<edge(n)>`, `<shadow(n)>`, `<bold(1)>`** ==> the rest of
the family, one or two rows each.

## The whole list

39 macro families exist in the game's English text, counted on 12 August 2026 over all 7,160 sheets
that hold any. Everything above, plus these — **none of them holds prose, so none is ever
translated**; copy them across and move on.

    <lower(x)> <caps(x)>         case, the mirror of <head>: `Used when playing with a <lower(…)>.`
    <ordinal(n)>                 «1st», «2nd»: `Travel to <ordinal(lnum2)> Ward`
    <digit(n,2)> <float(n,10,.)> zero-padded and decimal numbers, both in coordinate readouts
    <settime(n)> <sec(x)>        a timestamp and its two-digit fields: t_hour, t_min, t_year
    <setresettime(h,d)>          the weekly reset, read by the <switch(t_wday,…)> that follows it
    <sound(1,96)> <levelpos(n)>  a sound cue and a map flag, both attached to the line
    <sheetsub(A,r,c,n,B,m)>      <sheet> for a sheet whose row points into another one
    <fixed(6,618)> <lowerhead(x)>  one row and eleven rows respectively; nothing to write in either

**`<ifself(n,A,B)>`** ==> «you» against a named player, 68 rows of the combat log:
`<ifself(lnum1,You are,<head(<pcname(lnum1)>)> is)> picked up by the kraken`. It branches like
`<if>` and **its branches hold prose**, so it is translated like `<if>`.

The three noun macros of the other languages — `<frnoun>`, `<denoun>`, `<janoun>` — appear in their
own language's sheets and never in the English. See the note above on why the French is unusable.

## Event-local parameters

`lnum1`, `lnum2`… only have a value inside the event that sets them. Rows using them are marked
`unresolved` and their `en` column has holes — « will be sealed off in  !». **Translate those from
`macro`, never from `en`**; `fr` and `ja` are whole and say what the sentence means.

`unresolved` no longer means "do not translate": that was the text-keyed runtime, which had to
evaluate the macro to build its lookup key. The redirect serves by row id. 1,602 of these rows ship.

## Not macros, though they look like it

**`\<sigh>`** ==> a stage direction. The backslash makes the game print the angle brackets literally,
so it is a character in the line, not notation.

    en  \<sigh> Hopefully things will be better this time...
    es  \<suspiro> Espero que esta vez las cosas vayan mejor...

Write it bare and the checker reads an unclosed macro, reports `macroInvented`, and the merge
discards the line. The corpus is unanimous: 599 escaped, none bare. Do not dissolve one into prose
(«Ay...») — it is a convention the player recognises across the whole game.

**`<-->`** and **`<->`** ==> a non-breaking and a soft hyphen. The validator cannot see them, since
its macro test needs a letter after `<`. Keep them where the English has them — `Radz<-->at<-->Han` —
and drop them when the compound they join disappears in translation.

## Traps

- **U+3000 inside a macro is invisible and fatal** — `<ennoun(Item,3,　28,2,1)>`.
- **Conditions use `==`.** A stray space exists in the source of `LogMessage#7591`; reproduce it.
- **Do not copy the French `<nbsp>` before `?` and `!`.** That is French typography. The `<nbsp>` that
  appears inside an English macro is a different thing and stays.
- **A macro in the Japanese or French but not the English is not yours to import.** If a row has no
  `macro` field, translate the plain `en` and do not adopt the `frMacro`: the French structure leaves
  a line the runtime can never match, and nothing reports it.
