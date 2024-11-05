![[Pasted image 20241105234659.png|center]]

- twór czysto logiczny
- rozróżnia się je dzięki masce podsieci
- budowa adresu dla podsieci:
	- adres sieci + adres podsieci + adres hosta
- adres podsieci “zużywa” bity na [[Adres IPv4#Budowa adresu|adres hosta]], więc więcej podsieci = mniej hostów w każdej podsieci
# Cele tworzenia podsieci

- podział dużych sieci [[Adres IPv4#Klasy adresowe|klasy A i B]] na mniejsze
- podział [[Segmentacja Sieci#Domena Rozgłoszeniowa|domen rozgłoszeniowych]] na mniejsze (każda podsieć to osobna domena rozgłoszeniowa)
- ukrycie szczegółów budowy sieci przed routerami zewnętrznymi
- możliwość połączenia różnych rodzajów sieci lokalnych
- lepsze od wielu sieci klasy wyższej, bo zmniejsza to rozmiar tablic routowania
# Maska podsieci

![[Pasted image 20241105235020.png|center]]

- 4 bajty (32 bity)
- najpierw ciąg 1 długości takiej, jak adres sieci + adres podsieci
- potem ciąg 0 długości takiej, jak adres hosta
- długość adresów sieci i hosta wyznacza klasa, a maska granicę między adresem podsieci i adresem hosta
- liczba 1 nie musi być wielokrotnością 8, np. może być 28 - wtedy na adresy hostów zostałyby tylko 4 bity
- maskę zapisuje się albo jak adres IP, albo przez / po adresie IP z liczbą bitów maski
- maska domyślna - jedynki na adresie sieci, zera na adresie hosta
- maska 255.255.255.255 - każdy host jest we własnej podsieci, wymusza ciągłą komunikację przez routery (łącze point-to-point)
## Analiza adresu

- Adres IP: $107.50.33.254$
- Maska podsieci: $255.255.255.0$

$107_{10} = 01101011_2 \rightarrow$ 0 na początku $\rightarrow$ klasa A $\rightarrow$  pierwszy bajt to adres sieci

$255.255.255.0 \rightarrow$  jedynki na pierwszych 3 bajtach $\rightarrow$  pierwsze 3 bajty to adresy sieci i podsieci, ostatni bajt to adres hosta

Więc mamy:
$107.0.0.0$ - numer sieci (sieć + same 0)
$107.50.33.0$ - numer podsieci (sieć + podsieć + same 0)
$107.50.33.254$ - numer hosta (sieć + podsieć + host)

Obrazkowo:

![[Pasted image 20241105235948.png|center]]

# Adresy specjalne

- Broadcast skierowany do podsieci
	- Oznacza wszystkie komputery w danej podsieci. Np.: 10.20.30.255/24 - wszystkie komputery w sieci 10 i podsieci 20.30
- Broadcast skierowany do wszystkich komputerów we wszystkich podsieciach danej sieci
	- 10.255.255.255/24 ó pakiet kierowany pod taki adres nie powinien być przekazywany przez routery

# Podsieci niezalecane

- sytuacja: dzielimy podsieć np. na 8 podsieci, bierzemy na to 3 ($2^3=8$) bitów, podsieci te można wypisać w kolejności rosnących adresów (po kolei dla bitów podsieci 000, 001, 010 etc.)
- **podsieć zero** - podsieć, która ma same zera na bitach adresu podsieci
- **podsieć samych jedynek** - podsieć, która ma same jedynki na bitach adresy podsieci
- problemy:
	- **podsieć zero** - ma taki sam adres sieci i podsieci, co jest mylące
	- **podsieć samych jedynek** - adres broadcast sieci i podsieci są takie same, co może w skrajnych przypadkach powodować cykle routowania
- ze względu na powyższe problemy używanie tych podsieci jest niezalecane (kiedyś te adresy były nawet domyślnie wyłączone)