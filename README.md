# Silkeborg Løn scripts

Dette repository indeholder Excel-filer til arbejde i Silkeborg Løn via IBM
Personal Communications.

Filerne er makroaktiverede Excel-filer (`.xlsm`). De bruges til at behandle data
i lønsystemet ud fra ark i Excel.

## Download

| Script | Excel-fil | Brugervejledning |
|---|---|---|
| Opret-Ret 101x | [Opret-Ret 101x.xlsm](dist/Opret-Ret%20101x.xlsm) | [Brugervejledning](docs/opret-ret-101x.md) |
| Ret Tillæg - 102 | [Ret Tillaeg - 102.xlsm](dist/Ret%20Tillaeg%20-%20102.xlsm) | [Brugervejledning](docs/ret-tillaeg-102.md) |

## Før du bruger filerne

Før du åbner en Excel-fil, skal følgende være klar:

- Microsoft Excel til Windows.
- IBM Personal Communications skal være startet.
- Excel og IBM Personal Communications skal begge være enten 32-bit eller
  64-bit.
- Der skal være en aktiv og indlogget forbindelse til Silkeborg Løn.
- Excel-filen skal være gemt i en lokal mappe.
- Makroer skal være tilladt.

## Hvis Windows blokerer filen

Hvis filen er hentet fra GitHub, kan Windows blokere den.

1. Luk Excel.
2. Højreklik på Excel-filen i Stifinder.
3. Vælg `Egenskaber`.
4. Markér eventuelt `Fjern blokering`.
5. Vælg `Anvend` og derefter `OK`.

Åbn derefter filen i Excel og aktivér makroer, hvis filen er fra en betroet
kilde.

## Versionscheck

Scriptfilerne indeholder en knap til manuel versionskontrol. Knappen køre
et versions check mod GitHub. Hvis der findes en nyere version, kan brugeren
åbne downloadlinket fra beskeden.
