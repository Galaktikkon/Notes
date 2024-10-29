# Idea

- nie pozwala na istnienie cykli w sieci, ale zapewnia połączenia zapasowe, które mogą być aktywowane w przypadku awarii
- dzięki powyższemu gwarantuje jednoznaczność ścieżki między urządzeniami
- działa na poziomie logicznym, “nadpisując” warstwę fizyczną
- realizuje Spanning Tree Algorithm (STA)
- wykorzystuje przesyłanie co regularny czas (domyślnie co 2 sekundy) [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|ramek BPDU (Bridge Protocol Data Unit)]]
- używa specjalnego adresu [[Adresacja w sieciach LAN#Multicast|multicastowego]] 01:80:C2:00:00:00
## Zalety

- eliminuje ryzyko burzy broadcastowej
- zapewnia poprawne działanie sieci nawet w przypadku istnienia wielu fizycznych cykli
- przezroczyste dla innych urządzeń w sieci
## Wady

- nie pozwala na istnienie dodatkowych połączeń zwiększających wydajność (jedynie na ewentualne użycie połączeń jako zapasowych)
- zmniejsza [[Przesyłanie informacji#Throughput|przepustowość]] sieci
- blokuje komunikację na niektórych połączeniach, w ogóle ich nie używając (chyba, że staną się zapasowe; inaczej zmarnowaliśmy na nie pieniądze)
- wymaga regularnego badania sieci i w razie potrzeby przebudowywania drzewa - znaczący koszt wydajnościowy i czasowy (rzędu kilkudziesięciu sekund przy podłączaniu nowego [[Urządzenia warstwy łącza danych|switcha/mostka]]!)
- nakłada limit przełącznic - domyślnie w każdej gałęzi drzewa może być co najwyżej 7 switchy
# Ogólny pseudokod STA

- Każdy [[Urządzenia warstwy łącza danych#Bridge|mostek]] ma swój identyfikator (adres fizyczny) i ustalany administracyjnie priorytet
- Wyznaczany jest korzeń drzewa rozpinającego (**root bridge**) - zostaje nim mostek o najwyższym priorytecie
- Prawo do bycia korzeniem jest okresowo weryfikowane; nowo włączone mostki zakładają zawsze, że są korzeniem (chcą nim być).
- Każdy z mostków określa określa **root path cost** - najtańszą ze ścieżek wiodących od tego portu do korzenia. Port, który posiada najtańszą drogę to **root port**.
- Koszt wyznaczany jest w oparciu o tzw. **designated cost** - koszt przejścia przez dany segment sieci (wartość odwrotnie proporcjonalna do szybkości łącza).
- Dla każdego segmentu sieci wybierany jest jeden mostek - **designated bridge**, który będzie stanowił drogę dojścia do korzenia. Port tego mostka to **designated port**. (Wszystkie porty korzenia są **portami designated**)
- Wszystkie porty, które nie są ani **root** ani **designated** są zablokowane.
## Streszczenie kroków

1. Wyznacz [[Urządzenia warstwy łącza danych#Switch|switch]] będący korzeniem drzewa (root/root bridge/root switch)
2. Zbuduj drzewo połączeń do korzenia, wybierając “najlepsze” połączenia (o najmniejszym koszcie, minimalne drzewo rozpinające)
3. Usuń pozostałe połączenia poprzez ich zablokowanie (usuwa cykle)
# Rodzaje portów

- **Root Port** (**RP**) - prowadzi do korzenia; [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|ramki BPDU]] nie są na niego wysyłane
- **Designated Port** (**DP**) - port, który odbiera i przekazuje ramki (jak normalny port switcha), część drzewa rozpinającego; korzeń drzewa ma zwykle tylko takie
- **Blocked Port** (**BP**) - port, który odbiera tylko ramki **BPDU** i służy tylko do STP, “granica” drzewa rozpinającego
- połączenie może mieć na końcach DP-RP lub DP-BP; połączenie nie może być całkiem wyłączone z użycia (BP-BP) etc.
# Czas trwania ramek BPDU

- ramki są wysyłane co **Hello Time**; domyślnie to 2 sekundy
- po przetworzeniu ramek konfiguracja jest zapisywana na **Max Age**, a jeżeli przez ten czas nie dostanie się **[[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|BPDU]]**, to [[Urządzenia warstwy łącza danych#Switch|switch]] zakłada, że topologia sieci się zmieniła i teraz to on jest root’em; domyślnie to 20 sekund
- można ustawić też **Forward Delay**, czyli czas trwania stanów portów Listening i Learning; domyślnie to 15 sekund
- wszystkie powyższe informacje są zawarte też w ramkach BPDU
- W każdej gałęzi drzewa jest co najwyżej 7 switchy, żeby był czas przekazać ramki co **Hello Time**. 7 * 2 s (domyślny czas) = 14, więc więcej się nie zmieści. Jeżeli zmniejszy się lub wydłuży **Forward Delay,** to można odpowiednio zmienić **Hello Time** i liczbę switchy
# Działanie STA w praktyce

1. Oznacz każdy switch jako root z kosztem 0
2. W pętli co czas **Hello Time** korzeń wysyła [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|ramki BPDU]] do wszystkich sąsiadów
3. Po otrzymaniu **BPDU**:
	- Prześlij własne **BPDU** do sąsiadów (poza **Root Portem**!).
	- Czy otrzymany **Root ID** jest mniejszy od dotychczasowego? Jeżeli tak, to zmień root na **Root ID**.
	- Do otrzymanego **Root Path Cost** dodaj koszt ostatniego kabla (ostatni kabel nadawca-odbiorca ogarnia się po odbiorze, nie przy wysyłaniu).
	- Znajdź port o najniższym koszcie, rozstrzygając według: 
		- łączny koszt, **Bridge ID**, **Sender Port ID**, **Receiver Port ID**. 
		- Ten port to **Root Port**, ten naprzeciwko niego to **Designated Port**.
	- Na każdym z pozostałych portów wysłano **BPDU** i każdy odebrał **BPDU**. Te, na których wysłano “lepszą” ramkę (tzn. o niższym koszcie) oznaczamy jako **Designated Port**, a te naprzeciwko nich jako **Blocked Port**.
4. Jeżeli przez **Hello Time** nie otrzymano wiadomości od [[Urządzenia warstwy łącza danych#Switch|switcha]], to przebuduj drzewo.