Najpopularniejsza adresacja
# Budowa adresu

![[Pasted image 20241030012353.png|center]]

- 32 bity
- podzielone na 4 oktety (4 * 8 b)
- dzieli się na 2 części: sieci i hosta, każda zajmuje ileś oktetów
- **klasa adresu** w **klasowym IPv4** wyznacza, ile oktetów dostaje adres sieci, a ile adres hosta; rozróżnia się ją po pierwszych bitach pierwszego oktetu
- pozwala na mniej adresów niż [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|MAC]], więc stosuje się hierarchiczne rozwiązania
- komputerów w sieci jest o 2 mniej, niż wynikałoby z liczby bitów na adres hosta! Trzeba te 2 zarezerwować dla adres sieci i [[Adresacja w sieciach LAN#Broadcast|broadcast]]
## Klasy adresowe

![[Pasted image 20241105231543.png|center]]

- Klasa A
	- 126 sieci, ponad 16mln komp.
	- adres sieci zajmuje 1 oktet
- Klasa B 
	- 65tys sieci, 65tys komp
	- adres sieci zajmuje 2 oktety
- Klasa C
	- ponad 16mln sieci, 254 komputery
	- adres sieci zajmuje 3 oktety
- Klasa D
	- multicast
- Klasa E
	- zarezerwowana
### Wyznaczanie numeru sieci i hosta

1. Zamiana pierwszej liczby (bajtu) na reprezentację binarną.
2. Sprawdzenie do której klasy należy numer sieci
3. W oparciu o liczbę zajmowanych przez klasę bajtów wyznaczamy numer hosta

![[Pasted image 20241105232448.png|center]]
# Uzyskiwanie adresu

- numer sieci
	- od odpowiedniej organizacji (**NIC**, *ang. Network Information Center*) lub Internet Providera
- numer hosta
	- nadawane przez lokalnego administratora
- dla każdej karty sieciowej trzeba pozyskać osobny adres
- hosty z tej samej sieci mają ten sam adres sieci i unikatowe numery hostów
- w przypadku braku podłączenia do sieci globalnej można nadawać dowolne adresy
# Notacja adresu

- Zapis binarny
	- 00000110 10000100 00000010 00000001
- Zapis szesnastkowy
	- 0x06840201
- Zapis dziesiętny
	- 109314561
- Zapis kropkowo-dziesiętny
	- 6.132.2.1
	- dziesiętnie, każdy bajt oddzielnie $\rightarrow$ każdy to liczba z zakresu 0-255
# Adresy specjalne

- 0.0.0.0 
	- ten komputer w tej sieci. Podawany jako adres źródłowy w trakcie uruchamiania komputera gdy nie zna on jeszcze swojego adresu IP
- 0.x.y.z
	- komputer x.y.z w tej sieci. Podawany w trakcie uruchamiania jako adres źródłowy w komputerze posiadającym niekompletne informacje
- 127.x.y.z
	- adres `loopback`. Pakiet wysłany na taki adres nie może zostać wysłany poza komputer. Pozwala dwóm aplikacjom pracującym na tym samy komputerze komunikować się poprzez **TCP/IP**
# Adresy typu broadcast

- Mogą być podane tylko jako adres docelowy.
- Ograniczony broadcast
	- 255.255.255.255
	- Oznacza wszystkie komputery w sieci lokalnej. Nigdy nie są przekazywane przez routery.
- Broadcast skierowany
	- Adres, w którym część adresu komputera składa się z samych jedynek, zaś część sieci jest określona. Oznacza wszystkie komputery w danej sieci. 
	- Np.: 130.1.255.255 
		- wszystkie komputery w sieci 130.1.0.0

