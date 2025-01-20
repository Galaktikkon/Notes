
# Budowa nagłówka IPv6

![[Pasted image 20250108010642.png|center]]

- stała długość 40 B
- prostsza budowa od nagłówka [[Adres IPv4|IPv4]]
- nie ma opcji ani sumy kontrolnej
- powyższe pozwalają na szybsze przetwarzanie od [[Adres IPv4#Budowa adresu|nagłówka IPv4]]

# Pola nagłówka

- **Next Header** - typ następnego nagłówka - może być to albo kolejny nagłówek IPv6 (coś jak opcje w IPv4), albo już nagłówek warstwy 4 ([[TCP]] - 6, [[UDP]] - 17); pola te zwykle przetwarzane są dopiero u odbiorcy
- **Payload Length** - długość całego pakietu po tym nagłówku - zarówno danych, jak i kolejnych nagłówków IPv6
- **Hop Limit** - maksymalna liczba skoków, coś jak [[Protokół IP#Time To Live (TTL)|TTL]] w IPv4; ważne jednak, że jeżeli docelowy host będzie miał Hop Limit = 0, to przetworzy pakiet, a nie odrzuci go

# Budowa adresu

- 8 grup po 2 bajty zapisane w hexie (4 znaki), tzw. notacja colon-hex, np.:
	**2001:0db8:85a3:0000:0000:8a2e:0370:7334**
- zapis często upraszcza się poprzez:
	- opuszczenie wiodących zer w oktetach (musi zostać 1 znak na oktet):
		**2001:db8:85a3:0:0:8a2e:370:7334**
	- zamiana (maksymalnie jednego!) ciągu zer na “::”:
		**2001:db8:85a3::8a2e:370:7334**
	- ostatnie 32 bity (4 B, a więc 2 ciągi po 4 znaki) można zapisać w postaci dot-decimal, jak IPv4, np.:
		**::149.156.97.23**
● można jawnie wyznaczyć prefix adresu:
	**2345:BA23:: /40**

# Fragmentacja

- zachodzi tylko u hosta wysyłającego ([[Routing#Router|routery]] nigdy nie [[Protokół IP#Fragmentacja pakietów IP|fragmentują]])
- trzeba znać [[Protokół IP#Fragmentacja pakietów IP|MTU ścieżki]] przed wysłaniem (lub tunelować, czyli pakować IPv6 do pakietów IPv4 i tak wysyłać)
- fragmentacja zachodzi poprzez użycie extension headera “Fragmentation header”
- kolejne fragmenty dostają własne bazowe nagłówki
# Autokonfiguracja

- stateless:
	- routery rozgłaszają prefix sieci, trasę domyślną i MTU
	- adres **link-local** - FE:80::/10, nie forwardowany poza łącze
	- host robi tymczasowy adres IP, sklejając adres link-local i swój [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|MAC]] i wysyła [[Adresacja w sieciach LAN#Multicast|multicas]]t, chcąc dostać informacje od routera
	- potem używa adresu: prefix sieci + własny MAC
	- w razie potrzeby można używać adresu z **link-local**
- stateful:
	- używa [[Metody autokonfiguracji IPv4 i IPv6#DHCPv6 (stateful)|DHCPv6]]
	- host multicastem zgłasza się do serwera (serwerów) [[Protokół DHCP|DHCP]] i dostaje informacje

# Procedura EUI-64

- EUI-64 pozwala na generowanie 64-bitowego identyfikatora interfejsu na podstawie 48-bitowego [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adresu MAC]].
- Algorytm:
    1. Rozdzielenie adresu MAC na dwie części: 24 bity OUI (organizacyjne) i 24 bity unikalnego identyfikatora.
    2. Wstawienie `FF:FE` w środku adresu MAC.
        - Przykład: MAC `00:1A:2B:3C:4D:5E` → `00:1A:2B:FF:FE:3C:4D:5E`.
    3. Zmiana siódmego bitu w pierwszym bajcie (bit **U/L**) na wartość przeciwną.
        - Jeśli pierwszy bajt to `00` (binarnie: `00000000`), zmiana siódmego bitu daje `02`.
    4. Otrzymany adres: `021A:2BFF:FE3C:4D5E`.

# Odwzorowanie adresów IPv6 na MAC

- W IPv6 stosuje się **Neighbor Discovery Protocol (NDP)** zamiast [[Protokół ARP|ARP]] (znanego z IPv4).
- NDP działa w ramach ICMPv6 i pozwala odwzorowywać adresy IPv6 na adresy MAC.
    - Główne komunikaty NDP:
        - **Neighbor Solicitation (NS)** – zapytanie o [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|MAC]] urządzenia odpowiadającego na dany adres IPv6.
        - **Neighbor Advertisement (NA)** – odpowiedź zawierająca adres MAC.