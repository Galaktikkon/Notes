- niższa: MAC (Media Access Control) - komunikacja z warstwą fizyczną
- wyższa: LLC (Logical Link Control) - komunikacja z warstwą sieciową
# Zadania podwarstwy MAC (Medium Access Control)

- zapewnia protokół dostępu do łącza pozwalający na poprawną transmisję danych
- obsługuje kolizje
- zapewnia adresację urządzeń i ich grup
- wykrywa i obsługuje błędy transmisji, np. oblicza i sprawdza sumy kontrolne, odrzuca błędne ramki
- formuje ramki, np. wstawia preambułę, dopełnia ramki do minimalnej długości
- wysyła i odbiera [[Ramka|ramki]]
# Zadania podwarstwy LLC (Logical Link Control)

- enkapsuluje dane otrzymane od warstwy sieciowej ($\downarrow$)
- dekapsuluje dane od podwarstwy MAC ($\uparrow$)
- dodaje informację o tym, jakiego protokołu używa warstwa sieciowa
- odczytuje informację o tym, jakiego protokołu warstwy sieciowej używał nadawca