

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

### 4.1 Opprinnelig planlagt adresseplan for fysisk nettverk

| Enhet | IP-adresse | Subnettmaske | Gateway | Tildeling |
|---|---|---|---|---|
| R1 | 192.168.50.1 | 255.255.255.0 | Ikke aktuelt | Statisk |
| SERVER01 | 192.168.50.10 | 255.255.255.0 | 192.168.50.1 | Statisk |
| CLIENT01 | 192.168.50.20 i første test | 255.255.255.0 | 192.168.50.1 | Statisk først, deretter DHCP |

DHCP-området skal konfigureres på R1. Adresser som brukes av ruteren og serveren, skal unntas fra DHCP-tildeling.


### 4.2 Faktisk konfigurasjon av fysisk nettverk

Det fysiske nettverket bruker foreløpig TP-Link-ruteren og adresseområdet `192.168.0.0/24`.

| Enhet | IPv4-adresse | Nettverksmaske | Gateway | IP-tildeling |
|---|---|---|---|---|
| Ruter | `192.168.0.1` | `255.255.255.0` | Ikke aktuelt | Eksisterende konfigurasjon |
| SERVER01 | `192.168.0.2` | `255.255.255.0` | `192.168.0.1` | Statisk |
| CLIENT01 | `192.168.0.101` | `255.255.255.0` | `192.168.0.1` | DHCP |

På SERVER01 viste `ipconfig /all` at DHCP er deaktivert. Dette stemmer med at serveren bruker en statisk IP-adresse.

På CLIENT01 ble IPv4-innstillingene endret til automatisk tildeling av IP-adresse og DNS-serveradresse. Etterpå viste `ipconfig /all` følgende:

- DHCP aktivert: Ja
- DHCP-server: `192.168.0.1`
- IPv4-adresse: `192.168.0.101`
- Standard gateway: `192.168.0.1`

Dette bekrefter at klienten mottar nettverksinnstillinger automatisk fra TP-Link-ruteren. At klienten beholdt samme IPv4-adresse, betyr ikke at den fortsatt er statisk konfigurert.

**Avvik fra planlagt adresseplan:** Det fysiske nettverket bruker foreløpig `192.168.0.0/24`, mens den opprinnelige planen bruker `192.168.50.0/24`. Den faktiske konfigurasjonen dokumenteres separat fra den planlagte adressestrukturen. Den fysiske konfigurasjonen beholdes foreløpig uendret for å unngå å forstyrre det eksisterende nettverket. Den planlagte adressestrukturen brukes som utgangspunkt dersom det fysiske nettverket senere skal konfigureres etter planen. Packet Tracer-løsningen dokumenteres separat.

### 4.3 Testresultater for fysisk nettverk

Ping-testene ble utført fra CLIENT01.

| Test | Resultat | Status |
|---|---|---|
| Ping til gateway `192.168.0.1` | 4 sendt, 4 mottatt, 0 % pakketap | Bestått |
| Ping til SERVER01 `192.168.0.2` | 4 sendt, 4 mottatt, 0 % pakketap | Bestått |

Begge testene bekreftet at CLIENT01 hadde kontakt med gatewayen og serveren.



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

Jeg brukte Nmap fra CLIENT01 for å undersøke hvilke TCP-porter og tjenester som var tilgjengelige på SERVER01 (`192.168.0.2`). Jeg kjørte kommandoen:

```cmd
nmap -sV 192.168.0.2
```

Nmap rapporterte at verten var tilgjengelig, og identifiserte flere åpne TCP-porter.

| Port | Tjeneste | Observasjon |
|---|---|---|
| 53/tcp | DNS | DNS-tjeneste ble identifisert. |
| 88/tcp | Kerberos | Brukes til autentisering i Active Directory. |
| 389/tcp | LDAP | Nmap identifiserte LDAP knyttet til domenet `firma.local`. |
| 445/tcp | Microsoft-DS | Relevant for Windows-fil- og delingstjenester. |
| 5985/tcp | HTTPAPI | Nmap identifiserte Microsoft HTTPAPI, ofte knyttet til WinRM. |

Nmap identifiserte også vertsnavnet `DC01` og domenet `firma.local`. Dette støtter observasjonen om at serveren allerede er konfigurert som domenekontroller.

**Resultat:** Nmap-testen er gjennomført. Flere TCP-porter ble rapportert som åpne. 987 andre TCP-porter ble rapportert som filtrerte, det vil si at Nmap ikke mottok svar som gjorde det mulig å fastslå tilstanden deres.

**Vurdering:** Port 445 er spesielt relevant for prosjektet fordi klienten bruker den delte mappen `Felles`. Skanningen viser hvilke tjenester som er tilgjengelige fra klienten, men beviser ikke alene at tjenestene er sikkert konfigurert.

**Dokumentasjon:** Jeg skal legge til et skjermbilde av Nmap-resultatet i prosjektets `images`-mappe.

### 6.2 Wireshark – ICMP

Jeg startet en pakkefangst i Wireshark på Ethernet-grensesnittet og utførte en ping-test fra CLIENT01 til SERVER01 (`192.168.0.2`). Med visningsfilteret `icmp` fant jeg både Echo (ping) request og Echo (ping) reply.

- **ICMP request:** Source `192.168.0.101`, destination `192.168.0.2`.
- **ICMP reply:** Source `192.168.0.2`, destination `192.168.0.101`.
- **Lengde:** Wireshark viste 74 byte for pakken jeg undersøkte.

Dette viser at klienten sendte en ICMP-forespørsel til serveren, og at serveren sendte et svar tilbake. Resultatet samsvarer med ping-testen, som fikk svar fra serveren.

**Dokumentasjon:** Legg ved et skjermbilde fra Wireshark som viser både request og reply.

#### TCP og SMB-fildeling

Jeg startet en pakkefangst i Wireshark mens jeg åpnet den delte mappen `\\192.168.0.2\Felles` fra CLIENT01. Jeg brukte visningsfilteret `tcp && ip.addr == 192.168.0.2`.

Jeg observerte følgende TCP-pakker:

1. **SYN:** `192.168.0.101:53940` til `192.168.0.2:445`.
2. **SYN, ACK:** `192.168.0.2:445` til `192.168.0.101:53940`.
3. **ACK:** `192.168.0.101:53940` til `192.168.0.2:445`.

Dette er TCP three-way handshake. Port 445 brukes vanligvis til SMB-fildeling i Windows. Pakkene viser at klienten opprettet en TCP-forbindelse til serveren på denne porten.

Sammen med den vellykkede testen der jeg åpnet delingen og opprettet en testfil, støtter dette at nettverksforbindelsen og fildelingen fungerer.

**Dokumentasjon:** Legg ved et skjermbilde som viser de tre TCP-pakkene i Wireshark.



## 7. Packet Tracer – topologi og routing

Bedriftsnettverket skal representere FjordTek AS, med hovedkontor og salgsavdeling.

Nettverket skal bestå av minst to rutere, fire switcher, tre klientnett og et eget servernett.
### Nettverkstopologi
![Packet Tracer-nettverk](images/packet%20tracer%20nett.png)

### 7.1 Ping mellom PC-ADMIN og PC-SALES

Ping-testen bekreftet at PC-ADMIN kunne kommunisere med PC-SALES på tvers av nettverkene.

![Ping Admin til Sales](images/ping%20admin%20sales.png)

### 7.2 Traceroute fra PC-ADMIN

Traceroute viste veien gjennom R1 og R2 til servernettverket.

![Traceroute-test](images/tracing%20csico%20oppgave.png)

### 7.3 Planlagt adresseplan

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

### 7.4 Faktisk topologi og konfigurasjon

Packet Tracer-nettverket er bygget med to rutere, fire switcher, tre klientnett og ett eget servernett.

**Tilkoblinger**

- R1 G0/0 → SW1 – Administrasjon
- R1 G0/1 → SW2 – Support
- R1 G0/2 → R2 G0/0 – forbindelse mellom ruterne
- R2 G0/1 → SW3 – Servernett
- R2 G0/2 → SW4 – Salg

**IP-adresser på rutere**

| Ruter | Grensesnitt | IP-adresse |
|---|---|---|
| R1 | G0/0 | 192.168.10.1/24 |
| R1 | G0/1 | 192.168.20.1/24 |
| R1 | G0/2 | 10.0.0.1/30 |
| R2 | G0/0 | 10.0.0.2/30 |
| R2 | G0/1 | 192.168.30.1/24 |
| R2 | G0/2 | 192.168.40.1/24 |

Det er konfigurert statiske ruter mellom R1 og R2 for kommunikasjon mellom nettverkene.

DHCP er konfigurert for klientnettverkene. Serveren bruker IP-adressen `192.168.30.10/24`, med gateway `192.168.30.1`, og leverer DNS- og HTTP-tjenester.

![Packet Tracer-nettverk](images/packet%20tracer%20nett.png)

## 8. Simulation Mode

Simulation Mode skal brukes til å undersøke hvordan pakker beveger seg gjennom nettverket.

Jeg skal gjennomføre følgende undersøkelser:

1. Kommunikasjon mellom to klienter i samme LAN, med fokus på ARP og ICMP.
2. Kommunikasjon fra Administrasjon til Salg, med fokus på trafikken gjennom R1 og R2.
3. Åpning av intranettsiden med DNS-navn, med fokus på DNS-oppslag og HTTP-trafikk.

### DNS- og HTTP-test

DNS-navnet `intranett.fjordtek.local` ble brukt for å åpne intranettsiden fra en klient. I Simulation Mode ble DNS- og HTTP-trafikken undersøkt.

![DNS og HTTP i Packet Tracer](images/cisco%20dns%26http%20test.png)

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
| K02 | Kontroller SERVER01s IP-adresse | Fast adresse er konfigurert | Bestått: 192.168.0.2, DHCP deaktivert, statisk IP. |
| K03 | Forny DHCP-adresse på CLIENT01 | Klienten får gyldig IP og gateway | Bestått: DHCP aktivert, DHCP-server 192.168.0.1, klientadresse 192.168.0.101. |
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
