
- [[CSMA|CSMA/CD]] służy tylko do [[Przesyłanie informacji#Tryby Transmisji Danych|half-duplexu]], bo obsługuje to, że tylko jeden naraz nadaje, a w [[Przesyłanie informacji#Tryby Transmisji Danych|full-duplexie]] wszyscy mogą naraz nadawać, więc nie ma kolizji, więc nie ma sensu tam wrzucać takiego protokołu
- wszystkie 3 elementy są wtedy bez sensu:
	- CS (Carrier Sense) - stacje nie muszą badać, czy medium jest wolne, bo zawsze jest
	- MA (Multiple Access) - każda stacja ma swoje medium
	- CD (Collision Detection) - nie ma kolizji do wykrywania
- w szczególności zastosowanie CSMA/CD w full-duplexie degradowałoby go do half-duplexu, bo ten protokół wymaga, żeby tylko jedno urządzenie naraz nadawało!

# Wymagania full-duplexu

- łącze point-to-point, bezpośrednie łączenie urządzeń na poziomie warstwy drugiej
- obie strony muszą mieć ten tryb
- łącze musi mieć niekolidujące ścieżki odbioru i transmisji (mogą być 2 przewody, może być to samo łącze z odpowiednim przesyłaniem sygnału


# Konsekwencje używania full-duplexu:

- dla dwukierunkowego 100 Mb/s potrzebujemy łącza o [[Przesyłanie informacji#Throughput|przepustowości]] 200 Mb/s (po równo na nadawanie i odbiór)
- nie można używać hubów ([[Przesyłanie informacji#Tryby Transmisji Danych|half-duplex]]), trzeba [[Urządzenia warstwy łącza danych#Switch|switchy]] ([[Przesyłanie informacji#Tryby Transmisji Danych|full-duplex]])
- nie można używać 10Base-2, 10Base-5, 100Base-T4
- nie można używać [[CSMA|CSMA/CD]]
- nie trzeba ograniczać rozległości sieci przez kolizje (ale dalej są ograniczenia fizyczne!)
- standard 802.3x

# Full duplex włącza się:

- administracyjnie - włączony na stałe
- automatycznie - przez [[Protokół autonegocjacji|protokół autonegocjacji]]