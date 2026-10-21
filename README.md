

**Fag:** IT / nettverk  
**Elev:** Thomas Lauritzen 
**Skole:** Kuben VGs
**Dato:** 10/20/2026  
**Status:** Under arbeid

## 1. Innledning og oppdragsbeskrivelse

Formålet med dette prosjektet er å bygge, undersøke og dokumentere et fysisk nettverk og deretter bruke de samme nettverksprinsippene til å bygge et større bedriftsnettverk i Cisco Packet Tracer.

Prosjektet består av to hoveddeler. I den første delen skal jeg koble sammen en ruter, en switch, en Windows Server og en bærbar Windows-klient. Jeg skal konfigurere IP-adresser, teste kommunikasjon, sette opp DHCP og undersøke en tjeneste på serveren.

I den andre delen skal jeg bygge et simulert bedriftsnettverk for FjordTek AS. Dette nettverket skal ha flere LAN, to rutere, routing mellom nettverkene og tjenestene DHCP, DNS og HTTP.

Jeg skal dokumentere planlegging, konfigurasjon, testing og feilsøking. Målet er å forstå hvordan fysisk tilkobling, IP-adresser, routing, porter og nettverkstjenester fungerer sammen.

### Avvik fra opprinnelig plan

Den opprinnelige planen var å bruke Windows Server som en standalone-server uten Active Directory eller domenetjenester. Under arbeidet oppdaget jeg at serveren allerede var konfigurert som domenekontroller for domenet `firma.local`.

Jeg valgte å beholde det eksisterende miljøet og bruke den delte mappen `Felles` til å teste kommunikasjon og filtilgang fra klient-PC-en. Under feilsøkingen undersøkte jeg også tilgangsrettigheter og en eksisterende Active Directory-sikkerhetsgruppe, `GG-informasjon-read`.

Dette innebærer at den fysiske delen av prosjektet avviker fra den opprinnelige planen. Jeg dokumenterer derfor den faktiske konfigurasjonen og resultatene fra testene, i stedet for å beskrive serveren som en standalone-server.

En videre forbedring vil være å begrense tilgangsrettighetene til relevante brukere eller grupper og kontrollere at de samsvarer med kravene til prosjektet.

## 2. Kravspesifikasjon

Kravspesifikasjonen beskriver hva nettverkene skal kunne gjøre når prosjektet er ferdig.

| ID | Krav | Testmetode |
|---|---|---|
| K01 | SERVER01 og CLIENT01 skal kommunisere i det fysiske labnettet. | Ping mellom klient og server. |
| K02 | SERVER01 skal ha fast IP-adresse. | Kontrollere IP-konfigurasjonen. |
| K03 | CLIENT01 skal motta nettverksinnstillinger fra R1 via DHCP. | Kontrollere med `ipconfig /all`. |
| K04 | CLIENT01 skal kunne bruke en tjeneste på SERVER01. | Åpne en delt mappe og opprette en testfil. |
| K05 | Packet Tracer-nettverket skal ha flere LAN og routing mellom dem. | Teste kommunikasjon mellom forskjellige LAN. |
| K06 | Packet Tracer-klientene skal få riktige nettverksinnstillinger og kunne bruke DNS og HTTP. | Kontrollere DHCP og åpne intranettsiden med DNS-navn. |
| K07 | Nettverksløsningene skal testes og dokumenteres. | Dokumentere tester og resultater fra begge delene. |
| K08 | Trafikken mellom CLIENT01 og SERVER01 skal undersøkes i Wireshark. | Fange opp ICMP- og TCP-trafikk og undersøke kilde- og mål-IP. |

Kravene skal kontrolleres på nytt i akseptansetesten mot slutten av prosjektet.

## 3. Fysisk nettverk – topologi og utstyr

### 3.1 Utstyr

Det fysiske nettverket skal bestå av:

- R1 – ruter
- SW1 – switch
- SERVER01 – Windows Server
- CLIENT01 – bærbar Windows-klient

### 3.2 Planlagt topologi

```text
SERVER01 ──┐
            ├── SW1 ─── R1
CLIENT01 ──┘
```

SERVER01 og CLIENT01 kobles til SW1. SW1 kobles videre til LAN-grensesnittet på R1.

Nettverket skal bruke et isolert labnett.

**Fysisk tilkobling og porter:** Fylles ut etter at utstyret er koblet til.

**Skjermbilde av fysisk topologi:** Legges til etter at nettverket er bygget.

## 4. Adresseplan og konfigurasjon

### 4.1 Planlagt adresseplan for fysisk nettverk

| Enhet | IP-adresse | Subnettmaske | Gateway | Tildeling |
|---|---|---|---|---|
| R1 | 192.168.50.1 | 255.255.255.0 | Ikke aktuelt | Statisk |
| SERVER01 | 192.168.50.10 | 255.255.255.0 | 192.168.50.1 | Statisk |
| CLIENT01 | 192.168.50.20 i første test | 255.255.255.0 | 192.168.50.1 | Statisk først, deretter DHCP |

DHCP-området skal konfigureres på R1. Adresser som brukes av ruteren og serveren, skal unntas fra DHCP-tildeling.

**Faktisk konfigurasjon og testresultater:** Fylles ut under arbeidet.

## 5. Server og tjenester

SERVER01 skal brukes som en standalone-server med fast IP-adresse.

I det fysiske nettverket bruker jeg en eksisterende mappe på Windows Server, INFORMASJON, som er delt med nettverksnavnet Felles. Klient-PC-en åpner mappen via \\192.168.0.2\Felles.

Jeg testet fildelingen fra klient-PC-en ved å åpne den delte mappen og forsøke å opprette et tekstdokument. Først fikk jeg en tillatelsesfeil. Under feilsøkingen endret jeg tilgangsrettighetene, og etter endringen kunne jeg opprette et tekstdokument fra klient-PC-en.

Testen viser at fildeling og skrivetilgang fungerer med de gjeldende rettighetene. Tilgangsrettighetene må begrenses til relevante brukere eller grupper før løsningen regnes som ferdig.

I Packet Tracer skal serveren bruke adressen `192.168.30.10/24` og levere DNS- og HTTP-tjenester.

DNS-navnet skal være:

`intranett.fjordtek.local`

Navnet skal peke til `192.168.30.10`.

**Resultater fra fildeling, DNS og HTTP:** Fylles ut etter testing.

## 6. Nmap, porter og Wireshark

### 6.1 Nmap

Jeg skal bruke Nmap fra CLIENT01 til å undersøke hvilke porter og tjenester som er tilgjengelige på SERVER01.

Jeg skal dokumentere én eller to relevante porter, med portnummer, tilstand og tjeneste. Undersøkelsen skal begrenses til labserveren.

### 6.2 Wireshark

Jeg skal bruke Wireshark til å undersøke trafikk mellom CLIENT01 og SERVER01.

Først skal jeg fange opp ICMP-trafikk ved hjelp av ping. Deretter skal jeg undersøke TCP-trafikk når klienten åpner den delte mappen.

Jeg skal sammenligne hva Nmap forteller om tilgjengelige tjenester, med hva Wireshark viser om trafikken som faktisk sendes mellom maskinene.

**Skjermbilder og faktiske observasjoner:** Legges til etter testene.

## 7. Packet Tracer – topologi og routing

Bedriftsnettverket skal representere FjordTek AS, med hovedkontor og salgsavdeling.

Nettverket skal bestå av minst to rutere, fire switcher, tre klientnett og et eget servernett.

### 7.1 Planlagt adresseplan

| Nettverk | Nettadresse | Planlagt gateway |
|---|---|---|
| Administrasjon | 192.168.10.0/24 | 192.168.10.1 |
| Support | 192.168.20.0/24 | 192.168.20.1 |
| Servere | 192.168.30.0/24 | 192.168.30.1 |
| Salg | 192.168.40.0/24 | 192.168.40.1 |
| Forbindelse R1–R2 | 10.0.0.0/30 | Fastsettes for hvert grensesnitt |

R1 skal håndtere nettverkene ved hovedkontoret, mens R2 skal håndtere salgsnettverket.

Det skal konfigureres statiske ruter mellom ruterne, slik at klienter i de forskjellige LAN-ene kan kommunisere.

DHCP skal gi klientene nettverksinnstillinger. Serveren skal levere DNS og HTTP.

**Faktisk topologi, grensesnitt, rutetabeller og konfigurasjoner:** Fylles ut etter bygging og testing.

## 8. Simulation Mode

Simulation Mode skal brukes til å undersøke hvordan pakker beveger seg gjennom nettverket.

Jeg skal gjennomføre følgende undersøkelser:

1. Kommunikasjon mellom to klienter i samme LAN, med fokus på ARP og ICMP.
2. Kommunikasjon fra Administrasjon til Salg, med fokus på trafikken gjennom R1 og R2.
3. Åpning av intranettsiden med DNS-navn, med fokus på DNS-oppslag og HTTP-trafikk.

Skjermbilder skal brukes til å vise relevante observasjoner og forklare hva de forteller om nettverket.

## 9. Feilsøking

### Feilsøking: Skrivetilgang til delt mappe

- **Symptom:** Klient-PC-en kunne åpne den delte mappen `Felles`, men fikk ikke tillatelse til å opprette et tekstdokument.
- **Hypotese:** Problemet skyldtes manglende tilgangsrettigheter på mappen eller delingen.
- **Test:** Jeg undersøkte delingsrettighetene og sikkerhetsrettighetene på den fysiske `INFORMASJON`-mappen på Windows Server.
- **Funn:** Klienten kunne lese mappen, men hadde ikke tilstrekkelig skrivetilgang.
- **Løsning:** Jeg utvidet rettighetene til alle under feilsøkingen. Etter endringen kunne klienten opprette et tekstdokument i den delte mappen.
- **Videre forbedring:** Jeg bør begrense rettighetene til relevante domenebrukere eller sikkerhetsgrupper og teste på nytt at riktig bruker har nødvendig tilgang.

## 10. Akseptansetest

Akseptansetesten skal kontrollere om kravene i kravspesifikasjonen er oppfylt.

| Krav | Test | Forventet resultat | Faktisk resultat |
|---|---|---|---|
| K01 | Ping mellom CLIENT01 og SERVER01 | Maskinene kommuniserer | 4/4 svar, 0 % pakketap |
| K02 | Kontroller SERVER01s IP-adresse | Fast adresse er konfigurert | Ikke testet |
| K03 | Forny DHCP-adresse på CLIENT01 | Klienten får gyldig IP og gateway | Ikke testet |
| K04 | Åpne Felles og opprette testfil | Filen kan opprettes på serveren | Bestått med gjeldende rettigheter. Klienten åpnet \\192.168.0.2\Felles og opprettet et tekstdokument. Rettighetene må sikres. |
| K05 | Ping mellom forskjellige Packet Tracer-LAN | Routing fungerer begge veier | Ikke testet |
| K06 | Test DHCP, DNS og HTTP | Klientene får riktige innstillinger og intranettet åpnes med DNS-navn | Ikke testet |
| K07 | Dokumenter testresultatene | Tester og observasjoner er dokumentert | Ikke testet |
| K08 | Fang opp ICMP- og TCP-trafikk i Wireshark | Relevant trafikk kan identifiseres | Ikke testet |

«Ikke testet» betyr at testen ennå ikke er gjennomført. Resultatene skal oppdateres når testene er utført.

## 11. Oppsummering og refleksjon

Dette prosjektet skal vise hvordan nettverksprinsipper fra et lite fysisk nettverk kan brukes i et større simulert bedriftsnettverk.

I refleksjonen skal jeg forklare sammenhengen mellom fysisk tilkobling, IP-adresser, gateway, routing, porter og tjenester.

Jeg skal også sammenligne det fysiske nettverket med Packet Tracer-løsningen, beskrive utfordringer jeg møtte, forklare hvordan feil ble funnet og rettet, og foreslå forbedringer.

Denne delen ferdigstilles når prosjektet og testene er gjennomført.

## 12. Prosjektstyring og filer

**GitHub Projects:** [Legg inn lenke til prosjektbrettet]

### Planlagt mappestruktur

```text
fjordtek-nettverksprosjekt/
├── README.md
├── packet-tracer/
│   └── fjordtek.pkt
├── images/
│   ├── fysisk-topologi.png
│   ├── fysisk-test.png
│   └── packet-tracer-topologi.png
└── docs/
    └── ekstra-dokumentasjon.md
```

Filene og bildene legges til etter hvert som arbeidet utføres. Ikke alle eksempelfilene i strukturen er opprettet ennå.
