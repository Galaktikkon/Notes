
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

## Implementacja okna przesuwnego u nadawcy

![[Pasted image 20250122012058.png|center]]

- dzielimy bajty na [[#Podział danych|4 kategorie]], tak jak opisano wcześniej
- obchodzą nas w praktyce tylko kategorie 2-4
- używa się zmiennych (SND od sender):
	- SND.UNA - “not acknowledged yet”, numer sekwencyjny pierwszego bajtu danych czekających na potwierdzenie, [[#Kategoria 1|kategoria 1]], działa jak wskaźnik
	- SND.WND - “window size”, liczba całkowita
	- SND.NXT - “next byte to send”, numer sekwencyjny pierwszego bajtu danych gotowych do wysłania, [[#Kategoria 3|kategoria 3]], działa jak wskaźnik
- długość [[#Kategoria 2|kategorii 2]] = SND.NXT - SND.UNA
- długość kategorii 3 = SND.UNA + SND.WND - SND.NXT
- usable window to wielkość kategorii 3
- na początku, przed komunikacją (ale po inicjalizacji) SND.UNA = SND.NXT
## Implementacja okna przesuwnego u odbiorcy

![[Pasted image 20250122012240.png|center]]

- kategorie [[#Kategoria 1|1]] i [[#Kategoria 2|2]] są połączone, bo odbiorca niczego nie musi potwierdzać - to są po prostu dane, które już otrzymał
- [[#Kategoria 3|kategoria 3]] to wielkość usable window u nadawcy - na tyle danych pozwolił odbiorca
- używa się zmiennych (RCV od receiver):
	- RCV.NXT - “next byte to receive”, numer sekwencyjny następnego bajtu, który odbiorca spodziewa się otrzymać, kategoria 3, działa jak wskaźnik
	- RCV.WND - “window size”, liczba całkowita
- początek [[#Kategoria 4|kategorii 4]] = RCV.NXT + RCV.WND

## Wartości wysyłane w wiadomościach a wartości wskaźników

- numer sekwencyjny nadawcy = SND.UNA (znany, bo wie, co wysyła)
- numer potwierdzenia odbiorcy RCV.NXT (znany, bo są synchronizowane podczas [[#Three-way handshake|three-way handshake]])
- rozmiar okna - nadawca otrzymał wcześniej od odbiorcy, a odbiorca wysyła to, co sam arbitralnie narzuca

## Optymalizacja liczby potwierdzeń

- problem: 
	- liczbę komunikatów w sieci trzeba oszczędzać, a przy potwierdzaniu każdego pakietu generowałoby to sporo ruchu
- idea: 
	- potwierdzać wiele segmentów naraz, bo numer potwierdzenia to i tak ostatni bajt w ciągłej serii (więc można połączyć segmenty i potwierdzić ostatni bajt)

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
### [[#Funkcja push|PSH]]

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
- **[[#Maximum Segment Size (MSS)|MSS (Maximum Segment Size)]]** - wielkość w bajtach największego segmentu, który wysyłające urządzenie może otrzymać; tylko w wiadomościach SYN
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
	- narusza [[Modele warstwowe|architekturę warstwową modelu]] (używa [[Zadania warstwy sieci|warstwy 3]] w [[Zadania warstwy transportowej|warstwie 4]])

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

## Funkcja push

- problem: 
	- przy TCP nie ma kontroli nad tym, kiedy dokładnie wyśle dane - optymalizuje ono przesył, kumulując je i wysyłając całe segmenty (zgodnie z mechanizmem przesuwnego okna), podczas gdy często przydałoby się wysyłać dane natychmiast, np. przy protokole telnet, zdalnym terminalu - głupio byłoby przesyłać wciśnięcia klawiszy serią, ale pojedyncze wciśnięcia przesyłane jak najszybciej już mają sens
- rozwiązanie: 
	- funkcja push (flaga PSH), która mówi TCP “wyślij te dane, które masz najszybciej, jak tylko się da”
- wykorzystanie: 
	- wrzucamy dane do wysłania do TCP i każemy mu zrobić push, wtedy nasze właśnie wrzucone dane zostaną szybko wysłane
- dane po dotarciu powinny być od razu przekazywane aplikacji (pushowane wyżej), ale nie zawsze jest to implementowane
- nie rozwiązuje to kwestii rozróżniania danych
	- w szczególności jeżeli jakieś dane siedziały w buforze do wysłania przed naszymi “natychmiastowymi” danymi, to one też zostaną zpushowane, a aplikacja-odbiorca sama musi sobie z tym radzić
- nie gwarantuje niczego co do segmentacji
	- dane pushowane mogą wylądować wszystkie w jednym segmencie, mogą podzielić się na wiele, pomieszać w jednym segmencie z tymi, na których nam nie zależało (patrz wyżej) - nie mamy żadnej gwarancji

## Urgent Function

- problem: 
	- skoro TCP widzi strumień, to nie rozróżnia priorytetów danych (traktuje je jednakowo), widzi tylko ich kolejność; nie zawsze można też użyć pushowania, żeby wysłać szybko dane, bo pushowane są wtedy wszystkie dane, które już są w buforze
- typowy use case: 
	- wysyłamy duży plik, orientujemy się, że wybraliśmy zły plik i chcemy anulować - anulowanie to wiadomość do wysłania przez TCP, push nic nie da (bo będzie chciał pushować bufor, w którym jest dużo danych); trzeba tylko wysłać szybko małą, priorytetową wiadomość “anuluj wysyłanie”
- rozwiązanie: 
	- flaga URG oraz Urgent Pointer; dzięki zastosowaniu wskaźnika na ostatni bajt priorytetowych danych można wrzucić pilną wiadomość do bufora, wskazać na jej koniec i zostanie ona wrzucona do pierwszego wysyłanego segmentu (choćby była na końcu kolejki) - poza nią oczywiście mogą być wysłane inne bajty
- dane urgent są poniekąd wysyłane “drugim kanałem”, poza głównym strumieniem transmisyjnym
- można połączyć flagi URG i PSH - wtedy pilne dane zostaną dodatkowo zpushowane (ale także reszta bufora!)

# Radzenie sobie z kłopotami w transmisji TCP
## Mechanizm retransmisji

- trzeba ogarniać, co dokładnie retransmitować i kiedy
- używa się kolejki retransmisji - kolejki zawierającej pary (kopia segmentu, pozostały czas w timerze), sortowanej rosnąco po pozostałym w timerze czasie
- kiedy czas się skończy, to TCP ściąga segment z kolejki (po to właśnie są tam ich kopie), wysyła go jeszcze raz i wrzuca znowu z odświeżonym timerem
- algorytm:
	1. Wysłanie segmentu - segment jest kopiowany i wrzucany do kolejki wraz z pełnym timerem
	2. Otrzymanie potwierdzenia - po otrzymaniu pełnego potwierdzenia (patrz niżej) dla segmentu jest on od razu usuwany z kolejki retransmisji
	3. Timeout - jeżeli ACK nie przyszedł i minął timer, to segment jest retransmitowany i wracamy do punktu 1
- ustawia się limit retransmisji, żeby nie retransmitować tego samego fragmentu w nieskończoność (domyślnie 5 dla Windowsów)
- problem: 
	- trzeba dobrać sensowną wartość timera retransmisji - co więcej, żeby była optymalna, musi być wyznaczana dynamicznie

## Pełne potwierdzenia

- problem: 
	- potwierdzenia muszą być ciągłe - co z tego, jak odbiorca nam potwierdzi odbiór bardzo wielu segmentów, jeżeli przed nimi wszystkimi jest jeden, którego nie potwierdził?
- idea: 
	- zdefiniować “fully acknowledged” - segment, który jest potwierdzony oraz znajduje się w ciągłej serii potwierdzonych pakietów
- rozumowanie:
	- mamy urządzenia A i B
	- A wysyła do B segment
	- B patrzy na numer potwierdzenia w otrzymanym segmencie - wszystkie bajty z numerem sekwencyjnym niższym niż ten numer potwierdzenia zostały otrzymane i potwierdzone przez A
- segment wysłany przez A do B będzie zatem uznany za potwierdzony, gdy wszystkie bajty tego segmentu będą miały niższe numery sekwencyjne niż ostatni numer potwierdzenia z B do A
- powyższe można sprawdzić, patrząc na ostatni numer sekwencji oraz długość pola danych (bo numer sekwencji to numer pierwszego bajtu! Po dodaniu długości otrzymujemy numer kolejnego bajtu, którego się spodziewamy - jak odejmiemy 1, to dostaniemy po prostu numer sekwencyjny ostatniego bajtu)

## Radzenie sobie z niepotwierdzonymi pakietami

- problem:
	- rozwiązanie z ciągłą serią potwierdzonych bajtów jest eleganckie (proste i działa), ale niewydajne - brak zaledwie jednego małego segmentu może potencjalnie powodować retransmisję bardzo wielu bardzo dużych segmentów, które są po nim, bo powoduje nieciągłość (a im dłuższy timer retransmisji u nadawcy, tym gorszy jest ten problem); można nawet w ten sposób zapełnić okno odbiorcy, a potem trzeba znowu wszystko wysyłać jeszcze raz
- inny problem: 
	- **nigdy nie wiadomo, co się dzieje z pakietami, dla których nie otrzymano z powrotem potwierdzenia** - mogły się zagubić, potwierdzenie mogło jeszcze nie dotrzeć, a może dotarły i przez jakiś błąd potwierdzenia nie wysłano - tego wysyłający nie wie i musi sobie radzić na ślepo
- możliwe podejścia:
	- **retransmisja tylko segmentów z timeoutem** - “optymistyczne”, liczymy na to, że dalsze segmenty doszły i tylko ten jest problematyczny (sytuacja opisana powyżej); jeżeli nie doszło dużo segmentów, to jest problem, bo będziemy czekać na timeout każdego po kolei i wtedy retransmitować
	- **retransmisja wszystkich dalszych segmentów po timeoucie** - “pesymistyczne”, zakładamy, że jak jeden segment zaliczył timeout, to też wszystkie wysłane po nim trzeba retransmitować; dobre, jeżeli faktycznie dużo segmentów nie dociera, bo zajmujemy się tym od razu, ale często można powodować w ten sposób niepotrzebne retransmisje
- wykorzystuje to też fakt, że segmenty niepotwierdzone (z “luką” w potwierdzeniach) nie są tak po prostu chamsko odrzucane od razu przez odbiorcę, tylko siedzą sobie trochę w buforze, bo nadawca może “dosłać” (szczególnie w wersji optymistycznej) brakujące segmenty

## Selective Acknowledgement (SACK)

- problem: 
	- opisany powyżej problem braku wiedzy wysyłającego i brak możliwości doboru optymalnego rozwiązania są po prostu niewydajne
- idea: 
	- potwierdzać konkretne zakresy bajtów (numerów sekwencyjnych), a nie potwierdzać “wszystkie bajty ciągle do numeru sekwencyjnego X”, czyli właśnie **selective acknowledgement (SACK)**
- na początek obydwa urządzenia muszą uzgodnić ze sobą używanie SACKa poprzez zaznaczenie flagi **SACK-Permitted** w wiadomościach SYN podczas inicjalizacji połączenia
- w praktyce SACK polega na tym, że w wiadomościach ACK wysyła się opcję SACK, która zawiera listę zakresów numerów sekwencyjnych, które się potwierdza
	- całą istotą i ulepszeniem tego rozwiązania jest to, że te zakresy z listy mogą być nieciągłe
- modyfikuje [[#Mechanizm retransmisji|kolejkę retransmisji]]
	- teraz trzyma się tam trójki (kopia segmentu, timer, flaga SACK); jeżeli flaga jest ustawiona, to dany segment był wysłany z opcją SACK
- używa się agresywnej (“pesymistycznej”) retransmisji wobec tych segmentów, które **nie używają SACKa** (nie mają ustawionej flagi), a przy segmentach SACK retransmituje się tylko je
- dzięki temu rozwiązaniu odbiorca może trzymać nieciągłe potwierdzone segmenty, bo te nieciągłe używają SACKa, a jednocześnie może trzymać spójny ciąg potwierdzonych bajtów (numerów sekwencyjnych) bez użycia SACK
- pozwala łatwo oddzielić tylko “potwierdzone” i “w pełni potwierdzone” bajty - SACK w gruncie rzeczy tyczy się tych “potwierdzonych”, czyli nieciągłych bajtów
- numer potwierdzenia w wiadomościach SACK to ostatni numer sekwencyjny w spójnym ciągu potwierdzonych bajtów, więc każdy **SACK to jednocześnie [[#ACK|ACK]]**

## Adaptacyjny timer retransmisji

- problem: 
	- trzeba dobrać odpowiednią wartość timera dla retransmisji segmentów, bo zbyt mały timer może doprowadzić nawet do retransmisji odebranych pakietów (ACK nie zdąży wrócić, a my już retransmitujemy), a zbyt duży oznacza marnowanie czasu, czekając na potwierdzenie, które nie dojdzie
- idea: 
	- ustawić retransmission timer na wartość trochę większą niż typowy [[Problemy w transmisji danych|RTT (Round-Trip Time)]], bo to czas, żeby wysłać wiadomość i żeby wróciła (trochę więcej, żeby odbiorca zdążył zrobić wiadomość ACK i ją wysłać)
- dlaczego to nie działa: 
	- nie istnieje typowy RTT - nie dość, że jest inny dla każdego połączenia, to jeszcze zmienia się w czasie w związku ze zmiennym obciążeniem sieci
- lepszy pomysł: 
	- dynamiczny/adaptacyjny schemat retransmisji, w którym TCP przybliża RTT między urządzeniami i zmienia go w czasie, opierając się na średnim delay’u
- staramy się jak najlepiej przybliżyć “przeciętny” RTT, odpowiednio zmniejszając lub zwiększając poprzedni obliczony RTT (oczywiście trzeba wybrać jakiś początkowy)
- matematycznie - wzór wygładzający (wygładzanie - zabezpiecza przed nadmiernymi wahaniami wartości):
$$\text{new\_RTT} = \alpha \cdot \text{old\_RTT} + (1 - \alpha) \cdot (\text{najnowsze zmierzone RTT)}$$
- $\alpha$ - współczynnik wygładzenia, między 0 a 1 - wyższe to mniejsze zmiany RTT, niższe to pozwolenie na większe wahania RTT; typowo $\alpha$ = 7/8 = 0.875
- wielkość timeoutu:
	- klasyczna: $$\text{timeout} = 2 \cdot \text{RTT}$$
	- algorytm Jacobsona $$\text{sample} = \text{najnowsze zmierzone RTT}$$$$D = \alpha \cdot \text{old\_D} + (1 - \alpha) \cdot |\text{RTT} - \text{sample}|$$$$\text{timeout} = \text{RTT} + 4 \cdot D$$
- algorytm Jacobsona jest lepszy, bo:
	- uwzględnia wariancję RTT
	- dla małych zmian RTT będzie małe D, więc old_RTT zdominuje liczenie nowego
	- dla dużych zmian RTT będzie większe D, a dodatkowo jest mnożone (zwykle przez 4 jak wyżej, można ustawić inaczej), więc to ono zdominuje liczenie nowego RTT

### Niepewność potwierdzenia

- idea: 
	- mierzymy RTT jako różnicę między wysłaniem segmentu a otrzymaniem ACK
- problem: 
	- jeżeli nastąpi retransmisja, to formalnie jest to ten sam segment - nie wiadomo, czy ACK przychodzi dla oryginalnego segmentu, czy dla retransmitowanego, więc nie wiemy, jaki czas początkowy wybrać
- problem jest nietrywialny - jeżeli wybierzemy zawsze wcześniejszy czas, to możemy mieć zbyt długi RTT (bo retransmisja może być dość późno), a jeżeli zawsze późniejszy, to zbyt krótki RTT

### Opóźnione potwierdzenie

- problem: 
	- można otrzymać wiele pakietów w prawie tym samym czasie, a potem trzeba by na nie wszystkie odpowiadać ACK
- idea: 
	- poczekać chwilę, a potem odpowiedzieć jednym ACK - w końcu i tak wiemy, gdzie kończy się ciągły strumień potwierdzonych bajtów
- zgodnie z RFC 1122 host może poczekać do 500ms z wysyłaniem ACK
- szczególnie przydatne dla danych interaktywnych, np. w protokole Telnet, gdzie ciągle wysyła się małe segmenty - ilość ACK może spaść 3x
- przy okazji wysyłania potwierdzenia można też samemu wysłać dane

### Algorytm Karna

- ulepszenie sposobu mierzenia RTT
- idea: 
	- oddzielić od siebie liczenie średniego RTT i liczenie wartości dla retransmission timerów
- algorytm dotyczy zatem nie RTT, a liczenia timerów retransmisji
- łączy się z algorytmami obliczania RTT - algorytm Karna wybiera segmenty, z których liczy się RTT, ale nie dotyczy samego sposobu liczenia średniego RTT
- ważna zmiana: 
	- nie używamy RTT retransmitowanych fragmentów w obliczaniu średniego RTT (w ogóle olewamy wszystkie segmenty, które mogłyby powodować niepewność potwierdzenia)
- używa timer backoff
	- przy retransmisji nie ustawiamy tego samego timera co wcześniej, tylko robimy backoff - mnożymy go przez β (zwykle β=2); ustala się też wartość maksymalną, żeby nie zwiększać w nieskończoność
- algorytm:
	1. Wysyłamy segment, timer = aktualny średni RTT (albo minimalnie więcej)
	2. Zakładamy, że segment nie dotarł, timer retransmisji spadł do 0.
	3. Retransmitujemy segment i ustawiamy:$$\text{new\_timer} = β * \text{old\_timer}$$
	4. Powtarzamy punkt 3 do skutku (lub przekroczenia maksymalnego czasu przez timer)
	5. Kiedy retransmisja się uda, to RTT pozostaje ustawione na tej powiększonej przez backoff wartości - zostanie przywrócone do poprzedniego, “normalnego” RTT, kiedy uda się wysłać segment bez potrzeby retransmisji
- konsekwencje:
	- retransmisje mają więcej czasu, żeby dotrzeć, więc mniejsza szansa na serie niepotrzebnych retransmisji
	- zmniejsza potencjalne zalewanie sieci niepotrzebnymi wiadomościami (związane z tym powyżej)
	- dostosowanie do zmiennych warunków w sieci - jeżeli akurat przez jakiś czas sieć ma niższą wydajność (np. godziny szczytu) i jest dużo retransmisji, to przez ten czas segmenty będą miały ustawiony wysoki RTT, bo nic nie “wyzeruje” backoffu i RTT pozostanie na wysokiej wartości; wróci natomiast do normalnego stanu, kiedy obciążenie spadnie do normalnych wartości i segmenty będą docierały bez retransmisji

## Okna a kontrola przepływu

- mechanizm zmiennej wielkości okna realizuje kontrolę przepływu, bo reguluje, ile danych można w danej chwili wysłać
- klient i serwer mają po 2 okna każdy
	1. receive window - tyle danych host jest gotów przyjąć; tę wielkość przesyła drugiemu i może ją sobie zmieniać, jak chce
	2. send window - tyle danych host może wysłać; tę wielkość otrzymał od drugiego i jest ona mu narzucona
- okno to mniej więcej wielkość bufora, którą ma dana strona komunikacji
- kiedy np. serwer otrzymuje dane od klienta, musi zrobić 3 rzeczy:
	1. wrzucić dane do bufora - a więc zmniejszyć wolne miejsce w receive window
	2. potwierdzić - żeby druga strona wiedziała, że dane dotarły i są w buforze
	3. przetworzyć - przesłać dane z bufora do warstwy wyższej
- w oknie przesuwnym dane są potwierdzane szybko (często jak tylko dotrą), ale niekoniecznie są od razu przetwarzane - a więc bufor może się zapełnić
- kiedy bufor się zapełnia, to musimy powiadomić drugą stronę, żeby wysyłała wolniej - robi się to przez zmniejszenie okna, bo wtedy szybciej zapcha się drugiemu bufor wyjściowy
- zmniejszanie okna:
	1. Załóżmy sytuację: klient wysyła dane do serwera, ale serwer ma mnóstwo połączeń od klientów i wolno przetwarza dane
	2. Klient ma do wysłania sporo danych, na dobry początek wysyła 140 B; serwer ma okno 360 B, dostaje te 140 B, stwierdza że ten klient musi zwolnić i odsyła mu potwierdzenie z numerem potwierdzenia np. 141 (zakładając, że liczyliśmy od 1) i wielkością okna 260 B - jednocześnie zmniejsza własne okno odbierania do 260
	3. Klient przesuwa lewy koniec okna o 140 bajtów (bo tyle danych wysłał i mu potwierdzono), ale prawy koniec tylko o 260 (a nie 360), efektywnie zmniejszając okno wysyłania
	4. Klient wysyła 180 B; serwer znowu uznaje, że to za dużo i zmniejsza okno do do 80; zmniejsza własne okno odbierania oraz wysyła takie okno klientowi (wraz z numerem potwierdzenia 321, bo 140 + 180 + 1 = 321)
	5. Klient przesuwa lewy koniec okna o 180 B, ale prawy tylko o 80 (teraz okno = 80 B)
	6. Klient wysyła serwerowi 80 B danych; serwer ma dość i zmniejsza okno do 0 B i wysyła do klientowi
	7. Klient nie ma opcji, tylko musi przestać wysyłać dane do serwera, bo teraz jego SND.WND = 0; przy okazji SND.UNA = SND.NXT, bo oba są na początku tego zerowego okna
### Zamykanie okna

- zmniejszanie rozmiaru okna do 0 B (czyli nadawca ma się zamknąć i przestać wysyłać).

### Problemy związane z mechanizmem przesuwanego okna

- kurczenie się bufora okna (shrinking the window)
- otwieranie zamkniętego okna
- syndrom głupiego okna

#### Kurczenie się bufora okna

- uwaga: 
	- zmniejszanie okna to **nie jest to samo** co kurczenie się (bufora) okna! (reducing window (size) vs shrinking the window)
- zmniejszanie okna jest normalnym elementem kontroli przepływu - używa się go wtedy, kiedy chcemy, żeby druga strona wolniej wysyłała informacje
- problem: 
	- gdy jednej stronie, np. serwerowi krytycznie brakuje pamięci, może chcieć zmniejszyć sam bufor, na którym operuje okno
- działałoby to tak, że serwer mówiłby klientowi “zmniejsz swoje okno wysyłania, przesuwając jego prawy koniec w tej chwili na lewo” - jest to absolutnie inna sytuacja niż zmniejszanie okna, gdzie serwer mówi “przesuń okno na prawo, ale jego prawy koniec trochę mniej”
- problem: 
	- klient może wysyłać dane, korzystając z poprzedniego znanego rozmiaru bufora, podczas gdy w tej chwili serwerowi każe się zmniejszyć jego rozmiar bufora; te dane więc dojdą, ale nie zmieszczą się w buforze - byłyby stracone (albo trzeba by je jakoś dzielić)
- rozwiązanie: 
	- nigdy nie wolno zmniejszać rozmiaru bufora (kurczyć okna)
- zmniejszanie bufora można zrobić, ale wolniej i w kilku krokach:
	1. Serwer otrzymuje od aplikacji żądanie zmniejszenia bufora
	2. Serwer “zamraża” klienta, zmniejszając mu okno do zera (zamyka okno), ale jeszcze nic nie robi ze swoim buforem
	3. Serwer czeka, aż zwolni mu się miejsce w buforze, a wszystkie ewentualne wysłane wcześniej przez klienta segmenty dotarły
	4. Serwer zmniejsza bufor do żądanej wielkości
	5. W końcu okno się otworzy (patrz niżej), wtedy powraca komunikacja
#### Zarządzanie zamkniętym oknem

- kiedy okno jest zamknięte, a serwer w końcu może je otworzyć, to wyśle segment (typowo bez danych) z niezerowym rozmiarem okna do klienta (“otwierając” okno)
- problem: 
	- segment może zaginąć, nawet wiele segmentów, połączenie może się w końcu skończyć (bo klient uzna, że serwer padł)
- idea: 
	- próbkowanie, klient regularnie wysyła pakiety bez danych z zapytaniem o okno
- używa persistence timer’a (kiedy spadnie do 0, to wysyła zapytanie)
- dane z [[#URG|flagą URG]] są wysyłane nawet przy zamkniętym oknie

#### Syndrom głupiego okna

- przypomnienie: 
	- chcemy mieć kompromis w kwestii wielkości segmentów - większe są wydajniejsze, bo mniej rzeczy musi iść przez sieć, a mniejszych nie trzeba dzielić na poziomie [[Protokół IP|IP]]
- [[#Maximum Segment Size (MSS)|MSS]] załatwia górną granicę wielkości pakietu
- problem: 
	- okno przesuwne nigdzie nie ma minimalnej wielkości segmentu - co więcej, przy dużym obciążeniu właśnie działa tak, że mamy małe okno i przesyłane dużo małych segmentów
- “głupie okno” to takie, które ciągle otwiera się troszkę, wysyłany jest mały (niewydajny) segment i znowu się zamyka i tak w kółko - niewydajny, duży narzut, nie chcemy tego
- przykład:
	1. Serwer jest mocno obciążony i powoli przetwarza dane, zamknął okno klientowi. Jest tak obciążony, że był w stanie wziąć z bufora tylko 40 B do przetworzenia.
	2. Klient wysyła probe’a do serwera z zapytaniem o otwarcie okna; serwer ma wolne miejsce w buforze, więc robi małe okno 40 B
	3. Klient wysyła mały segment 40 B do serwera
	4. Serwer ma pełny bufor i zamyka okno… i tak w kółko
- w najgorszym wypadku będą 1-bajtowe okna - nie jest to per se “błąd” przesuwnego okna, ale jest potwornie niewydajne (narzut samego [[#Budowa nagłówka TCP|nagłówka TCP]] to wtedy co najmniej 20x więcej niż dane!)
- przyczyna: 
	- ogłaszanie dowolnie małych rozmiarów okien oraz wysyłanie w odpowiedzi dowolnie małych segmentów
- stosuje się metody unikania syndromu głupiego okna (nie eliminacji, tylko unikania!), zarówno po stronie odbiorcy, jak i wysyłającego
- odbiorca: 
	- rozwiązanie Clarka
- nadawca: 
	- opóźnione potwierdzenie, algorytm Nagle’a

##### Rozwiązanie Clarka

- błąd po stronie odbiorcy jest taki, że ciągle zmniejsza swoje okno, chcąc od nadawcy wysyłania coraz mniejszych pakietów
- rozwiązanie Clarka: 
	- zabrania się ogłaszania zbyt małego okna - narzucamy, o ile minimalnie ma się przesunąć prawy koniec okna
- zwykle bierze się: $$\text{min(MSS odbiorcy}, 0.5 \cdot \text{rozmiar bufora odbiorcy})$$
- w praktyce: 
	- jak nadawca przyśle nam tyle, że mamy mało miejsca, to nie zmniejszamy naszego okna w odpowiedzi, tylko całkiem je zamykamy, przetwarzamy dane i wysyłamy okno dopiero, jak będziemy w stanie przyjąć wartość minimalną
- przykładowo:
	1. Serwer ma okno 360 B, dostaje 360 B od klienta, może przetworzyć tylko 120 B
	2. Serwer nie ogłasza okna 120 B! Zamiast tego wysyła okno 0 B, zamykając je
	3. Klient siedzi cicho, co najwyżej próbkując serwer (i dostaje odpowiedzi “okno dalej jest 0 B, siedź cicho”)
	4. Serwer przetwarza dane, aż będzie mógł przetworzyć 180 B (połowę bufora, zakładamy, że to jest minimum); kiedy to się stanie, to ogłasza okno 180 B klientowi

##### Algorytm Nagle’a

- agreguje dane w większe segmenty, jeżeli tylko jest taka możliwość
- algorytm:
```
if (są nowe dane do wysłania):
	if (są dane czekające na wysłanie && danych jest >= MSS):
		wyślij segment MSS danych
		resztę danych wrzuć do bufora
	else:
		if (są dane wysłane i czekające na potwierdzenie):
			wrzuć nowe dane do bufora
		else:
			wyślij natychmiast
```
- jeżeli aplikacja generuje dużo małych danych, to raczej nie będzie danych czekających na potwierdzenie i wykona się szybko
- jeżeli aplikacja generuje duże dane, to pewnie i tak uzbiera segmenty wielkości MSS
- uwaga: 
	- push nie zadziała tak, jakbyśmy się tego spodziewali - dane pushowane są w algorytmie Nagle’a traktowane tak samo, muszą poczekać na potwierdzenie danych lub pełny segment
- najlepiej działa w przypadku sieci WAN, z długim RTT - wtedy czeka się dość długo na potwierdzenie i przez ten czas algorytm zdąży sporo nagromadzić danych i je efektywnie wysłać jako jeden segment

##### Rozwiązanie Clarka + algorytm Nagle’a - nadawca transmituje dane w 3 sytuacjach

- segment wielkości MSS
- segment wielkości co najmniej 0.5 * rozmiar bufora odbiorcy
- dowolną ilość danych, o ile nie ma niczego niepotwierdzonego

## Kwestia obciążenia sieci

- TCP nie może być całkiem ślepe na to, jak niższe warstwy (w szczególności 3) są obciążone - w końcu jeżeli routery są przeciążone i zaczynają odrzucać pakiety (bo mają pełne bufory), to będą timeouty i TCP będzie musiało retransmitować
- TCP ma mechanizmy, które realizują obsługę przeciążenia (congestion handling)
- mechanizmy:
	- powolny start (slow start)
	- unikanie przeciążenia (congestion avoidance)

### Powolny start

- problem: 
	- tuż po inicjalizacji połączenia bufory odbiorcze są puste, okna duże - można wysyłać szybko i ile wlezie… co może łatwo zapchać sieć, bufory routerów i odrzucić te segmenty
- idea: 
	- okno przeciążenia (congestion window, cnwd), które po stronie nadawcy określa, ile segmentów będzie wysyłał; jedno cnwd jest równe MSS (więc np. cnwd=3 to 3 * MSS)
- analogia:
	- wielkość okna - kontrola przepływu u odbiorcy, “chcę dostawać max tyle danych”
	- powolny start - kontrola przepływu u nadawcy, “będę wysyłał max tyle danych”
- algorytm:
	1. Inicjalizuj cwnd, zwykle cwnd = 1, ale też 4 czy 10
	2. Otrzymanie ACK -> zwiększ cwnd o 1
	3. Powtarzaj w pętli 1-2, aż wykryjesz timeout (= przeciążenie)
	4. Przy timeoucie lub osiągnięciu ssthresh:
		- wartość cwnd, która spowodowała przeciążenie, jest zapisywana w [[#Transmission Control Block (TCB)|TCB]] jako **ssthresh (Slow Start Threshold)** - tylko dla timeoutu (dla ssthresh punkt 7)
		- zmniejsz cwnd 2 razy (do min. 1) - tylko dla timeoutu
	5. Otrzymanie ACK -> zwiększ cwnd o 1
	6. Powtarzaj 5, aż dojdziesz do ssthresh
	7. Zwiększ cwnd o 1 dopiero wtedy, gdy potwierdzisz wszystkie segmenty w oknie
- mamy teraz 2 okna:
	- sending window - otrzymane od drugiej strony, okno przesuwne
	- congestion window - obliczone powyższym algorytmem
- liczba przesyłanych bajtów: $$\text{min(sending\_window, congestion\_window)}$$
- powolny start jest uaktywniany po każdym timeoutcie