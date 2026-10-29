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

I den første delen av prosjektet satte jeg opp et fysisk nettverk på skolen. Jeg brukte en ruter, en switch, en server og en bærbar PC.

| Navn | Utstyr | Oppgave i nettverket |
|---|---|---|
| R1 | TP-Link Archer C50 | Kobler nettverket videre og deler ut IP-adresser med DHCP |
| SW1 | Switch | Kobler sammen enhetene i det lokale nettverket |
| SERVER01 | Stasjonær PC med Windows Server | Brukes som server med fast IP-adresse og fildeling |
| CLIENT01 | ASUS-laptop med Windows | Brukes som klient for å teste nettverket |

Jeg brukte Ethernet-kabler for å koble enhetene sammen.

### 3.2 Hvordan jeg koblet nettverket

Jeg startet med å koble SERVER01 og CLIENT01 til switchen. Deretter koblet jeg en LAN-port på ruteren til switchen.

Jeg brukte følgende porter:

| Tilkobling | Port på SW1 | Kabel |
|---|---|---|
| SW1 til R1 | Port 1 | Sølvfarget |
| SW1 til SERVER01 | Port 2 | Gul |
| SW1 til CLIENT01 | Port 3 | Gul |

Jeg koblet også den grønne kabelen fra skolens nettverksuttak til WAN-porten på ruteren.

Denne oppkoblingen gjorde at SERVER01 og CLIENT01 kunne være i samme lokale nettverk og kommunisere gjennom switchen.

### 3.3 Kontroll av fysisk tilkobling

Etter at kablene var koblet til, kontrollerte jeg at utstyret hadde strøm og at nettverksportene viste linklys.

Dette gjorde jeg før jeg begynte med IP-adressene, fordi jeg ville være sikker på at den fysiske tilkoblingen var på plass.

Jeg tok også bilder av utstyret og kablene for å vise hvordan nettverket var koblet sammen.

### 3.4 Hvorfor jeg koblet nettverket slik

Jeg brukte switchen fordi både SERVER01 og CLIENT01 skulle være koblet til det samme lokale nettverket.

Ruteren fungerte som gateway og DHCP-server for klienten. SERVER01 skulle ha fast IP-adresse, slik at CLIENT01 kunne finne serveren på samme adresse hver gang.

TP-Link-ruteren ble konfigurert gjennom nettleseren. Derfor brukte jeg ikke Cisco IOS-kommandoer på den fysiske ruteren.

Etter at jeg hadde kontrollert tilkoblingene, gikk jeg videre til å konfigurere IP-adressene og teste kommunikasjonen mellom enhetene.

## 4. IP-adresseplan og konfigurering

### 4.1 Adresseplan

Etter at jeg hadde koblet sammen utstyret, begynte jeg med IP-adressene.

Jeg brukte nettverket `192.168.50.0/24`. Det betyr at enhetene i dette labnettet har subnettmasken `255.255.255.0`.

| Enhet | IP-adresse | Subnettmaske | Gateway | Adressering |
|---|---|---|---|---|
| R1 | 192.168.50.1 | 255.255.255.0 | – | Fast |
| SERVER01 | 192.168.50.10 | 255.255.255.0 | 192.168.50.1 | Fast |
| CLIENT01 | 192.168.50.100 | 255.255.255.0 | 192.168.50.1 | DHCP |

Jeg valgte å ha faste adresser på ruteren og serveren. CLIENT01 skulle få IP-adresse automatisk fra ruteren.

### 4.2 Konfigurering av ruteren

Jeg brukte en TP-Link Archer C50 som R1. For å konfigurere ruteren åpnet jeg administrasjonssiden i nettleseren.

Jeg endret LAN-adressen til:

- IP-adresse: `192.168.50.1`
- Subnettmaske: `255.255.255.0`

Etterpå konfigurerte jeg DHCP på ruteren.

DHCP-området ble satt til:

- Startadresse: `192.168.50.100`
- Sluttadresse: `192.168.50.199`
- Default gateway: `192.168.50.1`

Jeg brukte dette området fordi klientene skulle få adresser automatisk. Samtidig ville jeg holde de lave adressene utenfor DHCP-området, slik at de kunne brukes til utstyr som skulle ha faste adresser.

SERVER01 hadde for eksempel adressen `192.168.50.10`, som ikke ligger innenfor DHCP-området.

### 4.3 Konfigurering av SERVER01

Jeg satte opp SERVER01 med fast IP-adresse:

- IP-adresse: `192.168.50.10`
- Subnettmaske: `255.255.255.0`
- Default gateway: `192.168.50.1`

Serveren skulle bruke den samme adressen hele tiden, slik at klienten kunne finne den når jeg skulle teste ping og fildeling.

Jeg brukte Windows sine nettverksinnstillinger til å konfigurere adressen.

Windows Server ble senere installert på nytt. Jeg har ikke et nytt skjermbilde som bekrefter hele IP-konfigurasjonen etter reinstallasjonen. Derfor kan jeg ikke dokumentere den siste serverkonfigurasjonen fullt ut.

### 4.4 Konfigurering av CLIENT01

På CLIENT01 brukte jeg automatisk IP-adressering (DHCP).

Etter at ruteren var konfigurert, fikk klienten adressen `192.168.50.100` fra DHCP-området.

Jeg kontrollerte nettverksinnstillingene med Windows-kommandoen:

`ipconfig /all`

Jeg brukte denne kommandoen for å kontrollere blant annet IP-adressen, subnettmasken, gatewayen og DHCP-innstillingene.

Under arbeidet hadde jeg også problemer med gamle nettverksinnstillinger og tilkoblingen på laptopen. Dette beskriver jeg nærmere i delen om feilsøking.

### 4.5 Hvorfor jeg brukte denne løsningen

Jeg brukte fast IP-adresse på SERVER01 fordi serveren skulle være tilgjengelig på en kjent adresse.

CLIENT01 fikk IP-adresse automatisk med DHCP. Det gjør det enklere å koble klienter til nettverket uten å konfigurere IP-adressen manuelt på hver PC.

Ruteren hadde adressen `192.168.50.1` og fungerte som standard gateway for enhetene.

Jeg brukte samme subnett på klienten og serveren fordi de skulle kommunisere i det samme lokale nettverket.

Etter konfigureringen testet jeg forbindelsen med ping. Resultatene fra disse testene dokumenterer jeg i testdelen av rapporten.


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
