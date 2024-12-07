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