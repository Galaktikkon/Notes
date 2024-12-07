# Classless InterDomain Routing (CIDR)

- klasy A, B, C, D, E już nie istnieją
- adres IP zawsze wysyła się razem z maską podsieci
- nie ma rozróżnienia na sieć i podsieć - są razem
- adres dzieli się teraz na adres sieci (= zarazem podsieci) oraz adres hosta
- krótsza maska = wyżej w hierarchii, więc regiony dostają krótkie maski, ISP dłuższe, a końcowi użytkownicy długie
- dostaje się zawsze blok adresów, tzn. jakiś adres IP i liczbę bitów maski - to, jak to podzielimy dalej na kolejne podsieci, zależy tylko od nas