# Idea

![[Pasted image 20241029232340.png|center]]

- **VLAN** (*Virtual Local Area Network*)
- służą do logicznego grupowania urządzeń za pomocą [[Urządzenia warstwy łącza danych|urządzeń warstwy 2]]
- pojęcie czysto logiczne, nie rusza fizycznej warstwy sieci
- jest szybsze i łatwiejsze, niż wykorzystywanie routerów (urządzeń warstwy 3)
- pozwalają np. podzielić sieć na dwie sieci bez fizycznego przełączania kabli i dodawania urządzeń
- reguła 80/20
	- 80% ruchu chcemy przełączać, a 20% routować

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

- statyczne:
	- port-based, port-centric
	- ustawienie na stałe, że dany port należy do danego VLANu
	- definiowana przez administratora
	- tak długo, jak użytkownik nie ruszy konfiguracji [[Urządzenia warstwy łącza danych#Switch|switcha]], to tylko admin może coś zmienić
- dynamiczne:
	- musi być włączony przez admina
	- urządzenia identyfikuje MAC (z czym użytkownik może majstrować) lub protokół warstwy 3 (czyli zwykle IP)
	- VLAN związany z danym portem określa się automatycznie przy podłączaniu urządzenia
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

- pojedyncze łącze, którego jednocześnie używa wiele VLANów (multipleksuje łącze)
- zwykle łącza punkt-punkt o dużej przepustowości (bo muszą ogarnąć wiele VLANów naraz) i dostępności (np. zwielokrotnienie, żeby zagwarantować brak awarii)
- z takich łączy buduje się okablowanie szkieletowe (backbone), czyli kable z serwerowni do punktów dystrybucyjnych (IDF), gdzie są switche ogarniające podłączone do nich VLANy
- trunkowanie - przesyłanie danych VLANu przez trunk

# Techniki trunkowania VLANów

## TDM

 - czas dzieli się na tyle VLANów, ile trzeba trunkować
- dla $n$ VLANów każdy dostaje 1/n czasu transmisji w jakiejś jednostce czasu
- problem: rzadko VLANy potrzebują tak samo dużej transmisji, więc dużo czasu się marnuje
- przez niewydajność rzadko używane
## Filtrowanie

- każdy switch posiada zestaw zasad filtracji ramek i dalszego postępowania z nimi
- przykład: switch posiada tablicę MAC-VLAN, którą ma statycznie narzuconą, lub też switche z trunka wysyłają sobie wzajemnie aktualizacje tablic
- problem: słabo skalowalne, bo trzeba aktualizować instrukcje filtrowania wielu switchom
- rzadko używane
## Tagowanie

- na czas transportu przez trunk ramki dostają ID VLANu (tag), do którego należą, na koniec trunka switch odczytuje ID, usuwa je i przesyła już normalną ramkę Ethernet do komputera z właściwego VLANu
- stara technika: Inter-Switch Link (external tagging, tagowanie dwupoziomowe), rozwiązanie Cisco, opakowanie ramki Ethernet w dodatkową ramkę ISL: ISL header (26 B) + ramka Ethernet + CRC ramki ISL (4 B)
- nowoczesna technika: 802.1Q, standard IEEE
