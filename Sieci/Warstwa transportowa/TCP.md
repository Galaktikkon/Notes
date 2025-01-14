
- połączeniowy
- każde połączenie ma dokładnie 2 końce (komunikacja zawsze point-to-point, np. brak multicastu)
- transmisja full duplex (jedna strona inicjuje transmisję, ale potem oba mogą się naraz komunikować)
- buforowanie w sposób niewidoczny dla aplikacji
- z perspektywy API wykorzystuje strumień danych (w przeciwieństwie do np. UDP czy IP, które wymagają otrzymania konkretnych datagramów)
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
- **sent and acknowledged** - bajty które już są “obsłużone”, tzn. wysłano je i otrzymano potwierdzenie otrzymania; żadna strona nie musi się już nimi przejmować
- **send but not yet acknowledges** - bajty które wysłano, ale nie otrzymano jeszcze potwierdzenia; jeżeli będą tu zbyt długo u nadawcy, to trzeba będzie retransmitować (bo minie timer na retransmisję)
- **not sent, recipient ready to receive** - nadawca dzięki poprzedniej komunikacji wie, że odbiorca jest dość szybki, żeby natychmiast móc otrzymać te bajty; do wysłania ASAP
- **not sent, recipient not ready to receive** - nadawca wie, że nie może ich wysłać, bo wtedy przeciążyłby odbiorcę (to jest właśnie kontrola przepływu); musi poczekać z wysyłaniem tego

# Okno (window)

- okno danych wysyłanych
- działa trochę jak skrzynka na listy do wysłania z ograniczoną pojemnością
- liczba bajtów, które mogą być “not acknowledged” - kategorie 2 i 3 powyżej
- usable window - liczba bajtów, które jeszcze w tej chwili można wysłać; sama kategoria 3 (not sent, but send ASAP)
![[Pasted image 20250108025611.png|center]]
- “zużywanie” usable window:
	- wysyłamy dane
	- kategoria 3 się zmniejsza
	- kategoria 2 rośnie
	- kiedy kategoria 2 zajmie 100% okna, to odbiorca jest teoretycznie “nasycony”danymi i w danej chwili nie możemy wysłać więcej (chociaż możliwe, że nic z tego nie dotarło, bo nie mamy potwierdzenia…)
# Mechanizm przesuwanego okna

- realizuje kontrolę przepływu (zmienna wielkość okna) i potwierdzenia z retransmisją (potwierdzenia, numery sekwencyjne i potwierdzeń)
- cumulative acknowledgement - uwzględniamy tylko potwierdzenia danych po kolei, np. jeżeli wysyłaliśmy bajty 32-51 w kilku częściach, a potwierdzenia przyszły dla 32-36 oraz 40-45, to “uznajemy” tylko 32-36, nie możemy mieć nieciągłości w potwierdzonych bajtach
![[Pasted image 20250108025800.png|center]]
- przesuwanie okna:
	- dane zostały wysłane, kategoria 2 wzrosła, kategoria 3 zmalała
	- otrzymujemy potwierdzenie otrzymania bajtów danych do X-tego (tzn. bajty bezpośrednio po tych, które już wcześniej były wysłane i potwierdzone)
	- przesuwamy lewy koniec okna o X bajtów w prawo, więc kategorie 2 i 3 rosną, a kategoria 4 maleje (prawy przesuwamy o nowy rozmiar okna - patrz niżej)
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