
- pozwala na śledzenie trasy datagramów IP między dwoma hostami
- umożliwia śledzenie z wykorzystaniem loose lub strict source routing
- wygodniejszy, niż śledzenie trasy zapisując ją w nagłówku IP
- wykorzystuje pole TTL (Time To Live) protokołu IP oraz komunikaty ICMP: “Czas przekroczony” oraz “Port nieosiągalny”
- wysyła dane do portu o numerze > 30000
- przykład działania:![[Pasted image 20241119233504.png|center]]
1. Host wysyła pakiet z TTL=1. Router A zmniejsza wartość do 0 i odrzuca pakiet, odsyłając komunikat ICMP “Czas przekroczony”. Komputer dostaje adres IP pierwszego routera (bo to on jest nadawcą komunikatu).
2. Host wysyła pakiet z TTL=2. Router A zmniejsza wartość do 1 i przekazuje pakiet do routera B, który zmniejsza ją do 0 i odrzuca. Odsyła do hosta komunikat ICMP “Czas przekroczony”, więc dostaje on adres IP routera B (drugiego routera na trasie)
3. Powyższe powtarza się dla routera C (TTL=3).
4. Kiedy pakiet dotrze do komputera docelowego (zaczyna z TTL=4), to ze względu na wysoki numer portu sprawi, że systemy Unixowe odrzucą go od razu i odeślą komunikat ICMP “Port nieosiągalny”. Jak przyjdzie, to proces kończy się - znane są wtedy trasa do hosta, routery po drodze i potrzebny TTL.