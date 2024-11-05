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

- 4 bajty (32 bity)
- najpierw ciąg 1 długości takiej, jak adres sieci + adres podsieci
- potem ciąg 0 długości takiej, jak adres hosta
- długość adresów sieci i hosta wyznacza klasa, a maska granicę między adresem podsieci i adresem hosta
- liczba 1 nie musi być wielokrotnością 8, np. może być 28 - wtedy na adresy hostów zostałyby tylko 4 bity
- maskę zapisuje się albo jak adres IP, albo przez / po adresie IP z liczbą bitów maski
- maska domyślna - jedynki na adresie sieci, zera na adresie hosta
- maska 255.255.255.255 - każdy host jest we własnej podsieci, wymusza ciągłą komunikację przez routery (łącze point-to-point)


