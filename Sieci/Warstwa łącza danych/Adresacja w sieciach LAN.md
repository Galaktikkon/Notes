# Network Interface Card (NIC)

**Karta sieciowa**:
- Urządzenie pracujące w warstwie (pierwszej i) drugiej modelu **OSI/ISO**
	- (zapewnia dostęp do medium w określonym standardzie)
	- zajmuje się formowaniem ramek, obsługuje określony protokół warstwy łącza danych
	- posiada (najczęściej niezmienny) adres **MAC**
- Połączone z urządzeniem sieciowym poprzez jego magistralę
# Adresacja

- Każde urządzenie NIC ma swój unikalny adres
- Adresy są na stałe związane z interfejsem sieciowym
- Adresacja płaska, bez hierarchii
	- słaba skalowalność
	- niemożliwy routing
- Najczęściej długość 48 bitów, na ogół zapisywane w postaci 12:34:34:56:AA:DC
- Rodzaje adresów 
	- unicast: adres konkretnego hosta
	- multicast: adres grupy hostów
	- broadcast: adres wszystkich hostów

# Adres MAC (Media Access Control):

- adres fizyczny/sprzętowy
- 48-bitowy adres, zapisywany szesnastkowo w postaci 6 bajtów: $\text{XY:XX:XX:ZZ:ZZ:ZZ}$
- skojarzony na stałe z konkretnym sprzętowym interfejsem sieciowym, np. kart sieciowa, moduł Wi-Fi, moduł Bluetooth
 - jednoznaczny, unikatowy identyfikator urządzenia na poziomie warstwy łącza
- nie wystarcza on do kontrolowania dostępu do sieci
- 3 starsze bajty (po lewej):
	- Organizationally Unique Identifier, OUI
	- producent dostaje taki od IEEE (jeden albo więcej)
	- jeżeli drugi najmłodszy bit najstarszego bajtu (bit 2 w zapisie binarnym tego bajtu) jest ustawiony na 1, to adres MAC jest zarządzany lokalnie i odpowiada za niego w całości lokalny admin (ustala, jak chce; jak są kolizje - jego wina)
-  3 młodsze bajty (po prawej):
	- unikalne dla urządzenia (Network Interface Controller specific)
	- producent / administrator sieci ustala, jak chce