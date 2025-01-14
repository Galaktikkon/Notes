- Mała rozległość (do kilku kilometrów)
- Wymagana duża przepustowość
- Wykorzystywane topologie:
	- [[#Magistrali|szyna]]
	- [[#Gwiazdy|gwiazda]], [[#Rozszerzonej gwiazdy|rozszerzona gwiazda]]
	- [[#Pierścienia|pierścień]]
- Stosowane media:
	- [[Media komunikacyjne#Kable|kable miedziane]]
	- [[Media komunikacyjne#Światłowód|światłowody]] (wielomodowe)
	- krótkozasięgowa łączność [[Media komunikacyjne#Media bezprzewodowe|bezprzewodowa]]
# Standardy sieci lokalnych (LAN)

Seria [[Wprowadzenie#Ważniejsze organizacje|IEEE]] 802:
- **802.3 - [[Ethernet]]**
- **802.4 - Token Bus**
- **802.5 - Token Ring**
- **802.11 - wireless LAN (WLAN)**
- **802.15 - PAN (Bluetooth)**

# Topologie

Każda, nawet najmniejsza sieć komputerowa, posiada **topologię fizyczną** oraz **logiczną**, które to definiują sposób połączenia urządzeń oraz to, w jaki sposób przesyłane są dane.
- **Topologia fizyczna** - sposób, w jaki fizycznie są połączone urządzenia.
- **Topologia logiczna** - sposób, w jaki propaguje się informacja, nie “widzi” topologii fizycznej.

Do **fizycznych topologii** sieci zaliczamy topologię:
- **Magistrali** (ang. Bus)
- **Pierścienia** (ang. Ring)
- **Gwiazdy** (ang. Star)
### Magistrali

![[Pasted image 20241015154252.png|center]]

- wszystkie urządzenia podłącza się do wspólnego medium transmisyjnego
- powszechnie używany był [[Media komunikacyjne#Kabel koncentryczny|koncentryk]]
- mała [[Przesyłanie informacji#Throughput|przepustowość]] (do 10Mb/s)
- duża podatność na awarię sieci (połączenie szeregowe)
- niewielki koszt wdrożenia
- sygnał odbierany przez wszystkich
### Pierścienia

![[Pasted image 20241015154534.png|center]]

- każde urządzenie podłączone jest z dwoma sąsiadami, tworząc zamknięty krąg
- mało kabli i dodatkowych urządzeń
- informacja od poprzednika do następnika
- podatność na awarię - tak jak w topologii magistrali
#### Podwójnego pierścienia (FDDI)

![[Pasted image 20241015184728.png|center]]

- podwojone okablowanie topologi pierścienia
- Fiber Distributed Data Interface
- używa 2 [[Media komunikacyjne#Światłowód|światłowodów]]
- transfer 100 Mb/s
- podobny do zwykłego pierścienia
- jeden pierścień jest pierwotny, drugi zapasowy (wtórny)
- bardziej odporny na uszkodzenia, bo po uszkodzeniu jednego pierścienia przechodzi na drugi
### Gwiazdy 

![[Pasted image 20241015154239.png|center]]
- układ sieci, gdzie mamy centralne urządzenie (punkt dystrybucyjny, np. [[Urządzenia warstwy łącza danych#Switch|switch]], hub), z którego wyprowadza się połączenia do innych urządzeń, np. komputerów.
####  Rozszerzonej gwiazdy
- topologia gwiazdy, gdzie z centrum mamy połączenie do mniejszych gwiazd, zamiast bezpośrednio do komputerów

## Przykłady topologi LAN

| **Standard** |       **Topologia fizyczna**       | **Topologia logiczna** |
| :----------: | :--------------------------------: | :--------------------: |
|   Ethernet   | Magistrala / (rozszerzona) gwiazda |       Magistrala       |
|  Token Ring  |        Pierścień / gwiazda         |       Pierścień        |
|     FDDI     |         Podwójny pierścień         |        Gwiazda         |

