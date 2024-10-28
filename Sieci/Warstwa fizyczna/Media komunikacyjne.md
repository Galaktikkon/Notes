
# Podział mediów transmisyjnych:

Media transmisyjne dzielimy na:
- przewodowe:
	- kable miedziane:
		- [[#Skrętka|skrętka]]: ekranowana, nieekranowana
		- [[#Kabel koncentryczny|kabel koncentryczny (koncentryk)]]: cienki, gruby
	- [[#Światłowód|światłowody]]:
		- jednomodowe
		- wielomodowe
- [[#Media bezprzewodowe|bezprzewodowe:]]
	- radiowe (np. Wi-Fi, Bluetooth)
	- line-of-sight (np. podczerwień IrDA)

# Kable

**Zależność:** długość $\cdot$ [[Przesyłanie informacji#Throughput|przepustowość]] = $const$ (dla [[#Skrętka|skrętek]] i [[#Światłowód|światłowodów]])
- jeżeli dwukrotnie zmniejszymy tor transmisji danych, to *teoretycznie* pozwala nam to zwiększyć przepustowość dwukrotnie

## Oznaczenia kabli:

- np. 100Base-TX, 10GBase-T
- liczba - prędkość transmisji w Mbps lub Gbps (jeżeli jest G)
- Base - baseband signaling
- T, TX, FX, T4 - oznaczenie medium i sposobu jego użycia, np. F to [[#Światłowód|światłowód]]

## Kabel koncentryczny

Kable koncentryczne (koncentryki):
- 2 współosiowe kable rozdzielone izolatorem
- budowa (idąc od środka):
	- kabel miedziany
	- izolacja plastikowa
	- pleciony kabel miedziany (działa jak ekran)
	- zewnętrzny izolator
- przewód zewnętrzny działa jak ekran, zmniejszając zewnętrzne zakłócenia elektromagnetyczne
- muszą być uziemione, inaczej mamy zakłócenia, bo robi antenę (ale trzeba uważać na to, żeby nie zrobić przewodu z 2 uziemień, tzw. ground loop!)
- budowa sieci:
	- kable łączą wtyki/złącza **BNC** (Bayonet Neill-Concelman)
	- łącze szkieletowe - kabel grupy ze stacjami ([[Osprzęt#|trójnik]] + [[Osprzęt#Transceiver (transmitter-receiver)|transceiver]])
	- w pomieszczeniach: kabel cienki i podłączone do niego stacje
	- na końcach terminatory (brak terminatora - odbicie sygnału, zakłóca)
- zalety:
	- pozwala na dłuższe segmenty niż [[#Skrętka|skrętka]]
	- lepsza od skrętki odporność na zakłócenia
-  wady:
	- niewygodny w użyciu i eksploatacji
	- łatwo wprowadzić zakłócenia nieprecyzyjnym montażem elementów łączących (np. trójników)
- gruby (żółty):
	- średnica 1/2 cala (1/2 * 2,54 cm)
	- maksymalna długość segmentu 500 m
	- stacje co wielokrotność 2,5 m
	- stacje podłączane za pomocą trójnika i transceivera
- cienki (czarny):
	- średnica 1/4 cala (1/4 * 2,54 cm)
	- maksymalna długość segmentu 185 m
	- stacje podłączane co najmniej co 0,5 m za pomocą trójnika, poza tym dowolnie
	- max 30 stacji
## Skrętka

Skrętka miedziana (ang. *twisted pair*):
- 8 kabli miedzianych skręconych w przewodzie w 4 pary
- każda para (2 kable) transmituje ten sam sygnał - dla samoredukcji zakłóceń (*cancellation*)
- najczęściej stosowania w [[Sieci lokalne|sieciach lokalnych]]
- dzieli się na kategorie według przepustowości (dopuszczalnej częstotliwości) i dopuszczalnego poziomu przesłuchów:
	- kat. 3: do 16 MHz ([[Ethernet]], ISDN, telefon), tłumienność przy 10 MHz 9,8 dB/km
	- kat. 4: do 20 MHz (Ethernet, Token Ring 16 Mb/s)
	- kat. 5: do 100 MHz (Fast Ethernet), do 100 m między urządzeniami, tłumienność przy 10 MHz 6,6 dB/km, przy 100 MHz 22 dB/km
	- kat. 5e: do 100 MHz (Gigabit Ethernet ze specjalnym kodowaniem), do 100 m między urządzeniami
	- kat. 6: do 250 MHz (Gigabit Ethernet), do 55 metrów dla standardu 10GBASE-T
	- kat. 6A: do 250 MHz (Gigabit Ethernet), do 100 metrów dla standardu 10GBASE-T
	- kat. 7: do 600 MHz
- nieekranowana:
	- UTP (Unshielded Twisted Pair)
	- mniej odporna na zakłócenia
	- po prostu splecione pary kabli w izolacji
	- tańsza, łatwiejsza w instalacji, częściej wykorzystywana
- ekranowana:
	- foliowana (FTP - Foiled Twisted Pair) ekranuje całość jednym ekranem (chroni przed zakłóceniami zewnętrznymi)
	- ekranowana (STP - Shielded Twisted Pair) ekranuje każdą parę z osobna, chroni przed crosstalkiem (wzajemne zakłócanie się par)
	- można łączyć powyższe, żeby ekranować i pary, i całość (S/FTP lub FTP/ScTP)
	- droższa, rzadziej wykorzystywana
- ekran trzeba uziemić, tak samo jak przy koncentryku

### Zakończenia skrętek

-  standardowa wtyczka 8P8C (czasami nazywa RJ-45, wtedy ma “klucz” z boku), ma 8 pinów i 8 styków, oznaczana też T-568A i T-568B
- Kolejność kabli we wtyczce![[Pasted image 20241015003158.png|center]]
- metoda zapamiętania: 4-5 to zawsze niebieski pełny i przerywany, 7-8 to zawsze brązowy przerywany i pełny. Późniejsze pary są przerywany-pełny: w A najpierw jest zielony (pomarańczowy na pozostałych miejscach), w B najpierw pomarańczowy (zielony na pozostałych miejscach)
- nieskrosowana/prosta (ang. *straight-through*):
	- po obu stronach jest albo A, albo B (te same końcówki)
	- dla łączenia różnych klas urządzeń, np. komputer-[[Urządzenia warstwy łącza danych#Switch|switch]]
- skrosowana/z przeplotem (ang. *crossover*):
	- po jednej stronie A, po drugiej B
	- zamienione pary RX (odbiór) i TX (nadawanie)
	- dla łączenia urządzeń tej samej klasy, np. switch-switch, komputer-router
- Auto MDI-X - funkcja, dzięki której wszystko jedno, jaki kabel podłączymy, urządzenia ogarną (wymagane dla Gigabit Ethernetu)

## Światłowód

Światłowód (ang. *optical fiber*):
- szklany przewód (*core*, włókno krzemowe), w którym odbija się wiązka światła; otoczony nieprzezroczystym płaszczem (*cladding*), dzięki czemu następuje całkowite wewnętrzne odbicie (nic nie ucieka na zewnątrz)
- światło jest wprowadzane pod kątem większym od kąta Brewstera, a w światłowodach wielomodowych też pod jak największym, na który pozwala kąt akceptacji (patrz niżej)
- stosowane w okablowaniu pionowym (między urządzeniami infrastruktury sieci)
- nadajnik: dioda laserowa lub **LED**
- odbiornik: fotodioda
- wymiary podaje się w formacie: *core diameter*/*cladding diameter* $[μm / μm]$

### Kąt akceptacji

- maksymalny kąt (w stosunku do osi rdzenia włókna), pod którym można wprowadzić światło do światłowodu tak, żeby było [[Problemy w transmisji danych#Prędkość i czas propagacji|propagowane]] (żeby zaszło zjawisko całkowitego wewnętrznego odbicia); większy = lepiej, bo można wprowadzić większą część światła i ulepszyć wielomodowość
- **aparatura numeryczna** - sinus kąta stożka akceptacji
### Okna transmisji

- 4 optymalne zakresy długości fal, dla których [[Problemy w transmisji danych#Tłumienność|tłumienność]] włókna światłowodowego jest mała; są po kolei - im wyższe okno, tym większa długość fal, mniejsza tłumienność i wyższy zasięg przed potrzebą regeneracji, ale też większa trudność techniczna (i koszt)![[Pasted image 20241015004855.png|center]]
	- **1. okno**: 850 nm (głównie dla światłowodów wielomodowych, **OM1**, **OM2**).
	- **2. okno**: 1310 nm (używane w sieciach jednomodowych, **OS1**).
	- **3. okno**: 1550 nm (używane w **WDM**, najmniejsze tłumienie).
	- **4. okno**: 1625 nm (stosowane w transmisjach długodystansowych).
### Zalety

- szybkość transmisji (> 100 Mb/s)
- odporny na zakłócenia elektromagnetyczne
- nie emituje zakłóceń elektromagnetycznych
- niska [[Problemy w transmisji danych#Tłumienność|tłumienność ]](ok. 0,5-1,5 dB/km)
### Wady

- drogie
- ograniczona możliwość zginania i łączenia (na zgięciach i łączeniach tracona jest energia)
- trudność instalacji
### Modowość

- jednomodowy:
	- **SMF**, Single-Mode Fiber
	- zasięg: do 70 km
	- nadajnik: dioda laserowa
	- pojedyncza wiązka świetlna (jeden mod światła)
	- wymiary: 9/125 μm
	- wymaga bardzo prostej ścieżki, bo inaczej zjawisko dyspersji przeszkadza w transmisji danych
	- głównie stosowane w sieciach **[[Wprowadzenie#Podział sieci komputerowych|WAN]]**
- wielomodowy:
	- **MMF**, Multi-Mode Fiber
	- zasięg: do 2 km
	- nadajnik: dioda **LED**, z tego powodu tańsze
	- wiele wiązek światła pod różnym kątem
	- wymiary: 62,5 / 125 μm
	- mniejszy zasięg, bo fale miejscami się nakładają i sygnał się rozmywa
	- ulepszany przez gęste zwielokrotnienie falowe, **DWDM**
	- głównie stosowane w sieciach **[[Sieci lokalne|LAN]]**
### WDM

- **WDM** (Wavelength Division Multiplexing) - wysyłanie w światłowodzie wielomodowym kilku wiązek światła o różnej długości fali (różnych “kolorach”, częstotliwości), co pozwala przesłać więcej danych naraz, niezależnie od siebie
	- **CWDM (Coarse WDM)**: Używa mniejszej liczby kanałów (max 16-18) o szerokiej separacji fali (np. 20 nm).
	- **DWDM (Dense WDM)**: Używa wielu kanałów (ponad 40, 80)na bardzo bliskich długościach fal (ok. 0,8 nm), co pozwala na przesyłanie ogromnych ilości danych.
### Podsłuchiwanie światłowodu

- zrywa się izolację zewnętrzną i zgina tak, żeby nie zachodziło całkowite wewnętrzne odbicie, promienie “wylatują” wtedy częściowo na zewnątrz i można je odsłuchać; łatwo wykryć, bo zmieniają się parametry transmisji
### EDFA (Erbium-Doped Fiber Amplifier)

- EDFA to wzmacniacz optyczny, który wzmacnia sygnały światłowodowe bez konieczności przekształcania ich na sygnały elektryczne. Używany w sieciach optycznych na długich dystansach, zwiększa zasięg transmisji przy minimalnych stratach.
### Ciemne włókno (Dark Fiber)

- Światłowód, który został zainstalowany, ale nie jest jeszcze aktywnie wykorzystywany do transmisji danych. Można go wydzierżawić do własnych celów - np. na potrzebę stworzenia lokalnej sieci światłowodowej.
# Media bezprzewodowe

- line-of-sight:
- np. podczerwień
- nadajnik i odbiornik muszą być w jednej linii bez przeszkód po drodze
- wbrew powszechnej opinii działają na duże odległości, po prostu ciężko wtedy o dobre warunki
	- duża odporność na interferencje magnetyczne
	- mała odporność na promieniowanie widzialne
	- diody **LED** lub laserowe
	- pasmo nielicencjonowane
- radiowe:
	- np. **Wi-Fi**, **Bluetooth**
	- używają często ogólnodostępnego pasma **ISM** (Industry, Science, Medical)
	- odbiornik i nadajnik nie muszą być w jednej linii
	- optymalnie: odbiornik i nadajnik naprzeciwko sobie jako anteny kierunkowe, brak przeszkód w elipsie (strefie Fresnela)
	- łatwiej uzyskać duży zasięg