![[Pasted image 20241207212726.png|center]]

# Auto-IP

- w IPv4, host losuje adres z puli 169.254.0.0/16 za wyjątkiem pierwszej i ostatniej podsieci /24 (czyli z zakresu 169.254.0.0 – 169.254.255.255, za wyjątkiem 169.254.0.0/24 i 169.254.255.0/24) i sprawdza z użyciem ARP Probe, czy adres jest wolny; jeżeli tak, to go bierze, inaczej losuje znowu i tak do skutku.
# Link-local

- w IPv6, każdy host posiada minimum jeden adres IPv6, powstały przez sklejenie FE80::/10, 54 bitów “0”(reprezentują brak podsieci) i adresu MAC w postaci EUI-64 (MAC rozszerzony do 64 bitów)

# RARP

- mechanizm przypominający prymitywne DHCP, w którym w każdej sieci jest serwer RARP (+ ew. zapasowe, ale nie biorą aktywnego udziału), serwer przechowuje odwzorowania IP -> MAC, nowe hosty dowiadują się o serwerze przez broadcast. Rozwiązanie to słabo wykorzystuje możliwości pasma i wymaga wielu serwerów. Używa się mechanizmu ARP.

# BOOTP

- poprzednik DHCP, gdzie host pyta, a serwer BOOTP odpowiada na broadcaście ograniczonym; klient odpowiada za niezawodność połączenia przez retransmisje. To w nim zostali wprowadzeni relay agents. W przeciwieństwie do RARP pozwala uzyskiwać także dodatkowe informacje (nie tylko IP) oraz używa UDP.

# Stateless

- routery wysyłają Router Advertisement z prefiksem sieci i managed=0, host dokleja do prefiksu własny adres MAC w formacie EUI-64 i dodaje trasę przez router, od którego dostał RA. Chociaż ta metoda nie ma serwera jako takiego, to router pełni podobną funkcję, bo też ogłasza się hostom.

# DHCPv6 (stateful)

- routery wysyłają Router Advertisement, ale managed=1, więc klient ma sam zgłosić się do zarządzającego adresami serwera DHCPv6. Serwer przydziela mu adres IP i ew. dodatkowe opcje. Dodawana trasa pochodzi jednak z RA, a nie z tego serwera DHCP!