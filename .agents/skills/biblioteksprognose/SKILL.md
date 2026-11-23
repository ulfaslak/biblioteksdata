---
name: biblioteksprognose
description: Lav en prognose over, hvor mange besøgende og udlån biblioteket får i en kommende uge, dag for dag og time for time, ud fra eksporter fra persontællerne og udlånsdata fra Cicero (Excel eller CSV). Brug den, når brugeren spørger om besøgstal eller udlån fremover, bemanding, travle dage eller timer, eller beder om en prognose.
---

# Biblioteksprognose

Du åbner et færdigt værktøj, `prognose.html` i samme mappe som denne fil, med brugerens egne data. Værktøjet læser filerne, regner prognosen ud i browseren og viser den. Du skal ikke selv regne prognosen ud, skrive kode eller ændre `prognose.html`.

## Sådan gør du

1. **Find dataene.** Som standard ligger de i mappen `data` i roden af repoet (mappen med `README.md` og `AGENTS.md`). Er den tom, så spørg brugeren, hvor filerne ligger (ofte i Overførsler/Downloads), og kopier dem til `data`. Brug alle filer med besøgstal fra persontællerne og udlånsdata fra Cicero (`.xlsx` eller `.csv`), gerne flere år. Værktøjet finder selv ud af, hvilke filer der er besøg, og hvilke der er udlån. Læg kun relevante filer i mappen, eller peg på en mappe, der kun har dem.
2. **Skriv `data.js`** i denne mappe med kommandoerne nedenfor. Skriv kommandoerne direkte i terminalen. Lav ikke en scriptfil: mange kommuner blokerer `.ps1`-filer.
3. **Hent vejret** for bibliotekets by med kommandoerne under *Vejr*. Det er valgfrit: virker internettet ikke, så spring det over. Prognosen virker også uden.
4. **Tilføj det, brugeren har fortalt** om ugen, filialen eller særlige dage (se *Indstillinger*).
5. **Åbn `prognose.html`** (sidste kommando nedenfor). Store udlånsfiler tager op til et halvt minut at læse.
6. **Svar kort**: prognosen er åbnet i browseren, med faner for besøgende og udlån. Brugeren kan folde "Kort tekst til kolleger eller chef" ud nederst på siden og kopiere teksten tilbage i chatten, hvis I skal arbejde videre med tallene.

Kommandoerne går ud fra, at terminalen står i roden af repoet. Skal du bruge filer fra en anden mappe, så erstat `data` med den mappe.

### Windows (PowerShell)

```powershell
$mappe = (Resolve-Path "data").Path; $skill = (Resolve-Path ".agents\skills\biblioteksprognose").Path
$dele = Get-ChildItem -Path $mappe -File | Where-Object { $_.Extension -in '.xlsx','.csv' -and $_.Name -notlike '~$*' } | ForEach-Object { '{navn:' + ($_.Name | ConvertTo-Json) + ',base64:"' + [Convert]::ToBase64String([IO.File]::ReadAllBytes($_.FullName)) + '"}' }
[IO.File]::WriteAllText("$skill\data.js", 'window.PROGNOSE_EKSPORTER=[' + ($dele -join ',') + '];', [Text.UTF8Encoding]::new($false))
Start-Process "$skill\prognose.html"
```

### Mac (Terminal)

```bash
MAPPE="data"; SKILL=".agents/skills/biblioteksprognose"
{ printf 'window.PROGNOSE_EKSPORTER=['; sep=''; find "$MAPPE" -maxdepth 1 -type f \( -name '*.xlsx' -o -name '*.csv' \) ! -name '~$*' | sort | while IFS= read -r f; do printf '%s{navn:"%s",base64:"' "$sep" "$(basename "$f")"; base64 -i "$f" | tr -d '\n'; printf '"}'; sep=','; done; printf '];\n'; } > "$SKILL/data.js"
open "$SKILL/prognose.html"
```

## Vejr

Henter dagens højeste temperatur og nedbør fra Open-Meteo (gratis, ingen nøgle) for byen, både historik og prognose for de næste 16 dage, og lægger dem i `data.js`. Kør det efter `data.js` er skrevet. Erstat `BY` med byen, fx `Roskilde`. Kun bynavnet sendes ud; bibliotekets data bliver på computeren.

Windows (PowerShell):

```powershell
$by = "BY"; $skill = (Resolve-Path ".agents\skills\biblioteksprognose").Path
$g = (Invoke-RestMethod "https://geocoding-api.open-meteo.com/v1/search?name=$([uri]::EscapeDataString($by))&count=1&countryCode=DK").results[0]
$ll = "latitude=$($g.latitude)&longitude=$($g.longitude)&daily=temperature_2m_max,precipitation_sum&timezone=Europe%2FCopenhagen"
$h = (Invoke-WebRequest -UseBasicParsing "https://archive-api.open-meteo.com/v1/archive?$ll&start_date=2014-01-01&end_date=$(Get-Date -Format yyyy-MM-dd)").Content
$p = (Invoke-WebRequest -UseBasicParsing "https://api.open-meteo.com/v1/forecast?$ll&past_days=14&forecast_days=16").Content
Add-Content -Encoding ASCII "$skill\data.js" "window.PROGNOSE_VEJR_HISTORIK=$h;`nwindow.PROGNOSE_VEJR_PROGNOSE=$p;"
```

Mac (Terminal):

```bash
BY="BY"; SKILL=".agents/skills/biblioteksprognose"
LL=$(curl -sG "https://geocoding-api.open-meteo.com/v1/search" --data-urlencode "name=$BY" -d count=1 -d countryCode=DK | grep -o '"latitude":[0-9.-]*,"longitude":[0-9.-]*' | head -1 | sed 's/"latitude":/latitude=/; s/,"longitude":/\&longitude=/')
Q="$LL&daily=temperature_2m_max,precipitation_sum&timezone=Europe%2FCopenhagen"
{ printf 'window.PROGNOSE_VEJR_HISTORIK='; curl -s "https://archive-api.open-meteo.com/v1/archive?$Q&start_date=2014-01-01&end_date=$(date +%F)"; printf ';\nwindow.PROGNOSE_VEJR_PROGNOSE='; curl -s "https://api.open-meteo.com/v1/forecast?$Q&past_days=14&forecast_days=16"; printf ';\n'; } >> "$SKILL/data.js"
```

Modellen bruger vejret sådan: dage, der er usædvanligt varme eller kolde for årstiden, ændrer antallet af besøgende og udlån (hvert bibliotek får sine egne tal). Regn på fridage (skoleferier, weekender og helligdage) ændrer, hvor mange der kommer, og regn ændrer, hvor længe folk bliver i huset.

## Indstillinger

Alt er valgfrit. Tilføj linjerne til sidst i `data.js`, *før* du åbner siden (PowerShell: `Add-Content -Encoding UTF8 ".agents\skills\biblioteksprognose\data.js" '...'`; Mac: `echo '...' >> .agents/skills/biblioteksprognose/data.js`).

```js
window.PROGNOSE_INDSTILLINGER = { bibliotek: "Roskilde", start: "2026-11-23", visning: "udlaan", vinterferieUge: 7, efteraarsferieUge: 42, sommerferieFra: 27, sommerferieTil: 32 };
window.PROGNOSE_SAERLIGE_DAGE = [ { dato: "2026-11-26", navn: "Forfatteraften", type: "arrangement" } ];
```

- `bibliotek`: navnet på filialen, som det står i dataene. Uden den vælges den største.
- `start`: en dato i den uge, prognosen skal gælde. Uden den bruges næste uge.
- `visning`: `besoeg` (standard) eller `udlaan`, hvis brugeren spørger om udlån.
- Skoleferierne er ugenumre. De varierer fra kommune til kommune; brug kommunens egne, hvis brugeren kender dem.
- Særlige dage har typen `valg`, `arrangement` eller `lukket`. Modellen lærer, hvor mange ekstra besøgende og udlån en type giver, ud fra tidligere dage af samme type. Tilføj derfor også tidligere arrangementer, hvis brugeren kender datoerne; uden dem tager prognosen ikke højde for arrangementet, og siden siger det. Landsdækkende valg fra 2019 til 2026 er allerede indbygget.

## Det skal du ikke

- Sende dataene nogen steder hen. Alt foregår på brugerens egen computer. Det eneste, der går ud, er bynavnet til vejrtjenesten.
- Love præcise tal. Prognosen har et spænd, og siden viser, hvor godt den har ramt de seneste uger.
