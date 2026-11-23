# Om

Et arbejdsrum til at analysere et folkebiblioteks egne data med en kodeagent.
Brugeren arbejder på et bibliotek og bruger Excel, måske Power BI, men programmerer ikke.
Dataene ligger i `data/` og bliver på brugerens computer. Resultater er HTML-sider, der åbner i browseren.

# Faste regler

- **Send aldrig data videre.** Upload ikke filer, og sæt ikke data ind i webtjenester, API'er eller delingssider. Det, du selv læser, går til AI-tjenesten, så læs kun det, du har brug for: kolonnenavne, nogle få rækker, summer.
- **Ingen personoplysninger.** Ser du navne, lånernumre, CPR-numre, adresser eller mails, så stop, og sig det til brugeren. KL's vejledning om generativ AI (december 2025): personoplysninger må kun bruges i værktøjer, kommunen har godkendt til det.
- **Rør ikke originalerne.** Arbejd på kopier i `data/`. Lav nye filer i stedet for at ændre eksporterne.
- **Sig, hvad der er usikkert.** Gæt ikke stille om kolonner, filialer eller perioder. Skriv, hvad du har antaget, og hvad der kan tjekkes.
- **Tjek tallene, før du svarer.** Sammenlign med en simpel optælling (rækker i alt, udlån pr. måned, besøg pr. år). Passer de ikke, så find ud af hvorfor, før du viser noget.

# Brugeren

- Bibliotekspersonale, ikke programmører. Forklar resultater på almindeligt dansk, kort, uden fagudtryk. Vis ikke kode, medmindre brugeren beder om det.
- Danske tal og datoer: 12.345 udlån, 8,5 %, tirsdag d. 24. november, uge 48.
- Spørg, hvad tallene skal bruges til (bemanding, indkøb, en rapport til chefen), hvis det ikke er klart. Det afgør, hvad der er vigtigt at vise.
- Afrund til det, der giver mening: "omkring 7.000 besøgende", ikke "7.012,4".

# Dataene

## Udlån fra Cicero

- Samme format i alle kommuner, så en udlånsanalyse virker hos alle.
- Én række pr. udlån: filial, afdeling, opstilling (hylde), DK5, materialetype, titel, forfatter, ISBN, lånergruppe og tidspunkt (til sekundet).
- Kun udlån: ingen afleveringer eller fornyelser. Ingen låner-id, så "lånere" kan ikke tælles, kun lån.
- En dag uden rækker er som regel en lukkedag, ikke en dag med nul udlån.
- Nogle lånergrupper er interne (personale, bogpleje) eller andre biblioteker (fjernlån). Spørg, om de skal med.
- Excel kan højst have cirka en million rækker, så store kommuner deler udtrækket i flere filer pr. år. Læg dem sammen.

## Besøg fra persontællerne

- Formatet afhænger af tællerleverandøren og er forskelligt fra bibliotek til bibliotek. Kig i filen (ark, kolonner, de første rækker), før du antager noget, og fortæl brugeren, hvad du fandt.
- Typisk tal pr. time og pr. tæller eller dør. Tallene er indgange (besøg), ikke personer. Ind og ud stemmer sjældent overens.
- Tjek for huller og fejl: dage med 0, dage med urimeligt høje tal, en tæller, der er skiftet.

## Afvigere

- **Valgdage**: biblioteket er ofte valgsted, og så kommer der tusindvis af ekstra besøgende, men færre udlån. Ugerne før kan også være høje (brevstemmer).
- Arrangementer, ferier, helligdage, lukkedage og ombygninger.
- Marker afvigere, og sig det. Fjern dem ikke stille.

# Sådan leverer du

- **Resultatet er én HTML-fil**, der åbner i browseren uden installation: tal, tabeller og grafer i én fil, intet hentet fra nettet. Så virker den uden net og kan sendes til en kollega. Gem den i `data/resultater/`, og åbn den for brugeren.
- **Undgå Python, hvor du kan.** Mange kommunale computere er låst: brugeren kan ikke installere programmer, og `.ps1`-filer er blokeret. Brug kommandoer direkte i terminalen (PowerShell på Windows), og lad regnearbejdet ske i browseren.
- Excel-filer kan læses i browseren uden ekstra programmer: se funktionerne `zipEntries`, `zipText` og `xlsxRows` i `.agents/skills/biblioteksprognose/prognose.html`, og mønstret i dens `SKILL.md`, hvor filerne pakkes ind i en `data.js` ved siden af siden.
- CSV fra danske systemer er ofte semikolonsepareret, med komma som decimaltegn.
- Skriv til sidst et kort svar i chatten: hvad du fandt, hvor sikkert det er, og hvor siden ligger.

# Skills

En skill er en opskrift, agenten følger, når brugeren beder om noget bestemt.

- `biblioteksprognose`: prognose for besøgende og udlån i en kommende uge, dag for dag og time for time.

## Gem en analyse som ny skill

Når brugeren vil have den samme analyse igen, eller beder om at gemme den:

1. Lav `.agents/skills/<navn>/SKILL.md` (navn med små bogstaver og bindestreger). Øverst `name` og `description`, hvor beskrivelsen siger, hvad skillen gør, og hvornår den skal bruges, med de ord brugeren selv bruger. Derefter trinene på dansk.
2. Læg en HTML-skabelon ved siden af, hvis analysen ender i en side, så næste gang ikke skal skrive koden forfra.
3. Lav `.claude/skills/<navn>/SKILL.md` med samme `name` og `description` og én linje: "Læs `.agents/skills/<navn>/SKILL.md`, og følg den." Så finder Claude Code den også.
4. Ingen data, navne eller tal fra jeres eget bibliotek i skillen. Så kan den deles med andre biblioteker: send mappen, eller læg den på GitHub.
