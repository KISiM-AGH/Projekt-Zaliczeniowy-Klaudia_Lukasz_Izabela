## Brak Ochrony Przed Atakami CSRF

### poziom ryzyka:
Wysoki

### opis:
Brak mechanizmu ochrony przed atakami CSRF, który mógłby zapobiec nieautoryzowanym operacjom wykonywanym w imieniu zalogowanych użytkowników.

### Szczeguły techniczne
Kod zawiera prostą weryfikację tokena, ale może nie być wystarczająco bezpieczna w kontekście ochrony przed atakami CSRF.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/DeleteAllResultsAction.java

if (!expectedSecret.equals(request.getParameter("secret"))) {
    throw new ServletException("Missing or wrong secret");
}

### Rekomendacja
Wprowadź silny mechanizm ochrony przed CSRF, tak jak tokeny CSRF, które są generowane, przechowywane i weryfikowane dla każdego żądania.