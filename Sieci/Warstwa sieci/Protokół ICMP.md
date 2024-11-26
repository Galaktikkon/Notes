- protokół sterujący w warstwie sieciowej
- wspomaga protokoły IP, UDP i TCP
- przesyła informacje:
	- o błędach transmisji, np. host nieosiągalny
	- sterujące ruchem, np. przekierowania
	- pomocnicze, np. rozgłaszanie routera
- zastosowania:
	- ping - badanie dostępności hosta
	- traceroute - badanie trasy pakietu
	- sterowanie routingiem IP
	- wykrywanie MTU ścieżki
	- proste sterowanie przepływem
- przesyła komunikaty ICMP, enkapsulowane w pakietach IP (są w warstwie 4!)
# Budowa

- typ (1 bajt), kod (1 bajt) - określają rodzaj komunikatu, patrz niżej
- suma kontrolna (2 bajty)
- inne dane potrzebne danemu typowi - zmienna długość

![[Pasted image 20241119233152.png|center]]

- poza tym, co w tabelce ważny jest jeszcze komunikat typu 11 z kodem 0 “Czas
przekroczony”, gdzie TTL wiadomości spadł do 0 (patrz niżej, program ping)


# ICMP Redirect

- problem: host może mieć niewydajną konfigurację przesyłania danych do jakiejś sieci, tzn. używa niepotrzebnie jakiegoś routera, który i tak wtedy zawsze przekazuje ruch jakiemuś innemu routerowi w tej samej sieci
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
1. Router G1 sprawdza swoją tablicę routingu i widzi, że musi routować pakiet do routera G2. Widzi też, że G2 jest w tej samej sieci, co host i G1, a zatem host wysyłając pakiet do sieci X mógłby wysłać pakiet od razu do G2, a nie naokoło przez G1.
3. Router G1 wysyła ICMP Redirect do hosta mówiąc mu, żeby dodał do swojej tablicy routingu wpis (sieć X, maska sieci X, G2).
4. Router G1 przesyła pakiet dalej do G2