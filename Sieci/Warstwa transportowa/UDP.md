- bezpołączeniowy
- prosty
- szybki, ma małe narzuty danych
- zawodny:
	- brak potwierdzenia otrzymania/zgubienia pakietu
	- brak gwarancji kolejności
	- możliwość duplikacji
	- brak kontroli prędkości przesyłu danych
- warstwy wyższe muszą same sobie radzić z tymi problemami

# Budowa nagłówka UDP

![[Pasted image 20250108014836.png|center]]

- stała długość 8 B
- numery portów:
	- po 2 B (dlatego 216 - 1 możliwości)
	- przy odsyłaniu odbiorca wie, do kogo odesłać - wystarczy zamienić miejscami adresy
- długość:
	- długość nagłówka oraz danych
	- od 8 B (sam nagłówek) do 216 - 1 B (nagłówek + max danych)
	- maksymalne dane: 216 - 29 B (216 - 1 B, minus 8 B za nagłówek UDP, minus 20 B za nagłówek IP)
- suma kontrolna:
	- opcjonalna (same 0 = brak sumy)
	- obliczana z użyciem pseudonagłówka
	- pseudonagłówek - na czas obliczania sumy kontrolnej uwzględniamy nagłówek IP
	- realnie zapisuje się kod U1 sumy (dlatego jak obliczymy sumę 0, to zapisywane są same jedynki i można to odróżnić od braku sumy - samych zer)
	- długość datagramu = nagłówek UDP + dane enkapsulowane
![[Pasted image 20250108015036.png|center]]

# Zastosowania, zalety i wady UDP

- dane mało wrażliwe na gubienie pakietów, np. multimedia - utrata kilku klatek przy streamingu wideo pewnie nawet nie zostanie zauważona przez ludzkie oko
- transmisje grupowe - UDP pozwala na multicast, TCP nie (połączeniowe = zawsze point-to-point); np. IPTV używa multicastu, a więc i UDP
- transmisje w czasie rzeczywistym, np. gry multiplayer - UDP jest szybkie, więc może być też używane w modelu mieszanym (to, co nie wymaga kontroli cały czas idzie szybko przez UDP, a przez TCP tylko to, co musi)
- przesyłanie w sieci LAN, np. DHCP, DNS - sieci LAN mają zwykle wysoką niezawodność i UDP działa w nich dobrze w takich protokołach konfiguracyjnych (do tego przesyłają stosunkowo małe dane)