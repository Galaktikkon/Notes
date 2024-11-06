
- problem: wysyłanie pakietu odbywa się po warstwie 2, np. adresem MAC w Ethernecie, a pakiet IP wysyłamy na adres IP, nie znając docelowego adresu MAC
- idea: dynamiczne mapowanie adresów IP (warstwy 3) na adresy MAC (warstwy 2)
- protokół ARP realizuje powyższe
- przesyła się pakiety/ramki ARP
- idea działania: wysłanie broadcastem zapytania ARP z docelowym IP o działaniu “jeśli masz ten IP, to odpowiedz mi swoim adresem MAC”, odpowiedni odbiorca wysyła odpowiedź ARP
- jeżeli hosty są w tej samej sieci, to nie ma problemu
- jeżeli hosty są w różnych sieciach, to trzeba dodatkowych mechanizmów, np. Proxy ARP (patrz niżej)

# Tablica ARP (ARP table, ARP cache)

- działa jak tablica Forwarding DataBase przy switchach, ale jest u hostów, a nie w urządzeniach pośrednich
- odwzorowanie IP -> MAC (lub odpowiednie inne odwzorowanie dla innych protokołów)
- adresy są odczytywanie z zapytań oraz z odpowiedzi ARP
- wpisy mają czas życia, są dynamiczne tworzone i kasowane
- można robić wpisy statyczne dla zwiększenia bezpieczeństwa

# Budowa pakietu/ramki ARP

● przesyła się całą ramkę (np. Ethernet), w środku w polu danych siedzi ramka ARP:

![[Pasted image 20241106014402.png|center]]

- typ - ARP, czyli 0x0806
- dla każdego elementu najpierw warstwa 2, potem 3
- rodzaj protokołu:
	- Ethernet - 0x0001
	- IP - 0x0800
- rozmiar adresu protokołu - w bajtach
- typ
	- zapytanie: 1
	- odpowiedź: 2
- adres nadawcy
- adres odbiorcy

# Działanie protokołu ARP w jednej sieci

1. Komputer A szukający komputera B zna jego adres IP. Tworzy ramkę Ethernet z zapytaniem ARP:
	- FF:FF:FF:FF:FF:FF | MAC A | 0806 | **0001 | 0800 | 6 | 4 | 1 | MAC A | IP A | 0 | IP B**
- Pogrubiona część to **ramka ARP**, enkapsulowana w ramce Ethernet. Po kolei:
	- adres broadcast jako adres docelowy ramki Ethernet
	- adres źródłowy to MAC nadawcy
	- 0806 - ramka Ethernet zawierająca ramkę ARP
	- dane - ramka ARP:
		- 0001 - używamy Ethernetu
		- 0800 - używamy IP
		- 6 - długość adresu MAC (w bajtach)
		- 4 - długość adresu IP (w bajtach)
		- 1 - oznaczenie zapytania ARP
		- MAC A - adres nadawcy w warstwie 2
		- IP A - adres nadawcy w warstwie 3
		- 0 - nieznany i szukany adres odbiorcy w warstwie 2
		- IP B - znany i używany adres odbiorcy w warstwie 3
2. Ramka jest broadcastowana do całej sieci lokalnej
3. Komputer B odbiera ramkę i tworzy ramkę Ethernet z odpowiedzią ARP:
	-  MAC A | MAC B | 0806 | **0001 | 0800 | 6 | 4 | 2 | MAC B | IP B | MAC A | IP A**
	- Różnice względem poprzedniej ramki:
		- adresy MAC źródłowy i docelowy są już znane (oba to unicasty)
		- dane - ramka ARP:
			- 2 - oznaczenie odpowiedzi ARP
			- są znane pełne dane warstw 2 i 3 nadawcy i odbiorcy
4. Komputer B wysyła ramkę do komputera A
5. Komputer A zna z ramki adres MAC komputera B, mogą się już komunikować
# Brama (ang. *gateway*)

- to urządzenie, które umożliwia komunikację pomiędzy różnymi sieciami komputerowymi, często o różnych protokołach lub adresacjach. Brama działa jak punkt dostępu, przez który pakiety danych mogą przemieszczać się z jednej sieci do drugiej.
## Brama domyślna

- adres IP routera, do którego komputery wysyłają pakiety, jeżeli ich odbiorca jest poza siecią lokalną. Jest częścią podstawowej konfiguracji hosta (więc zna ją np. od serwera DHCP, dostaje ją razem ze swoim IP).
# Działanie protokołu ARP w wielu sieciach

![[Pasted image 20241106020640.png|center]]

1. Komputer A porównuje sieci swoją i docelową. Są różne - tworzy więc zapytanie ARP do routera będącego bramą domyślną (a więc zna jego IP).
2. Wysyła ramkę do routera od swojej strony (Eth X na rysunku).
3. Router tworzy odpowiedź ARP ze swoim MACiem i odsyła do A.
4. Komputer A wysyła ramkę Ethernet z pakietem IP do routera.
5. Router zdejmuje ramkę Ethernet i analizuje pakiet IP. Chce go wysłać do B, ale nie zna jego adresu MAC.
6. Router tworzy zapytanie ARP do B i je wysyła (trzymając oryginalny pakiet od A).
7. Komputer B dostaje zapytanie od routera (od swojej strony, Eth Y na rysunku) i odsyła mu odpowiedź ze swoim adresem MAC.
8. Router dostaje adres MAC B. Tworzy ramkę Ethernet z tym adresem oraz oryginalnym pakietem IP od A i wysyła go do komputera B.

Gdyby było więcej routerów po drodze, to punkt 5 składałby się z ciągu zapytań i odpowiedzi między routerami po drodze.

Pakiety wysyłane w przykładzie powyżej (Eth X i Eth Y to adresy MAC routera na interfejsach odpowiednio po lewej i prawej):
- od A do routera (zapytanie ARP)
	- broadcast | MAC A | 0806 | 0001 | 0800 | 6 | 4 | 1 | MAC A | 10.2.3.4 | 0 | 10.2.3.1
- od routera do A (odpowiedź ARP)
	- MAC A | Eth X | 0806 | 0001 | 0800 | 6 | 4 | 2 | Eth X| 10.2.3.1 | MAC A | 10.2.3.4
- od A do routera, ta główna ramka od A do B (ramka Ethernet z pakietem IP)
	- Eth X | MAC A | IP A | IP B | (dane)
- od routera do B (zapytanie ARP)
	- broadcast | Eth Y | 0806 | 0001 | 0800 | 6 | 4 | 1 | Eth Y | 100.1.1.1 | 0 | 100.1.1.2
- od B do routera (odpowiedź ARP)
	-  Eth Y | MAC B | 0806 | 0001 | 0800 | 6 | 4 | 2 | MAC B | 100.1.1.2 | Eth Y | 100.1.1.1
- od routera do B, oryginalna ramka od A do B (ramka Ethernet z pakietem IP)
	- MAC B | Eth Y | IP A | IP B | (dane)

# Proxy ARP

- wybór proxy device jako pośrednika w przekazywaniu informacji do innej sieci
- proxy “podstawia” swój adres MAC za adres MAC celu (tzn. tego IP, do którego chce się wysłać pakiet), a potem sam przesyła pakiety dalej
- działanie:
	1. Komputer A wysyła zapytanie ARP przez router do komputera B (wierzy błędnie, że są w tej samej sieci, np. bo nie obsługuje podsieci albo ma tak skonfigurowaną maskę)
	2. Router znajduje u siebie w tablicy ARP adres docelowy
	3. Router zwraca komputerowi A swój własny adres MAC
	4. Komputer A chcąc komunikować się z B komunikuje się de facto z routerem, który przekazuje dalej do B pakiety
- ukrywa istnienie dwóch sieci, bo host myśli, że to proxy (z tej samej sieci, co on) jest finalnym celem
## Zalety

- ukrywa istnienie wielu sieci (oba hosty mogą korzystać z tego samego adresu sieci)
- umożliwia komunikację źle skonfigurowanym hostom
- umożliwia komunikację hostom nieobsługującym podsieci
- można włączyć bez naruszania tablic routingu
## Wady

- skalowalność (wymaga ustawienia proxy’ego dla każdego urządzenia)
- nie działa dla niektórych topologii (np. 2 routery łączące 2 fizyczne sieci)
- brak mechanizmów niezawodności (brak planu B, jeżeli proxy padnie)
- zwiększenie stopnia skomplikowania sieci
- źle skonfigurowany router-proxy może zablokować ruch (bo gromadzi pakiety jako proxy, ale nie umie ich z jakiegoś powodu wysłać)
- ukrywanie złej konfiguracji hostów
- zwiększa ruch ARP
- hosty muszą posiadać większe tablice ARP
- bezpieczeństwo (np. można podszyć się pod cudzy adres)
- nie działa w sieciach nieużywających ARP

# Gratuitous ARP

- wysyła się dodatkowe, specjalnie ramki ARP
- ramka GARP:
	- IP hosta wysyłającego ramkę to zarówno IP źródłowe, jak i docelowe
	- używają zawsze adresu broadcastowego
	- nikt nie powinien odpowiedzieć na taką ramkę, ale każdy ją otrzyma

## Zastosowania

- aktualizowanie tablic ARP innych hostów - przydatne np. przy backupowychserwerach, bo gdy główny padnie, to zapasowy (ten sam adres IP, co główny) wyśle wszystkim ramkę GARP ze swoim MACiem, żeby wszyscy dowiedzieli się o zmianie
- powiadomienie innych hostów o pojawieniu się nowego - zawczasu wypełnia tablice ARP innych hostów IP i MACiem nowego hosta, co potencjalnie zmniejsza późniejszą liczbę wysyłanych pakietów ARP
- powiadomienie wcześniej switcha o porcie danego hosta lub o zmianie jego adresu MAC
- wykrywanie błędów w konfiguracji - bardzo częste ramki GARP od danego hosta mogą oznaczać, że np. ma problem z kablem i co chwilę odłącza się i podłącza z powrotem
## Wady

- dodatkowe pakiety wysyłane przy podłączeniu urządzenia powodują dużo ruchu w sieci, a mogą być niepotrzebne (np. nowy host komunikuje się tylko z 1 innym hostem),

# ARP probe

- problem: trzeba się upewnić, że ma się unikatowy adres IP, np. serwer DHCP nie dałbłędnie dwóch takich samych
- idea: coś podobnego do gratuitous ARP (też są wysyłane “nadmiarowo”), ale do innego celu
- różni się od GARP tym, że ma ustawione:
	- MAC odbiorcy na 00:00:00:00:00:00
	- IP nadawcy na 0.0.0.0
- powyższe gwarantuje, że nic nie wykorzysta tego pakietu do update’owania tablic ARP (bo jeżeli jest konflikt adresów, to zdecydowanie nie chcemy, żeby odbiorcy się “uczyli” na tych danych), w przeciwieństwie do GARP, gdzie tego właśnie chcemy
- jeżeli ktoś odpowie, to jest konflikt adresów IP i nie można z niego korzystać
- jak nikt nie odpowie, to jest ok