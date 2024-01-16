1. Helmet

    Zabezpieczenia nagłówków HTTP: Używanie Helmet, zwłaszcza z funkcją frameguard({ action: "SAMEORIGIN" }), pomaga zabezpieczyć aplikację przed atakami typu Clickjacking, ograniczając możliwość osadzania strony w ramkach na innych domenach.
    
    Dla czego jest to ważne:

   - Zabezpieczenie przed atakami Clickjacking: Atak Clickjacking polega na osadzeniu zawartości strony internetowej w ramkach (iframe) na innej stronie, często złośliwej. W sytuacji, gdy strona jest osadzana na innej domenie bez zgody, może to prowadzić do różnych ataków, takich jak przechwytywanie danych, manipulowanie treścią lub fałszowanie akcji użytkownika.

   - Ograniczanie dostępu do ramki do tej samej domeny (SAMEORIGIN): Ustawiając frameguard({ action: "SAMEORIGIN" }), aplikacja ogranicza dostęp do swojej zawartości w ramkach tylko dla tych stron, które znajdują się w tej samej domenie. To skutecznie eliminuje potencjalne zagrożenia związane z osadzaniem strony na niezaufanych witrynach, chroniąc użytkowników przed atakami Clickjacking.

   - Ochrona integralności i bezpieczeństwa zawartości: Poprzez zastosowanie odpowiednich nagłówków HTTP, Helmet wspiera ochronę integralności i bezpieczeństwa zawartości serwowanej przez aplikację, co jest kluczowe dla zapewnienia bezpiecznego środowiska dla użytkowników.

2. Logowanie aktywności serwera (Morgan)

   Logowanie do pliku: Używanie morgan do zapisywania logów do pliku pozwala na monitorowanie aktywności serwera i ułatwia diagnozowanie problemów.

   Dla czego jest to ważne:

   - Śledzenie działań i monitorowanie wydajności: Logowanie aktywności serwera jest kluczowe do śledzenia, co się dzieje w systemie w czasie rzeczywistym. Pozwala to na identyfikację problemów, monitorowanie wydajności oraz zrozumienie, jak użytkownicy korzystają z aplikacji.

   - Diagnozowanie błędów i awarii: Logi serwera są nieocenionym narzędziem przy diagnozowaniu błędów i awarii. Rejestracja informacji o żądaniach, odpowiedziach oraz ewentualnych błędach pozwala szybko zlokalizować i zrozumieć przyczyny problemów.

   - Zabezpieczenie przed atakami i monitorowanie bezpieczeństwa: Logowanie aktywności serwera umożliwia monitorowanie prób ataków, takich jak próby złamania zabezpieczeń, skanowania podatności czy inne nieprawidłowości w ruchu sieciowym. To ważne narzędzie w zapobieganiu i reagowaniu na potencjalne zagrożenia.

   - Dowody zdarzeń i zgodność z regulacjami: Rejestracja aktywności serwera dostarcza obiektywnych i wiarygodnych dowodów zdarzeń, co może być istotne w kontekście audytów bezpieczeństwa, analizy incydentów czy zgodności z przepisami prawnymi.

3. Express.json() i Express.urlencoded():

   Obsługa parsowania JSON i danych z formularzy: Ustawienia express.json() i express.urlencoded() umożliwiają aplikacji obsługę danych w formacie JSON oraz danych przesyłanych za pomocą formularzy.

   Dla czego jest to ważne:

   - Zabezpieczenia przed atakami typu Injection: Parsowanie danych wejściowych jest kluczowe w zapobieganiu atakom typu Injection, takim jak SQL Injection czy Cross-Site Scripting (XSS). Używając express.json(), aplikacja jest w stanie skutecznie przetwarzać dane w formacie JSON, eliminując potencjalne luki bezpieczeństwa związane z manipulacją danymi.

   - Zapobieganie błędom związanym z przekraczaniem limitów danych: Ustawienie express.urlencoded({ extended: false }) pomaga w kontrolowaniu przekraczania limitów danych przesyłanych przez formularze. Pozwala to na uniknięcie błędów związanych z przekroczeniem limitów danych, co może prowadzić do problemów z wydajnością i bezpieczeństwem.
   
   - Ułatwienie pracy z danymi w kontrolerach: Przetwarzanie danych wejściowych w odpowiednich formatach ułatwia pracę z nimi w kontrolerach i pozwala na skupienie się na logice biznesowej, jednocześnie minimalizując ryzyko ataków związanych z manipulacją danymi.
