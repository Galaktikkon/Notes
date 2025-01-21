
- liczba naturalna, identyfikator abstrakcyjnego punktu docelowego
- na nich opiera się schemat adresacji procesów wewnątrz hosta (działa lepiej niż np. przy ID procesów, bo one zmieniają się ciągle)
- numery portów dla różnych protokołów warstwy 4 mogą się powtarzać
# Dostęp do portów

- synchroniczny - przetwarzanie jest wstrzymywane na czas komunikacji (proces czeka na komunikację)
- asynchroniczny - bez przerywania przetwarzania (proces dalej sobie działa)
# Buforowanie portów

- zazwyczaj każdy port ma bufor na dane przychodzące
- zapobiegają utracie danych, jeżeli proces nie może od razu obsłużyć tych danych
- może skracać czas oczekiwania procesu na dane
# Ważne porty:

- dobrze znane - numery < 1024, zarezerwowane, np.:
	- 21 - FTP
	- 23 - Telnet
	- 25 - SMTP (mail)
	- 80 - HTTP
- zarezerwowane - niektóre numery od 1024 do początku efemerycznych
- efemeryczne - numery od (214 + 215) do (216 - 1), na krótkotrwałe połączenia

# Asocjacja 

- krotka (piątka) postaci (protokół, lokalny IP, port lokalny, obcy IP, obcy port).
# Socket (półasocjacja)

- krotka (trójka): (protokół, adres, proces)
- proces = port
- może być albo (adres + port) lokalny, albo (adres + port) obcy

# Porty a połączenia

- **Problem**:
    - Serwer działa na określonym porcie, ale nie zmienia go dla każdego nowego połączenia.
    - Może się wydawać, że jeśli wiele klientów próbuje połączyć się z tym samym portem serwera, dojdzie do konfliktu, ponieważ port jest jeden.
- **Idea**:
    - Można obsługiwać wiele połączeń na tym samym porcie, dzięki mechanizmowi odróżniania ich za pomocą kombinacji adresów i portów.
- **Rozwiązanie**:
    - **Krotka identyfikująca połączenie**: W protokole TCP i UDP każde połączenie jest identyfikowane przez **parę endpointów**, czyli zestaw pięciu elementów:  
        `(protokoł, adres źródłowy, port źródłowy, adres docelowy, port docelowy)`.  
        Nawet jeśli port serwera (np. 80 dla HTTP) jest wspólny, inne elementy krotki (np. adresy i porty klientów) będą różne, co pozwala rozróżniać połączenia.
    - **Przykład dla TCP**:  
        Serwer nasłuchuje na porcie 80 (np. HTTP). Klient nawiązuje połączenie z portu `12345`. Para endpointów wygląda tak:  
        `(TCP, klient_IP, 12345, serwer_IP, 80)`  
        Gdy inny klient połączy się z tego samego portu serwera (80), ale z innego portu źródłowego (np. `23456`), połączenie będzie wyglądać tak:  
        `(TCP, inny_klient_IP, 23456, serwer_IP, 80)`  
        Dzięki temu system wie, które dane należą do którego połączenia.
# Gniazda BSD

- implementacja socketów na Unixie
- socket():
	- tworzy gniazdo
	- int socket(int family, int type, int protocol)
- bind():
	- przypisanie do lokalnego adresu
	- zwykle po stronie serwera (żeby klienci łączyli się z tym adresem)
	- int bind(int sockfd, struct sockaddr* myaddr, int addrlen)
- listen():
	- oznaczenie, że socket służy do słuchania (czekania na połączenia i akceptowania ich)
	- serwer oznacza w ten sposób, że gniazdo jest gotowe do przyjmowania połączeń
	- int listen(int sockfd, int connection_queue_size)
- accept():
	- akceptuje połączenie na dany adres i port
	- socket musi być wcześniej związany z danym adresem (bind) i nasłuchiwać połączeń na tym porcie (listen)
	- serwer potwierdza w ten sposób połączenie od klienta
	- int accept(int sockfd, struct sockaddr* foreign_addr, int* addrlen)
- connect():
	- łączy się z socketem na podanym adresie
	- klient łączy się w ten sposób z serwerem
	- int connect(int sockfd, struct sockaddr* server_addr, int addrlen)

# Sockety TCP (połączeniowe)

![[Pasted image 20250108014527.png|center]]

# Sockety UDP (bezpołączeniowe)

![[Pasted image 20250108014553.png|center]]

# Send vs sendto, recv vs recvfrom

- send/recv używają TCP, bo wymagają wcześniej połączenia się z kimś konkretnym; nie
	trzeba w nich podawać adresata, bo i tak jest znany (to ten, z którym połączył się TCP
	wcześniej poprzez connect)
- sendto/recvfrom używają UDP, bo wymagają podania adresata; trzeba go podawać, bo
	to protokół bezpołączeniowy i nie wiadomo z góry, kto jest adresatem

# Protokoły bezpołączeniowe vs połączeniowe

- bezpołączeniowe
	- tylko transmisja
	- szybsze
	- mniejsze narzuty na informacje kontrolne
	- np. **[[UDP]]**
- połączeniowe
	- nawiązywanie połączenie, transmisja, zamykanie połączenia
	- większe możliwości
	- QoS, np. zapewnienie, że dane dotrą do celu, potwierdzenia
	- np. **[[TCP]]**