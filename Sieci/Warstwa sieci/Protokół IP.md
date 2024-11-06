
# Właściwości

- bezpołączeniowy - nie zestawia połączenia przed rozpoczęciem przesyłania pakietów, tylko przesyła każdy pakiet oddzielnie
- brak potwierdzenia dostarczenia - nie ma gwarancji, że wiadomość w ogóle dotarła
- brak timeout’u, retransmisji - związane z powyższym
- brak kontroli poprawności danych - nie ma sumy kontrolnej
- brak kontroli przepływu
- ograniczenie długości wiadomości
- nie gwarantuje braku powtórzeń - te same pakiety mogą przyjść wielokrotnie
- nie gwarantuje kolejności - pakiety mogą przyjść w dowolnej kolejności
## Użyteczność protokołu IP mimo powyższych wad

- braki rozwiązują warstwy wyższe, w szczególności TCP/UDP
- prostota i elastyczność
- minimalny narzut
- zapewnia organizację trasy do przesyłania pakietów
- w praktyce powyższe braki nie są tak odczuwalne, jak by się mogło wydawać

# Fragmentacja pakietów IP

- problem: maksymalny rozmiar pakietu IP to 64 kB, a warstwy niższej może być mniejszy, np. dla ramek Ethernet to 1518 B
- **MTU (Maximum Transmission Unit)** warstwy niższej wyznacza, czy trzeba podzielić datagram IP na mniejsze kawałki
- warstwa 3 odpytuje protokół warstwy 2 o jej **MTU** i w razie potrzeby fragmentuje datagram IP
- fragmentacja następuje w komputerze wysyłającym i routerach pośrednich
- **MTU** ścieżki - najmniejsze **MTU** na ścieżce między nadawcą a odbiorcą
- składanie następuje tylko u odbiorcy

# Składanie pakietów IP

- każdy niekompletny pakiet IP u hosta ma bufor, tablicę znaczników i zegar (pakiet, nie fragment!)
- fragmenty posiadają pole **identyfikatora**, więc wiadomo, do którego pakietu należą
- wypełnianie bufora:
	- na podstawie **przesunięcia** (patrz budowa datagramu IP niżej) wiadomo, jak daleko od początku wstawić dany fragment do bufora
	- po odebraniu fragmentu uruchamia się zegar na czas `max(init_value, TTL)` (TTL - Time To Live, czas życia zegara); jeżeli następny fragment nie dojdzie w tym czasie, to odrzuca się cały pakiet (czyści bufor)
	- ostatni fragment posiada 0 na bicie **MF (More Fragments)**, więc wiadomo, że to koniec pakietu (przy czym nie musi przyjść ostatni - po prostu jest ostatni w sensie ułożenia fragmentów w pamięci)

# Budowa Pakietu IP (Datagramu IP)

![[Pasted image 20241106004230.png|center]]

## Wersja

- po prostu 4 (0100) dla IPv4

## Długość nagłówka

- 4 b
- zawiera liczbę x - liczbę “słów” 4-bajtowych
- długość nagłówka = 4 * x [bajt]
- minimalna i maksymalna wielkość nagłówka - 20-60 B
- długość minimalna
	- bez żadnych opcji
- długość maksymalna
	- wartość maksymalna z 4 bitów to 15, 15 * 4 B = 60 B

## Typ usługi

- ToS, Type of Service
- 8 bitów
- po kolei:
	- **priorytet (3 bity)**
		- im wyższa wartość liczbowa, tym ważniejszy pakiet; jeżeli router jest zapełniony i musi odrzucić jakieś pakiety, to odrzuci najpierw te o niższym priorytecie
	- **opóźnienie (1 bit)**
		- jeżeli ustawiony (na 1), to opóźnienie ma być jak najmniejsze; wykorzystywane np. w pakietach telnet (protokół zdalnego terminala)
	- **niezawodność (1 bit)**
		- niezawodność ma być jak największa, np. protokół SNMP do zarządzania i monitorowania sieci
	- **przepustowość (1 bit)**
		- przepustowość ma być jak największa, np. protokół FTP do wymiany plików
	- ECN (Explicit Congestion Notification, 1 bit) 
		- powiadamia o przeciążeniu łącza

## Długość całkowita

- długość całego pakietu IP (nagłówek + dane)
- w bajtach
- minimalna długość: 20 B (sam nagłówek)
- maksymalna długość: 64 kB

## Pola do fragmentacji

- **identyfikator (16 bitów)** - numer pakietu, do którego należy fragment
- **bit 0** - padding (wypełnienie)
- **Don’t Fragment (DF, 1 bit)** - oznaczenie, że dany pakiet nie ma być fragmentowany; jeżeli trzeba by było (np. przez protokół warstwy niższej), to nadawca dostaje komunikat z błędem protokołu ICMP Fragmentation Needed
- **More Fragments (MF, 1 bit)** - zawsze 1, poza ostatnim fragmentem
- **przesunięcie fragmentacji (offset, 13 bitów)** - miejsce, na które w pakiecie ma trafić fragment, liczone w jednostkach po 8 bajtów (w związku z tym przy podziale wielkości fragmentów poza ostatnim muszą być podzielne przez 8!)

## Time To Live (TTL)

- 8 bitów
- maksymalna liczba przeskoków pakietu w trakcie obiegu
- zapobiega krążeniu pakietów w sieci (cykle)
- wartość zmniejszana o 1 po każdym przetworzeniu pakietu przez router
- przy usunięciu nadawca dostaje komunikat z błędem **ICMP** Time Exceeded
## Protokół

- 8 bitów
- informacja o protokole warstwy wyższej (**TCP/UDP**)
- odbiorca dzięki temu polu wie, jaki protokół warstwy wyższej ma dostać dane z pakietu

## Suma kontrolna

- dotyczy tylko nagłówka
- sprawdzana przy przetwarzaniu pakietu, więc np. w routerach po drodze
- o poprawność danych dbają wyższe warstwy

## Adresy źrodłowy i docelowy

- po 4 B każdy
- źródłowy - zawsze pojedynczego hosta
- docelowy - pojedynczego hosta, grupowy lub rozgłoszeniowy


## Opcje

- zmienna długość, wielokrotność 32 bitów
- mogą być, ale nie muszą
- każda opcja jest wypełniana zerami do długości 32 bitów (padding)


### Budowa pola opcji

![[Pasted image 20241106004929.png|center]]

- **Option Type**
	- **flag (copied)** - czy opcja ma być kopiowana do wszystkich fragmentów danego pakietu
	- **class**
		- 0 dla opcji kontrolnych, 
		- 2 dla debugowania i pomiarów, 
		- 1 i 3 są zarezerwowane
	- number - specyfikuje konkretną opcję (patrz poniżej)
- **Option Length**
	- długość całego pola danej opcji (od flag do końca Option Data)
- **Option Data**
	- dane potrzebne danej opcji

### Możliwe opcje

- **0 - koniec listy opcji**
	- oznaczenie końca listy opcji, całe pole opcji to tylko 1 bajt, brak pól Option Length i Option Data
- **1 - brak operacji**
	- podobnie do powyższego, bez szczególnego znaczenia
- **2 - security**
	- różne dane związane z bezpieczeństwem, np. szyfrowaniem pakietu
- **3 - Loose Source Routing**
	- ciąg adresów IP routerów, pakiet ma przejść po kolei przez te routery, ale z dowolną trasą pomiędzy danymi routerami
- **4 - Internet Timestamp**
	- zwykły znacznik czasowy
- **7 - Record Route**
	- lista adresów IP, przez które przeszedł pakiet (jego trasa)
- **8 - Stream ID**
	- zwykle ignorowany
- **9 - Strict Source Routing**
	- ciąg adresów IP routerów, pakiet ma przejść po kolei przez te routery i tylko i wyłącznie przez te routery, w danej kolejności

