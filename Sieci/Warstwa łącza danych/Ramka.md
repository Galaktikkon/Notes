- Porcja danych przesyłana przez sieć jako całość
- Typowe pola w ramce sieci [[Sieci lokalne|LAN]]
	- znacznik początku i końca ramki - dodawane przez układy nadawczo-odbiorcze
	- adres nadawcy i odbiorcy
	- informacja o typie lub długości ramki
	- pole danych
	- suma kontrolna
- Poszczególne standardy określają własną minimalną i maksymalną długość ramki
	- **MTU** (Maximum Transmission Unit) – maksymalna długość pola danych ramki warstwy łącza danych
# Interframe Gap (IFG)

 - czas braku transmisji pomiędzy ramkami. Dla [[Ethernet|Ethernetu]] czas na transmisję 96 bitów (12 B).
# Szczelina czasowa

- czas potrzebny na wysłanie ramki o minimalnym rozmiarze
- podawana w jednostkach *bit time* (bt) lub *byte time* (Bt) lub sekundach
- często można spotkać podawanie w bitach/bajtach - wtedy szczelina czasowa to czas potrzebny na przesłanie takiej ilości informacji
- musi być na tyle duża, żeby wykryło, że dwie stacje jednocześnie nadają, zanim skończą nadawać swoje ramki
- czas trwania pojedynczej szczeliny to **slot time** (**ST**)
- czas na wysłanie ramki > 1 **ST**
- czas na poinformowanie o kolizji < 1 **ST**
- określa maksymalną [[#Rozległość fizycznego segmentu sieci|rozległość fizycznego segmentu]] sieci
- wartości:
	- [[Ethernet]]: 512 bt = 64 Bt
	- Fast Ethernet: 512 bt = 64 Bt
	- Gigabit Ethernet: 4096 bt = 512 Bt
# Minimalny rozmiar ramki

- idea: czas wysyłania ramki musi być na tyle duży, żeby w tym czasie dało się stwierdzić, że jest kolizja i poinformować o tym nadawców
- w najgorszym przypadku host potrzebuje aż **[[Problemy w transmisji danych#Prędkość i czas propagacji|RTT]]** czasu na dowiedzenie się o transmisji (pełna droga ramki tam i z powrotem), więc minimalny rozmiar trzeba tak skorelować z **RTT** i rozległością sieci, żeby czas przesłania całej ramki był mniejszy niż **RTT**
- ramka musi być na tyle duża, żeby jej początek dotarł do wszystkich stacji, zanim wysłany zostanie jej ostatni bit
- jej czas wysyłania się = wielkość szczeliny czasowej
- bierze pod uwagę:
	- RTT (Round Trip Time)
	- opóźnienia w przetwarzaniu informacji
	- skończona szybkość propagacji sygnału (ok. 2/3 prędkości światła)
- wartości:
	- [[Ethernet]]: 512 b = 64 B
	- Fast Ethernet: 512 b = 64 B
	- Gigabit Ethernet: 4096 b = 512 B
# Runt frame (collision fragment)

- ramka krótsza niż minimalna. Zwykle istnieje przez kolizje, błędy [[Adresacja w sieciach LAN#Network Interface Card (NIC)|kart sieciowych]] etc.

# Rozległość fizycznego segmentu sieci
- wyznaczana przez minimalny rozmiar ramki i wielkość szczeliny czasowej
- wynika z ograniczeń nakładanych przez [[CSMA#with Collision Detection|CSMA/CD]] (związane z punktem powyżej, zapewnia brak problemów z kolizjami)
- musi brać pod uwagę uwarunkowania fizyczne, np. niezerowe opóźnienia urządzeń warstwy pierwszej
- zależy od standardu (np. zwykły [[Ethernet]])
- $\text{max rozległość} = \text{prędkość propagacji} \cdot (\text{wielkość szczeliny czasowej} / 2)$
- przykład:

```
Ethernet 10 Mb/s.
Minimalny rozmiar ramki = 512 bitów = 64 B

Teoretyczny czas wysłania minimalnej ramki (czas propagacji = 2/3 c): 51,2 μs
Teoretyczna maksymalna rozległość sieci: > 5 km

Uwzględniając opóźnienia elementów warstwy pierwszej, czas na wysłanie jam signalu
etc.:
Realna maksymalna rozległość sieci: 2,8 km
```

# Ramka BPDU (Bridge Protocol Data Unit)

## Budowa ramki BPDU:

| Root ID | Root Path Cost | Bridge ID | Sender Port ID  | Receiver Port ID |
| ------- | -------------- | --------- | -------------- | ---------------- |

- Root ID:
○ 8 B
○ 2 bajty - priorytet (arbitralnie narzucony); im niższy, tym ważniejszy jest switch
○ 6 bajtów - adres MAC root’a
● Root Path Cost:
○ 4 B
○ każde połączenie ma koszt - im lepsze połączenie, tym niższy
○ zgodnie z tabelką: