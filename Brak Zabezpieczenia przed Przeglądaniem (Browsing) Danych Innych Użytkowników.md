## Nieprawidłowa Konfiguracja Bezpieczeństwa 

### poziom ryzyka:
Średni

### opis:
Nieprawidłowa konfiguracja bezpieczeństwa, znana również jako Security Misconfiguration, to podatność wynikająca z niewłaściwego konfigurowania środowiska aplikacji, co może prowadzić do różnych luk w zabezpieczeniach.

### Szczeguły techniczne
Brak weryfikacji, czy użytkownik jest właścicielem konta, którego hasło jest zmieniane.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/ChangePasswordAction.java

connection = DAOUtils.getConnection();
SystemDAO systemDAO = new SystemDAO(connection);
systemDAO.changePassword(request.getUserPrincipal().getName(), changePasswordForm.getPassword());

### Rekomendacja
Dodaj dodatkowe sprawdzenia, aby upewnić się, że użytkownik jest uprawniony do zmiany hasła.

W metodzie changePassword zamiast przechowywać hasło w postaci jawnej, użyj silnego algorytmu haszowania (np. bcrypt, Argon2).