# Brugervejledning til Opret-Ret 101x

`Opret-Ret 101x.xlsm` er en Excel-fil til validering, oprettelse og opdatering
af personer i Silkeborg Løn via IBM Personal Communications.

## Før du starter

Følgende skal være klar:

- Microsoft Excel til Windows.
- IBM Personal Communications skal være startet.
- Excel og IBM Personal Communications skal begge være enten 32-bit eller
  64-bit.
- Der skal være en aktiv og indlogget forbindelse til Silkeborg Løn.
- Excel-filen skal være hentet til en lokal mappe, ikke en midlertidig mappe,
  et netværksdrev eller en webvisning.
- Makroer skal være tilladt.

Hvis Windows har blokeret filen efter download:

1. Luk Excel.
2. Højreklik på Excel-filen i Stifinder.
3. Vælg `Egenskaber`.
4. Markér eventuelt `Fjern blokering`.
5. Vælg `Anvend` og derefter `OK`.

## Overblik

Den normale arbejdsgang er:

1. Indtast eller indsæt personer i arket `Batch`.
2. Vælg den korrekte institution.
3. Tryk `Valider`.
4. Ret eventuelle fejl.
5. Tryk `Opret` for personer, der ikke findes.
6. Tryk `Opdater` for at kontrollere og rette eksisterende personer.
7. Kontroller resultatet i statuskolonnerne og i `Lønsystem Log`.

## Arket Batch

Data starter på række 4.

Behandlingen stopper ved den første række, hvor både CPR og dato er tomme.
Der må derfor ikke være tomme linjer midt i de data, som skal behandles.

De vigtigste kolonner er:

| Kolonne | Indhold |
|---|---|
| Status | Resultatet af valideringen eller behandlingen |
| TJNR | Tjenestenummer eller en særlig statusværdi |
| CPR | CPR-nummer med præcis 10 cifre, gemt som tekst |
| Dato | Den dato personen skal kontrolleres eller behandles på |
| Ekstern Inst/TJNR | Ekstern identifikation, hvis denne funktion anvendes |
| Opret-status | Resultatet af en oprettelse |

De øvrige kolonner indeholder de værdier, som skal kontrolleres eller
opdateres i lønsystemet.

## Institution

Kontroller institutionen øverst i arket `Batch`, før en behandling startes.

Scriptet anvender institutionen ved opslag og navigation i lønsystemet.
En forkert institution kan derfor give forkerte opslag eller tekniske fejl.

## Valider

Knappen `Valider` kontrollerer først:

- At opsætningen er gyldig.
- At CPR-numre har korrekt format.
- At datoer er gyldige og ligger inden for det tilladte interval.
- At inputfelter har den forventede datatype og længde.
- At External ID højst er 10 tegn, når funktionen er aktiveret.

Derefter spørger Excel:

```text
Skal Valider også tjekke værdier i lønsystemet for rækker med angivet TJNR?
```

- `Ja`: Udfører fuld validering mod lønsystemet.
- `Nej`: Udfører kun basis- og inputvalidering.
- `Annuller`: Stopper uden behandling.

Ved opslag ud fra CPR undersøges de fundne tjenestenumre for at afgøre, om
personen er relevant på den valgte dato.

## Resultater ved CPR-opslag

Ved et CPR-opslag nulstilles lønsystemet først, så gammel tekst på
nøglelinjen ikke påvirker søgningen.

Hvis lønsystemet viser en oversigt med flere TJNR, kontrolleres Statuskode og
Dt.kode direkte på oversigten. TJNR, der ikke består denne kontrol, noteres
som tidligere ansættelse eller timeløn og åbnes ikke.

De resterende TJNR kontrolleres enkeltvis:

1. Personen åbnes på den valgte dato.
2. CPR-nummeret kontrolleres.
3. Statuskode og Dt.kode skal begge være `0` eller `1`.
4. Hvis External ID-kontrollen er aktiveret, skal Ekstern Inst/TJNR matche
   værdien i Batch.
5. Personens startdato skal være lig med eller før datoen i Batch.

Hvis ét TJNR består alle kontroller, skrives det i Batch. Hvis flere består,
skrives `FLERE`. Hvis ingen består, afhænger resultatet af, hvorfor de fundne
personer blev frasorteret.

TJNR-feltet og statusfeltet kan blandt andet vise:

| Status | TJNR-felt | Betydning |
|---|---|---|
| `OK` | Et tjenestenummer | Én relevant person blev fundet |
| `Ikke oprettet` | `OPRET` | CPR-nummeret findes ikke i lønsystemet |
| `Tidligere ansat / timeløn` | `OPRET` | Der findes tidligere eller ikke-relevante ansættelser, men ingen relevant aktiv person |
| `OK` | `FLERE` | Flere relevante tjenestenumre blev fundet; brugeren skal tage stilling |
| `Aktiv ansat, fejl i Ekstern Inst/TJNR` | `ANSAT` | En aktiv person blev fundet, men External ID matcher ikke |
| `Person oprettet efter valgt dato` | `DATE ERR` | Personen er oprettet efter den dato, som står i Batch |
| Fejl i opslag | `ERROR` | Opslaget kunne ikke gennemføres sikkert |

`ANSAT`, `DATE ERR`, `FLERE` og `ERROR` markeres rødt og skal undersøges
manuelt.

## Opret

Rækker med samme CPR-nummer behandles som én CPR-gruppe. Arket sorteres
automatisk efter CPR og dato, så gruppens rækker står samlet.

Der oprettes kun én person pr. CPR-gruppe. Alle rækker i gruppen skal have
`OPRET` i TJNR-feltet, før gruppen behandles. Ved oprettelsen anvendes datoen
fra gruppens første række. Det nye TJNR skrives derefter på alle gruppens
rækker, som efterfølgende kontrolleres og opdateres én ad gangen.

Knappen `Opret` behandler CPR-grupper, hvor TJNR står som `OPRET`.

Før oprettelsen kontrolleres input og de nødvendige oprettelsesparametre.
Hvis valideringen finder fejl, stoppes oprettelsen.

Ved en vellykket oprettelse:

- Det nye TJNR skrives tilbage i arket.
- Oprettelsesstatus opdateres.
- De relevante felter kontrolleres og opdateres efter oprettelsen.
- Resultatet skrives i `Lønsystem Log`.

Mulige oprettelsesresultater omfatter:

- `Oprettet`
- `Oprettet med advarsel`
- `Oprettet og Opdateret`
- `Oprettet, men opdatering fejlede`
- `Fejl i oprettelse`

Kontroller altid rækker med advarsler eller fejl.

## Opdater

Knappen `Opdater`:

1. Validerer input.
2. Slår personen op i lønsystemet.
3. Kontrollerer CPR, dato og eventuelt External ID.
4. Sammenligner Batch-værdier med værdierne i lønsystemet.
5. Opdaterer felter, som er konfigureret til handlingen `Ret`.
6. Genlæser værdierne efter opdateringen.
7. Skriver resultatet i statuskolonnen og i logarket.

Felter, der kun er konfigureret til kontrol, ændres ikke.

## Farvemarkering

Farver bruges som visuel hjælp:

- Grøn: Værdien stemmer eller er opdateret.
- Gul: Der er en afvigelse ved validering eller en advarsel ved opdatering,
  som skal vurderes.
- Rød: Ugyldig værdi, teknisk fejl eller et resultat, som kræver manuel
  behandling.

Statuskolonnen og loggen er den endelige forklaring. Farven bør ikke stå
alene.

## Felter

Arket `Felter` styrer, hvilke Batch-kolonner der skal kontrolleres eller
opdateres i lønsystemet.

Hver række beskriver ét felt:

| Kolonne | Indhold |
|---|---|
| A | Kolonnen i Batch, eksempelvis `G` eller `AA` |
| B | Den forventede kolonneoverskrift i Batch |
| D | Side i lønsystemet, fra 1 til 3 |
| E | Række i lønsystemet, fra 1 til 24 |
| F | Kolonne i lønsystemet, fra 1 til 80 |
| G | Feltets længde |
| H | Datatype eller kontroltype |
| I | Tilladte værdier eller forventet formel |
| J | Eventuelt gruppenavn |
| K | Handling: `Ret`, `Tjek` eller `Inaktiv` |

Feltets kolonne og længde må ikke gå ud over position 80 på
lønsystemskærmen.

Mulige handlinger:

- `Ret`: Feltet sammenlignes og kan ændres ved `Opdater`.
- `Tjek`: Feltet sammenlignes, men ændres ikke.
- `Inaktiv`: Feltet springes over.

Mulige datatyper:

- `Tekst`: Almindelig tekst op til den angivne længde.
- `Tal`: Heltal.
- `Decimal`: Tal, der formateres til lønsystemets decimalformat.
- `Værdiliste`: Værdien skal findes i den semikolonseparerede liste i
  kolonne I.
- `Formular`: Batch-cellen skal indeholde den forventede formel fra kolonne I.
  Teksten `[ROW]` i formlen erstattes med det aktuelle rækkenummer.

Felter med samme gruppenavn skal stå på sammenhængende rækker og have samme
side og handling. Gruppen behandles samlet: værdierne sendes og kontrolleres
som én logisk opdatering.

## Parameter

Arket `Parameter` indeholder de indstillinger og standardværdier, som bruges
af Valider, Opret og Opdater.

Parameternavnet står i første kolonne, og værdien står i den næste kolonne.
Parameternavnene må ikke ændres.

De vigtigste parametre er:

| Parameter | Funktion |
|---|---|
| `DatoMin` | Tidligste dato, der accepteres i Batch |
| `DatoMax` | Seneste dato, der accepteres i Batch |
| `InforceExternalID` | `TRUE` aktiverer kontrol af Ekstern Inst/TJNR |
| `SetRegnrOnCreate` | `TRUE` indsætter standardregistreringsnummer ved oprettelse |
| `CreateKey` | Nøgle, der anvendes ved oprettelse |
| `ValueOnCreateOvk` | Standardværdi til OVK ved oprettelse |
| `ValueOnCreateLk` | Standardværdi til LK ved oprettelse |
| `ValueOnCreateStilling` | Standardværdi til stilling ved oprettelse |
| `ValueOnCreateAFD` | Standardværdi til afdeling ved oprettelse |
| `ValueOnCreateDtKode` | Dt.kode ved oprettelse; skal være `0`, `1`, `2` eller `6` |
| `ValueOnCreateStill` | Standardværdi til stillingskode ved oprettelse |
| `SuccessLsCreateCode` | Returkoder, der regnes som en vellykket oprettelse |
| `WarningLsCreateCode` | Returkoder, der regnes som en oprettelse med advarsel |
| `SuccessLsCode` | Returkoder, der regnes som en vellykket opdatering |
| `WarningLsCode` | Returkoder, der regnes som en opdatering med advarsel |
| `ErrorLsCode` | Returkoder, der regnes som fejl |

Flere returkoder kan angives i samme værdi adskilt med semikolon, komma eller
linjeskift.

Ændr kun parametre, når betydningen og det krævede format er kendt. Forkerte
oprettelsesværdier stopper valideringen eller kan give en fejlretur fra
lønsystemet.

## Lønsystem Log

Arket `Lønsystem Log` indeholder sporbarhed for:

- TJNR-opslag
- Oprettelser
- Feltopdateringer
- Returkoder og returtekster fra lønsystemet
- Værdier før, sendt og efter en opdatering
- Scriptets samlede status

Ved TJNR-opslag kan loggen vise, hvorfor et tjenestenummer blev valgt eller
frasorteret, eksempelvis:

- Tidligere ansat eller timeløn
- External ID matcher ikke
- Person oprettet efter valgt dato
- Flere relevante tjenestenumre

## Hvis behandlingen fejler

Kontroller følgende:

1. Er IBM Personal Communications startet?
2. Er forbindelsen til Silkeborg Løn aktiv?
3. Er brugeren logget ind?
4. Er den korrekte institution valgt?
5. Står lønsystemet i en dialog eller fejlmeddelelse, som skal lukkes?
6. Er CPR, dato og External ID indtastet korrekt?
7. Er der tomme linjer midt i Batch-data?

Kør derefter `Valider` igen.

Hvis fejlen fortsætter, gem oplysninger om:

- Batchrækken
- Statusfeltet
- TJNR-feltet
- Det relevante opslag i `Lønsystem Log`
- Den viste fejltekst i lønsystemet

## Download fra GitHub

1. Åbn projektets GitHub-side.
2. Vælg Excel-filen `Dist/Opret-Ret 101x.xlsm`.
3. Vælg download af den rå fil, eller hent repositoryets ZIP-fil.
4. Gem Excel-filen lokalt.
5. Fjern eventuel Windows-blokering som beskrevet ovenfor.
6. Åbn filen i Excel og aktivér makroer, hvis filen er fra en betroet kilde.

## Versionscheck

Workbooken kan have en knap til manuel versionskontrol. Knappen kører makroen
`TjekForNyVersion`.

Når makroen køres, henter den den publicerede versionsoversigt fra GitHub og
sammenligner workbookens versionsnummer med den nyeste publicerede version.
Hvis der findes en nyere version, viser Excel en besked med mulighed for at
åbne downloadlinket.

Versionschecket kører ikke automatisk, når workbooken åbnes.
