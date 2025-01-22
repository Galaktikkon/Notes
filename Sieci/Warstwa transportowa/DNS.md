DNS (Domain Name System) - rozproszona baza danych z obustronnym mapowaniem
nazwa_hosta <-> IP. Jest rozproszona na wiele serwerów (każdy posiada część informacji).
# Hierarchia nazw DNS

![[Pasted image 20250122023348.png|center]]

- nazwy budowane są bottom-up, bo zaczynamy od najbardziej szczegółowych i dopisujemy po kropce coraz bardziej ogólne
- domena arpa ma specjalne zastosowane w zapytaniach DNS
- pozostałe domeny dzielą się na podstawowe (np. rodzaj instytucji z danym adresem) oraz geograficzne (wiążą adres IP z położeniem geograficznym)
# Nazwy węzłów w drzewie DNS

- każdy węzeł ma do 63 znaków (a-z, A-Z, 0-9, -) (poza korzeniem z pustą nazwą)
- nie rozróżnia się wielkości liter
- **FQDN (Fully Qualified Domain Name)** - pełna nazwa domeny, od samego dołu (zwykle “www”), aż do najwyższej
- nazwa bez końcowej kropki musi być uzupełniona; jeżeli ma 2+ członów, to można ją traktować jak w pełni określoną lub uzupełnić o nazwę zależną od lokalizacji węzła
# Obszar DNS

- część drzewa DNS, która jest oddzielnie administrowana
- może być dzielony na podobszary (delegacja odpowiedzialności), co zwiększa skalowalność
- każdy obszar musi mieć podstawowy serwer DNS i 1 lub więcej drugoplanowych serwerów DNS
- serwer podstawowy jest główny, autorytatywny, zawiera pełne informacje dla obszaru
- serwery drugoplanowe służą zwiększeniu wydajności, otrzymują informacje od podstawowego serwera DNS poprzez transfer obszaru i trzymają tylko ich część
# Zapytania DNS

- host wydaje zapytanie do serwera DNS i chce odpowiedzi, przy czym serwery też mogą odpytywać inne serwery
- każdy serwer zna:
	- adresy hostów ze swojej domeny DNS
	- adresy serwerów, do których delegował odpowiedzialność
	- adresy głównych serwerów nazw
	- adres serwera domeny macierzystej (nadrzędnej)
- zapytania dzielą się na rekurencyjne i iteracyjne

## Rodzaje zapytań DNS

- zapytania proste (typu A):
	- najczęstsze
	- znamy domenę, szukamy IP
	- typu “Jaki jest adres IP urządzenia o nazwie www.X.Y.Z?”
- zapytania wskazujące (typu PTR):
	- tzw. **reverse DNS lookup**
	- znamy IP, szukamy domeny
	- wykorzystuje się specjalną domenę **in-addr.arpa.**
	- odwraca kolejność oktetów adresu IP, np. dla 149.156.98.14 szukamy wpisu 14.98.156.149.in-addr.arpa. i otrzymujemy domenę
- MX - serwer poczty dla określonego adresu e-mail
- NS - autorytatywny serwer DNS dla danej domeny
- CNAME - canonical name, alias
- HINFO - informacje na temat komputera


## Zapytania rekurencyjne DNS

![[Pasted image 20250122023647.png|center]]

1. Host odpytuje swój lokalny serwer DNS o adres IP poszukiwanego celu
2. Serwer DNS z domeny hosta rekurencyjnie odpytuje swój główny serwer nazw o adres, bo go nie zna
3. Główny serwer wie, że delegował odpowiedzialność do domeny .pl do innego serwera, więc go rekurencyjnie odpytuje
4. Serwer .pl wie, że delegował odpowiedzialność do domeny agh.edu.pl do innego serwera, więc go rekurencyjnie odpytuje
5. Serwer agh.edu.pl zna ten adres domeny, więc daje odpowiedź i zaczyna zwijać rekurencję
6. Serwery zwracają sobie odpowiedzi, aż odpowiedź dojdzie do hosta (aż do 8.)

## Zapytania iteracyjne DNS

![[Pasted image 20250122023821.png|center]]

1. Host odpytuje swój lokalny serwer DNS o adres IP poszukiwanego celu
2. Serwer DNS z domeny hosta nie zna tego adresu, ale wie, jaki jest serwer główny i jego można zapytać, więc przekazuje tę informację hostowi
3. Host pyta serwer główny, który wie, że delegował tę informację do serwera .pl, więc 
4. Odpowiada hostowi, żeby to jego zapytał
5. Identycznie jak wyżej, ale .pl 
6. Delegował dla agh.edu.pl
7. Serwer agh.edu.pl zna adres IP i 
8. Odpowiada nim hostowi

## Mieszane zapytania DNS

![[Pasted image 20250122024247.png|center]]

- połączenie powyższych dwóch, w zależności od konfiguracji serwerów
- serwer główny nigdy nie jest rekurencyjny
- rzeczywiście stosowane rozwiązanie

# Pamięć podręczna serwerów DNS

- cache’owane wpisy dla zmniejszenia wymiany komunikatów DNS i zwiększenia wydajności
- dzięki niej nie zachodzi część zapytań z przykładów powyżej
- **odpowiedź autorytatywna** 
	- pochodząca bezpośrednio od serwera z odpowiedniej domeny
- odpowiedzi z pamięci podręcznej **nie** są autorytatywne, ale zawierają informacje na temat serwera, od którego taką odpowiedź można uzyskać
- odpowiedzi autorytatywne dla serwera zawierają Time To Live (TTL) wpisu do pamięci podręcznej

# Sposób przesyłania komunikatów DNS

- UDP lub TCP
- port 53
- UDP zwykle, TCP jak odpowiedź ma > 512 B (maksymalny pakiet UDP, który musi odebrać każdy host)
- jeżeli UDP zawiera informację, że obcięto je do 512 B, to resolver ponawia zapytanie, ale po TCP
- obszary transmituje się po TCP
- przy UDP programy same muszą ogarniać czas oczekiwania i retransmisje