 - [film](https://www.youtube.com/watch?v=kfvJ8QVJscc&t) o protokole OSPF wprowadzający w tematykę
 - [film](https://www.youtube.com/watch?v=yWNgrG5tlyI) o protokole OSPF  budujący intuicję

 # Cechy protokołu OSPF (Open Shortest Path First)
- protokół [[Routing#Protokoły stanu łącza (link state)|link state]]
- [[#Zalewanie z potwierdzeniami|zalewanie z potwierdzeniami]]
- zabezpieczanie pakietów z opisami sieci
- każdy rekord opisu łącza ma czas życia (potem są usuwane)
- wszystkie rekordy są zabezpieczone sumą kontrolną
- możliwość autoryzacji komunikatów (np. hasłem)
## Zalewanie z potwierdzeniami

- gdy zostanie wykryta zmiana w topologii sieci, to wszystkie routery są powiadamiane i oczekuje się na potwierdzenie otrzymania tego powiadomienia.
# Założenia projektowe OSPF
- **separacja routerów od innych komputerów**
	- łącze z sąsiednim routerem identyfikowane adresem routera
	- łącze z innym komputerem adresowane numerem sieci
- **obsługa sieci rozgłoszeniowych (Ethernet, FDDI)**
	- używa pakietów link state advertisement (LSA)
	- wykorzystuje adres multicastowy 224.0.0.6 do informowania wybranych routerów (designated routers, patrz niżej)
	- designated routers po otrzymaniu takiego pakietu jeżeli zawiera on nowe informacje rozsyłają go na adres 224.0.0.5
- **obsługa sieci bez rozgłoszeń (X.25, ATM)**
	- łącza, gdzie rozgłoszenia są drogie
	- używa się wielu połączeń punkt-punkt, bo jest taniej
- **podział wielkich sieci na mniejsze**
	- zmniejsza czas obliczeń ścieżki
	- oszczędza pamięć routera
- **kryteria wyboru ścieżki**
	- można wybrać ścieżkę według określonego kryterium lub kilku z poniższych
	- minimalny koszt
	- stopień pewności dotarcia pakietu do celu
	- maksymalna przepustowość ścieżki
	- minimalne opóźnienie
# Idea OSPF

- wykryj sąsiadów i nawiąż z nimi relację
	- protokół Hello
- wymień się z każdym informacjami na temat sieci
	- protokół Exchange
	- w wyniku czego każdy ma komplet danych o sieci
- niech każdy odpali algorytm Dijkstry, żeby obliczyć najlepsze ścieżki
- w przypadku zmiany topologi powiadom wszystkich sąsiadów (zalej ich)
	- protokół Flooding
# Protokoły wewnętrzne OSPF

- protokół Hello:
	- sprawdza, czy łącze funkcjonuje
	- wybiera reprezentanta z routerów w jednej sieci
- protokół Exchange:
	- synchronizacja map sieci
	- asymetryczny (master-slave)
		- na bazie RID, master inicjuje proces wymiany informacji
		- RID to numer routera, może być nadawany w różny sposób
- protokół Flooding:
	- powiadamianie o zmianach w topologii sieci
# Designated router

- problem: 
	- liczba wpisów w tablicach połączeń może rosnąć nawet kwadratowo w stosunku do liczby routerów
- idea: 
	- wyznaczyć specjalne designated routery i ich sąsiedzi będą widzieć tylko designated router jako sąsiada, więc będzie np. tylko 1 wpis zamiast 5
	- dla każdej domeny rozgłoszeniowej wybieramy **DR**, więc router może mieć kilka interfejsów **designated** (i podinterfejsów, jeżeli wchodzą w grę VLANy)
- designated router widzi wszystkie sąsiednie routery jako sąsiadów
- wybór następuje przy wymianie pakietów Hello
- wybiera się też backup designated router
- metryka:
	- od designated do normalnego - 0
	- od normalnego do designated - wynika z sieci
- jest to też sposób na zmniejszenie floodingu, informacje o zmianie topologii roześle dalej tylko designated router
# Wykrywanie sąsiadów

- każdy router wysyła ze wszystkich swoich interfejsów do swoich sąsiadów komunikaty Hello
- po otrzymaniu informacji odsyła się z powrotem potwierdzenie
- dzięki powyższemu routery mają pełne informacje o stanach łączy
# Pakiety Link State Advertisement (LSA)

- każdy router wysyła takie do swoich sąsiadów
- są to tablice topologii każdego routera, wysyłane do sąsiadów, a potem z ich pomocą do reszty routerów w systemie autonomicznym
- pozwalają routerom zbudować LSD (Link State Database), bo informują o stanie sieci (w tym możliwe, że odległych jej fragmentów względem danego routera)
- wpisy postaci:
	**(ID sąsiada (destination), sieć, metryka)**
- pakiety mają też numer identyfikujący ich wiek (więc można porównać pakiety nowszy vs starszy)
- odebranie nowego pakietu: 
	- router zapamiętuje jego informacje (aktualizuje swój LSD), a potem rozsyła pakiet na wszystkie interfejsy poza tym, z którego przyszedł (zalewanie sieci)
- odebranie starego pakietu:
	- router odrzuca pakiet
# Tablica Link State Database (LSD)

- zawiera mapę sieci
- każdy router ma taką, z ich pomocą tworzy się tablice routingu
- budowana z użyciem LSA - zawiera te informacje, co LSA + wiadomość, skąd przyszedł pakiet
- wpisy postaci:
	**(źródło, docelowy router, sieć, metryka)**
- dla każdego routera (czyli źródła) jest tyle wpisów, ile ma on kabli do innych routerów
- każdy wpis to pojedynczy krok, tzn. zawsze wpis dotyczy sąsiednich routerów (bo ma się z tego potem zbudować tablicę routingu)
# Tworzenie tablicy routingu w OSPF

- wykorzystuje algorytm Dijkstry (algorytm znajdowania najkrótszych ścieżek z wyróżnionym początkowym wierzchołkiem grafu)
- wykorzystuje informacje z LSD
- każdy router puszcza sobie algorytm Dijkstry z początkiem w sobie samym i w ten sposób znajduje najlepsze (najkrótsze) ścieżki do pozostałych routerów
## Algorytm Dijkstry w OSPF

- oznaczenia:
	- E - zbiór “gotowych” routerów (znamy najkrótszą ścieżkę)
	- R - zbiór pozostałych routerów (jeszcze nic o nich nie wiemy)
	- O - lista ścieżek (wpis to lista routerów w ścieżce wraz z sumarycznym kosztem ścieżki, czyli sumą metryk łącz w ścieżce)
- algorytm (teoretyczny, z wykładu):
	1.   E zawiera tylko router początkowy S
		R zawiera wszystkie pozostałe routery
		O zawiera wszystkie łącza wychodzące z S
	2. Algorytm przestaje działać w chwili, kiedy O jest puste lub metryka pierwszego łącza w O jest nieskończonością (wtedy reszta routerów z R jest nieosiągalna); tak długo, jak tak nie jest, powtarzamy poniższe kroki:
	3. Wybieramy najkrótszą ścieżkę z O, nazwijmy ją P. Usuwamy P z O. Na drugim końcu ścieżki P jest jakiś router V:
		a. Jeżeli V jest w E, to zignoruj (to pętla) i wróć do 3.
		b. W przeciwnym wypadku P to najkrótsza ścieżka do V: przenieś V z R do E i kontynuuj do 4.
	4. Robimy nowe ścieżki, tym razem od S aż do V (wykorzystując nasze nowe łącze P), w ten sposób sprawdzamy wszystkich sąsiadów V. Dodajemy do istniejących ścieżek koszt P i te nowe ścieżki wrzucamy do O, ale uwaga: jeżeli nowa ścieżka kończy się na routerze, który już jest w E, to nie ma sensu go dodawać, bo to pętla
- algorytm praktyczny (do zadań na kartce):
	1.   E zawiera router początkowy S
		O zawiera wszystkie łącza wychodzące z S
	2. Przestajemy działać, gdy wszystkie routery z sieci będą w E; inaczej w pętli:
	3. Wybieramy najkrótszą ścieżkę z O (usuwając ją). Dodajemy ścieżki przez ostatni router tej ścieżki do wszystkich jego sąsiadów, robiąc “jeszcze jeden krok”. Ten ostatni router dodajemy do E.
	4. Sprawdzamy O - jeżeli do danego routera prowadzi wiele ścieżek, to bierzemy tylko tę najlepszą, pozostałe usuwamy. Dodajemy ostatni router z tej
- złożoność: $O(nlog(n))$ (jeżeli jest dobrze zaimplementowane dodawanie ścieżki do listy)
- utrzymuje się 2 drzewa:
	- tymczasowe - te ścieżki, które jeszcze badamy (nie wiemy na 100%, że znamy idealną, najkrótszą drogę do nich)
	- ostateczne - ścieżki, do których znamy najkrótszą drogę (one zostają na koniec w drzewie)
- tablic LSD nie usuwa się - mogą się przydać po zmianie topologii

## Przykład tworzenia tablicy routingu

![[Pasted image 20250121032629.png|center]]

Mamy daną taką topologię sieci, jak powyżej (jest dla wygody też przeklejona poniżej,
przy każdej iteracji drzewa). Tablice LSD są identyczne dla wszystkich routerów - wszystko
jest gotowe, więc można zaczynać z algorytmem Dijkstry. Będziemy tworzyć tablicę
routingu dla routera R3. Zaczynamy od tworzenia drzewa z algorytmu Dijkstry.
Dla uproszczenia będziemy wypisywać tylko O i E (R można bezpiecznie pominąć, bo to po
prostu wszystkie routery, których nie ma w E), a ścieżki w O będziemy wypisywać
posortowane rosnąco po metrykach (jak w kolejce priorytetowej minimum).

O: {R3-R6: 8, R3-R5: 21, R3-R2: 33}
E: {R3}

![[Pasted image 20250121032702.png|center]]

Najkrótsza ścieżka to R3-R6 z kosztem 8. Dodajemy zatem R6 do E.
Usuwamy ścieżkę, którą właśnie wykorzystaliśmy i dodajemy te, które wychodzą z R6.
O: {R3-R6-R3: 16, R3-R5: 21, R3-R2: 33, R3-R6-R5: 25}
E: {R3, R6}

Bierzemy najkrótszą ścieżkę, czyli R3-R6-R3. Zauważamy, że ostatni router R3 jest już w
E, więc odrzucamy tę ścieżkę.
O: {R3-R5: 21, R3-R2: 33, R3-R6-R5: 25}
E: {R3, R6}

![[Pasted image 20250121032719.png|center]]

Zauważmy też, że mamy 2 ścieżki kończące się na R5, z czego R3-R6-R5 jest dłuższa, więc
ją odrzucamy.
O: {R3-R5: 21, R3-R2: 33}
E: {R3, R6}

![[Pasted image 20250121032742.png|center]]

Bierzemy znowu najkrótszą ścieżkę, czyli R3-R5. Router R5 ma sąsiadów R2, R3, R5 i R6,
więc dodajemy ścieżki przez R3-R5 do nich do O.
O: {R3-R5-R2: 32, R3-R5-R4: 32, R3-R2: 33, R3-R5-R6: 38, R3-R5-R3: 42}
E: {R3, R5, R6}

![[Pasted image 20250121032758.png|center]]

Widzimy, że R3-R5-R2 ma lepszą ścieżkę do R2 niż R3-R2, więc tę drugą trasę odrzucamy.
O: {R3-R5-R2: 32, R3-R5-R4: 32, R3-R5-R6: 38, R3-R5-R3: 42}
E: {R3, R5, R6}

Możemy odrzucić R3-R5-R6 i R3-R5-R3, bo mamy lepsze trasy do R3 i R6.
O: {R3-R5-R2: 32, R3-R5-R4: 32}
E: {R3, R5, R6}

![[Pasted image 20250121032818.png|center]]

Jako że mamy remis, to wszystko jedno, którą trasę weźmiemy najpierw, ale zacznijmy od
tej kończącej się w R2. Bierzemy więc R3-R5-R2 i dodajemy sąsiadów.
O: {R3-R5-R4: 32, R3-R5-R2-R5: 43, R3-R5-R2-R4: 44, R3-R5-R2-R1: 45, R3-R5-R2-R3: 65}
E: {R2, R3, R5, R6}

Możemy usunąć gorsze trasy od obecnych, które biegną do routerów w E, otrzymujemy:
O: {R3-R5-R4: 32, R3-R5-R2-R1: 45}
E: {R2, R3, R5, R6}

![[Pasted image 20250121032833.png|center]]

Bierzemy najlepszą trasę, czyli tę do R4. Dodajemy trasy do sąsiadów R4.
O: {R3-R5-R4-R5: 43, R3-R5-R4-R2: 44, R3-R5-R4-R1: 45, R3-R5-R2-R1: 45}
E: {R2, R3, R4, R5, R6}

Możemy usunąć gorsze trasy od obecnych, które biegną do routerów w E. Możemy też
usunąć jedną z tras, która prowadzi do R1, usuwamy tę młodszą (przez R4 do R1, ta przez
R2 jest starsza).
O: {R3-R5-R4-R1: 45}
E: {R2, R3, R4, R5, R6}

Bierzemy jedyną (a więc najlepszą) trasę prowadzącą do R1. Jako że jest to ostatni
router, to kończymy budowę drzewa.

![[Pasted image 20250121032857.png|center]]



# Problem synchronizacji tablic topologii
- opisy topologii w każdym routerze musi być identyczny
- zagrożenia:
	- zgubione pakiety
	- błędy pamięci
	- atak człowieka
- po podniesieniu łącza wymienia się pakiety database description

# Routing wielościeżkowy
- wiele ścieżek może prowadzić do tego samego celu
- zwiększa niezawodność (zapasowe ścieżki) oraz wydajność (load balancing)

# Dzielenie obciążenia
- ścieżki o zbliżonych metrykach mogą równocześnie służyć do przesyłania pakietów (zwykle wymaga się identycznych, ale jest często używany parametr variance dla umożliwienia “delty” dla podobnych metryk)
- nie gwarantuje dotarcia pakietów w kolejności wysyłania
- przyspiesza transfer
- problem: trzeba dokonać analizy grafu i rozwiązać problem maksymalnego przepływu, żeby zoptymalizować części ruchu na poszczególne ścieżki

# OSPF nie nadaje się do całego internetu naraz
- każdy router wymieniałby informacje z każdym
- obciążenie sieci
- olbrzymie tablice LSD
- długie obliczanie algorytmu Dijkstry
# Działanie OSPF z użyciem systemów autonomicznych
- instancja OSPF działa w obrębie danego systemu autonomicznego
- AS boundary router (ASBR):
	- router brzegowy komunikujący się z innymi AS
	- uzyskują informacje spoza AS
	- ogłasza rzeczy spoza AS wewnątrz AS pakietami external LSA
- problem: w bardzo dużym systemie autonomicznym zaczynają się pojawiać takie same problemy, co przy całym internecie
- idea: podzielić obszar autonomiczny na mniejsze obszary i wprowadzić dwupoziomową strukturę hierarchiczną, czyli tzw. hierarchiczny OSPF

# Hierarchiczny OSPF
- poziom pierwszy to same pojedyncze obszary, a drugi to tzw. backbone (obszar 0), czyli struktura sieci ogarniająca połączenie tych obszarów

![[Pasted image 20250121033312.png|center]]

- LSA są wysyłane tylko w obrębie danego obszaru, tam OSPF działa normalnie (każde area ma własną instancję OSPFa)
- area border routers znają dystanse wewnątrz własnego obszaru i ogłaszają “podsumowanie” swojego obszaru innym area border routers, tzw. summary LSA
- backbone routers to area border routers + te powyżej, utrzymują hierarchię, mają własną instancję OSPFa
- nie ma automatycznego tworzenia backupowego połączenia na wypadek padnięcia czegoś w backbone’ie

## Stub area
- taki obszar końcowy, który składa się tylko z jednego area border routera. Nie wysyła on ani external LSA, ani summary LSA.