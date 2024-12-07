
- Dynamic Host Configuration Protocol
- serwer DHCP dostaje pulę adresów do przydzielania, nowy host bierze dane od serwera na określony czas (czas dzierżawy) i kiedy przestaje być aktywny, to adres wraca do puli
- pozwala automatycznie skonfigurować adres IP, maskę podsieci, adres bramy sieciowej i inne
- wykorzystuje protokół UDP
- klient używa portu 68, serwer DHCP portu 67

# Komunikaty (DHCP + <>)

- **DISCOVER** - host nie wie nic i prosi o oferty adresów
- **OFFER** - serwer proponuje hostowi dany adres
- **REQUEST** - host akceptuje ofertę adresu lub host zna swój poprzedni adres i prosi o jego odnowienie (wtedy zamiast **DISCOVER**)
- **ACK** - serwer przyjmuje do wiadomości akceptację adresu przez hosta
- **NACK** - odmowa przydzielenia adresu, np. negatywna odpowiedź na **REQUEST** dla odnowienia dzierżawy adresu
- **DECLINE** - nastąpił konflikt adresów, serwer powinien wyłączyć dany adres
- **RELEASE** - host kończy pracę i sam zwraca adres do puli serwera

# Algorytm

0. Klient może spróbować użyć swojej starej konfiguracji. Wymaga autorytatywnego serwera DHCP, który ogarnia takie żądania. Wysyła **REQUEST** z prośbą o przydzielenie poprzedniego adresu. Jeżeli dostanie **ACK**, to ok; jeśli **NACK**, to idzie do punktu 1.
1. Host wysyła na adres ograniczonego broadcastu 255.255.255.255 komunikat **DISCOVER** ze swoim adresem MAC.
2. Serwery DHCP w segmencie sieci dostają komunikat od hosta, więc znają jego MAC. Jeśli mogą (mają wolne adresy w puli i dany host nie jest zablokowany dla danego serwera przez admina), to odsyłają hostowi **OFFER** z tym adresem i oznaczają go jako zaoferowany.
3. Host otrzymuje 1 lub więcej ofert. Bierze “najlepszą”, czyli zwykle otrzymaną najszybciej i odsyła broadcastem (wszystkim serwerom) **REQUEST**, żądając wybranego adresu.
4. Wszystkie serwery dostają komunikat. Te, których oferty zostały odrzucone, oznaczają adres z powrotem jako wolny. Ten, który ma żądany adres wysyła hostowi **ACK** z potwierdzeniem dzierżawy i czasem dzierżawy. Może też wysłać pozostałe żądane informacje, np. maskę podsieci czy adres bramy sieciowej.
5. Host otrzymuje potrzebne informacje. Wysyła do otrzymanego własnego adresu IP pakiet ARP Probe, chcąc odpowiedzi na własny adres (sprawdza, czy na pewno dostał unikatowy adres). Ma nie dostać odpowiedzi - wtedy zaczyna korzystać z adresu i uruchamia zegar czasu dzierżawy. Jeżeli natomiast dostanie, to znaczy, że jest konflikt adresów, to wysyła do serwera, z którego ma adres **DECLINE**, a serwer wyłącza dany adres. Host wraca do punktu 1.
6. Po minięciu 50% czasu dzierżawy, czyli T1 Renewal Time host wysyła unicastowo do serwera, z którego ma adres, **REQUEST**, chcąc przedłużenia dzierżawy. Jeżeli serwer odeśle **ACK**, to zegar się zeruje, jeśli nie odeśle nic to czas leci dalej, a jeżeli odeśle **NACK** to dzierżawa się kończy i wracamy do punktu 1.
7. Po minięciu 87,5% (7/8 czasu), czyli T2 Rebind Time host wysyła broadcastowo do wszystkich serwerów **REQUEST**, chcąc przedłużenia dzierżawy (identycznie, jak przy T1). Reakcja na **ACK**/brak odpowiedzi/**NACK** identyczna jak w punkcie 6.
8. Po minięciu 100% czasu wracamy do punktu 1.

- w punkcie 7. używa się broadcastu (w przeciwieństwie do unicastu z punktu 6.), bo w punkcie 6. serwer mógł nie odpowiedzieć, bo padł, a do czasu T2 uruchomi się serwer zapasowy, który ma inny adres (więc na unicast do oryginalnego serwera by nie odpowiedział, a na broadcast odpowie)
- host wyśle **RELEASE**, jeżeli w trakcie czasu dzierżawy będzie kończyć działanie

![[Pasted image 20241207210959.png|center]]

# DHCP a wiele sieci

- problem: 
	- powyższy mechanizm działa poprawnie, jeśli serwer DHCP i host są w jednej sieci, więc nie zadziała np. dla jednej firmowej sieci i kilku podsieci w niej, bo routery zatrzymają broadcast ograniczony 255.255.255.255
- rozwiązania:
	- osobny serwer DHCP dla każdej podsieci - koszty i trudność administracyjna, ale dla niewielu segmentów sieci dobre rozwiązanie, bo proste
	- jedna maszyna obsługująca wszystkie sieci - programowy serwer ogarnia wszystkie sieci, ale to problem z bezpieczeństwem (bo wszyscy muszą mieć dostęp do tego serwera) i trzeba ciągnąć dodatkowe kable
	- użycie mechanizmu **relay agent**

# Relay agent

- idea
	-  w każdym końcowym segmencie sieci jest router relay agent, który pośredniczy między hostami z segmentu a serwerem DHCP; nie blokuje on komunikatów broadcastu ograniczonego DHCP, tylko realizuje relay - przesyła te komunikaty dalej, modyfikując niektóre pola
- wiadomości między relay agentem a serwerem idą unicastem, natomiast w sieci z hostem i relay agentem unicastem za wyjątkiem pierwszego połączenia
## Algorytm

1. Host wysyła broadcastem ograniczonym komunikat **DHCPDISCOVER** za pomocą UDP, na port 67. Dociera więc też do routera, czyli relay agenta.
2. Relay agent, czyli router w tej samej sieci, co host sprawdza adres bramy domyślnej w nagłówku DHCP. Jeżeli jest równy 0.0.0.0, to zamienia adres źródłowy na własny adres(tak, że bramą jest dla serwera ten router), adres odbiorcy na adres serwera i przesyła dalej.
3. Komunikat idzie po routerach unicastem aż do serwera. Serwer sprawdza IP bramy, znajdując pulę adresów dla danej bramy. Jeżeli tylko ma pulę dla danej bramy i wolne adresy, to tworzy komunikat **DHCPOFFER** i wysyła unicastem do relay agenta.
4. Pakiet idzie aż do oryginalnego relay agenta (routera, który pierwszy odebrał komunikat od hosta), który przesyła do unicastem do oryginalnego hosta, który sobie już to sam przetwarza.
5. Punkty 1-5 powtarzają się dla kolejnych komunikatów DHCP.

