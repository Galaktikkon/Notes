Jest to jedno z głównych zadań warstwy 3 - znalezienie drogi (najlepszej) między dwoma hostami.

# Routing źródłowy

- routing należący do nadawcy, wpisuje on adresy do nagłówka pakietu IP, realizuje strict/loose source routing
- pakiet zna drogę powrotną
- routery nie muszą znać odległych sieci - mają tylko przekazywać dalej zgodnie ze wskazaniami pakietu
## Wady

- niewygodny
- ograniczony zasięg (bo pole “opcje” ma ograniczoną wielkość), ścieżki maksymalnie po 9 routerów
- rzadko wykorzystywany
- często blokowany przez administratorów (bo stanowi zagrożenie dla bezpieczeństwa sieci)

# Tablica routingu

- odwzorowanie postaci:
	- (IP sieci docelowej) $\rightarrow$ (IP następnego skoku lub “sieć bezpośrednia”)
- adresy IP = IP + maska
- IP następnego skoku = brama (next hop IP)
- tablica, dzięki której router wie, gdzie kierować pakiety zaadresowane do danej sieci
- pamięta tylko informację o następnym routerze na trasie do sieci (next hop IP) lub informację o tym, że sieć jest już przyległa do danego routera (nie zna całej ścieżki)
- wpisy w tablicy są tam:
	- ustawiane ręcznie przez administratora - routing statyczny
	- tworzone i zarządzane automatycznie przez protokół - routing dynamiczny
- jeżeli wpis nie zostanie znaleziony w tablicy, to realizuje się najdłuższe dopasowanie, czyli “wygrywa” ten adres docelowy w tablicy, który ma najdłuższą zgodną maskę podsieci
- maska 255.255.255.255 to "najlepsza" maska
- **brama domyślna** - maska 0.0.0.0, “najgorsza” maska, zostanie użyta, jeżeli nie znajdzie się żadne lepsze dopasowanie i jest w tablicy
- wpisy można **agregować**, łącząc wiele podobnych w jeden (np. jeśli wiele wpisów ma ten sam adres następnego skoku) i zaoszczędzić miejsce w tablicy routingu (pozwala na to zasada najdłuższego dopasowania)

# Routing statyczny

- wpisy w tablicy routingu są zarządzane ręcznie przez administratora
## Zalety

- łatwa konfiguracja (dla małych sieci)
- nie wymaga dodatkowych danych na łączu ani ich przetwarzania (w przeciwieństwie do dynamicznego)
- przewidywalność – trasa pakietu jest znana i może być kontrolowana
## Wady

- nie skaluje się
- zmiany konfiguracji są trudne (zmiana może wymagać przekonfigurowania całej sieci)
- brak dostosowania do zmieniających się warunków w sieci

# Agregacja wpisów

- idea: niektóre wpisy są redundantne i można je zredukować
- trasy biegnące przez ten sam router można zgrupować, bo i tak ważny jest tylko next hop
- dla każdego pakietu routery wybierają z tablicy routingu wpis o najdłuższej pasującej masce, więc wpisy można rozróżniać

## Przykład

Mamy wpisy w tablicy routingu:

- 1.1.1.0/24 directly connected
- 1.2.3.0/30 via Serial0/0
- 1.4.0.4/30 via Serial0/0
- 10.0.23.0/24 via 1.1.1.2
- 10.4.8.0/21 via 1.1.1.2
- 10.4.8.0/24 via 1.1.1.42
- 10.3.0.128/25 via 1.1.1.3
- 193.193.65.224/27 via 1.1.1.42
- 2.0.0.1/32 via 1.1.1.42

Na początek można pogrupować wpisy według next hopu, tzn. adresu, który jest po “via”:
1. 
	- 1.1.1.0/24 directly connected
2. 
	- 1.2.3.0/30 via Serial0/0
	- 1.4.0.4/30 via Serial0/0
3. 
	- 10.0.23.0/24 via 1.1.1.2
	- 10.4.8.0/21 via 1.1.1.2
4. 
	- 10.4.8.0/24 via 1.1.1.42
	- 193.193.65.224/27 via 1.1.1.42
	- 2.0.0.1/32 via 1.1.1.42
5. 
	- 10.3.0.128/25 via 1.1.1.3

Teraz każdą grupę można połączyć, biorąc kolejne zgodne liczby; kiedy w ramach jednej
grupy zaczną się różnić, to do końca idą same zera. Można także skrócić maski podsieci do
liczby zgodnych bitów.
1. 
	- 1.1.1.0/24 directly connected
	Nie ma co upraszczać, zostaje bez zmian.
2. 
	- 1.2.3.0/30 via Serial0/0
	- 1.4.0.4/30 via Serial0/0
	
	Zgadza się pierwsza liczba (pierwszy bajt = 8 bitów). Trzeba sprawdzić, ile bitów drugiego
	bajtu się zgadza.
	
	$2_{10} = 00000010_2$
	$4_{10} = 00000100_2$
	
	Zgadza się pierwsze 5 bitów, więc w sumie 8+5=13 bitów, więc długością maski będzie 13.
	Zgrupowany adres: 1.0.0.0/13 via Serial0/0
3. 
	- 10.0.23.0/24 via 1.1.1.2
	- 10.4.8.0/21 via 1.1.1.2
	Zgadza się pierwsza liczba (8 bitów). Z drugiej liczby zgadza się pierwsze 5 bitów, więc
	maska będzie miała 13 bitów.
	
	$2_{10} = 00000010_2$
	$4_{10} = 00000100_2$
	
	Zgrupowany adres: 10.0.0.0/13 via 1.1.1.2
4. 