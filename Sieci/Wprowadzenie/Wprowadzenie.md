
# Potrzeba standaryzacji

- DZIŚ: ogromne zapotrzebowanie na globalną komunikację między komputerami, wolno stojący komputer to już rzadkość
- DAWNIEJ: **systemy zamknięte** - programy do obsługi sieci, często asemblerowe, pisane na lokalne potrzeby trudne do testowania i nieprzenośne
- Rozwiązanie: opracowanie i przestrzeganie zespołu norm pozwalających na wzajemne porozumiewanie się komputerów
- Koniec lat siedemdziesiątych: powstanie standardu ISO: **ISO** **Reference Model for Open Systems Interconnections** - ramy dla koordynacji nowych standardów
- **Systemy otwarte**: pozwalające na współpracę sprzętu i oprogramowania różnych producentów, zbudowane zgodnie z pewną normą, zdolne do wymiany informacji z innymi systemami otwartymi (dzisiejszy standard)

# Ważniejsze organizacje

- **ISO** – International Standards Organization
- **IEEE** – Institute of Electronics and Electrical Engineers
	- forum standaryzacyjne. Grupa 802 zajmuje się standaryzacją [[Sieci lokalne|sieci lokalnych]], 2 najniższych warstw modelu **[[Modele warstwowe#Warstwy modelu OSI/ISO|OSI/ISO]]**
- **IETF** – Internet Engineering Task Force
	- grupa opracowująca standardy na poziomie **[[Modele warstwowe#Warstwy modelu TCP/IP|TCP/IP]]** (standaryzacja [[Protokół Komunikacyjny|protokołów]])
- **ITU-T** – International Telecommunication Union - Telecommunications Sector
- **TIA/EIA** – Telecommunications Industry Associations/Electronics Industry Association
	- organizacja zajmująca się określaniem norm dotyczących okablowania


# Dokumenty RFC (Request for Comments)

- Oficjalne dokumenty opisujące standardy sieciowe (dotyczące [[Protokół Komunikacyjny|protokołów]] - wymiany informacji w sieciach)
	 - proces publikowania nadzorowany przez **IETF**
	 - podstawowe dokumenty specjalistów
	 - numerowane, np. RFC 1889
- najstarszy: RFC 1 (7 kwietnia 1969) – Host Software
- kolejne wersje unieważniają poprzednie
- dostępne darmowo, np. www.rfc-editor.org lub www.zvon.org


# Podział sieci komputerowych

- Sieci rozległe **WAN** (Wide Area Network)
	- łączą sieci lokalne, przykładem jest globalna sieć Internet
- Sieci lokalne **LAN** (Local Area Network)
	- biura, uczelnie, fabryki
	- **Fast Ethernet** (100Base-T) - standard szybkiej sieci lokalnej o prędkości przesyłu danych 100 Mb/s (zwykły [[Ethernet]] ma 10 Mb/s). Najpopularniejsza wersja to 100Base-TX używająca dwóch par [[Media komunikacyjne#Skrętka|skrętek]] kategorii 5.
- Sieci miejskie **MAN** (Metropolitan Area Network)
- Sieci personalne **PAN** (Personal Area Network)

# Symbole oznaczające sprzęt sieciowy

![[Pasted image 20241014175733.png|center]]

## DTE, DCE

- **DTE** - Data Terminal Equipment
	- urządzenia końcowe
	- komputery, routery
- **DCE** - Data Communication Equipment (też: Data Circuit Terminating Equipment)
	- urządzenia pośredniczące w transmisji
	- regeneratory sygnału, mostki, przełącznice, modemy, serwery komunikacyjne

# Typy Sieci według rodzaju komutacji

**Komutacja** (ang. *switching*) - dynamiczne zestawianie połączeń, tworzenie połączenia między hostami do przesłania informacji.

Wyróżniamy dwa rodzaje komutacji:
- Sieci z komutacją łączy (ang. *circuit switching*)
	- tradycyjna telefonia, POTS (Plain Old Telephone Service)
		- stare telefony, łączenie drutów między telefonami
	- **ISDN**
	- telefonie komórkowe 2G
	- w zasadzie już zanikła
- Sieci z komutacją pakietów (ang. *packet switching*)
	- **IP**
	- X.25, Frame Relay
	- technologie komórkowe 2.5 G, 3G, 4G, 5G

# Rodzaje połączeń w sieciach

- poziome: użytkownicy-architektura, np. komputer-[[Urządzenia warstwy łącza danych#Switch|switch]]
- pionowe: między elementami infrastruktury, np. switch-switch