# Carrier Sense Multiple Access

- rozwiązuje problem z tym, że przy [[Przesyłanie informacji#Tryby Transmisji Danych|half-duplexie]] w [[Ethernet|Ethernecie]] wiele urządzeń naraz “widzi” wolne medium, nadaje i występuje wtedy kolizja (bo czas propagacji jest skończony i urządzenia nie “zauważyły”, że inne już nadają)
- protokół wielodostępu z wykrywaniem fali nośnej
- ocenia ruch w kanale transmisyjnym przed wysłaniem sygnału przez medium fizyczne
- dzieli się na 2 protokoły:
	- **CSMA/CA** - with Collision Avoidance, używa sygnałów próbnych do sprawdzenia, czy nie ma kolizji; używany w sieciach bezprzewodowych
	- **CSMA/CD** - with Collision Detection
- Carrier Sense:
	- nasłuchiwanie ruchu w medium przed przesłaniem sygnału
	- pozwala na wykrycie sygnału wysłanego z innego nadajnika
	- pozwala bezkolizyjnie przesyłać własne sygnały
- Multiple Access:
	- wiele stacji naraz ma dostęp do medium
	- jeżeli któraś z nich (lub wiele z nich) stwierdzi, że może nadawać, to zaczyna (nie synchronizują się między sobą)
	- powoduje kolizje

# with Collision Detection

**CSMA/CD**:
- protokół wielodostępu z wykrywaniem kolizji
- pozwala na wielodostęp w half-duplexowym Ethernecie
- urządzenia nasłuchują własne wyjście, czy się zgadza - w razie wykrycia kolizji emituje się sygnał zagłuszający (jam signal), który “psuje” sekwencję i nadawcy to wykrywają
- algorytm: ![[Pasted image 20241015213656.png|center]]
	1. Jeżeli jest cisza, to po odczekaniu [[Ramka#Interframe Gap (IFG)|IFG]] urządzenie zaczyna transmisję ramki.
		- a. Jak nie ma kolizji, to dokańczamy transmisję ramki.
		- b. Jak jest kolizja, to przetransmituj jam signal przez czas potrzebny do wysłania 32 bitów (żeby na 100% wszyscy ogarnęli, że są zagłuszani) i zrealizuj algorytm Binary Exponential Backoff
	2. Jeżeli jest zajęte, to czekamy, aż się zwolni
	3. Jeżeli nie powiedzie się 15 prób, to zwracamy warstwie sieciowej błąd transmisji
## Algorytm Binary Exponential Backoff

- idea: mamy $N$ hostów i chcemy, żeby prawdopodobieństwo rozpoczęcia nadawania było $\frac{1}{N}$ (wtedy prawdopodobnie nie będzie kolizji)
- chcemy preferować stacje z mniejszą ilością nieudanych prób
- ten algorytm przybliża powyższe
- działanie:
	1. Po kolizji losuje liczbę w zakresie $n = [0, 2^{(\text{nr próby}) - 1}]$
	2. Czeka $n$ szczelin czasowych: $\text{T} = \text{ST} \cdot  n$
	3. Ponawia próbę nadawania
- nr próby to max 10, żeby funkcja wykładnicza nie urosła za bardzo