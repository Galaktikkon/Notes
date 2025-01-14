# Tryby Transmisji Danych

- ***simplex*** - transmisja jest możliwa tylko w jedną stronę
	- ulica jednokierunkowa
- ***half-duplex*** - transmisja możliwa jest w obie strony, ale w danym czasie tylko w jedną
	- mijanka na remontowanym moście
	- pierwszy [[Ethernet]]
- ***duplex*** - **równoczesna** transmisja w obie strony
	- ulica dwukierunkowa
	- współczesny [[Ethernet]]

# Jednostki

**Bod (baud)** - *signaling rate*, miara prędkości przesyłania zmian [[Media komunikacyjne|medium transmisyjnego]] z wykorzystaniem określonej modulacji lub [[Kodowanie Informacji]]. Każdy symbol (zmiana medium) reprezentuje 1 lub więcej bitów danych. **NIE** jest to prędkość transmisji danych!

**Bit** - ilość informacji, którą uzyskujemy w wyniku otrzymania, że spośród dwóch jednakowo prawdopodobnych zdarzeń zaszło jedno.

$\text{M}$  - liczba poziomów sygnału
$\text{m}$  - liczba bitów na sygnał
$\text{m}  = log_2{\text{M}}$
$\text{R}_{\text{S}}$  - *signaling rate* $[\text{baud}]$, prędkość zmian medium
$\text{R}$  - bit rate $[\frac{b}{s}]$, prędkość transmisji, liczba bitów przekazywanych w ciągu sekundy
$\text{R} = \text{R}_{\text{S}} \cdot m$

# Bandwidth

Szerokość pasma (ang. *bandwidth*) - maksymalna **teoretyczna** przepustowość sieci w $[\frac{b}{s}]$.

# Throughput

Przepustowość (ang. *throughput*) - aktualne możliwości sieci w zakresie przesyłania danych, mniejsza lub równa szerokości pasma, w $[\frac{b}{s}]$. Zależy między innymi od:
- wydajności sprzętu - zarówno komputerów końcowych jak i elementów pośredniczących
- obciążenia sieci - jak bardzo aktywni są jej użytkownicy
- typu danych - przede wszystkim narzutu na pola kontrolne

# Baseband 

- rodzaj komunikacji, w którym całego pasma ([[#Bandwidth|bandwidth]]) używa jeden sygnał (kanał). Wykorzystywany jest wąski zakres częstotliwości. Normalnie jest [[#Tryby Transmisji Danych|half-duplexowy]], ale komunikujące się urządzenia dostają szczeliny czasowe (TDM, Time-Division Multiplexing), żeby mieć full duplex. Tego używa [[Ethernet]].

# Broadband 

- rodzaj komunikacji, w którym używa się szerokiego spektrum częstotliwości, gdzie multiplexing osiąga się przez przydzielenie różnym urządzeniom różnych częstotliwości (FDM, Frequency-Domain Multiplexing).