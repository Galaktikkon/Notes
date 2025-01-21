**NAT** (Network Address Translation) - sposób mapowania adresów IP z różnych przestrzeni
adresowych na siebie poprzez podmianę informacji w nagłówku IP, np. adresu źródłowego
czy docelowego.

# Zalety NAT
● pozwala sieciom z adresami prywatnymi IP na komunikację z internetem
● pozwala na komunikację sieciom z nakładającymi się adresami IP
● eliminuje potrzebę readresacji hostów przy zmianie ISP lub sposobów adresacji
● ułatwia administrację adresami IP
● w wersji PAT oszczędza przestrzeń adresową IPv4
● zwiększa bezpieczeństwo, ukrywając “prawdziwe” adresy IP

# Rodzaje adresów w NAT:
● Inside Local (IL) - prawdziwy adres hosta wewnątrz sieci (zwykle prywatny adres IP)
● Inside Global (IG) - adres hosta wewnętrznego tak, jak jest widoczny na zewnątrz
(zwykle pojedynczy adres przydzielony przez ISP)
● Outside Local (OL) - adres hosta zewnętrznego tak, jak jest widoczny wewnątrz
● Outside Global (OG) - prawdziwy adres hosta zewnętrznego
Patrząc z perspektywy hosta wewnątrz sieci z NAT:
● on wie, że ma naprawdę adres IL
● na zewnątrz myślą, że ma adres IG
● jak widzi hosta z zewnątrz, to widzi u niego adres OL
● tak naprawdę host z zewnątrz ma adres OG
Nazywa się to też czasami maskaradą IP, ze względu na te podmiany adresów i to, że
hosty widzą co innego, niż jest naprawdę.
Ważne, że te adresy mogą być takie same! Przykładowo IL może być takie samo, jak IG, po
prostu wtedy nie ukrywamy tego prawdziwego adresu (ale musi być wtedy globalnie
unikatowy).