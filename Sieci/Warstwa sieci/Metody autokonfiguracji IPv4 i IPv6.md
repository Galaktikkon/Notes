![[Pasted image 20241207212726.png|center]]

# Auto-IP

- w [[Adres IPv4|IPv4]], host losuje adres z puli 169.254.0.0/16 za wyjątkiem pierwszej i ostatniej podsieci /24 (czyli z zakresu 169.254.0.0 – 169.254.255.255, za wyjątkiem 169.254.0.0/24 i 169.254.255.0/24) i sprawdza z użyciem [[Protokół ARP#ARP probe|ARP Probe]], czy adres jest wolny; jeżeli tak, to go bierze, inaczej losuje znowu i tak do skutku.
# Link-local

- w IPv6, każdy host posiada minimum jeden adres IPv6, powstały przez sklejenie FE80::/10, 54 bitów “0”(reprezentują brak podsieci) i [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adresu MAC]] w postaci EUI-64 (MAC rozszerzony do 64 bitów)

# RARP

- mechanizm przypominający prymitywne [[Protokół DHCP|DHCP]], w którym w każdej sieci jest serwer RARP (+ ew. zapasowe, ale nie biorą aktywnego udziału), serwer przechowuje odwzorowania IP -> MAC, nowe hosty dowiadują się o serwerze przez broadcast. Rozwiązanie to słabo wykorzystuje możliwości pasma i wymaga wielu serwerów. Używa się mechanizmu ARP.

# BOOTP

- poprzednik DHCP, gdzie host pyta, a serwer BOOTP odpowiada na broadcaście ograniczonym; klient odpowiada za niezawodność połączenia przez retransmisje. To w nim zostali wprowadzeni relay agents. W przeciwieństwie do RARP pozwala uzyskiwać także dodatkowe informacje (nie tylko IP) oraz używa UDP.

# Stateless

- [[Routing#Router|routery]] wysyłają Router Advertisement z prefiksem sieci i managed=0, host dokleja do prefiksu własny adres MAC w formacie EUI-64 i dodaje trasę przez router, od którego dostał RA. Chociaż ta metoda nie ma serwera jako takiego, to router pełni podobną funkcję, bo też ogłasza się hostom.

# DHCPv6 (stateful)

- routery wysyłają Router Advertisement, ale managed=1, więc klient ma sam zgłosić się do zarządzającego adresami serwera DHCPv6. Serwer przydziela mu adres IP i ew. dodatkowe opcje. Dodawana trasa pochodzi jednak z RA, a nie z tego serwera DHCP!


# SLAAC

- Stateless Address Autoconfiguration
## Mechanizm działania

1. **Uruchomienie interfejsu sieciowego:**
    - Gdy urządzenie jest podłączone do sieci IPv6, jego interfejs sieciowy generuje tymczasowy **adres link-local**, który służy do komunikacji w ramach lokalnego łącza.
2. **Wysłanie żądania Router Solicitation (RS):**
    - Urządzenie wysyła komunikat **Router Solicitation (RS)** na multicastowy adres `ff02::2`, pytając o obecność routerów w sieci.
3. **Otrzymanie odpowiedzi Router Advertisement (RA):**
    - Router w sieci odpowiada komunikatem **Router Advertisement (RA)**, zawierającym informacje potrzebne do konfiguracji:
        - Prefiks sieciowy
        - Czas życia prefiksu
        - Informację o tym, czy SLAAC jest obsługiwany.
        - MTU, adres bramy.
4. **Generowanie adresu IPv6:**
    - Urządzenie na podstawie prefiksu otrzymanego w RA oraz swojego identyfikatora interfejsu (np. adresu MAC) generuje unikalny **adres IPv6**.
    - Adres ten może być utworzony za pomocą:
        - **EUI-64** (rozszerzony identyfikator unikalny), który bazuje na adresie MAC.
        - Innych metod, np. adresów prywatnych (zgodnych z RFC 7217), aby zwiększyć prywatność.
5. **Weryfikacja adresu:**
    - Urządzenie wykonuje **Duplicate Address Detection (DAD)**, aby upewnić się, że wygenerowany adres jest unikalny w sieci.
6. **Użycie adresu:**
    - Po weryfikacji urządzenie zaczyna korzystać z nowego adresu IPv6 do komunikacji w sieci.

### Zalety SLAAC

- **Brak serwera DHCPv6:** Konfiguracja odbywa się w pełni automatycznie, bez konieczności wdrażania serwera DHCP.
- **Prostota i efektywność:** Routery rozsyłają prefiksy sieciowe, a urządzenia same generują adresy.
- **Skalowalność:** Działa dobrze w dużych sieciach, gdzie ręczne przypisywanie adresów lub utrzymywanie serwerów DHCP byłoby trudne.
### Ograniczenia SLAAC

1. **Brak dodatkowych konfiguracji:** SLAAC samodzielnie nie przekazuje informacji takich jak adresy serwerów DNS (chyba że są używane rozszerzenia, np. RDNSS).
2. **Brak kontroli centralnej:** W sieciach, gdzie wymagana jest centralna kontrola adresów, SLAAC może nie być wystarczające (w takich przypadkach stosuje się DHCPv6).
3. **Wrażliwość na fałszywe komunikaty RA:** Złośliwe urządzenie może wysyłać fałszywe komunikaty RA, co prowadzi do błędnej konfiguracji sieci.

### Przykład działania SLAAC

- Prefiks sieciowy: `2001:db8:1::/64` (otrzymany w RA).
- Identyfikator interfejsu: `02:1a:2b:3c:4d:5e`.
- Wygenerowany adres IPv6: `2001:db8:1::21a:2bff:fe3c:4d5e`.

To urządzenie automatycznie konfiguruje się do pracy w sieci IPv6, bez udziału administratora.