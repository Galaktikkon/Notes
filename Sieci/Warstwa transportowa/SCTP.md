# Multihoming

- posiadanie przez jednego hosta wielu interfejsów sieciowych (podłączenie do wielu sieci naraz). Pozwala zwiększyć niezawodność (bo mamy sieci zapasowe) oraz wydajność (bo możemy zrobić load balancing między sieciami). 
- Klasyczny przykład: smartfon korzystający jednocześnie z wifi i 3G
# Wady TCP

- często chcemy tylko wybranego podzbioru możliwości TCP, np. niezawodności, ale nie zależy nam na kolejności
- nie oferuje przesyłania komunikatów
- nie wspiera [[#Multihoming|multihomingu]] (każde połączenie point-to-point jest osobne)
- tylko 1 strumień na połączenie - asocjacja (piątka) ma pojedynczą parę źródłową i pojedynczą parę docelową
- dane są traktowane stricte jako strumień bajtów - brak podziału na wiadomości, konieczność rozróżniania przez aplikację
- podatny na ataki typu DoS
# SCTP

- Stream Control Transmission Protocol
- idea: 
	- połączyć zalety TCP i UDP
- protokół połączeniowy
- cechy:
	- transport danych - niezawodny i z selektywnymi potwierdzeniami
	- przesyłanie komunikatów (można bez kontroli kolejności)
	- kontrola i zapobieganie przeciążeniom
	- wykrywanie MTU ścieżki, fragmentacja
	- multihoming
	- wiele strumieni w jednym połączeniu (asocjacja ma grupy adresów)
	- strumień reprezentowany jako sekwencja wiadomości (krótszych lub dłuższych)
# Socket SCTP

- trójka (protokół, adresy lokalne, port lokalny), gdzie adresy to lista.
# Asocjacja SCTP

-  piątka (protokół, adresy lokalne, port lokalny, adresy obce, port obcy). W odróżnieniu od zwykłej asocjacji TCP ma listę adresów, więc realizuje wiele równoległych strumieni w jednym połączeniu między 2 portami.

# Budowa pakietu SCTP

![[Pasted image 20250122025550.png|center]]

- wspólny nagłówek jest jeden i reprezentuje dane wspólne dla całych danych
- nagłówki części są [[Komunikacja w modelu warstwowym#Przesyłanie danych|enkapsulowane]] wewnątrz pakietu SCTP

## Budowa wspólnego nagłówka SCTP

![[Pasted image 20250122025724.png|center]]

- zawsze 12 B
- porty źródłowy i docelowy - po 2 B każdy, standardowo
- znacznik weryfikacyjny - pozwala odróżnić pakiety z różnych połączeń, musi być różny od 0
- suma kontrola - solidne CRC

## Budowa nagłówka części SCTP

![[Pasted image 20250122025759.png|center]]

- **typ**:
	- dużo, dużo możliwych typów, poniżej wybrane
	- DATA - po prostu zawiera dane, zawsze na końcu pakietu
	- INIT - inicjalizacja komunikacji, pierwszy pakiet ever
	- INIT ACK - potwierdzenie INITa, drugi pakiet
	- SACK - selective acknowledgement
	- ABORT, SHUTDOWN - bardziej agresywne (abort) lub eleganckie (shutdown) zamknięcie połączenia
- **flagi** - zależą od konkretnego typu
- **długość** - długość w bajtach części włącznie z nagłówkiem

## Budowa nagłówka DATA

![[Pasted image 20250122025912.png|center]]

- **flaga** - wskazuje na to, czy wiadomość jest [[Protokół IP#Fragmentacja pakietów IP|pofragmentowana]] i jeśli tak, to jaki to jest kawałek:

| Wartość | Znaczenie                            |
| ------- | ------------------------------------ |
| 00      | Początek pofragmentowanej wiadomości |
| 01      | Środek pofragmentowanej wiadomości   |
| 10      | Końcówka pofragmentowanej wiadomości |
| 11      | Cała wiadomość (bez fragmentacji)    |

- **długość** - część + nagłówek, minimalnie 17, bo trzeba zawsze przesyłać min. 1 bajt danych (inaczej po co wysyłać część DATA?)
- **TSN** - Transmission Stream Number, numer sekwencyjny całego DATA, używany przy składaniu pofragmentowanych wiadomości (dzięki temu wiadomo, gdzie dokładnie ma trafić fragment)
- **identyfikator strumienia** - dzięki temu wiadomo, do którego strumienia (są równoległe!) należą dane
- **SSN** - Stream Sequence Number, numer sekwencyjny enkapsulowanych danych (dzięki temu wiadomo, które dokładnie dane ze strumienia wysłano)
- **identyfikator danych** - “payload data identifier”, rodzaj protokołu warstw wyższych, do którego należą dane; STCP to pole nie obchodzi, ale może być przydatne dla [[Modele warstwowe|warstw]] wyższych

## Budowa nagłówka INIT

![[Pasted image 20250122030147.png|center]]

- **długość** - minimalnie 20 B
- **początkowy znacznik weryfikacyjny (initiate tag)** - odbiorca INITa zapisuje ten numer, a w późniejszych wysyłanych wiadomościach taką wartość musi mieć znacznik weryfikacyjny we wspólnym nagłówku
- **okno odbiorcy** - wielkość bufora w bajtach, który nadawca wiadomości przeznaczył na tę asocjację; nie wolno go zmniejszać
- **liczba strumieni**:
	- **wyjściowych** - tylu strumieni zamierza używać nadawca (offered)
	- **maksymalna wejściowych** - tylu maksymalnie strumieni życzy sobie nadawca (requested)
	- nie ma negocjacji liczby strumieni w żadną stronę!
	- w praktyce przy wysyłaniu używa się $\text{min(requested, offered)}$ strumieni (to, czego żądała druga strona i to, co samemu zaoferowaliśmy)
- **początkowy TSN** - pierwszy TSN, który będzie użyty przez nadawcę (pierwszy numer sekwencyjny danych, patrz nagłówek DATA)
- **adresy IP asocjacji** - opcjonalna lista adresów IP nadawcy w ramach tego strumienia
## Budowa nagłówka SACK

![[Pasted image 20250122030654.png|center]]

- **flagi** - brak
- **długość** - w bajtach, minimalnie 16
- **kumulacyjne TSN ACK** - potwierdza otrzymanie spójnego ciągu bajtów aż do numeru sekwencyjnego, który jest tu wpisany (dalsze też mogą być otrzymane, ale są nieciągłe
- okno odbiorcy - wielkość bufora odbiorcy
- liczba opuszczonych bloków - tyle wpisów “początek i koniec opuszczonego bloku” można się spodziewać dalej
- liczba zdublowanych bloków - liczba zdublowanych TSNów, które otrzymano, a więc liczba nadmiarowych nagłówków DATA
- lista początków i końców opuszczonych bloków:
	- UWAGA - każdy normalny człowiek spodziewałby się, że w czymś o takiej nazwie są puste, niepotwierdzone kawałki; zamiast tego **są to kawałki, które zostały prawidłowo otrzymane i potwierdzone**
	- tłumacząc RFC: “Wszystkie wiadomości DATA z numerami TSN pomiędzy (kumulacyjne TSN ACK + początek opuszczonego bloku) a (kumulacyjne TSN ACK + koniec opuszczonego bloku) są uznawane za prawidłowo otrzymane”, czyli:
		(kumulacyjny TSN ACK + numer początkowy dla danego bloku)
		<=
		prawidłowo otrzymane bajty
		<=
		(kumulacyjny TSN ACK + numer końcowy dla danego bloku)
	- początek - offset (w stosunku do liczby z pola “kumulacyjne TSN ACK”) dla pierwszego bajtu z bloku, który został otrzymany
	- koniec - offset (w stosunku do liczby z pola “kumulacyjne TSN ACK”) dla ostatniego bajtu z bloku, który został otrzymany
- lista TSN powtórzonych bloków - jeżeli blok dotarł N razy, to jest tutaj N-1 kopii TSN tego bloku
### Przykład

![[Pasted image 20250122031206.png|center]]

Mamy potwierdzone bajty do 12. Rozmiar okna weźmy… jakikolwiek. Nagłówek SACK:

![[Pasted image 20250122031237.png|center]]

Mamy 2 bloki potwierdzone po naszym kumulatywnym TSN ACK: te z TSN 14-15 oraz TSN 17. Bloki liczone są ich offsetem względem kumulatywnego TSNa, więc policzyć, że pierwszy (ten 14-15) zaczyna się na offsecie 2 (14 - 12 = 2) i kończy na offsecie 3 (15 - 12 = 3). Co ważne, 1-bajtowy blok ma ten sam start i koniec.

# Rozszerzenia protokołu

- ze względu na modułową budowę nagłówków łatwo dodawać kolejne nagłówki
- problem: jedna strona może ogarniać nowe nagłówki, a druga nie
- pierwsze 2 bity typu wyznaczają, jak obsługiwać nieznane nagłówki (poniższe nie jest używany przy znanych nagłówkach!):
	- 00 - cicho porzuć wiadomość, przerwij analizę całej wiadomości
	- 01 - porzuć wiadomość zgłaszając to nadawcy, przerwij analizę całej wiadomości
	- 10 - cicho porzuć nagłówek, analizuj kolejne nagłówki
	- 11 - porzuć nagłówek zgłaszając to nadawcy, analizuj kolejne nagłówki

# Nawiązywanie połączenia w SCTP

![[Pasted image 20250122030409.png|center]]

1. INIT - host B poznaje:
	- początkowy znacznik weryfikacyjny hosta A
	- liczbę strumieni wyjściowych, których chce używać A
	- maksymalną liczbę strumieni wejściowych, których życzy sobie A
2. INIT-ACK - połączone INIT i ACK:
	- host A poznaje to samo, co wcześniej host B
	- znacznik weryfikacyjny tej wiadomości to poznany wcześniej init tag hosta A
	- zawiera pole state cookie, czyli trochę danych o urządzeniu, np. [[Adresacja w sieciach LAN#Adres MAC (Media Access Control)|adres MAC]] czy dane z [[TCP#Transmission Control Block (TCB)|TCB]]
3. COOKIE-ECHO:
	- znacznik weryfikacyjny ma ten poznany od B
	- musi być przed jakimikolwiek danymi (części DATA muszą być po części COOKIE- ECHO)
	- zawiera dokładną kopię state cookie, którą otrzymano w INIT-ACK, żeby sprawdzić, czy na pewno wszystko dotarło ok
4. COOKIE-ACK
	- potwierdza otrzymanie przez drugą stronę state cookie
	- musi być przed danymi i SACK

# Zamykanie połączenia SCTP

![[Pasted image 20250122031333.png|center]]


1. SHUTDOWN
	- Strona inicjująca zamknięcie (np. host A) wysyła wiadomość **SHUTDOWN**.
	- Wiadomość zawiera:
	    - Znacznik weryfikacyjny (ang. **Verification Tag**) przypisany podczas ustanawiania połączenia.
	    - Informację o numerze sekwencyjnym potwierdzającym ostatni przesłany przez A fragment danych.
	- **Cel**: 
		- Host A informuje host B, że zakończył przesyłanie danych i nie ma więcej informacji do wysłania.
2. SHUTDOWN-ACK
    - Po otrzymaniu wiadomości **SHUTDOWN**, host B:
        - Sprawdza, czy wszystkie dane zostały odebrane.
        - Jeśli tak, wysyła wiadomość **SHUTDOWN-ACK** do hosta A.
        - Wiadomość zawiera:
            - Znacznik weryfikacyjny.
            - Informację, że B potwierdza zamknięcie połączenia i kończy swoją transmisję danych.
    - **Cel**: 
        - Host B zgadza się na zamknięcie połączenia.
3. SHUTDOWN-COMPLETE
	- Po otrzymaniu wiadomości **SHUTDOWN-ACK**, host A wysyła wiadomość **SHUTDOWN-COMPLETE**.
	- Wiadomość ta:
	    - Zawiera znacznik weryfikacyjny.
	    - Informuje, że host A zakończył cały proces zamykania.
	- **Cel**: 
		- Ostateczne potwierdzenie, że połączenie zostało zamknięte.
4. Opcjonalny TIMER i retransmisje
	- SCTP wprowadza **timery**, które monitorują proces zamykania połączenia.
	- Jeśli host A lub host B nie otrzyma oczekiwanej wiadomości (np. SHUTDOWN-ACK lub SHUTDOWN-COMPLETE) w określonym czasie, nastąpi retransmisja odpowiedniej wiadomości.