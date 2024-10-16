**Trójnik (tap)** - rozgałęzienie, w którym główny kabel biegnie dalej, ale wychodzi z niego dodatkowe, trzecie wyjście (np. tutaj podłączamy komputer).

**Szafa sieciowa (*rack*)**
- szerokość w calach
- wysokość w **U** (1,75 cala)
- głębokość w milimetrach


# Urządzenia Aktywne 

Urządzenia aktywne **wymagają** prądu do działania.

**Regeneracja sygnału** - odtworzenie na wyjściu urządzenia regenerującego cech charakterystyki otrzymanego na wejściu sygnału. Pozwala to powiększyć rozległość sieci, bo radzi sobie z zanikaniem i zniekształcaniem sygnału. Powiększa to też domenę kolizyjną sieci.

**Domena kolizyjna** - fragment sieci, w którym tylko jedno urządzenie naraz może transmitować (jak sekcja krytyczna, tylko w sieciach). Granicę wyznaczają bridge, switch, router albo repeater. Urządzenia z jednej domeny kolizyjnej rywalizują o dostęp do medium pozwalającego z niej transmitować dane (“wyjść” z niej)
## Transceiver (transmitter-receiver):

- sprzęt aktywny (wymaga prądu), układ nadawczo-odbiorczy
- konwertuje sygnał między różnymi [[Media komunikacyjne|mediami transmisyjnymi]]
- izoluje elektrycznie stację i medium (spali się transceiver, a nie urządzenie)
- wykrywa kolizje w medium
- z ich pomocą buduje się repeatery (regeneratory sygnału)
- **jabber control** - transceivery [[Ethernet|Ethernetowe]] ograniczają czas nadawania do 150ms, więc nie wyśle się nic powyżej ok. 1500 bajtów w tym czasie; chroni przed wysyłaniem “jabber frames”, czyli zbyt dużych ramek (limit Ethernet - 1518 B)

## Repeater (wtórnik) 

Regenerator sygnału, transceiver używany do przywrócenia poprawnych cech sygnału (usunięcia zakłóceń itp.). Nie interpretują w żaden sposób danych (np. nie sprawdzają spójności), działają w [[Zadania warstwy fizycznej|1 warstwie modelu]] [[Modele warstwowe#Warstwy modelu OSI/ISO|OSI]]. Mostki czy switche zwykle mają wbudowany repeater dla automatycznej regeneracji sygnału.

## Hub

**Hub** - wieloportowy repeater, który nie "rozumie" adresów MAC, więc przesyła dane wszędzie, co powoduje duży ruch i możliwe kolizje w sieci.

# Urządzenia nieaktywne

- kable
- wtyczki
- gniazdka
- **krosownice** (ang. *patch-panel*) - zakończenie okablowania strukturalnego, z rzędami gniazdek 8P8C, ma z tyłu porty do reszty sieci, a z przodu porty do podłączania switchy, routerów etc.; używa się jej, żeby ograniczyć ilość pracy przy budowie sieci, bo z tyłu wpina się odpowiednie żyły z kabla, a potem pracuje tylko z przodem

