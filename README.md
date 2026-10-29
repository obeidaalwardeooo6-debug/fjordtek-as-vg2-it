# Nettverksprosjekt – Vg2 IT

## 1. Om prosjektet

I dette prosjektet har jeg jobbet med å sette opp, konfigurere og teste nettverk. Prosjektet består av to deler: et fysisk nettverk på skolen og et større nettverk i Cisco Packet Tracer.

I den første delen brukte jeg en TP-Link-ruter, en switch, en Windows Server-PC (SERVER01) og ASUS-laptopen min (CLIENT01). Jeg koblet sammen utstyret, satte opp IP-adresser og testet om enhetene kunne kommunisere med hverandre.

I den andre delen laget jeg et nettverk for en fiktiv bedrift som heter FjordTek AS. Jeg delte nettverket inn i forskjellige avdelinger: Administrasjon, Support, Servere og Salg. Her jobbet jeg blant annet med VLAN, ruting, DHCP, DNS og HTTP.

### 1.1 Målet mitt

Målet mitt var å forstå bedre hvordan et nettverk fungerer, både i praksis og i en simulering.

Jeg ville lære hvordan jeg kan koble sammen forskjellige enheter, konfigurere riktige IP-adresser og sørge for at klientene får kontakt med serveren.

Jeg ønsket også å bli bedre på feilsøking. Når noe ikke fungerte, prøvde jeg å finne ut hva som var feil, teste en mulig løsning og kontrollere resultatet etterpå.

### 1.2 Dokumentasjon

Jeg bruker GitHub til å samle arbeidet mitt. Her skal jeg vise hvordan jeg planla nettverket, hvilke innstillinger jeg brukte, hvilke tester jeg gjennomførte, og hvilke problemer jeg møtte underveis.

Jeg legger også ved skjermbilder fra testene og forklarer hva resultatene betyr.

Hvis jeg mangler et skjermbilde eller ikke har fått testet noe ferdig, skal jeg skrive det tydelig i dokumentasjonen.


## 2. Kravspesifikasjon

Før jeg startet med nettverket, måtte jeg lage en kravspesifikasjon. Den skulle vise hva nettverket måtte kunne gjøre når prosjektet var ferdig.

Jeg tok utgangspunkt i kravene fra læreren og valgte ett ekstra krav selv.

| Krav | Hva nettverket skal kunne gjøre | Hvordan jeg skal teste det |
|---|---|---|
| K1 | SERVER01 og CLIENT01 skal kunne kommunisere i det fysiske nettverket. | Jeg bruker `ping` fra CLIENT01 til SERVER01 og kontrollerer om jeg får svar. |
| K2 | SERVER01 skal ha fast IP-adresse. | Jeg kontrollerer IP-innstillingene med `ipconfig /all`. |
| K3 | CLIENT01 skal få IP-adresse automatisk fra ruteren med DHCP. | Jeg bruker `ipconfig /all` for å sjekke IP-adresse, gateway og DHCP-server. |
| K4 | CLIENT01 skal kunne bruke minst én tjeneste på SERVER01. | Jeg åpner den delte mappen `Felles` fra CLIENT01 og tester om jeg kan opprette en fil. |
| K5 | Nettverket i Cisco Packet Tracer skal ha flere LAN med ruting mellom dem. | Jeg tester med `ping`, `tracert` og `show ip route`. |
| K6 | Klientene i Packet Tracer skal få riktige nettverksinnstillinger og kunne bruke DNS og HTTP. | Jeg kontrollerer DHCP og tester om intranettsiden kan åpnes med IP-adresse og DNS-navn. |
| K7 | Nettverkene skal testes, og resultatene skal dokumenteres. | Jeg samler testresultater, skjermbilder og forklaringer i GitHub. |
| K8 | CLIENT01 skal kunne fornye IP-adressen med DHCP og fortsatt ha kontakt med SERVER01. | Jeg bruker `ipconfig /release`, `ipconfig /renew` og tester deretter forbindelsen med `ping`. |

### 2.1 Hvorfor jeg valgte K8

Jeg valgte DHCP-fornyelse som mitt eget krav fordi jeg ville undersøke hva som skjer når en klient ber om en ny IP-adresse.

Det er viktig at en PC får riktige nettverksinnstillinger automatisk og fortsatt kan kommunisere med andre enheter etter at adressen er fornyet.

### 2.2 Hvordan jeg skal kontrollere kravene

Når jeg har dokumentert oppsettet og testene, skal jeg sammenligne resultatene med kravspesifikasjonen.

I akseptansetesten (QA) skal jeg skrive hva jeg forventet, hva som faktisk skjedde, og om testen ble bestått.

Hvis jeg mangler dokumentasjon på et krav, skal jeg skrive det tydelig i stedet for å markere det som fullt dokumentert.


## 3. Fysisk nettverk – topologi og utstyr

### 3.1 Utstyret jeg brukte

Jeg startet prosjektet med å bygge et fysisk nettverk på skolen. Jeg brukte en ruter, en switch, en Windows Server-PC og min egen ASUS-laptop.

| Navn | Utstyr | Funksjon |
|---|---|---|
| R1 | TP-Link Archer C50 V4 | Ruter og DHCP-server |
| SW1 | TP-Link TL-SG105 | Switch med fem porter |
| SERVER01 | Stasjonær PC med Windows Server | Server for blant annet fildeling |
| CLIENT01 | ASUS-laptop med USB-Ethernet-adapter | Klient for konfigurering og testing |

SERVER01 fikk senere installert Windows Server 2025 Standard på nytt.

### 3.2 Hvordan jeg koblet utstyret

Den 20.10.2026 dokumenterte jeg hvordan utstyret var koblet sammen.

Jeg koblet både SERVER01 og CLIENT01 til SW1 med Ethernet-kabler. Deretter koblet jeg ruteren til switchen.

Dette var portene og kablene jeg brukte:

| Fra | Port | Til | Port | Kabel |
|---|---|---|---|---|
| Vegguttak | Ethernet | R1 | Internet/WAN | Grønn |
| R1 | LAN 3 | SW1 | Port 1 | Grå/sølv |
| SW1 | Port 2 | SERVER01 | Ethernet | Gul |
| SW1 | Port 3 | CLIENT01 | USB-Ethernet | Gul |

Læreren hadde godkjent at jeg brukte den grønne kabelen fra skolens nettverksuttak til WAN-porten på R1.

Jeg brukte ikke WAN-porten for å koble til switchen. Den lokale forbindelsen mellom R1 og SW1 gikk gjennom LAN 3.

### 3.3 Nettverkstopologi

Nettverket var koblet slik:

```mermaid
flowchart TD
    A["Vegguttak på skolen"] -->|"Grønn kabel – WAN"| B["R1 – TP-Link Archer C50 V4"]
    B -->|"LAN 3 – grå kabel"| C["SW1 – TP-Link TL-SG105, port 1"]
    C -->|"Port 2 – gul kabel"| D["SERVER01 – Windows Server"]
    C -->|"Port 3 – gul kabel"| E["CLIENT01 – ASUS-laptop"]
```

Diagrammet viser den fysiske oppkoblingen. Både klienten og serveren var koblet til samme switch.

### 3.4 Kontroll av fysisk tilkobling

Etter at kablene var koblet til, kontrollerte jeg utstyret og statuslysene.

Jeg observerte grønne lys ved de tilkoblede switchportene. Dette tydet på at det var fysisk link mellom enhetene.

Jeg tok bilder av ruteren, switchen, kablene og arbeidsplassen for å dokumentere oppkoblingen.

Jeg lærte at linklys ikke alene betyr at nettverket fungerer riktig. Lysene viser fysisk forbindelse, men IP-adresser og kommunikasjon må testes separat.

Derfor gjennomførte jeg senere ping-tester mot R1 og SERVER01.

### 3.5 Hvorfor jeg valgte denne oppkoblingen

Jeg koblet både klienten og serveren til switchen fordi de skulle kommunisere i samme lokale nettverk.

R1 hadde LAN-adressen `192.168.50.1` og skulle fungere som standard gateway og DHCP-server for CLIENT01.

SERVER01 skulle ha fast IP-adresse, slik at klienten kunne nå serveren på en kjent adresse.

Jeg brukte USB-Ethernet-adapteren på CLIENT01 for å koble laptopen til det kablede labnettet.

Ved å skrive ned hvilke porter og kabler jeg hadde brukt, ble det enklere å kontrollere oppkoblingen og finne feil senere.

### 3.6 Dokumentasjon

Jeg har tatt bilder av den fysiske oppkoblingen, blant annet:

- Nettverkstopologien
- Ruterens kabeltilkoblinger
- Switchportene og linklysene
- Ruteren og statuslysene
- Arbeidsplassen med SERVER01 og CLIENT01
- Oversikt over nettverksutstyret

Bildene viser hvordan utstyret var koblet opp. Selve IP-kommunikasjonen dokumenterer jeg gjennom de separate nettverkstestene.


### 3.1 Bilder av det fysiske nettverket

Jeg dokumenterte oppkoblingen av det fysiske nettverket med bilder. Bildene viser utstyret jeg brukte, hvordan enhetene var koblet sammen, og hvordan arbeidsplassen var satt opp.

**Nettverkstopologi**

![Topologi for det fysiske nettverket](images/fysisk-nettverk/01-fysisk-nettverkstopologi.png)

**R1 – Ruter og kabeltilkoblinger**

![R1 med kabeltilkoblinger](images/fysisk-nettverk/02-r1-ruter-tilkoblinger.jpg)

**SW1 – Switchporter og linklys**

![SW1 med porter og linklys](images/fysisk-nettverk/03-sw1-porter-og-linklys.jpg)

**R1 – Statuslys**

![Statuslys på R1](images/fysisk-nettverk/04-r1-ruter-statuslys.jpg)

**SERVER01 og CLIENT01 – Arbeidsplass**

![Arbeidsplass med server og klient](images/fysisk-nettverk/05-server-klient-arbeidsplass.jpg)

**Oversikt over nettverksutstyret**

![Oversikt over fysisk nettverksutstyr](images/fysisk-nettverk/06-nettverksutstyr-oversikt.jpg)

Bildene dokumenterer den fysiske oppkoblingen. For å kontrollere at kommunikasjonen fungerte, brukte jeg egne tester med blant annet ping, Nmap og Wireshark. Disse resultatene er dokumentert i de neste kapitlene.


## 4. IP-adresseplan og konfigurering

### 4.1 Adresseplan

Jeg brukte nettverket `192.168.50.0/24` i den fysiske laben. Subnettmasken var `255.255.255.0`.

| Enhet | IP-adresse | Gateway | Tildeling |
|---|---|---|---|
| R1 | 192.168.50.1 | – | Fast LAN-adresse |
| SERVER01 | 192.168.50.10 | 192.168.50.1 | Statisk |
| CLIENT01 – manuell test | 192.168.50.20 | 192.168.50.1 | Statisk |
| CLIENT01 – DHCP-test | 192.168.50.100 | 192.168.50.1 | DHCP |

Jeg ville ha en fast adresse på serveren, slik at klienten alltid kunne bruke samme IP-adresse for å få tilgang til den.

På CLIENT01 testet jeg først manuell IP-konfigurasjon og senere DHCP.

### 4.2 Konfigurering av R1 – 20.10.2026

Jeg brukte en TP-Link Archer C50 V4 som ruter.

Da jeg først åpnet administrasjonssiden, hadde ruteren LAN-adressen `192.168.0.1`.

Jeg endret adressen til `192.168.50.1`, slik at den passet med adresseplanen min.

Etter endringen mistet CLIENT01 midlertidig tilgang til administrasjonssiden fordi nettverksinnstillingene på klienten ikke lenger passet med den nye adressen.

Jeg brukte derfor:

```cmd
ipconfig /release
ipconfig /renew
```

Etterpå fikk klienten IP-adressen `192.168.50.100`, og jeg kunne åpne administrasjonssiden på `http://192.168.50.1`.

Jeg testet også forbindelsen med `ping 192.168.50.1` og fikk svar.

### 4.3 Kontroll av DHCP på ruteren

Jeg åpnet **DHCP → DHCP Settings** på ruteren og kontrollerte innstillingene.

| Innstilling | Verdi |
|---|---|
| DHCP Server | Enabled |
| Start IP Address | 192.168.50.100 |
| End IP Address | 192.168.50.199 |
| Lease Time | 120 minutter |
| Default Gateway | 192.168.50.1 |
| DNS Server | 0.0.0.0 |
| Secondary DNS Server | 0.0.0.0 |

DHCP var allerede aktivert med riktig adresseområde da jeg kontrollerte denne siden. Jeg trengte derfor ikke å endre innstillingene.

DNS-feltene hadde ingen eksplisitt DNS-adresse i ruterens oppsett.

Jeg brukte DHCP-området fra `.100` til `.199` for å holde adressene til ruteren og serveren utenfor området som ble delt ut automatisk.

### 4.4 Statisk IP på SERVER01

Jeg jobbet med å sette SERVER01 til:

- IP-adresse: `192.168.50.10`
- Subnettmaske: `255.255.255.0`
- Default gateway: `192.168.50.1`

Jeg brukte `ncpa.cpl` for å åpne nettverkstilkoblingene og endre IPv4-innstillingene.

Serveren ble senere installert på nytt med Windows Server 2025. Etter reinstallasjonen fikk jeg igjen svar fra `192.168.50.10`, men jeg mangler en fullstendig `ipconfig /all` fra SERVER01 som bekrefter alle innstillingene i den siste installasjonen.

Derfor er serverens endelige IP-konfigurasjon ikke fullt dokumentert med et eget skjermbilde.

### 4.5 Første test med statisk IP på CLIENT01 – 21.10.2026

Jeg testet CLIENT01 med disse innstillingene:

| Innstilling | Verdi |
|---|---|
| IPv4-adresse | 192.168.50.20 |
| Subnettmaske | 255.255.255.0 |
| Default gateway | 192.168.50.1 |

Jeg kontrollerte adressene med `ipconfig`.

Deretter kjørte jeg:

```cmd
ping 192.168.50.1
ping 192.168.50.10
```

Jeg fikk fire svar fra både ruteren og serveren, uten pakketap.

Dette viste at klienten kunne kommunisere med begge enhetene med den manuelle IP-konfigurasjonen.

### 4.6 DHCP-test på CLIENT01 – 21.10.2026

Etter testen med statisk IP satte jeg Ethernet 2 på CLIENT01 tilbake til automatisk IP-adressering.

Jeg brukte USB-Ethernet-adapteren **ASIX AX88179 USB 3.0 to Gigabit Ethernet Adapter**.

Deretter kontrollerte jeg innstillingene med:

```cmd
ipconfig /all
```

Dette var resultatet:

| Innstilling | Resultat |
|---|---|
| DHCP Enabled | Yes |
| IPv4 Address | 192.168.50.100 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.50.1 |
| DHCP Server | 192.168.50.1 |
| DNS Servers | 192.168.50.1 |
| Lease Obtained | 21.10.2026 kl. 09:57:21 |
| Lease Expires | 21.10.2026 kl. 11:57:20 |

Her kunne jeg bekrefte at CLIENT01 fikk adressen fra R1.

Leietiden var omtrent 120 minutter, som stemte med DHCP-innstillingene på ruteren.

### 4.7 Kontroll av forbindelsen etter DHCP

Etter DHCP-testen kjørte jeg Ping på nytt:

```cmd
ping 192.168.50.1
ping 192.168.50.10
```

Resultatene var:

| Mål | Sendt | Mottatt | Pakketap |
|---|---|---|---|
| R1 – 192.168.50.1 | 4 | 4 | 0 % |
| SERVER01 – 192.168.50.10 | 4 | 4 | 0 % |

Jeg fikk svar fra begge enhetene etter overgangen til DHCP.

Dette viste at klienten hadde fått riktige nettverksinnstillinger og fortsatt kunne kommunisere med ruteren og serveren.

### 4.8 Hva jeg lærte

Jeg lærte forskjellen mellom statisk IP og DHCP.

Med statisk IP måtte jeg skrive inn adressen selv. Med DHCP fikk klienten automatisk IP-adresse, gateway og DNS-innstillinger.

Jeg lærte også at når jeg endrer LAN-adressen på en ruter, må klientens nettverksinnstillinger passe med det nye subnettet.

Testene mine viste at DHCP fungerte, men jeg har ikke ett sammenhengende skjermbilde som viser hele prosessen fra `ipconfig /release` og `ipconfig /renew` til den siste Ping-testen.

Jeg har derfor dokumentert de registrerte testene og resultatene hver for seg.

## 5. Testing av fysisk nettverk

Etter at jeg hadde koblet sammen utstyret og konfigurert IP-adressene, begynte jeg å teste nettverket.

Jeg ville først finne ut om CLIENT01 hadde fått riktige nettverksinnstillinger. Deretter testet jeg om klienten kunne kommunisere med ruteren og SERVER01.

### 5.1 Kontroll av IP-adresse

På CLIENT01 brukte jeg kommandoen:

`ipconfig /all`

Jeg brukte denne kommandoen for å kontrollere IP-adressen, subnettmasken og standard gateway.

CLIENT01 fikk IP-adressen `192.168.50.100`, som ligger innenfor DHCP-området jeg hadde konfigurert på ruteren.

Dette viste at klienten hadde fått en adresse fra riktig nettverk.

### 5.2 Ping til ruteren

Jeg åpnet kommandolinjen (CMD) på CLIENT01 og kjørte:

`ping 192.168.50.1`

Jeg ville kontrollere at klienten hadde kontakt med ruteren på det lokale nettverket.

**Resultat fra testen:**
- Pakker sendt: 4
- Pakker mottatt: 4
- Pakketap: 0 %

Ruteren svarte på alle fire forespørslene. Dette viste at CLIENT01 kunne nå ruteren med ICMP.

### 5.3 Ping til SERVER01

Etterpå testet jeg forbindelsen mellom klienten og serveren:

`ping 192.168.50.10`

SERVER01 hadde IP-adressen `192.168.50.10`.

**Resultat fra testen:**
- Pakker sendt: 4
- Pakker mottatt: 4
- Pakketap: 0 %

Jeg fikk svar fra SERVER01 på alle fire forespørslene.

Dette viste at klienten og serveren kunne kommunisere med ICMP da testen ble gjennomført. Det betydde ikke nødvendigvis at alle tjenestene på serveren fungerte. Derfor måtte jeg teste fildeling separat.

### 5.4 Hva jeg fant ut

Resultatene viste at den grunnleggende nettverksforbindelsen fungerte på tidspunktet jeg testet.

Jeg hadde fått riktig IP-adresse på klienten, og jeg kunne nå både ruteren og serveren.

Jeg lærte også at en vellykket ping ikke betyr at alle nettverkstjenester fungerer. En server kan svare på ping selv om for eksempel fildeling er blokkert av brannmuren.

Dette ble viktig senere da jeg jobbet med SMB og Windows-brannmuren.

### 5.5 Begrensning i dokumentasjonen

Jeg har testresultater fra arbeidet med det fysiske nettverket. Windows Server ble senere installert på nytt, og jeg har ikke en fullstendig sluttkontroll av serverens nettverksinnstillinger etter reinstallasjonen.

Derfor skiller jeg mellom testene jeg faktisk gjennomførte, og det jeg ikke fikk kontrollert på nytt.


## 6. SERVER01 og SMB-fildeling

### 6.1 Oppsett av fildeling

Jeg brukte SERVER01 som en filserver i det fysiske nettverket.

Målet var at CLIENT01 skulle kunne åpne en delt mappe på serveren og bruke den over nettverket.

Jeg opprettet en mappe med navnet `Felles` og jobbet med delingsinnstillinger og en lokal testbruker på Windows Server.

Fra CLIENT01 brukte jeg denne adressen i Filutforsker:

`\\192.168.50.10\Felles`

Adressen viser IP-adressen til serveren og navnet på den delte mappen.

### 6.2 Kontroll av SMB-forbindelsen – 22.10.2026

Jeg brukte PowerShell på CLIENT01 for å kontrollere forbindelsen:

```powershell
Get-SmbConnection
```

Resultatet jeg dokumenterte i arbeidsloggen, viste:

- ServerName: `192.168.50.10`
- ShareName: `Felles`
- En aktiv SMB-forbindelse

Jeg kunne også se at forbindelsen brukte påloggingsinformasjon knyttet til SERVER01.

Dette viste at klienten hadde en SMB-forbindelse til den delte mappen.

### 6.3 Test av fildeling

Jeg skrev i arbeidsloggen at jeg hadde opprettet og kontrollert en testfil på serveren.

Jeg mangler likevel et eget skjermbilde som viser filen på SERVER01. Jeg har heller ikke et fullstendig bilde av de endelige Share- og NTFS-tillatelsene etter reinstallasjonen.

Derfor kan jeg dokumentere SMB-forbindelsen, men ikke hele filtesten med direkte skjermbildebevis.

### 6.4 SMB og Windows Firewall

SMB brukes til fildeling i Windows og benytter vanligvis TCP-port 445.

Jeg undersøkte senere porten med Nmap og så at `445/tcp` var åpen.

Etter reinstallasjonen hadde jeg også et problem med ping mot SERVER01. Jeg undersøkte Windows Firewall og aktiverte en regel for ICMPv4 Echo Requests.

Dette var en egen ICMP-feil, som jeg har beskrevet i kapittel 11. Jeg har ikke grunnlag for å si at den samme regelen løste SMB-problemer.

### 6.5 Hva jeg lærte

Jeg lærte hvordan en klient kan bruke en delt mappe på en Windows-server.

Jeg forstod også at det er forskjell på å ha nettverkskontakt med serveren, å ha en aktiv SMB-forbindelse og å ha tillatelse til å lese eller skrive filer.

En åpen TCP-port eller en aktiv forbindelse er ikke alene bevis på at alle filoperasjoner fungerer.


## 7. Nmap og nettverksporter

### 7.1 Hvorfor jeg brukte Nmap

Etter at jeg hadde jobbet med fildeling, ville jeg undersøke hvilke TCP-porter jeg kunne nå på SERVER01.

Jeg brukte Nmap fra CLIENT01 og begrenset skanningen til labserveren `192.168.50.10`.

Før skanningen kontrollerte jeg at Nmap var installert ved å bruke:

```powershell
nmap --version
```

Jeg hadde Nmap 7.99 og Npcap 1.88 installert på klienten.

### 7.2 Portskanning – 22.10.2026

Jeg kjørte denne kommandoen i PowerShell:

```powershell
nmap -sT -Pn -p 135,139,445 192.168.50.10
```

Jeg brukte:

- `-sT` for TCP Connect-skanning.
- `-Pn` for å hoppe over vanlig vertoppdagelse.
- `-p` for å velge bare portene 135, 139 og 445.

Skanningen ble gjennomført klokken 10:57 og tok 3,59 sekunder.

### 7.3 Resultatet jeg fikk

| Port | State | Service |
|---|---|---|
| 135/tcp | open | msrpc |
| 139/tcp | filtered | netbios-ssn |
| 445/tcp | open | microsoft-ds |

Port 135 var åpen. Denne porten brukes vanligvis av Microsoft RPC.

Port 139 ble vist som `filtered`. Det betyr at Nmap ikke fikk nok informasjon til å avgjøre om porten var åpen eller lukket. Filtrering i nettverket eller en brannmur kan være en forklaring.

Port 445 var åpen. Dette var spesielt relevant fordi jeg hadde brukt SMB til å koble CLIENT01 til mappen `Felles`.

### 7.4 Hvordan jeg tolket resultatet

Nmap viste også `Host is up`, men jeg brukte `-Pn`, som hopper over vanlig vertoppdagelse. Derfor brukte jeg ikke denne meldingen som bevis på en egen vellykket ping-test.

Tjenestenavnene i Nmap-resultatet var standard navn knyttet til portnumrene. Jeg brukte ikke `-sV` for å undersøke hvilken programvareversjon som kjørte på portene.

Jeg lærte at Nmap kan vise om en TCP-port er tilgjengelig, men at det ikke automatisk betyr at jeg har tilgang til tjenesten.

For eksempel viste Nmap at port 445 var åpen, men dette beviste ikke hvilke Share- eller NTFS-tillatelser brukeren hadde.

### 7.5 Hva jeg lærte

Jeg fikk bedre forståelse av sammenhengen mellom IP-adresser, porter og nettverkstjenester.

Jeg lærte også forskjellen mellom `open` og `filtered`, og hvorfor det er viktig å tolke resultatene riktig.

Jeg gjorde ingen endringer i brannmurinnstillingene som følge av denne skanningen.


## 8. Wireshark – analyse av nettverkstrafikk

### 8.1 Hvorfor jeg brukte Wireshark

Den 22.10.2026 brukte jeg Wireshark på CLIENT01 for å undersøke trafikken mellom klienten og SERVER01.

Jeg hadde allerede testet forbindelsen med ping og undersøkt porter med Nmap. Nå ville jeg se hvilke pakker som faktisk ble sendt mellom maskinene.

Jeg brukte nettverksadapteren `Ethernet 2` til pakkefangsten.

### 8.2 ICMP – trafikk fra ping

Jeg undersøkte ICMP-trafikk med dette visningsfilteret:

`icmp`

I Wireshark fant jeg åtte ICMP-pakker mellom:

- CLIENT01: `192.168.50.100`
- SERVER01: `192.168.50.10`

Det var fire Echo Request og fire Echo Reply, med sekvensnumrene 37, 38, 39 og 40.

Pakkene hadde en lengde på 74 byte i Wireshark.

Jeg sammenlignet Source og Destination for å se hvordan trafikken gikk mellom klienten og serveren.

**Resultat:**

Jeg kunne se både forespørsler og svar. Det viste at ICMP-kommunikasjonen fungerte i denne pakkefangsten.

Bildet viste fire forespørsel-/svarpar, men ikke den fullstendige statistikken fra ping-kommandoen. Derfor bruker jeg ikke dette bildet alene til å dokumentere pakketapsprosenten.

### 8.3 SMB – trafikk på TCP-port 445

Etterpå undersøkte jeg trafikken i forbindelse med den delte mappen `Felles`.

Jeg brukte dette filteret:

`ip.addr == 192.168.50.10 && tcp.port == 445`

Filteret viste TCP-trafikk der SERVER01 var avsender eller mottaker, og der kilde- eller målporten var 445.

I pakkefangsten kunne jeg se kommunikasjon mellom CLIENT01 og SERVER01.

En av pakkene viste:

- Source IP: `192.168.50.100`
- Destination IP: `192.168.50.10`
- TCP Source Port: `34647`
- TCP Destination Port: `445`

Jeg kunne også se TCP-trafikk i motsatt retning, fra port 445 på serveren til port 34647 på klienten.

Wireshark viste flere pakker med protokolletiketten `SMB2`.

### 8.4 SMB-meldingene jeg fant

Jeg kunne blant annet se disse meldingene:

| Melding | Hva den viser |
|---|---|
| Create Request | Klienten sender en forespørsel om å åpne et objekt |
| Create Response | Serveren svarer på forespørselen |
| Close Request | Klienten ber om å lukke et objekt |
| Close Response | Serveren svarer på lukkingen |
| Cancel Request | En forespørsel blir forsøkt avbrutt |
| Notify Response – STATUS_CANCELLED | En forespørsel om varsling er blitt avbrutt |

Jeg la merke til at noen av pakkene viste `Create Request, File: <share>`.

Dette var interessant fordi det viste SMB-kommandoer knyttet til delingen. Men det var ikke et bevis på at jeg hadde opprettet en vanlig testfil i mappen.

Jeg så også `STATUS_CANCELLED`. Jeg kunne ikke konkludere med at fildelingen hadde feilet bare på grunn av denne meldingen.

### 8.5 Forskjellen mellom Nmap og Wireshark

Jeg brukte Nmap for å undersøke hvilke TCP-porter som var tilgjengelige på SERVER01.

Nmap viste blant annet at port 445 var åpen.

Med Wireshark kunne jeg se selve kommunikasjonen som brukte port 445, inkludert TCP-pakker og SMB-meldinger.

Jeg lærte at Nmap og Wireshark brukes til forskjellige oppgaver. Nmap undersøker blant annet porttilgjengelighet, mens Wireshark gjør det mulig å analysere nettverkspakker.

### 8.6 Hva jeg lærte

Denne oppgaven gjorde det lettere for meg å forstå hvordan klienten og serveren kommuniserer.

Jeg lærte å bruke visningsfiltre for å finne relevante pakker og å undersøke IP-adresser, porter og protokoller.

Jeg lærte også at jeg må være forsiktig når jeg tolker resultater. SMB-trafikk viser at det foregår kommunikasjon, men beviser ikke alene at en bestemt fil ble lagret, eller hvilke rettigheter brukeren har.

Jeg har dokumentert ICMP- og SMB-trafikken med skjermbilder fra den fysiske laben. Bildene skal legges til i den nye dokumentasjonen senere.



## 9. Tracert og nettverksvei

### 9.1 Hvorfor jeg brukte tracert

Jeg brukte `tracert` for å undersøke veien fra CLIENT01 til SERVER01.

Målet var å finne ut om trafikken gikk direkte i det lokale nettverket, eller om den måtte gå gjennom en ruter.

### 9.2 Testen jeg gjennomførte

Den 22.10.2026 kjørte jeg denne kommandoen fra CLIENT01:

```powershell
tracert -d 192.168.50.10
```

Jeg brukte `-d` for å unngå navneoppslag og vise IP-adressene direkte.

Resultatet var:

```text
Tracing route to 192.168.50.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms    192.168.50.10

Trace complete.
```

### 9.3 Hva resultatet viste

Jeg fikk bare ett hopp, og det var direkte til SERVER01 på `192.168.50.10`.

Dette stemte med adresseplanen min:

- CLIENT01: `192.168.50.100/24`
- SERVER01: `192.168.50.10/24`

Begge maskinene lå i samme subnett. Derfor trengte ikke trafikken å gå via R1.

SW1 videresendte trafikken mellom maskinene, men switchen ble ikke vist som et eget ruterhopp i tracert.

Svartiden var omtrent 1 ms eller mindre.

### 9.4 Hva jeg lærte

Jeg lærte at to enheter i samme lokale IP-nettverk normalt kan kommunisere uten å bruke standard gateway.

I Packet Tracer bygde jeg senere flere nettverk. Der måtte trafikken mellom forskjellige subnett gå gjennom enheter som utførte ruting.

Dette gjorde det lettere for meg å forstå forskjellen mellom kommunikasjon i samme LAN og kommunikasjon mellom forskjellige nettverk.
Jeg forstod også bedre hvorfor det er viktig å vite om to enheter ligger i samme subnett før jeg begynner med feilsøking.

`ping` hjalp meg å teste om enheten svarte, mens `tracert` ble brukt for å undersøke veien mot målet.


## 10. Systematisk fysisk feilsøking

### 10.1 Kontrollert nettverksfeil – 22.10.2026

I denne oppgaven skulle jeg lage en kontrollert feil i det fysiske nettverket og undersøke hvordan jeg kunne finne og rette problemet.

Jeg valgte å koble fra hele USB-Ethernet-adapteren på CLIENT01. Jeg lot Ethernet-kabelen være koblet til adapteren, men tok selve adapteren ut av laptopen.

Jeg gjorde ingen endringer på R1 eller SERVER01.

### 10.2 Kontroll før feilen

Før jeg koblet fra adapteren, testet jeg forbindelsen til SERVER01.

Jeg kjørte:

```powershell
ping 192.168.50.10
```

Resultatet var fire sendte og fire mottatte pakker, med 0 % pakketap.

Deretter kontrollerte jeg nettverksadapteren:

```powershell
Get-NetAdapter -Name "Ethernet 2" | Format-List Name,Status,LinkSpeed
```

Resultatet viste:

- Name: `Ethernet 2`
- Status: `Up`
- LinkSpeed: `1 Gbps`

Da visste jeg at nettverksadapteren var aktiv, og at klienten kunne kommunisere med serveren.

### 10.3 Jeg laget feilen

Jeg tok USB-Ethernet-adapteren ut av CLIENT01.

Etterpå kjørte jeg den samme kommandoen med `Get-NetAdapter`.

Denne gangen fikk jeg feilmeldingen `ObjectNotFound`. Windows fant ikke lenger en nettverksadapter med navnet `Ethernet 2`.

Jeg testet også forbindelsen til SERVER01 på nytt:

```powershell
ping 192.168.50.10
```

Resultatet var fire meldinger med:

`PING: transmit failed. General failure.`

Ingen av de fire ping-forsøkene lyktes, og Windows rapporterte 100 % pakketap.

### 10.4 Hvordan jeg fant årsaken

Jeg ville undersøke om problemet var knyttet til nettverksadapteren.

Jeg kjørte:

```powershell
Get-NetAdapter | Format-Table Name,Status,InterfaceDescription -AutoSize
```

Jeg så at `Ethernet 2` manglet i listen.

Samtidig kunne jeg se andre nettverksadaptere, blant annet VMware VMnet1 og VMnet8. Disse var virtuelle adaptere og ga ikke automatisk forbindelse til det fysiske labnettet.

Dette hjalp meg å forstå at problemet ikke nødvendigvis lå på SERVER01. CLIENT01 manglet nettverksadapteren som ble brukt til forbindelsen.

### 10.5 Hvordan jeg løste problemet

Jeg satte USB-Ethernet-adapteren tilbake i laptopen.

Deretter kjørte jeg:

```powershell
Get-NetAdapter | Format-Table Name,Status,LinkSpeed -AutoSize
```

Nå var `Ethernet 2` synlig igjen med status `Up` og linkhastighet `1 Gbps`.

Jeg brukte også `ipconfig` for å kontrollere nettverksinnstillingene.

Resultatet viste:

- IP-adresse: `192.168.50.100`
- Subnettmaske: `255.255.255.0`
- Gateway: `192.168.50.1`

Til slutt testet jeg forbindelsen til SERVER01 på nytt:

```powershell
ping 192.168.50.10
```

Jeg fikk fire svar fra serveren, med 0 % pakketap. Responstiden var 1 ms, og svarene hadde TTL 128.

Forbindelsen fungerte igjen.

### 10.6 Oppsummering av feilsøkingen

| Steg | Hva jeg fant ut |
|---|---|
| Symptom | CLIENT01 mistet kontakten med SERVER01 |
| Hypotese | Problemet kunne skyldes en manglende nettverksadapter |
| Test | Jeg brukte `Get-NetAdapter` og `ping` |
| Funn | Ethernet 2 var ikke lenger tilgjengelig i Windows |
| Løsning | Jeg koblet USB-Ethernet-adapteren til igjen |
| Kontroll | Adapteren ble aktiv, IP-innstillingene var riktige, og ping fungerte igjen |

### 10.7 Hva jeg lærte

Gjennom denne testen lærte jeg at jeg bør kontrollere den fysiske forbindelsen før jeg begynner å endre IP-adresser eller andre innstillinger.

Jeg lærte også forskjellen mellom å koble fra en Ethernet-kabel og å fjerne hele USB-Ethernet-adapteren. Når adapteren fjernes, kan den forsvinne helt fra listen over nettverkskort i Windows.

En linkhastighet på 1 Gbps betyr heller ikke at jeg har målt faktisk dataoverføring på 1 Gbps. Det viser hastigheten på nettverksforbindelsen.

Feilmeldingen `General failure` fortalte meg at klienten ikke klarte å sende ping på vanlig måte. Den beviste ikke at det var noe feil med SERVER01.

Det viktigste jeg lærte, var å teste én ting om gangen og kontrollere resultatet etter at jeg hadde gjort en endring.

Jeg dokumenterte feilsøkingen selv. Jeg har ikke dokumentert at en annen deltaker gjennomførte feilsøkingen, slik oppgaven også foreslo.


## 11. Feilsøking av Windows Firewall etter reinstallasjon

### 11.1 Problemet jeg opplevde – 21.10.2026

Etter at jeg hadde installert Windows Server 2025 Standard på nytt på SERVER01, fikk jeg problemer med ping mot serveradressen `192.168.50.10`.

Jeg kjørte:

```powershell
ping 192.168.50.10
```

Resultatet var:

- Sendt: 4 pakker
- Mottatt: 0 pakker
- Pakketap: 100 %
- Feilmelding: `Request timed out`

Jeg hadde tidligere fått svar fra serveren, men etter reinstallasjonen måtte jeg kontrollere nettverket på nytt.

### 11.2 Hvordan jeg undersøkte feilen

Jeg ville finne ut hvorfor serveradressen ikke svarte på ping.

Jeg undersøkte Windows Defender Firewall på SERVER01 ved å åpne:

```text
wf.msc
```

Jeg fant regelen:

`Fil- og skriverdeling (ekkoforespørsel – ICMPv4-In)`

Denne regelen styrer om Windows-brannmuren skal tillate innkommende ICMPv4 Echo Requests.

Jeg aktiverte regelen slik at serveren kunne svare på ping.

Etter endringen viste brannmurinnstillingen:

- Aktivert: Ja
- Handling: Tillat
- Profil: Alle

Jeg deaktiverte ikke hele Windows-brannmuren.

### 11.3 Testing etter endringen

Etter at jeg hadde aktivert regelen, testet jeg serveradressen på nytt:

```powershell
ping 192.168.50.10
```

Denne gangen fikk jeg fire svar:

```text
Reply from 192.168.50.10: bytes=32 time<1ms TTL=128
```

Resultatet var:

| Kontroll | Resultat |
|---|---|
| Pakker sendt | 4 |
| Pakker mottatt | 4 |
| Pakketap | 0 % |
| Responstid | Under 1 ms i svarlinjene |
| TTL | 128 |

Etter endringen svarte serveradressen på alle ping-forespørslene.

### 11.4 Hva som var årsaken

Ut fra testene og endringen jeg gjorde i brannmuren, vurderte jeg at blokkering av innkommende ICMPv4 var årsaken til at ping ikke fungerte.

Resultatet støttet denne forklaringen fordi ping begynte å fungere etter at jeg tillot ICMPv4 Echo Requests.

Jeg kan likevel ikke bruke ping-resultatet alene som bevis på at DNS eller SMB-fildeling fungerte.

Disse tjenestene måtte testes separat.

Jeg kan heller ikke fastslå sikkert hvilken maskin den opprinnelige PowerShell-testen ble kjørt fra, bare ut fra skjermbildet som var beskrevet i arbeidsloggen.

### 11.5 Sikkerhet og refleksjon

Jeg lærte at Windows Firewall kan blokkere en bestemt type nettverkstrafikk selv om maskinen er fysisk tilkoblet nettverket.

Jeg lærte også at det er bedre å kontrollere og endre en bestemt brannmurregel enn å slå av hele brannmuren.

Regelen jeg aktiverte, gjaldt alle nettverksprofiler. I et vanlig bedriftsnettverk ville jeg vurdert å begrense regelen til riktig profil og godkjente IP-adresser.

Jeg fikk ping til å fungere igjen, men dette var ikke en fullstendig test av alle tjenester på serveren.

SMB-forbindelsen ble undersøkt separat med `Get-SmbConnection`, Nmap og Wireshark.

Jeg mangler fortsatt et fullstendig skjermbilde av den endelige IP-konfigurasjonen på SERVER01 etter reinstallasjonen.



## 12. Oppsummering av det fysiske nettverket

Etter at jeg hadde konfigurert og testet det fysiske nettverket, gikk jeg tilbake til kravspesifikasjonen for å se hvor langt jeg hadde kommet.

Jeg sammenlignet kravene med testresultatene og arbeidsloggen min fra 20.–22.10.2026.

### 12.1 Status på kravene

| Krav | Hva jeg fikk til | Dokumentasjon |
|---|---|---|
| K1 – Kommunikasjon mellom CLIENT01 og SERVER01 | Jeg fikk fire ping-svar uten pakketap fra klienten til serveren. | Resultatet er loggført, men et eget ping-bilde ligger ikke i bildemappen. |
| K2 – Fast IP på SERVER01 | Jeg satte ifølge arbeidsloggen serveradressen til `192.168.50.10/24`. | Jeg mangler fullstendig skjermbilde av `ipconfig /all` etter reinstallasjonen. |
| K3 – DHCP til CLIENT01 | Klienten fikk `192.168.50.100/24` fra DHCP-serveren `192.168.50.1`. | IP-innstillingene er registrert i arbeidsloggen. |
| K4 – Tjeneste på SERVER01 | Jeg fikk kontakt med den delte mappen `Felles` ved hjelp av SMB. | SMB-forbindelsen og trafikken er dokumentert. Eget bilde av testfilen mangler. |
| K8 – DHCP-fornyelse og kommunikasjon | Jeg fornyet IP-innstillingene og fikk senere fire ping-svar fra serveren uten pakketap. | Testene er loggført, men hele forløpet er ikke dokumentert i ett sammenhengende skjermbilde. |

### 12.2 Hva som fungerte

Jeg fikk koblet sammen ruteren, switchen, serveren og klienten.

Jeg fikk også testet både statisk IP og DHCP på CLIENT01.

Ping-testene viste at klienten kunne nå både R1 og SERVER01. Jeg fikk dessuten undersøkt SMB med PowerShell, Nmap og Wireshark.

Under feilsøkingen opplevde jeg blant annet at ICMP ble blokkert av Windows Firewall, og at nettverkstilkoblingen forsvant da jeg fjernet USB-Ethernet-adapteren. Jeg fikk rettet begge problemene og kontrollert forbindelsen etterpå.

### 12.3 Hva jeg ikke kunne dokumentere fullt ut

Jeg mangler noen direkte skjermbilder fra den fysiske laben.

Det gjelder særlig den endelige statiske IP-konfigurasjonen på SERVER01, testfilen i den delte mappen og den komplette DHCP-fornyelsen.

Jeg har ikke lenger tilgang til skoleutstyret for å ta nye bilder. Derfor bruker jeg testresultatene jeg registrerte under arbeidet, og forklarer hvilke direktebevis som mangler.

Jeg vil ikke bruke skjermbilder fra Packet Tracer som bevis for innstillinger på den fysiske serveren.

### 12.4 Min vurdering

Jeg mener at jeg fikk det grunnleggende fysiske nettverket til å fungere.

Jeg lærte mye om IP-adressering, DHCP, servertilkobling og bruk av forskjellige testverktøy. Det viktigste var at jeg fikk erfaring med å finne feil og kontrollere om løsningen faktisk fungerte.

Samtidig ser jeg at jeg burde tatt bedre vare på enkelte skjermbilder underveis.

I den samlede akseptansetesten skal jeg bruke både kravspesifikasjonen og de faktiske resultatene for å vurdere hva jeg kan dokumentere som bestått, og hva som fortsatt har mangler.


## 13. FjordTek AS – nettverksdesign i Cisco Packet Tracer

### 13.1 Om bedriftsnettverket

Etter at jeg hadde jobbet med det fysiske nettverket på skolen, gikk jeg videre til Cisco Packet Tracer.

Denne gangen skulle jeg bygge et større nettverk for en fiktiv bedrift som heter FjordTek AS.

Bedriften hadde et hovedkontor med tre avdelinger: Administrasjon, Support og Servere. I tillegg hadde bedriften en egen salgsavdeling.

Målet mitt var å koble sammen flere nettverk og sørge for at klientene kunne kommunisere med serveren, selv om de var i forskjellige subnett.

Jeg skulle også bruke DHCP, DNS, HTTP og ruting mellom nettverkene.

### 13.2 Planlegging og første utkast – 23.10.2026

Jeg startet med å plassere nettverksenhetene i Logical Workspace i Packet Tracer.

I det første utkastet brukte jeg:

- 2 Cisco 2911-rutere
- 4 Cisco 2960-24TT-switcher
- 3 klient-PC-er
- 1 server

Jeg kalte ruterne `R1-HQ` og `R2-SALG`.

Switchene fikk navn etter avdelingene de skulle brukes i. Serverswitchen het først `SW-SERVER2`, men jeg endret senere navnet til `SW-SERVER`.

På dette tidspunktet hadde jeg bare plassert enhetene. Jeg hadde ennå ikke fullført konfigureringen eller testet kommunikasjonen.

### 13.3 Den ferdige nettverkstopologien

Jeg bygde videre på det første utkastet og la til en Cisco 3560 Layer 3-switch som kjerneswitch på hovedkontoret.

Jeg la også til en ekstra klient, `PC-ADMIN2`, slik at jeg kunne teste kommunikasjon mellom to PC-er i samme VLAN.

Den ferdige topologien bestod av:

| Enhet | Modell | Funksjon |
|---|---|---|
| R1-HQ | Cisco 2911 | Ruter ved hovedkontoret |
| R2-SALG | Cisco 2911 | Ruter ved salgsavdelingen |
| SW-CORE-HQ | Cisco 3560-24PS | Kjerneswitch med Layer 3-ruting |
| SW-ADMIN | Cisco 2960-24TT | Tilgangsswitch for Administrasjon |
| SW-SUPPORT | Cisco 2960-24TT | Tilgangsswitch for Support |
| SW-SERVER | Cisco 2960-24TT | Tilgangsswitch for servernettet |
| SW-SALG | Cisco 2960-24TT | Tilgangsswitch for Salg |
| PC-ADMIN | PC-PT | Klient i Administrasjon |
| PC-ADMIN2 | PC-PT | Ekstra klient i Administrasjon |
| PC-SUPPORT | PC-PT | Klient i Support |
| PC-SALG | PC-PT | Klient i Salg |
| SERVER01 | Server-PT | Server for DNS og HTTP |

Jeg brukte totalt fem switcher: fire tilgangsswitcher og én kjerneswitch.

### 13.4 Adresseplan og VLAN

Jeg valgte å bruke forskjellige IP-nettverk for hver avdeling.

| Avdeling | VLAN | Nettverk | Gateway |
|---|---|---|---|
| Administrasjon | 10 | 192.168.10.0/24 | 192.168.10.1 |
| Support | 20 | 192.168.20.0/24 | 192.168.20.1 |
| Servere | 30 | 192.168.30.0/24 | 192.168.30.1 |
| Salg | 40 | 192.168.40.0/24 | 192.168.40.1 |

På hovedkontoret brukte jeg VLAN 10, 20 og 30.

Gatewayene for disse VLAN-ene lå på SW-CORE-HQ, som kunne utføre ruting mellom VLAN-ene.

På Salg brukte jeg R2-SALG som gateway med adressen `192.168.40.1`.

SERVER01 fikk en fast adresse i servernettet:

- IP-adresse: `192.168.30.10`
- Subnettmaske: `255.255.255.0`
- Gateway: `192.168.30.1`

Jeg brukte forskjellige subnett for å skille avdelingene logisk. Det gjorde det også mulig å øve på ruting mellom nettverkene.

### 13.5 Forbindelsene mellom rutere og kjerneswitch

Jeg brukte egne /30-nettverk mellom rutingsenhetene.

| Forbindelse | Nettverk | IP-adresser |
|---|---|---|
| R1-HQ ↔ R2-SALG | 10.0.0.0/30 | R1: 10.0.0.1, R2: 10.0.0.2 |
| SW-CORE-HQ ↔ R1-HQ | 10.0.0.4/30 | R1: 10.0.0.5, CORE: 10.0.0.6 |

Jeg brukte Ethernet-forbindelser mellom rutingsenhetene.

Jeg valgte /30 fordi hver av disse forbindelsene bare trengte to brukbare IP-adresser.

Med denne oppkoblingen kunne trafikken fra hovedkontoret gå via R1-HQ og R2-SALG til salgsavdelingen.

### 13.6 Hvorfor jeg brukte en Layer 3-switch

Jeg brukte SW-CORE-HQ for å samle nettverkene på hovedkontoret.

En vanlig Layer 2-switch videresender Ethernet-rammer innenfor det lokale nettverket, mens en Layer 3-switch også kan rute IP-trafikk mellom subnett.

Jeg brukte virtuelle VLAN-grensesnitt (SVI) på kjerneswitchen som gateway for VLAN 10, 20 og 30.

Dette gjorde at jeg kunne bruke én kjerneswitch til ruting mellom avdelingene på hovedkontoret, i stedet for å bruke en egen fysisk ruter for hvert VLAN.

### 13.7 Hvordan arbeidet utviklet seg

Jeg startet med å plassere enhetene og lage en adresseplan.

Deretter bygde jeg videre på løsningen, konfigurerte forbindelsene og testet kommunikasjonen mellom forskjellige nettverk.

Underveis måtte jeg også rette en feil i forbindelsen mellom SW-CORE-HQ og R1-HQ.

Da jeg hadde rettet forbindelsen, fikk jeg fem av fem ping-svar fra kjerneswitchen til R1-HQ.

Jeg dokumenterer selve feilsøkingen og testresultatene i de neste kapitlene.

### 13.8 Hva jeg lærte av nettverksdesignet

Jeg lærte at det er viktig å planlegge IP-adressene og forbindelsene før jeg begynner med konfigureringen.

Jeg fikk også bedre forståelse av forskjellen mellom et fysisk nettverk og et større bedriftsnettverk med flere VLAN og rutere.

I den fysiske laben var klienten og serveren i samme subnett. I FjordTek AS måtte jeg konfigurere ruting for at klienter i forskjellige nettverk skulle kunne kommunisere.

Dette gjorde det lettere for meg å forstå hvorfor både adresseplan, gateway og rutetabeller må være riktige.


## 14. VLAN og Inter-VLAN Routing

### 14.1 Hvorfor jeg brukte VLAN

I FjordTek AS skulle jeg dele nettverket inn i flere avdelinger.

Jeg brukte VLAN for å skille nettverkene logisk, slik at Administrasjon, Support og Servere kunne ha hvert sitt IP-nettverk.

På hovedkontoret brukte jeg disse VLAN-ene:

| VLAN | Navn | Nettverk | Gateway |
|---|---|---|---|
| 10 | ADMIN | 192.168.10.0/24 | 192.168.10.1 |
| 20 | SUPPORT | 192.168.20.0/24 | 192.168.20.1 |
| 30 | SERVERS | 192.168.30.0/24 | 192.168.30.1 |

Salgsavdelingen hadde et eget nettverk, `192.168.40.0/24`, med `192.168.40.1` på R2-SALG som gateway.

Jeg lærte at VLAN kan brukes til å dele opp et nettverk uten at hver avdeling trenger en egen ruter.

### 14.2 Konfigurering av SW-CORE-HQ

Jeg brukte en Cisco 3560 Layer 3-switch som kjerneswitch på hovedkontoret.

På SW-CORE-HQ brukte jeg VLAN-grensesnitt, også kalt SVI (Switched Virtual Interface), som gateway for nettverkene.

Jeg kontrollerte grensesnittene med:

```text
show ip interface brief
```

Resultatet viste:

| Interface | IP-adresse | Status | Protocol |
|---|---|---|---|
| Vlan10 | 192.168.10.1 | up | up |
| Vlan20 | 192.168.20.1 | up | up |
| Vlan30 | 192.168.30.1 | up | up |
| GigabitEthernet0/1 | 10.0.0.6 | up | up |

Dette viste at de tre VLAN-grensesnittene var aktive. GigabitEthernet0/1 ble brukt til forbindelsen mot R1-HQ.

Jeg brukte Layer 3-funksjonen på kjerneswitchen for å kunne rute mellom VLAN-ene.

### 14.3 Tilkobling av tilgangsswitchene

Jeg koblet tilgangsswitchene til SW-CORE-HQ slik:

| Port på SW-CORE-HQ | Tilkoblet enhet | Port på tilgangsswitch |
|---|---|---|
| Fa0/1 | SW-ADMIN | Fa0/1 |
| Fa0/2 | SW-SUPPORT | Fa0/1 |
| Fa0/3 | SW-SERVER | Fa0/1 |
| Gi0/1 | R1-HQ | Gi0/0 |

På denne måten kunne jeg samle nettverkene på hovedkontoret gjennom kjerneswitchen.

### 14.4 Kontroll av VLAN 10 på SW-ADMIN

Da jeg la til den ekstra klienten PC-ADMIN2, måtte jeg kontrollere at switchporten var plassert i riktig VLAN.

På SW-ADMIN konfigurerte jeg FastEthernet0/3 som en accessport i VLAN 10.

Dette er kommandoene fra testen min:

```text
enable
configure terminal
interface fastEthernet 0/3
switchport mode access
switchport access vlan 10
no shutdown
end
show vlan brief
```

Resultatet fra `show vlan brief` viste:

```text
10    ADMIN    active    Fa0/1, Fa0/2, Fa0/3
```

Da kunne jeg se at portene Fa0/1, Fa0/2 og Fa0/3 tilhørte VLAN 10.

Jeg lærte at en accessport plasserer den tilkoblede enheten i et bestemt VLAN.

### 14.5 Inter-VLAN Routing

Klientene i de forskjellige avdelingene brukte ulike IP-nettverk.

For eksempel hadde Support nettverket `192.168.20.0/24`, mens serveren lå i `192.168.30.0/24`.

Disse nettverkene kunne ikke kommunisere direkte gjennom vanlig Layer 2-switching. Derfor brukte jeg SW-CORE-HQ til å utføre ruting mellom VLAN-ene.

Jeg kontrollerte rutetabellen på SW-CORE-HQ med:

```text
show ip route
```

Tabellen viste blant annet:

```text
C 192.168.10.0/24 is directly connected, Vlan10
C 192.168.20.0/24 is directly connected, Vlan20
C 192.168.30.0/24 is directly connected, Vlan30
S* 0.0.0.0/0 via 10.0.0.5
```

`C` betyr at nettverket er direkte tilkoblet rutingsenheten.

Den statiske standardruten pekte mot R1-HQ på `10.0.0.5`.

Dette gjorde at kjerneswitchen hadde en rute videre for trafikk til nettverk som ikke var direkte tilkoblet.

### 14.6 Testing av kommunikasjon mellom VLAN

Jeg testet blant annet kommunikasjonen fra PC-SUPPORT til SERVER01.

PC-SUPPORT hadde adressen `192.168.20.100`, mens SERVER01 hadde `192.168.30.10`.

Fra PC-SUPPORT kjørte jeg:

```text
ping 192.168.30.10
```

Resultatet var:

- Pakker sendt: 4
- Pakker mottatt: 4
- Pakketap: 0 %

Jeg fikk svar fra serveren selv om klienten og serveren var i forskjellige subnett.

Dette viste at IP-kommunikasjonen mellom de to nettverkene fungerte under testen.

Jeg hadde også to klienter i VLAN 10, PC-ADMIN og PC-ADMIN2. Dem brukte jeg senere til å undersøke ARP og ICMP i Simulation Mode.

### 14.7 Hva jeg lærte

Jeg lærte forskjellen mellom switching og ruting.

Når to klienter ligger i samme VLAN og subnett, kan trafikken gå gjennom Layer 2-switching uten å gå via en ruter.

Når en klient skal kommunisere med et annet subnett, må trafikken sendes til en gateway som kan utføre IP-ruting.

Jeg fikk også bedre forståelse av hvorfor VLAN-grensesnittene på SW-CORE-HQ måtte ha riktige IP-adresser, og hvorfor rutetabellen var viktig.

VLAN gir logisk oppdeling av nettverket, men det betyr ikke automatisk at all trafikk mellom avdelingene er blokkert. Da jeg aktiverte ruting mellom VLAN-ene, kunne de kommunisere. Hvis jeg ville begrense denne trafikken, måtte jeg konfigurere egne tilgangsregler.

I denne delen dokumenterte jeg VLAN-oppsettet, rutetabellen og en vellykket test mellom Support og Servere. Jeg har ikke dokumentert en egen test av alle mulige forbindelser mellom VLAN-ene.


## 15. Statisk routing mellom hovedkontoret og Salg

### 15.1 Hvorfor jeg brukte statisk routing

Etter at jeg hadde konfigurert VLAN-ene på hovedkontoret, måtte jeg få nettverket til å kommunisere med salgsavdelingen.

Hovedkontoret og Salg hadde forskjellige IP-nettverk. Derfor måtte rutingsenhetene vite hvor de skulle sende trafikken.

Jeg brukte statiske ruter for å bestemme veien mellom nettverkene.

I denne løsningen hadde jeg tre enheter som utførte ruting:

- SW-CORE-HQ – ruting mellom VLAN på hovedkontoret.
- R1-HQ – forbindelse mellom kjerneswitchen og R2-SALG.
- R2-SALG – forbindelse til salgsnettverket.

### 15.2 Forbindelsene mellom rutingsenhetene

Jeg brukte to /30-nettverk til forbindelsene.

| Forbindelse | Enhet | IP-adresse |
|---|---|---|
| SW-CORE-HQ ↔ R1-HQ | SW-CORE-HQ | 10.0.0.6/30 |
| SW-CORE-HQ ↔ R1-HQ | R1-HQ | 10.0.0.5/30 |
| R1-HQ ↔ R2-SALG | R1-HQ | 10.0.0.1/30 |
| R1-HQ ↔ R2-SALG | R2-SALG | 10.0.0.2/30 |

Jeg valgte /30 fordi hver forbindelse hadde to enheter som trengte hver sin IP-adresse.

### 15.3 Routing på SW-CORE-HQ

Jeg kontrollerte rutetabellen på kjerneswitchen med:

```text
show ip route
```

Resultatet viste at VLAN 10, 20 og 30 var direkte tilkoblet kjerneswitchen:

```text
C 192.168.10.0/24 is directly connected, Vlan10
C 192.168.20.0/24 is directly connected, Vlan20
C 192.168.30.0/24 is directly connected, Vlan30
```

Jeg hadde også konfigurert en statisk standardrute:

```text
S* 0.0.0.0/0 [1/0] via 10.0.0.5
```

Denne ruten betydde at trafikk til nettverk som ikke hadde en mer spesifikk rute, kunne sendes videre til R1-HQ på `10.0.0.5`.

Dette var nødvendig for at klientene på hovedkontoret skulle kunne nå Salg.

### 15.4 Routing på R1-HQ

Jeg brukte R1-HQ som forbindelsen mellom hovedkontoret og salgsavdelingen.

Jeg kontrollerte rutene med:

```text
show ip route
```

Rutetabellen viste disse statiske rutene:

```text
S 192.168.10.0/24 [1/0] via 10.0.0.6
S 192.168.20.0/24 [1/0] via 10.0.0.6
S 192.168.30.0/24 [1/0] via 10.0.0.6
S 192.168.40.0/24 [1/0] via 10.0.0.2
```

De tre første rutene pekte mot SW-CORE-HQ, som hadde VLAN-nettverkene.

Den siste ruten pekte mot R2-SALG, som hadde forbindelse til `192.168.40.0/24`.

Jeg kunne også se at R1-HQ hadde direkte tilkobling til nettverkene `10.0.0.0/30` og `10.0.0.4/30`.

### 15.5 Routing på R2-SALG

På R2-SALG kontrollerte jeg også rutetabellen med:

```text
show ip route
```

Den viste:

```text
S 192.168.10.0/24 [1/0] via 10.0.0.1
S 192.168.20.0/24 [1/0] via 10.0.0.1
S 192.168.30.0/24 [1/0] via 10.0.0.1
```

Alle tre rutene pekte mot R1-HQ på `10.0.0.1`.

R2-SALG hadde dessuten direkte tilkobling til salgsnettverket `192.168.40.0/24`.

Jeg lærte at rutene måtte fungere begge veier. Det var ikke nok at hovedkontoret visste hvordan det skulle nå Salg. R2-SALG måtte også vite hvordan svartrafikken skulle komme tilbake til hovedkontoret.

### 15.6 Feil i forbindelsen mellom SW-CORE-HQ og R1-HQ

Under arbeidet hadde jeg problemer med forbindelsen mellom kjerneswitchen og R1-HQ.

Jeg testet fra SW-CORE-HQ med:

```text
ping 10.0.0.5
```

Først fikk jeg ingen svar på de fem ping-forsøkene, slik jeg registrerte i arbeidsloggen.

Jeg undersøkte kablingen og fant at forbindelsen til R1-HQ var koblet til feil port på kjerneswitchen.

Jeg rettet forbindelsen slik at den gikk mellom:

- SW-CORE-HQ: `GigabitEthernet0/1`
- R1-HQ: `GigabitEthernet0/0`

Etter omkoblingen testet jeg på nytt:

```text
SW-CORE-HQ>ping 10.0.0.5
!!!!!
Success rate is 100 percent (5/5)
```

Dette viste at forbindelsen mellom kjerneswitchen og R1-HQ fungerte etter rettingen.

Jeg har bilde av den vellykkede testen. Resultatet før omkoblingen er registrert i arbeidsloggen, men jeg har ikke et eget skjermbilde av de fem mislykkede forsøkene.

### 15.7 Tracert fra Administrasjon til Salg

Etter at rutene var konfigurert, testet jeg nettverksveien fra PC-ADMIN til PC-SALG.

Jeg kjørte:

```text
tracert 192.168.40.100
```

Resultatet var:

```text
1    192.168.10.1
2    10.0.0.5
3    10.0.0.2
4    192.168.40.100

Trace complete.
```

Jeg kunne se fire hopp:

1. SW-CORE-HQ – gateway for VLAN 10.
2. R1-HQ – forbindelsen videre fra hovedkontoret.
3. R2-SALG – ruteren ved salgsavdelingen.
4. PC-SALG – målet.

Dette viste at trafikken kunne gå fra Administrasjon til Salg gjennom rutingsenhetene.

### 15.8 Tracert fra Salg til SERVER01

Jeg testet også forbindelsen fra PC-SALG til SERVER01.

Jeg kjørte:

```text
tracert 192.168.30.10
```

Resultatet var:

```text
1    192.168.40.1
2    10.0.0.1
3    10.0.0.6
4    192.168.30.10

Trace complete.
```

Denne gangen gikk trafikken fra PC-SALG via R2-SALG, videre til R1-HQ og SW-CORE-HQ, før den nådde SERVER01.

Dette var en viktig test fordi jeg kunne se at trafikken også fant veien tilbake til hovedkontoret.

### 15.9 Hva jeg lærte

Jeg lærte hvordan statiske ruter brukes for å koble sammen forskjellige IP-nettverk.

Jeg forstod også bedre forskjellen mellom direkte tilkoblede nettverk og nettverk som krever en statisk rute.

Med `show ip route` kunne jeg kontrollere rutetabellene, mens `tracert` hjalp meg å se hvilke rutingsenheter trafikken passerte.

Jeg lærte at både riktig kabling, IP-adresser og rutetabeller må fungere sammen.

Da forbindelsen mellom SW-CORE-HQ og R1-HQ ikke fungerte, hjalp det å undersøke den fysiske oppkoblingen før jeg begynte å endre rutene.

Tracert-testene viste at de valgte nettverksveiene fungerte. De var likevel ikke en fullstendig stabilitetstest av hele bedriftsnettverket.


## 16. DHCP i Cisco Packet Tracer

### 16.1 Hvorfor jeg brukte DHCP

I FjordTek AS brukte jeg DHCP for at klientene skulle få nettverksinnstillinger automatisk.

Jeg ville slippe å skrive inn IP-adresse, subnettmaske, gateway og DNS manuelt på hver klient.

Siden bedriften hadde flere nettverk, måtte klientene også få adresser som passet til sin egen avdeling.

### 16.2 DHCP på PC-ADMIN

Jeg brukte `ipconfig /all` på PC-ADMIN for å kontrollere nettverksinnstillingene.

Resultatet viste:

| Innstilling | Verdi |
|---|---|
| IPv4-adresse | 192.168.10.100 |
| Subnettmaske | 255.255.255.0 |
| Default gateway | 192.168.10.1 |
| DHCP-server | 192.168.10.1 |
| DNS-server | 192.168.30.10 |

Dette viste at PC-ADMIN hadde fått nettverksinnstillinger som passet til VLAN 10.

DHCP-serveradressen var den samme som gatewayen på VLAN 10.

Jeg testet også forbindelsen til gatewayen:

```text
ping 192.168.10.1
```

Jeg fikk fire svar uten pakketap.

Dermed hadde jeg kontrollert både IP-innstillingene og at klienten kunne nå sin egen gateway.

### 16.3 DHCP på PC-ADMIN2

Jeg la til PC-ADMIN2 som en ekstra klient i Administrasjon.

På denne PC-en valgte jeg DHCP under Desktop → IP Configuration i Packet Tracer.

Jeg fikk meldingen:

`DHCP request successful.`

Klienten fikk disse innstillingene:

| Innstilling | Verdi |
|---|---|
| IPv4-adresse | 192.168.10.101 |
| Subnettmaske | 255.255.255.0 |
| Default gateway | 192.168.10.1 |
| DNS-server | 192.168.30.10 |

PC-ADMIN og PC-ADMIN2 hadde forskjellige IP-adresser, men lå i samme subnett.

Jeg brukte senere disse to klientene til å undersøke ARP og ICMP i Simulation Mode.

### 16.4 DHCP på PC-SUPPORT

Etterpå kontrollerte jeg nettverksinnstillingene på PC-SUPPORT.

Jeg kjørte:

```text
ipconfig /all
```

Resultatet viste:

| Innstilling | Verdi |
|---|---|
| IPv4-adresse | 192.168.20.100 |
| Subnettmaske | 255.255.255.0 |
| Default gateway | 192.168.20.1 |
| DHCP-server | 192.168.20.1 |
| DNS-server | 192.168.30.10 |

PC-SUPPORT hadde fått en adresse fra riktig nettverk, `192.168.20.0/24`.

Jeg testet også forbindelsen fra PC-SUPPORT til SERVER01 på `192.168.30.10`. Jeg fikk fire ping-svar uten pakketap.

Dette viste at klienten kunne kommunisere med serveren i et annet subnett under testen.

### 16.5 Nettverksinnstillinger på PC-SALG

På PC-SALG kontrollerte jeg adressen med:

```text
ipconfig
```

Resultatet viste:

- IPv4-adresse: `192.168.40.100`
- Subnettmaske: `255.255.255.0`
- Default gateway: `192.168.40.1`

Dette stemte med adresseplanen for salgsavdelingen.

Jeg hadde oppgitt i arbeidsloggen at DHCP var brukt på Salg. Skjermbildet fra `ipconfig` viser likevel ikke DHCP-serveren eller om adressen ble tildelt automatisk.

Derfor bruker jeg bildet som dokumentasjon på IP-adresse og gateway, men ikke som et fullstendig bevis på DHCP-tildelingen.

### 16.6 Fast IP-adresse på SERVER01

SERVER01 skulle ha en fast IP-adresse, fordi klientene skulle bruke serveren til DNS og HTTP.

I Packet Tracer kontrollerte jeg følgende innstillinger:

| Innstilling | Verdi |
|---|---|
| IP Configuration | Static |
| IPv4-adresse | 192.168.30.10 |
| Subnettmaske | 255.255.255.0 |
| Default gateway | 192.168.30.1 |
| DNS-server | 192.168.30.10 |

SERVER01 brukte altså ikke DHCP.

Jeg valgte fast IP fordi DNS-innstillingene på klientene pekte til `192.168.30.10`. Hvis serveradressen endret seg, kunne klientene få problemer med å finne tjenestene.

### 16.7 Kontroll av DHCP-resultatene

Jeg hadde dokumentasjon på at PC-ADMIN, PC-ADMIN2 og PC-SUPPORT fikk riktige nettverksinnstillinger.

For PC-SALG dokumenterte jeg riktig IP-adresse og gateway, men ikke DHCP-serverfeltet.

Jeg har ikke tatt med de nøyaktige DHCP-poolene, ekskluderte adressene eller alle konfigurasjonskommandoene i dette kapitlet, fordi disse opplysningene ikke er dokumentert fullstendig i de opprinnelige skjermbildene.

Jeg vil heller vise innstillingene jeg faktisk kunne kontrollere, enn å skrive inn kommandoer jeg ikke kan bekrefte at jeg brukte.

### 16.8 Hva jeg lærte

Jeg lærte at DHCP gjør det enklere å administrere IP-adresser når det finnes mange klienter.

Jeg forstod også at forskjellige VLAN trenger nettverksinnstillinger som passer til hvert subnett.

DHCP gir klienten blant annet IP-adresse, gateway og DNS-server, men en riktig DHCP-adresse betyr ikke automatisk at alle nettverkstjenester fungerer.

Derfor måtte jeg også teste kommunikasjonen med ping og kontrollere DNS og HTTP separat.

Jeg lærte forskjellen mellom en klient som får adresse automatisk og en server som bruker fast IP-adresse.


## 17. DNS og HTTP på SERVER01

### 17.1 Hvorfor jeg brukte DNS og HTTP

I FjordTek AS skulle klientene kunne åpne en enkel intranettside som lå på SERVER01.

Jeg ville at brukerne skulle kunne åpne siden med et navn, i stedet for å måtte huske IP-adressen til serveren.

Derfor brukte jeg DNS og HTTP.

DNS brukes til å finne IP-adressen som hører til et domenenavn, mens HTTP brukes til å hente nettsider fra en webserver.

### 17.2 Nettverksinnstillinger på SERVER01

Jeg brukte SERVER01 i Cisco Packet Tracer som både DNS-server og webserver.

Serveren hadde disse innstillingene:

| Innstilling | Verdi |
|---|---|
| IP Configuration | Static |
| IPv4 Address | 192.168.30.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.30.1 |
| DNS Server | 192.168.30.10 |

Jeg ga serveren fast IP-adresse fordi klientenes DNS-innstillinger skulle peke til denne adressen.

SERVER01 lå i VLAN 30, som var servernettverket på hovedkontoret.

### 17.3 Konfigurering av DNS

Jeg åpnet SERVER01 i Packet Tracer og gikk til:

`Services → DNS`

Jeg aktiverte DNS-tjenesten ved å velge **On**.

Deretter la jeg inn en DNS-post av typen A Record:

| Innstilling | Verdi |
|---|---|
| Name | intranett.fjordtek.local |
| Type | A Record |
| Address | 192.168.30.10 |

Denne posten gjorde at navnet `intranett.fjordtek.local` kunne oversettes til serverens IPv4-adresse.

Jeg kontrollerte at posten lå i listen over Resource Records.

### 17.4 Konfigurering av HTTP

Deretter gikk jeg til:

`Services → HTTP`

Jeg kontrollerte at HTTP-tjenesten var aktivert.

Dette var innstillingene som ble dokumentert:

- HTTP: On
- HTTPS: On
- Filen `index.html` fantes på serveren.

Jeg brukte HTTP for at klientene skulle kunne åpne en nettside på SERVER01.

HTTPS var også aktivert, men jeg gjennomførte ikke en egen funksjonstest av HTTPS. Derfor dokumenterer jeg HTTP som den testede webtjenesten.

### 17.5 Testing med IP-adresse

Jeg testet tilgang til webserveren fra klienter i bedriftsnettverket.

Jeg åpnet Web Browser i Packet Tracer og brukte serverens IP-adresse:

```text
http://192.168.30.10
```

Jeg registrerte i arbeidsloggen at jeg fikk åpnet siden fra PC-ADMIN og PC-SALG.

Denne testen var viktig fordi jeg ville kontrollere om klientene kunne nå webserveren uten å bruke DNS-navnet.

Jeg har ikke et eget skjermbilde av nettsiden i nettleseren fra denne testen. Resultatet er derfor basert på det jeg skrev i arbeidsloggen.

### 17.6 Testing med DNS-navn

Etterpå testet jeg tilgang til samme server med DNS-navnet:

```text
http://intranett.fjordtek.local
```

Jeg registrerte at testen fungerte fra PC-ADMIN og PC-SALG.

For at dette skal fungere, må klienten kunne kontakte DNS-serveren og få riktig IP-adresse tilbake.

Deretter må klienten kunne kommunisere med webserveren på den adressen.

Jeg hadde konfigurert klientene på hovedkontoret til å bruke DNS-serveren `192.168.30.10`.

Denne testen hjalp meg å forstå hvorfor riktig DNS-konfigurasjon er viktig, selv om den grunnleggende IP-kommunikasjonen allerede fungerer.

### 17.7 Kontroll med Simulation Mode

Jeg brukte også Simulation Mode for å undersøke DNS- og HTTP-trafikken mellom PC-ADMIN og SERVER01.

I DNS-testen kunne jeg følge meldinger fra PC-ADMIN gjennom SW-ADMIN, SW-CORE-HQ og SW-SERVER til SERVER01, og se trafikk i motsatt retning.

I HTTP-testen kunne jeg se en forespørsel fra PC-ADMIN til SERVER01 og et svar som gikk tilbake til klienten.

Dette var dokumentert med egne skjermbilder fra Simulation Mode.

Jeg beskriver selve pakkeforløpet mer detaljert i neste kapittel.

### 17.8 Hva jeg lærte

Jeg lærte at DNS og HTTP har forskjellige oppgaver, selv om de brukes sammen når en bruker åpner en nettside med et domenenavn.

DNS hjelper klienten å finne riktig IP-adresse, mens HTTP brukes til å hente innhold fra webserveren.

Jeg lærte også at det er nyttig å teste en tjeneste med IP-adressen først og deretter med DNS-navnet.

Hvis nettsiden fungerer med IP-adresse, men ikke med navn, er det naturlig å undersøke DNS-konfigurasjonen. Det betyr likevel ikke at DNS alltid er den eneste mulige feilkilden.

Jeg fikk brukt både DNS og HTTP i bedriftsnettverket og undersøkte kommunikasjonen i Simulation Mode.

Testene mine viste at tjenestene fungerte på de forbindelsene jeg undersøkte. Jeg gjennomførte ikke en egen HTTPS-test eller en fullstendig test fra alle klientene.


## 18. Testing i Simulation Mode

### 18.1 Hvorfor jeg brukte Simulation Mode

Etter at jeg hadde konfigurert nettverket i Cisco Packet Tracer, brukte jeg Simulation Mode for å undersøke hvordan pakkene beveget seg mellom enhetene.

I Realtime Mode kunne jeg kontrollere om kommunikasjonen fungerte. Med Simulation Mode kunne jeg følge hendelsene steg for steg og se hvilke protokoller som ble brukt.

Jeg gjennomførte tre tester:

1. ARP og ICMP mellom to klienter i samme VLAN.
2. ICMP mellom Administrasjon og Salg.
3. DNS og HTTP mellom PC-ADMIN og SERVER01.

### 18.2 Test 1 – ARP og ICMP i samme VLAN

Jeg brukte to klienter i VLAN 10:

- PC-ADMIN2: `192.168.10.101`
- PC-ADMIN: `192.168.10.100`

Begge klientene var koblet til SW-ADMIN og brukte samme subnett.

Jeg startet Simulation Mode og undersøkte kommunikasjonen fra PC-ADMIN2 til PC-ADMIN.

Jeg valgte ARP og ICMP som synlige protokoller i Event List.

**ARP**

I starten kunne jeg se ARP-meldinger fra PC-ADMIN2.

Event List viste blant annet:

| Tid (s) | Fra | Til | Protokoll |
|---|---|---|---|
| 0.000 | PC-ADMIN2 | SW-ADMIN | ARP |
| 0.002 | SW-ADMIN | PC-ADMIN | ARP |
| 0.003 | PC-ADMIN | SW-ADMIN | ARP |
| 0.004 | SW-ADMIN | PC-ADMIN2 | ARP |

Jeg kunne også se en ARP-melding fra SW-ADMIN mot SW-CORE-HQ. Dette var knyttet til videresending av ARP-trafikk i nettverket, ikke at kommunikasjonen mellom klientene måtte rutes til et annet subnett.

Jeg lærte at ARP brukes til å finne MAC-adressen som hører til en IPv4-adresse i det lokale nettverket.

**ICMP**

Etter ARP-meldingene kunne jeg se ICMP-trafikk mellom PC-ADMIN2 og PC-ADMIN.

Jeg observerte både Echo Request og Echo Reply. I en senere del av simuleringen viste Event List flere ICMP-hendelser mellom klientene via SW-ADMIN.

Dette viste at de to klientene kunne kommunisere i samme VLAN uten å gå gjennom en IP-ruter.

### 18.3 Test 2 – ICMP fra Administrasjon til Salg

I den andre testen undersøkte jeg kommunikasjonen mellom to forskjellige nettverk.

Jeg sendte ICMP fra PC-ADMIN til PC-SALG.

- PC-ADMIN: `192.168.10.100`
- PC-SALG: `192.168.40.100`

Event List viste denne veien for forespørselen:

```text
PC-ADMIN
   ↓
SW-ADMIN
   ↓
SW-CORE-HQ
   ↓
R1-HQ
   ↓
R2-SALG
   ↓
SW-SALG
   ↓
PC-SALG
```

De synlige hendelsene var:

| Tid (s) | Fra | Til |
|---|---|---|
| 0.001 | PC-ADMIN | SW-ADMIN |
| 0.002 | SW-ADMIN | SW-CORE-HQ |
| 0.003 | SW-CORE-HQ | R1-HQ |
| 0.004 | R1-HQ | R2-SALG |
| 0.005 | R2-SALG | SW-SALG |
| 0.006 | SW-SALG | PC-SALG |

Etterpå kunne jeg følge svartrafikken fra PC-SALG tilbake mot PC-ADMIN.

Ved simuleringstid `0.012 s` viste Event List at ICMP-svaret hadde kommet tilbake til PC-ADMIN.

**Hva jeg fant ut**

Denne testen viste at trafikken kunne gå mellom Administrasjon og Salg gjennom de konfigurerte rutingsenhetene.

Jeg kunne se forskjellen mellom kommunikasjon innenfor samme VLAN og kommunikasjon mellom forskjellige IP-nettverk.

Tidene i Event List var simuleringstider fra Packet Tracer, ikke målinger av responstid i et fysisk bedriftsnettverk.

### 18.4 Test 3 – DNS mellom PC-ADMIN og SERVER01

Jeg undersøkte også hva som skjedde når PC-ADMIN brukte intranettnavnet:

`intranett.fjordtek.local`

DNS-serveren var SERVER01 på `192.168.30.10`.

Jeg valgte DNS og HTTP som synlige protokoller i Simulation Mode.

I Event List kunne jeg følge DNS-trafikken mellom PC-ADMIN og SERVER01.

Forespørselen gikk gjennom:

```text
PC-ADMIN
   ↓
SW-ADMIN
   ↓
SW-CORE-HQ
   ↓
SW-SERVER
   ↓
SERVER01
```

Jeg kunne også se DNS-trafikk i motsatt retning.

På SERVER01 hadde jeg konfigurert en A-post:

`intranett.fjordtek.local → 192.168.30.10`

Denne testen hjalp meg å forstå hvordan DNS brukes når en klient skal kontakte en server ved hjelp av et navn.

Skjermbildet viste DNS-hendelser mellom klienten og serveren. Jeg brukte også DNS-konfigurasjonen på SERVER01 for å dokumentere hvilken adresse navnet skulle peke til.

### 18.5 HTTP Request og Response

Etter DNS-testen undersøkte jeg HTTP-trafikken.

Jeg kunne følge en HTTP-forespørsel fra PC-ADMIN til SERVER01.

Event List viste:

| Tid (s) | Fra | Til |
|---|---|---|
| 0.018 | PC-ADMIN | SW-ADMIN |
| 0.019 | SW-ADMIN | SW-CORE-HQ |
| 0.020 | SW-CORE-HQ | SW-SERVER |
| 0.021 | SW-SERVER | SERVER01 |

Etterpå viste hendelseslisten HTTP-trafikk tilbake fra SERVER01 til PC-ADMIN:

| Tid (s) | Fra | Til |
|---|---|---|
| 0.022 | SERVER01 | SW-SERVER |
| 0.023 | SW-SERVER | SW-CORE-HQ |
| 0.024 | SW-CORE-HQ | SW-ADMIN |
| 0.025 | SW-ADMIN | PC-ADMIN |

Dette viste en HTTP-forespørsel og tilhørende svartrafikk gjennom nettverket.

Jeg hadde tidligere registrert at intranettsiden kunne åpnes i nettleseren. Simulation Mode ga meg i tillegg mulighet til å se nettverkstrafikken som var knyttet til HTTP.

Skjermbildet av Event List dokumenterer HTTP-trafikken, men viser ikke alene hvordan selve nettsiden så ut i nettleseren.

### 18.6 Ekstra observasjon – ARP i Salg

Jeg undersøkte også ARP mellom PC-SALG og den lokale gatewayen R2-SALG.

PC-SALG hadde adressen `192.168.40.100`, og gatewayen var `192.168.40.1`.

I Event List kunne jeg se ARP-trafikk mellom PC-SALG, SW-SALG og R2-SALG før videre ICMP-kommunikasjon.

Dette var nyttig fordi jeg kunne se at en klient også trenger å finne MAC-adressen til gatewayen når den skal sende trafikk til et annet IP-nettverk.

Denne observasjonen var en ekstra test og ikke en erstatning for de tre hovedtestene.

### 18.7 Hva jeg lærte

Jeg fikk bedre forståelse av hvordan ARP, ICMP, DNS og HTTP brukes i nettverket.

ARP hjalp klientene med å finne riktige MAC-adresser i det lokale nettverket. ICMP brukte jeg til å undersøke kommunikasjon mellom enheter.

Jeg lærte også at trafikk mellom forskjellige subnett må sendes gjennom enheter som utfører ruting.

DNS og HTTP viste meg hvordan flere protokoller kan brukes når en klient skal åpne en nettside med et domenenavn.

Det jeg syntes var mest nyttig med Simulation Mode, var at jeg kunne følge pakkene gjennom nettverket og se hvilke enheter som behandlet dem.

Samtidig forstår jeg at Simulation Mode er en simulering. Resultatene viser hvordan nettverket fungerte i Packet Tracer under testene, men er ikke målinger fra et fysisk bedriftsnettverk.


## 19. Telnet som kontrollert labøvelse

### 19.1 Hvorfor jeg testet Telnet

I denne delen skulle jeg teste fjernadministrasjon av en ruter i Cisco Packet Tracer.

Jeg brukte Telnet for å se hvordan jeg kunne koble til en ruter fra en klient og kjøre en kommando uten å bruke ruterens lokale konsoll.

Telnet er en eldre protokoll som ikke krypterer kommunikasjonen. Derfor brukte jeg den bare som en avgrenset øvelse i Packet Tracer.

### 19.2 Tilkobling fra PC-SALG

Jeg åpnet Command Prompt på PC-SALG og skrev:

```text
telnet 192.168.40.1
```

Adressen `192.168.40.1` tilhørte grensesnittet på R2-SALG som var koblet til salgsnettverket.

I den første tilkoblingen fikk jeg meldingen:

```text
Trying 192.168.40.1 ...Open

User Access Verification
Password:
% Password: timeout expired.
```

Forbindelsen ble deretter lukket.

Dette var ikke en vanlig nettverks-timeout. Telnet-forbindelsen ble åpnet, men passordinnloggingen ble ikke fullført før tidsfristen gikk ut.

Jeg prøvde derfor å koble til på nytt.

### 19.3 Vellykket innlogging

Jeg kjørte samme kommando en gang til:

```text
telnet 192.168.40.1
```

Denne gangen fullførte jeg innloggingen og kom til:

```text
R2-SALG>
```

Det viste at jeg hadde fått tilgang til kommandolinjen på ruteren gjennom Telnet.

Jeg skrev deretter:

```text
enable
show ip interface brief
```

Resultatet viste blant annet:

| Interface | IP-adresse | Status | Protocol |
|---|---|---|---|
| GigabitEthernet0/0 | 192.168.40.1 | up | up |
| GigabitEthernet0/1 | 10.0.0.2 | up | up |
| GigabitEthernet0/2 | unassigned | administratively down | down |
| Vlan1 | unassigned | administratively down | down |

Jeg kunne dermed kontrollere de aktive IP-grensesnittene på R2-SALG.

GigabitEthernet0/0 var tilkoblet salgsnettverket, mens GigabitEthernet0/1 ble brukt til forbindelsen mot R1-HQ.

### 19.4 Deaktivering av Telnet

Etter at jeg hadde gjennomført øvelsen, skulle Telnet ikke være tilgjengelig videre på de VTY-linjene jeg konfigurerte.

På R2-SALG brukte jeg disse kommandoene:

```text
enable
configure terminal
line vty 0 4
transport input none
no password
end
copy running-config startup-config
```

`line vty 0 4` valgte VTY-linjene 0 til 4.

`transport input none` fjernet tillatelsen til innkommende fjernadministrasjonsprotokoller på disse linjene.

`no password` fjernet passordet som var konfigurert på linjene. Det var `transport input none` som ble brukt til å stoppe innkommende tilkoblinger.

Til slutt lagret jeg konfigurasjonen med:

```text
copy running-config startup-config
```

Ruteren viste:

```text
Building configuration...
[OK]
```

Dette bekreftet at konfigurasjonen ble lagret.

### 19.5 Begrensning i testen

Jeg dokumenterte at Telnet-innloggingen fungerte, og at jeg kunne bruke `show ip interface brief` på R2-SALG.

Jeg har også skjermbilde av kommandoene som endret VTY 0–4, og av at konfigurasjonen ble lagret.

Jeg gjennomførte ikke en egen negativ test der jeg prøvde å koble til med Telnet etter deaktiveringen.

Derfor kan jeg bekrefte at innstillingene ble brukt og lagret, men ikke vise et separat testresultat som bekrefter at en ny Telnet-tilkobling ble avvist.

### 19.6 Hvorfor jeg ikke ville brukt Telnet i en bedrift

Telnet sender blant annet innloggingsinformasjon uten kryptering. Det kan gjøre informasjonen tilgjengelig for noen som klarer å fange opp nettverkstrafikken.

I en virkelig bedrift ville jeg derfor valgt SSH i stedet.

SSH gir kryptert kommunikasjon ved fjernadministrasjon og er et sikrere alternativ.

Jeg konfigurerte ikke SSH i denne øvelsen. Jeg nevner det som et alternativ jeg ville vurdert i en produksjonsløsning.

### 19.7 Hva jeg lærte

Jeg lærte at fjernadministrasjon er en nettverkstjeneste som kan brukes til å konfigurere og kontrollere en ruter.

Jeg fikk erfaring med å koble til R2-SALG fra en klient, logge inn og bruke en visningskommando.

Jeg lærte også at det ikke er nok å få en tjeneste til å fungere. Jeg må vurdere om tjenesten er sikker, og om den skal være aktiv etter at testen er ferdig.

Denne øvelsen gjorde det lettere for meg å forstå forskjellen mellom å teste en eldre protokoll i et laboratorium og å velge en sikker løsning i et bedriftsnettverk.


## 20. Feilsøking og kontrolltester i Packet Tracer

### 20.1 Hvorfor jeg jobbet med feilsøking

Under arbeidet med FjordTek AS opplevde jeg at noen forbindelser ikke fungerte slik jeg forventet.

Jeg brukte blant annet `ping`, `show ip interface brief` og `show ip route` for å kontrollere nettverket.

Jeg ville prøve å finne ut hvor feilene lå, rette dem og teste på nytt etterpå.

### 20.2 Feil mellom SW-CORE-HQ og R1-HQ

Da jeg jobbet med forbindelsen mellom SW-CORE-HQ og R1-HQ, fikk jeg først ikke kontakt med ruteren.

Jeg testet fra SW-CORE-HQ med:

```text
ping 10.0.0.5
```

I arbeidsloggen registrerte jeg at alle fem ping-forsøkene feilet.

Jeg undersøkte derfor den fysiske oppkoblingen i Packet Tracer.

Jeg fant at kabelen mot R1-HQ var koblet til feil port på SW-CORE-HQ. Den var koblet til `FastEthernet0/4`, mens den skulle gå til `GigabitEthernet0/1`.

Jeg rettet tilkoblingen slik:

| Enhet | Port |
|---|---|
| SW-CORE-HQ | GigabitEthernet0/1 |
| R1-HQ | GigabitEthernet0/0 |

Etterpå testet jeg forbindelsen på nytt:

```text
SW-CORE-HQ>ping 10.0.0.5

Sending 5, 100-byte ICMP Echos to 10.0.0.5:

!!!!!

Success rate is 100 percent (5/5)
```

**Resultat:** Jeg fikk fem svar av fem forsøk.

Dette viste at forbindelsen fungerte etter at jeg hadde rettet kabeltilkoblingen.

Jeg har et skjermbilde av den vellykkede testen. Resultatet før omkoblingen er registrert i arbeidsloggen, men jeg har ikke et eget bilde av de fem mislykkede ping-forsøkene.

### 20.3 Ping fra PC-SALG til SERVER01

Jeg opplevde også et annet resultat da jeg testet kommunikasjonen fra PC-SALG til SERVER01.

Jeg brukte disse adressene:

| Enhet | IP-adresse |
|---|---|
| PC-SALG | 192.168.40.100 |
| SERVER01 | 192.168.30.10 |

På PC-SALG kjørte jeg:

```text
ping 192.168.30.10
```

Den første testen viste:

```text
Request timed out.
Request timed out.
Request timed out.
Reply from 192.168.30.10: bytes=32 time<1ms TTL=125
```

Statistikken var:

| Resultat | Første test |
|---|---|
| Pakker sendt | 4 |
| Pakker mottatt | 1 |
| Pakker mistet | 3 |
| Pakketap | 75 % |

Jeg fikk altså ikke svar på de tre første forespørslene, men den fjerde fikk svar.

Det viste at serveren var nåbar ved slutten av denne testen, men at forbindelsen ikke hadde svart på alle forespørslene.

### 20.4 Ny ping-test

Jeg kjørte samme ping-kommando på nytt:

```text
ping 192.168.30.10
```

Denne gangen fikk jeg fire svar fra SERVER01.

Svarene viste `TTL=125`, og responstiden var under 1 ms i hver svarlinje.

Resultatet var:

| Resultat | Andre test |
|---|---|
| Pakker sendt | 4 |
| Pakker mottatt | 4 |
| Pakker mistet | 0 |
| Pakketap | 0 % |

Den andre testen viste at alle fire ping-forespørslene fungerte.

Jeg har et skjermbilde som viser både den første og den andre testen.

### 20.5 Hva jeg vet, og hva jeg ikke vet

Det var en tydelig forskjell mellom de to ping-testene fra PC-SALG til SERVER01.

I den første testen fikk jeg 75 % pakketap, mens den andre testen ga 0 % pakketap.

En mulig forklaring kan være at nettverket måtte lære MAC-adresser gjennom ARP, eller at det var en midlertidig forsinkelse i simuleringen.

Jeg har likevel ikke dokumentert nok til å fastslå den nøyaktige årsaken.

Derfor kan jeg ikke skrive at ARP var årsaken til pakketapet.

Jeg kan heller ikke si at nettverket var helt stabilt over tid bare fordi den andre testen fungerte.

Det jeg kan bekrefte, er at forbindelsen svarte på alle fire forespørslene under den andre testen.

### 20.6 Andre kontrolltester

Jeg gjennomførte også andre tester for å kontrollere kommunikasjonen i bedriftsnettverket.

| Test | Resultat |
|---|---|
| PC-ADMIN til gateway `192.168.10.1` | 4/4 svar, 0 % pakketap |
| PC-SUPPORT til SERVER01 `192.168.30.10` | 4/4 svar, 0 % pakketap |
| SW-CORE-HQ til R1-HQ `10.0.0.5` | 5/5 svar etter omkoblingen |
| Tracert fra ADMIN til SALG | Fire hopp, Trace complete |
| Tracert fra SALG til SERVER01 | Fire hopp, Trace complete |

Disse testene hjalp meg å kontrollere at de utvalgte forbindelsene fungerte.

Rutene og tracert-resultatene er forklart nærmere i kapittel 15, så jeg gjentar ikke alle detaljene her.

### 20.7 Hva jeg lærte av feilsøkingen

Jeg lærte at jeg bør kontrollere kabling og grensesnitt før jeg begynner å endre rutingskonfigurasjonen.

Da SW-CORE-HQ ikke fikk kontakt med R1-HQ, hjalp det å undersøke hvilken port kabelen var koblet til.

Jeg lærte også at jeg må være forsiktig med å trekke konklusjoner når et testresultat endrer seg.

Selv om ping fungerte ved andre forsøk fra PC-SALG, visste jeg ikke sikkert hvorfor tre pakker gikk tapt i den første testen.

Det viktigste jeg tar med meg, er at feilsøking handler om å undersøke symptomer, bruke riktige verktøy, teste mulige løsninger og kontrollere resultatene.

Jeg vil også skille mellom det jeg har bevist gjennom tester, og det jeg bare har en mulig forklaring på.


## 21. Akseptansetest (QA)

### 21.1 Hvorfor jeg gjennomførte en akseptansetest

Da jeg startet prosjektet, laget jeg en kravspesifikasjon med åtte krav, K1–K8.

Etter at jeg hadde jobbet med både det fysiske nettverket og FjordTek AS i Packet Tracer, gikk jeg gjennom kravene på nytt.

Jeg ville kontrollere hva som faktisk hadde fungert, hvilke tester jeg hadde gjennomført, og om jeg hadde nok dokumentasjon.

Jeg brukte arbeidsloggen, testresultatene og de opprinnelige skjermbildene fra prosjektet.

Jeg skiller mellom tester som fungerte, og tester der jeg mangler fullstendig dokumentasjon.

### 21.2 Akseptansetest – K1 til K8

| Krav | Test | Forventet resultat | Faktisk resultat | Status |
|---|---|---|---|---|
| K1 – Kommunikasjon mellom CLIENT01 og SERVER01 | `ping 192.168.50.10` fra CLIENT01 | Serveren svarer på ping | Jeg fikk 4 av 4 svar, med 0 % pakketap | Bestått ifølge arbeidsloggen |
| K2 – Fast IP på SERVER01 | Kontroll av IPv4-innstillinger på den fysiske serveren | SERVER01 bruker `192.168.50.10/24` som statisk adresse | Jeg satte opp adressen, men mangler en fullstendig `ipconfig /all` etter reinstallasjonen | Ikke fullstendig dokumentert |
| K3 – DHCP på CLIENT01 | Kontroll med `ipconfig /all` | Klienten får IP-adresse, gateway og DHCP-server fra riktig nettverk | CLIENT01 fikk `192.168.50.100/24`, gateway og DHCP-server `192.168.50.1` | Bestått ifølge arbeidsloggen |
| K4 – Tjeneste på SERVER01 | Åpne `Felles`, kontrollere SMB og opprette testfil | Klienten får tilgang til fildelingen og kan bruke den | SMB-forbindelsen fungerte. Jeg oppga at testfilen ble opprettet, men mangler eget bilde av filtesten | Delvis dokumentert |
| K5 – Routing mellom LAN i Packet Tracer | `ping`, `tracert` og `show ip route` | Klienter i forskjellige nettverk kan kommunisere | Tracert fra ADMIN til SALG og fra SALG til SERVER01 fullførte fire hopp. Rutetabellene viste riktige ruter | Bestått for de testede forbindelsene |
| K6 – DHCP, DNS og HTTP i Packet Tracer | Kontrollere klientadresser og teste intranettsiden med IP og DNS-navn | Klientene får riktige innstillinger og kan bruke DNS og HTTP | DHCP ble kontrollert på flere klienter. Jeg registrerte at intranettet fungerte med IP og DNS-navn. DNS- og HTTP-trafikk ble observert i Simulation Mode | Bestått for testede funksjoner, med dokumentasjonsbegrensninger |
| K7 – Testing og dokumentasjon | Kontrollere README, tester, bilder, `.pkt` og GitHub Projects | Arbeidet er dokumentert og kan gjennomgås av læreren | Testresultatene er skrevet inn. Jeg skal fortsatt flytte de originale bildene og Packet Tracer-filen til det nye repositoryet og fullføre GitHub Projects | Pågår |
| K8 – DHCP-fornyelse på CLIENT01 | `ipconfig /release`, `ipconfig /renew`, `ipconfig /all` og ping | Klienten får riktige innstillinger og kan fortsatt nå SERVER01 | DHCP-fornyelse og senere vellykket ping er registrert i arbeidsloggen. Hele testforløpet er ikke dokumentert samlet | Bestått ifølge arbeidsloggen, med begrenset direktebevis |

### 21.3 Krav som ikke er fullstendig dokumentert

Jeg fikk gjennomført mange av testene, men jeg mangler noen direkte skjermbilder fra det fysiske nettverket.

**K2 – SERVER01**

Jeg satte ifølge arbeidsloggen opp SERVER01 med statisk IP-adresse. Etter at Windows Server ble installert på nytt, fikk jeg ikke tatt vare på et fullstendig skjermbilde av den endelige IP-konfigurasjonen.

Jeg kan derfor ikke dokumentere den siste statiske konfigurasjonen fullt ut.

**K4 – SMB-fildeling**

Jeg fikk tilgang til den delte mappen `Felles`, og `Get-SmbConnection` viste en aktiv SMB-forbindelse.

Wireshark viste også SMB-trafikk, og Nmap viste at TCP-port 445 var åpen.

Jeg mangler likevel et eget bilde som bekrefter testfilen og de endelige Share- og NTFS-tillatelsene.

**K8 – DHCP-fornyelse**

Jeg brukte DHCP-fornyelse på klienten og dokumenterte riktige nettverksinnstillinger. Jeg fikk også svar fra SERVER01 i en senere ping-test.

Jeg har ikke et sammenhengende skjermbilde som viser hele fornyelsen og den påfølgende forbindelsestesten.

Jeg har ikke tilgang til skoleutstyret for å hente nye direktebevis. Derfor bruker jeg de opprinnelige testresultatene og er tydelig på hva som mangler.

### 21.4 Begrensninger i Packet Tracer-testene

I Packet Tracer fungerte de fleste forbindelsene jeg testet.

Jeg hadde likevel noen resultater som jeg måtte undersøke nærmere.

Fra PC-SALG til SERVER01 fikk jeg først 75 % pakketap. Da jeg gjentok testen, fikk jeg 0 % pakketap.

Jeg vet ikke sikkert hvorfor den første testen mistet tre pakker. Derfor bruker jeg ikke den andre testen som bevis på at hele nettverket var stabilt over tid.

Jeg testet også Telnet på R2-SALG. Innloggingen fungerte, og jeg kunne bruke `show ip interface brief`.

Etterpå endret jeg VTY-innstillingene for å deaktivere Telnet og lagret konfigurasjonen. Jeg gjennomførte ikke en egen test av at en ny Telnet-tilkobling ble avvist.

For PC-SALG har jeg dokumentert riktig IP-adresse og gateway, men det opprinnelige bildet viser ikke DHCP-serveren. Derfor kan jeg ikke bruke dette bildet alene som bevis på DHCP-tildelingen.

### 21.5 Status på dokumentasjonen

I det tidligere repositoryet hadde jeg samlet README, Packet Tracer-filen og bildene fra prosjektet.

I dette nye repositoryet skriver jeg dokumentasjonen på nytt med en tydeligere struktur og mer presise forklaringer.

Jeg skal fortsatt legge inn de originale bildene, Packet Tracer-filen og lenken til GitHub Projects.

Jeg må også kontrollere at læreren har tilgang til repositoryet og prosjektbrettet før innlevering.

Derfor markerer jeg ikke hele dokumentasjonen eller den samlede akseptansetesten som ferdig ennå.

### 21.6 Min vurdering av kravene

Jeg mener at testresultatene viser at jeg fikk den grunnleggende kommunikasjonen til å fungere i det fysiske nettverket.

I Packet Tracer fikk jeg bygget et større nettverk med flere LAN, VLAN, ruting, DHCP, DNS og HTTP.

Jeg har også gjennomført feilsøking og brukt forskjellige verktøy for å kontrollere resultatene.

Samtidig har jeg lært at det ikke er nok å få noe til å fungere. Jeg må også kunne dokumentere hvordan jeg testet det, og hva resultatene faktisk viser.

Jeg vurderer derfor ikke alle kravene som like godt dokumentert.

Akseptansetesten blir endelig ferdig når jeg har lagt inn de opprinnelige bevisene i det nye repositoryet og kontrollert leveransen.

## 22. Oppsummering og egenvurdering

### 22.1 Hvordan jeg opplevde prosjektet

Dette prosjektet har vært både lærerikt og utfordrende for meg. Jeg har jobbet med et fysisk nettverk på skolen og et større bedriftsnettverk i Cisco Packet Tracer.

Jeg har fått mye praktisk erfaring, men jeg vil også være ærlig om hvordan jeg vurderer min egen innsats og forståelse.

Jeg er fornøyd med at jeg fikk bygget nettverkene og gjennomført mange tester. Samtidig er jeg ikke helt fornøyd med hvor godt jeg behersker alt jeg har jobbet med.

Jeg har lært mye underveis, men det er fortsatt flere ting jeg synes er vanskelige å huske og forklare uten å bruke notater eller få hjelp.

Jeg merker at det er forskjell på å gjennomføre en oppgave med veiledning og å kunne gjennomføre den helt selvstendig.

For meg er dette en viktig forskjell.

Jeg ønsker ikke bare å få et nettverk til å fungere. Jeg vil forstå hvorfor det fungerer, hvordan jeg skal finne feil, og hvordan jeg kan bygge det opp igjen uten å følge den samme oppskriften.

Det føler jeg ikke at jeg mestrer fullt ut ennå.

### 22.2 Hva jeg lærte av det fysiske nettverket

I den første delen jobbet jeg med en TP-Link-ruter, en switch, Windows Server og min egen laptop.

Jeg fikk praktisk erfaring med hvordan enhetene kobles sammen, og hvordan IP-adresser, subnettmasker og gateway brukes.

Jeg lærte forskjellen mellom statisk IP og DHCP ved å teste begge deler på CLIENT01.

Jeg forstod også bedre hvorfor en server bør ha en kjent og stabil IP-adresse, mens en vanlig klient kan få nettverksinnstillingene automatisk.

En viktig erfaring var at en nettverksforbindelse kan ha flere forskjellige problemer.

Da jeg fjernet USB-Ethernet-adapteren fra CLIENT01, mistet jeg kontakten med serveren. Jeg brukte `Get-NetAdapter` og `ping` til å undersøke problemet og kontrollere forbindelsen etter at adapteren var satt tilbake.

Jeg fikk også erfaring med Windows Firewall etter reinstallasjonen av SERVER01. Der lærte jeg at brannmuren kan blokkere ICMP selv om enheten er koblet til nettverket.

Jeg forstod mer av disse problemene da jeg jobbet med dem i praksis.

Likevel trenger jeg fortsatt trening på å finne lignende feil uten å vite på forhånd hva årsaken er.

### 22.3 Hva jeg lærte av Packet Tracer

I Packet Tracer bygde jeg et større nettverk for FjordTek AS.

Jeg jobbet med VLAN, Inter-VLAN Routing, statisk ruting, DHCP, DNS og HTTP.

Det var nyttig å se hvordan flere nettverk kunne kobles sammen, og hvordan en klient i én avdeling kunne kommunisere med en server i en annen.

Jeg lærte også mer om rutetabeller og forskjellen mellom direkte tilkoblede nettverk, statiske ruter og standardruter.

Det var spesielt nyttig å bruke `show ip route` og `tracert`, fordi jeg kunne se hvilke veier trafikken tok gjennom nettverket.

Samtidig synes jeg fortsatt at ruting og subnetting er krevende.

Jeg kan forstå en forklaring når jeg får den, men det betyr ikke alltid at jeg klarer å konfigurere hele løsningen selv etterpå.

Jeg trenger derfor mer øvelse på å lage adresseplaner, konfigurere VLAN og skrive statiske ruter uten å se på den ferdige konfigurasjonen.

### 22.4 Hva jeg lærte av feilsøking

Feilsøking var en av de viktigste delene av prosjektet.

Jeg lærte at det er bedre å undersøke en feil systematisk enn å endre flere innstillinger tilfeldig.

Da forbindelsen mellom SW-CORE-HQ og R1-HQ ikke fungerte, undersøkte jeg oppkoblingen og fant at kabelen var koblet til feil port.

Etter at jeg rettet tilkoblingen, fikk jeg fem av fem svar fra ruteren.

Jeg lærte også at et testresultat ikke alltid forteller hele historien.

Da jeg testet fra PC-SALG til SERVER01, fikk jeg først 75 % pakketap. Ved neste test fikk jeg 0 % pakketap.

Jeg kunne se at resultatet var blitt bedre, men jeg hadde ikke nok informasjon til å fastslå hvorfor de første pakkene gikk tapt.

Dette lærte meg at jeg må skille mellom det jeg har bevist, og det jeg tror kan være årsaken.

Jeg trenger fortsatt mer trening i å velge riktig test når jeg møter en ukjent feil.

### 22.5 Bruk av nettverksverktøy

Jeg fikk bruke flere verktøy, blant annet `ping`, `ipconfig`, `tracert`, Nmap og Wireshark.

Jeg lærte at de har forskjellige oppgaver.

Med ping kunne jeg teste ICMP-kommunikasjon. Med Nmap kunne jeg undersøke TCP-porter, mens Wireshark lot meg se nærmere på nettverkspakker.

I Packet Tracer brukte jeg Simulation Mode for å undersøke ARP, ICMP, DNS og HTTP.

Jeg synes disse verktøyene er interessante, men jeg merker at jeg ikke kan tolke alle resultatene like godt ennå.

Noen ganger trenger jeg å få forklart hva en bestemt verdi eller melding betyr.

Jeg ønsker å bli flinkere til å lese resultatene selv og forstå hva de forteller om nettverket.

### 22.6 Hva jeg syntes var vanskelig

Det vanskeligste for meg var å holde oversikt over alle innstillingene og forstå hvordan de påvirket hverandre.

I Packet Tracer måtte jeg tenke på IP-adresser, subnettmasker, VLAN, gateway, grensesnitt og rutetabeller samtidig.

Jeg kunne forstå én del av konfigurasjonen, men bli usikker når jeg skulle koble flere deler sammen.

Jeg syntes også det var utfordrende å huske Cisco-kommandoene og vite hvilken kommando jeg skulle bruke i en bestemt situasjon.

En annen utfordring var å forklare tekniske begreper med mine egne ord.

Det er lettere for meg å forstå en forklaring når jeg leser den, enn å forklare det samme uten hjelp etterpå.

Dette er noe jeg ønsker å forbedre.

### 22.7 Hvorfor jeg ikke er helt fornøyd med mitt eget nivå

Selv om prosjektet inneholder mange vellykkede tester, er jeg ikke helt fornøyd med min egen forståelse ennå.

Jeg har klart å gjennomføre mange oppgaver, men jeg har også hatt behov for forklaringer, veiledning og notater underveis.

Hvis jeg skulle bygget hele FjordTek AS-nettverket på nytt uten å se på det gamle prosjektet, tror jeg at flere deler fortsatt ville vært utfordrende.

Jeg ville sannsynligvis trengt å repetere spesielt VLAN-konfigurasjon, statisk ruting, DHCP og kontroll av rutetabeller.

Jeg ønsker å komme til et nivå der jeg ikke bare kjenner igjen kommandoene, men også kan velge og bruke dem selv.

Jeg er derfor mer fornøyd med fremgangen min enn med hvor selvstendig jeg er akkurat nå.

Det betyr ikke at jeg ikke har lært noe. Tvert imot har jeg lært mye, men prosjektet har også vist meg hvor mye jeg fortsatt trenger å øve på.

Jeg vil ikke si at jeg behersker noe fullt ut bare fordi jeg fikk det til én gang.

For meg vil det å mestre en oppgave bety at jeg kan gjennomføre den flere ganger, forklare hvorfor jeg gjør det, og finne feil hvis resultatet ikke blir som forventet.

Det er dette nivået jeg ønsker å jobbe mot.

### 22.8 Hvordan jeg brukte kunstig intelligens i læringsarbeidet

Under prosjektet brukte jeg også kunstig intelligens som et hjelpemiddel.

Jeg brukte det blant annet til å få forklart nettverksbegreper, forstå kommandoer og få eksempler når noe var vanskelig.

Når jeg ikke forstod hvorfor en bestemt konfigurasjon var nødvendig, kunne jeg stille spørsmål og få en forklaring med enklere ord.

Dette var spesielt nyttig når jeg jobbet med VLAN, ruting, DHCP, DNS og tolkning av testresultater.

Jeg har også brukt kunstig intelligens som støtte til å organisere og formulere deler av dokumentasjonen på norsk.

Det praktiske prosjektet, konfigurasjonene og testresultatene bygger på arbeidet jeg gjennomførte med nettverket og i Packet Tracer.

Samtidig vil jeg være åpen om at jeg har fått hjelp til både faglige forklaringer og skriftlig fremstilling.

Jeg ser både fordeler og begrensninger med denne måten å lære på.

Fordelen er at jeg kan få en forklaring når jeg står fast, og stille oppfølgingsspørsmål til jeg forstår mer.

Utfordringen er at jeg noen ganger kan oppleve at jeg forstår et tema når jeg leser forklaringen, men likevel ikke klarer å gjennomføre oppgaven selv etterpå.

Derfor er jeg opptatt av at kunstig intelligens ikke skal erstatte min egen forståelse eller praktiske trening.

Jeg må også være kritisk til svarene jeg får. En forklaring eller en foreslått kommando er ikke automatisk riktig, og jeg må kontrollere det mot oppgaven, konfigurasjonen og de faktiske testresultatene.

Jeg ønsker å bli mindre avhengig av hjelp når jeg skal løse oppgaver.

Målet mitt er å bruke kunstig intelligens som støtte når jeg lærer, men etter hvert kunne forklare og utføre flere av oppgavene på egen hånd.

### 22.9 Hvorfor jeg trenger repetisjon og mer praktisk trening

Jeg har merket at jeg lærer best når jeg får jobbe praktisk og gjenta oppgavene.

Det er ikke nok for meg å lese en forklaring én gang.

Jeg trenger å bruke kunnskapen flere ganger for å huske den og forstå den bedre.

Hvis det går en stund uten at jeg jobber med nettverk, kan jeg bli usikker på kommandoer og fremgangsmåter jeg tidligere har brukt.

Jeg vil derfor repetere de grunnleggende oppgavene i stedet for å gå for raskt videre til mer avanserte temaer.

Jeg ønsker spesielt å trene mer på:

- IP-adressering og subnetting.
- Konfigurering av VLAN og accessporter.
- Inter-VLAN Routing og statiske ruter.
- DHCP-konfigurasjon og kontroll av klientinnstillinger.
- Å lese `show ip route` og forstå next-hop.
- Å bruke Nmap og Wireshark til å undersøke feil.
- Å forklare resultatene med mine egne ord.

Jeg vil også øve på å gjennomføre oppgaver uten å se på løsningen først.

Hvis jeg står fast, kan jeg bruke notatene mine eller få en forklaring. Deretter vil jeg prøve den samme oppgaven på nytt uten hjelp.

På denne måten kan jeg kontrollere om jeg faktisk har lært noe, eller om jeg bare fulgte instruksjonene.

### 22.10 Hvordan jeg ønsker å øve videre

Jeg ønsker å lage en enkel plan for videre trening.

Først vil jeg bygge et lite nettverk i Packet Tracer med én ruter, én switch og to klienter.

Deretter vil jeg konfigurere IP-adressene selv og teste kommunikasjonen.

Når jeg mestrer dette bedre, vil jeg legge til flere VLAN og øve på ruting mellom dem.

Etterpå vil jeg prøve å koble sammen to rutere med statiske ruter, uten å kopiere konfigurasjonen fra dette prosjektet.

Jeg vil også lage noen kontrollerte feil, for eksempel feil subnettmaske, feil gateway, feil VLAN eller manglende rute.

Da vil jeg prøve å undersøke problemet selv før jeg leser en forklaring eller ber om hjelp.

Jeg tror det vil være en god måte å utvikle den praktiske forståelsen min på.

Etter hver øvelse vil jeg prøve å forklare hva jeg gjorde, hvorfor jeg gjorde det, og hvordan jeg kontrollerte at det fungerte.

Jeg tror at når jeg klarer å forklare en oppgave uten å lese fra notatene, er det et tegn på at jeg har forstått mer.

### 22.11 Hva jeg ville gjort annerledes

Hvis jeg skulle gjennomført prosjektet på nytt, ville jeg vært mer systematisk fra starten.

Jeg ville laget en tydeligere plan for dokumentasjon og tatt skjermbilder med en gang jeg hadde gjennomført en test.

Jeg oppdaget at jeg hadde skrevet ned flere resultater, men at jeg manglet enkelte skjermbilder som kunne bekrefte dem direkte.

Jeg ville også brukt mer tid på å kontrollere at en løsning fungerer over flere tester, i stedet for å være fornøyd etter ett vellykket forsøk.

I tillegg ville jeg satt av mer tid til repetisjon mellom de forskjellige oppgavene.

Noen ganger gikk jeg videre til neste del selv om jeg fortsatt var litt usikker på den forrige.

Neste gang vil jeg prøve å stoppe opp og kontrollere om jeg faktisk kan forklare og gjennomføre oppgaven selv.

Jeg tror det vil gi meg et bedre faglig grunnlag.

### 22.12 Sikkerhet og videre læring

Prosjektet gjorde meg også mer interessert i nettverkssikkerhet.

Jeg brukte Telnet som en kontrollert øvelse i Packet Tracer og lærte hvorfor denne protokollen ikke er et godt valg for moderne fjernadministrasjon.

Senere ønsker jeg å lære mer om SSH, brannmurregler og tilgangskontroll mellom nettverk.

Men jeg mener at det er viktig å forstå grunnleggende nettverk godt før jeg går videre til mer avansert sikkerhet.

Hvis jeg ikke forstår hvordan nettverkstrafikken beveger seg, blir det også vanskeligere å forstå hvordan jeg skal beskytte den.

Derfor vil jeg prioritere grunnleggende nettverksforståelse og feilsøking videre.

### 22.13 Forskjellen mellom fysisk nettverk og Packet Tracer

Jeg synes det var nyttig å jobbe med både ekte utstyr og et simulert nettverk.

I den fysiske delen måtte jeg håndtere kabler, nettverksadaptere, Windows-innstillinger og faktiske forbindelser.

I Packet Tracer kunne jeg bygge en større løsning med flere VLAN, rutere og tjenester uten å ha alt utstyret fysisk tilgjengelig.

Simulation Mode gjorde det lettere å se hvordan pakker beveget seg gjennom nettverket.

Samtidig forstod jeg at en simulering ikke viser alle utfordringer som kan oppstå med ekte maskinvare.

Begge arbeidsmåtene var derfor lærerike.

Jeg ønsker å fortsette å bruke Packet Tracer til repetisjon, men vil også gjerne få mer praktisk erfaring med fysisk nettverksutstyr.

### 22.14 Min samlede egenvurdering

Når jeg ser tilbake på prosjektet, er jeg fornøyd med at jeg fikk gjennomført mange praktiske oppgaver og lært mer om nettverk.

Jeg fikk satt opp et fysisk nettverk, jobbet med Windows Server og bygget et større bedriftsnettverk i Packet Tracer.

Jeg har brukt flere verktøy til testing og feilsøking, og jeg har fått bedre forståelse av sammenhengen mellom IP-adresser, ruting og nettverkstjenester.

Men jeg vil ikke si at jeg er helt fornøyd med mitt eget faglige nivå ennå.

Jeg har fortsatt behov for mye repetisjon, og jeg har brukt både notater, veiledning og kunstig intelligens for å forstå flere av temaene.

Dette har hjulpet meg i arbeidet, men det viser også at jeg fortsatt trenger å utvikle min egen forståelse og selvstendighet.

Jeg ønsker ikke å være avhengig av å få forklart den samme oppgaven hver gang jeg skal utføre den.

Jeg vil kunne planlegge et nettverk, konfigurere det, finne feil og forklare resultatene med større sikkerhet.

Jeg mener derfor at prosjektet har vært et viktig steg i riktig retning, men ikke et punkt der jeg kan si at jeg behersker alt.

Jeg er stolt av fremgangen jeg har hatt, men samtidig ikke helt tilfreds med hvor mye jeg kan utføre uten hjelp.

Det gir meg motivasjon til å fortsette å øve.

For meg handler det ikke bare om å få en god karakter. Jeg ønsker å utvikle kunnskap som jeg faktisk kan bruke videre i utdanningen og senere i arbeidslivet.

Det viktigste jeg tar med meg fra prosjektet, er at jeg har lært mye, men også blitt mer bevisst på hva jeg fortsatt må jobbe med.

Jeg vil fortsette å repetere, teste og utfordre meg selv til jeg blir tryggere og mer selvstendig innen IT og nettverk.



## 23. GitHub Projects og arbeidsprosess

### 23.1 Hvorfor jeg brukte GitHub

I dette prosjektet brukte jeg GitHub for å samle dokumentasjonen og holde oversikt over arbeidet mitt.

Jeg ville ha ett sted der jeg kunne dokumentere planlegging, nettverksoppsett, tester, feilsøking og resultatene fra prosjektet.

GitHub var også en del av oppgaven fra læreren. Jeg skulle bruke repository, README, commits og GitHub Projects til å vise hvordan arbeidet utviklet seg.

Jeg lærte at GitHub ikke bare brukes til programmering. Det kan også brukes til teknisk dokumentasjon og prosjektstyring.

### 23.2 Arbeidet i det opprinnelige repositoryet

Jeg startet med å dokumentere arbeidet i repositoryet `mitt-forste-nettverk`.

Der brukte jeg GitHub Issues til å registrere oppgaver knyttet til blant annet:

- Planlegging og kravspesifikasjon.
- Oppkobling av det fysiske nettverket.
- IP-adressering og DHCP.
- Testing med ping, Nmap og Wireshark.
- Bygging og konfigurering av FjordTek AS.
- Testing i Simulation Mode.
- Feilsøking og akseptansetest.
- Dokumentasjon og innlevering.

Jeg brukte også GitHub Projects til å få en bedre oversikt over oppgavene.

I arbeidsloggen fra 29.10.2026 hadde jeg registrert 19 oppgaver i Done og 2 i In Progress. De to oppgavene som fortsatt pågikk, gjaldt QA og den siste kontrollen før innlevering.

Dette var statusen jeg hadde dokumentert i det opprinnelige prosjektet, ikke en kontroll av dagens status i det nye repositoryet.

### 23.3 Hvorfor jeg startet dokumentasjonen på nytt

Etter at jeg hadde jobbet med prosjektet en stund, ønsket jeg å gjøre dokumentasjonen tydeligere og mer oversiktlig.

Det opprinnelige README-dokumentet inneholdt mye informasjon, men noen temaer ble gjentatt, og enkelte resultater var vanskeligere å finne.

Derfor begynte jeg å skrive dokumentasjonen på nytt i et eget repository.

Jeg bruker de samme nettverksoppsettene, testresultatene og bildene fra det opprinnelige prosjektet.

Jeg bygger ikke et helt nytt nettverk og gjennomfører ikke nye tester bare fordi jeg skriver rapporten på nytt.

Målet mitt er å forklare det tidligere arbeidet bedre og samle informasjonen i en mer logisk rekkefølge.

Jeg har også prøvd å skille tydeligere mellom resultater som har direkte skjermbildebevis, og resultater som bare er registrert i arbeidsloggen.

### 23.4 GitHub Projects og Kanban

Jeg skal bruke GitHub Projects med Board-visning i det nye repositoryet.

Brettet skal ha tre kolonner:

| Kolonne | Hva den betyr |
|---|---|
| Todo | Oppgaver jeg ikke har begynt på |
| In Progress | Oppgaver jeg arbeider med |
| Done | Oppgaver jeg har fullført |

Jeg vil bruke konkrete oppgaver, slik at jeg lettere kan følge med på hva som mangler.

I den siste dokumentasjonsfasen gjelder dette blant annet kontroll av README, overføring av bilder, opplasting av Packet Tracer-filen og gjennomgang av akseptansetesten.

Jeg vil ikke markere en oppgave som ferdig før jeg faktisk har gjennomført den.

Siden jeg nå skriver dokumentasjonen på nytt, skal jeg også skille mellom det praktiske nettverksarbeidet som allerede ble gjennomført, og arbeidet som fortsatt gjenstår med den nye GitHub-leveransen.

Det er viktig for meg at prosjektbrettet viser den faktiske statusen, ikke bare at alle oppgavene ser ferdige ut.

### 23.5 Commits og dokumentasjon av endringer

Jeg har brukt README til å forklare hva jeg gjorde i prosjektet, hvilke innstillinger jeg valgte, og hvilke tester jeg gjennomførte.

I det nye repositoryet deler jeg dokumentasjonen inn i flere kapitler og lagrer endringene underveis.

Jeg ønsker å bruke tydelige commit-meldinger som forteller hva som er lagt til eller rettet.

For eksempel:

- `Dokumenterte IP-adresseplan og DHCP-oppsett`
- `Dokumenterte VLAN og inter-VLAN-ruting`
- `Rettet Wireshark-dokumentasjon med faktiske pakkefunn`
- `La til akseptansetest for krav K1–K8`

På den måten blir det lettere å følge endringene.

Samtidig er jeg klar over at commit-historikken i det nye repositoryet først og fremst viser hvordan jeg har skrevet og organisert dokumentasjonen på nytt. Den viser ikke hele den opprinnelige byggeprosessen fra oktober.

Den prosessen har jeg beskrevet ut fra den tidligere arbeidsloggen og testresultatene.

### 23.6 Filer og dokumentasjon

I det opprinnelige repositoryet samlet jeg både README, bilder og Packet Tracer-prosjektfilen.

Den gamle prosjektkopien inneholder 41 bilder: 11 i mappen for det fysiske nettverket, 29 fra Packet Tracer og ett fra den første planleggingen.

Ikke alle bildene er testbevis. Noen viser utstyr, nettverksdesign eller teori.

Jeg har også den ferdige Packet Tracer-filen:

`FjordTek-AS-Ferdig.pkt`

Disse filene skal overføres til det nye repositoryet.

Jeg ønsker å organisere dem slik:

```text
fjordtek-as-vg2-it/
├── README.md
├── images/
│   ├── fysisk-nettverk/
│   ├── packet-tracer/
│   └── designhistorikk/
└── packet-tracer/
    └── FjordTek-AS-Ferdig.pkt
```

Når bildene er lastet opp, skal jeg legge inn lenker i README der de passer til testene.

Jeg må også kontrollere at alle filnavn og lenker stemmer.

### 23.7 Hva jeg fortsatt må gjøre

Før jeg kan levere prosjektet, må jeg fullføre den nye GitHub-leveransen.

Det som gjenstår, er blant annet:

- Kontrollere README og rette eventuelle feil.
- Overføre de opprinnelige bildene.
- Laste opp den ferdige Packet Tracer-filen.
- Opprette eller koble riktig GitHub Project til det nye repositoryet.
- Registrere og oppdatere oppgavene i Kanban-brettet.
- Kontrollere at dokumentasjonen og filene kan åpnes.
- Kontrollere at læreren har tilgang til repositoryet og prosjektbrettet.
- Gjennomføre den siste leveransekontrollen.

Jeg skal ikke markere innleveringen som ferdig før disse punktene er kontrollert.

### 23.8 Hva jeg lærte av å bruke GitHub

Jeg har lært at dokumentasjon er en viktig del av et IT-prosjekt.

Det er ikke nok å få nettverket til å fungere hvis jeg ikke klarer å forklare hvordan det er bygget, hvilke tester jeg har gjennomført, eller hva jeg gjorde da noe gikk galt.

Jeg har også erfart at det er lettere å dokumentere arbeidet mens jeg gjennomfører det, enn å prøve å samle alle detaljer etterpå.

I ettertid oppdaget jeg at noen testresultater manglet direkte skjermbilder. Det gjorde arbeidet med den siste dokumentasjonen vanskeligere.

Neste gang vil jeg være mer systematisk med filnavn, skjermbilder, arbeidslogg og commits fra starten av.

Jeg trenger fortsatt mer erfaring med GitHub Projects og Kanban, men jeg forstår bedre hvorfor slike verktøy brukes til å planlegge og følge et prosjekt.

Målet mitt videre er å bli bedre både på det tekniske arbeidet og på å dokumentere det på en ryddig måte.
