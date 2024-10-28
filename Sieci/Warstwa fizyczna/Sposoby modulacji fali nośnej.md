- można modulować:
	- amplitudę - Amplitude Modulation, AM
	- fazę - Phase Modulation, PM
	- częstotliwość, Frequency Modulation, FM
# Modulacja AM

- używana, gdy mamy sygnał szerokopasmowy o małej częstotliwości (informacyjny)
- realizuje kodowanie jako chwilowe zmiany amplitudy sygnału nośnego
- idea: mamy sygnał bazowy (carrier) i modulujący (message), dodajemy je do siebie i to transmitujemy; zmieniamy amplitudę (bo dodawaliśmy fale), ale nie częstotliwość (fale są tak samo gęste)
- 0 ma inną amplitudę (np. zerową), a 1 inną (np. jakąś falę o stałej amplitudzie)
- użyta do sygnałów cyfrowych nazywa się amplitude-shift keying (ASK)
- wynik modulacji to sygnał wąskopasmowy, nadający się do transmisji
- problem: zakłócenia po drodze są uwzględniane u odbiorcy

![[Pasted image 20241028003029.png|center]]
# Modulacja FM

- realizuje kodowanie jako chwilowe zmiany częstotliwości sygnału nośnego
- idea: tam, gdzie message ma niską częstotliwość sygnał jest rzadszy, tam gdzie wysoka, jest gęstszy
- pozwala odbiornikowi na odfiltrowanie znacznie więcej zakłóceń niż przy [[#Modulacja AM|AM]]
- użyta do sygnałów cyfrowych nazywa się frequency-shift keying (**FSK**)
- idea: mamy 2 różne częstotliwości, gdzie jedna (rzadsza) reprezentuje 0, a druga (gęstsza) reprezentuje 1, np. gdy mamy sinusoidalną falę nośną, to 0 ma taką samą częstotliwość (taki sam kształt fali), a 1 ma dwa razy większą częstotliwość (fale są dwa razy gęściej ułożone)

![[Pasted image 20241028003041.png|center]]

# Modulacja PM

- realizuje kodowanie jako chwilowe zmiany fazy sygnału nośnego
- demodulacja: zamiana na modulację FM, a potem normalna demodulacja
- szeroko stosowana w transmisji cyfrowej, np. bezprzewodowy LAN, RFID, Bluetooth
- używa określonej ilości faz, gdzie każda koduje unikatową serię bitów (symbol)