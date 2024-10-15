
# Ramka sieci Ethernet

![[Pasted image 20241015205921.png|center]]

Segmenty:
1. (Preambuła + znacznik początku ramki)
2. Adres docelowy
3. Adres źródłowy
4. Typ/długość
5. Dane
6. Suma kontrolna

Bity poszczególnych bajtów są w Little Endian (tzn. najpierw są najmniej znaczące bity, potem najbardziej znaczące, na odwrót niż normalnie).

![[Pasted image 20241015210018.png|center]]

## Preambuła

- 7 B
- 10101010101010...
- synchronizacja bitowa (synchronizacja zegarów między urządzeniami)
- nie jest wliczana do minimalnej długości ramki, bo de facto nie stanowi jej części - jest to element poprzedzający ramkę, część warstwy fizycznej
## Znacznik początku ramki (SFD, Starting Frame Delimiter)

- 1 B
- 10101011
- synchronizacja bajtowa (początek faktycznej ramki)
- nie jest wliczany do minimalnej długości ramki, z tego samego powodu, co preambuła
## Adres docelowy

- 6 B
- unicast, broadcast lub multicast
- bajty są w Big Endian
- jest pierwszy, bo:
	- pozwala szybko ogarnąć, czy to transmisja do nas (ważne przy magistralach i hubach, bo wtedy wysyła się do wszystkich i trzeba wiedzieć, czy to do nas i mamy czytać)
	- pozwala szybko ogarnąć, czy to multicast (opis niżej)
	- ułatwia szybkie buforowanie
## Adres źródłowy

- 6 B
- zawsze unicast
- sposób przesyłania jak przy adresie docelowym
## Typ/Długość

- 2 B
- typ mówi, do jakiego protokołu warstwy wyższej przekazać
- w standardzie DIX (Ethernet II): typ ramki (np. 0x0800 to IP)
- w standardzie IEEE 802.3:
	- długość pola danych, gdy < 1518
	- typ, gdy > 1536 (0x0600)
- gdy zawiera typ, to długość można znaleźć dzięki IFG (Interframe Gap), czyli ciszy między ramkami
## Dane

![[Pasted image 20241015210921.png|center]]

- 46-1500 B
- jeżeli < 46 B i poprzednie pole zawiera długość: reszta bitów to dopełnienie (padding)
- jeżeli < 46 B i poprzednie pole zawiera typ: pozostałe bity są dowolne, a warstwa wyższa musi wiedzieć, ile bajtów to właściwe dane
- zawiera PDU podwarstwy LLC
- budowa ustrukturyzowana standardem IEEE 802.2 (taka sama nie tylko w Ethernecie!):
- budowa:
	- DSAP (Destination Service Access Point) - 1 B, numer protokołu warstwy wyższej, gdzie mają trafić dane
	- SSAP (Source Service Access Point) - 1 B, numer protokołu warstwy wyższej, z którego pochodzą dane
	- Control - 1-2 B
	- Dane - faktyczne dane z warstwy wyższej
	- Pad - ewentualne wypełnienie dla minimalnej długości, zawarte w danych

## Suma kontrolna

- **FCS**, Frame Check Sequence - zawiera sumę kontrolną CRC, Cyclic Redundancy Check
- obliczana przy nadawaniu ramki
- ponownie obliczana przy odbiorze ramki i porównywana z tą w ramce
- niezgodności - ciche odrzucenie ramki jako niepoprawnej (brak informacji dla nadawcy, bo nie wiadomo, czy to nie właśnie adres nadawcy psuje