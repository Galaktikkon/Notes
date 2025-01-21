# Kodowanie fizyczne
**[[Metody kodowania fizycznego|Kodowanie fizyczne]]** - doprowadzenie informacji do postaci możliwej do przesłania w danym medium, np.:
- [[Media komunikacyjne#Kable|kable miedziane]]: zmiana napięcia
- [[Media komunikacyjne#Światłowód|światłowód]]: transmisja światła lub jej brak
- bezprzewodowo lub analogowo (modem): [[Sposoby modulacji fali nośnej|modulacja fali nośnej]]
	- AM (amplitude modulation) - modulacja amplitudy
	- FM (frequency modulation) - modulacja częstotliwości
	- PM (phase modulation) - modulacja faz
# Kodowanie logiczne
**[[Metody kodowania logicznego|Kodowanie logiczne]]** - doprowadzenie informacji do postaci odpowiedniej do przesłania w konkretnych warunkach, zapisywane jako $x\text{B}/y\text{Q}$, czyli:
- $x$ bitów
- reprezentowane jako $y$ sygnałów
- o wartościowości $Q$ (określona wartość ilości poziomów sygnału)
	- $\text{B}$ - binarne wartości, $\text{T}$ - trójwartościowość
- przykładowo: 8B/6T
## Kodowanie blokowe
- dane dzieli się na bloki po $x$ bitów i zamienia się na y-bitowe symbole kodowe, $x < y$
- nadmiarowość:
	- części symboli można użyć do funkcji kontrolnych
	- wybiera się symbole o dużej zmienności
- wymagają wyższej częstotliwości
- używane np. w światłowodach wraz z fizycznym kodowaniem **NRZ**
- stosowane kody:
	- 4B/5B, używane np. w FDDI, Fast Ethernet 100Base-TX (skrętka i światłowód)
	- 8B/10B, używane np. w Gigabit Ethernet 1000Base-TX (skrętka i światłowód)
# Samosynchronizacja
**Samosynchronizacja** - pozwala na radzenie sobie bez dodatkowych mechanizmów (np. wysyłania sygnału zegarowego) z sytuacją, w której można mieć potencjalnie długą sekwencję sygnału o stałym poziomie, gdzie mogłaby się zatrzeć granica między kolejnymi fragmentami (np. ciągle stan 1).

