
- połączeniowy
- każde połączenie ma dokładnie 2 końce (komunikacja zawsze point-to-point, np. brak multicastu)
- transmisja [[Przesyłanie informacji#Tryby Transmisji Danych|full duplex]] (jedna strona inicjuje transmisję, ale potem oba mogą się naraz komunikować)
- buforowanie w sposób niewidoczny dla aplikacji
- z perspektywy API wykorzystuje strumień danych (w przeciwieństwie do np. [[UDP]] czy [[Protokół IP|IP]], które wymagają otrzymania konkretnych datagramów)
- możliwości (zapewniają niezawodność):
	- nawiązywanie i zamykanie połączenia
	- potwierdzenia odbioru (positive acknowledgement)
	- retransmisje
	- kontrola przepływu danych
	- sumy kontrolne (nagłówek i dane)
	- porządkowanie kolejności segmentów
	- usuwanie zdublowanych segmentów
# TCP nie zapewnia

- API - to trzeba zapewnić programowo, np. socket API
- bezpieczeństwa - trzeba zapewnić osobnym protokołem, np. IPSec
- rozróżnienia wiadomości - TCP wysyła strumień informacji, nie dyskretne, podzielone wiadomości; aplikacje muszą same ogarniać, gdzie kończy się jeden komunikat i zaczyna drugi
- gwarancji komunikacji - TCP zapewnia wiele usług QoS (potwierdzenia odbioru, retransmisje, kontrolę przepływu danych etc.), ale nie daje gwarancji komunikacji, np. w żaden sposób nie naprawi problemów na warstwach niższych; jest to szczegół techniczny, ale protokół ten polega właśnie na “usilnie próbuję”, a nie “na pewno dasz radę dogadać się z innym hostem z moją pomocą”
# Numery sekwencyjne

- problem: TCP jest niezawodny, więc musi ogarniać, które dane są od kogo i do kogo (np. na potrzeby potwierdzeń i retransmisji); jednocześnie nie ma jak np. przydzielić ID do pakietu, bo traktuje dane jako strumień bajtów
- idea: przydzielić ID każdemu bajtowi, czyli numer sekwencyjny
- pozwala sprawdzać, czy każdy bajt został wysłany i potwierdzony; pozwala też pakować bajty z różnymi numerami sekwencyjnymi do jednego segmentu
- w praktyce:
	- używa się bloków bajtów (bo do wysyłanych segmentów pakuje się ciąg bajtów)
	- numer sekwencyjny = numer sekwencyjny pierwszego bajtu w segmencie
	- pierwotny numer sekwencyjny dla sesji jest ustalany na początku połączenia w wiadomościach SYN (synchronizacja numerów sekwencyjnych)
## Numery sekwencyjne w praktyce

- generalnie idea jest taka sama, jak opisana wcześniej, ale w praktyce pojawiają się pewne problemy, przez które nie można zawsze zaczynać numerów od 1 (wtedy w ogóle nie trzeba by ich synchronizować)
- problem:
	- weźmy połączenie urządzeń A i B, gdzie A wysyła komunikaty do B
	- A wysyła pierwsze 30 bajtów (od 1 do 30) do B
	- komunikat gdzieś się gubi, mija sporo czasu, połączenie TCP zostaje zerwane (np. użytkownik A stracił cierpliwość)
	- C nawiązuje nowe połączenie z B, niedługo zacznie wysyłać
	- zagubiony pakiet A->B w tej chwili dociera, B widzi, że zaczyna się od 1, więc myśli, że to pierwszy pakiet od C
- powyższe to tylko przykładowa sytuacja, może być wiele różnych błędów w praktyce
- rozwiązanie: losowy **initial sequence number (ISN)**, integer 32-bitowy
# Numery potwierdzenia

- problem: kiedy pakujemy dane ze strumienia do segmentu TCP, to chcemy potem potwierdzenia, że konkretny segment dotarł
- idea: przydzielić ID każdemu segmentowi, czyli numer potwierdzenia
- pozwala na asynchroniczne wysyłanie - nie musimy wysyłać segmentów po kolei, tylko można wysłać serię segmentów i otrzymać potwierdzenie dla każdego z nich, a dzięki temu numerowi wiadomo, który dokładnie segment jest w danej chwili potwierdzany
- w praktyce:
	- numer sekwencyjny jednocześnie identyfikuje dane i potwierdzenie
	- potwierdzenie używa numeru sekwencyjnego ostatniego bajtu z segmentu + 1
	- numer potwierdzenia = następny numer sekwencyjny, którego wysyłający wiadomość ACK (acknowledge) się spodziewa (zapewnia właściwą kolejność)

# Podział danych

- ze względu na obecność potwierdzeń, retransmisji i kontroli przepływu bajty można podzielić na kategorie - w praktyce realizują je wskaźniki w pamięci

![[Pasted image 20250108025259.png|center]]
## Kategoria 1

- **sent and acknowledged** - bajty które już są “obsłużone”, tzn. wysłano je i otrzymano potwierdzenie otrzymania; żadna strona nie musi się już nimi przejmować
## Kategoria 2

- **send but not yet acknowledges** - bajty które wysłano, ale nie otrzymano jeszcze potwierdzenia; jeżeli będą tu zbyt długo u nadawcy, to trzeba będzie retransmitować (bo minie timer na retransmisję)
## Kategoria 3

- **not sent, recipient ready to receive** - nadawca dzięki poprzedniej komunikacji wie, że odbiorca jest dość szybki, żeby natychmiast móc otrzymać te bajty; do wysłania ASAP
## Kategoria 4

- **not sent, recipient not ready to receive** - nadawca wie, że nie może ich wysłać, bo wtedy przeciążyłby odbiorcę (to jest właśnie kontrola przepływu); musi poczekać z wysyłaniem tego

# Okno (window)

- okno danych wysyłanych
- działa trochę jak skrzynka na listy do wysłania z ograniczoną pojemnością
- liczba bajtów, które mogą być “not acknowledged” - kategorie 2 i 3 powyżej
- usable window - liczba bajtów, które jeszcze w tej chwili można wysłać; sama kategoria 3 (not sent, but send ASAP)
![[Pasted image 20250108025611.png|center]]
- “zużywanie” usable window:
	- wysyłamy dane
	- [[#Kategoria 3|kategoria 3]] się zmniejsza
	- [[#Kategoria 2|kategoria 2]] rośnie
	- kiedy kategoria 2 zajmie 100% okna, to odbiorca jest teoretycznie “nasycony”danymi i w danej chwili nie możemy wysłać więcej (chociaż możliwe, że nic z tego nie dotarło, bo nie mamy potwierdzenia…)
# Mechanizm przesuwanego okna

- realizuje kontrolę przepływu (zmienna wielkość okna) i potwierdzenia z retransmisją (potwierdzenia, numery sekwencyjne i potwierdzeń)
- cumulative acknowledgement - uwzględniamy tylko potwierdzenia danych po kolei, np. jeżeli wysyłaliśmy bajty 32-51 w kilku częściach, a potwierdzenia przyszły dla 32-36 oraz 40-45, to “uznajemy” tylko 32-36, nie możemy mieć nieciągłości w potwierdzonych bajtach
![[Pasted image 20250108025800.png|center]]
- przesuwanie okna:
	- dane zostały wysłane, [[#Kategoria 2|kategoria 2]] wzrosła, [[#Kategoria 3|kategoria 3]] zmalała
	- otrzymujemy potwierdzenie otrzymania bajtów danych do X-tego (tzn. bajty bezpośrednio po tych, które już wcześniej były wysłane i potwierdzone)
	- przesuwamy lewy koniec okna o X bajtów w prawo, więc kategorie 2 i 3 rosną, a [[#Kategoria 4|kategoria 4]] maleje (prawy przesuwamy o nowy rozmiar okna - patrz niżej), [[#Kategoria 1|kategoria 1]] rośnie
- konsekwencje cumulative acknowledgement:
	- przykład: wysyłamy w segmentach bajty 32-36, 37-41 i 42-45
	- dostajemy potwierdzenie 32-36, przesuwamy okno
	- odbiorca nie otrzymuje 37-41 (segment zaginął), ale 42-45 otrzymał
	- odbiorca nie wysyła potwierdzenia dla jakiegokolwiek segmentu po 37-41, choćby otrzymał ich dużo (a pewnie dostanie, bo okno się przesunie na prawo, kategoria 3 u nadawcy wzrośnie i wyśle on kolejne dane) - musi dostać dane po kolei
	- jakakolwiek przerwa w prawidłowym potwierdzaniu = blokujemy potwierdzanie (musi być synchronicznie i po kolei)
	- segment bez potwierdzenia w końcu będzie miał timeout i nadawca zretransmituje te bajty, ale będzie musiał też retransmitować wszystkie późniejsze
- wykorzystywane są pola z [[#Budowa nagłówka TCP|nagłówka TCP]]:
	- SYN - flaga 0/1; dla pierwszych pakietów 1, bo synchronizuje się wtedy numery sekwencyjne, potem 0 (bo już są zsynchronizowane)
	- ACK - flaga 0/1; jeżeli jest ustawiona, to wiadomość stanowi potwierdzenie, potwierdza się otrzymanie bajtów aż do bajtu o numerze: numer potwierdzenia - 1
	- numer potwierdzenia - nadawca mówi “ok, to jest numer sekwencyjny następnego bajtu, którego się od ciebie spodziewam; wszystkie wcześniejsze otrzymałem”
## Przykład

![[Pasted image 20250121235748.png|center]]

Na dobry początek nawiązujemy połączenie, synchronizujemy numery sekwencyjne, numer
potwierdzenia oraz numer okna (**[[#Three-way handshake|three-way handshake]]**). Będziemy
wysyłać pakiety po 1024 B.

***Disclaimer***: screeny są z wykładu, a obrazki są idiotyczne. “ACK” na obrazkach to nie flaga
ACK, tylko “ACKnowledgement number”, numer potwierdzenia. Jeżeli jest ACK=1, to
znaczy, że nadawca nic wcześniej nie dostał od drugiej strony i mówi “ok, wysłałeś mi
wcześniej całe 0 bajtów, następny, który wyślesz, ma mieć numer sekwencyjny
pierwszego bajtu 1”.

![[Pasted image 20250121235818.png|center]]

Wysyłamy pierwsze 1024 B. Bajty są “[[#Kategoria 2|sent, but not yet acknowledged]]”.

![[Pasted image 20250121235833.png|center]]

Zużyliśmy do tej pory tylko 1024 z 4096 B okna, więc wysyłamy kolejne 1024 B. Nie
przesuwamy okna, dopóki nie dostaniemy potwierdzenia - póki co zmniejszamy więc dalej
[[#Kategoria 3|kategorię 3]], bo [[#Kategoria 2|kategoria 2]] “zjada” jej bajty.

![[Pasted image 20250121235848.png|center]]

Wysyłamy kolejne 1024 B. Co prawda okno się powoli kończy, ale to bez znaczenia -
wysyłamy, dopóki mamy w usable window jeszcze wolne miejsce.

![[Pasted image 20250121235906.png|center]]

W końcu otrzymaliśmy potwierdzenie - odbiorca wysyła numer potwierdzenia 2049, co
oznacza, że bajty do 2048 zostały odebrane (numer potwierdzenia = numer sekwencji + 1,
stąd ta różnica). Wysyła też wielkość okna - bez zmian 4096 B, czyli możemy spokojnie
dalej wysyłać. Lewy koniec okna przesuwa się do miejsca, gdzie dostaliśmy potwierdzenie.

![[Pasted image 20250121235922.png|center]]

Dostaliśmy potwierdzenie bajtów do 3072 włącznie… ale zmniejszono wysyłającemu okno -
odbiorca dostał dużo danych i chce, żeby kolejne były wysyłane wolniej, żeby zdążył
przetworzyć to, co już ma. Ale usable window dalej jest dość duże, więc wysyłamy. Okno
przesuwa się i zmienia rozmiar (prawy koniec przesunie się mniej, niż lewy).

![[Pasted image 20250121235942.png|center]]

Wypełniamy przesunięte już okno, wysyłając kolejny segment.

![[Pasted image 20250121235958.png|center]]

Dostajemy potwierdzenie dopiero co wysłanego segmentu, więc przesuwamy okno.

Będzie się to tak kontynuować, aż wysłana zostanie całość. Jeżeli potwierdzenie któregoś
fragmentu nie zostałoby otrzymane w czasie wyznaczonym przez timer (“pod spodem”
każdego wysłanego przez nadawcę segmentu siedzi zegar!), to nastąpiłaby retransmisja od
ostatniego miejsca, gdzie było potwierdzenie (żeby była ciągłość).

# Transmission Control Block (TCB)

- przechowuje dane o połączeniu
	- dzięki takim blokom może być wiele połączeń klientów z jednym serwerem, bo każde połączenie ma własne TCB
- tworzone na samym początku transmisji
- przechowuje:
	- [[Port#Socket (półasocjacja)|sockety]] lokalny i obcy, a więc dwie pary (adres IP, [[Port|port]])
	- aktualną wielkość [[#Okno (window)|okna]]
	- pierwszy niepotwierdzony bajt (początek “sent, but not yet acknowledged”)
	- próg powolnego startu (slow start threshold, ssthresh)
	- maksymalną wielkość segmentu (maximum segment size, MSS)
	- wskaźniki na bufory (nadawcze i odbiorcze, np. SND.NXT, RCV.NXT - opis niżej)
	- … sporo innych rzeczy

# Działanie TCP

![[Pasted image 20250122000248.png|center]]

## Używane flagi z nagłówka TCP

- **SYN** - synchronize, inicjalizuje i ustawia połączenie, np. synchronizuje numery sekwencji
- **FIN** - finish, kończy połączenie
- **ACK** - acknowledgement, potwierdza SYN, FIN lub cokolwiek innego
## Stany i przejścia

### CLOSED

- połączenia nie ma (jeszcze lub zostało zamknięte), przejścia:
	- passive Open: po stronie serwera, otwiera [[Port|port]] TCP i inicjalizuje [[#Transmission Control Block (TCB)|TCB]] (nie wie jeszcze, kto się z nim połączy, więc w części na socket klienta ma 0 i czeka na połączenie, wtedy robi bind); przechodzi do stanu LISTENING
	- active Open: po stronie klienta, otwiera port TCP, inicjalizuje TCB oraz inicjalizuje połączenie - wysyła SYN do serwera, żeby rozpocząć [[#Three-way handshake|three-way handshake]]
### LISTEN

- serwer nasłuchuje inicjalizacji przez klientów (wiadomości SYN):
	- otrzymanie SYN od klienta, wysłanie SYN+ACK: kiedy serwer otrzyma w końcu SYN od klienta (czyli zainicjuje on połączenie w stanie SYN-SENT), odsyła potwierdzenie z jego własnym SYN i potwierdzeniem ACK; przechodzi do stanu SYN-RECEIVED
### SYN-SENT

- klient wysyła SYN do serwera, inicjalizując połączenie:
	- otrzymanie SYN+ACK, wysłanie ACK: normalna sytuacja w której klient wysłał SYN i dostał od serwera potwierdzenie własnego SYN oraz informacje od serwera; przechodzi do stanu ESTABLISHED
	- otrzymanie SYN, wysłanie ACK: klient wysłał SYN, ale dostał SYN od kogoś innego, a nie SYN+ACK w odpowiedzi na własną wiadomość (bo można być klientem i serwerem jednocześnie!); potwierdza otrzymanie SYN i będzie dalej czekał na potwierdzenie SYN, które sam wysłał - przechodzi do stanu SYN-RECEIVED
### SYN-RECEIVED

- urządzenie dostało SYN i wysłało SYN (albo serwer dostał SYN od klienta i wysyłał SYN+ACK, albo klient wysłał SYN i dostał jakiś inny SYN - bez ACK):
	- otrzymanie ACK: urządzenie musi w końcu dostać ACK na ten SYN, które wysłało; kiedy w końcu dostanie, to przechodzi do ESTABLISHED
### ESTABLISHED

- normalny stan połączenia TCP, kiedy oba urządzenia się po prostu komunikują w tę i z powrotem:
	- chcemy zamknąć połączenie, wysyłamy FIN: ta aktywna strona, która chce się odłączyć wysyła wiadomość FIN i przechodzi do FIN-WAIT-1
	- ktoś chce zamknąć połączenie, otrzymujemy FIN: aktywna strona prosi bierną o zamknięcie; wysyłamy potwierdzenie otrzymania prośby ACK i przechodzimy do CLOSE-WAIT
### CLOSE-WAIT

- druga strona poprosiła o koniec połączenia, czekamy aż aplikacja się wyłączy:
	- aplikacja oznajmia, że jest gotowa do zamknięcia, wysyłamy FIN: jesteśmy gotowi na spełnienie prośby o zakończenie połączenia, wysyłamy FIN i przechodzimy do LAST-ACK
### LAST-ACK

- spełniliśmy prośbę o zamknięcie, czekamy na potwierdzenie:
	- otrzymanie ACK do FIN: druga strona potwierdza zamknięcie, przechodzimy do CLOSED
### FIN-WAIT-1

- wysłaliśmy FIN mówiąc, że chcemy zamknąć połączenie i czekamy na potwierdzenie otrzymania tej prośby:
	- otrzymanie ACK na nasze FIN: bierna strona potwierdziła, że dostała naszą prośbę; przejście do FIN-WAIT-2
	- otrzymanie FIN, odsyłamy ACK: nie dostaliśmy ACK naszej własnej prośby, ale z kolei drugie urządzenie samo poprosiło o zamknięcie połączenia (prośby są asynchroniczne, więc np. obie strony mogły wysłać naraz); odsyłamy ACK i przechodzimy do CLOSING
### FIN-WAIT-2

- druga strona potwierdziła naszą chęć zakończenia połączenia, czekamy, aż zamknie aplikację u siebie i odeśle nam FIN:
	- otrzymanie FIN, odesłanie ACK: druga strona wysłała FIN (czyli zamknęła u siebie aplikację), odsyłamy ACK z potwierdzeniem; przejście do TIME-WAIT
### CLOSING

- chcieliśmy zakończyć połączenie i wysłaliśmy FIN, ale zamiast ACK dostaliśmy FIN od drugiego urządzenia; potwierdziliśmy je ACK i czekamy na ACK naszego własnego FIN:
	- otrzymanie ACK na nasze własne FIN: obie strony wysłały teraz FIN i otrzymały ACK z powrotem, przechodzimy do TIME-WAIT
### TIME-WAIT

- na wszelki wypadek jeszcze chwilę czekamy, żeby np. nowe połączenie nie namieszało nam czegoś ze starym:
	- uruchamiany timer i czekamy - czeka się jakiś czas, jak zegar minie, to kończymy i przechodzimy do CLOSED
- przyczyny istnienia:
	- zapewnia, że ACK dotrze do drugiego urządzenia, a nawet w razie potrzeby zdąży się to zretransmitować i dotrzeć
	- segmenty z różnych połączeń nie pomieszają się (to taki bufor czasowy)
- standardowo wynosi 2 $\cdot$ maximum segment lifetime (MSL) = 4 minuty
- **MSL = 120 sekund (2 minuty)**
- często obniża się MSL, bo jest strasznie długie jak na dzisiejsze standardy

## Three-way handshake

- proces nawiązywania połączenia (connection establishment)
- droga od CLOSED do ESTABLISHED (górna część diagramu powyżej)
- normalne zachowanie - jedna strona robi passive Open, druga active Open
- funkcje:
	- nawiązanie kontaktu między serwerem a klientem - w szczególności serwer zwykle nie zna klienta, a klient zna serwer, więc teraz obaj się wzajemnie znają (i mogą uzupełnić pola TCB)
	- synchronizacja numerów sekwencyjnych - każde urządzenie daje znać drugiemu, jakiego numeru sekwencyjnego chce używać w pierwszej transmisji
	- ustalenie parametrów połączenia
- używane wiadomości:
	- SYN - z włączoną flagą SYN, oznacza, że segment jest używany do inicjalizacji połączenia, tzn. synchronizacji numerów sekwencyjnych
	- ACK - z włączoną flagą ACK, oznacza, że wiadomość stanowi potwierdzenie na wcześniej otrzymaną wiadomość (np. SYN)
	- SYN+ACK - wiadomość jednocześnie pełni funkcję SYN i ACK
- pełny algorytm:
	- SYN nadawca -> odbiorca
	- ACK odbiorca -> nadawca
	- SYN odbiorca -> nadawca
	- ACK nadawca -> odbiorca
- optymalizacja: wysłać środkowe ACK i SYN jako połączoną wiadomość, SYN+ACK, co daje 3 komunikaty - stąd nazwa, three-way handshake

### Przykład

Załóżmy, że klient i serwer chcą nawiązać połączenie:

| Krok                | Flagi   | Numer sekwencyjny (Seq) | Numer potwierdzenia (Ack) |
| ------------------- | ------- | ----------------------- | ------------------------- |
| **Klient → Serwer** | SYN     | Seq = 1000              | Ack = —                   |
| **Serwer → Klient** | SYN-ACK | Seq = 5000              | Ack = 1001                |
| **Klient → Serwer** | ACK     | Seq = 1001              | Ack = 5001                |

1. Klient zaczyna od ISN = 1000
2. Serwer odpowiada swoim ISN = 5000 i potwierdza ISN klienta.
3. Klient potwierdza ISN serwera, a połączenie zostaje nawiązane.

### Jednoczesne otwarcie

![[Pasted image 20250122002855.png|center]]

- sytuacja, kiedy dwóch klientów jednocześnie próbuje nawiązać ze sobą połączenie
- dość rzadka sytuacja, m. in. wymaga, żeby jeden z klientów używał dobrze znanego portu (numer < 1024)
- nie zachodzi three-way handshake, bardziej coś jak dwa jednoczesne two-way handshake
- każda strona wysyła SYN, otrzymuje SYN od drugiej strony, wysyła ACK drugiej stronie i oczekuje na własny ACK
- głównym warunkiem, żeby zaszła taka sytuacja jest to, żeby jedna strona wysyłała SYN i dostała SYN od drugiej strony jeszcze przed otrzymaniem ACK

### Inne parametry

- TCP może ustawiać całkiem sporo parametrów przy okazji three-way handshake poza synchronizacją numerów sekwencyjnych
- używają pola [[#Opcje|Options]] o zmiennej długości w [[#Budowa nagłówka TCP|nagłówku TCP]]
- przenoszone we wiadomościach SYN (więc 2 razy przy inicjalizacji)
- przykładowo:
	- **MSS (maximum segment size)** - odbiorca zapisuje sobie ten rozmiar w swoim [[#Transmission Control Block (TCB)|TCB]] i nigdy nie wyśle drugiej stronie segmentów większych niż ta wartość; jest to asymetryczne - klient i serwer mogą mieć różne MSS, bo np. klient przyjmuje tylko małe segmenty, a serwer nawet bardzo duże
	- **współczynnik skalowania okna** - pozwala na rozmiary okna większe niż pozwalałoby na to normalnie 16-bitowe pole “rozmiar okna” w nagłówku TCP; rozmiar okna jest mnożony przez to pole
	- **selective acknowledgement permitted** - czy można używać selective acknowledgement, tzn. retransmitować tylko zgubione pakiety (nieciągłość w potwierdzonych pakietach)
	- **alternatywa metoda liczenia sumy kontrolnej** - można ustawić customowy sposób liczenia sumy kontrolnej

# Połączenia półotwarte, resetowanie połączenia

- problem: 
	- całkiem prawdopodobna jest sytuacja, kiedy jedno urządzenie podczas sesji TCP jest [[#ESTABLISHED|ESTABLISHED]] cały czas (np. serwer, który pracuje cały czas), a drugie chwilowo stanie się [[#CLOSED|CLOSED]] (np. bluescreen w Windowsie u klienta, ale klient po chwili znowu się połączy)
- połączenie półotwarte (half-open) / półzamknięte (half-close) - taki stan połączenia TCP, gdzie strony się rozsynchronizowały, np. przez zcrashowanie się jednej ze stron; jedna ze stron zamknęła swój socket bez powiadamiania drugiej strony, przez co z jednej strony połączenie jest otwarte (ta strona, która jest nieświadoma), a z drugiej zamknięte
- do obsługi takich sytuacji używa się funkcji resetu, czyli wiadomości z ustawioną [[#RST|flagą RST]]
- wiadomość resetująca generowana jest przy napotkaniu nietypowej sytuacji, przykładowo:
	- otrzymanie segmentu TCP od urządzenia, z którym odbiorca nie ma połączenia (innego niż SYN do nawiązania połączenia)
	- otrzymanie wiadomości z błędnym numerem sekwencyjnym lub numerem potwierdzenia
		- wskazuje na to, że wiadomość może np. należeć do poprzednio istniejącego połączenia
	- otrzymanie wiadomości SYN na port, gdzie nie ma stanu [[#LISTEN|LISTEN]]
- urządzenie, które wykryje problem wysyła RST do urządzenia, od którego przyszła problematyczna wiadomość
- reakcja na otrzymanie resetu:
	- najpierw zawsze sprawdza się numer sekwencji (czy jest prawidłowy), np. żeby zapobiec atakowi i wymuszeniu resetu
	- ogólnie reakcja zależy od tego, w jakim stanie jest odbiorca
		- [[#LISTEN|LISTEN]] - odbiorca ignoruje reset
		- [[#SYN-RECEIVED|SYN-RECEIVED]], a wcześniej był w stanie LISTEN - wraca do stanu LISTEN (dość normalna sytuacja resetu serwera)
		- dowolna inna sytuacja - porzucenie połączenia, powrót do stanu [[#CLOSED|CLOSED]], przekazanie informacji o tym warstwie wyższej (żeby np. od razu znowu nawiązać połączenie)
# Keepalive

- wysyłanie okresowo wiadomości **keepalive** (tylko nagłówek, zero danych), na które powinno normalnie przyjść ACK
- **nie jest obowiązkowy**, nie jest częścią standardu
- elementy:
	- keepalive timer - odpala ten licznik, jak dojdzie do 0, to wysyła wiadomość keepalive; jest puszczany od nowa, jak przyjdzie ACK
	- keepalive interval - jeżeli ACK keepalive’a nie przyjdzie w tym czasie, to znowu wysyła keepalive (timer dalej jest 0)
	- keepalive retry - liczba retransmisji keepalive’a; jeżeli się zużyją, to zakładamy, że druga strona przestała istnieć
- zalety:
	- pozwala szybko wykryć problemy z połączeniem
	- oszczędność np. przy serwerach - pozwala eliminować bierne połączenia (bo np. klient się zcrashował i socket “wisi” na serwerze)
- wady:
	- potencjalnie niepotrzebne - nawet, jeżeli nie ma komunikacji przez długi czas i jest to przez błąd, to zostałoby to wykryte, kiedy komunikacja w końcu nastąpi (i naprawione przez reset)
	- generuje dodatkowy ruch na łączu
	- trzeba dodatkowo zrobić mechanizm obsługi takich segmentów
	- teoretycznie może wyłączyć dobre połączenie - może się zdarzyć tak, że normalnie wszystko z łączem jest ok, tylko urządzenia milczą, a akurat zepsuje się przy wiadomości keepalive i zostanie to uznane za ogólny problem z połączeniem (chociaż to mało prawdopodobna sytuacja)
# Zamykanie połączenia

- problem: 
	- oba urządzenia ciągle nadają i odbierają strumień bajtów, więc nie można tak po prostu zamknąć połączenia; trzeba przekazać drugiej stronie “koniec jest bliski”, żeby przestać nadawać, dokończyć odbieranie i zakończyć połączenie, bo każda strona zamyka swój koniec niezależnie
- rozwiązanie: 
	- dużo stanów i skomplikowany proces (patrz diagram), bardziej niż przy nawiązywaniu połączenia
- możliwe sytuacje:
	- jedna strona chce zamknąć połączenie - trzeba poinformować drugą stronę i najpierw ta druga strona kończy połączenie i o tym informuje, a dopiero potem samemu można zakończyć
	- jednoczesne zakończenie - coś jak jednoczesne włączanie, ale w drugą stronę
## Normalne zamykanie

- mamy 2 strony: nadawcę (on chce zakończyć połączenie) i odbiorcę (to jest ta bierna strona, która się dowiaduje o chęci zakończenia)
- przykładowo dla klienta-nadawcy i serwera-odbiorcy:

![[Pasted image 20250122004503.png|center]]

- 2 two-way handshake po kolei
- algorytm:
	1. FIN od klienta do serwera - klient chce zamknąć u siebie aplikację, wysyła informację serwerowi o chęci zakończenia; klient czeka w stanie [[#FIN-WAIT-1|FIN-WAIT-1]]
	2. ACK od serwera do klienta - serwer zaczyna zamykać aplikację, potwierdza klientowi; klient po otrzymaniu czeka w stanie [[#FIN-WAIT-2|FIN-WAIT-2]]
	3. Serwer czeka w stanie [[#CLOSE-WAIT|CLOSE-WAIT]], aż aplikacja raczy się zamknąć; w tym czasie serwer może jeszcze coś wysyłać do klienta i on to otrzyma, ale klient nic sam nie wyśle
	4. FIN od serwera do klienta - kiedy serwer w końcu zamknie aplikację, to wysyła klientowi wiadomość FIN i czeka w stanie [[#LAST-ACK|LAST-ACK]]
	5. ACK od klienta do serwera - to jest właśnie ten ostatni ACK z nazwy stanu serwera; serwer w końcu może zamknąć połączenie, klient też
	6. TIME-WAIT - klient czeka jeszcze trochę czasu, a potem przechodzi w [[#CLOSED|CLOSED]]
## Jednoczesne zamknięcie

- następuje, gdy urządzenie wyśle do drugiego FIN, a w międzyczasie to drugie wyśle FIN do pierwszego (żaden ACK nie zostanie wysłany ani nie zdąży dotrzeć w międzyczasie)

![[Pasted image 20250122004922.png|center]]

- proces jest de facto prostszy niż zwykłe zamykanie
- każdy wysyła FIN, każdy dostaje FIN drugiej strony i odsyła ACK

# Budowa nagłówka TCP

![[Pasted image 20250122005241.png|center]]

- 20-60 B (w zależności od użycia opcji)
## Numery portów

- źródłowy i docelowy - znane, bo to protokół połączeniowy
- port klienta - zwykle efemeryczny (duże numery)
- port serwera - dobrze znany (< 1024) lub zarezerwowany
- po 2 B każdy (bo jest 216 - 1 możliwości)
## Numer sekwencyjny

- numer sekwencyjny pierwszego bajtu danych w przesyłanym segmencie; w przypadku pierwszej wiadomości SYN jest to ISN źródła (a pierwszy bajt danych dostanie numer ISN + 1)
## Numer potwierdzenia

- następny spodziewany numer sekwencyjny, tzn. numer sekwencyjny pierwszego nieotrzymanego przez odbiorcę bajtu; to dzięki temu odbiorca wie, jakie dane właściwie otrzymał
## Długość nagłówka (data offset)

- 4 bity
- długość samego nagłówka TCP
- liczba 32-bitowych (4-bajtowych) słów w nagłówku
- ze względu na powyższe długość nagłówka TCP w bajtach musi być zawsze podzielna przez 4
- data offset, bo jednocześnie mówi, jak daleko od początku nagłówka przesunięty jest początek danych TCP
## Pole zarezerwowane

- 6 bitów
## Flagi

- 6 bitów (po 1 bit każda)
### URG

- dane są priorytetowe, używa się mechanizmu priorytetowego przesyłu danych i Urgent Pointera (patrz niżej)
### ACK

- pakiet stanowi potwierdzenie, pole numeru potwierdzenia zawiera następny numer sekwencji oczekiwany przez źródło
### PSH

- push, nadawca żąda usługi pushowania danych, czyli natychmiastowego wysłania do aplikacji u odbiorcy
### RST

- reset, nadawca chce zresetować połączenie, bo wykrył problem
### SYN

- wiadomość ma zsynchronizować numery sekwencyjne i nawiązać połączenie, pole numeru sekwencyjnego zawiera ISN nadawcy
### FIN
- finish, nadawca chce zakończyć połączenie

## Rozmiar okna

- 2 B
- wielkość okna w bajtach
- nadawca jest gotów otrzymać tyle danych od odbiorcy
- tej samej wielkości, co wolne miejsce w buforze odbiorcy dla danego połączenia
- maksymalny rozmiar okna:
	- bez skalowania: 216 - 1 B
	- ze skalowaniem: (216 - 1 ) * (216 - 1 B) ~ 232 B
## Suma kontrolna

- 2 B
- obliczana z nagłówka TCP oraz pseudonagłówka
- jeżeli nie zgadza się po przeliczeniu u celu, to segment jest odrzucany
## Urgent Pointer (wskaźnik ważności)

- 2 B, numer sekwencyjny ostatniego bajtu z pilnych danych
## Opcje

- składają się z rodzaju opcji, długości opcji (w bajtach) i danych opcji (zmienna długość)
- **koniec listy opcji** - nie musi być używana, jeżeli koniec opcji jest jednocześnie końcem nagłówka TCP
- **brak opcji** - 1-bajtowa “spacja” pomiędzy opcjami, używana, jeżeli poprzednia opcja nie kończyła się na wielokrotności 32 bitów (4 bajtów)
- **MSS (Maximum Segment Size)** - wielkość w bajtach największego segmentu, który wysyłające urządzenie może otrzymać; tylko w wiadomościach SYN
- **window scale** - zawiera jakąś liczbę x zapisaną binarnie; realna wielkość okna powinna wynosić: size = 2x * (wielkość z nagłówka); pozwala na wykorzystanie bardzo dużych okien (o ile oba urządzenia się na takie zgodzą), używane np. w łączach wysokiej wydajności
- **selective acknowledgement permitted** - pozwala na używanie SACK (selective acknowledgement)
- **SACK** - selective acknowledgement, wybiera (bez wymagania ciągłości!) bajty, które są potwierdzane i nie muszą być retransmitowane (więc mogą być “luki” danych do retransmisji)
- **alternatywny algorytm sumy kontrolnej, alternatywna suma kontrolna**

## Pseudonagłówek w TCP

![[Pasted image 20250122010124.png]]

- elementy:
	- adresy IP - normalnie
	- reserved - zerowy bajt
	- protocol - TCP, czyli zapisane binarnie 6
	- TCP length - długość segmentu TCP, nagłówek + dane (jest obliczane na potrzeby sumy kontrolnej)
- obliczany z powyższych elementów oraz samego nagłówka TCP
- przechowuje się go w buforze na czas obliczania sumy kontrolnej tuż przed nagłówkiem TCP, a potem usuwa
- podczas obliczania sumy kontrolnej pole “suma kontrolna” w nagłówku TCP ma długość 0 (żeby uniknąć nieskończonej rekurencji, gdzie sumę kontrolną liczymy z sumy kontrolnej)
### Powody używania pseudonagłówka

- chroni dodatkowo przed pewnymi problemami (w stosunku do sumy z samego nagłówka TCP), a przy tym nie wymaga przesyłania dodatkowych danych (wszystko dodatkowe i tak jest w nagłówku IP)
- **bierze informacje z warstwy IP**
- zalety - chroni przed:
	- **niepoprawna adresacja segmentu** - wykrywa różne adresy źródłowe i adresy docelowe
	- **niepoprawny protokół** - sam TCP zakłada, że to segment skierowany do niego; pseudonagłówek pozwala wykryć, że to np. jest skierowane do [[UDP]]
	- **niepoprawna długość segmentu** - pozwala wykryć zgubienie po drodze części segmentu TCP (sam TCP trzyma w nagłówku tylko długość nagłówka, nie całego segmentu)
- wady:
	- TCP w dzisiejszych sieciach ma bardzo dużą niezawodność i zysk z tych dodatkowych elementów jest dość niewielki
	- suma kontrolna dalej jest liczona w dość prymitywny sposób i jak na standardy algorytmów obliczania sum kontrolnych jest dość kiepska
	- narusza [[Modele warstwowe|architekturę warstwową modelu]] (używa [[Zadania warstwy sieci|warstwy 3]] w warstwie 4)

## Maximum Segment Size (MSS)

- zaletą strumienia bajtowego TCP jest to, że można wygodnie dostosowywać wielkość segmentów do połączenia
- aktualne górne ograniczenie na wielkość segmentu nakłada głównie wielkość okna
- **absolutne górne ograniczenie** (w bajtach), które nigdy nie może zostać przekroczone, wyznacza Maximum Segment Size (MSS)
- MSS wyznacza **maksymalną ilość danych**, która jest [[Komunikacja w modelu warstwowym#Przesyłanie danych|enkapsulowana]] w segmencie, nie uwzględnia nagłówka, więc de facto maksymalny segment to MSS + nagłówek TCP (20-60 B)
- asymetryczne - końce połączenia mogą mieć różne MSS
- wyznaczanie MSS jest problematyczne i stanowi kompromis między:
	- narzutem nagłówków - lepiej jest przesyłać więcej danych naraz, bo TCP narzuca 20-60 B nagłówkiem, [[Protokół IP|IP]] też 20-60 B, więc duże MSS jest wydajniejsze
	- potrzebą [[Protokół IP#Fragmentacja pakietów IP|fragmentacji]] IP - mniejsze MSS oznacza, że rzadziej trzeba fragmentować, bo łatwiej jest mieścić się w MTU łączy; fragmentacja = wolniej i więcej błędów
- minimalna wartość:
	- narzucono **minimalne MTU dla IP = 576 B**; wszystkie sieci IP mają obowiązkowo wspierać datagramy tej wielkości bez fragmentacji (wyznaczono to doświadczalnie)
	- od 576 B trzeba odjąć nagłówki TCP i IP (po 20 B)
	- **minimalne MSS: teoretycznie 1 B, praktycznie 536 B** (dla [[Adres IPv4|IPv4]])
	- **maksymalne MSS: teoretycznie $2^{16} - 1$ B, praktycznie tyle, ile udźwigną niższe warstwy** (dla [[Ethernet|Ethernetu]] 1460 B, bo do tego 20 B na TCP, 20 na IP i wychodzi pełne ethernetowe 1500)
	- **domyślne MSS: 536 B**
- zmiana MSS:
	- jeżeli chce się używać innego MSS niż domyślne, to używa się opcji MSS w nagłówku wiadomości SYN przy inicjalizacji połączenia
	- informuje się inne urządzenia “jak chcesz coś do mnie wysyłać, to maksymalnie tyle naraz”
	- inne urządzenia zapisują MSS drugiej strony w swoim TCB
- można wyznaczyć optymalne używając **MTU path discovery**