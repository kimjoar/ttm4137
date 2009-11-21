TTM4137
=======

Læringsmålene er kunnskap og forståelse om

* Security threats in wireless communication systems, thereby establishing a basis for vulnerability analysis skill.
* Some of the models, design principles, mechanisms and solutions used in wireless network security.
* Topical wireless technologies (WLAN, UMTS, Adhoc).

Disse notatene er primært basert på Edney & Arbaugh: _Real 802.11 Security_ og Niemi & Nyberg: _UMTS Security_.

Hva var problemet i WEP?
------------------------

Står for Wired Equivalent Privacy. 

Mål i 1999-standarden inkluderer rimelig sterk, selvsynkronisering for hver PDU (Protocol Data Unit), effektiv i hardware og software, og eksporterbar. Standarden spesifiserte 40-bits nøkler, men en ikke-standard utvidelse bruker 104 bit. Definerte også to typer sikkerhet: åpent (dvs ingen sikkerhet) og delt nøkkel.

Og med delt nøkkel starter problemet. Greit for et lite hjemmenettverk, men hva skjer om man skal implementere det i en stor organisasjon? Problemer er blant annet at det er ingen måte å skille brukere og det er manuell distribusjon av nøkkel.

* Autentiseringsfase. Bevise sin identitet til hverandre. Problem: ingen hemmelig token, altså ingen måte å vite at etterfølgende meldinger kommer fra samme enhet. Meningsløst. Med delt nøkkel er målet at mobil enhet skal vise at den har nøkkelen. AP sender plaintext, får tilbake ciphertext. Snilt for hackere.
* Krypteringsfase. Benytter RC4. Enkel å implementere. Rask. Initialisering og kryptering skjer på hver pakke. 24 bit til initialiseringsvektor (IV). IV endres for hver pakke, og sendes sammen med pakken. Gjør at samme plaintext gir forskjellige ciphertext. Nøkkel til RC4 er sammensatt av hemmelig nøkkel og IV. Problemet er at IV aldri bør brukes mer enn én gang per nøkkel. I WEP er det 24 bit, dvs ca 17 mill IV-verdier. Mange systemer starter med samme IV etter oppstart, og benytter et pseudorandom bytte.

![WEP Block Diagram](http://github.com/kjbekkelund/ttm4137/raw/master/media/wep-block-diagram.png)

### Svakheter

* IV for kort
* Svake nøkler på grunn av oppbygningen (FMS-angrepet)
* Ingen deteksjon av tampering
* Bruker master key direkte
* Ingen beskyttelse mot replay, sjekker aldri noen sekvens

Hva er forskjellen på default keys og key mapping keys?
-------------------------------------------------------

Dette er de to typene nøkler som er nevnt i standarden. Begge to har bestemt lengde (Vanligvis 40 eller 104 bit), og er statiske, delte og symmetriske.

Distribuering av nøkler er ikke en del av 802.11-standarden.

### Default

Når alle mobile enheter og AP-en har samme sett med nøkler, kaller vi disse _default keys_. Opp til fire nøkler per enhet. Kun én trengs for at sikkerhet skal fungerere, mens flere støttes slik at man kan endre nøkler jevnt. Dette fungerer på følgende måte: Når det er flere nøkler spesifisert, benyttes aktiv nøkkel til å kryptere sendingen, mens mottakeren kan dekryptere ved å benytte hvilken som helst av nøklene. Egentlig trenger man bare to nøkler for dette, men med fire nøkler kan man ha _directional key use_ — altså to tilgjengelig i hver retning.

### Key mapping

Når alle mobile enheter har sin unike nøkkel mot AP-en, kaller vi disse _key mapping keys_. Mer komplekst å konfigurere og vedlikeholde. Spesielt gjelder dette AP, som nå må inneholde alle nøkler som er i bruk.

Komplikasjon i forhold til broadcast. All multicast-trafikk sendes ved å benytte _default key_. Trenger dermed å legge inn to nøkler i hver enhet.

Hva skjer med en melding før den sendes i WEP-protokollen?
----------------------------------------------------------

Først ankommer en pakke IEEE 802.11 MAC tjenestelaget. Denne kalles MSDU (MAC service data unit). Denne kan bli delt i mindre deler før sending (fragmentering). Hvert fragment blir så en pakke som kalles MPDU (MAC protocol data unit). WEP-kryptering gjøres på MPDU.

* _Integrity check value_ (ICV) gjør at pakken ikke skal kunne endres under transport.
* Nøkkelnummer og IV sendes ukryptert, slik at mottakeren vet hvordan meldingen skal dekrypteres.

![WEP message, nesten klar](http://github.com/kjbekkelund/ttm4137/raw/master/media/wep-message.png)

I tillegg til dette

* _Cyclic Redundancy Check_ (CRC) legges til for å oppdage transmisjonsfeil.
* MAC header legges til for destinasjon.

Hvordan fungerer RC4?
---------------------

Samme algoritme til kryptering og dekryptering. Enkel å implementere samtidig som den er sterk. Feilene i WEP stammer fra måten WEP benytter RC4, ikke at algoritmen er svak.

Grunnleggende idé: Generer pseudorandom sekvens (key stream) som XOR-es med datastrømmen. Utfordringen er å lage en god strøm med pseudorandom tall.

Hvorfor IV i WEP?
-----------------

Nøkkelen som benyttes sier hvilken tilstand RC4 skal starte i. Uten et salt (her IV) vil den være statisk, og dermed alltid starte i samme tilstand. Altså veldig enkelt å finne samme tekst i forskjellige pakker. Ved å benytte IV, startes RC4 i forskjellig tilstand.

Hvorfor er ikke WEP sikker?
---------------------------

Mekanismer som trengs for et sikkert system, og hvorfor WEP feiler:

### Authentication

* Robust metode for å bevise identitet, og som ikke kan forfalskes. Problem: Under autentisering sendes en random 128 bytes streng fra AP til mobil enhet. Den mobile enheten krypterer denne, og sender den tilbake til AP. Problemet er da at plaintext og ciphertext er sendt, slik at en som lytter på samtalen enkelt kan finne de random bytes-ene som benyttes til å kryptere. Vet da _key stream_ som korresponderer til gitt IV.
* Metode for å bevare identiteten over etterfølgende transaksjoner, og som ikke kan overføres. Problem: Ikke implementert i det hele tatt.
* Gjensidig autentisering. Problem: Mobil enhet autentiseres hos AP, men ikke motsatt.
* Autentiseringsnøkkel er uavhengig av krypteringsnøkkel. Problem: WEP bruker samme for begge.

### Access Control

IEEE 802.11 spesifiserer ikke hvordan aksesskontroll implementeres. Men identifikasjon gjøres på MAC-adresser, noe som kan gi en enkel form for aksesskontroll (MEN MAC-adresser kan enkelt forfalskes).

### Replay prevention

WEP har ingen beskyttelse, eksisterer rett og slett ikke standarden.

### Message modification detection

WEP inkluderer ICV, som skal øke beskyttelsen av ciphertext-en. MEN: CRC-metoden som benyttes for å beregne ICV er lineær, altså er det mulig å anslå hvikle bit i ICV som endres når man endrer et enkelt bit i meldingen. WEP sikrer selve verdien, men det er mulig å forutsigbart endre enkeltbits.

### Message privacy


Altså angrep på selve krypteringsmåten i WEP. To mål: få tak i nøkkelen eller dekode meldingen. 

Tre svakheter i måten WEP benytter RC4:

* IV Reuse. Ved å ha lik IV i flere pakker har samme problem som dersom man har null salt i det hele tatt. RC4 vil dermed starte i samme tilstand.
  
  ![IV Reuse Problem](http://github.com/kjbekkelund/ttm4137/raw/master/media/iv-reuse-problem.png)

  Som vi ser kan angriperen benytte det faktum at IV er lik i begge tilfeller til å finne plaintext som er XOR-et med plaintext. Det gir ikke mye i seg selv, men kan benyttes sammen med det faktum at IP-adressen i mange nettverk er rimelig spesifisert, osv. Jo mer data man vet om pakkene, jo mer plaintext kan man finne.
* RC4 Weak Keys. For noen nøkler vil for mange bits i de første bytes-ene i nøkkelstrømmen (pseudorandom bytes) være bestemt av noen få bit i nøkkelen selv. Altså: noen bit i nøkkelen har større effekt enn andre, mens andre har null effekt. Dette er kun i begynnelsen, fram til RC4 "kommer igang". Kan løse dette ved å forkaste de første 256 bytes.
* Direct Key Attacks.

Hva er forholdet mellom Wi-Fi og IEEE 802.11?
---------------------------------------------

Som følge av at IEEE 802.11 er en lang og kompleks standard, har Wi-Fi Alliance laget en test basert på IEEE 802.11 som produsent må gjennom for å få Wi-Fi sertifisering. Slik kan man garantere at alle enheter som har blitt sertifisert faktisk fungerer sammen.

Hva er RSN?
-----------

IEEE 802.11i definerer en ny type trådløst nettverk, kalt Robust Security Network (RSN). IEEE 802.11i definerer også en Transitional Security Network (TSN) der RSN- og WEP-systemer kan operere i parallell.

Security Goals: 

* Confidentiality
* Message Integrity and Authenticity
* Replay detection

Består av:

* AES-CCMP. 128-bit block cipher with 128-bit secret key. Counter Mode initial nonce value (Source address (48), Packet number (48), Ctr(16), other(16)). CBC-MAC initial nonce value (Similar to counter mode), output is the 64 lower bits.
* RSN nøkkelhierarki
* 802.1x Access Control System

Hva er TSN?
-----------

Transitional Security Network = WPA på samme måte som RSN = WPA2

Hva er WPA?
-----------

Siden det ville ta lang tid å innføre en ny standard etter man innså at WEP ikke var sikker, lagde IEEE 802.11i en erstatning som fungerte med funksjonaliteten i eksiterende Wi-Fi-produkter — Wi-Fi Protected Access (WPA). Spesifiserte her Temporal Key Integrity Protocol (TKIP), som er tillatt som en valgfri modus i RSN.

Sammenligning av WEP, TKIP og RSN
---------------------------------

![Sammenligning av WEP, TKIP og RSN](http://github.com/kjbekkelund/ttm4137/raw/master/media/comparison-wep-tkip-rsn.png)

Hva er forskjellen på TSN og RSN?
---------------------------------

Deler arkitektur og tilnærming. TSN har et subset av funksjonaliteten, fokusert på en måte å implementere et nettverk. RSN har støtte for AES i tillegg til TKIP. 

Både RSN og TSN fungerer i infrastruktur modus, mens kun RSN fungerer i ad-hoc modus (dvs ingen aksesspunkter). Ad-hoc modus kalles noen ganger IBSS (Independent Basic Service Set) modus. 

Hva er en sikkerhetskontekst (security context)?
------------------------------------------------

I forhold til WEP, er brukerautentisering og beskyttelse av meldinger separert. Dette gjør at systemet enklere kan skaleres opp til mange brukere. Disse delene kobles sammen i en sikkerhetskontekst (og dermed også på WPA). (Pass-analogi på side 108). Basis: autentiseringsprosesess etterfulgt av en tidsbegrenset sikkerhetskontekst. 

To typer nøkler:

* Master key. Benyttes til autentisering. 
* Temporal keys. Lages etter autentisering. Er en del av sikkerhetskonteksten. Benyttes til alle operasjoner etter autentisering, istedet for å benytte master key.

Hvilke lag finnes i IEEE802.11i-standarden?
-------------------------------------------

Benytter samme lag som enhver LAN-relatert løsning. Dette gjør at RSN passer inn i eksiterende sikkerhetsarkitekturer.

* Wireless LAN Layer. Behandler rå kommunikasjon, annonserer funksjonalitet og aksepterer applikasjoner som ønsker å koble til nettverket. Ansvarlig for kryptering og dekryptering.
* Access Control Layer. "Middle manager". Administrerer sikkerhetskontekst. Holder oversikt over hvem som er autentisert, slik at kun de rette brukerne har tilgang. Snakker med autentiseringslaget for å finne ut når den skal åpne en sikkerhetskontekst. Medvirker i å lage temporære nøkler.
* Authentication Layer. Beslutninger gjøres her. Identitering godkjennes eller avslås. Delegerer ansvar til Access Control Layer når den har autentisert bruker.

Wireless LAN er alltid i AP. Access Control er vanligvis i AP. Authentication er gjerne i AP i små systemer, men i en egen autentiseringsserver i større systemer. 

IEEE802.11 dekker kun Wireless LAN. Access Control benytter 802.11X. IEEE 802.11i spesifiserer ingen obligarisk autentiseringsmetode, men RSN ble designet slik at man selv kan bestemme ønsket metode. 

![Main Standards in an RSN Solution Based on TLS](http://github.com/kjbekkelund/ttm4137/raw/master/media/main-standards-rsn.png)

Hvorfor er aksesskontroll viktig?
---------------------------------

Det er aksesskontrolen som styrer hvem som har tilgang hvor — autorisering.

* Entitet som ønsker tilgang — Supplicant
* Entitet som kontrollerer adgangsgate — Authenticator
* Entitet som avgjør om supplicant får adgang — Authorizer

Aksesskontroll følger alltid et lignende mønster:

* Authenticator får beskjed om at Supplicant er tilstede.
* Supplicant identifiserer seg selv.
* Authenticator spør etter autorisering fra Authorizer.
* Authorizer svarer ja eller nei.
* Authenticator tillatter eller blokkerer tilgang.

I boka fokuserer de på tre protokoller for implementering av adgangskontoll: 

* IEEE 802.1X — Obligatorisk i WPA og RSN.
* EAP (Extensible Authentication Protocol) — Obligatorisk i WPA og RSN.
* RADIUS (Remote Authentication Dial-In User Service) — Obligatorisk i WPA, valgfritt i RSN.

Hva er IEEE 802.1X?
-------------------

Grunnlaget for WPA og RSN.

Har som hensikt å implementere aksesskontroll på det punktet der Supplicant kobler til nettverket. Dette punktet kalles en _port_ (og i forbindelse med trådløse nett er dette en logisk port, ikke en fysisk), og det er et 1-til-1 forhold mellom Supplicant og port. Hver port er assosisert med en Authenticator som kontrollerer tilstanden. Mange porter er koblet til en autentiseringsserver (Authorizer).

Dersom autentiseringsserveren er innebygd i AP, trengs ikke RADIUS.

I trådløse nett er det ikke nok med å bli autentisering en gang, men man må autensisere for hver pakke, ellers kan andre stjele identiteten til en bruker og bruke deres åpne port. Dermed må autoriseringen bindes til pakken, slik at dette ikke er mulig. Dette gjøres ved å inkludere meldingsautentisering (integrity).

For de fleste Wi-Fi-LAN er den logiske plassen å plassere IEEE 802.1X i AP. I ad-hoc-modus er hver enhet både Supplicant og Authenticator.

![802.1x lag](http://github.com/kjbekkelund/ttm4137/raw/master/media/802-1x-lag.png)

Hva er EAP?
-----------

Universelt autentiseringsrammeverk (altså ikke autentiseringsmetode). Hensikten er å gjøre det mulig å benytte forskjellige typer autentiseringsmetoder mellom Supplicant og Authorizer. Autentiseringen kan være ensidig eller gjensidig, avhengig av metoden. EAP benyttes i alle (upper-layer) autentiseringsmetoder, som SSL, TLS og Kerberos.

Lar de to partene utveksle informasjon spesifikk for valgt autentiseringsmetode, og innholdet i disse er ikke spesifisert i EAP. EAP starter og avslutter autentisering, men tillatter hva-som-helst i midten.

Fire meldingstyper:

* Request. Authenticator -> Supplicant.
* Response. Supplicant -> Authenticator.
* Success.
* Failure.

I 802.1X videresendes disse meldingene til og fra autentiseringsserveren, slik at Authenticator blir mellommann (Analogi: dørvakt).

Alle EAP-meldinger følger samme format.

![EAP meldingsformat](http://github.com/kjbekkelund/ttm4137/raw/master/media/eap-message-format.png)

* Code. 1 byte. Request = 1, Response = 2, Success = 3, Failure = 4.
* Identifier. 2 bytes. Inkrementeres for hver melding sent. ResponseID settes til RequestID.
* Length. 2 bytes. Totalt antall byte i EAP-melding.
* Data.

Detaljene i autentiseringsmetoden sendes i request- og response-meldinger.

Request- og response-meldinger deles videre opp etter EAP Type-feltet. Første 6 typer spesifisert (resten utstedes av IANA). Identity (type 1) er viktigst. Benyttes i EAP-introduksjon. EAP-Request/Identity sendes av Autheticator til Supplicant, som svarer med EAP-Response/Identity. NAK (type 3) bruker når det requestes en autentiseringsmetode som ikke støttes. Serieautentisering er mulig. Kan gjøre så mange autentiseringer i sekvens som man ønsker.

![EAP-Request/Response-melding](http://github.com/kjbekkelund/ttm4137/raw/master/media/eap-request-response.png)

Hva er EAPOL?
-------------

For å sende EAP-meldinger rundt i nettverket må de enkapsuleres. EAP RFC spesifiserer ikke hvordan meldinger skal sendes rundt, for eksempel transport over Internett med bruk av IP. IEEE 802.1X spesifiserer protokollen EAP over LAN (EAPOL) for å sende meldinger mellom Supplicant og Authenticator. 

Fem typer meldinger:

* EAPOL-Start. Sendes til en spesiell reservert gruppe-multikast MAC-adresse, for å finne ut om en Authenticator er tilstede. Som svar sendes en EAPOL-Packet som inneholder en EAP-Request/Identity-melding.
* EAPOL-Key. Brukes for å sende krypteringsnøkler fra Authenticator til Supplicant.
* EAPOL-Packet. Brukes for å sende EAP-meldinger. Enkel beholder.
* EAPOL-Logoff. Indikerer at Supplicant ønsker å koble fra nettverket.
* EAPOL-Encapsulated-ASF-Alert. _Benyttes ikke i WPA eller RSN_.

![EAP meldingsflyt](http://github.com/kjbekkelund/ttm4137/raw/master/media/EAP-message-flow.png)

Hva er RADIUS?
--------------

Nettverksprotokoll som gir sentraliserert autentisering, autorisering og regnskapsføring. Definerer to ting: funksjonaliteten inkludert i autentiseringsserver, og protokollen for å snakke med serveren. Laget for TCP/IP.

Fire meldingstyper:

* Access-Request. AP -> AS.
* Access-Challange. AP <- AS.
* Access-Accept. AP <- AS.
* Access-Reject. AP <- AS.

Alle RADIUS-meldinger har samme basisformat.

![RADIUS-meldingsformat](http://github.com/kjbekkelund/ttm4137/raw/master/media/radius-message-format.png)

* Code. Access-Request = 1, Access-Accept = 2, Access-Reject = 3, Access-Challange = 11.
* Identifier. Vilkårlig tall brukt for å matche forespørsel og svar.
* Length. Totalt antall byte i meldingen.
* Authenticator. 16 bytes, avhenger av meldingstype. I Access-Request er det en nonce, og er salt i passordet som sendes passord. I Reply-meldinger brukes det til integritetssjekk.
* I hoveddelen av RADIUS-meldingen sendes en serie attributter, som er (self-contained) pakker med informasjon. Nytteinformasjon. WPA har spesifiserer attributtene de bruker. Hvert attributt har samme format.
  * Type. 1 byte. Identifisere typen.
  * Length. 1 byte. Totalt antall byte.
  * Data.

Trengs kun dersom autentiseringsserveren ikke holder til på samme plass som Authenticator. I WPA er RADIUS obligatorisk. 

Hvordan fungerer EAP over RADIUS?
---------------------------------

Utvidet til å støtte EAP-meldinger videresendt av AP. AP blir det mellomperson i autentiseringsprosessen. Autentiseringsbeslutning blir sendt til både AP og Supplicant.

EAP-meldinger sendes til autentiseringsserveren i Access-Request-meldinger, og returneres i Access-Challange-meldinger. Selge EAP-meldinger sendes i (en eller flere) attributter med type = 79. 

![EAP over RADIUS](http://github.com/kjbekkelund/ttm4137/raw/master/media/eap-radius.png)

Hvordan henger RSN, WPA, IEEE 802.1X, EAP og RADIUS sammen?
-----------------------------------------------------------

Hvilke metoder kan brukes for autentisering i RSN?
--------------------------------------------------

RSN er fleksibelt, slik at mange forskjellige typer autentisering er mulig. TLS, Kerberos, PEAP og autentiseringen i GSM-SIM er noen eksempler.

Wi-Fi Alliance er fri til å velge hvilke typer autentisering de støtter, mens IEEE 802 har vanskeligere for å gjøre det, siden det er en LAN protokoll-standard. Autentiseringen gjøres i øverste lag.

Det er vanlig å benytte en PKI til å etablere en sikkerhetskontekst, og deretter utveksle nøkler som skal brukes til kryptering. Grunnen til dette er at asymmetrisk kryptering er mer prosessor-krevende enn symmetrisk kryptering.

Beskriv TLS
-----------

TLS er en standardisert versjon av SSL. 

WPA/RSN benytter kun en del av TLS, siden de benytter henholdvis TKIP og CCMP til kryptering. Benytter altså autentisering fra TLS. 

TLS er delt i to lag. _Record protocol_ er ansvarlig for å flytte data mellom de to enhetene ved å benytte de parametrene man ble enige om via _handshake protocol_. Record opererer etter en tilkoblingstilstand. Har fire slike: Current transmit, pending transmit, current receive og pending receive. Kan sees på som konfigurasjonen til laget. Pending er de tilstandene som gjøres klar til bruk. Bytter ved å sende "Change Connection State".

Benytter symmetrisk kryptering etter oppsetting ved bruk av assymetrisk. Handshake protokollen benytter sertifikater til å gjøre offentlig nøkkel-kryptering.

Prosessen med handshake:

* Client Hello. Inneholder en liste over støttede _ciphersuites_ og _compression methods_. Ciphersuite = Type sertifikat, krypteringsmetode og integritetssjekk-metode. Inneholder også en nonce.
* Server Hello. Inneholder nonce, forskjellig fra mottatt nonce, og sesjonsID. På dette tidspunktet har klient og server synkronisert tilstander, blitt enige om sesjonsID og _ciphersuite_, og utvekslet to nonce-er. 
* Server Certificate. Server sender sertifikat til klient, som validerer det ved å benytte CAs offentlige nøkkel.
* Client Certificate. Kun dersom server krever sertifikat av klient. 
* Client Key Exchange. Målet er å lage en gjensidig hemmelig nøkkel (Master secret). Klient genererer 48 bytes random tall, som krypteres og sendes til server, som dekrypterer det. Dette blir da pre-master secret.
* Client Certificate Verification. Klienten verifiserer seg ved å hashe alle meldinger fram til nå (sendt og mottatt), signer det med den sertifikatet, og sender til serveren, som sjekker det. Regner så ut master secret. Benytter pre-master secret og de to nonce-ene til å lage en 48 bytes master secret. 
* Change Connection State. Har nå lagd en pending tilstand som det kan byttes til. Hver side sender en til hverandre.
* Finished. Bekreftelse av at systemet er oppe og går. Begge sender til hverandre. Siden dette er etter tilstandsbytte vil begge meldinger være krypterte.

Hvordan brukes TLS i EAP?
-------------------------

TLS handshake oppnår tre ting: Autentisert serveren (og optionally klienten), generert master key, intialisert ciphersuite. WPA og RSN benytter kun disse delene av TLS, siden de allerede har TKIP eller CCMP for kryptering og integritetssjekking. 

Format på EAP-TLS-melding er litt forskjellig fra standard EAP-melding.

![EAP-TLS-meldingsformat](http://github.com/kjbekkelund/ttm4137/raw/master/media/eap-tls-message.png)

Først _length_ spesifiserer antall byte i meldingen. Den andre er lengden på TLS-meldingen, siden den kan strekke over flere EAP-meldinger. Flags: Length included, More fragments, Start (av handshake).

![EAP-TLS handshake](http://github.com/kjbekkelund/ttm4137/raw/master/media/eap-tls-handshake.png)

TLS settes opp mellom autentiseringsserver og Supplicant, og RADIUS brukes for å sende EAP-meldinger til autentiseringsserveren og får slik en kopi av master key.

Hvordan fungerer PEAP?
----------------------

Den orginale motivasjonen var å gjøre passord-basert trygg fra "offline dictionary"-angrep. For å oppnå dette er EAP-sesjonen fullstendig gjemt for angriper. Målet med EAP er autentisitet, men det er fullt mulig å gjøre en kobling privat uten autentisering først. Altså kan man sette opp en privat kanal (f.eks. med pseudonym fra klienten, mens serveren autentiseres), og så gjennomføre gjensidig autentisering. Har ingenting å si om "feil" person får opprettet en privat kanal.

Beskriv EAP-SIM
---------------

Benytter den hemmelige informasjon i SIM-kortet. Basic approach: Challange-response der nettverket sender en random verdi som må krypteres og sendes tilbake. Prosessen består av tre tall (kalt triplet): (Random challange (RAND), 64 bit session key (Kc), Respons kalt SRES som beregnes ut fra Kc og RAND). Algoritmen for SRES og Kc er ikke tilgjenglig utenfor SIM-kortet. (Setter en viss begrensning på hvem som kan implementere EAP-SIM.)

Målet er å benytte eksisterende GSM-autentisering så uendret som mulig. Et problem er at SIM-kortet ikke produserer en lang master key, kun 64 bits. Benytter derfor algoritmen flere ganger slik at flere master keys genereres, og kan konkateneres. Introduserer _IMSI privacy_. I en autentisering blir server og mobil enig om ny identitet som skal brukes i neste autentisering (pseudonym). 

Et sentralt problem er at det ikke er skikkelig gjensidig autentisering. Nettverket blir ikke eksplisitt autentisert, kun gjennom at det har lik SRES og Kc som mobilen beregner.

![Meldingsflyt i EAP-SIM](http://github.com/kjbekkelund/ttm4137/raw/master/media/message-flow-eap-sim.png)

Hva innebærer det at en autentiseringsmetode er key-generating?
---------------------------------------------------------------

Hvordan verifiserer Supplicant at AP legitim?
---------------------------------------------

At AP faktisk har nøkkelen Supplicant og Autentiseringsserveren har blitt enig om. Denne blir sendt på sikker måte med RADIUS fra autentiseringsserveren til AP.

Hvordan lages/velges en nonce?
------------------------------

Forensic analysis of mobile phones
----------------------------------

Faser:

* Sikre bevis. Prinsipper: Integritet og kompletthet. Praktiske omstendigheter (incluence) valg av metode — enten harddisk-imageing eller filkopiering. 
* Recovery.
* Analyse. I prinsippet to metoder: Manuell analyse & søk. Hovedproblem: Enorme mengder data. Keywords, bytes (f.eks. starten på JPEG-fil), time stamps, links (metadata).
* Presentasjon.

Hva er Milenage?
----------------

Hash-funksjonen som brukes i UMTS.

Hva er Kasumi?
--------------

Hva er crypto-period?
---------------------

The communication period a cryptokey is used/valid.

Hva er TKIP?
------------

TKIP = Temporal Key Integrity Protocol. Eksistrer for å oppgradere og sikre WEP-systemer. Er en del av RSN i IEEE 802.11i.

Et Wi-Fi LAN-kort består av fire deler:

* RF. Sende og motta.
* Modem. Hente ut data fra mottatt signal.
* MAC (Medium Access Control). Protokoll-greier, inkludert WEP-kryptering.
* Host interface. Koble til ekstern enhet.

Det er MAC-en som implementerer IEEE 802.11-protokollen. MAC ligger på en IC, rundt en mikroprosessor. 

![TKIP](http://github.com/kjbekkelund/ttm4137/raw/master/media/tkip.png)

### Integrity

Trengte en måte å lage en MIC som ikke trengte multiplikasjon eller nye krypto-algoritmer. Løsningen heter Michael, som kun består av shift- og add-operasjoner. Problemet er at Michael er sårbar mot brute force-angrep, men gjør opp for dette med _countermeasures_. Dette går ut på å ha en pålitelig måte å detektere angrep. Michael opererer på MSDU-er. Dette reduserer overhead siden man ikke trenger en MIC per fragment (MPDU). TKIP-kryptering gjøres derimot på MPDU-nivå. 

### Valg og bruk av IV

* I WEP var det ingen krav til hvordan IV velges. I TKIP er det et krav til at man begynner på et tilfeldig tall, og inkrementerer med 1 for hver nye IV. 
* Utvidet fra 24 bit i WEP til 48 bit i TKIP (Egentlig 56 bit, men forkaster 1 byte på grunn av svake nøkler.)
* IV brukes sekundært som sekvensteller for å unngå replay-angrep.
* IV er konstruert for å unngå svake nøkler.

### TKIP Sequence Counter (TSC)

Samme som IV. Brukes til å skjerme mot replay-angrep.

Problem: Burst-ack. Sender mange meldinger på en gang (opp til 16). Dersom noen av pakkene tapes på turen, spesifiseres det hvilke som må sendes på nytt. Dersom f.eks. første pakke ble mistet, får man et problem når den gjensendes siden mottaker egentlig kun godtar >= tidligere TSC-verdi. Løser dette med replay-vindu. Dette vinduet godtar alt mellom største mottatte verdi og 16 mindre enn denne verdien.

Beskriv FMS-angrepet
--------------------

Beskriv per-packet key mixing i TKIP
------------------------------------

Økningen i IV-lengde skapte problemer, siden legacy-systemer antok den gamle måten, altså en 64 bits RC4, ikke 88 bits. De løste dette ved å dele den 48 bit lange IV-en i to deler. De første 16 bit paddes til 24 bit, og inkluderes som før. Mixer så sammen dette med de resterende 32 bitene. 

![Per-packet key mixing](http://github.com/kjbekkelund/ttm4137/raw/master/media/per-packet-key-mixing.png)

Hvorfor er IV viktig?
---------------------



Hvorfor gir ikke ICV i WEP noen beskyttelse?
--------------------------------------------

(s 235)

Hvorfor ble TKIP byttet ut med CCMP?
------------------------------------

Den eneste grunnen til at TKIP ble benyttet i WPA var at protokollen måtte bygge på samme hardware som WEP. AES-basert sikkerhet kan generelt sees som sikrere enn TKIP-basert sikkerhet, men det betyr _ikke_ at TKIP er usikker. CCMP var spesialbygd for RSN fra grunnen av.

AES er ikke en sikkerhetsprotokoll, men et blokkcipher. I RSN er sikkerhetsprotokollen CCMP, som definerer et seg med regler for kryptering og sikring av IEEE 802.11 frames.

På hvilken måte er TKIP forskjellig fra WEP?
--------------------------------------------

* Message integrity. Prevent tampering.
* Nye regler for hvordan IV velges og gjenbrukes.
* Endrer krypteringsnøkkel for hver frame.
* Større IV.
* Nøkkelstyring.

![TKIP og WEP](http://github.com/kjbekkelund/ttm4137/raw/master/media/tkip-and-wep.png)

Beskriv AES
-----------

Block cipher. 128 bit nøkkel- og blokklengde.

Beskriv operasjonsmodusen CCM
-----------------------------

En operasjonsmodus trengs når meldinger ikke er av spesifikk lengde. Må da definere en måte å konvertere dataen til en sekvens med blokker med gitt lengde før kryptering. CCMP benytter CCM (Counter Mode + CBC-MAC), som er basert på counter mode. 

![Counter Mode](http://github.com/kjbekkelund/ttm4137/raw/master/media/countermode.png)

Siden counter mode endrer verdi for hver blokk, vil cipher text endres selv om dataen krypteres med samme nøkkel. Initialisert fra en nonce, ikke fra 1, siden man da ville kunnet funnet tilbake til dataen. Kryptering og dekryptering er likt i counter mode. Grunnen til at man ikke brukte counter mode, er fordi den kun gir kryptering, ikke MIC. 

I CCM benyttes CBC til å produsere en MIC, som kalles Message Authentication Code (MAC). Derav CBC-MAC.

![CBC-MAC](http://github.com/kjbekkelund/ttm4137/raw/master/media/cbc-mac.png)

Nyttige features i CCM:

* Spesifisering av nonce
* Kobling mellom kryptering og autentisering med én nøkkel
* Utvidet autentisering til data som ikke er kryptert (f.eks. headeren)

Hva er en MIC?
--------------

En Message Integrity Code (MIC) trengs for å garantere en meldings autensitet. 

Hvordan er MIC-en i CCMP regnet ut?
-----------------------------------

Utregning av MIC starter med CBC-MAC, og XOR-er etterpå alle de neste blokkene, og krypterer resultatet. Ender på 128 bit, der de nedre 64 bit discardes.

Første blokk bruker ikke data fra MPDU, men med en blokk bestående av nonce, flag og DLen. 

* Nonce blir laget ved å kombinere PN og MAC-adressen til senderen, samt en prioritetsverdi, til evt framtidig bruk. 
* Flag har alltid samme verdi i RSN: 01011001
* DLen indikerer lengden på plaintext

![CBC-MAC første blokk](http://github.com/kjbekkelund/ttm4137/raw/master/media/cbc-mac-first-block.png)

Hvorfor kan ikke packet number (PN) brukes som nonce i CCMP?
------------------------------------------------------------

Nederst på s 274

Hvordan brukes CCMP i RSN?
--------------------------

Rammeflyt gjennom CCMP:

![Rammeflyt gjennom CCMP](http://github.com/kjbekkelund/ttm4137/raw/master/media/ccmp-mpdu.png)

Steg i MPDU-prosessering:

![Steg i MPDU-prosessering](http://github.com/kjbekkelund/ttm4137/raw/master/media/steps-mpdu.png)

CCMP headeren sendes ukryptert. Totalt 8 bytes fordelt på:

* 48 bits = 6 byte pakkenummer (PN) som gir replay-beskyttelse, og som gjør det mulig for mottaker å dedusere seg fram til nonce brukt i krypteringen. 
* 1 byte nøkkel-id. Informasjon om hvilken gruppenøkkel som skal brukes ved multikast.
* 1 byte reservert.

![CCMP header](http://github.com/kjbekkelund/ttm4137/raw/master/media/ccmp-header.png)

Kryptering og dekryptering:

![Kryptering og dekryptering](http://github.com/kjbekkelund/ttm4137/raw/master/media/encrypt-decrypt-ccmp.png)

CCMP-kryptering:

![CCMP-kryptering](http://github.com/kjbekkelund/ttm4137/raw/master/media/ccmp-encryption.png)

Steg i CCMP-kryptering:

![Steg i CCMP-kryptering](http://github.com/kjbekkelund/ttm4137/raw/master/media/ccmp-encryption-stages.png)

Et essensielt steg i krypteringen er å initialisere counteren, slik at den ikke får en verdi som har blitt brukt før. Lages på nesten samme måte som nonce til MIC-en, men bruker en Ctr (Counter) istedenfor DLen. Ctr begynner å telle på 1, og er 16 bit. Dermed unik for enhver melding med færre enn 65536 blokker.

Dekryptering:

* Velger nøkkel for dekryptering basert på MAC-adresse
* Sjekker om PN > tidligere mottatt PN
* Dekrypterer ved bruk av AES i counter-mode.
* Sjekker MIC mot mottatte felt

![CCMP Crypto](http://github.com/kjbekkelund/ttm4137/raw/master/media/ccmp-crypto.png)

Beskriv Man-in-the-Middle-angrepet på UMTS
------------------------------------------

![UMTS](http://github.com/kjbekkelund/ttm4137/raw/master/media/umts.png)

### Hvorfor mulig?

For å gjøre et MitM-angrep må angriperen imitere et gyldig nettverk. Ved kun UMTS-utstyr er ikke dette mulig på grunn av to sikkerhetsmekanismer:

1. Autentiseringstegn (AUTN). Sikrer at tegnet er nytt og at det kommer fra riktig plass. Gjøres ved sekvensnummer (SQN) og meldingsautentiseringskode (MAC). Beskytter dermed mot replay av autentiseringsdata. 
2. Integritetsbeskyttelse i steg 12. Forhindrer angriper å videresende korrekt autentiseringsinformasjon samtidig som de respektive parter blir lurt til å ikke bruke kryptering.

![Authentication and key agreement in standard UMTS](http://github.com/kjbekkelund/ttm4137/raw/master/media/auth-and-key-agreement-umts.png)

![Authentication and key agreement in UMTS with GSM components](http://github.com/kjbekkelund/ttm4137/raw/master/media/auth-and-key-agreement-umts-with-gsm.png)

### Gjennomføring

Tre deler:

1. Få tak i MS sin IMSI. Kan gjøres ved å initiere autentiseringsprosedyre før angrepet starter. Dette gjøres ved å imitere en GSM basestasjon. Kobler av etter mottatt IMSI.
2. Angriper handler på vegne av MS for å få en gylding autentiseringstoken (AUTN) fra et ekte nettverk. Ingen av disse meldingene er sikret. ![Obtain valid AUTN](http://github.com/kjbekkelund/ttm4137/raw/master/media/obtain-valid-autn.png)
3. Imiterer en GSM basestasjon som MS kobler på. Velger i steg 6 å bruke "No encryption". ![Impersonate valid GSM base station](http://github.com/kjbekkelund/ttm4137/raw/master/media/impersonate-valid-gsm.png)

Dette angrepet gjør det ikke mulig for angriperen å skape en kobling mellom MS og en ekte basestasjon, og for å få en "vanlig" kobling må angriperen lage en kobling til et ekte nettverk for å videresende trafikk.

Cursory
=======

Bakgrunnsinformasjon.

Hva er de seks prinsippene for sikkerhetstenkning?
--------------------------------------------------

1. Ikke snakk til noen du ikke kjenner. For a Wi-Fi LAN, it is not enough to verify the identity of the other party. A Wi-Fi LAN must also verify that every message really came from that party.
2. Godta ingenting uten garanti. Means a guarantee of authenticity. Ingen endring. 
3. Behandle alle som en fiende til det motsatte er bevist.
4. Ikke stol på vennene dine for lenge. "Friends" in a Wi-Fi LAN can be identified because they possess tokens such as a secret key that can be verified. Such tokens, whether they are keys, certificates, or passwords, need to have a limited life. Renew tokens periodically.
5. Bruk gjennomprøvde løsninger.
6. Se etter sprekker i bakken du står på. The challenge for hackers, of course, is to look for the little cracks and crevices that result from hidden assumptions.

Begreper
--------

* Threat model. The basis for designing and evaluating security. Identify all the threats against which we plan to defend.
* Security protocol. Real security is provided by a set of processes and procedures that are carefully linked together.
* Key Entropy. The number of possible key values determines the strength of the key.

Tradisjonell sikkerhetsarkitektur
---------------------------------

The traditional approach for network security is to divide the network into two zones: trusted and untrusted. There is no need for network security protection within the trusted zone because there are no enemies present. By contrast, you regard the untrusted zone as full of enemies.

![Conventional Security Architecture](http://github.com/kjbekkelund/ttm4137/raw/master/media/conventional-security-architecture.png)

How does Wi-Fi LAN fit into this conventional security architecture?

1. Put Wireless LAN in the Untrusted Zone. With the possible exception of national security headquarters, offices do not have perfect screening. You may decide, therefore, that Wi-Fi LANs are always operating in an untrusted zone. You can get around this by using VPN, but that might slow down communication, demand a high capacity VPN server, and limit the types of operation that can be performed.
2. Make Wi-Fi LAN Trusted. Make the Wi-Fi LAN itself fundamentally impenetrable by enemies. You can make Wi-Fi LANs as trusted as wired LANs by making it very difficult to decode the wireless signals.

Hvorfor er Wi-Fi sårbar mot angrep?
-----------------------------------

Bruker radiobølger. Enhver kan lytte. Dette krever en ny måte å tenke, siden det er enormt forskjellig fra før, der mediet i seg selv var lukket i mye større grad. 

Generelt for trådløs kommunikasjon: Ikke-detekterbar passiv og lavrisiko aktive angrep. Muliggjør man-in-the-middle-angrep. Også mulig med angrep som jamming og elektromagnetisk "bombing". Mange klienter benytter en basestasjon/aksesspunkt. Mobilitet, roaming.

Hva er hovedkategoriene for angrep?
-----------------------------------

1. Snooping. Aksessere privat informasjon. Kryptering kan brukes for å gjøre det vanskelig å få tak i informasjonen.
2. Modifications. Modifisering av data, f.eks. destinasjonsfeltet (IP-address) i en melding eller innholdet i epost-er.
3. Masquerading. Maskeres seg som en gyldig enhet. Ideell fremgangsmåte dersom angriperen ønsker å forbli umerket. Kan bruke dette til å for eksempel få tak i innloggingskredentialer. 
4. Denial of Service. Forskjellig fra 1-3, hovedsakelig med tanke på at den prøver å blokkere alle, også seg selv, istedenfor å gi seg selv mer adgang, slik målet er i de andre typene angrep.

Hvilke angrep er mulig uten nøkkel?
-----------------------------------

### Snooping

Ved å snuse rundt kan man kun finne ut informasjon om hvordan, når, og fra hvilke enheter nettverket brukes. Begrenset bruk, men kan være verdifullt sammen med annen informasjon en angriper kan få tak i.

En angriper kan blant annet finne ut:

* Leverandør av aksesspunkt ved å benytte MAC-addressen. Modell kan muligens finnes ved å se på funksjonalitet eller proprietær informasjon i beacon.
* Trafikkanalyse kan gi informasjon om hvordan nettverket brukes, f.eks. antall brukere, når nettverket bruker, osv.
* Dersom WEP benyttes, kan man se om flere brukere benytter samme nøkkel.

### Man-in-the-Middle

Kan benyttes til å modifisere meldinger i transitt uten at det detekteres. Det er to måter å modifisere: Enten kan det gjøres on-the-fly eller man kan fange, modifisere, og gjenspille meldingen (store-and-forward).

For å gjøre et store-and-forward-angrep i Wi-Fi må angriperen stoppe den intielle meldingen, slik at modifikasjoner kan gjøres på den før den videresendes til brukeren.

(Figur av liste)

En annen måte å gjøre dette, er ved å sette opp et falsk aksesspunkt. Det falske aksesspunktet identifiseret et ekte aksesspunkt. Når en enhet prøver å koble til det falske aksesspunktet, modifiseres meldingen blant annet med å substituere inn sin egen MAC, og videresendes så til det ekte aksesspunktet. Gjør det samme med data andre vei. Har ikke nøkkel, så har bare kryptert data.

Hvilke angrep er mulig på nøklene?
----------------------------------

Mennesket er ofte det svakeste ledd i sikkerhet. For å unngå dette problemet, kan man bruke engangspassord. Eks: Bank-ID. Nøkkelen er kun aktiv i noen minutter, noe som gjør at det har begrenset verdi.

Man kan "begrave" nøkkelen, slik man for eksempel gjør i SIM-kort. Nøkkelen gjøres utilgjengelig for utenomverden. Men det er fortsatt essensielt å endre passord fra tid til annen.

Trådløshet introduserer helt nye problemer. Lett å få tilgang til datastrømmene, selv om de er krypterte. 

Hva er infrastruktur-modus og ad-hoc-modus?
-------------------------------------------

Når IEEE 802.11-systemer arbeider gjennom et aksesspunkt, opererer de i infrastruktur-modus (ESS). Koordineringen av Wi-Fi LAN-et gjøres av aksesspunktet fra et bestemt punkt, og har ofte koblingen til Ethernett-nettverket.

I ad-hoc-modus (IBSS) er det derimot ikke noe aksesspunkt, og hver trådløs-enhet kan sende direkte til hvilken som helst annen enhet.

Forklar basisoperasjon i infrastruktur-modus
--------------------------------------------

* AP = Fixed Access Point
* STA = (Station) Wireless Device

AP og STA snakker med hverandre ved å bruke trådløse meldinger. AP er koblet til (wired) nettverket STA ønsker å aksessere.

AP annonserer periodiske sin tilstedeværelse i korte meldinger kalt _beacons_. Gjør at STA kan avdekke identiteten til AP. En STA søker etter AP. Flere forskjelllige frekvenser (kanaler) kan bruker, så STA-en må lytte på hver av disse. Dette kalles skanning. 

Når en STA er klar for å koble til en AP, sender den en _authenticate request message_ til AP-en. AP responderer på denne ved å sende en aksept. Nå har STA tillatelse til å koble til AP, men en assosiasjon settes opp før den kan sende og motta data fra nettverket. STA sender en _association request_ og AP svarer med en _association response_.

Roaming. STA sender _disassociation message_ til gammel AP, og _reassociation message_ til ny. Sistnevnte inneholder informasjon som muliggjør smoothere handover.

Beskriv kort arkitekturen i IEEE 802.11
---------------------------------------

To laveste nivå av referansemodell. Tilsvarende Physical og Data Link i OSI.

Hvilke type meldinger finnes i IEEE 802.11?
-------------------------------------------

1. Control. Korte meldinger for start og stopp sending av informasjon, samt om det har vært en kommunikasjonsfeil.
2. Management. Brukes av AP og STA for å forhandle og kontrollere forhold. 
   * Beacons. AP annonserer periodiske sin tilstedeværelse. Inneholder informasjon som nettverksnavn og funksjoner i AP.
   * Probing. En STA kan sende en _probe request message_. Dersom AP mottar, sendes umiddelbart en respons. Slik kan STA finne AP hurtig uten å søke gjennom alle kanaler og vente på beacon.
3. Data. Sendes vanligvis mellom STA og AP, også når mottaker er en annen STA. AP videresender. IEEE 802.11 datarammer som går til eller fra AP har tre adresser; kilde, mål og mellomliggende (dvs AP den går gjennom).

Forklar MAC (header) i IEEE 802.11
----------------------------------

MAC-headeren er en del av det generelle formatet på rammer i IEEE 802.11.

![Generelt format på rammer i IEEE 802.11](http://github.com/kjbekkelund/ttm4137/raw/master/media/basic-frame-format-802-11.png)

Tre typer basert på om det er snakk om en kontrollramme, styringsramme eller dataramme. Viktigste del: adresser. 6 byte per adresse. Hver enhet har en unik adresse spesifisert under produksjon. Destinasjon kan være unikast eller multikast.

To til fire adresser per header:

* Transmitter address (TA)
* Receiver address (RA)
* Source address (SA)
* Destination address (DA)

(AP til AP-kommunikasjon ikke fullt spesifisert i standarden, og det er få implementasjoner.)

Enkelt for andre å late som de er andre ved å benytte deres MAC-adresse.

Styringsrammer, men ikke kontroll- og datarammer, er viktige med tanke på sikkerhet. Består av to deler; et sett med bestemte felt, og elementer. Et element er en (self-contained) pakke med informasjon. Gjør at standarden enklere kan oppdateres. Første byte identifiserer type, det andre lengde. 
