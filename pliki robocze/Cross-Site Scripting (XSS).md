## Cross-Site Scripting (XSS)

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
