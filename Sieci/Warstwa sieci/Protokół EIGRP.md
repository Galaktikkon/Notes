# Algorytm DUAL

- **D**iffusing **U**pdate **AL**gorithm
- usuwa pętle
- używany zarówno w protokołach [[Routing#Protokoły dystans-wektor|dystans-wektor]], jak i w protokołach stanu łącza
- wykorzystywany w EIGRP (Enhanced IGRP)
- zaletą jest, że robi zamieszanie tylko w pobliżu zmiany sieci - reszta nie musi wiedzieć o tym, że gdzieś nastąpiła zmiana
- używa 3 tablic
	- [[Protokół IGRP#Neighbour table|neighbour table]]
	- [[Protokół IGRP#Topology table|topology table]]
	- [[Protokół IGRP#Topology table|routing table]]
- definiuje funkcje
	- $d(k, j)$ - odległość między routerami $k$ i $j$
	- $l(i, k)$ - koszt łącza między routerem $i$ oraz jego sąsiadem $k$

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
	- [[Protokół IGRP#Feasible distance|feasible distance]] do celu
	- listę [[Protokół IGRP#Reported distance (advertised distance)|reported distance]] dla sąsiadów
	- stan trasy
		- pasywna - stabilna i gotowa do użycia
		- aktywna - DUAL jeszcze ją oblicza
## Routing table

- najlepsze adresy docelowe wybierane z topology table.
## Feasible distance

- najlepsza [[Metryka|metryka]] (najmniejszy koszt) na drodze od routera do docelowej sieci (włącznie z odległością do sąsiada, który rozgłasza tę ścieżkę, przez którą ona biegnie).
## Reported distance (advertised distance)

- feasible distance, ale bez drogi od routera do sąsiada, czyli nie uwzględnia pierwszego kabla na drodze. To właśnie to rozgłaszają routery - odbiorcy muszą sami dodać kabel do sąsiada, od którego dostali wiadomość (i zrobić z tego feasible distance). Każdy sąsiad ma takie.
## Successor 

- sąsiad, przez którego biegnie obecna najlepsza ścieżka, a więc ten, od którego otrzymaliśmy wiadomość, z której zrobiliśmy feasible distance.
## Feasible successor

- sąsiad, którego reported distance jest krótsze niż obecne feasible distance. Wystarczy więc, że jego odległość do docelowej sieci jest mniejsza, nie bierzemy pod uwagę kabla od naszego routera do tego sąsiada, jak w przypadku feasible distance!

# Metryka EIGRP

- domyślny wzór podobny, jak w IGRP:
$$M = (B + D) \cdot 256$$
- mnożenie przez 256 wynika z tego, że IGRP i EIGRP mają różną liczbę bitów na wielkość metryki (24 vs 32 bity); pomnożenie przez 256 zapewnia kompatybilność tych protokołów

## Przykład działania

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