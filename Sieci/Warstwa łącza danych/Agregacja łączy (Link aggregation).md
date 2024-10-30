- technika łączenia wielu kabli w jeden logiczny
- np. protokół LACP lub EtherChannel
- w kontekście [[Protokół drzewa rozpinającego (Spanning Tree Protocol, STP)|STP]] stanowi dla niego uzupełnienie lub alternatywę, bo pozwala usunąć cykle tworzone przez wielokrotne połączenia między tymi samymi switchami
- ma dodatkowo w stosunku do STP tę zaletę, że “sumuje” przepustowości kabli, tworząc nowe, bardziej wydajne połączenie (natomiast STP zignorowałoby wszystkie kable poza jednym, marnując je)
# EtherChannel

![[Pasted image 20241030014059.png|center]]

- składa się z pojedynczych połączeń zebranych jako jedno logiczne połączenie
- zapewnia full-duplexową [[Przesyłanie informacji#Bandwidth|szerokość pasma]] od 8 Gb/s do 80 Gb/s (Gigabit Etherchannel do 10-Gigabit EtherChannel) pomiędzy switchem, a hostem lub switche