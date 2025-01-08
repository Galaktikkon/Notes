
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