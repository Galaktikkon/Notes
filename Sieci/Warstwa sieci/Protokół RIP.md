
# RIPv1

- Routing Information Protocol
- co 30 sekund routery wysyłają [[Adresacja w sieciach LAN#Broadcast|broadcastem]] informacje do sąsiadów, tzw. RIP response
- metryka - liczba hopów (routerów po drodze do sieci)
- czas życia wpisu w tablicy routingu to 180 s
- pakiety RIP są enkapsulowane w polu danych datagramu [[UDP]] (warstwy 4!), a dopiero potem w polu danych pakietu [[Protokół IP|IP]]
- pakiety wysyłane z portu i na [[Port|port]] 520 [[UDP]], broadcastem (adres 255.255.255.255), zawierają dane o wszystkich sieciach znanych routerowi
- pakiety po 24 B, ale mają dużą część pustą (marnują to miejsce)
- kiedy nowy router podłącza się do sieci, wysyła broadcastem RIP request i w ten sposób dostaje od sąsiadów ich [[Routing#Tablica routingu|tablice routingu]]
## Wady

- wolna [[Metryka#Zbieżność metryki|zbieżność]]
- [[Protokół RIP#Zliczanie do nieskończoności|zliczanie do nieskończoności]]
- protokół [[Routing#Obsługa Adresacja bezklasowa Classless InterDomain Routing (CIDR) CIDR|klasowy]]
- nie obsługuje [[Adresacja bezklasowa|adresacji bezklasowej]] ani podsieci o zmiennej długości (stałej długości - w ograniczonym zakresie)
- generowanie dużo ruchu w sieci, używanie [[Adresacja w sieciach LAN#Broadcast|broadcastu]]
- brak autoryzacji i zabezpieczeń (cokolwiek na [[Port|porcie]] [[UDP]] 520 to [[Routing#Router|router]]; można ustawić ręcznie listę autoryzowanych sąsiadów dla routera, ale eliminuje to przewagę routingu dynamicznego)
- limit maksymalnej rozległości sieci - 15 skoków (żadna ścieżka nie może mieć więcej routerów po drodze)
- jedyna [[Metryka|metryka]] to liczba przeskoków, więc mając wybór: wolniejsza droga przez 1 router, szybsza droga przez 2 routery, RIPv1 wybierze to pierwsze
- co 30 sekund spada wydajność sieci przez wymianę informację przez routery:
- zmniejsza przepustowość lub gubi [[Protokół IP#Budowa Pakietu IP (Datagramu IP)|pakiety]]
- można inicjować routery w różnych momentach lub zmodyfikować interwał, np. losować między 15 a 45 sekund
- rozgłaszanie nie jest dobre dla każdej sieci:
	- Ethernet/FDDI są bezpołączeniowe, można rozgłaszać
	- ISDN/X.25 muszą najpierw zestawić połączenie, np. w X.25 przesłanie ledwie 2 pakietów RIP zajmuje aż 1 sekundę
	- można robić transmisję z potwierdzeniem i rozgłaszać tylko, kiedy absolutnie trzeba, ale wtedy trzeba zakładać osiągalność sąsiadów czy też przechowywać w routerze listę tras
- w sieciach niejednorodnych (np. mieszanka X.25 i Ethernet) przez prostą metrykę działa bardzo słabo
- problem z transmisją informacji o podsieci - jeżeli na jakimś interfejsie nie ma podsieci, to router nie prześle o niej informacji
# Zliczanie do nieskończoności

- występuje w protokołach z wektorem odległości (w szczególności w RIPie)
- problem: [[Routing#Router|router]] nie jest świadomy, że może być częścią trasy biegnącej przez niego samego
## Przykład

Mamy sieć A-B-C-D-E-F (po prostu linia połączonych routerów) z metryką liczby hopów jak
w RIPie.

Załóżmy, że sieć między A do B pada. Przez 180 sekund router B nie dostanie wiadomości
od A i usunie wpis o nim ze swojej tablicy routingu. Uznaje przez ten czas, że odległość
między nim a A to nieskończoność (jak w przypadku każdego nieznanego routera). Po 30
sekundach poinformuje o tym router C.

Podczas tych 30 sekund jednak może przyjść informacja od routera C, który dalej sądzi, że
z routerem A jest dalej wszystko w porządku. C mówi B, że “odległość od C do A to 2”.
Router B nie jest świadomy, że jest częścią takiej trasy. Porównuje odległości: od B do A
jest nieskończoność, od C do A jest 3 (2 do C + 1 za łącze B-C). Wpisuje więc do swojej
tablicy routingu, że odległość przez C do A to 3.

Router B w końcu wysyła wiadomość do C. C nadal uważa, że może dostać się do A przez
B. Nie interesuje go, że odległość jest gorsza, niż była (bo było 2, teraz jest 4), bo z
perspektywy C to jedyny sposób na dostanie się do A. C aktualizuje więc swój wpis:
odległość przez B do A to 4.

Poprzednie 2 akapity będą powtarzać się w nieskończoność, stąd nazwa “zliczanie do
nieskończoności”.
## Przeciwdziałanie zliczaniu do nieskończoności

- Poniżej typowe mechanizmy wykorzystywane w tym celu:
### Górne ograniczenie na odległość

- narzuca się maksymalną liczbę hopów, np. 15 dla RIPa
- kiedy router dostanie wiadomość z odległością 15, to odrzuca
- wada - wykrycie anomalii zajmuje pesymistycznie aż 16 * 30 s = 8 minut!
### Dzielony horyzont (split horizon):

- nie wysyła się informacji o dostępności danej sieci w kierunku tego routera, przez który prowadzi najlepsza trasa do tej sieci (czyli nie odsyłamy informacji do tego, od kogo ją dostaliśmy)
- zmniejsza prawdopodobieństwo zliczania do nieskończoności, ale go nie eliminuje
### Zatruwanie (poison reverse)

- modyfikacja dzielonego horyzontu, gdzie zamiast nie informować z powrotem, odsyłamy taką informację, ale z odległością ustawioną na nieskończoność, przykładowo:

![[Pasted image 20250120202133.png|center]]

- Mamy taką sieć i odległości, a zatem router Z wysyła pakiety do X przez router Y. Zwiększmy koszt ścieżki między Z a Y:

![[Pasted image 20250120202147.png|center]]

- Nastąpiłoby tutaj zliczanie do nieskończoności, bo Y nie widzi, że jest częścią ścieżki między Z a X.
- Teraz następuje zatruwanie, gdy Z wysyła wiadomość do swoich sąsiadów. Y widzi, że koszt do Z wzrósł do 1000 (połączenie zostało zatrute), więc za chwilę dostanie wiadomość od X o lepszej drodze do Z i jej użyje.
### Wstrzymywanie (holddown)

- kiedy router dostanie informacje, że jakaś znana wcześniej sieć jest niedostępna, włącza licznik (zwykle na 60 sekund)
- jeśli dostanie wiadomość **od tego samego routera**, że sieć jest dostępna, to wyłącza licznik
- jeśli dostanie wiadomość **od innego routera z lepszą trasą**, to wyłącza licznik
- jeśli dostanie wiadomość **od innego routera z gorsza trasą**, to ignoruje ją
- po upłynięciu licznika wpis dla danej sieci jest usuwany
### Odświeżanie wymuszone

- de facto nie przeciwdziała zliczaniu do nieskończoności, ale przyspiesza zbieżność
- pakiet RIP jest wysyłany natychmiast po zmianie, a nie w ramach zwyczajnych wiadomości co 30 sekund
- “natychmiast” to często kilka sekund, żeby nie zalać sieci takimi komunikatami
- wysyła się tylko informację o zmianach
# RIPv2


- pakiety mają dodatkowe informacje tam, gdzie w v1 były puste miejsca:
	- [[Podsieci#Maska podsieci|maska podsieci]]
	- numer domeny routingu - usunięty w RFC 1721
	- adres następnego routera (next hop)
- kompatybilny z RIPv1
- protokół bezklasowy, używa [[Adresacja bezklasowa#Classless InterDomain Routing (CIDR)|CIDR]] i [[Adresacja bezklasowa]]
- do rozgłaszania używa adresu [[Adres IPv4#Klasy adresowe|multicastowego 224.0.0.9]], co zmniejsza obciążenie sieci; nie trzeba ich też [[Routing|routować]], bo dotyczą tylko sieci lokalnej
- pozwala na użycie 20-bajtowego hasła używającego MD5
- **nie** eliminuje wszystkich wad RIPv1:
	- ryzyko [[Protokół RIP#Zliczanie do nieskończoności|zliczania do nieskończoności]]
	- prosta [[Routing#Metryka|metryka]]
# SIN routing

- Ship In the Night routing
- pozwala na konfigurację kilku niezależnych protokołów routingu na jednym [[Routing#Router|routerze]]
- wymaga definicji konwersji metryk protokołów, np. jak zamienić OSFP na RIP