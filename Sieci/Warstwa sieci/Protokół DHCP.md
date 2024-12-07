
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