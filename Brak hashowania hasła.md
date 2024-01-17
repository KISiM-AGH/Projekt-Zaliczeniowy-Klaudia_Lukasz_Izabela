## 2 Brak zabezpieczenia hasła

### poziom ryzyka:
Wysoki

### opis:
W pliku `CreateAccountAction.java` występuje brak haszowania hasła przed zapisaniem go do bazy danych. Brak tej praktyki zwiększa ryzyko bezpieczeństwa, ponieważ hasła są przechowywane w postaci jawnej, co może prowadzić do nieautoryzowanego dostępu do danych użytkowników w przypadku ewentualnego naruszenia bezpieczeństwa bazy danych.

### Szczegóły techniczne
W metodzie `createAccount`, hasło jest przechowywane w postaci jawnej przed zapisaniem go do bazy danych.

### Lokalizacja
src/main/java/demo/dao/SystemDAO.java

```java
SystemDAO systemDAO = new SystemDAO(connection);
systemDAO.createAccount(username, password);
```

### Rekomendacja
Aby zabezpieczyć hasła użytkowników, zaleca się stosowanie funkcji skrótu (np. hashowania) przed zapisaniem ich w bazie danych. W przypadku metody createAccount, hasło powinno być hashowane przed dodaniem do bazy danych. W przypadku metody changePassword, nowe hasło również powinno być hashowane przed aktualizacją w bazie danych. Warto również rozważyć stosowanie soli (ang. salt) dla dodatkowego zabezpieczenia. Można  wykorzystać bibliotekę "org.mindrot.jbcrypt.BCrypt" do hashowania i sprawdzania poprawności haseł.