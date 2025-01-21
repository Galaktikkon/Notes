# 4B/5B
- nazywane kodowaniem blokowym
- dzieli bity na 4-bitowe bloki i zamienia je na 5-bitowe pakiety, dodając piąty bit
- każdy 5-bitowy pakiet może mieć dwie 1, co wymusza częste zmiany wartości bitów
- ułatwia synchronizację zegarów dzięki wymuszeniu częstej zmiany wartości bitów
- stosowany w 100Base-TX (Fast Ethernet)
- wada: wymaga zegara 125 MHz dla uzyskania 100 Mb/s
- dobór wartości pakietów: empiryczny, sprawdzono, że te kody dobrze działają
- przykładowa tabela kodów - hex $\rightarrow$ 4B/5B (oryginalnie hex zapisywany jest na 4 bitach, więc dodany jest 5 bit)![[Pasted image 20241015141926.png|center]]
# 5B/6B
- idea taka sama jak przy 4B/5B, tylko używamy innych liczb bitów (mapowanie 5 bitów na 6 bitów)
- **DC bias** - średnia amplituda fali prądu stałego; w kontekście sieci - średnia wartość sygnału, gdzie np. 0 to -1V, a 1 to +1V
- kodowanie to pozwala osiągnąć **DC balance** - jednakową liczbę zer i jedynek, DC bias = 0
- powyższe zapewnia brak polaryzacji sygnału - dominacji zer lub jedynek, co mogłoby powodować błędy
- łatwa synchronizacja dzięki częstym zmianom sygnału oraz średniemu napięciu na linii (jeżeli 0 to -1V, a 1 to +1V, to powinno być średnio 0V)
- łatwe wykrywanie błędów - łatwo wykryć sekwencję więcej niż 3 zer lub jedynek
- przykład: zegary 30 MHz na kabel w sieci 100VG-AnyLAN dają 30 Mbit/s na parę, więc 120 Mbit/s w skrętce, 5 bitów kodujemy za pomocą 6, więc realnie mamy  $\frac{5}{6} \cdot 120$ = 100 Mbit/s
# 8B/10B:
- 8 bitów kodowane za pomocą 10
- zapewnia DC balance
- realizuje running disparity - obliczenia gwarantujące, że będzie utrzymany DC balance
- zapewnia łatwe odzyskiwanie zegara, tzn. odczytanie go z sekwencji przesyłanych bitów; gwarantuje, że w ciągu 20 bitów DC bias to co najwyżej 2 i nie ma więcej niż 5 zer lub jedynek po kolei
- algorytm:
	1. Oktet jest rozbijany na 3 bardziej znaczące bity (jak piszemy normalnie binarnie, to są to po prostu trzy pierwsze od lewej) i 5 mniej znaczących
	2. Bardziej znaczące bity koduje się **3B/4B** (oznaczane x), mniej znaczące **5B/6B** (oznaczane y)
	3. Łączy się wyniki i zapisuje jako D.x.y
- definiuje się 12 **symboli danych (data symbols)**, służących do zakodowania np. początku ramki, końca ramki, +przecinek, -przecinek (patrz kropki w zapisie D.x.y)
- dla zachowania DC balance i max 5 zer/jedynek po kolei nie wykorzystuje wszystkich symboli
- 1 Gb/s przekłada się na 1,25 GHz
# 8B/6T
- 8 bitów wysyłamy za pomocą 6 sygnałów 3-poziomowych -V, 0 i +V
- powyższe pozwala, żeby prędkość transmisji stanowiła 3/4 data rate
- ze wszystkich możliwych 729 trójek wybiera się te zapewniające DC balance
- w 100Base-4T pozwala zmniejszyć wymaganą częstotliwość zegara



