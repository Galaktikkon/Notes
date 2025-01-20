Jest to jedno z głównych zadań warstwy 3 - znalezienie drogi (najlepszej) między dwoma hostami.

# Router

![[62-629684_network-router-clip-art-cisco-router-symbol 1.jpg]]

- urządzenie sieciowe pracujące w trzeciej warstwie. Służy do łączenia różnych sieci komputerowych (różnych w sensie informatycznym, czyli np. o różnych klasach, maskach itd.), pełni więc rolę węzła komunikacyjnego. Na podstawie informacji zawartych w pakietach TCP/IP jest w stanie przekazać pakiety z dołączonej do siebie sieci źródłowej do docelowej, rozróżniając ją spośród wielu dołączonych do siebie sieci. Proces kierowania ruchem nosi nazwę trasowania, routingu lub routowania.
# Routing źródłowy

- routing należący do nadawcy, wpisuje on adresy do nagłówka [[Protokół IP#Budowa Pakietu IP (Datagramu IP)|pakietu IP]], realizuje strict/loose source routing
- pakiet zna drogę powrotną
- routery nie muszą znać odległych sieci - mają tylko przekazywać dalej zgodnie ze wskazaniami pakietu
## Wady

- niewygodny
- ograniczony zasięg (bo pole “opcje” ma ograniczoną wielkość), ścieżki maksymalnie po 9 routerów
- rzadko wykorzystywany
- często blokowany przez administratorów (bo stanowi zagrożenie dla bezpieczeństwa sieci)

# Tablica routingu

- odwzorowanie postaci:
	- (IP sieci docelowej) $\rightarrow$ (IP następnego skoku lub “sieć bezpośrednia”)
- adresy IP = IP + [[Podsieci#Maska podsieci|maska]]
- IP następnego skoku = brama (next hop IP)
- tablica, dzięki której router wie, gdzie kierować pakiety zaadresowane do danej sieci
- pamięta tylko informację o następnym routerze na trasie do sieci (next hop IP) lub informację o tym, że sieć jest już przyległa do danego routera (nie zna całej ścieżki)
- wpisy w tablicy są tam:
	- ustawiane ręcznie przez administratora - routing statyczny
	- tworzone i zarządzane automatycznie przez protokół - routing dynamiczny
- jeżeli wpis nie zostanie znaleziony w tablicy, to realizuje się najdłuższe dopasowanie, czyli “wygrywa” ten adres docelowy w tablicy, który ma najdłuższą zgodną maskę podsieci
- maska 255.255.255.255 to "najlepsza" maska
- **brama domyślna** - maska 0.0.0.0, “najgorsza” maska, zostanie użyta, jeżeli nie znajdzie się żadne lepsze dopasowanie i jest w tablicy
- wpisy można **[[Routing#Agregacja wpisów|agregować]]**, łącząc wiele podobnych w jeden (np. jeśli wiele wpisów ma ten sam adres następnego skoku) i zaoszczędzić miejsce w tablicy routingu (pozwala na to zasada najdłuższego dopasowania)

# Routing statyczny

- wpisy w tablicy routingu są zarządzane ręcznie przez administratora
## Zalety

- łatwa konfiguracja (dla małych sieci)
- nie wymaga dodatkowych danych na łączu ani ich przetwarzania (w przeciwieństwie do [[Routing#Routing dynamiczny|dynamicznego]])
- przewidywalność – trasa pakietu jest znana i może być kontrolowana
## Wady

- nie skaluje się
- zmiany konfiguracji są trudne (zmiana może wymagać przekonfigurowania całej sieci)
- brak dostosowania do zmieniających się warunków w sieci
# Agregacja wpisów

- idea: niektóre wpisy są redundantne i można je zredukować
- trasy biegnące przez ten sam router można zgrupować, bo i tak ważny jest tylko **next hop** (następny router na trasie)
- dla każdego pakietu routery wybierają z tablicy routingu wpis o **najdłuższej pasującej [[Podsieci#Maska podsieci|masce]]**, więc wpisy można rozróżniać
## Trasa domyślna

- wpis 0.0.0.0/0
- hosty wysyłają tam pakiety, jeżeli nie było “lepszego” wpisu (pasującego, z dłuższą maską)
## Przykład

Mamy wpisy w tablicy routingu:

- 1.1.1.0/24 directly connected
- 1.2.3.0/30 via Serial0/0
- 1.4.0.4/30 via Serial0/0
- 10.0.23.0/24 via 1.1.1.2
- 10.4.8.0/21 via 1.1.1.2
- 10.4.8.0/24 via 1.1.1.42
- 10.3.0.128/25 via 1.1.1.3
- 193.193.65.224/27 via 1.1.1.42
- 2.0.0.1/32 via 1.1.1.42

Na początek można pogrupować wpisy według next hopu, tzn. adresu, który jest po “via”:
1. 
	- 1.1.1.0/24 directly connected
2. 
	- 1.2.3.0/30 via Serial0/0
	- 1.4.0.4/30 via Serial0/0
3. 
	- 10.0.23.0/24 via 1.1.1.2
	- 10.4.8.0/21 via 1.1.1.2
4. 
	- 10.4.8.0/24 via 1.1.1.42
	- 193.193.65.224/27 via 1.1.1.42
	- 2.0.0.1/32 via 1.1.1.42
5. 
	- 10.3.0.128/25 via 1.1.1.3

Teraz każdą grupę można połączyć, biorąc kolejne zgodne liczby; kiedy w ramach jednej
grupy zaczną się różnić, to do końca idą same zera. Można także skrócić maski podsieci do
liczby zgodnych bitów.

1. 
	- 1.1.1.0/24 directly connected
	Nie ma co upraszczać, zostaje bez zmian.
2. 
	- 1.2.3.0/30 via Serial0/0
	- 1.4.0.4/30 via Serial0/0
	
	Zgadza się pierwsza liczba (pierwszy bajt = 8 bitów). Trzeba sprawdzić, ile bitów drugiego
	bajtu się zgadza.
	
	$2_{10} = 00000010_2$
	$4_{10} = 00000100_2$
	
	Zgadza się pierwsze 5 bitów, więc w sumie 8+5=13 bitów, więc długością maski będzie 13.
	Zgrupowany adres: 1.0.0.0/13 via Serial0/0
3. 
	- 10.0.23.0/24 via 1.1.1.2
	- 10.4.8.0/21 via 1.1.1.2
	Zgadza się pierwsza liczba (8 bitów). Z drugiej liczby zgadza się pierwsze 5 bitów, więc
	maska będzie miała 13 bitów.
	
	$2_{10} = 00000010_2$
	$4_{10} = 00000100_2$
	
	Zgrupowany adres: 10.0.0.0/13 via 1.1.1.2
4. 
	- 10.4.8.0/24 via 1.1.1.42
	- 193.193.65.224/27 via 1.1.1.42
	- 2.0.0.1/32 via 1.1.1.42
	
	Nie zgadza się nic, więc trzeba będzie zrobić bramę domyślną.
	Zgrupowany adres to 0.0.0.0/0 via 1.1.1.42
5. 
	- 10.3.0.128/25 via 1.1.1.3
	
	Nie ma co upraszczać, zostaje bez zmian.

Grupy po uproszczeniu to więc:

- 1.1.1.0/24 directly connected
- 1.0.0.0/13 via Serial0/0
- 10.0.0.0/13 via 1.1.1.2
- 0.0.0.0/0 via 1.1.1.42
- 10.3.0.128/25 via 1.1.1.3

Pojawia się pewien problem - grupowanie w ten sposób może sprawić, że wpisy będą
zawierać w sobie wpisy, które wylądowały w innej grupie (a więc miały inny next hop,
“via”).

Wpis 10.4.8.0/24 via 1.1.1.42 wylądował pierwotnie w grupie 3 (dla routera 1.1.1.42),
natomiast po zgrupowaniu zawiera go grupa 10.0.0.0/13 (bo zgadza się początek).

Grupa 0.0.0.0/0 via 1.1.1.42 zawiera w sobie wszystkie pozostałe grupy.


Rozwiązanie problemu dla wpisu 10.4.8.0/24 via 1.1.1.42 jest prostsze, ponieważ
korzystamy z zasady, że bardziej szczegółowy wpis (z dłuższą maską) jest ważniejszy.
Dlatego też wystarczy z powrotem wyodrębnić ten jeden adres i wszystko będzie działać,
bo /24 będzie miało pierwszeństwo nad /13.

Po tym kroku grupy to:
- 1.1.1.0/24 directly connected
- 1.0.0.0/13 via Serial0/0
- 10.0.0.0/13 via 1.1.1.2
- 10.4.8.0/24 via 1.1.1.42 <- wyodrębniony wpis
- 0.0.0.0/0 via 1.1.1.42
- 10.3.0.128/25 via 1.1.1.3

Wpis 0.0.0.0/0 via 1.1.1.42 nie stanowi problemu ze względu na tę samą zasadę, z której
skorzystaliśmy powyżej - ma maskę /0, a więc najmniej ważną, bo jest trasą domyślną.
Ten router i wpis zostaną użyte tylko, jeżeli żaden inny wpis nie będzie pasował, więc
chociaż teoretycznie zawiera pozostałe grupy, to w praktyce to bez znaczenia. Możemy
więc pozostawić tablicę routingu tak, jak jest.

# Metryka

- funkcja licząca odległość dwóch punktów (np. hostów) od siebie
- wartość metryki jest używana przy wybieraniu trasy pakietu
- może uwzględniać:
	- liczbę hopów (routerów po drodze)
	- [[Przesyłanie informacji#Throughput|przepustowość]] łączy
	- obciążenie łączy
	- niezawodność łączy
	- koszt łączy
- charakterystyczna dla protokołu
## Zbieżność metryki

- gwarancja, że po pewnym czasie t (czasie zbieżności) wszystkie routery będą “widziały” taką samą sieć. Ważne np. przy zmianie [[Sieci lokalne#Topologie|topologii]].
- Protokół jest szybciej zbieżny od innego, jeżeli jego czas zbieżności jest krótszy. Szybciej zbieżny = lepiej. 
- Stan ustalony - stan sieci, w którym wszystkie [[Routing#Router|routery]] mają taki sam obraz sieci

# System autonomiczny

- Fragment sieci nadzorowany przez spójną władzę administracyjną.
- Zbiór routerów korzystających z tego samego protokołu routingu dynamicznego.
- Identyfikowane przez numery nadawane przez **RIR** (Regional Internet Registries).
### Zalety

- Hierarchiczna struktura.
- Skalowalność.
- Zmniejsza wielkość [[Routing#Tablica routingu|tablic routingu]].
- Przyspiesza wyznaczanie tablic routingu.
# Routing dynamiczny

- informacje routingu są utrzymywane przez protokół
## Zalety

- zawartość tablic routingu jest na bieżąco dostosowywana do warunków w sieci
- dobrze skalowalny
- łatwość konfiguracji (w stosunku do wielkości sieci)
## Wady

- obciąża sieć informacjami potrzebnymi protokołowi routingu
- bezpieczeństwo - informacje można podsłuchać
- ciężko skonfigurować pracę między różnymi protokołami
- większa złożoność działania sieci, protokoły są skomplikowane

## Podział routingu dynamicznego

- Obszar zastosowania
	- wewnętrzne - w systemie autonomicznym
	- zewnętrzne - pomiędzy systemami autonomicznymi
- Sposób działania
	- wektor odległości, np. [[Protokół RIP|RIP]], IGRP, EIGRP
	- stan łącza, np. OSPF, IS-IS
### Obsługa [[Adresacja bezklasowa#Classless InterDomain Routing (CIDR)|CIDR]]

- klasowe - np. [[Protokół RIP#RIPv1|RIPv1]], IGRP
- bezklasowe - np. [[Protokół RIP#RIPv2|RIPv2]], EIGRP, OSPF, IS-IS
### Aktywność

- proaktywne - szukają drogi do celu jeszcze zanim będzie potrzebna
- reaktywne - szukają drogi do celu dopiero w chwili, kiedy będzie potrzebna

## Protokoły wewnętrzne vs zewnętrzne

### Wewnętrzne

- stosowane wewnątrz jednej domeny administracyjnej
- proste, mało obciążają routery
- mało skalowalne
- np. [[Protokół RIP|RIP]], IGRP, OSPF
### Zewnętrzne

- wymiana informacji między niezależnymi administracyjnie sieciami
- dobrze skalowalne, łatwo obsługują duże sieci
- skomplikowane, wymagają przesyłu dużej ilości dodatkowych informacji
- np. EGP (Exterior Gateway Protocol), BGP (Border Gateway Protocol)
## Cechy protokołów routingu dynamicznego

- skalowalność
- [[Routing#Metryka|metryka]]
- czas osiągania stanu ustalonego
- bezpieczeństwo
- ilość wymaganego dodatkowego ruchu
- klasowość
## Protokoły dystans-wektor

- routery wysyłają sąsiadom informacje o wszystkich znanych sobie sieciach
- dystans - jak daleko jest do tych sieci (w sensie metryki)
- wektor - jak można się do niej dostać (do jakiego routera się kierować)
- wektor to prawie zawsze “wyślij do mnie, ja wiem, gdzie dalej to wysłać”, rzadko inny router (np. gdy router docelowy obsługuje tylko jakiś inny protokół)
- algorytm oparty na algorytmie Bellmana-Forda

## Algorytm protokołów dystans-wektor

- oparty analogicznie do algorytmu Bellmana-Forda na relaksacji, czyli zmniejszaniu odległości między wierzchołkami w kolejnych krokach
- właściwości:
	- rozproszony - każdy router wysyła swoje informacje tylko do bezpośrednich sąsiadów
	- iteracyjny - występuje w kolejnych iteracjach, aż nastąpi brak wymiany informacji (może potem działać okresowo lub np. przy wykryciu zmiany topologii)
	- asynchroniczny - nie wymaga synchronizacji między routerami
	- zbieżny - w skończonej liczbie kroków osiągnie rozwiązanie, jeżeli sieć jest spójna
- wzór na odległość:
$$ 
	D(X, Y) = \text{min}_{Z \in N(X)}\{c(X,Z)+D(Z, Y)\} 
$$
- gdzie:
$$
D(X, Y) - \text{odległość z $X$ do $Y$}
$$
$$
N(X) - \text{sąsiedzi $X$}
$$
$$
c(X, Z) - \text{odległość bezpośrednia z $X$ do $Z$}
$$

- dokładna metryka zależy od protokołu, ale ogólny wzór pozostaje taki, jak powyżej (będzie się zmieniać wzór na $c(X, Z)$)
- algorytm:
	1. Inicjalizacja: routery mają puste tablice routingu, odległość do każdego odbiorcy jest przyjmowana jako nieskończoność
	2. Routery otrzymują informacje od swoich sąsiadów
	3. Porównują odległość do określonego odbiorcy z dotychczas znaną; jeżeli jest mniejsza, to aktualizują odległość oraz router, od którego otrzymano lepszą ścieżkę

### Przykład:

![[Pasted image 20250120201148.png|center]]

Mamy daną taką sieć. Routery na początku znają tylko swoich bezpośrednich sąsiadów.
Jako metrykę przyjmujemy liczbę hopów (routerów po drodze), jak w protokole RIP.
Kolejne routery wysyłające informacje będą wybierane przypadkowo (zgodnie z własnością
asynchroniczności)

![[Pasted image 20250120201207.png|center]]

Router U wysyła swoją wiadomość z odległością zwiększoną o 1.

![[Pasted image 20250120201226.png|center]]

Po przesłaniu router Y dowiedział się o sieci “a”, a router V o sieci “c”. Najkrótsza droga
do tych sieci (i póki co jedyna znana) prowadzi przez router U (i tylko przez niego, więc
odległość to 1).

![[Pasted image 20250120201245.png|center]]

Routery V i Y rozsyłają znane sobie informacje. Sieci przyległe do nich (np. c i f dla
routera Y) mają odległości 1, natomiast te poznane dzięki routerowi U (np. a dla routera
Y) odległość 1+1=2.

![[Pasted image 20250120201605.png|center]]

Router U dowiedział się o sieciach “b” i “d” od routera V oraz o sieci “f” od routera Y.
Pozostałe routery na analogicznej zasadzie nauczyły się od swoich sąsiadów.

![[Pasted image 20250120201624.png|center]]

![[Pasted image 20250120201633.png|center]]

![[Pasted image 20250120201642.png|center]]

![[Pasted image 20250120201652.png|center]]

![[Pasted image 20250120201703.png|center]]

Sieć znajduje się już w stanie ustalonym. Wszystkie routery dowiedziały się o wszystkich
sieciach, tzn. przez jakie routery można dojść do tych sieci (co dalej, to już ich nie
obchodzi).

