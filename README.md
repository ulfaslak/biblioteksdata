# Biblioteksdata

Et arbejdsrum til at analysere bibliotekets egne data med en kodeagent: en AI-assistent, der arbejder i en mappe på din computer, læser dine filer og laver analyser, når du beder om det på dansk.

Lavet af Ulf Aslak til Bibliotekernes Datakonference i Vejle, 24. november 2026. Tag den med hjem, og prøv den på jeres egne tal.

## Kom i gang

1. **Hent mappen.** Klik på den grønne knap **Code** øverst på siden, og vælg **Download ZIP**. Pak zip-filen ud, fx i Dokumenter. (Bruger du git: `git clone https://github.com/ulfaslak/biblioteksdata.git`.)
2. **Installer én kodeagent.** Det er nok med én af dem. Følg vejledningen på deres side:

   | Agent | Kræver | Vejledning | Start med |
   |---|---|---|---|
   | Claude Code | Claude-abonnement | [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code) | `claude` |
   | Codex | ChatGPT-abonnement | [github.com/openai/codex](https://github.com/openai/codex) | `codex` |
   | GitHub Copilot CLI | Copilot-abonnement | [github.com/github/copilot-cli](https://github.com/github/copilot-cli) | `copilot` |
   | Mistral Vibe | Mistral-konto | [github.com/mistralai/mistral-vibe](https://github.com/mistralai/mistral-vibe) | `vibe` |

   På en låst kommunal computer kan det kræve hjælp fra IT.
3. **Åbn en terminal i mappen.**
   - Windows: åbn mappen i Stifinder, klik i adresselinjen, skriv `powershell`, og tryk Enter.
   - Mac: åbn Terminal, skriv `cd ` (med mellemrum), træk mappen ind i vinduet, og tryk Enter.
4. **Start agenten** med kommandoen i tabellen. Spørger den, om du stoler på mappen, så svar ja. Ellers læser den ikke instruktionerne og opskrifterne her.
5. **Skriv, hvad du vil vide**, på almindeligt dansk. Fx: "Hvilke materialetyper bliver lånt mere i år end sidste år?"

## Dine data

Læg bibliotekets eksporter i mappen [`data`](data/README.md): udlån fra Cicero og besøgstal fra persontællerne, som `.xlsx` eller `.csv`. Agenten finder dem dér. Filerne kommer aldrig på GitHub (git ignorerer dem).

## Prognose for besøg og udlån

Mappen har en færdig opskrift (en *skill*) til prognoser. Læg besøgs- og udlånsdata i `data`, start agenten, og spørg fx:

> Hvor mange besøgende kan vi forvente i næste uge?

Agenten pakker filerne, henter vejrudsigten for jeres by og åbner prognosen i browseren: besøgende og udlån dag for dag og time for time, med et spænd, et tjek af hvor godt den har ramt de seneste uger, og en kort tekst til kolleger eller chef. Selve regnearbejdet sker i din browser; kun bynavnet sendes til vejrtjenesten.

Siden virker også uden agent: åbn `.agents/skills/biblioteksprognose/prognose.html` i Edge eller Chrome, og træk filerne ind på den. (På Mac er mapper, der starter med punktum, skjult i Finder; tryk Cmd+Shift+punktum for at se dem.)

## Lav dine egne opskrifter

Har du en analyse, du vil lave igen, så bed agenten: "Gem det her som en skill." Den skriver en opskrift i `.agents/skills/`, som du kan dele med andre biblioteker. Udlånsdata fra Cicero har samme format i alle kommuner, så en udlånsanalyse virker hos alle. Agentens instruktioner står i [AGENTS.md](AGENTS.md); ret dem gerne til jeres bibliotek.

## Databeskyttelse

- **Ingen personoplysninger i `data`**: ingen navne, lånernumre, CPR-numre eller adresser. Udlånseksporten fra Cicero med filial, materiale og lånergruppe og besøgstal fra tællerne er ikke personoplysninger, men tjek selv, hvad jeres eksport indeholder.
- **Det, agenten læser, sendes til AI-tjenesten** (Anthropic, OpenAI, GitHub eller Mistral). Agenten er bedt om kun at læse det, den har brug for, og om aldrig at sende data andre steder hen.
- **KL's vejledning om generativ AI** (december 2025): personoplysninger må kun bruges i AI-værktøjer, kommunen har godkendt til det. Spørg jeres IT eller databeskyttelsesrådgiver, hvilke værktøjer I må bruge, og til hvad.

## Licens

MIT. Brug, ret og del frit.
