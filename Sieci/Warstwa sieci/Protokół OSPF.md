# Cechy protokołu OSPF (Open Shortest Path First)
- protokół [[Routing#Protokoły stanu łącza (link state)|link state]]
- [[#Zalewanie z potwierdzeniami|zalewanie z potwierdzeniami]]
- zabezpieczanie pakietów z opisami sieci
- każdy rekord opisu łącza ma czas życia (potem są usuwane)
- wszystkie rekordy są zabezpieczone sumą kontrolną
- możliwość autoryzacji komunikatów (np. hasłem)
## Zalewanie z potwierdzeniami
- gdy zostanie wykryta zmiana w topologii sieci, to wszystkie routery są powiadamiane i oczekuje się na potwierdzenie otrzymania tego powiadomienia.
# Założenia projektowe OSPF
- **separacja routerów od innych komputerów**
	- łącze z sąsiednim routerem identyfikowane adresem routera
	- łącze z innym komputerem adresowane numerem sieci
- **obsługa sieci rozgłoszeniowych (Ethernet, FDDI)**
	- używa pakietów link state advertisement (LSA)
	- wykorzystuje adres multicastowy 224.0.0.6 do informowania wybranych routerów (designated routers, patrz niżej)
	- designated routers po otrzymaniu takiego pakietu jeżeli zawiera on nowe informacje rozsyłają go na adres 224.0.0.5
- **obsługa sieci bez rozgłoszeń (X.25, ATM)**
	- łącza, gdzie rozgłoszenia są drogie
	- używa się wielu połączeń punkt-punkt, bo jest taniej
- **podział wielkich sieci na mniejsze**
	- zmniejsza czas obliczeń ścieżki
	- oszczędza pamięć routera
- **kryteria wyboru ścieżki**
	- można wybrać ścieżkę według określonego kryterium lub kilku z poniższych
	- minimalny koszt
	- stopień pewności dotarcia pakietu do celu
	- maksymalna przepustowość ścieżki
	- minimalne opóźnienie
# Designated router
- problem: liczba wpisów w tablicach połączeń może rosnąć nawet kwadratowo w stosunku do liczby routerów
- idea: wyznaczyć specjalne designated routery i ich sąsiedzi będą widzieć tylko designated router jako sąsiada, więc będzie np. tylko 1 wpis zamiast 5
- designated router widzi wszystkie sąsiednie routery jako sąsiadów
- wybór następuje przy wymianie pakietów Hello (patrz niżej)
- wybiera się też backup designated router
- metryka:
	- od designated do normalnego - 0
	- od normalnego do designated - wynika z sieci

# Wykrywanie sąsiadów:
● każdy router wysyła ze wszystkich swoich interfejsów do swoich sąsiadów komunikaty
Hello
● po otrzymaniu informacji odsyła się z powrotem potwierdzenie
● dzięki powyższemu routery mają pełne informacje o stanach łączy