**NAT** (Network Address Translation) - sposób mapowania adresów IP z różnych przestrzeni
adresowych na siebie poprzez podmianę informacji w [[Protokół IP#Długość nagłówka|nagłówku IP]], np. adresu źródłowego
czy docelowego.
# Zalety NAT

- pozwala sieciom z adresami prywatnymi IP na komunikację z internetem
- pozwala na komunikację sieciom z nakładającymi się adresami IP
- eliminuje potrzebę readresacji hostów przy zmianie ISP lub sposobów adresacji
- ułatwia administrację adresami IP
- w wersji PAT oszczędza [[Adres IPv4#Klasy adresowe (adresacja klasowa - *Classful Addressing*)|przestrzeń adresową IPv4]]
- zwiększa bezpieczeństwo, ukrywając “prawdziwe” adresy IP
# Rodzaje adresów w NAT

## Inside Local (IL) 

- prawdziwy adres hosta wewnątrz sieci (zwykle prywatny adres IP)
## Inside Global (IG) 

- adres hosta wewnętrznego tak, jak jest widoczny na zewnątrz (zwykle pojedynczy adres przydzielony przez ISP)
## Outside Local (OL)

- adres hosta zewnętrznego tak, jak jest widoczny wewnątrz
## Outside Global (OG)

- prawdziwy adres hosta zewnętrznego
## Perspektywa hosta

Patrząc z perspektywy hosta wewnątrz sieci z NAT:
- on wie, że ma naprawdę adres IL
- na zewnątrz myślą, że ma adres IG
- jak widzi hosta z zewnątrz, to widzi u niego adres OL
- tak naprawdę host z zewnątrz ma adres OG

Nazywa się to też czasami maskaradą IP, ze względu na te podmiany adresów i to, że
hosty widzą co innego, niż jest naprawdę.

Ważne, że te adresy mogą być takie same! Przykładowo IL może być takie samo, jak IG, po
prostu wtedy nie ukrywamy tego prawdziwego adresu (ale musi być wtedy globalnie
unikatowy).
# Rodzaje translacji

- statyczna - wpisane ręcznie
- dynamiczna - dynamicznie zarządzane przez mechanizm
# NAT vs PAT

- Simple NAT (Network Address Translation):
	- jak jest napisane NAT, to zwykle ma się na myśli to
	- mapowanie 1-do-1 adresów IP
	- działa dwukierunkowo
- PAT (Port Address Translation):
	- mapuje pary (adres IP, port)
	- porty jednoznacznie identyfikują hostów - wiele portów dla tego samego adresu IP
	- mapowanie 1-do-N
	- oszczędza przestrzeń adresową, bo od ISP wykupuje się 1 publiczny adres, a router będzie ogarniał hosty wewnątrz po portach
	- działa jednokierunkowo
	- nazywany też Extended NAT

# Rodzaje tłumaczenia w NAT

- inside source address:
	- [[#Inside Local (IL)|Inside Local]] -> [[#Inside Global (IG)|Inside Global]] (IL -> IG)
	- hosty wewnętrzne używają wtedy różnych adresów publicznie widocznych

![[Pasted image 20250121041403.png|center]]

- outside source address:
	- [[#Outside Global (OG)|Outside Global]] -> [[#Outside Local (OL)|Outside Local]] (OG -> OL)
	- to właśnie to pozwala współpracować sieciom z pokrywającymi się adresami, bo nawet jak przyjdzie to samo z zewnątrz (Outside Global), to możemy to przetłumaczyć na unikatowe wewnątrz (Outside Local)

![[Pasted image 20250121041440.png|center]]

# Działanie PAT
- tłumaczy parę (adres IP, port) na (adres IP, port)
- różne adresy wewnętrzne są tłumaczone na ten sam zewnętrzny adres IP, ale różne porty
- multipleksuje adres IP za pomocą portów
- świat na zewnątrz widzi całą sieć jako ten jeden router, tak jakby on wszystko nadawał; jak coś przyjdzie z zewnątrz do tego routera, to przychodzi jednak na konkretny port, dzięki czemu on wie, gdzie to przesłać wewnątrz

![[Pasted image 20250121041650.png|center]]

# Współpraca sieci o nakładających się adresach
Weźmy sytuację taką, jak na rysunku poniżej. Mamy włączony NAT (ale nie PAT!) na
routerze brzegowym sieci wewnętrznej (ta po lewej).

Parę założeń co do DNSa:
- serwery DNS są autorytatywne dla swoich sieci, domyślne dla pokazanych hostów i odpytują rekurencyjnie
- serwer DNS ns.foo.com jest nadrzędny dla ns.bar.com, to on łączy się z wyższymi w hierarchii serwerami DNS
- chcemy, żeby hosty z obu sieci mogły się komunikować ze sobą za pomocą DNSa

Jak widać, adresy się nakładają i to prywatne adresy IP, więc trzeba NATa do komunikacji.

![[Pasted image 20250121041749.png|center]]

Żeby spełnić powyższe wymagania, NAT musi zrobić pełne tłumaczenie [[#Inside Local (IL)|IL]] -> [[#Inside Global (IG)|IG]] (żeby ci na
zewnątrz mogli komunikować się z wewnętrzną siecią) oraz [[#Outside Global (OG)|OG]] -> [[#Outside Local (OL)|OL]] (żeby ci wewnątrz
mogli komunikować się z zewnętrzną siecią).

Tłumaczymy więc, używając pul adresów z rysunku. Adresy po tłumaczeniu są dowolnymi
adresami z puli. Komunikacja między hostami póki co nie zachodziła, więc w tablicy NAT
będą tylko adresy dla serwerów DNS (bo one muszą się komunikować) - wpisy statyczne.

![[Pasted image 20250121043216.png|center]]

Jak widać, mamy dwukierunkowe tłumaczenie odpowiednich adresów. Serwery DNS mogą
dzięki tej tablicy się ze sobą komunikować.

Załóżmy teraz, że host wewnętrzny y.bar.com chce skomunikować się z hostem
zewnętrznym x.foo.com. Najpierw potrzebuje jego adresu IP, więc robi DNS Query.

![[Pasted image 20250121043150.png|center]]

1. Host pyta swój lokalny serwer DNS o adres:
	- oba są w sieci wewnętrznej, więc komunikują się z użyciem swoich adresów IL
	- 10.1.1.1 IL -> 10.1.1.10 IL
2. Serwer DNS nie zna adresu IP, więc pyta swój nadrzędny serwer ns.foo.com:
	- serwer z wewnątrz komunikuje się z zewnętrznym, ale tłumaczenie już znamy
	- 10.1.1.10 IL -> 192.168.1.254 OL
3. Router przesyła dalej zapytanie:
	- od nadawcy (adres IG) trzeba wysłać do odbiorcy (adres OG)
	- 140.16.1.254 IG -> 10.1.1.10 OG
4. Serwer DNS ns.foo.com zna adres IP hosta x.foo.com i może odpowiedzieć:
	- dokładnie to, co powyżej, tylko w drugą stronę
	- 10.1.1.10 OG -> 140.16.1.254 IG
5. Router przesyła odpowiedź do serwera ns.bar.com, który wysłał zapytanie:
	- jak krok 2, ale w drugą stronę
	- 192.168.1.254 OL -> 10.1.1.10 IL
6. Serwer odsyła odpowiedź z adresem IP x.foo.com hostowi y.bar.com, który o to pytał:
	- jak krok 1, ale w drugą stronę
	- 10.1.1.10 IL -> 10.1.1.1 IL

Podsumowując:
![[Pasted image 20250121043336.png|center]]

Jak widać, jest sporo “odwróconych” wpisów, tzn. w tych dalszych krokach robimy to, co
we wcześniejszych, ale “w drugą stronę”. Wynika to z tego, że jest to jednokierunkowe
tłumaczenie.

Załóżmy, że adres OL x.foo.com to 192.168.1.75, przy czym jego prawdziwy adres OG to
10.1.1.1. Do tablicy NAT dojdą więc wpisy dynamiczne dla tego adresu (dla oszczędności
miejsca nie przepisywałem adresów statycznych).

![[Pasted image 20250121043358.png|center]]

Host y.bar.com zna więc już adres x.foo.com i widzi go jako 192.168.1.75. Może więc
nastąpić komunikacja między hostami. Załóżmy, że będzie tam i z powrotem, np.
pingowanie.

![[Pasted image 20250121043413.png|center]]

1. Host y.bar.com wysyła pakiet zaadresowany do x.foo.com, wysyła więc do swojego routera:
	- router widzi pakiet, który idzie od 10.1.1.1 (IL y.bar.com) do 192.168.1.75 (OL x.foo.com)
	- wie, że będzie musiał podmienić adres nadawcy (musi być IG, a nie IL) oraz adres odbiorcy (musi być OG, a nie OL)
2. Router przesyła pakiet dalej:
	- nie ma żadnego wpisu IL -> IG dla 10.1.1.1 (bo to pierwsza komunikacja hosta ze światem zewnętrznym), więc musi dodać wpis IL -> IG, dodaje jakiś adres z puli IG
	- 10.1.1.10 IL -> 140.16.1.55 IG
	- router ma tłumaczenie OL -> OG dla adresu 192.16.1.75 (wpis dynamiczny 192.16.1.75 OL -> 10.1.1.1 OG), więc może podmienić
	- pakiet zmienia swój adres nadawcy i odbiorcy:
		(10.1.1.1, 192.16.1.75) -> (140.16.1.55, 10.1.1.1)
3. Host x.foo.com dostaje pakiet i odpowiada, przesyłając pakiet do routera:
	- trzeba zmapować OG (adres źródłowy x.foo.com) na IG
	- 10.1.1.1 OG -> 140.16.1.55 IG
4. Router przesyła pakiet do y.bar.com:
	- trzeba zmapować OL (adres docelowy x.foo.com) na IL
	- 192.168.1.75 OL -> 10.1.1.1 IL

Podsumowując:

![[Pasted image 20250121043558.png|center]]

Do tablicy NAT dodaliśmy kolejne dynamiczne wpisy, tym razem pochodzące od
y.bar.com.

![[Pasted image 20250121043611.png|center]]

Podsumowując, cała tablica NAT:

![[Pasted image 20250121043628.png|center]]

Pierwsza część - statyczne wpisy z DNSa
Druga część - dynamiczne wpisy od x.foo.com
Trzecia część - dynamiczne wpisy od y.bar.com

W przypadku PAT na powyższym przykładzie router miałby po prawej stronie (publiczna
sieć) jeden adres IP, po lewej (prywatna sieć) drugi. Host y.bar.com wysyłałby coś do
routera z własnego adresu i portu, port wysyłałby to do x.foo.com z adresem nadawcy
podmienionym na własny (oraz portem reprezentującym y.bar.com), tak samo z
powrotem. Istotą PAT jest jeden adres routera w części zewnętrznej i używanie po 1
porcie na każdego hosta wewnątrz prywatnej sieci.