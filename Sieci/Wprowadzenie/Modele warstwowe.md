
# Warstwy modelu OSI/ISO

1. **Warstwa fizyczna** - koduje/dekoduje strumień danych przekazany przez warstwy wyższe do/z sygnałów danego medium transmisyjnego; zajmuje się poziomem napięcia, rodzajem kodowania, używanym medium transmisyjnym
2. **Łącza danych** - zapewnia komunikację między hostami w jednej sieci, fizycznie adresuje hostów, zapewnia dostęp do medium; topologia sieci dotyczy tej warstwy; protokoły dostępu do medium transmisji: Ethernet, Token Ring, FDDI, Frame Relay; mechanizmy korekcji błędów (jeżeli jest to potrzebne)
3. **Sieciowa** - szuka najlepszej drogi łączącej dwóch hostów (mogą być z punktu widzenia warstwy 2 w różnych sieciach); logicznie adresuje hostów; protokół: IP (najpopularniejszy)
4. **Transportowa** - segmentuje dane z wyższych warstw / składa je z powrotem; zapewnia niezawodność przesyłu danych, parametry jakości  - wektor parametrów takich jak przepustowość, opóźnienia, stopa błędów (QoS, Quality of Service); protokoły: TCP, UDP
5. **Sesji** - tworzy, zarządza, kończy sesję między hostami, synchronizuje dialog między komputerami; implementuje ją system operacyjny; protokół HTTP (nad TCP)
6. **Prezentacji** - odpowiada za reprezentację danych (np. konwersja kodowania znaków Big Endian - Small Endian) - uzgodnienie jej (konwersja pomiędzy różnymi standardami kodowania znaków). Implementowana przez system operacyjny
7. **Aplikacji** - programy wykorzystywane przez użytkownika

# Warstwy modelu TCP/IP

1. Warstwa dostępu do sieci - (fizyczna + łącza danych), definiuje protokoły i hardware odpowiedzialne za podłączenie hosta do fizycznej sieci i przesył danych przez nią; “widzi” sieć fizyczną, nie logiczną, przesyła ramki danych; protokoły: Ethernet, Point-to-Point Protocol, Frame Relay
2. Warstwa internetowa - zapewnia adresowanie logiczne, wyznacza ścieżkę, odpowiada za forwarding; “widzi” strukturę logiczną sieci, przesyła pakiety; protokoły: IP, ICMP (Internet Control Message Protocol)
3. Warstwa transportowa - odpowiada za transport danych end-to-end, tworzy logiczne połączenie między hostami, używa portów (i numerów portów), przesyła segmenty lub datagramy; protokoły: TCP, UDP
4. Warstwa aplikacji - odpowiada za wszystko związane z aplikacją i komunikacją między systemami, oferuje usługi (np. transfer plików między hostami za pomocą FTP) implementowane przez niższe warstwy; protokoły: Telnet, HTTP, FTP, SMTP

![[Pasted image 20241014170950.png|center]]

# Zalety modelu warstwowego

 - Umożliwia niezależny rozwój warstw
- Zmniejsza złożoność systemów
- Standaryzuje interfejsy
- Zapewnia współpracę pomiędzy urządzeniami pochodzącymi od różnych producentów
- Przyspiesza rozwój
- Ułatwia uczenie (się)