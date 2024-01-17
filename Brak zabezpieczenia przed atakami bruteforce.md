## Brak zabezpieczenia przed atakami bruteforce

### poziom ryzyka:
Krytyczny

### opis:
Istnieje luka w bezpieczeństwie, która umożliwia atakującym wielokrotne próby logowania, aż do odgadnięcia poprawnego hasła.

### Warunki niezbędne do wykorzystania podatności
[Opis]

### Szczeguły techniczne
Brak sprawdzenia, czy metoda Login zwraca poprawny wynik, co oznacza, że błędy uwierzytelniania mogą być ignorowane.

### Lokalizacja
vulnerable-JAVA/src/main/java/com/fruit/servlet
/AdminServlet.java

int result=login.Login(username,password)

### Rekomendacja
Wprowadzenie mechanizmu blokady konta po przekroczeniu określonej liczby błędnych prób logowania jest kluczowe dla zabezpieczenia przed atakami bruteforce.
