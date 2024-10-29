# Idea

![[Pasted image 20241029232340.png|center]]

- **VLAN** (*Virtual Local Area Network*)
- służą do logicznego grupowania urządzeń za pomocą [[Urządzenia warstwy łącza danych|urządzeń warstwy 2]]
- pojęcie czysto logiczne, nie rusza fizycznej warstwy sieci
- jest szybsze i łatwiejsze, niż wykorzystywanie routerów (urządzeń warstwy 3)
- pozwalają np. podzielić sieć na dwie sieci bez fizycznego przełączania kabli i dodawania urządzeń

## Modele VLAN

Wyróżniamy dwa modele VLAN.
### Dostępowe

- Sposób definiowania filtracji ramek w celu ograniczenia komunikacji pomiędzy komputerami w jednej sieci LAN z mostkami (lub switchami)
### Niezależne

- Sposób użytkowania jednej fizycznej sieci jako wielu niezależnych sieci

## Zalety

- zmniejszenie [[Segmentacja Sieci#Domena Rozgłoszeniowa|domeny rozgłoszeniowej]] ([[Ramka|ramki]] [[Adresacja w sieciach LAN#Broadcast|broadcastowe]] rozsyłają się po mniejszej części sieci i nie zajmują tyle ruchu)
- lepsza skalowalność
- większe bezpieczeństwo (można odseparować sieci)
- programowa (re)konfiguracja (nie trzeba fizycznie ruszać kabli)
## Wady

- koszt urządzeń do obsługi i konfiguracji VLANów (bardziej historyczne)
- trudniejsze w konfiguracji (od wsadzania kabli do gniazdek, ale dalej łatwiej niż router)
- komunikacja między urządzeniami różnych VLANów jest trudna (zwykle wymaga routera lub switcha warstwy 3)
- mało zaawansowana standaryzacja

## VLANy a domeny:

- [[Segmentacja Sieci#Domena Kolizyjna|domena kolizyjna]]: nie mają żadnego wpływu (bo domena kolizyjna to pojęcie fizyczne, a VLANy są czysto logiczne)
- [[Segmentacja Sieci#Domena Rozgłoszeniowa|domena rozgłoszeniowa]]: każdy VLAN to osobna domena rozgłoszeniowa

