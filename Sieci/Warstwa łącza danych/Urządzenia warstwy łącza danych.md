#  Bridge

- Działa na poziomie warstwy 2
- OSI/ISO, interpretuje więc zawartość ramek
- Ze swojego punktu widzenia dzieli sieć na kilka części (zazwyczaj dwie) w oparciu o porty, do którego te fragmenty (segmenty) sieci są podłączone
- Posiada wiedzę pozwalającą na stwierdzenie, w którym segmencie sieci znajduje się host o danym adresie MAC
- W oparciu o tę wiedzę podejmuje decyzję, czy, a jeśli tak, to na który port przekazać ramkę, której adres docelowy pobiera i analizuje (kwalifikuje ramki na ruch lokalny lub zdalny)
- Powstał w celu odciążenia segmentów sieci Ethernet (aby CSMA/CD działało optymalnie - na 3-4% obciążenia)

![[Pasted image 20241028021209.png|center]]

## Skąd mostki wiedzą o położeniu hostów?

- Uczą się tego same
- Działając w trybie *promiscuous* pobierają adres źródłowy każdej ramki i wpisują do specjalnej tablicy **FDB** (tzw. **tablicy forwardingu**, ***forwarding database***) wraz z numerem portu, na którym ta ramka się pojawiła
- Każdy wpis ma określony czas ważności, jeśli informacja nie jest odnawiana - znika z tablicy (przydatne, gdy urządzenie zmieni port podpięcia)
- przy nowej ramce (wszystkie porty = poza tym, z którego przyszła wiadomość):
	- broadcast: wysyła ramkę na wszystkie porty
	- multicast: przy głupich [[Urządzenia warstwy łącza danych#Switch|switchach]] jak broadcast, przy lepszych podłączone hosty wysyłają najpierw komunikat protokołu IGMP o chęci dołączenia do transmisji, zapisują kto i na którym porcie jest zainteresowany grupą i przekazuje multicasty tylko do odpowiednich hostów
	- adresu nie znaleziono w tablicy forwardingu: ramka zostaje wysłana na wszystkie porty (odbiorca może się wtedy odezwać i można uzupełnić tabelę), dodajemy wpis do tablicy z adresem MAC i portem nadawcy
	- adres znaleziono i port odbiorcy = port nadawcy: nie przekazuj dalej, bo ramka ma zostać w tym samym segmencie sieci (switch prawdopodobnie dostał ramkę przez bezmyślnie przekazujący ją hub)
	- adres znaleziono i ma inny port: przekaż dalej na ten port, aktualizacja w tablicy czasu życia wpisu dla danego adresu MAC

![[Pasted image 20241028021500.png|center]]

# MAC flooding

- technika ataku na tablicę forwardingu, w którym komputer generuje dużo ramek z losowymi adresami źródłowymi, tak, aby zapełnić tablicę. Dzięki temu można wykorzystać exploity związane z przepełnieniem pamięci w [[Urządzenia warstwy łącza danych#Switch|switchu]] oraz wymusza to na switchu wysyłanie wszystkich wiadomości jak [[Adresacja w sieciach LAN#Broadcast|broadcastowych]]
# Sposoby przekazywania ramek

- **store-and-forward**:
	- przełączanie asymetryczne
	- może obsługiwać różne prędkości bitowe, tzn. odebrać całą ramkę z jedną prędkością, sprawdzić i nadać z inną (np. Ethernet -> Gigabit Ethernet)
	- wymaga buforowania pamięci
	- odbieramy ramkę, sprawdzamy sumę kontrolną, jeśli ok to zapamiętujemy adres nadawcy i decydujemy o jej losie
	- zaleta: wykrywa wszystkie błędne ramki
	- wada: wprowadza największe opóźnienie
- **cut-through**:
	- przełączanie symetryczne
	- port wyjściowy musi pracować z co najmniej taką samą prędkością, co wejściowy
	- decyzja o ramce jest podejmowana jeszcze przed otrzymaniem całej (zanim można w ogóle policzyć sumę kontrolną)
	- 2 rozwiązania:
		- **fast-forward**: decydujemy, jak dostaniemy ostatni bit adresu nadawcy (12 B); szybkie, ale może powodować problemy z kolizjami
		- **fragment-free**: decydujemy, jak odczytamy 64 B ramki (minimalny rozmiar), bo zgodnie z CSMA/CD wtedy na 100% już nie ma kolizji; wolniejsze, ale zapewnia brak kolizji
	- przy half-duplexie fragment-free jest bezpieczniejsze, ale przy full-duplexie mamy gwarancję braku kolizji, więc można wykorzystywać szybsze fast-forward
# Alignment error 

- błąd wyrównania, rozmiar ramki nie jest wielokrotnością 8 (ramka nie składa się tylko z pełnych bajtów)
# Switch

- [[Urządzenia warstwy łącza danych|Mostek]] wieloportowy – identyczna zasada działania
	- zmniejszenie domeny kolizyjnej
	- możliwość równoczesnej transmisji w różnych segmentach sieci
	- obsługa protokołu spanning-tree
- Przełącznice asymetryczne - wiele portów wolnych (10 Mb/s) i jeden (kilka) szybkich (100 Mb/s)
	- stacje robocze i serwery
	- problem z buforowaniem
- Szybsze niż mostki, zazwyczaj stosują mechanizm cut-through
# Mikrosegmentacja
- technika w [[Urządzenia warstwy łącza danych#Switch|switchach]], zgodnie z którą na jednym porcie jest tylko 1 host.