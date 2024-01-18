# Audyt Aplikacji Marathon

- Audyt przeprowadzani: Łukasz Gołojuch, Izabela Bubula, Klaudia Balicka
- Data zakończenia audytu: 18.01.2024r.
- Wersja audytu: 1.0

## Repozytorium aplikacji
https://github.com/cschneider4711/Marathon

Sprawdzany commit:
|Nazwa|Data commitu| SHA|
|:-:|:-:|:-:|
|docker scripts|18.06.2023|52ddbdaeead1cbf4b78f42577194f265fddeb93c|


## Podsumowanie

W poniższej tabelce przedstawiona jest liczba podatności danego typu, które zostały opisane w tym audycie:

|Wysoki|Średni|Niski|
|:-:|:-:|:-:|
|8|2|0|

Podczas zmiany i naprawy aplikacji należy priorytetyzować podatności z wysokim zagrożeniem.

## Wstęp

Aplikacja Marathon została poddana audytowi w celu identyfikacji potencjalnych zagrożeń bezpieczeństwa. Poniżej przedstawiamy wyniki audytu, wraz z opisem podatności, poziomem ryzyka, lokalizacją w kodzie oraz rekomendacjami dotyczącymi poprawek. Dodatkowo zawarte zostały również pozytywne aspekty kodu.


## Poziomy ryzyka:


- **wysoki:**
  - **Znaczące zagrożenia:** Istnieją istotne podatności, które mogą prowadzić do poważnych incydentów bezpieczeństwa.

  - **Możliwość wykorzystania przez atakujących:** Istnieje wysokie prawdopodobieństwo, że atakujący skorzystają z podatności w celu uzyskania nieautoryzowanego dostępu lub wykonania szkodliwego kodu.

  - **Wpływ na działalność biznesową:** Ryzyko to może prowadzić do znacznych strat finansowych, utraty reputacji, a nawet przerw w świadczeniu usług.
- **Średni:**
  - **Istotne podatności, ale z ograniczonym wpływem:** Istnieją podatności, jednak ich potencjalny wpływ na system lub dane jest bardziej ograniczony niż w przypadku wysokiego ryzyka.

  - **Możliwość wykorzystania, ale z mniejszym prawdopodobieństwem:** Atakujący może nadal wykorzystać podatności, ale prawdopodobieństwo sukcesu jest niższe niż w przypadku wysokiego ryzyka.

  - **Wpływ na działalność biznesową:** Ryzyko to może prowadzić do pewnych strat finansowych i problemów operacyjnych, ale skala wpływu jest kontrolowana
- **Niski:**
  - **Ograniczone podatności:** Istnieje niewiele lub żadne znaczące podatności, które mogłyby być wykorzystane przez atakujących.

  - **Niska możliwość wykorzystania:** Nawet jeśli istnieją podatności, to prawdopodobieństwo ich skutecznego wykorzystania przez potencjalnych atakujących jest minimalne.

  - **Minimalny wpływ na działalność biznesową:** Ryzyko to jest małe, a wpływ na organizację jest zaniedbywalny.

# Podatności

## 1. Brak dodatkowych zabezpieczeń, takich jak sprawdzanie siły hasła
Znalazła: Izabela Bubula

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
Znalazł: Łukasz Gołojuch

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
Znalazła: Klaudia Balicka

### Poziom ryzyka
Wysoki

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



## 4. Nieograniczona Skalowalność Obrazów
Znalazła: Klaudia Balicka

### Poziom ryzyka
Średni

### Opis
Metoda `getProfilePic` w klasie `MarathonService` pozwala na przeskalowanie obrazów profilowych do dowolnych rozmiarów. Brak ograniczeń w rozmiarach, na które użytkownik może przeskalować obraz, może prowadzić do znacznego zużycia zasobów serwera, szczególnie CPU i pamięci RAM, co stwarza ryzyko ataków typu Denial-of-Service (DoS).

### Szczegóły techniczne
Podatność wynika z faktu, że metoda getProfilePic przyjmuje parametry `width` i `height` i używa ich do skalowania obrazu za pomocą funkcji `originalImage.getScaledInstance(width, height, Image.SCALE_SMOOTH)`. Przetwarzanie dużych obrazów wymaga znacznych zasobów, a skalowanie do bardzo dużych rozmiarów może prowadzić do przeciążenia serwera.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/service/MarathonService.java

Metoda getProfilePic w klasie MarathonService, szczególnie linie kodu odpowiadające za skalowanie obrazu.

### Rekomendacja
- Zdefiniuj stałe w Java dla maksymalnych dopuszczalnych rozmiarów obrazów na przykład:

```java
private static final int MAX_WIDTH = 2000;
private static final int MAX_HEIGHT = 2000;
```
- W metodzie getProfilePic, przed skalowaniem, można sprawdzić czy żądane wymiary nie przekraczają limitów.
- Należy upewnić się, że podane wartości width i height są dodatnie i nie przekraczają ustalonych limitów. Możesz użyć Math.min() do automatycznego ograniczenia wartości.




## 5. Brak walidacji danych wejściowych
Znalazła: Izabela Bubula

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



## 6.  Ataki typu Zip Slip
Znalazła: Klaudia Balicka

### Poziom ryzyka
Wysoki

### Opis
Podatność na ataki typu Zip Slip występuje w funkcji `extractFromZip` klasy `UpdateResultsViaImportAction`. Atak Zip Slip wykorzystuje wady w procesie rozpakowywania plików ZIP, umożliwiając atakującemu umieszczenie plików w lokalizacjach poza oczekiwanym katalogiem docelowym.

### Szczegóły techniczne
Funkcja extractFromZip otwiera strumień ZIP i przygotowuje się do rozpakowania pierwszego wpisu bez dokonywania jakiejkolwiek weryfikacji ścieżki pliku. Nie ma żadnych środków zapobiegających rozpakowaniu plików do lokalizacji poza oczekiwanym katalogiem docelowym.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/UpdateResultsViaImportAction.java

Metoda extractFromZip w klasie UpdateResultsViaImportAction.

```java
private InputStream extractFromZip(InputStream inputStream) throws IOException {
    ZipInputStream zipStream = new ZipInputStream(inputStream);
    zipStream.getNextEntry();
    return zipStream;
}
```

### Rekomendacja
- Przed rozpakowaniem każdego pliku z archiwum ZIP, dokładnie sprawdź jego ścieżkę. Upewnij się, że nie zawiera ona wzorców mogących prowadzić do wyjścia poza zamierzony katalog docelowy.
- Rozważ użycie listy dozwolonych lokalizacji i typów plików, które mogą być rozpakowywane. To pomoże w zapobieganiu umieszczania plików w nieautoryzowanych katalogach i zapewni, że rozpakowywane są tylko pliki oczekiwanego typu.



## 7. Cross-Site Scripting (XSS)
Znalazła: Izabela Bubula

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
Znalazł: Łukasz Gołojuch

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




## 9. Deserializacja Niezaufanych Danych
Znalazła: Klaudia Balicka

### Poziom ryzyka
Wysoki

### Opis
Metoda `deserializeInput` w klasie `UpdateRunnerAttendancesAction` wykonuje deserializację danych wejściowych zakodowanych w Base64, które mogą być kontrolowane przez użytkownika. To stanowi potencjalne ryzyko umożliwienia wykonania dowolnego, złośliwego kodu na serwerze (ataki typu Remote Code Execution - RCE).

### Szczegóły techniczne
Metoda deserializeInput używa ObjectInputStream do deserializacji danych wejściowych zakodowanych w Base64. Jeśli dane wejściowe zawierają złośliwy kod, ich deserializacja może prowadzić do wykonania tego kodu. Atakujący może wykorzystać tę lukę, aby wykonać dowolne działania w kontekście serwera aplikacji, co może skutkować przejęciem kontroli nad serwerem, kradzieżą danych, uszkodzeniem systemu.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/UpdateRunnerAttendancesAction.java

Metoda deserializeInput w klasie UpdateRunnerAttendancesAction.

### Rekomendacja
- Zmodyfikuj podejście do przetwarzania danych przychodzących od użytkownika. Zamiast używać deserializacji, rozważ wykorzystanie formatów takich jak JSON lub XML, które są bezpieczniejsze i oferują lepszą kontrolę nad przetwarzanymi danymi. W Javie możesz wykorzystać biblioteki takie jak Jackson lub Gson do obsługi JSON.
- Monitoruj i rejestruj wszystkie operacje deserializacji, szczególnie te, które zakończyły się niepowodzeniem. To może pomóc w wykrywaniu i reagowaniu na podejrzane działania.


## 10. SQL Injection
Znalazł: Łukasz Gołojuch

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



# Pozytywne aspekty aplikacji

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