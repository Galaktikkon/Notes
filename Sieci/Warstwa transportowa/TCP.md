
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
- wykorzystywane są pola z nagłówka TCP:
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
	- sockety lokalny i obcy, a więc dwie pary (adres IP, port)
	- aktualną wielkość okna
	- pierwszy niepotwierdzony bajt (początek “sent, but not yet acknowledged”)
	- próg powolnego startu (slow start threshold, ssthresh)
	- maksymalną wielkość segmentu (maximum segment size, MSS)
	- wskaźniki na bufory (nadawcze i odbiorcze, np. SND.NXT, RCV.NXT - opis niżej)
	- … sporo innych rzeczy

# Działanie TCP

![[Pasted image 20250122000248.png|center]]

## Używane flagi z nagłówka TCP

- SYN - synchronize, inicjalizuje i ustawia połączenie, np. synchronizuje numery sekwencji
- FIN - finish, kończy połączenie
- ACK - acknowledgement, potwierdza SYN, FIN lub cokolwiek innego
## Stany i przejścia

### CLOSED

- połączenia nie ma (jeszcze lub zostało zamknięte), przejścia:
	- passive Open: po stronie serwera, otwiera port TCP i inicjalizuje TCB (nie wie jeszcze, kto się z nim połączy, więc w części na socket klienta ma 0 i czeka na połączenie, wtedy robi bind); przechodzi do stanu LISTENING
	- active Open: po stronie klienta, otwiera port TCP, inicjalizuje TCB oraz inicjalizuje połączenie - wysyła SYN do serwera, żeby rozpocząć three-way handshake (patrz niżej)
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