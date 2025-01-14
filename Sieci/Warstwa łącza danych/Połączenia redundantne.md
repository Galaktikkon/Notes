- Czy dokładnie jedna ścieżka pomiędzy dowolnymi dwoma hostami w sieci jest wystarczająca?
# Problem

- Standard [[Ethernet]] nie pozwala na istnienie naraz więcej niż jednej ścieżki od punktu A do punktu B
- Dlaczego? Może wystąpić krążenie [[Ramka|ramek]] w sieci

![[Pasted image 20241028024413.png|center]]

- Rozwiązanie: dynamiczna adaptacja do warunków panujących w sieci, czasowa dezaktywacja namiarowych połączeń, z sieci cyklicznej należy zrobić [[Protokół drzewa rozpinającego (Spanning Tree Protocol, STP)|graf spójny acykliczny (drzewo)]]