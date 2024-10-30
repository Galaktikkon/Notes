Najpopularniejsza adresacja
# Budowa adresu

![[Pasted image 20241030012353.png|center]]

- 32 bity
- podzielone na 4 oktety (4 * 8 b)
- dzieli się na 2 części: sieci i hosta, każda zajmuje ileś oktetów
- **klasa adresu** w **klasowym IPv4** wyznacza, ile oktetów dostaje adres sieci, a ile adres hosta; rozróżnia się ją po pierwszych bitach pierwszego oktetu

| Klasa | Oznaczenie | Oktety (sieć-host) |
| ----- | ---------- | ------------------ |
| A     | 0          | 1-3                |
| B     | 10         | 2-2                |
| C     | 110        | 3-1                |
| D     | 1110       | Multicast          |
| E     | 1111       | Zarezerwowane      |
- pozwala na mniej adresów niż MAC, więc stosuje się hierarchiczne rozwiązania
- komputerów w sieci jest o 2 mniej, niż wynikałoby z liczby bitów na adres hosta! Trzeba te 2 zarezerwować dla adres sieci i broadcast
## Wyznaczanie klasy adresu:

1. Bierzemy pierwszy oktet (pierwsza liczba w X.Y.Z.Q lub pierwsze 8 bitów binarnie)
2. Zamieniamy na postać binarną
3. Sprawdzamy pierwsze cyfry
# Uzyskiwanie adresu

- numer sieci - od odpowiedniej organizacji (**NIC**) lub Internet Providera
- numer hosta - nadawane przez lokalnego administratora
- dla każdej karty sieciowej trzeba pozyskać osobny adres
- hosty z tej samej sieci mają ten sam adres sieci i unikatowe numery hostów
- w przypadku braku podłączenia do sieci globalnej można nadawać dowolne adresy

