# Non Return To Zero (NRZ):

![[Pasted image 20241015133616.png|center]]

- 0 to brak napięcia lub napięcie ujemne, 1 to napięcie dodatnie
- problem: rozróżnienie ciągu 0 lub 1, wymaga dodatkowej synchronizacji
- szybkość sygnalizacji = szybkość transmisji
- 2 bity informacji na sygnał
- ważny poziom sygnału (0 lub 1)
- używane w [[Media komunikacyjne#Światłowód|światłowodach]]
# Return To Zero (RZ)

![[Pasted image 20241015132225.png|center]]

- 0 to brak napięcia lub napięcie ujemne przez cały cykl
- 1 to napięcie dodatnie przez pół cyklu i brak napięcia przez kolejne pół cyklu
- rozwiązuje problem rozróżniania ciągów 1 (problem z ciągiem 0 nadal jest)
- mniejsza wydajność niż NRZ, bo 1 zużywa teraz cały cykl, wtedy jest tylko 1 bit informacji na sygnał
# Non Return To Zero Inverted (NRZ-I)

![[Pasted image 20241015133153.png|center]]

- 0 to napięcie, które było wcześniej (brak zmiany napięcia)
- 1 to odwrócenie wcześniejszego napięcia (+V -> -V, -V -> +V)
- kod różnicowy - informację niesie zmiana poziomu sygnału
- nie potrzeba transmitować sygnału zegara, bo dzięki różnicy się synchronizuje (kod [[Kodowanie Informacji#Samosynchronizacja|samosynchronizujący]])
- zapewni DC balance - średnie napięcie równe 0
- szybkość sygnalizacji = szybkość transmisji
- dobre dla [[Media komunikacyjne#Światłowód|światłowodów ]](można użyć zegara o wysokiej częstotliwości, bo nie ma problemów z mocą), przy miedzianych gorzej
# Alternate Mark Inversion (AMI)

![[Pasted image 20241015140602.png|center]]

- kod trójwartościowy
- 0 to brak napięcia
- 1 to na przemian napięcie dodatnie i ujemne, zmienia się w zależności od wartości poprzedniej
# Manchester / Manchester Phase Encoding (MPE)

![[Pasted image 20241015135010.png|center]]

- 0 jest kodowane przez zbocze malejące, czyli 10 (połowa sygnału - napięcie dodatnie, druga połowa - napięcie ujemne)
- 1 jest kodowane przez zbocze rosnące, czyli 01
- jeżeli są dwie 1 lub dwa 0, to nastąpi przeskok wartości między bitami
- ważny poziom sygnału (0 lub 1)
- zmiana w połowie bitu pozwala synchronizować komunikację, szczególnie dzięki nagłemu spadkowi między tymi samymi wartościami, co rozwiązuje problem ciągu 0 lub 1
- zapewnia częstą zmianę napięcia, proporcjonalną do częstotliwości zegara, co uodparnia na błędy
- wykorzystuje je [[Ethernet]] w modelu 10BASE-T (czyli 10 Mb/s)
# Manchester różnicowy / Differential Manchester Encoding (DME)

![[Pasted image 20241015135229.png|center]]

- 0 powtarza ostatni sygnał (01 lub 10)
- 1 zmienia sygnał (01 na 10, 10 na 01)
- zachowuje zalety Manchestera
- kod różnicowy - informację niesie zmiana poziomu sygnału
- jest bardzo odporny na błędy, bo gwarantuje zmianę napięcia w środku bitu, a wykrywanie samego faktu takiej zmiany napięcia jest mniej podatne na błędy niż porównywanie sygnału z jakąś wartością
- odporny np. na zmianę polaryzacji przez zamianę przewodów (dzięki używaniu tylko faktu zmiany napięcia)
- wada: double bandwidth, są 2 ticki zegara na bit
- w porównaniu do Manchestera eliminuje konieczność dodawania preambuły do synchronizacji zegarów, więc np. Token Ring w przeciwieństwie do [[Ethernet|Ethernetu]] jej nie ma (bo korzysta z DME)
- używają go np. Token Ring, modemy telefoniczne
# Multi-Level Transition 3 (MLT-3)

![[Pasted image 20241015140502.png|center]]

- trójwartościowy - napięcie ujemne, brak napięcia i wysokie napięcie
- poziomy po kolei: + 0 - 0 , np. +0-0+0-0+0-0+...
- 0 zachowuje poprzedni poziom
- 1 przechodzi do następnego poziomu
- pozwala uzyskać 100 Mb/s z sygnału 31,25 MHz (zmniejsza 4 razy wymaganą częstotliwość zegara)
- używany w sieciach 100Base-TX (Fast Ethernet) do uzyskania 100 Mb/s, dzięki temu połączeniu wystarczy 31,25 MHz zamiast 125 MHz (tak jest bez MLT-3)
- problem: ciągi 0 lub 1 w wyniku (+ i -), trzeba synchronizować
# Pulse Amplitude Modulation 5 (PAM5)
- pięciowartościowy: -2V, -V, 0V, 1V, 2V (V - dowolne napięcie, całość może być np. od -1,0 V do +1,0 V)
- 4 poziomy wykorzystuje do kodowania, a 0V do korekcji błędów
- koduje 2 bity sygnału na jeden symbol
- 125 mln sygnałów / s * 4 pary * 2 bity = 1 Gb/s
- umożliwia prędkość 1 Gb/s i full duplex w standardzie 1000Base-T