## IDOR (Insecure Direct Object References)

### Poziom ryzyka:
Wysoki

### Opis:
IDOR umożliwia atakującemu obejście mechanizmów kontroli dostępu i uzyskanie nieuprawnionego dostępu do danych lub zasobów poprzez manipulację identyfikatorami obiektów w żądaniach.

### Warunki niezbędne do wykorzystania podatności:
Atakujący musi być w stanie manipulować parametrem "runner" w żądaniu HTTP.

### Szczegóły techniczne:
W pliku `ShowRunnerAction.java` występuje potencjalna podatność związana z IDOR (Insecure Direct Object References). Identyfikator biegacza (`runnerId`) jest pobierany bezpośrednio z parametru żądania (`request.getParameter("runner")`) i używany do wczytania danych biegacza bez sprawdzania uprawnień dostępu.

### Lokalizacja:
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