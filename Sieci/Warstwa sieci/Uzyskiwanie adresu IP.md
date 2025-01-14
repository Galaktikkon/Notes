- dwie opcje
	- statyczne - urządzenia mają ręcznie przypisane [[Adres IPv4|adresy IP]]
	- dynamiczne - sieć sama zajmuje się przydzielaniem adresów IP nowym urządzeniom

# Statyczne przydzielanie adresu IP

- administrator sam ręcznie przypisuje wszystkim urządzeniom w sieci adresy IP
## Zalety

- prostota (przy małych sieciach)
## Wady

- słaba skalowalność
- podatność na błędy konfiguracji
- trzeba znać z góry urządzenia, które będą w sieci (lub ręcznie rekonfigurować przy dodawaniu nowych)
- niewydajne wykorzystanie adresów - nawet, jeżeli urządzenie jest rzadko używane, to musi mieć przypisany na stałe adres i zajmuje go w puli

# Dynamiczne przydzielanie adresu IP

- sieć posiada protokół odpowiadający za przydzielanie adresów IP, np. [[Protokół DHCP|DHCP]]
## Zalety

- automatyczna konfiguracja nowych urządzeń
- scentralizowane zarządzanie (serwer z pulą adresów)
- możliwość podłączenia do sieci urządzeń, które nie mają nigdzie zapisanej własnej konfiguracji (np. urządzenia bezdyskowe)
- efektywne wykorzystanie adresów - tylko używane w danej chwili urządzenia mają przydzielone adresy z puli, reszta jest wolna do użycia
## Wady

- serwer może się zepsuć - trzeba zadbać o rozwiązania zapasowe
- chcąc, żeby adres urządzenia był stały, trzeba to ręcznie skonfigurować w serwerze
- brak autoryzacji, niepowołana osoba może otrzymać adres