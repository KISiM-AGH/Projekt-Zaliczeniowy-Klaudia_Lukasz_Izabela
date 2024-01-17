## Niewłaściwe Zarządzanie Zasobami

### poziom ryzyka:
Średni

### opis:
Niewłaściwe zarządzanie połączeniem z bazą danych może prowadzić do wycieków zasobów, które mogą z kolei obciążać serwer bazy danych i powodować problemy z wydajnością lub dostępnością.

### Szczeguły techniczne
Kod używa tradycyjnego trybu "try-finally" do zarządzania połączeniem z bazą danych. W bloku finally, połączenie jest zamykane, ale ta metoda nie gwarantuje zamknięcia zasobu w przypadku wystąpienia wyjątków w bloku try.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/ChangePasswordAction.java

Connection connection = null;
try {
    connection = DAOUtils.getConnection();
    // ...
} finally {
    if (connection != null) connection.close();
}

### Rekomendacja
Użyj konstrukcji try-with-resources do zarządzania połączeniem z bazą danych.Ta zmiana zapewni, że połączenie z bazą danych zostanie prawidłowo zamknięte niezależnie od tego, czy wystąpią wyjątki.