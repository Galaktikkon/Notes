# Network Interface Card (NIC)
**Karta sieciowa**:
- Urządzenie pracujące w warstwie (pierwszej i) drugiej modelu [[Modele warstwowe#Warstwy modelu OSI/ISO]]
	- (zapewnia dostęp do medium w określonym standardzie)
	- zajmuje się formowaniem ramek, obsługuje określony protokół warstwy łącza danych
	- posiada (najczęściej niezmienny) adres **MAC**
- Połączone z urządzeniem sieciowym poprzez jego magistralę
# Rodzaje transmisji:
- w żadnym przypadku nie ma gwarancji, że informacja trafi tylko do zaadresowanego urządzenia! Adres mówi tylko o tym, czy urządzenie powinno się zainteresować informacją
- na współczesnym sprzęcie [[Urządzenia warstwy łącza danych#Switch|switche]] interpretują [[Ramka|ramki]], żeby powyższe nie zachodziło, ale sam [[Ethernet]] niczego nie gwarantuje
## Broadcast
- wysyłany do wszystkich urządzeń w danej sieci
- otrzymają go wszystkie urządzenia, czy tego chcą, czy nie
- granice: [[Routing#Router|routery,]] ew. [[Urządzenia warstwy łącza danych#Switch|switche]] z [[Wirtualne sieci lokalne (VLAN)|VLANami]]
- [[Segmentacja Sieci#Domena Rozgłoszeniowa|domena rozgłoszeniowa]] to wszędzie tam, gdzie sięga broadcast
- adres MAC - FF:FF:FF:FF:FF:FF
- używane np. do znalezienia posiadacza jakiegoś adresu IP
## Multicast

- wysyłany do grupy odbiorców
- grupa jest traktowana jak jeden “specjalny” odbiorca
- odbiorcy wyznaczani przez specjalny komunikat dołączenia do danej grupy multicastowej
- najmłodszy bit najstarszego bajtu to 1 (prawy bit z bajtu najbardziej na lewo)
- innymi słowy: najstarszy bajt jest nieparzysty
- wybór powyższego: pozwala interfejsowi od razu przygotować się do obsługi takiej [[Ramka|ramki]]
## Unicast

- do konkretnego urządzenia w sieci
- wszystkie adresy, które nie są specjalne (2 powyższe przypadki)
- nie ma gwarancji, że informacja trafi tylko do zaadresowanego urządzenia (patrz ostrzeżenie powyżej)
## Anycast

- rodzaj wysyłania pakietu typu “traf gdziekolwiek”. W praktyce trafia do najbliższego topologicznie hosta.

## Tryb Promiscuous

Tryb pracy interfejsu sieciowego (np. [[Adresacja w sieciach LAN#Network Interface Card (NIC)|ethernetowej karty sieciowej]], polegający na odbieraniu całego ruchu (wszystkie [[Ramka|ramki]]) docierającego do karty sieciowej, nie tylko skierowanego na [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adres MAC]] karty sieciowej. Aby przestawić kartę sieciową w tryb promiscuous w systemie Linux należy wydać w terminalu polecenie:

```bash
ifconfig <karta_sieciowa> promisc
```

## Analizator ramek ([sniffer](https://minecraft.fandom.com/wiki/Sniffer))

To narzędzie służące do przechwytywania i analizowania ruchu sieciowego. Działa w połączeniu z kartą sieciową pracującą w [[#Tryb Promiscuous|trybie promiscuous]]. Popularne narzędzia tego typu to Wireshark. Sniffery pozwalają analizować szczegółowe informacje o ramkach, takie jak [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adresy MAC]], [[Adres IPv4|IP]], protokoły transportowe (**TCP/UDP**), a nawet zawartość przesyłanych danych. Są używane do diagnostyki sieci, wykrywania problemów i bezpieczeństwa.


# Adres MAC (Media Access Control):

- adres fizyczny/sprzętowy
- 48-bitowy adres, zapisywany szesnastkowo w postaci 6 bajtów: $\text{XY:XX:XX:ZZ:ZZ:ZZ}$
- skojarzony na stałe z konkretnym sprzętowym interfejsem sieciowym, np. kart sieciowa, moduł Wi-Fi, moduł Bluetooth
 - jednoznaczny, unikatowy identyfikator urządzenia na poziomie warstwy łącza
- nie wystarcza on do kontrolowania dostępu do sieci
- 3 starsze bajty (po lewej):
	- Organizationally Unique Identifier, **OUI**
	- producent dostaje taki od **[[Wprowadzenie#Ważniejsze organizacje|IEEE]]** (jeden albo więcej)
	- jeżeli drugi najmłodszy bit najstarszego bajtu (bit 2 w zapisie binarnym tego bajtu) jest ustawiony na 1, to adres MAC jest zarządzany lokalnie i odpowiada za niego w całości lokalny admin (ustala, jak chce; jak są kolizje - jego wina)
-  3 młodsze bajty (po prawej):
	- unikalne dla urządzenia (Network Interface Controller specific)
	- producent / administrator sieci ustala, jak chce