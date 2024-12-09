
- dostarczanie adresacji **logicznej**
- znalezienie najlepszej drogi łączącej dwa hosty (mogą być w różnych sieciach z perspektywy warstwy łącza danych) - tzw. *[[Routing|routing]]*
- tworzenie i rozpakowywanie [[Protokół IP#Budowa Pakietu IP (Datagramu IP)|pakietów IP]]

# Protokoły warstwy 3

- [[Protokół IP|IP]] (Internet Protocol)
- [[Protokół ARP|ARP]] (Address Resolution Protocol)
- [[Protokół ICMP|ICMP]] (Internet Control Message Protocol)
- IGMP (Internet Group Management Protocol)
# Potrzeba adresacji w warstwie 3

- adresy [[Adresacja w sieciach LAN|Ethernet]]:
	- tylko w teorii unikatowe
	- ograniczona liczba
	- płaska struktura
	- nie dają informacji o położeniu geograficznym
	- słabo skalowalne
	-  Ethernet to nie jedyny standard, a powinna być możliwość obsłużenia wielu naraz
- adresy IP:
	- hierarchiczna
	- związana z położeniem geograficznym
	- skalowalna