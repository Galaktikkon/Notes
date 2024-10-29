# Idea

- nie pozwala na istnienie cykli w sieci, ale zapewnia połączenia zapasowe, które mogą być aktywowane w przypadku awarii
- dzięki powyższemu gwarantuje jednoznaczność ścieżki między urządzeniami
- działa na poziomie logicznym, “nadpisując” warstwę fizyczną
- realizuje Spanning Tree Algorithm (STA)
- wykorzystuje przesyłanie co regularny czas (domyślnie co 2 sekundy) ramek **BPDU** (Bridge Protocol Data Unit)
- używa specjalnego adresu [[Adresacja w sieciach LAN#Multicast|multicastowego]] 01:80:C2:00:00:00
## Zalety

- eliminuje ryzyko burzy broadcastowej
- zapewnia poprawne działanie sieci nawet w przypadku istnienia wielu fizycznych cykli
- przezroczyste dla innych urządzeń w sieci
## Wady

- nie pozwala na istnienie dodatkowych połączeń zwiększających wydajność (jedynie na ewentualne użycie połączeń jako zapasowych)
- zmniejsza [[Przesyłanie informacji#Throughput|przepustowość]] sieci
- blokuje komunikację na niektórych połączeniach, w ogóle ich nie używając (chyba, że staną się zapasowe; inaczej zmarnowaliśmy na nie pieniądze)
- wymaga regularnego badania sieci i w razie potrzeby przebudowywania drzewa - znaczący koszt wydajnościowy i czasowy (rzędu kilkudziesięciu sekund przy podłączaniu nowego [[Urządzenia warstwy łącza danych|switcha/mostka]]!)
- nakłada limit przełącznic - domyślnie w każdej gałęzi drzewa może być co najwyżej 7 switchy

# Ogólny pseudokod STA

- Każdy [[Urządzenia warstwy łącza danych#Bridge|mostek]] ma swój identyfikator (adres fizyczny) i ustalany administracyjnie priorytet
- Wyznaczany jest korzeń drzewa rozpinającego (**root bridge**) - zostaje nim mostek o najwyższym priorytecie
- Prawo do bycia korzeniem jest okresowo weryfikowane; nowo włączone mostki zakładają zawsze, że są korzeniem (chcą nim być).
- Każdy z mostków określa określa **root path cost** - najtańszą ze ścieżek wiodących od tego portu do korzenia. Port, który posiada najtańszą drogę to **root port**.
- Koszt wyznaczany jest w oparciu o tzw. **designated cost** - koszt przejścia przez dany segment sieci (wartość odwrotnie proporcjonalna do szybkości łącza).
- Dla każdego segmentu sieci wybierany jest jeden mostek - **designated bridge**, który będzie stanowił drogę dojścia do korzenia. Port tego mostka to **designated port**. (Wszystkie porty korzenia są **portami designated**)
- Wszystkie porty, które nie są ani **root** ani **designated** są zablokowane.
## Streszczenie kroków

1. Wyznacz [[Urządzenia warstwy łącza danych#Switch|switch]] będący korzeniem drzewa (root/root bridge/root switch)
2. Zbuduj drzewo połączeń do korzenia, wybierając “najlepsze” połączenia (o najmniejszym koszcie, minimalne drzewo rozpinające)
3. Usuń pozostałe połączenia poprzez ich zablokowanie (usuwa cykle)