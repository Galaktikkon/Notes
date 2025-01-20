
- protokół [[Routing#Protokoły dystans-wektor|dystans-wektor]]
- wykorzystuje złożoną [[Metryka|metrykę]]
- routery przekazują sobie tylko składowe do obliczenia metryki
- każdy router ma własne współczynniki metryki $K_1 - K_5$
- dobrą praktyką jest ustawienie takich samych współczynników na wszystkich routerach w domenie routingu
- nieskończoność to $D = \text{MAX}$
- wykorzystuje dla zapobiegania pętlom:
	- [[Protokół RIP#Dzielony horyzont (split horizon)|dzielony horyzont]]
	- [[Protokół RIP#Zatruwanie (poison reverse)|zatruwanie ścieżki]]
	- [[Protokół RIP#Wstrzymywanie (holddown)|wstrzymywanie]]
- zegary:
	- update timer - jak często rozsyłane są wiadomości (domyślnie 90 s)
	- invalid timer - po tym czasie uznaje się ścieżkę za nieważną (3 * update timer)
	- hold-time - czas wstrzymywania (domyślnie 280 s)
	- flush timer - czas przed usunięciem ścieżki z tablicy routingu (630 s)
- wady:
	- synchronizacja
	- nieefektywne algorytmy zapobiegania pętlom
# Metryka protokołu IGRP

- uwzględnia
	- $B$ - [[Metryka#Przepustowość|bandwidth]]
	- $L$ - [[Metryka#Obciążenie|load]]
	- $D$ - [[Metryka#Opóźnienie|delay]]
	- $R$ - [[Metryka#Niezawodność|reliability]]
- pełny wzór:
$$
M = \left( K_1 \cdot B + K_2 \cdot \frac{B}{256-L}+K_3 \cdot D\right) \cdot \frac{K_5}{R+K_4}
$$
- współczynniki K1-K5 to wagi metryki, mogą być ustawiane przez administratora
- domyślnie:
	- $K_1 = K_3 = 1$
	- $K_2 = K_4 = K_5 = 0$
- dla $K_5 = 0$ opuszcza się tylko prawy element, tzn. pozostaje lewy nawias
- domyślnie bierze się pod uwagę tylko statyczne parametry, więc nie trzeba dynamicznie przeliczać wartości metryki
- wzór domyślny:
$$
M = K_1 \cdot B + K_3 \cdot D
$$