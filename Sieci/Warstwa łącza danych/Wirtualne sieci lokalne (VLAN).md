# Idea

![[Pasted image 20241029232340.png|center]]

- **VLAN** (*Virtual Local Area Network*)
- służą do logicznego grupowania urządzeń za pomocą [[Urządzenia warstwy łącza danych|urządzeń warstwy 2]]
- pojęcie czysto logiczne, nie rusza fizycznej warstwy sieci
- jest szybsze i łatwiejsze, niż wykorzystywanie routerów (urządzeń warstwy 3)
- pozwalają np. podzielić sieć na dwie sieci bez fizycznego przełączania kabli i dodawania urządzeń
- reguła 80/20
	- 80% ruchu chcemy przełączać, a 20% routować
	- "switch when you can, route when you must"

## Podział sieci wirtualnych

- można grupować po:
	- portach
		- na jednym porcie w danym czasie tylko jeden VLAN
		- najpopularniejsze rozwiązanie
		- duża ingerencja administratora
		- rozwiązanie możliwe do stosowanie an wielu połączonych ze sobą [[Urządzenia warstwy łącza danych#Switch|switchach]]
	- [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adresach MAC]]
		- łatwa (automatyczna rekonfiguracja)
		- konieczność przypisania hosta do VLAN
		- wydajność znacznie spada gdy reprezentanci różnych grup są na tym samym porcie switcha (duże obciążenie portu)
	- protokole warstwy 3
		- rozróżnienie w oparciu o protokoły warstwy 3 i adresację logiczną
		- nie ma routingu, stosuje się STA
		- brak konieczności rekonfiguracji hosta po zmianie podłączenia
		- brak konieczności tagowania ramek
		- wolniejsze działanie
## Modele VLAN

Wyróżniamy dwa modele VLAN.
### Dostępowe

- Sposób definiowania filtracji ramek w celu ograniczenia komunikacji pomiędzy komputerami w jednej [[Sieci lokalne|sieci LAN]] z mostkami (lub switchami)
### Niezależne

- Sposób użytkowania jednej fizycznej sieci jako wielu niezależnych sieci

## Zalety

- zmniejszenie [[Segmentacja Sieci#Domena Rozgłoszeniowa|domeny rozgłoszeniowej]] ([[Ramka|ramki]] [[Adresacja w sieciach LAN#Broadcast|broadcastowe]] rozsyłają się po mniejszej części sieci i nie zajmują tyle ruchu)
- lepsza skalowalność
- większe bezpieczeństwo (można odseparować sieci)
- programowa (re)konfiguracja (nie trzeba fizycznie ruszać kabli)
## Wady

- koszt urządzeń do obsługi i konfiguracji VLANów (bardziej historyczne)
- trudniejsze w konfiguracji (od wsadzania kabli do gniazdek, ale dalej łatwiej niż router)
- komunikacja między urządzeniami różnych VLANów jest trudna (zwykle wymaga routera lub switcha warstwy 3)
- mało zaawansowana standaryzacja
## VLANy a domeny:

- [[Segmentacja Sieci#Domena Kolizyjna|domena kolizyjna]]: nie mają żadnego wpływu (bo domena kolizyjna to pojęcie fizyczne, a VLANy są czysto logiczne)
- [[Segmentacja Sieci#Domena Rozgłoszeniowa|domena rozgłoszeniowa]]: każdy VLAN to osobna domena rozgłoszeniowa

# Sposoby definiowania przynależności do sieci wirtualnych

## Statyczne

- port-based, port-centric
- ustawienie na stałe, że dany port należy do danego VLANu
- definiowana przez administratora
- tak długo, jak użytkownik nie ruszy konfiguracji [[Urządzenia warstwy łącza danych#Switch|switcha]], to tylko admin może coś zmienić

## Dynamiczne

- musi być włączony przez admina
- urządzenia identyfikuje MAC (z czym użytkownik może majstrować) lub protokół warstwy 3 (czyli zwykle IP)
- VLAN związany z danym portem określa się automatycznie przy podłączaniu urządzeni
- dla sprawdzenia docelowego VLANu używa się serwera VMPS (VLAN Membership Policy Server, mapa MAC-VLAN)


# VLAN a wiele switchy

- problem: możemy chcieć, żeby porty z wielu switchy były w tym samym VLANie (bo np. nie możemy fizycznie ruszyć sieci i tych switchy, a logiczny podział tego wymaga)
- robienie kablami nie wchodzi w grę, bo po pierwsze eliminuje zalety VLANów, a po drugie dla każdej sieci trzeba by osobnego kabla i portu
- idea: skoro nie da się zrobić “1 kabel - 1 VLAN”, to można zrobić “1 kabel - wiele VLANów” + mechanizm współdzielenia łącza
- 3 zasadnicze rozwiązania w praktyce:
	- **Time-Division Multiplexing (TDM)**
	- filtrowanie
	- tagowanie

# Trunk

- pojedyncze łącze między przełącznicami, którego jednocześnie używa wiele VLANów (multipleksuje łącze)
- element szkieletu sieci (*backbone*)
- zwykle łącza punkt-punkt o dużej przepustowości (bo muszą ogarnąć wiele VLANów naraz) i dostępności (np. zwielokrotnienie, żeby zagwarantować brak awarii)
- z takich łączy buduje się okablowanie szkieletowe (backbone), czyli kable z serwerowni do punktów dystrybucyjnych (IDF), gdzie są switche ogarniające podłączone do nich VLANy
- trunkowanie - przesyłanie danych VLANu przez trunk

# Techniki trunkowania VLANów

Wyróżniamy 3 techniki trunkowania:
## TDM

- czas dzieli się na tyle VLANów, ile trzeba trunkować
- dla $n$ VLANów każdy dostaje $\frac{1}{n}$ czasu transmisji w jakiejś jednostce czasu
- problem: rzadko VLANy potrzebują tak samo dużej transmisji, więc dużo czasu się marnuje
- przez niewydajność rzadko używane
## Filtrowanie

- każdy [[Urządzenia warstwy łącza danych#Switch|switch]] posiada zestaw zasad filtracji ramek i dalszego postępowania z nimi
- przykład: switch posiada tablicę MAC-VLAN, którą ma statycznie narzuconą, lub też switche z trunka wysyłają sobie wzajemnie aktualizacje tablic
- problem: słabo skalowalne, bo trzeba aktualizować instrukcje filtrowania wielu switchom
- rzadko używane
## Tagowanie

- na czas transportu przez trunk ramki dostają ID VLANu (tag), do którego należą, na koniec trunka switch odczytuje ID, usuwa je i przesyła już normalną ramkę [[Ethernet]] do komputera z właściwego VLANu
- stara technika: Inter-Switch Link (external tagging, tagowanie dwupoziomowe), rozwiązanie Cisco, opakowanie ramki Ethernet w dodatkową ramkę ISL: ISL header (26 B) + ramka Ethernet + CRC ramki ISL (4 B)
- nowoczesna technika: 802.1Q, standard IEEE
### Technika 802.1Q tagowania ramek

- internal tagging
- modyfikuje oryginalną [[Ethernet#Ramka sieci Ethernet|ramkę Ethernet]], wstawiając dodatkowe 4 B pole, nagłówek 802.1Q, pomiędzy adres nadawcy a pole typ/długość

![[Pasted image 20241030002523.png|center]]

- wymaga przeliczenia oryginalnej sumy kontrolnej
- zwiększa maksymalny rozmiar ramki z 1518 B do 1522 B
- składa się z dwóch części: **TPID** i **TCI**
#### TPID:

- *Tag Protocol Identifier*
	- 2 B
	- wartość 0x8100
	- pozwala zidentyfikować ramkę jako 802.1Q
	- jako że te 2 B w normalnej ramce opisują typ, to urządzenia “nieznające” VLANów mogą teoretycznie dalej odbierać i przekazywać takie ramki (wtedy reszta nagłówka będzie traktowana jako część pola danych)
#### TCI:

- *Tag Control Information*
	- 2 B
	- 3 bity - **PCP (Priority Code Point)** - zarezerwowane i niezdefiniowane, może to być np. priorytet ramki według protokołu 802.1P (0 - najniższy, 7 - najwyższy, np. ważne ramki, ramki audio, ramki wideo, komunikaty sterujące siecią)
	- 1 bit - **DEI (Drop Eligible Indicator)** - czy ramka może zostać porzucona, gdy sieć jest zapchana; kiedyś - **CFI (Canonical Form Indicator)** określający, czy pierwszy bajt MACu jest w postaci kanonicznej (0, Ethernet, Token Bus), czy normalnej binarnej (1, Token Ring), pozwalało to na kompatybilność Ethernetu i Token Ring
	- 12 bitów - **VID (VLAN Identifier)** - numer VLANu danej ramki, określa maksymalną liczbę VLANów ($2^{12} = 4096$)
##### VID:

- 0 - brak tagowania, nagłówek 802.1Q ma, żeby używać priorytetu (pole PCP w TCI)
- 1 - brak oficjalnego znaczenia, ale często jest to numer VLANu do zarządzania urządzeniami sieciowymi (albo natywny VLAN, patrz niżej)
- od 2 do 4094 - swobodne wykorzystanie
- 4095 - zarezerwowane

# Optymalizacja działania i konfiguracji trunków

![[Pasted image 20241030003539.png|center]]

- problem: domyślnie switche wszystkie VLANy obsługują przez trunk, obciążając go
- idea: switche mogą wymieniać ze sobą po sąsiedzku informacje o swoich VLANach, tzw. *pruning*
- przykład: jeżeli jeden switch ma VLANy 1, 2, 3, a drugi tylko 1 i 2, to bez sensu byłoby trunkować VLAN 3 do drugiego switcha
- protokoły:
	- **GVRP (GARP VLAN Registration Protocol)** - standardowy
	- **VTP (VLAN Trunking Protocol)** - własność Cisco

## VTP

- przypisuje urządzeniom role:
	- serwer: może edytować bazę VLANów i rozsyła informację o tej bazie do pozostałych urządzeń w domenie VTP
	- klient: nie może edytować bazy VLANów (ale dalej może zmieniać przypisania swoich portów i VLANów)
	- przezroczyste: odbiera i przesyła ramki VTP, ale nie przetwarza ich
- udostępnia VTP pruning - urządzenia wysyłają sobie informacje, które VLANy gdzie są, więc nie przepychają niepotrzebnych danych przez trunki
- problem: jeżeli podłączymy nowego switcha ze starą konfiguracją VTP i przypadkiem zadziała, to zepsuje konfigurację