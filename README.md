# Cornflakes

Interaktiv dashboard som visualiserar försäljnings- och rådgivardata från ett Excel-ark. Allt körs i webbläsaren — ingen server, ingen molntjänst, ingen data som lämnar datorn.

## Komma igång

Klicka **"Ladda Excel-fil"** i headern och välj ett ifyllt Excel-ark som följer formatet nedan. Datan cacheas i webbläsaren så den finns kvar nästa gång du besöker sidan.

### Kom-ihåg-fil (auto-uppdatering)

I Chrome och Edge minns appen vilken fil du senast valde via [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API):

- **Vid sidladdning** läses filen om automatiskt om webbläsaren fortfarande har läsbehörighet (t.ex. efter F5 i samma session). Spara nya värden i Excel, refresha, klart.
- **🔄 Uppdatera-knappen** i headern dyker upp så fort en fil är ihågkommen. Ett klick läser om filen direkt — utan att öppna filväljaren. Efter omstart av webbläsaren visar webbläsaren en liten "Tillåt åtkomst?"-bekräftelse första gången, sen är behörigheten tillbaka.

Allt sker lokalt; inga filer skickas någonstans. I Safari och Firefox saknas API:et, så där fungerar appen som tidigare: klicka "Ladda Excel-fil" varje gång du vill ladda om.

## Funktioner

- **KPI-kort** för omsättning, resultat, antal möten och nya kunder — visar hur långt över eller under förväntat man ligger just nu.
- **Budget vs Omsättning** och **Resultat ackumulerat** — månadsvisa linjediagram. Hela året visas på x-axeln, men omsättnings- och resultatlinjerna ritas bara fram till senaste månaden med ifylld data. Punkterna blir gröna om värdet ligger över budget/mål den månaden.
- **Möten per Rådgivare** — staplad horisontell graf där "Antal möten" är taket och Individ-protokoll, Företags-protokoll och V2 fyller upp det. V2 ligger som en överlappande del av Individ-protokoll.
- **Per-kategori progress** för Cross Selling, Modern Redovisning, B2B, V2, Löneväxling och Flytt — staplarnas längd visar faktiska budget i kr/antal, färgen styrs av om rådgivaren ligger i fas mot proportionell budget (`budget × månader hittills / 12`):
  - **Röd** under 80%
  - **Gul** 80–99%
  - **Grön** 100% eller över
  - Extra grön bit visas om man passerat helårsbudget
- **Filtrera per rådgivare** — klicka en pill överst så visas bara de grafer/sektioner personen är med i, plus en kombinerad progress-graf över alla kategorier.
- **Mörkt och ljust tema** — växla med knappen i headern. Valet sparas mellan besök.

## Excel-format

Filen ska ha en flik som heter **`Data`** (eller så plockas första fliken). I kolumn A definieras sektionsrubriker, raden under är kolumnrubriker, sedan följer dataraderna. Tom rad eller en ny känd rubrik avslutar sektionen.

Sektioner som dashboarden känner igen:

| Rubrik | Kolumner |
|--------|----------|
| `Möten` | Antal möten · Individ-protokoll · Företags-protokoll · V2 i möte |
| `Cross Selling` | Budget · Totalt Skickade |
| `Nya Kunder` | Antal nya kunder |
| `Budget` | Budget ackumulerad · Omsättning · Omsättning ackumulerad · Resultat mål · Resultat |
| `MR` | Värde · Budget |
| `B2B` | Värde · Budget |
| `V2` | Värde · Budget |
| `LV` | Värde · Budget |
| `SO` | Värde · Budget |

Sektionerna kan ligga i valfri ordning och vara olika långa — parsern letar efter rubrikerna, inte fasta radnummer. Lägg till eller ta bort rådgivare hur du vill.

För Budget-sektionen är raderna månader (`Januari` … `December`).

## Integritet

Hela appen är klient-side. Excel-filen läses i webbläsaren via `FileReader`- eller File System Access-API:et, parsas av `xlsx.js`, och datan sparas bara i din egen `localStorage` (parsad data) och `IndexedDB` (fil-handtag i Chrome/Edge). Inga nätverksanrop görs till någon backend — fungerar likadant på GitHub Pages som vid lokal `file://`-körning.
