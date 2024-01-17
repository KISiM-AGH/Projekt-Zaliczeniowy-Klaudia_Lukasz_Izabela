## Brak walidacji danych wejściowych

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
