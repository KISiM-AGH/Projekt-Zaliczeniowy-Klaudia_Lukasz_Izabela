## 1 Przetwarzanie plików SVG:

W kodzie akcji UpdateRunnerPhotoAction zaimplementowano obsługę przesyłanych plików SVG w celu zwiększenia bezpieczeństwa i zgodności. Poniżej przedstawiono analizę tego punktu z perspektywy bezpieczeństwa:

### Opis:
W przypadku, gdy przesyłany plik jest w formacie SVG, kod akcji używa biblioteki Batik do jego konwersji na format PNG. To podejście ma na celu zminimalizowanie potencjalnych zagrożeń wynikających z bezpośredniego obsługiwania plików SVG, które mogą zawierać złośliwy kod.

### Szczegóły techniczne
Wykorzystanie biblioteki Batik do transkodowania pliku SVG na format PNG.
Sprawdzenie typu pliku i przetworzenie go tylko wtedy, gdy jest to plik SVG.

### Lokalizacja
Akcja UpdateRunnerPhotoAction w kodzie aplikacji.

### Rekomendacja
Kontynuować monitorowanie bibliotek i narzędzi używanych do przetwarzania plików SVG w celu zapewnienia aktualności i bezpieczeństwa.
Przeprowadzić testy bezpieczeństwa w zakresie konwersji SVG do upewnienia się, że nie występują zagrożenia związane z przetwarzaniem tego formatu pliku.
Podsumowanie:
Przetwarzanie plików SVG jest odpowiednio zabezpieczone poprzez użycie sprawdzenia typu pliku i konwersję do formatu PNG przy użyciu sprawdzonej biblioteki Batik. Jednak konieczne jest regularne aktualizowanie zależności, aby utrzymać bezpieczeństwo tej funkcjonalności w miarę możliwości. Dodatkowo, warto monitorować nowe zagrożenia związane z przetwarzaniem plików graficznych i dostosowywać zabezpieczenia w miarę potrzeb.


## 2 Wykorzystanie wyłapywania wyjątków

W kodzie większość metod Akcji wykorzystuje w znacznym stopniu sposoby wyłapywania i wyrzucania wyjątków "try-catch-finally", a każda z metod ma przedstawione, jakie wyjątki wyrzuca.