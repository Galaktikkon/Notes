
# Komunikacja warstw

Każda warstwa posiada interfejs z dolną i górną warstwą (z wyjątkiem 1 i 7 warstwy). Bardzo ważną rolę odgrywa realizowanie interfejsów protokołów.

- W obrębie obu komunikujących się systemów:
	- każda z implementacji warstw modelu [[Modele warstwowe#Warstwy modelu OSI/ISO|OSI/ISO]] w jednym systemie komunikuje się z implementacją **tej samej** warstwy w drugim systemie: komunikacja typu *peer-to-peer*
	- implementacja innego systemu może być zupełnie inna pod warunkiem zachowania wymagań protokołu (interfejsu)
	- Czerwone przerywane linie symbolizują protokół funkcjonujący w ramach danej warstwy![[Pasted image 20241014172000.png|center]]
- W obrębie jednego systemu:
	- implementacja każdej warstwy jest niezależna od innej
	- pomiędzy bezpośrednio położonymi warstwami istnieje dobrze zdefiniowany interfejs

# Przesyłanie danych

Poszczególne koperty protokołów warstw odpowiednio zagnieżdżają się ze sobą, komunikacja w ramach warstwy odbywa się poprzez interpretację nagłówków danego protokołu.

![[Pasted image 20241014173246.png|center]]

- Porcja danych na poziomie warstwy N nosi nazwę **N-PDU** (ang. *protocol data unit*) i składa się z trzech podstawowych części:
	- nagłówek (ang. *header*)
	- pole danych (ang. *payload*)
	- zakończenie ramki (ang. *trailer*)
- **PDU** z poziomu warstwy N+1 jest *enkapsulowane* w **PDU** warstwy N tzn. ramka poziomu N+1 jest zawarta w polu danych ramki poziomu N
	- Jak schodzę w dół to pakuję dane na zasadzie matrioszki, przy procesie *dekapsulacji* rozbieram rosyjską zabawkę

Nazewnictwo PDU w zależności od warstwy:
1. Warstwa fizyczna - sygnały elektryczne
2. Warstwa łącza danych - [[Ramka|ramki]] [[Ethernet]]
3. Warstwa sieciowa - pakiet IP
4. Warstwa transportowa - datagram UDP / segment TCP

**Enkapsulacja** - zawieranie się PDU warstwy wyższej (SDU) w polach danych PDU warstwy niższej. W drugą stronę (u odbiorcy), rozpakowywanie, to **dekapsulacja**.

SDU - Service Data Unit, dane z warstwy wyższej enkapsulowane przez warstwę niższą.