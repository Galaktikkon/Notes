## Prędkość i czas propagacji

- prędkość propagacji - wielkość skończona
- max ok. 2/3 c (200 tys. m/s)
- czas propagacji - im większa odległość, tym większy, np. Europa - USA
- czas wyrażany przez **RTT** (Round-Trip-Time), czas na drogę tam i z powrotem
## Tłumienność

**Tłumienność** (ang. *attenuation*):
- amplituda sygnału maleje w czasie transmisji
- narzuca ograniczenia na długość [[Media komunikacyjne|medium]] (przed potrzebą regeneracji sygnału)
- powody: 
	- szumy termiczne
	- nieidealna struktura materiału
	- przeniki i przesłuchy (zakłócenia zewnętrzne i wewnętrzne, EMI i [[Media komunikacyjne#Skrętka|crosstalk]])
	- pojemność i indukcyjność pasożytnicza (dla łączy miedzianych)
- zależy m. in. od:
	- rodzaj przewodnika
	- częstotliwość
	- temperatura
- wyrażana w $\text{dB/km}$, zgodnie ze wzorami:
	- $10 \cdot log(\text{P1}/\text{P2}) [\text{dB}]$; $\text{P1}, \text{P2}$ - moce $[\text{W}]$
	- $20 \cdot log(\text{V1}/\text{V2}) [\text{dB}]$; $\text{V1}, \text{V2}$ - napięcia $[\text{V}]$
- przykładowo:
	- 2-krotny spadek mocy: 3 dB
	- 10-krotny spadek mocy: 10 dB
	- 100-krotny spadek mocy: 20 dB

**Wzmocnienie** - przeciwieństwo tłumienności, w powyższych wzorach trzeba zrobić $\text{P2/P1}$ i $\text{V2/V1}$.

## Zniekształcenia sygnału

Zniekształcenia sygnału:
- dzielą się na zewnętrzne (spoza kabla) i wewnętrzne (od innych elementów kabla)
- zewnętrzne:
	- **EMI** (ElectroMagnetic Interference) oraz **RFI** (Radio-Frequency Interference)
	- powodowane przez indukcję elektromagnetyczną, promieniowanie elektromagnetyczne, fale radiowe
	- pochodzą np. od innych sieci bezprzewodowych lub telefonicznych, pobliskich przewodów elektrycznych, innych kabli sieciowych
- wewnętrzne:
	- powodowane przez inne kable w skrętce (w pewnym sensie każda para kabli w skrętce powoduje zewnętrzne **EMI** dla pozostałych par kabli)
	- **NEXT** (Near End CrossTalk) - zakłócenie na innych parach mierzone po stronie nadajnika (bliski koniec kabla, stąd Near End); im wyższa wartość (w dB), tym lepiej - tym mniej crosstalku jest otrzymywane przez inne kable; kategorie skrętek mają określone wymagania co do minimalnej wartości **NEXT**
	- **FEXT** (Far End CrossTalk) - jak wyżej, ale mierzone po drugiej stronie kabla (po stronie odbiornika), mniejsze znaczenie niż **NEXT**
- przeciwdziałanie:
	- ekranowanie medium - zwiększa koszt
	- wzajemne znoszenie zakłóceń
	- właściwy sposób prowadzenia okablowania

## Stopa błędu

- wskaźnik prawdopodobieństwa wystąpienia przekłamania bitu informacji
- Bit Error Rate (BER) - współczynnik ilości bitów błędnie otrzymanych do ogólnej liczby otrzymanych bitów
- przewodowo - nawet tylko $10^{-13}$
	- na $10^{-13}$ bitów 1 będzie wadliwy
- bezprzewodowo - nawet aż $10^{-3}$
	- na 1000 bitów 1 ulega przekłamaniu
	- musi być to uwzględniane przez protokoły

