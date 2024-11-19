
- Packet InterNet Groper (PING)
- możliwości:
	- sprawdzanie, czy host jest osiągalny
	- sprawdzanie czasu podróży pakietu (Round-Trip Time, RTT)
	- sprawdzanie liczby zgubionych i zdublowanych pakietów
- pozwala sprawdzić, czy host jest osiągalny
- korzysta z komunikatów ICMP typów 0 i 8 (odpowiedź echo i zapytanie echo)
- dodatkowe dane: identyfikator i numer sekwencyjny
- działanie:
	1. Komputer A tworzy ramkę ICMP Echo Request: 
		- 8 | 0 | process ID | 0 | (dane)
	2. Komputer A wysyła ramkę do komputera B.
	3. Komputer B tworzy ramkę ICMP Echo Reply:
		-  0 | 0 | process ID | 0 | (dane)
	4. Komputer B wysyła ramkę do komputera A.