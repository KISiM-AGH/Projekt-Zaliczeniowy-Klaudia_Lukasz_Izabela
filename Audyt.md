# Audyt Aplikacji Marathon

## Repozytorium aplikacji
https://github.com/cschneider4711/Marathon

## Wstęp

Aplikacja Marathon została poddana audytowi w celu identyfikacji potencjalnych zagrożeń bezpieczeństwa. Poniżej przedstawiamy wyniki audytu, wraz z opisem podatności, poziomem ryzyka, lokalizacją w kodzie oraz rekomendacjami dotyczącymi poprawek. Dodatkowo zawarte zostały również pozytywne aspekty kodu.

# Podatności

## 1. Brak dodatkowych zabezpieczeń, takich jak sprawdzanie siły hasła

### Poziom ryzyka
Średni

### Opis
W dostarczonym kodzie klasy ChangePasswordForm brak jest logiki sprawdzającej siłę hasła. Nie zostały uwzględnione dodatkowe kryteria, takie jak wymaganie różnych typów znaków (duże litery, małe litery, cyfry, znaki specjalne), co może obniżyć poziom bezpieczeństwa hasła.
### Szczegóły techniczne
Obecna implementacja metody validate sprawdza jedynie, czy hasło i jego potwierdzenie są identyczne i niepuste. Brak jest logiki, która oceniłaby siłę hasła na podstawie różnych kryteriów, co mogłoby ułatwić atakom brutalnej siły lub atakom opartym na słowniku.
### Lokalizacja
Kod związany z brakiem sprawdzania siły hasła znajduje się w metodzie validate klasy ChangePasswordForm w pliku zawierającym implementację formularza zmiany hasła.

### Rekomendacja
Aby zwiększyć poziom bezpieczeństwa, zaleca się dodanie logiki sprawdzającej siłę hasła. Można wymagać, aby hasło zawierało różne typy znaków, takie jak duże litery, małe litery, cyfry, oraz znaki specjalne. Ponadto, można ustawić minimalną długość hasła, aby zapobiec krótkim hasłom. To podejście utrudni potencjalnym atakującym odgadnięcie lub złamanie hasła.




## 2. Brak zabezpieczenia hasła

### Poziom ryzyka
Wysoki

### Opis
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




## 3. Brak Ochrony Przed Atakami CSRF

### Poziom ryzyka
Średni

### Opis
Implementacja mechanizmu ochrony przed CSRF (Cross-Site Request Forgery) w ChangePasswordAction ma na celu zapobieganie nieautoryzowanym zmianom hasła przez osoby trzecie. CSRF polega na wykorzystaniu zaufania serwera do autentyczności żądań pochodzących od użytkownika. W przypadku braku odpowiednich środków ochrony, atakujący może zmusić przeglądarkę użytkownika do wykonania niechcianych działań na stronie, na której użytkownik jest zalogowany.

### Szczegóły techniczne
Mechanizm ochrony polega na porównaniu tokena sesji przechowywanego na serwerze (expectedSecret), z tokenem przekazanym w żądaniu `(request.getParameter("secret"))`. Tylko jeśli te dwa tokeny są identyczne, żądanie jest przetwarzane dalej. Token musi być unikalny dla każdej sesji i niemożliwy do przewidzenia przez atakującego.


### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/ChangePasswordAction.java

```java
final String expectedSecret = (String) request.getSession().getAttribute(SessionListener.SENSITIVE_ACTIONS_TOKEN);
			if (!expectedSecret.equals(request.getParameter("secret"))) {
				throw new ServletException("Missing or wrong secret");
			}
```

### Rekomendacja
- Zamiast specyficznych komunikatów jak "Missing or wrong secret", użyj bardziej ogólnych komunikatów, takich jak "Nieautoryzowane żądanie", aby nie dawać atakującym dodatkowych wskazówek.
  
- Użyj bezpiecznych mechanizmów generowania tokenów, takich jak java.security.SecureRandom. 
  
- Upewnij się, że tokeny są wystarczająco długie i losowe, aby uniemożliwić ich przewidzenie.



## 4. Niewłaściwe Zarządzanie Zasobami

### Poziom ryzyka
Średni

### Opis
Niewłaściwe zarządzanie połączeniem z bazą danych może prowadzić do wycieków zasobów, które mogą z kolei obciążać serwer bazy danych i powodować problemy z wydajnością lub dostępnością.

### Szczegóły techniczne
Kod używa tradycyjnego trybu "try-finally" do zarządzania połączeniem z bazą danych. W bloku finally, połączenie jest zamykane, ale ta metoda nie gwarantuje zamknięcia zasobu w przypadku wystąpienia wyjątków w bloku try.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/ChangePasswordAction.java

```java
Connection connection = null;
try {
    connection = DAOUtils.getConnection();
    // ...
} finally {
    if (connection != null) connection.close();
}
``` 

### Rekomendacja
Użyj konstrukcji try-with-resources do zarządzania połączeniem z bazą danych.Ta zmiana zapewni, że połączenie z bazą danych zostanie prawidłowo zamknięte niezależnie od tego, czy wystąpią wyjątki.




## 5. Brak walidacji danych wejściowych

### Poziom ryzyka
Wysoki

### Opis
Metoda updateRunner w aplikacji przyjmuje mapę updates jako parametr, a następnie używa jej do aktualizacji obiektu Runner. Brak jakiejkolwiek walidacji i sprawdzania poprawności danych wejściowych może prowadzić do nieprawidłowego zaktualizowania danych biegacza.

### Szczegóły techniczne
Metoda updateRunner przyjmuje mapę updates zawierającą zmienne do aktualizacji obiektu Runner. Bez odpowiedniej kontroli i walidacji, atakujący może manipulować danymi wejściowymi, co prowadzi do potencjalnych błędów w danych biegacza.

### Lokalizacja
Kod dotyczący tej podatności znajduje się w metodzie updateRunner klasy MarathonService w pliku zawierającym implementację usługi.

### Rekomendacja
Aby zabezpieczyć metodę przed atakami polegającymi na manipulacji danymi, zaleca się wprowadzenie walidacji i sprawdzania poprawności danych wejściowych przed ich użyciem do aktualizacji obiektu Runner. Przykładowe kroki obejmują sprawdzanie typów danych, zakresów wartości oraz sprawdzanie, czy dana operacja jest autoryzowana dla danego użytkownika. Ograniczenie dostępu do edytowalnych pól i użycie bezpiecznych mechanizmów aktualizacji danych pomogą w minimalizacji potencjalnych zagrożeń związanych z nieprawidłowymi danymi.




## 6. Nieprawidłowa Konfiguracja Bezpieczeństwa 

### Poziom ryzyka
Średni

### Opis
Nieprawidłowa konfiguracja bezpieczeństwa, znana również jako Security Misconfiguration, to podatność wynikająca z niewłaściwego konfigurowania środowiska aplikacji, co może prowadzić do różnych luk w zabezpieczeniach.

### Szczegóły techniczne
Brak weryfikacji, czy użytkownik jest właścicielem konta, którego hasło jest zmieniane.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/ChangePasswordAction.java

```java
connection = DAOUtils.getConnection();
SystemDAO systemDAO = new SystemDAO(connection);
systemDAO.changePassword(request.getUserPrincipal().getName(), changePasswordForm.getPassword());
```

### Rekomendacja
Dodaj dodatkowe sprawdzenia, aby upewnić się, że użytkownik jest uprawniony do zmiany hasła. Można to osiągnąć poprzez porównanie UserPrincipal z identyfikatorem konta, które ma być zmieniane, i dopiero wtedy wykonać operację zmiany hasła.




## 7. Cross-Site Scripting (XSS)

### Poziom ryzyka
Wysoki

### Opis
Cross-Site Scripting (XSS) to atak polegający na umieszczeniu złośliwego kodu JavaScript w treści strony internetowej, który jest później wykonany przez przeglądarkę użytkownika. Atak ten może prowadzić do kradzieży danych sesji, manipulacji treści strony, a nawet przejęcia kontroli nad kontem użytkownika.

### Szczegóły techniczne
W kontekście akcji ShowRunnerAttendancesAction, dane użytkownika, takie jak runner i attendancesList, są przekazywane do warstwy prezentacji poprzez request.setAttribute(). Brak odpowiedniego filtrowania i escapowania tych danych może umożliwić potencjalnym atakującym wstrzykiwanie złośliwego kodu JavaScript.

### Lokalizacja
Miejsca, gdzie należy zastosować zabezpieczenia przed XSS w kodzie ShowRunnerAttendancesAction:

### Rekomendacja
Filtrowanie danych: Przeprowadź filtrowanie danych wejściowych, usuwając wszelkie potencjalnie niebezpieczne znaczniki HTML i skrypty JavaScript. Można użyć bibliotek do filtrowania takich jak OWASP Java Encoder lub funkcji dostarczanych przez framework.




## 8. IDOR (Insecure Direct Object References)

### Poziom ryzyka
Wysoki

### Opis
IDOR umożliwia atakującemu obejście mechanizmów kontroli dostępu i uzyskanie nieuprawnionego dostępu do danych lub zasobów poprzez manipulację identyfikatorami obiektów w żądaniach.

### Szczegóły techniczne
W pliku `ShowRunnerAction.java` występuje potencjalna podatność związana z IDOR (Insecure Direct Object References). Identyfikator biegacza (`runnerId`) jest pobierany bezpośrednio z parametru żądania (`request.getParameter("runner")`) i używany do wczytania danych biegacza bez sprawdzania uprawnień dostępu.

### Lokalizacja
src/main/java/demo/action/ShowRunnerAction.java

```java
final String runnerId = request.getParameter("runner");

final Runner runner;

Connection connection = null;
try {
    connection = DAOUtils.getConnection();

    RunnerDAO runnerDAO = new RunnerDAO(connection);
    if (runnerDAO.hasRunnerFinished(runnerId)) {
        System.out.println("Accessing a runner's profile who has finished");
    }
    runner = runnerDAO.loadRunner(Long.parseLong(runnerId));
} catch (Exception e) {
    e.printStackTrace();
    return mapping.findForward(FORWARD_showError);
} finally {
    if (connection != null) connection.close();
}
```

### Rekomendacja:
Aby zabezpieczyć kod przed potencjalnymi atakami IDOR, zaleca się podjęcie następujących kroków:

- Sprawdzenie Uprawnień:
Upewnij się, że bieżący użytkownik ma uprawnienia dostępu do danych biegacza przed wczytaniem ich. Można to osiągnąć przez wprowadzenie mechanizmów kontroli dostępu dostępnych w frameworku Struts lub innym używanym frameworku.

- Wprowadzenie Mechanizmu Uwierzytelniania i Autoryzacji:
Skorzystaj z mechanizmów uwierzytelniania i autoryzacji dostępnych w frameworku Struts (lub innym używanym frameworku). Wprowadzenie mechanizmów kontroli dostępu pozwoli na skonfigurowanie, które role użytkowników mają dostęp do konkretnych akcji.




## 9. Potencjalne Problemy z Zarządzaniem Wyjątkami

### Poziom ryzyka
Średni

### Opis
Nieodpowiednia obsługa wyjątków może prowadzić do narażenia aplikacji na nieprzewidziane zachowania, niespójność danych oraz może ujawnić wrażliwe informacje o strukturze aplikacji.

### Szczegóły techniczne
W kodzie, rzucanie wyjątku ServletException bez dalszej obsługi lub logowania może nie być wystarczające. Brak szczegółowych informacji o wyjątku i jego kontekście może utrudnić diagnozowanie i rozwiązywanie problemów, a także może ujawnić użytkownikom informacje o wewnętrznej strukturze aplikacji.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/DeleteAllResultsAction.java

```java
if (!expectedSecret.equals(request.getParameter("secret"))) {
    throw new ServletException("Missing or wrong secret");
}
```

### Rekomendacja
Upewnij się, że kod zawiera odpowiednią obsługę błędów dla operacji, które mogą zgłosić wyjątki. To pozwoli uniknąć nieoczekiwanych awarii i pomaga w diagnozowaniu problemów. Jeśli różne rodzaje wyjątków wymagają różnych działań naprawczych, rozważ użycie wielu bloków catch zamiast jednego ogólnego bloku. To pozwoli na bardziej precyzyjną obsługę różnych rodzajów wyjątków.




## 10. SQL Injection

### Poziom ryzyka
Wysoki

### Opis
SQL Injection to atak, który pozwala  użytkownikowi wstrzykiwać złośliwe zapytania SQL do kodu aplikacji, co może prowadzić do nieautoryzowanego dostępu do bazy danych, modyfikacji danych lub usunięcia danych.

### Szczegóły techniczne
W pliku `ResultsDAO.java`, metoda `loadResults` używa konkatenacji napisów do budowania zapytania SQL. Wartość `marathonId` jest włączana bezpośrednio do zapytania, co umożliwia złośliwemu użytkownikowi wprowadzenie złośliwych danych.

### Lokalizacja
Marathon/src/main/java/demo/dao/ResultsDAO.java

### Rekomendacja:
Aby zabezpieczyć kod przed SQL Injection, zaleca się używanie parametryzowanych zapytań SQL przy użyciu przygotowanych instrukcji SQL lub mechanizmów ORM. W przypadku metody loadResults, zamiast konkatenować wartość marathonId bezpośrednio do zapytania, zastosuj parametryzowane zapytanie SQL przy użyciu PreparedStatement. Przeprowadź również odpowiednią walidację danych wejściowych, aby zapobiec manipulacji złośliwymi danymi.




# elementy pozytywne

## 1. Przetwarzanie plików SVG:

W kodzie akcji UpdateRunnerPhotoAction zaimplementowano obsługę przesyłanych plików SVG w celu zwiększenia bezpieczeństwa i zgodności. Poniżej przedstawiono analizę tego punktu z perspektywy bezpieczeństwa:

### Opis
W przypadku, gdy przesyłany plik jest w formacie SVG, kod akcji używa biblioteki Batik do jego konwersji na format PNG. To podejście ma na celu zminimalizowanie potencjalnych zagrożeń wynikających z bezpośredniego obsługiwania plików SVG, które mogą zawierać złośliwy kod.

### Szczegóły techniczne
Wykorzystanie biblioteki Batik do transkodowania pliku SVG na format PNG.
Sprawdzenie typu pliku i przetworzenie go tylko wtedy, gdy jest to plik SVG.

### Lokalizacja
Akcja UpdateRunnerPhotoAction w kodzie aplikacji.

### Rekomendacja
Kontynuować monitorowanie bibliotek i narzędzi używanych do przetwarzania plików SVG w celu zapewnienia aktualności i bezpieczeństwa.
Przeprowadzić testy bezpieczeństwa w zakresie konwersji SVG do upewnienia się, że nie występują zagrożenia związane z przetwarzaniem tego formatu pliku.


## 2. Wykorzystanie wyłapywania wyjątków

W kodzie większość metod Akcji wykorzystuje w znacznym stopniu sposoby wyłapywania i wyrzucania wyjątków "try-catch-finally", a każda z metod ma przedstawione, jakie wyjątki wyrzuca.