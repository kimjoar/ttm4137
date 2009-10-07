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

[conventional-security-architecture.png](Conventional Security Architecture)

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

[basic-frame-format-802-11.png](Generelt format på rammer i IEEE 802.11)

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
* Krypteringsfase. Benytter RC4. Enkel å implementere. Rask. Initialisering og kryptering skjer på hver pakke. 24 bit til initialisering.