## 1 SQL Injection

### poziom ryzyka:
Wysoki

### opis:
SQL Injection to atak, który pozwala  użytkownikowi wstrzykiwać złośliwe zapytania SQL do kodu aplikacji, co może prowadzić do nieautoryzowanego dostępu do bazy danych, modyfikacji danych lub usunięcia danych.

### Szczeguły techniczne:
W pliku `ResultsDAO.java`, metoda `loadResults` używa konkatenacji napisów do budowania zapytania SQL. Wartość `marathonId` jest włączana bezpośrednio do zapytania, co umożliwia złośliwemu użytkownikowi wprowadzenie złośliwych danych.

### Lokalizacja:
Marathon/src/main/java/demo/dao/ResultsDAO.java

#### Dodatkowa lokalizacja

asd
ads
asd

### Rekomendacja:
Aby zabezpieczyć kod przed SQL Injection, zaleca się używanie parametryzowanych zapytań SQL przy użyciu przygotowanych instrukcji SQL lub mechanizmów ORM. W przypadku metody loadResults, zamiast konkatenować wartość marathonId bezpośrednio do zapytania, zastosuj parametryzowane zapytanie SQL przy użyciu PreparedStatement. Przeprowadź również odpowiednią walidację danych wejściowych, aby zapobiec manipulacji złośliwymi danymi.
