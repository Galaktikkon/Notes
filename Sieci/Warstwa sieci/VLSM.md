
# Classless InterDomain Routing (CIDR)

- klasy A, B, C, D, E już nie istnieją
- [[Adres IPv4|adres IP]] zawsze wysyła się razem z [[Podsieci#Maska podsieci|maską podsieci]]
- nie ma rozróżnienia na sieć i podsieć - są razem
- adres dzieli się teraz na adres sieci (= zarazem podsieci) oraz [[Adres IPv4#Budowa adresu|adres hosta]]
- krótsza maska = wyżej w hierarchii, więc regiony dostają krótkie maski, ISP dłuższe, a końcowi użytkownicy długie
- dostaje się zawsze blok adresów, tzn. jakiś adres IP i liczbę bitów maski - to, jak to podzielimy dalej na kolejne podsieci, zależy tylko od nas

# Variable Length Subnet Mask (VLSM)

- technika, w której maska podsieci ma różną długość w zależności od wymagań [[Podsieci|podsieci]]
- maska nie musi być pełnym bajtem (/0, /8, /16, /24 lub /32), tylko dowolna, np. /21
- stosowane zwykle razem z CIDRem
- używany do projektowania podsieci oraz agregacji wpisów w [[Routing#Tablica routingu|tablicach routingu]]

# Przykład użycia

Mamy przydzielony adres 192.68.10.0 i maskę 255.255.255.0, czyli 192.68.10.0/24.
Potrzebujemy podzielić to na 4 podsieci:
- podsieć A: 60 hostów
- podsieć B: 28 hostów
- podsieć C: 12 hostów
- podsieć D: 12 hostów

Wykorzystamy tutaj VLSM oraz metodę drzewka. Wykorzystuje ona fakt, iż każdy kolejny
bit dodany do maski dzieli podsieć na dwie, więc mamy w ten sposób drzewo binarne.
W tej metodzie zaczynamy od pełnego adresu sieci i minimalnej maski, a następnie
dzielimy na połowy, tworząc węzły-dzieci (każdy węzeł reprezentuje podsieć): lewe
dziecko ma dany bit podmaski ustawiony na 0, prawe na 1. Kiedy dziecko ma już
odpowiednią ilość hostów dla naszej sieci (tzn. nie da się jej bardziej zmniejszyć - zwykle
zależy nam na minimalnych podsieciach), to nie dzielimy dalej, tylko przydzielamy dany
adres danej podsieci.

Na początek zauważmy, że każda sieć potrzebuje 2 adresów więcej, niż wskazuje na to
liczba hostów, bo trzeba zmieścić jeszcze adresy localhost i broadcast. Mamy więc realne
zapotrzebowanie:
- podsieć A: 62 adresy
- podsieć B: 30 adresów
- podsieć C: 14 adresów
- podsieć D: 14 adresów

Pierwotnie mamy maskę /24, więc 8 bitów na hostów, czyli cała sieć mieści 28=256
hostów.

Będziemy brać bity z ostatniego bajtu - domyślnie jest on ciągiem zer, więc po zamianie
pierwszego bitu na 1 przyjmie wartość 128, po kolejnym bicie 128+64=192, po kolejnym
192+32=224 itd.

Przydzielać najłatwiej jest od największej sieci do najmniejszej - w wyniku podziałów
najpierw tworzymy największe podsieci, więc można je od razu przydzielać.

Po lewej stronie drzewka będziemy zapisywać liczbę hostów dostępną w podsieciach na
danym poziomie, na środku drzewko i adresy podsieci, a po prawej długość maski podsieci.
256 to znacznie więcej, niż największa potrzebna nam podsieć (62 adresy). Podzielmy ją
więc na dwie, po 128 hostów. Warto też zauważyć, że jedną z tych podsieci (tylko jedna
będzie nam potrzebna) można znowu podzielić na dwie podsieci, tym razem po 64 hostów:

![[Pasted image 20241207220400.png|center]]

Jak widać, przydzieliliśmy już jeden adres podsieci A, bo miała odpowiednią liczbę
adresów. Mamy też niewykorzystany liść drzewa 192.68.10.0/25 - jest to normalna
sytuacja, bo to duża podsieć, a takiej nie potrzebujemy (może się przydać np. w
przyszłości do rozbudowy sieci o kolejne podsieci).

Dzielimy znowu i znowu, aby uzyskać podsieci B, C i D:

![[Pasted image 20241207220424.png|center]]

Jak widać, musieliśmy zużyć wszystkie 3 liście powstałe w wyniku dodatkowych podziałów,
bo B potrzebował 30 adresów, a C i D po 14 adresów.

Ostatecznie mamy zatem podsieci:
- A: 192.68.10.128/26
- B: 192.68.10.192/27
- C: 192.68.10.224/28
- D: 192.68.10.240/28

