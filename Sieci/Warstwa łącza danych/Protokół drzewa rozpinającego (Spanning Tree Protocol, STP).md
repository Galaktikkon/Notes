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
# Porty w STP

## Rodzaje portów

- **Root Port** (**RP**) - prowadzi do korzenia; [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|ramki BPDU]] nie są na niego wysyłane
- **Designated Port** (**DP**) - port, który odbiera i przekazuje ramki (jak normalny port switcha), część drzewa rozpinającego; korzeń drzewa ma zwykle tylko takie
- **Blocked Port** (**BP**) - port, który odbiera tylko ramki **BPDU** i służy tylko do STP, “granica” drzewa rozpinającego
- połączenie może mieć na końcach DP-RP lub DP-BP; połączenie nie może być całkiem wyłączone z użycia (BP-BP) etc.

## Stany portów w STP

- **Disabled**:
	- port wyłączony z użytkowania
	- wynik awarii, ustawienia administratora etc.
- **Blocking**:
	- domyślny stan portu nowych urządzeń w sieci oraz tych, które zostały wyznaczone przez **STP** jako **BP**
	- tutaj odbywa się wybór root [[Urządzenia warstwy łącza danych#Switch|switcha]]
	- przetwarza tylko otrzymane **BPDU**, ignoruje resztę ruchu
	- nie jest mu przekazywany ruch z innych portów
	- jeżeli przez 20 sekund nie dostanie wiadomości, że ma pozostać w **Blocking**, to przechodzi w **Listening**
- **Listening**:
	- kandydat na **Root Port** lub **Designed Port** (wiadomo już, kto jest root switchem)
	- trwa 15 sekund
	- nasłuchuje tylko [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|BPDU]], które mogą go zmienić w Blocking; jak nie dostanie przez 15 sekund, to staje się **Learning**
- **Learning**:
	- przygotowuje się do przekazywania ruchu
	- trwa 15 sekund
	- wpisuje do tablicy forwardingu [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adresy MAC]] nadawców (“uczy się” tablicy MACów)
	- poza powyższym ignoruje ruch
- **Forwarding**:
	- normalna praca portu, tak, jakby nie było STP

- **Disabled** to de facto nie stan portu, to po prostu oznaczenie jego wyłączenia
- **Blocking** i **Forwarding** są stałymi trybami pracy portu
- **Listening** i **Learning** to stany przejściowe, mają na celu rozpropagowanie informacji i uniknięcie istnienia tymczasowych **cykli**
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
# Zasady konfiguracji STP

- prawidłowe ustawienie priorytetów:
	- korzeniem powinno być urządzenie z największą ilością ruchu (czyli “na środku” grafu), z podłączonymi kablami o wysokiej przepustowości
	- jeżeli rootem zostanie [[Urządzenia warstwy łącza danych#Switch|switch]] “na brzegu” grafu, to żeby dojść do przeciwległego końca drzewa ruch będzie musiał iść przez całą sieć
- zróżnicowanie priorytetów:
	- zabezpieczenie w przypadku awarii
	- jak korzeń padnie przez awarię, to priorytety decydują o przejmowaniu roli korzenia; powinny być układane w kolejności zgodnie z zasadą z punktu powyżej
- jak nie trzeba **STP**, to nie używajmy **STP**:
	- protokół STP zmniejsza wydajność sieci, wymaga wysyłania dużej ilości [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|ramek BPDU]]
	- jeżeli mamy sieć bez redundantnych połączeń (= drzewo rozpinające już dzięki kablom), to nie ma sensu włączać **STP**
	- wymaga upewnienia się, że porty końcowe (tam, gdzie podłączamy komputery) są zabezpieczone przed utworzeniem cykli
## Przykład

W tej sieci, błędnie zrobione **STP** obniży wydajność.

![[Pasted image 20241029214554.png|center]]

Środkowe 2 [[Urządzenia warstwy łącza danych#Switch|switche]] prawdopodobnie będą najbardziej obciążone, więc to jeden z nich powinien być korzeniem. Jeżeli natomiast priorytety nie zostały ustawione odpowiednio (lub w ogóle), to korzeniem może zostać któryś z 4 skrajnych switchy, co obniży wydajność.
# Modyfikacje STP:

## Rapid Spanning Tree Protocol (RSTP)

- zamiast [[#Rodzaje portów|Blocked Port (BP)]] 2 nowe rodzaje portów:
	- **Alternate Port** - nie dostał [[#Rodzaje portów|Designated Port]], bo w segmencie sieci znaleziono lepsze połączenie na innym switchu
	- **Backup Port** - nie dostał Designated Port, bo na tym samym switchu znaleziono lepszy port
- zamiast [[#Stany portów w STP|Blocking]] i [[#Stany portów w STP|Listening]] jest stan Discarding
- teraz są stany: Disabled, Discarding, Learning, Forwarding
- modyfikacje:
	- wszystkie switche wysyłają [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|BPDU]] co [[#Czas trwania ramek BPDU|Hello Time]], nie tylko korzeń - zwiększa niezawodność
	- szybko uznaje się informację za przestarzałą - wystarczy brak 3 kolejnych **BPDU** od poprzedniego switcha w ścieżce do korzenia i już uznajemy, że nie ma połączenia z tym sąsiadem, a więc i z korzeniem poprzez niego
- dwukierunkowa informacja między switchami - w **STP** tylko przetwarzamy informację od sąsiadów, w **RSTP** switche komunikują się między sobą, żeby szybciej reagować na zdarzenia
- nowa tabela kosztów:

| Połączenie  | Koszt     |
| ----------- | --------- |
| 4 Mb/s <br> | 5.000.000 |
| 10 Mb/s     | 2.000.000 |
| 100 Mb/s    | 200.000   |
| 1 Gb/s      | 20.000    |
| 2 Gb/s      | 10.000    |
| 10 Gb/s     | 2000      |

- **edge ports** to te, do których podłącza się komputery; w **RSTP** od razu włącza się na nich stan [[#Stany portów w STP|Forwarding]], bo i tak nie zrobią cyklu; jeżeli pojawi się tam **BPDU**, to zmieniają się w “zwykły” port switcha i realizują **STP**
- jeśli [[#Rodzaje portów|Root Port]] zostanie stracony (np. odłączony kabel), to włącza się natychmiast **Alternate Port** (jeżeli można) mechanizmem **UplinkFast**, wysyła też do sąsiadów **Topology Change Notification (TCN)**
### Topology Change Notification

 - specjalne [[Ramka#Ramka BPDU (Bridge Protocol Data Unit)|BPDU]] wysyłane przez mostki do root’a, żeby poinformować go, że topologia sieci się zmieniła (np. sąsiad nie odpowiada). Jeżeli root otrzyma **TCN**, to wysyła do switchy **TCA (Topology Change Acknowledgement)**, żeby zmniejszyły swoje czasy w [[Urządzenia warstwy łącza danych#Skąd mostki wiedzą o położeniu hostów?|tablicach forwardingu]] (bo niektóre [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adresy MAC]] mogą być nieaktualne).
## Per VLAN Spanning Tree + (PVST/PVST+)

- dla każdego VLANu robi osobne drzewo rozpinające
- może być redundantny (identyczne drzewa dla wielu VLANów)
## Multiple Spanning Tree Protocol (MSTP)

- ulepszenie PVST+
- robi wiele drzew, ale nie 1 per VLAN
- dla drzewa wybieramy VLANy, które z niego korzystają
- BPDU dla wszystkich drzew z MSTP pakowane są w jedną ramkę, więc trzeba przesyłać mniej ramek, niż jest drzew