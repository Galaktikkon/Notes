
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
FF:FF:FF:FF:FF:FF | MAC A | 0806 | 0001 | 0800 | 6 | 4 | 1 | MAC A | IP A | 0 | IP B

Pogrubiona część to **ramka ARP**, enkapsulowana w ramce Ethernet.
Po kolei:
● adres broadcast jako adres docelowy ramki Ethernet
● adres źródłowy to MAC nadawcy
● 0806 - ramka Ethernet zawierająca ramkę ARP
● dane - ramka ARP:
○ 0001 - używamy Ethernetu
○ 0800 - używamy IP
○ 6 - długość adresu MAC (w bajtach)
○ 4 - długość adresu IP (w bajtach)
○ 1 - oznaczenie zapytania ARP
○ MAC A - adres nadawcy w warstwie 2
○ IP A - adres nadawcy w warstwie 3
○ 0 - nieznany i szukany adres odbiorcy w warstwie 2
○ IP B - znany i używany adres odbiorcy w warstwie 3
2. Ramka jest broadcastowana do całej sieci lokalnej
3. Komputer B odbiera ramkę i tworzy ramkę Ethernet z odpowiedzią ARP:
MAC A | MAC B | 0806 | 0001 | 0800 | 6 | 4 | 2 | MAC B | IP B | MAC A | IP A
Różnice względem poprzedniej ramki:
● adresy MAC źródłowy i docelowy są już znane (oba to unicasty)
● dane - ramka ARP:
○ 2 - oznaczenie odpowiedzi ARP
○ są znane pełne dane warstw 2 i 3 nadawcy i odbiorcy
4. Komputer B wysyła ramkę do komputera A
5. Komputer A zna z ramki adres MAC komputera B, mogą się już komunikować