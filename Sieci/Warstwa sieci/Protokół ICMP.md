- protokół sterujący w warstwie sieciowej
- wspomaga protokoły [[Protokół IP|IP]], [[UDP]] i [[TCP]]
- przesyła informacje:
	- o błędach transmisji, np. host nieosiągalny
	- sterujące ruchem, np. przekierowania
	- pomocnicze, np. rozgłaszanie routera
- zastosowania:
	- ping - badanie dostępności hosta
	- traceroute - badanie trasy pakietu
	- sterowanie routingiem IP
	- wykrywanie [[Protokół IP#Fragmentacja pakietów IP|MTU ścieżki]]
	- proste sterowanie przepływem
- przesyła komunikaty ICMP, enkapsulowane w pakietach IP (są w warstwie 4!)
# Budowa

- typ (1 bajt), kod (1 bajt) - określają rodzaj komunikatu, patrz niżej
- suma kontrolna (2 bajty)
- inne dane potrzebne danemu typowi - zmienna długość

![[Pasted image 20241119233152.png|center]]

- poza tym, co w tabelce ważny jest jeszcze komunikat typu 11 z kodem 0 “Czas
przekroczony”, gdzie [[Protokół IP#Time To Live (TTL)|TTL]] wiadomości spadł do 0 (patrz niżej, program ping)


# ICMP Redirect

- problem: host może mieć niewydajną konfigurację przesyłania danych do jakiejś sieci, tzn. używa niepotrzebnie jakiegoś [[Routing#Router|routera]], który i tak wtedy zawsze przekazuje ruch jakiemuś innemu routerowi w tej samej sieci
- idea: router wysyła hostowi informację “niewydajnie przekierowujesz ruch, lepiej będzie, jak dla pakietów do sieci X użyjesz routera Y”
- routery po wykryciu niewydajnego przekierowywania pakietu w ramach tej samej sieci, co host, informują go komunikatem ICMP Redirect o tym, że dla pakietów do sieci X lepiej korzystać z innego routera
- zmniejsza ilość ruchu w sieci i zmniejsza obciążenie routerów
- hosty mogą ignorować te komunikaty - zależy to od ich konfiguracji
- aby router wysłał ICMP redirect, muszą być spełnione 4 warunki:
	- interfejs, na który przychodzi pakiet musi być taki sam, jak ten, na który jest on dalej routowany (przesyłany dalej)
	- adres źródłowy sieci i podsieci muszą być takie same, jak następnego routera (next hop)
	- pakiet nie ma skonfigurowanego routingu źródłowego (loose/strict source routing)
	- router ma włączone wysyłanie redirectów (na sprzęcie Cisco domyślnie jest)
- przykład:![[Pasted image 20241119233644.png|center]]
1. Host wysyła pakiet do sieci X na bramę domyślną, którą jest router G1.
2. Router G1 sprawdza swoją tablicę routingu i widzi, że musi routować pakiet do routera G2. Widzi też, że G2 jest w tej samej sieci, co host i G1, a zatem host wysyłając pakiet do sieci X mógłby wysłać pakiet od razu do G2, a nie naokoło przez G1.
3. Router G1 wysyła ICMP Redirect do hosta mówiąc mu, żeby dodał do swojej tablicy routingu wpis (sieć X, maska sieci X, G2).
4. Router G1 przesyła pakiet dalej do G2


# PING

- Packet InterNet Groper (PING)
- możliwości:
	- sprawdzanie, czy host jest osiągalny
	- sprawdzanie czasu podróży pakietu ([[Problemy w transmisji danych#Prędkość i czas propagacji|Round-Trip Time, RTT]])
	- sprawdzanie liczby zgubionych i zdublowanych pakietów
- pozwala sprawdzić, czy host jest osiągalny
- korzysta z komunikatów ICMP typów 0 i 8 (odpowiedź echo i zapytanie echo)
- dodatkowe dane: identyfikator i numer sekwencyjny
- działanie:
	1. Komputer A tworzy [[Ramka|ramkę]] **ICMP Echo Request**: 
		- 8 | 0 | process ID | 0 | (dane)
	2. Komputer A wysyła ramkę do komputera B.
	3. Komputer B tworzy ramkę **ICMP Echo Reply**:
		-  0 | 0 | process ID | 0 | (dane)
	4. Komputer B wysyła ramkę do komputera A (jak jest PING, to musi być PONG)

# Traceroute

- pozwala na śledzenie trasy datagramów IP między dwoma hostami
- umożliwia śledzenie z wykorzystaniem loose lub strict source routing
- wygodniejszy, niż śledzenie trasy zapisując ją w nagłówku IP
- wykorzystuje pole [[Protokół IP#Time To Live (TTL)|TTL (Time To Live)]] protokołu IP oraz komunikaty ICMP: “Czas przekroczony” oraz “Port nieosiągalny”
- wysyła dane do portu o numerze > 30000
- przykład działania:![[Pasted image 20241119233504.png|center]]
1. Host wysyła pakiet z TTL=1. Router A zmniejsza wartość do 0 i odrzuca pakiet, odsyłając komunikat ICMP “Czas przekroczony”. Komputer dostaje adres IP pierwszego routera (bo to on jest nadawcą komunikatu).
2. Host wysyła pakiet z TTL=2. Router A zmniejsza wartość do 1 i przekazuje pakiet do routera B, który zmniejsza ją do 0 i odrzuca. Odsyła do hosta komunikat ICMP “Czas przekroczony”, więc dostaje on adres IP routera B (drugiego routera na trasie)
3. Powyższe powtarza się dla routera C (TTL=3).
4. Kiedy pakiet dotrze do komputera docelowego (zaczyna z TTL=4), to ze względu na wysoki numer portu sprawi, że systemy Unixowe odrzucą go od razu i odeślą komunikat ICMP “Port nieosiągalny”. Jak przyjdzie, to proces kończy się - znane są wtedy trasa do hosta, routery po drodze i potrzebny TTL.

# With record-route (opcja IP 7)

Opcja **record-route** w nagłówku IPv4 umożliwia zbieranie adresów IP routerów pośredniczących na trasie między źródłem a celem. Routery, które obsługują tę opcję, dodają do nagłówka swój adres IP, co pozwala zobaczyć ścieżkę pakietu w sieci.

1. **Pakiet wychodzący z hosta źródłowego**:
    - Host źródłowy (np. komputer) wysyła pakiet z włączoną opcją _record-route_ w nagłówku IP.
    - Routery po drodze dodają swoje adresy IP do specjalnego pola w nagłówku.
2. **Pakiet dociera do celu**:
    - Pakiet zawiera listę adresów IP routerów, przez które przechodził w kierunku do celu.
3. **Odpowiedź (np. ICMP Echo Reply)**:
    - Odpowiedź z hosta docelowego również zawiera włączoną opcję _record-route_.
    - Routery na trasie powrotnej dodają swoje adresy IP do pola nagłówka, jeżeli je obsługują.
#### **Przykład**

Wyobraźmy sobie następującą sieć:

- **Host źródłowy (A):** 192.168.1.1
- **Host docelowy (B):** 10.0.0.1
- Routery na trasie:
    - R1: 192.168.1.254
    - R2: 172.16.0.1
    - R3: 10.0.0.254
1. Host A wysyła pakiet ICMP Echo Request z opcją _record-route_ do B.
2. Routery dodają swoje adresy IP do nagłówka:
    - R1: Dodaje 192.168.1.254.
    - R2: Dodaje 172.16.0.1.
    - R3: Dodaje 10.0.0.254.
3. Pakiet dociera do hosta B z listą adresów: `[192.168.1.254, 172.16.0.1, 10.0.0.254]`.
4. Host B odsyła pakiet ICMP Echo Reply, również z włączoną opcją _record-route_.
5. Routery na trasie powrotnej dodają swoje adresy IP:
    - R3: Dodaje 10.0.0.254 (adres wyjściowy).
    - R2: Dodaje 172.16.0.1.
    - R1: Dodaje 192.168.1.254.
6. Host A odbiera odpowiedź z pełnym zapisem tras:
    - Do celu: `[192.168.1.254, 172.16.0.1, 10.0.0.254]`.
    - Z powrotem: `[10.0.0.254, 172.16.0.1, 192.168.1.254]`.
#### **Zalety**

- Daje pełny wgląd w trasę pakietu, zarówno w kierunku do celu, jak i z powrotem.
- Przydatne w przypadku asymetrycznego routingu, gdy trasa do celu różni się od trasy powrotnej.
#### **Wady**

1. **Ograniczona liczba routerów**:
    - W nagłówku IPv4 jest ograniczone miejsce na adresy IP. Można zapisać maksymalnie 9 adresów routerów.
2. **Wsparcie przez routery**:
    - Wiele nowoczesnych routerów ignoruje opcję _record-route_ ze względów wydajnościowych lub bezpieczeństwa.