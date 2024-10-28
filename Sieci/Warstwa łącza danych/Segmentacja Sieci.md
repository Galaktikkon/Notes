- wyeliminowanie z sieci hubów i repeaterów
- podział sieci na małe sieci wirtualne (VLAN), bo chociaż fizycznie są połączone, to na poziomie warstwy drugiej (logicznie) są wtedy odseparowane
- użycie szyfrowania na wyższych warstwach sieci
- użycie “inteligentnego” sprzętu (np. zarządzalne switche), które wykrywają i reagują na próby podsłuchiwania lub blokują je (np. blokują komunikację dowolnych dwóch urządzeń niebędących częścią infrastruktury sieci)
# Domena Kolizyjna

- część sieci, gdzie transmitować może tylko jedno urządzenie naraz. Granicę wyznaczają [[Urządzenia warstwy łącza danych#Bridge|mostki]], [[Urządzenia warstwy łącza danych#Switch|switche]] i routery. 
# Domena Rozgłoszeniowa

- część sieci, gdzie docierają [[Adresacja w sieciach LAN#Broadcast|broadcasty]] lub [[Adresacja w sieciach LAN#Multicast|multicasty]]. Granicę wyznaczają routery i sieci wirtualne VLAN.

# Podział za pomocą warstw

## Za pomocą urządzenia warstwy pierwszej

- hub - ale to nic nie daje (nie da się)
- rośnie domena kolizyjna

![[Pasted image 20241028015332.png|center]]

## Za pomocą urządzenia warstwy drugiej

- [[Urządzenia warstwy łącza danych|mostek lub switch (wieloportowy mostek)]]
- rozszerza domenę rozgłoszeniową (domyślnie, można zrobić VLANy)
- stanowi granicę sieci kolizyjnych

![[Pasted image 20241028015517.png|center]]

## Za pomocą urządzenia warstwy trzeciej

- router
- granica domen rozgłoszeniowych
- granica domen kolizyjnych

![[Pasted image 20241028015558.png|center]]

# Zastosowania urządzeń poszczególnych warstw

- fizyczna:
	- pozwalają zwiększyć rozległość sieci (regeneracja ramek)
- łącza danych:
	- mają zalety [[Osprzęt#Repeater (wtórnik)|repeaterów]]
	- zmniejszają ruch w sieci poprzez filtrację [[Ramka|ramek]]
	- separują tylko [[#Domena Kolizyjna|domeny kolizyjne]]
- sieciowa:
	- separacja domen kolizyjnych i [[#Domena Rozgłoszeniowa|rozgłoszeniowych]]