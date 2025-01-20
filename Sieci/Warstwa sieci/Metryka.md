
- funkcja licząca odległość dwóch punktów (np. hostów) od siebie
- wartość metryki jest używana przy wybieraniu trasy pakietu
- może uwzględniać:
	- liczbę hopów (routerów po drodze)
	- [[Przesyłanie informacji#Throughput|przepustowość]] łączy
	- obciążenie łączy
	- niezawodność łączy
	- koszt łączy
- charakterystyczna dla danego protokołu (routingu)

## Podział metryk

![[Pasted image 20250120215910.png|center]]
### Metryki elementarne

Wyróżniamy:

#### Opóźnienie

- $D$ - delay
- jednostka: 
	- dziesiątki $μs$
- suma opóźnień między [[Routing#Router|routerem]] a adresem docelowym
#### Przepustowość

- $B$ - [[Przesyłanie informacji#Bandwidth|bandwidth]]
- jednostka: 
	- liczba sekund potrzebna na przesłanie 10 miliardów bitów, tzn.:$$\frac{10^7}{(\text{przepustowość w kb}/s)}$$
- najmniejsza z przepustowości między routerem a adresem docelowym
- w przypadku uzyskania ułamków metryka jest zaokrąglana w górę do najbliższej liczby całkowitej
#### Niezawodność

- $R$ - reliability
- jednostka: 
	- procent zapisany na 8 bitach (1 - 0%, 255 - 100%)
- mierzy się przez 5 minut
- stopień pewności, że pakiet dotrze do celu
- % pakietów odrzuconych przez router przez przepełnienie bufora
#### Obciążenie

- $L$ - load
- jednostka: 
	- procent zapisany na 8 bitach (1 - 0%, 255 - 100%)
- mierzy się przez 5 minut
- stopień największego obciążenia łącza na ścieżce
- % zapełnienia bufora routera
#### Routery

- $H$ - hops
- liczba routerów na ścieżce
#### [[Protokół IP#Fragmentacja pakietów IP|MTU ścieżki]]

## Zbieżność metryki

- gwarancja, że po pewnym czasie t (czasie zbieżności) wszystkie routery będą “widziały” taką samą sieć. Ważne np. przy zmianie [[Sieci lokalne#Topologie|topologii]].
- Protokół jest szybciej zbieżny od innego, jeżeli jego czas zbieżności jest krótszy. Szybciej zbieżny = lepiej. 
- Stan ustalony - stan sieci, w którym wszystkie [[Routing#Router|routery]] mają taki sam obraz sieci