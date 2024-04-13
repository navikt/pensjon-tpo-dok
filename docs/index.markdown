---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: Tjenestepensjon
---
## Offentlig Tjenestepensjon

### Simulering
TP-ordninger må implementere et API som NAV (og evt andre) kan kalle for å få simulert offentlig tjenestepensjon for brukere født 
fra og med 1963, for to forskjellige formål:
* NAV trenger et simulert beløp for AFP Offentlig for å kunne beregne en simulert tidligst mulig uttak av alderspensjon fra folketrygden (i første omgang vil NAV beregne dette selv, men etterhvert vil det være ønskelig at dette hentes fra TP-ordninger)
* NAV vil vise hvor mye brukere får i offentlig tjenestepensjon i sin pensjonskalkulator

Det antas at begge disse behovene kan dekkes gjennom ett og samme API, der konsument av tjenesten kan spesifisere hvilke tjenestepensjonsytelser man vil ha simulert i input (feks AFP, påslag, alderspensjon opptjent før 2020)

Grovt sett kan NAV-kalkulator-prosessen illustreres som følger:
* Bruker logger på NAVs pensjonskalkulator, og velger selv om hen har rett til AFP Offentlig
* Bruker velger ønsket uttakstidpunkt (gradert og/eller helt) og evt uttaksgrad
* NAV beregner simulert AFP Offentlig (på kort sikt) om bruker spesifiserer at hen har rett til det
* NAV beregner simulert alderspensjon fra folketrygden
* NAV finner siste ordning (kall til ny SPK-tjeneste?), og kaller [Simuler Tjenstepensjon](api/offentligtjenestepensjon/v2/tjenestepensjon.html) på siste ordning.
* Ved simulering av tjenestepensjon gjør TP-ordninger kall tilbake til NAV for å hente nødvendig informasjon avhengig av hvilke tjenestepensjonsytelser som skal simuleres:
    * Ved simulering av AFP:
      * Kall [Hent Simulert AFPBeholdningGrunnlag](https://navikt.github.io/pensjon-ekstern-api/api/afpgrunnlagbeholdning/afp-grunnlag-beholdning.html)
      * Beregn simulert AFP basert på simulert AFPBeholdningGrunnlag
    * Ved simulering av Alderspensjon opptjent før 2020 (APOF2020/Oppsatt Brutto):
      * kall [Simuler Folketrygdbeholdning](https://pensjonssimulator.ekstern.dev.nav.no/swagger-ui/index.html#/beholdning-controller/simulerFolketrygdbeholdning)
      * Beregn APOF2020 basert på simulert folketrygdbeholdning
* NAV henter private rettigheter fra Norsk Pensjon
* NAV viser i pensjonskalkualtoren:
  * Pensjonsgivende inntekt
  * Alderspensjon fra folketrygden
  * AFP Offentlig
  * Offentlig tjenestepensjon (minus AFP Offentlig)
  * Private avtaler (fra Norsk Pensjon)

### APIer

Dokumentasjon av APIer som skal tilbys av tjenestepensjonsordninger

* [Offentlig Tjenestepensjon V1](api/offentligtjenestepensjon/v1/tjenestepensjon.html)
  * Beholdes inntil videre siden KLP har startet utvikling basert på denne
* [Offentlig Tjenestepensjon V2](api/offentligtjenestepensjon/v2/tjenestepensjon.html)
  * Endringer i V2:
    * Renamet noen felter for konsistens med andre simuleringstjenester
    * Endret struktur på utbetalingsliste så den er mer i henhold til tilsvarende tjeneste fra NAV (simuler alderspensjon fra folketrygden)
    * Fjernet årlig utbetaling