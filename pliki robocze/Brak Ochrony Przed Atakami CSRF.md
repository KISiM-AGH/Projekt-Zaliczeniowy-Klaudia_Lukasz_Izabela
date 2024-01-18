## Brak Ochrony Przed Atakami CSRF

### Poziom ryzyka
Wysoki

### Opis
Cross-Site Request Forgery (CSRF) to atak, w którym złośliwy użytkownik może zmusić uprawnionego użytkownika do wykonania niechcianych działań na aplikacji internetowej, na którą jest zalogowany. W przypadku kodu, który usuwa wszystkie wyniki maratonu, brak jest odpowiednich zabezpieczeń przed tym rodzajem ataku.

### Szczegóły techniczne
W kodzie występuje pole secret, które jest używane do weryfikacji, czy żądanie jest ważne. Jednakże, to pole jest przechowywane w sesji użytkownika i nie jest wymagane w żądaniu w formie parametru. To oznacza, że złośliwy użytkownik może przygotować fałszywe żądanie POST, które zawiera prawidłowy secret, ale nie jest zalogowany. W przypadku zalogowanego użytkownika, który nieświadomie odwiedzi takie żądanie, zostaną usunięte wszystkie wyniki.

### Lokalizacja
https://github.com/cschneider4711/Marathon/blob/master/src/main/java/demo/action/DeleteAllResultsAction.java

if (!expectedSecret.equals(request.getParameter("secret"))) {
    throw new ServletException("Missing or wrong secret");
}

### Rekomendacja
Wprowadź silny mechanizm ochrony przed CSRF, tak jak tokeny CSRF, które są generowane, przechowywane i weryfikowane dla każdego żądania.