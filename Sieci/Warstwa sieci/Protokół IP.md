
# Właściwości

- bezpołączeniowy - nie zestawia połączenia przed rozpoczęciem przesyłania pakietów, tylko przesyła każdy pakiet oddzielnie
- brak potwierdzenia dostarczenia - nie ma gwarancji, że wiadomość w ogóle dotarła
- brak timeout’u, retransmisji - związane z powyższym
- brak kontroli poprawności danych - nie ma sumy kontrolnej
- brak kontroli przepływu
- ograniczenie długości wiadomości
- nie gwarantuje braku powtórzeń - te same pakiety mogą przyjść wielokrotnie
- nie gwarantuje kolejności - pakiety mogą przyjść w dowolnej kolejności
## Użyteczność protokołu IP mimo powyższych wad

- braki rozwiązują warstwy wyższe, w szczególności TCP/UDP
- prostota i elastyczność
- minimalny narzut
- zapewnia organizację trasy do przesyłania pakietów
- w praktyce powyższe braki nie są tak odczuwalne, jak by się mogło wydawać

