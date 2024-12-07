
- dostarczanie adresacji **logicznej**
- znalezienie najlepszej drogi łączącej dwa hosty (mogą być w różnych sieciach z perspektywy warstwy łącza danych) - tzw. *routing*
- tworzenie i rozpakowywanie pakietów IP

# Protokoły warstwy 3

- IP (Internet Protocol)
- ARP (Address Resolution Protocol)
- ICMP (Internet Control Message Protocol)
- IGMP (Internet Group Management Protocol)
# Potrzeba adresacji w warstwie 3

- adresy Ethernet:
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