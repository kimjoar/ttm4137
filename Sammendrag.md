TTM4137
=======

Hva er de seks prinsippene for sikkerhetstenkning?
--------------------------------------------------

1. Don't talk to anyone you don't know. For a Wi-Fi LAN, it is not enough to verify the identity of the other party. A Wi-Fi LAN must also verify that every message really came from that party. 
2. Accept nothing without a guarantee. Means a guarantee of authenticity. Ingen endring. 
3. Treat everyone as an enemy until proved otherwise.
4. Don't trust your friends for long. "Friends" in a Wi-Fi LAN can be identified because they possess tokens such as a secret key that can be verified. Such tokens, whether they are keys, certificates, or passwords, need to have a limited life. Renew tokens periodically.
5. Use well-tried solutions.
6. Watch the ground you are standing on for cracks. The challenge for hackers, of course, is to look for the little cracks and crevices that result from hidden assumptions.

Begreper
--------

* Threat model. The basis for designing and evaluating security. Identify all the threats against which we plan to defend.
* Security protocol. Real security is provided by a set of processes and procedures that are carefully linked together.
* Key Entropy. The number of possible key values determines the strength of the key.

(spørsmål ang tradisjonell sikkerhetsarkitektur)
------------------------------------------------

The traditional approach for network security is to divide the network into two zones: trusted and untrusted. There is no need for network security protection within the trusted zone because there are no enemies present. By contrast, you regard the untrusted zone as full of enemies.

[http://github.com/kjbekkelund/ttm4137/raw/master/conventional-security-architecture.png](Conventional Security Architecture)

How does Wi-Fi LAN fit into this conventional security architecture?

1. Option 1: Put Wireless LAN in the Untrusted Zone. With the possible exception of national security headquarters, offices do not have perfect screening. You may decide, therefore, that Wi-Fi LANs are always operating in an untrusted zone. You can get around this by using VPN, but that might slow down communication, demand a high capacity VPN server, and limit the types of operation that can be performed.
2. Option 2: Make Wi-Fi LAN Trusted. Make the Wi-Fi LAN itself fundamentally impenetrable by enemies. You can make Wi-Fi LANs as trusted as wired LANs by making it very difficult to decode the wireless signals.

Hvorfor er Wi-Fi sårbar mot angrep?
-----------------------------------

Bruker radiobølger. Enhver kan lytte. Dette krever en ny måte å tenke, siden det er enormt forskjellig fra før, der mediet i seg selv var lukket i mye større grad. 

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

AP = Fixed Access Point
STA = (Station) Wireless Device

AP og STA snakker med hverandre ved å bruke trådløse meldinger. AP er koblet til (wired) nettverket STA ønsker å aksessere.

AP annonserer periodiske sin tilstedeværelse i korte meldinger kalt _beacons_. Gjør at STA kan avdekke identiteten til AP. En STA søker etter AP. Flere forskjelllige frekvenser (kanaler) kan bruker, så STA-en må lytte på hver av disse. Dette kalles skanning. 

Når en STA er klar for å koble til en AP, sender den en _authenticate request message_ til AP-en. AP responderer på denne ved å sende en aksept. Nå har STA tillatelse til å koble til AP, men en assosiasjon settes opp før den kan sende og motta data fra nettverket. STA sender en _association request_ og AP svarer med en _association response_.

Roaming. STA sender _disassociation message_ til gammel AP, og _reassociation message_ til ny. Sistnevnte inneholder informasjon som muliggjør smoothere handover.

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

[http://github.com/kjbekkelund/ttm4137/raw/master/basic-frame-format-802-11.png](Generelt format på rammer i IEEE 802.11)

Tre typer basert på om det er snakk om en kontrollramme, styringsramme eller dataramme. Viktigste del: adresser. 6 byte per adresse. Hver enhet har en unik adresse spesifisert under produksjon. Destinasjon kan være unikast eller multikast.

To til fire adresser per header:

* Transmitter address (TA)
* Receiver address (RA)
* Source address (SA)
* Destination address (DA)

(AP til AP-kommunikasjon ikke fullt spesifisert i standarden, og det er få implementasjoner.)

Enkelt for andre å late som de er andre ved å benytte deres MAC-adresse.

Styringsrammer, men ikke kontroll- og datarammer, er viktige med tanke på sikkerhet. Består av to deler; et sett med bestemte felt, og elementer. Et element er en (self-contained) pakke med informasjon. Gjør at standarden enklere kan oppdateres. Første byte identifiserer type, det andre lengde. 

Hva var problemet i WEP?
------------------------

Wired Equivalent Privacy. 

Mål i 1999-standarden inkluderer rimelig sterk, selvsynkronisering, effektiv og eksporterbar. Standarden spesifiserte 40-bits nøkler, men en ikke-standard utvidelse bruker 104 bit. Definerte også to typer sikkerhet: åpent (dvs ingen sikkerhet) og delt nøkkel.

* Autentiseringsfase. Bevise sin identitet til hverandre. Problem: ingen hemmelig token, altså ingen måte å vite at etterfølgende meldinger kommer fra samme enhet. Meningsløst. Med delt nøkkel er målet at mobil enhet skal vise at den har nøkkelen. AP sender plaintext, får tilbake ciphertext. Snilt for hackere.
* Krypteringsfase. Benytter RC4. Enkel å implementere. Rask. Initialisering og kryptering skjer på hver pakke. 24 bit til initialiseringsvektor (IV). IV endres for hver pakke, og sendes sammen med pakken. Gjør at samme plaintext gir forskjellige ciphertext. Nøkkel til RC4 er sammensatt av hemmelig nøkkel og IV. Problemet er at IV aldri bør brukes mer enn én gang per nøkkel. I WEP er det 24 bit, dvs ca 17 mill IV-verdier. Mange systemer starter med samme IV etter oppstart, og benytter et pseudorandom bytte.

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

[http://github.com/kjbekkelund/ttm4137/raw/master/wep-message.png](WEP message, nesten klar)

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
  
  [http://github.com/kjbekkelund/ttm4137/raw/master/iv-reuse-problem.png](IV Reuse Problem)

  Som vi ser kan angriperen benytte det faktum at IV er lik i begge tilfeller til å finne plaintext som er XOR-et med plaintext. Det gir ikke mye i seg selv, men kan benyttes sammen med det faktum at IP-adressen i mange nettverk er rimelig spesifisert, osv. Jo mer data man vet om pakkene, jo mer plaintext kan man finne.
* RC4 Weak Keys. For noen nøkler vil for mange bits i de første bytes-ene i nøkkelstrømmen (pseudorandom bytes) være bestemt av noen få bit i nøkkelen selv. Altså: noen bit i nøkkelen har større effekt enn andre, mens andre har null effekt. Dette er kun i begynnelsen, fram til RC4 "kommer igang". Kan løse dette ved å forkaste de første 256 bytes.
* Direct Key Attacks.

Hva er forholdet mellom Wi-Fi og IEEE 802.11?
---------------------------------------------

Som følge av at IEEE 802.11 er en lang og kompleks standard, har Wi-Fi Alliance laget en test basert på IEEE 802.11 som produsent må gjennom for å få Wi-Fi sertifisering. Slik kan man garantere at alle enheter som har blitt sertifisert faktisk fungerer sammen.

Hva er RSN?
-----------

IEEE 802.11i definerer en ny type trådløst nettverk, kalt Robust Security Network (RSN). IEEE 802.11i definerer også en Transitional Security Network (TSN) der RSN- og WEP-systemer kan operere i parallell.

Hva er WPA?
-----------

Siden det ville ta lang tid å innføre en ny standard etter man innså at WEP ikke var sikker, lagde IEEE 802.11i en erstatning som fungerte med funksjonaliteten i eksiterende Wi-Fi-produkter — Wi-Fi Protected Access (WPA). Spesifiserte her Temporal Key Integrity Protocol (TKIP), som er tillatt som en valgfri modus i RSN.

Hva er forskjellen på WPA og RSN?
---------------------------------

Deler arkitektur og tilnærming. WPA har et subset av funksjonaliteten, fokusert på en måte å implementere et nettverk. RSN har støtte for AES i tillegg til TKIP. 

Både RSN og WPA fungerer i infrastruktur modus, mens kun RSN fungerer i ad-hoc modus (dvs ingen aksesspunkter). Ad-hoc modus kalles noen ganger IBSS (Independent Basic Service Set) modus. 

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

[http://github.com/kjbekkelund/ttm4137/raw/master/main-standards-rsn.png](Main Standards in an RSN Solution Based on TLS)

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

[http://github.com/kjbekkelund/ttm4137/raw/master/eap-message-format.png](EAP meldingsformat)

* Code. 1 byte. Request = 1, Response = 2, Success = 3, Failure = 4.
* Identifier. 2 bytes. Inkrementeres for hver melding sent. ResponseID settes til RequestID.
* Length. 2 bytes. Totalt antall byte i EAP-melding.
* Data.

Detaljene i autentiseringsmetoden sendes i request- og response-meldinger.

Request- og response-meldinger deles videre opp etter EAP Type-feltet. Første 6 typer spesifisert (resten utstedes av IANA). Identity (type 1) er viktigst. Benyttes i EAP-introduksjon. EAP-Request/Identity sendes av Autheticator til Supplicant, som svarer med EAP-Response/Identity. NAK (type 3) bruker når det requestes en autentiseringsmetode som ikke støttes. Serieautentisering er mulig. Kan gjøre så mange autentiseringer i sekvens som man ønsker.

[http://github.com/kjbekkelund/ttm4137/raw/master/eap-request-response.png](EAP-Request/Response-melding)

Hva er EAPOL?
-------------

For å sende EAP-meldinger rundt i nettverket må de enkapsuleres. EAP RFC spesifiserer ikke hvordan meldinger skal sendes rundt, for eksempel transport over Internett med bruk av IP. IEEE 802.1X spesifiserer protokollen EAP over LAN (EAPOL) for å sende meldinger mellom Supplicant og Authenticator. 

Fem typer meldinger:

* EAPOL-Start. Sendes til en spesiell reservert gruppe-multikast MAC-adresse, for å finne ut om en Authenticator er tilstede. Som svar sendes en EAPOL-Packet som inneholder en EAP-Request/Identity-melding.
* EAPOL-Key. Brukes for å sende krypteringsnøkler fra Authenticator til Supplicant.
* EAPOL-Packet. Brukes for å sende EAP-meldinger. Enkel beholder.
* EAPOL-Logoff. Indikerer at Supplicant ønsker å koble fra nettverket.
* EAPOL-Encapsulated-ASF-Alert. _Benyttes ikke i WPA eller RSN_.

[http://github.com/kjbekkelund/ttm4137/raw/master/EAP-message-flow.png](EAP meldingsflyt)

Hva er RADIUS?
--------------

Nettverksprotokoll som gir sentralisering autentisering, autorisering og regnskapsføring. Definerer to ting: funksjonaliteten inkludert i autentiseringsserver, og protokollen for å snakke med serveren. Laget for TCP/IP.

Fire meldingstyper:

* Access-Request. AP -> AS.
* Access-Challange. AP <- AS.
* Access-Accept. AP <- AS.
* Access-Reject. AP <- AS.

Alle RADIUS-meldinger har samme basisformat.

[http://github.com/kjbekkelund/ttm4137/raw/master/radius-message-format.png](RADIUS-meldingsformat)

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

[http://github.com/kjbekkelund/ttm4137/raw/master/eap-radius.png](EAP over RADIUS)

Hvordan henger IEEE 802.1X, EAP og RADIUS sammen?
-------------------------------------------------

