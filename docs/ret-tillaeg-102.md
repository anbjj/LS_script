# Brugervejledning til Ret Tillæg - 102

`Ret Tillaeg - 102.xlsm` er en Excel-fil til behandling af tillæg på billede 102 i Silkeborg Løn via IBM Personal Communications.

Scriptet indeholder ark til både tilføjelse og sletning af tillæg og skriver resultatet til statuskolonnen og logarket.

## Før du starter

Følgende skal være klar:

- Microsoft Excel til Windows.
- IBM Personal Communications skal være startet.
- Excel og IBM Personal Communications skal begge være enten 32-bit eller 64-bit.
- Der skal være en aktiv og indlogget forbindelse til Silkeborg Løn i session `A`.
- Lønsystemet skal stå på et almindeligt LS-billede. Hvis du er i tvivl, log ind og gå til billede 100.
- Excel-filen skal være hentet til en lokal mappe, ikke en midlertidig mappe, et netværksdrev eller en webvisning.
- Makroer skal være tilladt.

Hvis Windows har blokeret filen efter download:

1. Luk Excel.
2. Højreklik på Excel-filen i Stifinder.
3. Vælg `Egenskaber`.
4. Markér eventuelt `Fjern blokering`.
5. Vælg `Anvend` og derefter `OK`.

## Ark

Workbooken indeholder disse centrale ark:

| Ark | Formål |
|---|---|
| `Batch_tilføj` | Tilføjer tillæg på billede 102 |
| `Batch_slet` | Sletter tillæg fra billede 102 |
| `Log` | Viser resultat, fejl og sporbarhed for behandlingen |

## Normal arbejdsgang

1. Indtast eller indsæt data i det relevante batchark.
2. Kontroller, at forbindelsen til Silkeborg Løn er aktiv og klar.
3. Kør inputvalidering via knappen `Valider`.
4. Tilføj eller slet tillæg via knappen `Kør`.
5. Kontroller statuskolonnen og eventuelle fejl i `Log`.
6. Ret fejl i batcharket, og kør behandlingen igen efter behov.

## Batch_tilføj

`Batch_tilføj` indsætter tillæg på billede 102 i lønsystemet.

Institutionskode angives én gang for hele kørslen i celle `B2`.

| Kolonne | Felt | Krav |
|---|---|---|
| A | TJNR | Præcis 5 cifre. Ved færre cifre tilføjer valideringen foranstillede nuller. |
| B | Dato | Gyldig Excel-dato mellem 01-01-2000 og 31-12-2050. |
| C | Nyt tillæg | Præcis 6 cifre. Ved færre cifre tilføjer valideringen foranstillede nuller. |
| D | Beløb | Blank eller tal med maks. 2 decimaler. |
| E | Status | Ryddes af valideringen og udfyldes af scriptet. |
| F | AFD | Blank eller 1-4 tegn. Må kun indeholde tal og bogstaver. |
| G | Enheder | Blank eller tal med maks. 4 decimaler. Vises som `0,0000`. |
| H | Navn | Blank eller maks. 40 tegn. |

Hvis `Beløb`, `AFD`, `Enheder` eller `Navn` er blank, ignorerer scriptet kolonnen for den pågældende række.

Valideringen formaterer relevante kolonner, normaliserer TJNR og tillægsnummer og skriver fejl i statuskolonnen. Rækker med valideringsfejl bliver ikke rettet automatisk ud over de nævnte normaliseringer.

## Batch_slet

`Batch_slet` sletter tillæg fra billede 102 i lønsystemet.

Institutionskode angives én gang for hele kørslen i celle `B2`.

| Kolonne | Felt | Krav |
|---|---|---|
| A | TJNR | Præcis 5 cifre. Ved færre cifre tilføjer valideringen foranstillede nuller. |
| B | Dato | Gyldig Excel-dato mellem 01-01-2000 og 31-12-2050. |
| C | Slet tillæg | Præcis 6 cifre. Ved færre cifre tilføjer valideringen foranstillede nuller. |
| D | Status | Ryddes af valideringen og udfyldes af scriptet. |

Hvis tillægget ikke findes, skriver scriptet status for rækken og fortsætter med næste række.

## Validering

Knappen `Valider` kontrollerer det aktive batchark.

Valideringen kontrollerer format, datoer, talværdier og tekstlængder. Den slår ikke op i lønsystemet. Valideringen kan derfor fange, om TJNR, tillægsnummer eller dato har forkert format, men den kan ikke kontrollere, om f.eks. et TJNR eller tillæg faktisk findes i lønsystemet.

Hvis valideringen er korrekt, vises beskeden:

```text
Valider ok
```

Hvis én eller flere rækker ikke kan valideres, vises antallet af fejl, og detaljerne skrives i statuskolonnen.

## Log

`Log` bruges til at kontrollere, hvad scriptet har gjort.

Loggen bør gemmes eller gennemgås efter behandling, især når der er:

- Tekniske fejl i kommunikationen med IBM Personal Communications.
- Rækker, som ikke kunne behandles.
- Rækker, hvor lønsystemet returnerer en advarsel eller fejltekst.

## Hvis behandlingen fejler

Kontroller følgende:

1. Er IBM Personal Communications startet?
2. Findes session `A`?
3. Er forbindelsen til Silkeborg Løn aktiv?
4. Er brugeren logget ind?
5. Står lønsystemet i en dialog eller fejlmeddelelse, som skal lukkes?
6. Er de nødvendige felter udfyldt i det relevante batchark?
7. Matcher oplysningerne i batcharket den registrering, der findes på billede 102?

Hvis fejlen fortsætter, gem oplysninger om:

- Batcharket og rækken.
- Status eller fejltekst i statuskolonnen.
- Relevant tekst fra `Log`.
- Den viste fejltekst i lønsystemet.

## Download fra GitHub

1. Åbn projektets GitHub-side.
2. Vælg Excel-filen `Dist/Ret Tillaeg - 102.xlsm`.
3. Vælg download af den rå fil, eller hent repositoryets ZIP-fil.
4. Gem Excel-filen lokalt.
5. Fjern eventuel Windows-blokering som beskrevet ovenfor.
6. Åbn filen i Excel, og aktivér makroer, hvis filen er fra en betroet kilde.

## Versionscheck

Filen indeholder en knap til manuel versionskontrol. Knappen kører et versionscheck mod GitHub.

Hvis der findes en nyere version af filen, kan brugeren åbne downloadlinket fra beskeden.



