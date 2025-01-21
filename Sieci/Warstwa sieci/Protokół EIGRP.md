- [Film](https://www.youtube.com/watch?v=QyymlFWDEgM) wyjaśniający krok po kroku mechanizm EIGRP i algorytm DUAL
# Algorytm DUAL
- **D**iffusing **U**pdate **AL**gorithm
- usuwa pętle
- używany zarówno w protokołach [[Routing#Protokoły dystans-wektor|dystans-wektor]], jak i w protokołach [[Routing#Protokoły stanu łącza (link state)|stanu łącza]]
- wykorzystywany w EIGRP (Enhanced IGRP)
- zaletą jest, że robi zamieszanie tylko w pobliżu zmiany sieci - reszta nie musi wiedzieć o tym, że gdzieś nastąpiła zmiana
- używa 3 tablic
	- [[#Neighbour table|neighbour table]]
	- [[#Topology table|topology table]]
	- [[#Topology table|routing table]]
- definiuje funkcje
	- $d(k, j)$ - odległość między routerami $k$ i $j$
	- $l(i, k)$ - koszt łącza między routerem $i$ oraz jego sąsiadem $k$
- będąc routerem $i$ chcemy dostać najlepszą ścieżkę do $j$, więc pytamy sąsiada $k$ o nią

![[Pasted image 20250120223808.png|center]]

## Neighbour table
- sąsiedzi danego routera
- routery przesyłają sobie na multicast wiadomości Hello, żeby utrzymywać te tablice
- wiadomość zawiera **HoldTime**, czyli czas życia wpisu
- jeżeli przez **HoldTime** nie przyjdzie kolejne Hello, to router uznaje, że zmieniła się topologia
## Topology table
- sieci docelowe rozgłaszane przez sąsiednie routery
- rekordy zawierają:
	- adres celu
	- [[#Feasible distance|feasible distance]] do celu
	- listę [[#Reported distance (advertised distance)|reported distance]] dla sąsiadów
	- stan trasy
		- pasywna - stabilna i gotowa do użycia
		- aktywna - DUAL jeszcze ją oblicza
## Routing table
- najlepsze adresy docelowe wybierane z topology table.
## Reported distance (advertised distance, RP)
- Ścieżka która jest reklamowana przez sąsiedni router. Odbiorcy muszą sami dodać kabel do sąsiada, od którego dostali wiadomość (i zrobić z tego feasible distance). Każdy sąsiad ma takie. 
## Feasible distance (FD)
- najlepsza [[Metryka|metryka]] (najmniejszy koszt) na drodze od routera do docelowej sieci (włącznie z odległością do sąsiada, który rozgłasza tę ścieżkę, przez którą ona biegnie). Czyli RP + koszt kabla do niej.
## Successor 
- sąsiad, przez którego biegnie obecna najlepsza ścieżka, a więc ten, od którego otrzymaliśmy wiadomość, z której zrobiliśmy feasible distance.
## Feasible successor
- sąsiad, którego reported distance jest krótsze niż obecne feasible distance. Wystarczy więc, że jego odległość do docelowej sieci jest mniejsza, nie bierzemy pod uwagę kabla od naszego routera do tego sąsiada, jak w przypadku feasible distance!
# Metryka EIGRP
- domyślny wzór podobny, jak w IGRP:
$$M = (B + D) \cdot 256$$
- mnożenie przez 256 wynika z tego, że IGRP i EIGRP mają różną liczbę bitów na wielkość metryki (24 vs 32 bity); pomnożenie przez 256 zapewnia kompatybilność tych protokołów
## Przykład działania

Tutaj pokazana jest sama metryka i jej obliczanie, pełnia EIGRP połączona z algorytmem DUAL jest w [[#Pełny przykład działania algorytmu DUAL|przykładzie]] poniżej.

![[Pasted image 20250121000941.png|center]]

Mamy skonfigurowane w takiej sieci EIGRP. Chcemy z routera “one” komunikować się z
siecią “a”. B to minimum bandwidth w kb/s, D to delay.

Idąc przez router “three” minimalny bandwidth na drodze do sieci “a” to $128$. Delay to
łącznie $1200$ $(1000 + 100 + 100)$. Całkowity koszt to:
$$
[(10.000.000/128) + 1200] * 256 = (78.125 + 1200) * 256 = 79.325 *
256 = 20.307.200
$$
Idąc przez router “four” minimalny bandwidth to $56$, delay to łącznie $2200$. Całkowity
koszt to zatem:
$$
[(10.000.000/56) + 2200] * 256 = (178.571 + 2200) * 256 = 180.771 *
256 = 46.277.376
$$
Jak widać, mniejszy koszt ma router “three”, więc to on będzie successorem (to przez
niego będziemy routować do sieci “a”).

Odległość od “three” do sieci “a” to reported distance, po dodaniu kabla między one i
three daje to feasible distance.

Aby sprawdzić, czy “four” jest feasible successorem dla routera “three” (bo może nie być
żadnych, jeżeli żaden sąsiad nie spełnia warunku!), trzeba sprawdzić reported distance dla
routera “four”:
$$B = 10.000$$
$$D = 200$$
$$[(10.000.000/10.000) + 200] * 256 = 307.200$$
Jak widać, $307.200 < 20.307.200$, a więc router “four” to feasible successor dla
routera “three”.


# Mechanizm dyfuzji w DUAL
Gdy obecny sukcesor ulegnie awarii (nie wyśle wiadomości Hello przez dość długi czas) lub
jego metryka się zwiększy, to następuje:

![[Pasted image 20250121001223.png|center]]

Jeżeli [[#Feasible successor|feasible successor]] istnieje, to awansuje się go na [[#Successor|successora]] i nie ma problemu.
Jeżeli natomiast nie istnieje, to mamy problem.

Sieć była wcześniej w stanie pasywnym, czyli po prostu działała. Teraz musi się przeliczyć
i zdobyć successora, więc zmienia stan sieci docelowej na aktywny. Zmienia też feasible
distance do niej na nieskończoność, więc jeżeli połączenie w ogóle istnieje, to na 100%
będzie lepsze.

Router wysyła wiadomość EIGRP Query i czeka 180 s na odpowiedź. Jeżeli dostanie
alternatywną trasę, to wszystko jest już ustawione - sąsiad z najlepszą ofertą zostaje
successorem, można ustawić [[#Feasible distance|feasible distance]] oraz zmienić sieć na pasywną (czyli
działającą). Oczywiście przy okazji mogą się też ustawić pozostałe reported distance’e
oraz feasible successor.

Warto zauważyć, że jeżeli przed tym przeliczeniem sąsiad nie spełniał warunku, że
[[#Reported distance (advertised distance)|reported distance]] < feasible distance, to dzięki zmianie na nieskończoność podczas
przeliczania na pewno spełnia ten warunek, a więc ma szansę stać się successorem.

# Pełny przykład działania algorytmu DUAL

![[Pasted image 20250121001456.png|center]]

Mamy taką topologię sieci. FD - [[#Feasible distance]], AD - [[#Reported distance (advertised distance)|Advertised Distance]] (reported
distance). Liczby przy strzałkach to koszty połączeń.

Jak widać, np. dla routera C jego sukcesor to B, bo feasible distance to 3 - 1 za advertised
distance od B oraz 2 za kabel B-C.

Jego feasible successor to D, ponieważ advertised distance D to 1+1=2, a więc mniej niż
feasible distance dla C, czyli 3.

Załóżmy, że kabel B-D pada. Pierwszą rzeczą, która się stanie, jest brak komunikatów
Hello od B w stronę D, więc wpis ten zniknie z tablicy topologii D.
Ważne jest, że dla routera D zniknie wtedy sukcesor, a nie istnieje [[#Feasible distance|feasible successor]], bo
jedyną alternatywą był router C, który ma advertised distance 3, a więc więcej, niż
dotychczasowy [[#Successor|sukcesor]] B miał feasible distance, czyli 2.

Ważne też, że D był sukcesorem E.

![[Pasted image 20250121001632.png|center]]

W związku z tym, że D nie ma teraz sukcesora, to przełącza się w tryb aktywny i ustawia
feasible distance na nieskończoność (zapisane jako -1). Wysyła więc zapytania (EIGRP
Query) do swoich sąsiadów.

![[Pasted image 20250121001648.png|center]]

E widzi, że D stracił sukcesora i stał się aktywny, więc wie, że też ma problem, bo to D był
sukcesorem E. W związku z tym także E musi się przełączyć na stan aktywny, usunąć D
jako sukcesora i zmienić feasible distance na nieskończoność.

Router E wysyła zatem EIGRP Query do routera C (do routera D nie ma sensu i o tym wie).
W tym samym czasie router C odpowiada routerowi D.

![[Pasted image 20250121001704.png|center]]

Router D dostał odpowiedź od C, ale nadal czeka na odpowiedź od E. E z kolei czeka na
odpowiedź od C, którą w końcu dostanie.

![[Pasted image 20250121001721.png|center]]

Router E dostaje odpowiedź od routera C. Ma już więc pełnię informacji, wybiera C jako
swojego sukcesora, zapisuje feasible distance (advertised distance od C + kabel C-E) i
przełącza się w stan pasywny. Może też więc odpowiedzieć już D.

![[Pasted image 20250121001735.png|center]]

D dostaje odpowiedź od E (ostatnią brakującą rzecz), wybiera C jako sukcesora. Nowy
advertised distance E jest mniejszy od nowego feasible distance, a zatem router D może
także zapisać feasible successora na wypadek, jakby C padł. Co charakterystyczne, D nie
ma pojęcia o tym, że bez C w ogóle nie dostałby się do sieci “a”.
To już ostateczny stan sieci. Dopóki nie nastąpi kolejna zmiana topologii i kolejny Update,
nic się nie zmieni.

# Wady i zalety EIGRP

- zalety:
	- złożona metryka uwzględniająca wiele parametrów
	- aktualizacja tylko przy zmianach w topologii
	- komunikaty HELLO wysyłane domyślnie co 5 sekund na standardowych łączach na multicast 224.0.0.10, a więc poznawanie sąsiadów i funkcja keepalive
	- algorytm usuwania pętli DUAL
	- jest bezklasowy (IGRP jest klasowy)
	- lepiej skalowany, szybsza zbieżność
- wady:
	- synchronizacja
	- nieefektywna technika zapobiegania pętlom