## A5: Security Misconfiguration

### poziom ryzyka:
Średni

### opis:
 Brak sprawdzenia, czy użytkownik jest uprawniony do zmiany hasła innego użytkownika.

### Warunki niezbędne do wykorzystania podatności
Użytkownik próbuje zmienić hasło dla konta, do którego nie ma uprawnień.

### Szczeguły techniczne
Brak weryfikacji, czy użytkownik jest właścicielem konta, którego hasło jest zmieniane.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/ChangePasswordAction.java
connection = DAOUtils.getConnection();
SystemDAO systemDAO = new SystemDAO(connection);
systemDAO.changePassword(request.getUserPrincipal().getName(), changePasswordForm.getPassword());

### Rekomendacja
Dodaj dodatkowe sprawdzenia, aby upewnić się, że użytkownik jest uprawniony do zmiany hasła.